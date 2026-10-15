# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Nomadic Wilderness: The Uprooted is a Stellaris mod that lets the Wilderness origin (BioGenesis) play as a Nomadic empire (Nomads), themed on the Tree of Life origin (Plantoids). The capital is a moon-sized tree drifting between stars; every Arkship is a seed it dropped.

Status: design complete, script scaffold partly written, never loaded in-game.
- The first pass was written **without access to the game files**. On 2026-10-15 it was audited against the 4.5.2 install (`docs/research/install-audit-4.5.2.md`).
- H1–H37 in `docs/04-vanilla-hooks.md` carry verified status. Anything not marked verified there is still an assumption.
- Design decisions D1–D14 were made on 2026-10-15. They're recorded in audit §6 and at the top of `docs/02` §1.

## Game Install

- **Vanilla files (authoritative):** `C:/Program Files (x86)/Steam/steamapps/common/Stellaris`. Verify every vanilla key and value here with `rg` before writing it into the mod; never guess one. Installed version: `rawVersion` in `launcher-settings.json` (v4.5.2 "Cygnus" as of 2026-10-15).
- **User data:** `~/OneDrive/Documents/Paradox Interactive/Stellaris` (`mod/`, `save games/`, `logs/error.log`).
- `descriptor.mod` targets `supported_version="4.5.*"`. When the install moves past that, run `@patch-watcher` before trusting `docs/04`.

## Build & Test

No build step; Stellaris reads the folder directly.

1. Before loading:
   - `rg -n "<<VANILLA" common events localisation gfx` must return nothing. It's scoped because `CLAUDE.md` and `docs/` quote the marker.
   - Every file headed `# TEMPLATE` must be rebuilt from its vanilla source, or deleted if a decision made it obsolete. Two are obsolete: `ship_sizes/nw_tree_arkship_sizes.txt` (D2) and `component_templates/nw_spinal_mounts.txt` (D8).
2. Install for development per `README.md` ("Installing for development"): a copy of `descriptor.mod` with a `path=` line in `<user data>/mod/`.
3. Launch with `-debug_mode`; read `logs/error.log` after reaching the main menu.
4. On the first load, work through `docs/05-roadmap.md` "First test load". It settles what the install can't show, such as whether the `zz_` overrides replace vanilla and whether the tree cultures render.
5. Phase checklists live in `docs/05-roadmap.md`.

## Toolchain

**MCP servers**, registered for this project at local scope (`~/.claude.json`) and shared with Sovereign-AI. Source and builds live in `C:/sandbox/Sovereign-AI/tools/`; rebuild there with `npm install && npm run build`.

- `stellaris-wiki` (12 tools): wiki search, pages and sections, structured game data, patch notes.
  - Call `wiki_patch_notes` with `filter: "all"`; the default `sovereign` filter and `wiki_patch_diff` drop Nomads content.
  - `wiki_patch_list` lists minor versions only, and `wiki_patch_notes` drops sub-bullets. Read point releases ("Patch 4.x.X" pages) with `wiki_page`.
  - `docs/research/patch-notes-harvest-4.0-4.5.2.md` already holds the keyword lines for 4.3–4.5.2, plus Wilderness, biomass and Tree of Life lines from 4.0.x. 4.1 and 4.2 weren't read.
- `stellaris-saves` (8 tools): save list, metadata, empires, budgets, raw gamestate sections, empire designs, installed-vs-save version.

## Layout

The repo root is the mod root; everything in it ships with a Workshop upload.

- `descriptor.mod`: mod metadata. `CREDITS.md`: art sources.
- `docs/01-concept.md` … `docs/05-roadmap.md`: concept, mechanics (decision log at the top of §1), Ark art brief, vanilla hooks (H1–H37), roadmap
- `docs/research/`: agent findings. Start here:
  - `vanilla-nomads-4.5.2.md`: how vanilla Nomads, Wilderness and Tree of Life work, with citations
  - `install-audit-4.5.2.md`: H-item verdicts and decisions D1–D14
  - `plan-missing-scripts.md`: P1–P21, ordered by critical path
  - `plan-event-timing.md`
- `common/`: scaffold.
  - Origin in `governments/civics/00_nw_origins.txt`; vanilla trigger overrides in `scripted_triggers/zz_nw_overrides.txt`; Heartwood Core in `deposits/` and `static_modifiers/`.
  - Also decisions, situations, technology, on_actions, scripted triggers/effects and the system initializer.
  - The remaining `# TEMPLATE` files are still in the folders the first pass guessed. Real targets are in `plan-missing-scripts.md`:
    - deck zones: `zones/`
    - contracts: `missions/missions/`
    - harvest actions: `scripted_actions/`
    - the Seed: `megastructures/`
- `events/nw_uprooted_events.txt`: namespace `nw_uprooted`
- `localisation/english/nw_uprooted_l_english.yml`
- `interface/nw_uprooted.gfx`: sprites (origin picture and its two timeline zoom crops)
- Origin art:
  - icon: `gfx/interface/icons/origins/origins_nw_uprooted.dds` (40×40 uncompressed BGRA)
  - picture: `gfx/event_pictures/origins/origin_nw_uprooted.dds` (220×115)
  - Both are cut from `spacetree.jpg`.
- `gfx/models/arkships/nw_tree/`: Tree Ark model scaffold.
  - Vanilla registers `pdxmesh` blocks in a `.gfx` file under `objectTypes` and keeps entities in `.asset` files.
  - The scaffold's mesh `.asset` must move to a `.gfx` (plan P12).
- `.claude/agents/`: subagents

## Key Systems

Three vanilla facts drive the design (audit §0):

- **Nomadic is a designer toggle.** `is_nomadic` is a country code flag set by the empire designer; origins only require it in `possible` (H1, H19).
- **An Ark is a ship, not a megastructure.**
  - Vanilla Arks are starbase-class ship sizes (`<civilian|science|military>_arkship_tier_<1-3>`) carrying a `pc_ark` colony.
  - Tiers are starbase levels.
  - Ark colonies are colony scope: iterate them with `*_owned_colony` + `is_planet_class = pc_ark`.
- **Biomass is the Wilderness pop count.**
  - `wilderness.10` (from `on_biomass_monthly`) resets the stockpile to it every month.
  - Spending biomass means killing `trait_wilderness` pops in `on_queued`.

The decided design (D-numbers from audit §6):

- **Identity (D1):** standalone `origin_nw_uprooted`. Plan B is dropped.
  - `zz_nw_overrides.txt` adds the origin to `is_wilderness_empire`, `has_purity_ascension` and `has_one_genetic_ascension_choice`. Each is the verbatim vanilla body plus one branch; re-copy them after every patch.
  - `nw_is_uprooted_empire` matches the origin or the `nw_uprooted` country flag. The flag is what keeps mod content on after Take Root changes the origin.
- **Other overrides** are planned as same-path copies of whole vanilla files (`starbase_levels/06_arkship_levels.txt`, `megastructures/30_nomad_waystation.txt`, `inline_scripts/megastructures/arkship.txt`). The install documents no duplicate-key rule.
- **Tree Arks (D2, D5, D6):**
  - They use the vanilla Ark sizes and classes.
  - The trees are graphical cultures `nw_tree_<heartwood|lumen|thornwood>`, each falling back to its vanilla class culture, plus a `_withered` twin for the wither visual. They're set by script (`set_graphical_culture`). The user is building fully new tree models for them (`docs/03`).
  - Tiers grow only through the Nurture situation (`set_starbase_size`); the vanilla upgrade is blocked for the Uprooted.
  - Only the Seed (`arkship_nw_seed`) is a megastructure.
- **Economy (D4):**
  - Recurring costs are paid in food.
  - One-off costs are paid in biomass, through vanilla's `on_queued` pop kill.
  - Rewards create pops (`create_pop_group`).
  - Numbers converted under D4 are marked "balance pending".
- **Take Root (D7):** parked at lowest priority (plan P19), because the user never settles Arks. Until it's picked up, vanilla settling only has to run without errors. The plan for when it is:
  - Vanilla settle runs: `arkship_settle` → `nomads.4700`/`.4705`. A hive settles its single Ark or scuttles the rest; vanilla enforces "Nomads should *never* have planets" (`events/nomads_events_1.txt:34`).
  - Vanilla settle destroys the Ark colony when it moves the pops (`transfer_carrier … destroy_colony = yes`). So the Heartwood copy (`copy_deposit_from`) has to run first, in `.101` on `on_arkship_settle`.
  - `.102` fires on `on_capital_changed`, keyed on vanilla's `settled_as_nomadic_empire` flag. One day later it fires `.100`, which:
    - sets `origin_wilderness`
    - sets the `nw_uprooted` flag
    - resets the `started_*` climate flags (`plan-event-timing.md`)
- **Names (D13):** Uprooted names (Sap, Rootfeeding, …) show only for Uprooted empires, through scripted or triggered loc. Other nomads keep the vanilla words.
- **Modifiers on Arks:** since 4.4.4, scripted modifiers must target colonies rather than planets for Arks. Static modifiers still use `planet_*` keys and are added in colony scope.

## Conventions

- Prefix every new key and file with `nw_`; `zz_` files override vanilla; `00_` controls load order.
- Unverified vanilla references stay as `<<VANILLA: what to fill (docs/04 Hn)>>` placeholders. Replace one only with a key confirmed in the install.
- Event IDs: `nw_uprooted.N`.
- Localisation: `.yml`, UTF-8 **with BOM**, `l_english:` header.
- Player-facing names follow the naming table in `README.md` (Ark, Roothold, Seedwing, Sap, Symbiosis, Take Root, …), for Uprooted empires only (D13).

## Subagents (`.claude/agents/`)

Adapted from `C:/sandbox/Sovereign-AI/.claude/agents/`. Findings go to `docs/research/`.

| Agent | Role |
|---|---|
| `@patch-watcher` | Patch notes and the install vs. the H-items; whether `supported_version` can move |
| `@numbers-nerd` | Vanilla values from install, wiki and community; balance against `docs/02` §13 |
| `@save-analyst` | Test-save checks against the `docs/05` phases; Voidfarers baselines |
