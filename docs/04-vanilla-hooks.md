# 04 – Vanilla hooks: what the mod depends on

The mod was designed without access to the game files. On 2026-10-15 every item below was checked against the installed game, **v4.5.2 "Cygnus"**. Each item now has a verdict (Confirmed / Partly / Refuted) and a `path:line` citation relative to the install root `C:/Program Files (x86)/Steam/steamapps/common/Stellaris`.

The full evidence is in `docs/research/install-audit-4.5.2.md` (the audit) and `docs/research/vanilla-nomads-4.5.2.md` (how vanilla works). Item IDs H1–H18 are kept so existing references stay valid. H19+ are dependencies the audit found. The design decisions D1–D14 that answer these items were recorded on 2026-10-15 (audit §6, `02-mechanics.md` §1).

Files headed `# TEMPLATE` contain placeholders of the form `<<VANILLA: ...>>`; real-script files keep a placeholder only where filling it needs new logic or a decision. A TEMPLATE file is comment-only today, so it loads as an empty file: nothing it describes exists in game until its blocks are written.

## 1. How to verify

Search the install with `rg` (quote the path, it contains spaces):

```
rg -n "^origin_default_nomads|^origin_wilderness|^origin_tree_of_life" common/governments/civics/00_origins.txt
rg -n "^is_wilderness_empire" common/scripted_triggers/
rg -n "^[a-z_]*arkship_tier_[123] = " common/ship_sizes/29_nomads_dlc_ships.txt
rg -n "tree_of_life|no_lifetree" common/deposits/ common/static_modifiers/ common/decisions/ events/origin_events_3.txt
```

Placeholders left in the mod: `rg -n "<<VANILLA" common events localisation gfx`. The docs quote the marker, so don't search `.`.

## 2. Identity triggers (highest risk)

| ID | Assumption | Status | What is true (install v4.5.2) |
|---|---|---|---|
| H1 | Nomadic status is carried by fields on the origin (`origin_voidfarers` vs `origin_default`). | **Refuted** | `origin_voidfarers` does not exist. Voidfarers is `origin_default_nomads`, and its only nomadic content is `possible = { is_nomadic = yes }` (`common/governments/civics/00_origins.txt:57-93`, `:73-75`). Nomadic status is the empire designer's Nomadic toggle, a country code flag (`prescripted_countries/00_top_countries.txt:319`; script form `set_country_code_flags = { is_nomadic = … }`, `common/scripted_effects/nomads_effects.txt:6-8`). `origin_nw_uprooted` now requires `is_nomadic = yes`. |
| H2 | Vanilla identifies Wilderness empires through `is_wilderness_empire`. | **Confirmed** | `common/scripted_triggers/00_scripted_triggers.txt:1766-1769`, body `has_origin = origin_wilderness`; 1334 references in 191 files. 41 raw `has_origin = origin_wilderness` checks in 16 files bypass it (listed in `vanilla-nomads-4.5.2.md` §2.1). `origin_wilderness` itself requires `is_nomadic = no` (`00_origins.txt:640`). **Decided D1-A** (2026-10-15): standalone origin. `common/scripted_triggers/zz_nw_overrides.txt` re-copies `is_wilderness_empire`, `has_purity_ascension` and `has_one_genetic_ascension_choice` (`common/scripted_triggers/05_scripted_triggers_biogenesis.txt:138-143,189-195`) with one added `has_origin = origin_nw_uprooted` branch each; the raw checks that matter are overridden one by one (plan P1). |
| H3 | Vanilla identifies Nomadic empires with a scripted trigger (`is_nomadic_empire`) and the Ark colony with a trigger or class check. | **Partly** | No scripted trigger: `is_nomadic` is an engine trigger. Ark colony: `is_planet_class = pc_ark` (e.g. `common/inline_scripts/megastructures/arkship.txt:69`). Ark ships, fleets and starbases are scripted triggers that list the ten vanilla Arkship sizes by name (`common/scripted_triggers/09_scripted_triggers_nomads.txt:159-369`). |

## 3. Arkship definitions

| ID | Assumption | Status | What is true |
|---|---|---|---|
| H4 | Arkships are `common/megastructures/` entries, three tiers per class, with an associated ship size. | **Refuted** | An Ark is a ship size `<class>_arkship_tier_<n>` with `class = shipclass_starbase` and `carries_colony = pc_ark` (`common/ship_sizes/29_nomads_dlc_ships.txt:104-1251`). Tiers are starbase levels (`common/starbase_levels/06_arkship_levels.txt`). The only megastructures are the three sites that build a **new** Ark (`common/megastructures/29_nomad_arkships.txt:1-173`, shared inline `common/inline_scripts/megastructures/arkship.txt`). **Decided D2-B** (2026-10-15): the Uprooted keep the vanilla sizes and classes, and the trees arrive as a graphical culture (`03-arkship-models.md`, H35-H37). The mod's one Ark megastructure is the Seed site `arkship_nw_seed` (**D6-A**). |
| H5 | The Ark colony has a planet class, and the Heartwood deposit must be allowed on it. | **Partly** | One class, `pc_ark`: size 9, `district_set = nomad`, 40% habitability (`common/planet_classes/06_planet_classes_nomads.txt:1-108`). 4.4.4: "Planet class modifiers now apply to Arkship (ship-carried) colonies." `nw_deposits.txt` now uses `is_planet_class = pc_ark`. |
| H6 | Ark type bonuses are static modifiers applied by the megastructure stage. | **Refuted** | They sit on each ship size (`modifier` / `ship_modifier`, e.g. `29_nomads_dlc_ships.txt:167-180`). Reserve capacity comes from scripted variables (`common/scripted_variables/10_scripted_variables_nomads.txt:543-550`) and the Scientific deep-scan bonus from a script value (`common/script_values/08_script_values_nomads.txt:607-629`). |
| H7 | Spinal mounts are `component_templates` with `size_restriction`. | **Refuted** | They are starbase modules in `spinal_mount_1` (`common/starbase_modules/00_arkship_spinal_modules.txt:4-332`), with no class restriction. Planetary Reprocessor = `arkship_planetary_refinery` (`:106-152`); Devastator Mount = `arkship_spinal_devastator_mount` (`:4-43`, plus component set `ARKSHIP_DEVASTATOR_WEAPON`). **Decided D8**: three vanilla spinal modules are re-skinned (models through the culture, names through scripted localisation), with no class gates. |
| H8 | Harvest actions live in a dedicated folder. | **Refuted** | They are scripted actions: `arkship_harvest_resources`, `arkship_strip_mine`, `arkship_reprocess_planet`, `logistic_harvest_resources` (`common/scripted_actions/03_arkships.txt:367,478,822,1651`). On-actions are in `common/on_actions/02_component_on_actions.txt:219-264`, effects in `common/scripted_effects/nomads_effects.txt:4690-4747`. |
| H9 | Ark colony backgrounds live under `gfx/interface/` keyed by planet class or Ark type. | **Partly** | They are keyed per ship size: `planet_view_header` (`interface/planet_view.gfx:4353-4364`), `hero_ship_fleet_background` (`interface/fleet_view.gfx:2758`), `arkship_picture` (`gfx/portraits/arkships/ships/`). The starbase-level `picture` can be triggered (`common/starbase_levels/00_documentation.txt:12-15`). Uprooted-only 2D art therefore needs size, level or planet-class overrides (`03-arkship-models.md` §6). |
| H10 | Ark districts use districts + zones, and the Arkship Deck has its own key. | **Confirmed** | `district_ark_city` / `_generator` / `_basic` ("Arkship Deck") / `_military` (`common/districts/07_ark_districts.txt`); zones in `common/zones/05_arkship_zones.txt`. There is no `district_specializations` folder. |

## 4. Tree of Life

| ID | Assumption | Status | What is true |
|---|---|---|---|
| H11 | Tree of Life is a deposit with a sapling variant, a Lost Tree modifier and a food-cost transplant decision. | **Confirmed** (keys differ) | `d_tree_of_life_home` / `d_tree_of_life_colony` (`common/deposits/07_federations_deposits.txt:579-644`; both name an `origin_tree_of_life` owner in `potential`, which only decides random placement, `common/deposits/99_README_DEPOSITS.txt:4`). `no_lifetree` (`common/static_modifiers/11_static_modifiers_federations.txt:331-338`). `decision_transplant_tree`: 500 food, 180 days (`common/decisions/06_federation_decisions.txt:5-36`). No sapling deposit exists. `d_nw_heartwood_core` now copies the colony values. |
| H12 | Tree destruction is scripted on devastation. | **Confirmed** | `origin.3003`, `planet_devastation >= 50` (`events/origin_events_3.txt:52-83`), fired from `on_planet_bombarded` (`common/on_actions/00_on_actions.txt:2157-2170`). The mod's wither runs on hull instead and reuses nothing. |

## 5. Wilderness

| ID | Assumption | Status | What is true |
|---|---|---|---|
| H13 | `biomass` is a strategic resource spendable in `cost` blocks. | **Partly** | The key exists (`common/strategic_resources/00_strategic_resources.txt:480-492`) and is spendable, but the stockpile is reset every month to the Wilderness pop count (`events/origin_events_wilderness.txt:40-55`). A spend sticks only through the vanilla `on_queued` pop kill (`common/decisions/14_wilderness_decisions.txt:34-64`). **Decided D4**: recurring costs are food, one-off rewards create pops, and one-off biomass costs use the vanilla `on_queued` kill. |
| H14 | Wilderness jobs have their own keys and are auto-worked. | **Partly** | No own keys. Gestalt jobs switch name and icon for `is_wilderness_empire` (`common/pop_jobs/04_gestalt_jobs.txt`: photosymbiont `:2354`, grubsymbiont `:2090`, pollinosymbiont `:2247`, tamersymbiont `:2612`). Auto-work comes from the 4.0.22 rework and the `virtual_jobs` flag (`common/defines/00_defines.txt:1548`). |
| H15 | Wilderness ship costs use a per-size food swap; the Seedwing can reuse vanilla sizes. | **Refuted** on the premise | No Wilderness ship sizes exist. Bioship costs are per-size `cost` branches on `country_uses_bio_ships`, and the Logistic Ship (`29_nomads_dlc_ships.txt:1390-1417`), science ship (`common/ship_sizes/00_ship_sizes.txt:1448-1475`), Waystation and new-Ark megastructure already have them. The conclusion holds: the Seedwing needs art only. |
| H16 | Vanilla Wilderness has a start initializer and homeworld class; the Ark is placed the Voidfarers way. | **Refuted** | No Wilderness initializer and no Wilderness planet class. No initializer spawns an Ark: the engine creates it from the designer's `ship_size` (H22). `nw_uprooted_home_system` now uses `usage = origin` (`common/solar_system_initializers/example.txt:27-30`). |

## 6. Contracts and situations

| ID | Assumption | Status | What is true |
|---|---|---|---|
| H17 | Contracts are in `common/contracts/` with issuer/acceptor triggers, duration and rewards. | **Partly** | They are missions in `common/missions/missions/02_contracts.txt` (schema `99_README_MISSIONS.txt:1-126`): `potential_issuer`, `possible_issuer`, `potential_operator`, `possible_operator`, `time_to_accept`, `event_chain`, `on_success`. "Remote Mineral Assessment" is `contract_onboard_minerals_resource` (`:2809`). |
| H18 | Situations use the stable 3.4+ format. | **Partly** | Still valid, but the scaffold used four keys that do not exist: an `on_monthly` effect, `on_deselect`, approach `monthly_progress`, `is_using_situation_approach`. These are fixed in `nw_situations.txt` per `common/situations/99_README_SITUATIONS.txt`. The Ark-targeted template is `situation_grand_refurbishment` (`common/situations/14_nomads_situations.txt:1225-1448`). |

## 7. Dependencies found by the 4.5.2 audit

| ID | Dependency | Evidence |
|---|---|---|
| H19 | The designer's Nomadic toggle; the origin must require `is_nomadic = yes` | `00_origins.txt:73-75`; `nomads_effects.txt:6-8` |
| H20 | Colony and carrier scope: `*_owned_colony`, `ship?`, `fleet`, `colony?`, `solar_system?`; planet flags work on colonies since 4.5 | `events/nomads_arkship_events.txt:5005-5008`; `events/game_start.txt:1792`; `09_scripted_triggers_nomads.txt:341` |
| H21 | Biomass is the Wilderness pop count; the `uses_biomass` flag; spending kills pops | `events/origin_events_wilderness.txt:40-55`; `common/defines/00_defines.txt:1218`; `common/scripted_effects/biogenesis_effects.txt:2810,2899` |
| H22 | The engine creates the starting Ark from the designer `ship_size`; `starting_<class>_arkship` flags | `prescripted_countries/default.txt:23`; `common/scripted_effects/01_start_of_game_effects.txt:5202-5260` |
| H23 | The nomad hive start skips Wilderness; the Wilderness start is raw-origin gated | `common/inline_scripts/game_start/authority_hive_nomad.txt:12-19`; `common/inline_scripts/game_start/origin_wilderness.txt:10-40` |
| H24 | Tier change with `set_starbase_size`; `upgrade_possible` on starbase levels | `events/game_start.txt:3487-3503`; `common/starbase_levels/02_orbital_ring_levels.txt:18-25` |
| H25 | New-Ark construction: cost, potential, `create_arkship_effect` | `common/inline_scripts/megastructures/arkship.txt:1-86`; `nomads_effects.txt:6982-6997` |
| H26 | Hooks: `on_waystation_built` (system scope), `on_entering_system_fleet`, `on_fleet_exit_battle`, `on_ship_disabled`, `on_damage_taken`, `on_arkship_settle`, `on_capital_changed`, `on_colony_transfer` | `00_on_actions.txt:5916-5928,973-976,1758-1768,2038-2058,1492-1500,3442-3454,3369-3422`; `02_component_on_actions.txt:380-387` |
| H27 | Settle flow: `arkship_settle`, `nomads.4700`/`.4705`, `arkship_settle_planet_effect`, `nomad_can_settle_trigger`, `copy_deposit_from` | `common/scripted_actions/03_arkships.txt:1243-1329`; `events/nomads_events_1.txt:13521-13780`; `nomads_effects.txt:5053-5223` |
| H28 | Contract issue/accept triggers; `contract_clear_blockers` | `09_scripted_triggers_nomads.txt:405-449`; `02_contracts.txt:1947-2139` |
| H29 | Hull test `has_hp_percentage` (ship scope, 0-1 fraction) | `events/cosmic_storms_events_1.txt:822` |
| H30 | `wilderness.300` adds Wilderness deposits to colonies captured from a Wilderness empire | `events/origin_events_wilderness.txt:150-262` |
| H31 | Tree of Life deposits name an `origin_tree_of_life` owner in `potential`; the deposits README says `potential` only decides random placement and terraforming swaps | `07_federations_deposits.txt:585-590,618-625`; `common/deposits/99_README_DEPOSITS.txt:4,25-26` |
| H32 | Operational Reserves are a resource converter plus a situation, not a resource | `common/resource_converters/00_resource_converters.txt:1-`; `common/situations/14_nomads_situations.txt:947` |
| H33 | Arks with a leader can already survey | `common/scripted_actions/03_arkships.txt:66-131` |
| H34 | Ark model: culture-prefixed section entity; `district_01..09` attach points | `common/HOW_TO_MAKE_NEW_SHIPS.txt:52-69`; `gfx/models/ships/other/_other_entities.asset:4713-4743` |
| H35 | Each Ark size lists the graphical cultures it accepts (`graphical_culture = { "civilian_arkship" }`). If that list blocks a mod culture, D2-B needs the nine sizes overridden | `common/ship_sizes/29_nomads_dlc_ships.txt:108,227,355,487,609,739,873,994,1123` |
| H36 | The culture prefix applies to Ark module and spinal sections; meshes are `pdxmesh` blocks in `.gfx` `objectTypes`, animations and entities are `.asset` entries | `gfx/models/ships/other/_other_entities.asset:9833,9949`; `gfx/models/ships/other/_other_meshes.gfx:1,1640-1666`; `gfx/models/ships/other/_other_animations.asset:395-418` |
| H37 | `set_graphical_culture` works in ship scope at empire setup; `create_ship` takes `graphical_culture` | `common/scripted_effects/01_start_of_game_effects.txt:5204-5218`; `common/scripted_effects/nomads_effects.txt:6989-6992` |
| H38 | Settling flags the country `settled_as_nomadic_empire`, then destroys the Ark colony as it moves the pops; vanilla changes the origin only for a released subject, never for the settling empire | `common/scripted_effects/nomads_effects.txt:5058,5187-5194`; `events/nomads_events_1.txt:13722,13782` |

## 8. Known compatibility risks

- **Planets and Arks in one country.**
  - No developer statement to this effect was found in the 4.3–4.5.2 notes.
  - Vanilla script enforces it: `nomads.1` hands any planet a nomad gains to a vassal, with the comment "Nomads should *never* have planets" (`events/nomads_events_1.txt:20-38`).
  - Settling ends nomadism and scuttles or releases the other Arks; a hive can only scuttle (`events/nomads_events_1.txt:13624-13760`).
  - Take Root follows that flow (**decided D7-A**). At settle the empire takes `origin_wilderness` and keeps the mod's content through the `nw_uprooted` flag (sub-choice W, decided 2026-10-15; H38).
- **4.4.4 colony modifiers.**
  - The 4.4.4 note reads "Any scripted modifier needs to be updated to work on colonies rather than planets".
  - The mod adds its static modifiers in colony scope, as vanilla does for Arkship modifiers (`events/nomads_arkship_events.txt:8390`).
- **Other Arkship mods.**
  - Anything that overrides `06_arkship_levels.txt`, the shared `inline_scripts/megastructures/arkship.txt` or `30_nomad_waystation.txt` conflicts with D5-A, D6-A and the Roothold cost (D4) respectively.
  - If the size culture lists block the tree culture (H35), the fallback overrides the nine Ark sizes in `29_nomads_dlc_ships.txt`, which conflicts with any mod that edits them.
- **Captured Arks.**
  - With the D1-A override in place, `is_wilderness_empire` matches the Uprooted, so `wilderness.300` gives a captured Ark `d_wilderness_city` and `d_wilderness_industrial`. Its zero-district checks use `value < 4` (`events/origin_events_wilderness.txt:164-262`), so they pass on an Ark.
  - The guard is plan P21.
- **Empty capital.**
  - With `is_wilderness_empire` true, no vanilla script fills the capital Ark (H23).
  - **Decided D14**: the mod's start setup builds out the capital Ark (plan P4).
