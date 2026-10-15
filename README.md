# Nomadic Wilderness: The Uprooted

A Stellaris mod that lets the **Wilderness** origin (BioGenesis) play as a **Nomadic empire** (Nomads expansion), with the whole thing themed after the **Tree of Life** origin from the Plantoids pack.

The pitch in one line: *the sapient biosphere didn't lose its world-tree; it became one, pulled its roots out of the crust, and left.* Your capital is not a planet. It is a tree the size of a moon, drifting between stars, and every Arkship you will ever own is a seed it dropped.

## Status

Design complete, scaffold scripted, nothing tested in-game yet. The scaffold was audited against the installed game (v4.5.2) on 2026-10-15, and the design decisions D1–D14 and their three follow-ups were recorded the same day; the docs and scaffold follow them (`docs/research/install-audit-4.5.2.md` §6, §10). 7 `<<VANILLA>>` placeholders remain, each tied to a planned script. See `docs/05-roadmap.md` for the build order and the first-test-load checklist, and `docs/04-vanilla-hooks.md` for the vanilla keys the mod depends on and their verified status.

## Version target

| Thing | Value |
|---|---|
| Stellaris patch | 4.5.2 "Cygnus" (released 2026-10-06). Nomads and Arkships arrived with 4.4 "Pegasus" (released 2026-06-15). 4.4.4 made planet-class modifiers apply to Arkship colonies and told modders to make scripted modifiers work on colonies. |
| Required DLC | **Nomads** (Nomad origins gate on `has_nomads_dlc`, `common/governments/civics/00_origins.txt:68-71`) and **BioGenesis** (`origin_wilderness` is `playable = { has_biogenesis_dlc = yes }`, `00_origins.txt:599-601`). |
| Optional DLC | **Plantoids Species Pack.** The mod only reuses Tree of Life values. `origin_tree_of_life` has no DLC gate in script (`00_origins.txt:2758-2793`); the game's DLC recommender ties it to Plantoids. Vanilla has no Tree of Life sapling events. |
| Mod key prefix | `nw_` |

The request named "v4.3". Nomads and Arkships are a 4.4 feature; the install is 4.5.2, so `descriptor.mod` targets `4.5.*`.

## What is in this repo

```
docs/
  01-concept.md          Lore, pillars, fantasy, how it maps Tree of Life onto Nomads
  02-mechanics.md        Origin, economy, the three Tree Arks, Rootholds, contracts, events, endgame
  03-arkship-models.md   Art brief and model pipeline: tree models as a graphical culture on the vanilla Arks, 3 classes x 3 tiers, plus the Seed
  04-vanilla-hooks.md    Every vanilla key the mod depends on, and how to verify it
  05-roadmap.md          Milestones from "loads without errors" to "Workshop release"
  research/              Install audit (4.5.2), how vanilla Nomads works, script and event-timing plans
common/, events/, localisation/, gfx/
  Script scaffold. Files headed # TEMPLATE are comment-only plans for scripts still to be written.
descriptor.mod
```

## Installing for development

1. Clone or symlink this folder into `Documents/Paradox Interactive/Stellaris/mod/NomadicWilderness`.
2. Copy `descriptor.mod` to `Documents/Paradox Interactive/Stellaris/mod/NomadicWilderness.mod` and add a `path=` line pointing at the folder.
3. Run the Phase 0 checklist in `docs/05-roadmap.md` before launching. The TEMPLATE files are comment-only, so they load as empty files until they are rebuilt.
4. Launch with `-debug_mode` and read `error.log` after reaching the main menu.

## Naming

The Uprooted terms are for Uprooted empires only; every other empire keeps the vanilla names. Mod text uses them directly. Vanilla text that names a term through a fixed localisation key keeps the vanilla word unless the mod adds scripted or triggered localisation for it (decision D13), so some vanilla tooltips will still say "Operational Reserves" or "Logistic Ship".

| Vanilla term | Uprooted term |
|---|---|
| Arkship | Ark (Heartwood Ark, Lumen Ark, Thornwood Ark) |
| Arkship tier 1 / 2 / 3 | Sapling / Bole / Elder |
| Waystation | Roothold |
| Wayline | Mycorrhizal Lattice |
| Logistic Ship | Seedwing |
| Science ship | Seedwing Scout |
| Operational Reserves | Sap |
| Contract | Symbiosis |
| Planetary Strip Mining | Rootfeeding |
| Planetary Reprocessing | Devouring Root |
| Settle | Take Root (the vanilla settle action, decision D7) |
| Planetary Reprocessor / Subspace Imaging Array / Devastator Mount | Taproot / Heliotrope Crown / Heartlance (re-skinned spinal mounts, decision D8) |
