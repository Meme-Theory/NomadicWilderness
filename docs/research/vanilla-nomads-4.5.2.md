# Vanilla Nomads, Wilderness and Tree of Life in 4.5.2

*Researched: 2026-10-15 | Game version: v4.5.2 "Cygnus" (`launcher-settings.json`, `rawVersion`) | Sources: the install, read-only; wiki patch notes (secondary)*

Every citation is `path:line` relative to the install root `C:/Program Files (x86)/Steam/steamapps/common/Stellaris`. Anything not cited is labelled unverified.

## 0. Summary

- **Nomadic is the designer's toggle**, a country code flag. Origins only filter on `is_nomadic`. Voidfarers is `origin_default_nomads`, a near-empty origin (§1.1).
- **An Arkship is a ship** whose size is starbase-class and carries a `pc_ark` colony:
  - three classes × three tier sizes
  - tiers are starbase levels, changed by `set_starbase_size`
  - building a *new* Ark is a megastructure whose completion spawns the ship (§1.2-1.3)
- **The engine creates the starting Ark** from the designer's `ship_size`. No initializer or origin field spawns it (§1.1).
- **Waystations are megastructures that become starbases.** Waylines are engine-side. The Logistic Ship is `nomads_constructor`. Operational Reserves are a resource converter plus a situation (§1.4-1.5).
- **Harvest actions and Settle are scripted actions** (§1.5-1.6). Settling ends nomadism; extra Arks are scuttled, or released as a subject (non-gestalt only).
- **Wilderness:**
  - The identity is `is_wilderness_empire` (one line, `has_origin = origin_wilderness`).
  - **Biomass is the Wilderness pop count**, reset monthly; spending it kills pops.
  - Wilderness jobs are display swaps on gestalt jobs. There is no Wilderness planet class.
  - Vanilla keeps Wilderness and Tree of Life out of nomadism (§2).
- **Tree of Life:**
  - two deposits whose `potential` names a Tree of Life owner
  - `no_lifetree` when the tree is lost
  - death at ≥50% bombardment devastation
  - a 500-food, 180-day transplant decision (§3)

## 1. Nomads

### 1.1 Voidfarers start

**Nomadic is a country setting, not an origin.** A nomadic country carries `is_nomadic = yes` as its own field (`prescripted_countries/00_top_countries.txt:319`, the Gundersen Research Society, whose origin is `origin_lost_colony` at `:330`). Origins only *filter* on it:

- Voidfarers is `origin_default_nomads` (`common/governments/civics/00_origins.txt:57-93`). Its `possible` is just `is_nomadic = yes` (`:73-75`). It has no nomadic marker field, no starting-Arkship field and no initializer. What it adds over `origin_default`: `non_colonizable_planet_class_neighbor = yes` (`:62`), `flags = { custom_start_screen }` (`:64-66`), `playable`/`ai_playable` on `has_nomads_dlc` (`:68-71`), and a modifier block (`:77-85`: `arkship_action_speed`, `science_ship_survey_speed`, `megastructure_waystation_megastructure_build_speed_mult`, voidcraft draw/research +20%, `ship_repair_hull_mult`/`ship_repair_armor_mult` +50%).
- Every other Nomad origin (`origin_sacred_path` `:95`, `origin_heirs_of_the_khan` `:127`, `origin_forever_cruise` `:173`) also gates on `is_nomadic = yes`. Settled origins either require `is_nomadic = no` or carry a `swap_type` for the nomadic case (`origin_default` `:19-49`).
- `origin_voidfarers` does not exist anywhere in the install (searched `common/`, `events/`, `localisation/english/`).

**The empire designer picks the starting Arkship.** A prescripted nomad country sets `planet_class = "pc_ark"` and `ship_size = "science_arkship_tier_1"` (`prescripted_countries/00_top_countries.txt:335-336`); the default template has `ship_size = "civilian_arkship_tier_1"` (`prescripted_countries/default.txt:23`). Only the three tier-1 sizes carry `is_starting_arkship = yes` (`common/ship_sizes/29_nomads_dlc_ships.txt:107,486,872`). The capital colony is a `pc_ark` colony carried by that ship (see 1.2). Nothing in script spawns the starting Arkship; the engine creates it from the designer choice. The file comment "prerequisites of a starting arkship will automatically be added in code" (`29_nomads_dlc_ships.txt:102`) confirms the code path.

**Initialization order (engine hooks, then game-start on_actions):**

| Step | Hook (`common/on_actions/00_on_actions.txt`) | Scope | Nomad-relevant effect |
|---|---|---|---|
| 1 | `empire_init_add_technologies` `:4047-4053` → `game_start.8` | country | starting techs |
| 2 | `empire_init_capital_colony` `:4055-4064` → `game_start.12` | **colony** (the capital, carried by the Arkship) | `generate_start_buildings_and_districts`, `generate_start_pops`; for ship carriers, `ship? = { home_arkship_initial_setup = yes }` (`events/game_start.txt:1724-1796`, call at `:1792-1794`). `pc_ark` capitals skip start deposits and blockers generation (`:1751-1754`). |
| 3 | `empire_init_create_ships` `:4066-4072` → `game_start.9` | country | starting ships (below) |
| 4 | `on_game_start` `:2-29` → `game_start.1` | none | `every_playable_country = { limit = { is_nomadic = yes } nomad_country_initial_setup_effect = yes }` (`events/game_start.txt:457-469`) |
| 5 | `on_game_start_country` `:31-78` | country | `wilderness.100` Wilderness origin setup (`:67`), `nomads.1000` Forever Cruise (`:72`), `nomads.20` "Start the Nomadic Economic Situation" (`:73`), `nomads.25` "Start Nomads Variable Tracking" (`:74`) |

Steps 1-3 run during empire initialization, before any `on_game_start*` hook, per the comments at `:4047`, `:4055`, `:4066` ("called during empire initialization when starting a new game"). The relative order of steps 4 and 5 is engine-defined and not stated in the files: **unverified**.

`home_arkship_initial_setup` (`common/scripted_effects/01_start_of_game_effects.txt:5202-5260`, ship scope): runs `initialize_arkship_starbase_effect`, sets the ship's graphical culture and a country flag per class (`starting_civilian_arkship` `:5208`, `starting_science_arkship` `:5212`, `starting_military_arkship` `:5216`), creates a commander and assigns it to the Ark fleet (`:5219-5232`), and adds one blocker deposit to the colony; hive empires get `d_collapsed_burrows` (`:5251-5258`).

`nomad_country_initial_setup_effect` (`01_start_of_game_effects.txt:4999-5200`, country scope): `upgrade_colony_shelter` on the capital (`:5000-5002`); queues `arkship.1`, the Arkship random event director (`:5004-5007`); `set_faction_properties = { needs_border_access = no }` (`:5009-5011`); `set_resource_converter = nomadic_operational_reserves` (`:5020`); Caravan Masters get a free Waystation via `create_waystation_effect` (`:5023-5049`); issues the starting contracts with `nomads.3010` unless Forever Cruise (`:5062-5070`); converts the two guaranteed neighbour systems into a research system and a resource system (`:5147-5161`); strips `d_minerals_5`/`d_energy_5` from the home system and pre-depletes harvestable bodies with `harvested_resources_mining` (`:5163-5194`); queues `nomads.50`, the Arkship Readiness checklist (`:5197-5199`).

**Starting ships for a nomad** (`game_start.9`, `events/game_start.txt:1130-1657`):
- One science ship from the `science` design (`:1137-1168`), as for any non-low-tech empire.
- One `nomads_constructor` (the Logistic Ship); Voidfarers (`has_origin = origin_default_nomads`) get a second (`:1316-1353`).
- Eager Explorers get `nomads_engineer_vessel` instead of an engineering vessel, two for Voidfarers (`:1375-1411`).
- **No military fleet.** The corvette/mauler fleet is gated on `is_nomadic = no` (`:1471-1476`). That is the block where bioship users get two `mauler_stage_1` plus a `weaver_stage_1` (`:1482-1512`). A nomad's only warship at start is its Arkship.

### 1.2 What an Arkship is

An Arkship is **a ship whose ship size is starbase-class and carries a colony**. It is not a megastructure and not a planet.

- Ship sizes `civilian_arkship_tier_1..3`, `science_arkship_tier_1..3`, `military_arkship_tier_1..3`, plus `military_arkship_champions_forge` (`common/ship_sizes/29_nomads_dlc_ships.txt:104,224,352,483,606,736,869,991,1120,1252`).
- Core fields on `civilian_arkship_tier_1`: `class = shipclass_starbase` "Needs to be a Starbase to have starbase modules and buildings" (`:113`); `carries_colony = pc_ark` "Enables Arkship custom UI" (`:114`); `can_disable = yes`, `stay_disabled = yes`, `never_lost = yes` (`:119-121`); `collects_stockpile_from` starbases and constructors (`:122-125`); `accepts_all_modifiers = yes` (`:127`); `potential_construction`/`possible_construction = { always = no }` (`:131-136`); `entity = civilian_arkship_01_stage_1_entity` (`:153`); `max_hitpoints = @arkship_hp_2500` (`:166`); `prerequisites = { "tech_civilian_arkship" }` (`:194`); upkeep 1.5 alloys + 1.5 energy (`:205-214`).
- Tier 2 and 3 set `base_ship_size = "<class>_arkship_tier_1"` (`:232`, `:360`), cost `alloys = @arkship_cost_alloys_5000` + `influence = @arkship_cost_influence_250`, with build times 900 and 1080 days (`:328-333`, `:459-464`). Their prerequisites are `tech_arkship_tier_2` and `tech_arkship_tier_3` (`:317`, `:448`). The variables resolve in `common/scripted_variables/10_scripted_variables_nomads.txt:169-178`.
- **Tiers are starbase levels.** `starbase_level_<class>_arkship_<n>` names the ship size and a `next_level` (`common/starbase_levels/06_arkship_levels.txt:1-51` civilian, `:53-103` science, `:105-` military). Module slots run 2/4/6+spinal and building slots 4/8/12 (`:13-14`, `:30-31`, `:46-50`). Script changes a tier with `set_starbase_size = <ship_size>` in starbase scope, reached from the colony as `fleet = { starbase? = { ... } }`; vanilla example `events/game_start.txt:3487-3503`.
- **Starbase types** `sarkship_capital`, `sarkship_civilian`, … select AI module and building weights (`common/starbase_types/06_arkship_starbase_types.txt:2-74`, `:77-`), gated on `is_capital_arkship_starbase` / `is_civilian_arkship_starbase`.
- **The colony.** Planet class `pc_ark` (`common/planet_classes/06_planet_classes_nomads.txt:1-108`): `planet_size = { min = 9 max = 9 }` (`:92`), `district_set = nomad` (`:95`), `starting_district = district_ark_city` (`:79`), `is_artificial_planet = yes` (`:98`), `colonizable = yes` (`:94`), `modifier = { pop_environment_tolerance = 0.40 }` (`:101-103`), `single_build_queue = yes` (`:107`). It switches icons by the carrier ship's size: `conditional_icon = { trigger = { ship = { is_ship_size = ... } } }` (`:6-68`). Each tier adds `planet_max_districts_add` 0 / 9 / 18 (`29_nomads_dlc_ships.txt:174`, `:297`, `:428`).
- **Colony scope is separate from planet scope.** 4.5.2 has `colony_event` and `carrier_event` event types. `has_carrier_flag`/`set_carrier_flag` are carrier flags; `carrier_is_type = ship|planet`; and the links `ship?`, `planet?`, `fleet`, `colony` (e.g. `events/game_start.txt:1726-1796`, `events/nomads_arkship_events.txt:8335-8395`, `common/scripted_effects/01_start_of_game_effects.txt:5234`). `every_owned_colony`/`any_owned_colony` iterate colonies (`common/inline_scripts/megastructures/arkship.txt:67-73`). An Ark colony is `is_planet_class = pc_ark` (same file `:69`).
- **Building another Arkship is a megastructure, not the ship.** `civilian_arkship_megastructure`, `science_arkship_megastructure` and `military_arkship_megastructure` (`common/megastructures/29_nomad_arkships.txt:1,48,102`) share `common/inline_scripts/megastructures/arkship.txt`:
  - `build_type = inside_gravity_well`, `build_time = @arkship_build_time_720` (`:6`, `:10`).
  - Cost: 500 influence plus 10000 alloys; bioship users pay 5000 alloys plus food = 10000 × `@halved_alloy_to_food_cost_ratio` (1.75, `common/scripted_variables/08_scripted_variables_biogenesis.txt:14`) = 17500 food (`:12-31`).
  - `potential`: `is_nomadic = yes`, `tech_arkship_construction`, the class tech, and `from = { is_arkship_fleet = yes }`, so an Arkship builds it (`:33-40`).
  - `on_build_complete`: `create_arkship_effect = { TYPE = $SHIP_SIZE$ OWNER = from LOCATION = fromfrom }`, then `remove_megastructure` and sets `arkship_built_<type>` (`:55-86`).
  - `create_arkship_effect` (`common/scripted_effects/nomads_effects.txt:6982-6997`) creates a fleet with `create_ship = { random_existing_design = $TYPE$_tier_1 graphical_culture = $TYPE$ }`. The colony comes with the ship.
- **Identity triggers enumerate ship sizes.** `common/scripted_triggers/09_scripted_triggers_nomads.txt` hard-codes the ten vanilla sizes in each of these:
  - `is_arkship_ship` `:169-182`, `is_arkship_starbase` `:184-197`
  - `is_civilian_arkship` / `is_science_arkship` / `is_military_arkship` `:235-258`
  - `is_civilian_arkship_starbase` … `is_military_arkship_starbase` `:315-337`
  - `is_arkship_tier_1..3` `:346-369`
  - `arkship_spinal_slot_free` `:201-211`

  `is_arkship_fleet` (`:159-167`) and `is_capital_arkship_starbase` (`:339-344`) build on them. Any new ship size is invisible to all of vanilla's Arkship content unless every one of these triggers is overridden.
- **How the model is chosen.** The visible hull is the section entity (`civilian_arkship_tier_1_section` → `civilian_arkship_01_stage_1_ship_entity`, `common/section_templates/nomads_dlc.txt:194-237`). Vanilla's modding notes say the game prepends the ship's graphical culture to a section entity name and falls back to the bare name (`common/HOW_TO_MAKE_NEW_SHIPS.txt:52-55`, `:61-69`). Arkships get the cultures `civilian_arkship` / `science_arkship` / `military_arkship` (`common/graphical_culture/00_graphical_culture.txt:1742,1774,1806`; `selectable`/`randomized = { always = no }`, `fallback = avian_01`), set by `set_graphical_culture` in `home_arkship_initial_setup` and by `create_ship ... graphical_culture` in `create_arkship_effect`. **Inference, unverified in-game:** a new graphical culture plus entities named `<culture>_civilian_arkship_01_stage_N_ship_entity` would re-skin the vanilla Arkship sizes without new ship sizes.

### 1.3 Ark internals: classes, tiers, districts, zones

**Class bonuses live on the ship sizes** (`modifier` / `ship_modifier`), not in static modifiers (`common/ship_sizes/29_nomads_dlc_ships.txt`):

| Class | Tier 1 | Tier 2 | Tier 3 | Lines |
|---|---|---|---|---|
| Civilian | `arkship_harvest_resources_produces_mult` 0.05, sensor +1 | 0.10, sensor +1 | 0.15, sensor +1 | `:175,179` / `:298,302` / `:429,433` |
| Scientific | `science_ship_survey_speed` 0.05, sensor +2, `ship_hyperlane_range_add` +1 | 0.10, +3, +2 | 0.15, +4, +3 | `:556-561` / `:681-686` / `:814-819` |
| Military | `ship_fire_rate_mult` and `ship_weapon_range_mult` 0.05, sensor +1 | 0.10 | 0.15 | `:941-946` / `:1065-1070` / `:1197-1202` |

- **All classes per tier:** hull 2500/5000/10000; armour and shields 1250/2500/5000; module capacity 2/4/7; building capacity 4/8/12; `planet_max_districts_add` 0/9/18; stockpile collection rate 125/375/625. Sources: `@arkship_*` values in `common/scripted_variables/10_scripted_variables_nomads.txt:195-256`, used at the lines above.
- **Civilian reserves:** Civilian Arkships add Operational Reserve capacity of 2500/5000/7500 by tier (`10_scripted_variables_nomads.txt:548-550`).
- **Scientific deep-scan bonus:** +5 / +10 / +15 Deep Scan success by tier, from the script value `arkship_scan_success_bonus` (`common/script_values/08_script_values_nomads.txt:607-629`). It keys on the vanilla `is_science_arkship` / `is_arkship_tier_N` triggers. This matches the 4.5 note "+5% Deep Scanning Success Chance per Tier".
- **Not found in the files:**
  - "Cannot be bombarded" (Military).
- **Military weapon slots** (corrected 2026-10-15; the first pass missed them): military hull sections carry medium and large turret slots that civilian and science sections lack. Tiers 1/2/3 have 4 medium / 4 medium + 4 large / 8 large (`common/section_templates/nomads_dlc.txt:729-1116`). Every Ark design is auto-generated (`is_designable = no`, `enable_default_design = yes`, `common/ship_sizes/29_nomads_dlc_ships.txt:951,959` for military tier 1). The military zones add weapon bonuses (`common/zones/05_arkship_zones.txt:545-760`).

**Tiers.**
- **How a tier changes:** by starbase upgrade (1.2). Tier 2 needs `tech_arkship_tier_2` and tier 3 needs `tech_arkship_tier_3`; both cost 5000 alloys + 250 influence.
- **No bioship cost branch:** the tier-upgrade cost has no `country_uses_bio_ships` alternative (`29_nomads_dlc_ships.txt:330-334`), unlike the Arkship megastructure, the Logistic Ship and the Waystation.
- **AI upgrades:** the AI upgrades mobile Arkships through the same path (4.5 AI note, section 4).
- **Script gate:** a starbase level can carry `upgrade_possible = { ... }` (`common/starbase_levels/02_orbital_ring_levels.txt:18-25`). The six Arkship levels do not use it.

**District set `nomad`** (`common/districts/07_ark_districts.txt`):

| Key | Display name (`localisation/english`) | Zone slots | Cost | Line |
|---|---|---|---|---|
| `district_ark_city` | Habitation Decks | `slot_city_government`, `slot_ark_city_01`, `slot_ark_city_02` | 250 alloys | `:4-145` |
| `district_ark_generator` | Reactor Arrays | `slot_ark_reactor` | 150 alloys; technician jobs | `:147-277` |
| `district_ark_basic` | Arkship Deck | `slot_ark_basic` | 150 alloys | `:279-391` |
| `district_ark_military` | Ordnance Engineering | `slot_ark_military` | 150 alloys | `:393-` |
| `district_ark_forever_cruise` | (Forever Cruise only) | | | `:596-` |

There is no Ark farming district. Food is the `zone_ark_food` zone ("Ark Hydroponics") on the Arkship Deck. No Ark district or zone has a biomass cost. `district_ark_military` already checks `owner = { is_wilderness_empire = no }` for its housing bonus (`:439-447`).

**Arkship zones** (`common/zones/05_arkship_zones.txt`), with the names the mod reuses:
- `zone_ark_food` "Ark Hydroponics" (`:428-442`)
- `zone_ark_farm_society` "Socio-Ecological Observation" (`:444-477`)
- `zone_ark_harvest` "Harvesting Bay" (`:479-491`)
- `zone_ark_waystation_trade` "Waystation Trade Hub" (`:493-541`, needs `tech_waystation_2`)
- `zone_ark_research_unity_gestalt` "Collective Research Complex" (`:169`)
- `zone_ark_foundry` "Military Logistics Complex" (`:250`)
- the military zones (`:545-885`)

Zone slots are defined in `common/zone_slots/00_zone_slots.txt:366-463`. Many Ark costs go through `common/inline_scripts/buildings/nomadic_cost_switcher.txt:1-13`, which charges nomads alloys × `NOMADIC_COST_MULT` instead of minerals; it is referenced 403 times.

**Starting capital Ark contents.**
- `generate_start_buildings_and_districts` (`common/scripted_effects/01_start_of_game_effects.txt:1593-1693`) runs `game_start/authority_hive_nomad` for hive nomads.
- That script is gated `is_hive_empire = yes`, `is_wilderness_empire = no`, `is_nomadic = yes` (`common/inline_scripts/game_start/authority_hive_nomad.txt:12-19`). It adds `building_hive_capital`, `building_spawning_pool`, `building_hive_warren`, two `district_ark_city` and the zones `zone_ark_research_unity_gestalt` + `zone_ark_foundry` (`:20-44`).
- The Wilderness start (`game_start/origin_wilderness.txt:10-40`) is gated on the raw `has_origin = origin_wilderness`. It sets planet size 18, removes all buildings, and adds `district_craglands` and Wilderness zones. Craglands require `uses_district_set = standard` (`common/districts/05_wilderness_districts.txt:20-23`), so none of it fits a `nomad` district set.
- **Result:** a nomad whose `is_wilderness_empire` is true gets no capital buildings or districts from vanilla.

**Precedent for a nomadic origin variant:** Knights of the Toxic God.
- `game_start/origin_toxic_knights.txt:114-145`, gated `has_origin = origin_toxic_knights` + `is_nomadic = yes`.
- Tags the capital Ark with `set_carrier_flag = toxic_knights_arkship`.
- Swaps a zone with `add_zone = { ... replace = yes }`, adds a building, and converts a start-system planet.

### 1.4 Waystations, Logistic Ships, Waylines

- **Waystation construction is a megastructure.** `waystation_megastructure` (`common/megastructures/30_nomad_waystation.txt:1-444`):
  - `build_type = outside_gravity_well`, `build_time = @waystation_build_time_180` (`:7`, `:10`); it becomes `starbase = starbase_level_waystation_1` (`:12`).
  - Cost: 100 influence plus 250 alloys; bioship users pay 125 alloys plus 250 × 1.75 = 437.5 food (`:14-51`).
  - `prerequisites = { "tech_waystation_1" }` (`:55`); `potential = { is_nomadic = yes ... }` (`:57-75`).
  - Limits: one per system (`requires_no_waystations`, `:152-159`); requires a surveyed system or intel (`:92-151`).
  - `on_build_complete` fires `on_waystation_built` with this = system, from = owner, fromfrom = starbase (`:411-417`) and sets the country flag `waystation_built` the first time (`:436-441`).
- **Levels:** `starbase_level_waystation_1..3` with 2/4/6 module slots (`common/starbase_levels/05_waystation_levels.txt:1-29`). The sizes are `starbase_waystation_1..3` (`common/ship_sizes/00_waystations.txt:4,85,196`).
- **Types:** `swaystation`, `swaystation_energy`, `swaystation_mining`, `swaystation_research`, `swaystation_piracy` (`common/starbase_types/01_waystation_types.txt:8-273`).
- **Modules** include the following (`common/starbase_modules/00_waystation_modules.txt`); weapon modules are in `01_waystation_weapon_modules.txt`:
  - `waystation_energy_module` (`:8`)
  - `waystation_mining_module` (`:120`)
  - `waystation_hydroponics_module` (`:281`)
  - `waystation_refinery_module` (`:371`)
  - `waystation_research_module` (`:650`)
  - `waystation_trade_module` (`:1284`)
  - `waystation_logistics_hub` (`:1674`)
  - `waystation_resource_silo` (`:1777`)
  - `waystation_observatory` (`:1972`)
  - `waystation_hyperlane_detailer` (`:3442`)
- **Techs:**
  - `tech_waystation_1`: start tech, prereq `tech_space_construction` (`common/technology/00_nomads_dlc_tech.txt:488`).
  - `tech_waystation_2` "Improved Wayline Logistics": tier 2 (`:520`).
  - `tech_waystation_3` "Advanced Wayline Logistics": tier 3 (`:603`).
- **Waylines are engine-side:** no script object defines them. The concept text: "interstellar trade routes formed by Waystations in neighboring systems… Waystation Modules provide effects and modifiers that are propagated along the Wayline" (`localisation/english/nomads_2_l_english.yml:378`). Script only reads the network (`benefits_from_waystation_network`, `common/scripted_triggers/09_scripted_triggers_nomads.txt:124-157`).
- **The Logistic Ship is `nomads_constructor`** (`common/ship_sizes/29_nomads_dlc_ships.txt:1337-1443`, comment "Logistic Ship handling" `:1338`):
  - Scripted actions `logistic_harvest_resources`, `collect_waystation_stockpile`, `logistic_drop_off_stockpile` (`:1339-1343`).
  - `ship_stockpile_capacity = 2500` (`:1346`); `construction_capabilities = { automated_collection megastructure }` (`:1348-1351`).
  - `class = shipclass_constructor` (`:1381`).
  - Cost 100 alloys, or 50 alloys + 175 food for bioships (`:1390-1417`).
  - `potential_country = { is_nomadic = yes ... }` (`:1425-1434`).
- **Engineering vessel:** `nomads_engineer_vessel` is the Eager Explorers variant (`:1445-1536`).
- **No Arkship construction:** Logistic Ships cannot build Arkships ("Cannot build new Arkships, which only other Arkships can do", `localisation/english/nomads_2_l_english.yml:390`), because the Arkship megastructure requires `from = { is_arkship_fleet = yes }`.

### 1.5 Operational Reserves, harvest actions, contracts, situations

**Operational Reserves are not a resource.**
- They are a resource converter plus a situation: `set_resource_converter = nomadic_operational_reserves` (`common/scripted_effects/01_start_of_game_effects.txt:5020`; converter at `common/resource_converters/00_resource_converters.txt:1-`).
- The converter lets energy and minerals pay for each other 1:1 and feeds `situation_nomad_economy` (`common/situations/14_nomads_situations.txt:947`), which `nomads.20` starts on game start (`events/nomads_events_1.txt:943-955`).
- Concept text: "a combined view of Minerals and Energy… One point of Operational Reserves is equal to one Mineral or one Energy Credit" (`localisation/english/nomads_2_l_english.yml:353`).
- Capacity: base 15000, plus 5000/7500/10000 per tier-1/2/3 Arkship and the Civilian extra (`common/scripted_variables/10_scripted_variables_nomads.txt:543-550`).

**Harvest actions are scripted actions** in `common/scripted_actions/03_arkships.txt`, not a `harvest_actions` folder:

| Action | Line | Gate | Cost | Progress | Yield duration | Depletion modifier |
|---|---|---|---|---|---|---|
| `arkship_harvest_resources` | `:367` | any Arkship | 0 | 90 | 120 months | 240 months |
| `arkship_strip_mine` | `:478-666` | Civilian tier 2+, or module `arkship_mining_array` or `arkship_planetary_refinery` (`:494-538`) | 500 **energy** (`:632-634`) | 180 | 300 months | 600 months |
| `arkship_reprocess_planet` | `:822-938` | module `arkship_planetary_refinery` (`:831-838`) | 5000 **energy** (`:900-902`) | 720 | 1200 months | 2400 months |
| `logistic_harvest_resources` | `:1651` | Logistic Ship | | 90 | 120 months | 240 months |

Numbers are from `common/scripted_variables/10_scripted_variables_nomads.txt:347-382`. The opinion penalties are −75 (harvest), −150 (strip mine) and −300 (reprocess).

- Targets must pass `is_valid_harvest_target`: not a star, not habitable, not artificial, not a ringworld, and a harvestable class (`common/scripted_triggers/09_scripted_triggers_nomads.txt:510-564`).
- The UI states efficacy as 100% / 250% / 600% (`localisation/english/nomads_1_l_english.yml:2901,3003,3036`). Script durations give 1× / 2.5× / 10× of a normal harvest (`common/scripted_effects/nomads_effects.txt:4690-4747`). The 600% figure does not match 1200/120 and the cause was not traced.
- Reprocessing fires `on_destroy_planet_with_arkship_reprocess_planet` unless the target is already `pc_shattered` or an asteroid. It then clears deposits, adds `d_minerals_3` and `d_alloys_1`, and sets the carrier flag `reprocessed_planet` (`nomads_effects.txt:4705-4747`). The tooltip says "Target is converted into a Shattered World" (`nomads_1_l_english.yml:3037`).
- Strip mining reduces planet size by 1 if it is over 5 (`nomads_effects.txt:4696-4701`).
- On-actions:
  - `on_harvest_resources` → `nomads.4100` (`common/on_actions/02_component_on_actions.txt:219-226`)
  - `on_arkship_strip_mine` → `nomads.4200` (`:228-233`)
  - `on_arkship_reprocess_planet` → `nomads.4400` (`:241-245`)
  - The harvest yield math is in `common/script_values/08_script_values_nomads.txt:701-786`.

**Contracts are missions** in `common/missions/missions/02_contracts.txt`, not a `common/contracts/` folder; the schema is in `common/missions/missions/99_README_MISSIONS.txt:1-126`.
- A contract needs `event_chain`; optional `time_to_accept`/`time_to_complete`.
- Hooks: `potential_issuer`/`possible_issuer`, `potential_operator`/`possible_operator`, `on_issue`, `on_accept`, `on_success`, `on_cancel`, `on_stop`, `abort_trigger`, `issued_abort_trigger`.
- Timers: `@contract_time_short/base/long` = 900/1800/2700 days (`10_scripted_variables_nomads.txt:471-473`).
- `can_issue_contracts_trigger` requires `is_nomadic = no` (`common/scripted_triggers/09_scripted_triggers_nomads.txt:405-426`).
- `can_accept_contracts_trigger` requires `is_nomadic = yes`, not Inward Perfection (`:445-446`), and not homicidal (`:448`).
- **The mod's "Reforest" already exists in vanilla:** `contract_clear_blockers` (`02_contracts.txt:1947-2139`). Its on_success clears natural blockers on the issuer's colony and pays minerals via `contract_scaling_reward_effect`.

**Nomad situations** (`common/situations/14_nomads_situations.txt`):

| Situation | Line | What it is |
|---|---|---|
| `situation_passenger_satisfaction` | `:5` | Forever Cruise; started by `nomads.1000` |
| `situation_core_conspiracy` | `:772` | |
| `situation_nomad_economy` | `:947` | Operational Reserves |
| `situation_grand_refurbishment` | `:1225` | Ark-targeted; started from `carrier_event arkship.1300` |
| `situation_arkship_lithoid_consume_planet` | `:1453` | lithoid Devouring Swarm "Consume World" |
| `situation_arkship_nanotech_consume_planet` | `:1545` | |

**Situation syntax in 4.5.2.** The authoritative schema is `common/situations/99_README_SITUATIONS.txt:1-322`. Points the scaffold gets wrong:
- `on_monthly` is an on_action block with `events`/`random_events`, not an effect (`:200-207`).
- Approaches take `allow`/`potential`/`on_select`/`default`/modifiers/`resources`/`ai_weight` (`:173-195`). There is no `on_deselect` (zero uses in vanilla) and no per-approach `monthly_progress`.
- The approach trigger is `current_situation_approach` (49 files use it). `is_using_situation_approach` does not exist.
- `on_progress_complete` must call `destroy_situation = this` (`:55-57`).
- `target_modifier` applies to "the target colony carrier" (`:79`).
- Vanilla template for an Ark-targeted situation: `situation_grand_refurbishment` (`common/situations/14_nomads_situations.txt:1225-1448`), started with `start_situation = { type = … target = root }` from a `carrier_event` (`events/nomads_arkship_events.txt:8375-8385`).

### 1.6 Settling

- The Settle action is `arkship_settle` ("Do not rename - name referenced in code", `common/scripted_actions/03_arkships.txt:1243-1329`):
  - Target: unowned and `is_colonizable = yes` (`:1273-1282`); the owner may not be a subject.
  - Cost 250 influence; progress 360 (`common/scripted_variables/10_scripted_variables_nomads.txt:405-407`).
  - Clickable only with `nomad_can_settle_trigger`: Adaptability or Versatility tradition finished (`common/scripted_triggers/09_scripted_triggers_nomads.txt:908-937`).
- `on_arkship_settle` (this = planet, from = fleet) → `nomads.4700` (`common/on_actions/02_component_on_actions.txt:380-387`). That event saves targets and fires country event `nomads.4705` (`events/nomads_events_1.txt:13521-13553`).
- `nomads.4705` options (`:13556-13780`):
  - **a** — exactly one Arkship: settle.
  - **b** — scuttle the other Arkships.
  - **c** — stay nomadic and release a settled subject.
  - **d** — settle and release the other Arks as a nomadic subject.

  Options c and d require `is_gestalt = no` (or Progenitor Hive), so a hive gets only **a** or **b**.
- `arkship_settle_planet_effect` (`common/scripted_effects/nomads_effects.txt:5053-5223`):
  - `set_country_as_nomad_effect = { IS_NOMAD = no }` (that is `set_country_code_flags = { is_nomadic = no }`, `:6-8`)
  - `set_resource_converter = none`
  - sets the flag `settled_as_nomadic_empire`
  - grants starting settled techs
  - creates a starport on the system
  - `transfer_carrier = { source = <ark> target = <planet> transfer_ownership = yes merge_pops = yes destroy_colony = yes }` (`:5187-5194`)
  - destroys the Ark fleet and spawns a constructor
  - adds `arkship_parts` to the planet (`:5219-5223`), which the tooltip lists as +60% habitability and +2 districts (`localisation/english/nomads_1_l_english.yml:3157`)
- Toxic Knights carry deposits from the Ark to the planet with `copy_deposit_from = { SCOPE = <ark> DEPOSIT = … }` (`:5156-5175`).
- A released settled subject gets `set_origin = origin_nomadic_settled` if its origin was `origin_default` (`events/nomads_events_1.txt:13717-13723`). `origin_nomadic_settled` is at `common/governments/civics/01_origins_non_playable.txt:311`.
- **Planets and Arks never coexist in one country, by design:** `nomads.1` hands any planet a nomad gains to a vassal, with the comment "Nomads should *never* have planets" (`events/nomads_events_1.txt:20-38`; fired from `on_colony_transfer`, `common/on_actions/00_on_actions.txt:3417`).
- `on_capital_changed` (this = new capital colony, from = old) exists (`00_on_actions.txt:3442-3454`). Whether settling fires it is not stated in the files: **unverified**.

### 1.7 Techs

`common/technology/00_nomads_dlc_tech.txt`. All but the start techs carry `potential = { is_nomadic = yes }`:

| Tech | Area / tier | Prerequisites | Unlocks | Line |
|---|---|---|---|---|
| `tech_arkship_construction` | engineering 3 | `tech_destroyers` OR `tech_weavers` (bioship) | Arkship megastructures | `:3-38` |
| `tech_arkship_tier_2` "Arkship Engineering" | engineering 3 | `tech_cruisers` OR `tech_harbingers`, + construction | tier-2 sizes | `:40-79` |
| `tech_arkship_tier_3` "Arkship Mastery" | engineering 4 | `tech_stingers` OR `tech_battleships`, + tier 2 | tier-3 sizes, Devastator and Capital Shipyard spinals | `:81-124` |
| `tech_civilian_arkship` / `tech_science_arkship` / `tech_military_arkship` | society / physics / engineering 3 | `tech_planetary_engineering` + construction (civilian) | class megastructure and ship size | `:126`, `:172`, `:218` |
| `tech_arkship_planetary_refinery` | engineering 4 | | Planetary Reprocessor spinal | `:268` |
| `tech_arkship_stellar_igniter`, `tech_arkship_system_scanner`, `tech_arkship_exodus_jump` | physics 4 | | other spinals | `:320`, `:367`, `:427` |
| `tech_nomads_mechanized_mining` | start | | `zone_ark_harvest` | `:469` |
| `tech_waystation_1/2/3` | engineering 0/2/3 | | Waystation levels | `:488`, `:520`, `:603` |
| `tech_planetary_engineering` | engineering 2 | | strip-mine module chain | `:681` |

**Spinal mounts are starbase modules, not components.** File: `common/starbase_modules/00_arkship_spinal_modules.txt`. All use `starbase_type = arkship`, `fits_on_slots = { spinal_mount_1 }` and `construction_days = @arkship_spinal_mount_build_time` (1800):

| Key | Display name | Gate | Line |
|---|---|---|---|
| `arkship_spinal_devastator_mount` | "Devastator Mount" | `tech_arkship_tier_3` + `tech_military_arkship`; `component_set = ARKSHIP_DEVASTATOR_WEAPON` | `:4-43` |
| `arkship_capital_shipyard` | "Capital Shipyard" | | `:45-104` |
| `arkship_planetary_refinery` | "Planetary Reprocessor" | `tech_arkship_planetary_refinery`; +25% harvest | `:106-152` |
| `arkship_stellar_igniter` | "Stellar Engine" | | `:154` |
| `arkship_system_scanner` | "Subspace Imaging Array" | | `:197` |
| `arkship_exodus_jump` | "Exodus Jump Coordinator" | | `:256` |

Any class can mount any spinal its owner has the tech for. The file header says new spinals must also be added to `common/inline_scripts/arkship/ai_design.txt` and the Arkship starbase types (`:1-2`). The strip-mine module `arkship_mining_array` ("Planetary Engineering Lab") is a utility module (`common/starbase_modules/00_arkship_utility_modules.txt:298`).

## 2. Wilderness

### 2.1 origin_wilderness and the identity tests

`origin_wilderness` (`common/governments/civics/00_origins.txt:591-662`):
- `playable = { has_biogenesis_dlc = yes }` (`:599-601`).
- `possible`:
  - hive authority, shown as "Mutualistic Intelligence" (`:604-609`)
  - graphical culture `biogenesis_01` or `biogenesis_02` (`:610-616`)
  - a NOR list of 13 hive civics (`:617-633`)
  - not volcanic (`:634-639`)
  - **`is_nomadic = no` (`:640`)**
- `flags = { uses_biomass custom_start_screen virtual_jobs }` (`:645-649`).
- `modifier = { planet_building_capacity_add = 5 }` (`:651-653`).
- `traits = { trait = trait_wilderness }` (`:659-661`).
- `room = "wilderness_room"`, `city_graphical_culture = "wilderness_01"` (`:596-597`).

**Devouring Swarm and Terravore.** The NOR list does not exclude `civic_hive_devouring_swarm`, so Wilderness may be a Devouring Swarm. The government `gov_blood_forest` is grouped with `gov_devouring_swarm` in `has_devouring_swarm_government` (`common/scripted_triggers/00_scripted_triggers.txt:1402-1405`). There is no `civic_hive_terravore`. Terravore is the lithoid presentation of `civic_hive_devouring_swarm` (`common/governments/civics/02_gestalt_civics.txt:289,355`). `is_lithoid_devouring_swarm` excludes `origin_wilderness` with the comment "Wilderness are not Terravores" (`00_scripted_triggers.txt:1397-1401`).

**Identity trigger.** `is_wilderness_empire = { has_origin = origin_wilderness }` (`common/scripted_triggers/00_scripted_triggers.txt:1766-1769`), with the comment "presents as an 'advanced_authority_swap' from auth_hive_mind".
- Usage: 1334 references in 191 files under `common/` and `events/`.
- Dependents include:
  - the authority swap `auth_mutualistic_intelligence` (`common/governments/authorities/00_authorities.txt:1385-1409`, trigger `:1391-1394`)
  - the biomass resource's visibility (`common/strategic_resources/00_strategic_resources.txt:485-491`)
  - every Wilderness job name swap (2.3)
  - the Wilderness districts' `potential` (`common/districts/05_wilderness_districts.txt:20-23`)
  - `wilderness.10`, `wilderness.100`, `wilderness.300`, `wilderness.310`
  - Ark-relevant `is_wilderness_empire = no` gates: `authority_hive_nomad.txt:16`, `07_ark_districts.txt:442`, `can_embark_no_tradition_check_trigger` (`09_scripted_triggers_nomads.txt:953`)

**Raw `has_origin = origin_wilderness` checks outside that trigger:** 41 lines in 16 files (42 counting the trigger itself):

| File | Lines | What it gates |
|---|---|---|
| `events/game_start.txt` | `:453` | `wilderness.150` stellar flare on game start |
| `common/inline_scripts/game_start/origin_wilderness.txt` | `:13` | capital planet setup (size 18, craglands, Wilderness capital buildings) |
| `common/scripted_effects/01_start_of_game_effects.txt` | `:2172` | Bodysnatcher species handling |
| `common/scripted_triggers/00_scripted_triggers.txt` | `:1400`, `:4676` | not-Terravore; `has_reduced_pop_origin` |
| `common/scripted_triggers/05_scripted_triggers_biogenesis.txt` | `:141`, `:192` | `has_purity_ascension`, `has_one_genetic_ascension_choice` |
| `common/terraform/02_special_terraform_links.txt` | `:5595-5856` (10 lines) | Wilderness terraforming links |
| `common/start_screen_messages/00_start_screen_messages.txt` | `:1852-1900` (6) | start-screen text |
| `events/biogenesis_events.txt` | `:597-929` (8) | biogenesis events |
| `common/situations/12_biogenesis_situations.txt` | `:568`, `:642` | biogenesis situation branches |
| `common/ascension_perks/00_ascension_perks.txt`, `00_ascension_paths.txt` | `:1034`; `:261`, `:559` | ascension gating |
| `common/policies/00_policies.txt` | `:3103` | policy |
| `common/technology/00_soc_tech.txt` | `:1959` | tech |
| `common/ai_budget/00_minerals_budget.txt` | `:293` | AI budget |
| `common/achievements.txt` | `:2458` | achievement |
| `events/progress_events.txt` | `:666` | progress event |

Wilderness and nomadism are mutually exclusive in vanilla, in three places:
- `origin_wilderness` requires `is_nomadic = no` (`00_origins.txt:640`).
- Wilderness and Tree of Life empires cannot Embark (`09_scripted_triggers_nomads.txt:953,965`).
- The nomadic hive start skips Wilderness empires (`authority_hive_nomad.txt:16`).

`origin_tree_of_life` also requires `is_nomadic = no` (`00_origins.txt:2775`).

**Nomadic status is a country code flag** that script can set: `set_country_code_flags = { is_nomadic = yes|no }` inside `set_country_as_nomad_effect` (`common/scripted_effects/nomads_effects.txt:6-8`). The 4.4 notes list `is_nomadic` as a new country trigger, government restriction and `create_country` option (section 4).

### 2.2 Biomass

- **Definition:** `biomass` is a strategic resource: `tradable = no`, `category = advanced`, `hide_gain = yes`, visible only to Wilderness empires (`common/strategic_resources/00_strategic_resources.txt:480-492`).
- **Biomass is the Wilderness pop count.**
  - `on_biomass_monthly` ("This.local_country_biomass = amount of wilderness pops", `common/on_actions/00_on_actions.txt:5523-5529`) fires `wilderness.10`.
  - `wilderness.10` sets the stockpile to that count every month: `set_resource = { resource = biomass value = root.local_country_biomass }` (`events/origin_events_wilderness.txt:40-55`).
  - The on_action only runs for countries with the `uses_biomass` country flag (`COUNTRY_USES_BIOMASS_FLAG`, `common/defines/00_defines.txt:1218`). The flag comes from the origin's `flags` block.
- **Spending biomass kills pops.**
  - The 4.0 notes: "When a resource is spent to build a building or enact a decision, it is stored locally and can be accessed in script such as on_queued and on_unqueued" (section 4).
  - Vanilla costs pair `cost = { biomass = N }` with an `on_queued` that kills `local_spent_biomass` pops of a `trait_wilderness` pop group and an `on_unqueued` that recreates them (`common/inline_scripts/buildings/on_all_wilderness_buildings_districts.txt:13-43`; `common/decisions/14_wilderness_decisions.txt:19-64`).
  - Helper effects are `global_biomass_spender` and `unqueue_biomass_effect` (`common/scripted_effects/biogenesis_effects.txt:2810`, `:2899`).
- **Consequences for the mod:**
  1. A biomass `cost` without the `on_queued` kill is refunded at the next monthly reset.
  2. `add_resource = { biomass = ±N }` and situation `upkeep = { biomass = N }` are overwritten by `wilderness.10` within a month.
  3. A modifier on biomass "production" has nothing to act on.

  No vanilla script uses a `planet_jobs_biomass_produces_*` modifier: `rg` for every `*biomass*` key in `common/` finds only `biomass`, `unqueue_biomass_effect`, `global_biomass_spender`, `biomass_cost`, `on_biomass_monthly` and four `biomass_expenditure_*` AI keys. Whether the engine auto-generates such a modifier is **unverified**.
- **District cost:** Wilderness districts cost minerals plus `biomass = @biomass_cost` (50). For example, craglands is 500 minerals + 50 biomass (`common/districts/05_wilderness_districts.txt:1-3`, `:48-59`), and photosynthesis fields is 300 minerals + 50 biomass (`:205-212`).

### 2.3 Jobs

There are no separate Wilderness job keys. `common/pop_jobs/14_wilderness_jobs.txt` holds only two variables. Wilderness empires work the ordinary gestalt jobs, which switch name and icon through a `swap_type` keyed on `owner? = { is_wilderness_empire = yes }` (`common/pop_jobs/04_gestalt_jobs.txt`):
- `technician_drone` → photosymbiont (`:2354`)
- `mining_drone` → grubsymbiont (`:2090`)
- `agri_drone` → pollinosymbiont (`:2247`)
- `warrior_drone` → tamersymbiont (`:2612`)
- `coordinator` → choirsymbiont (`:488`)
- `calculator_physicist/biologist/engineer` → evosymbiont_* (`:703`, `:983`, `:1199`)
- `fabricator` → forgesymbiont (`:1457`)
- `logistics_drone` → spindlesymbiont (`:1955`)
- `patrol_drone` → immunosymbiont (`:1791`)
- `spawning_drone` → broodsymbiont (`:42`)
- `transference_drone` → neurosymbiont (`common/pop_jobs/15_biogenesis_jobs.txt:379`)

`wilderness_maintenance_drone` is a real separate job (`04_gestalt_jobs.txt:2562`).

Names the mod uses that are vanilla **zones**, not jobs (`localisation/english/wilderness_l_english.yml`):
- "Heartgrove" = `zone_urban_wilderness` (`:285`)
- "Orespine" = `zone_minerals_wilderness` (`:300`)
- "Solar Mycolungs" = `zone_energy_wilderness` (`:303`)

The zones are defined in `common/zones/03_wilderness_zones.txt:5-478`. "Nectar Gatherer" and "Mycelial Comptroller" do not exist in vanilla localisation. "Synapse Drone" is the hive job `synapse_drone` (`04_gestalt_jobs.txt:608`), which has no Wilderness name.

The 4.0.22 notes say Wilderness jobs are "automatically worked without requiring biomass… worked by your primary species" (section 4).

### 2.4 Bioships

- **How an empire gets bioships:** `country_uses_bio_ships = { uses_ship_category = bio_ship }` (`common/scripted_triggers/07_scripted_triggers_ships.txt:33-36`). The `bio_ship` kind belongs to the graphical cultures `biogenesis_01` and `biogenesis_02` (`common/graphical_culture/00_graphical_culture.txt:1501-1535`, `:1434-1439`), which is why `origin_wilderness` requires them.
- **Hulls:** maulers, weavers, harbingers and stingers in stages 1-3 (`common/ship_sizes/00_biogenesis.txt:1-1271`), with `ship_category = bio_ship`, `evaluation_resource = food` and growth by `bioship_growth_progress_required`.
- **The food cost is a per-size cost branch, not a global override.** The pattern is "half alloys + alloys × 1.75 food" (`@halved_alloy_to_food_cost_ratio`, `common/scripted_variables/08_scripted_variables_biogenesis.txt:13-14`). The pattern appears on:
  - the Logistic Ship (`29_nomads_dlc_ships.txt:1390-1417`)
  - the science ship (`common/ship_sizes/00_ship_sizes.txt:1448-1475`, size starts `:1410`)
  - the Waystation (`30_nomad_waystation.txt:31-50`)
  - the Arkship megastructure (`common/inline_scripts/megastructures/arkship.txt:17-30`)

  So a bioship nomad already pays food for these four without mod changes. Arkship **tier upgrades** have no food branch (1.3).
- **Starting fleet:** the Wilderness bioship start fleet (two `mauler_stage_1` + one `weaver_stage_1`) is spawned only for `is_nomadic = no` (`events/game_start.txt:1471-1512`).

### 2.5 Planet class

- There is **no Wilderness planet class**: `pc_wilderness*` has zero hits in `common/`, `events/`, `localisation/`. Wilderness homeworlds use ordinary habitable classes. The start script sets size 18 and the carrier flag `wilderness_planet` (`common/inline_scripts/game_start/origin_wilderness.txt:16-18`).
- A colonised world becomes "Wilderness" through `wilderness.500` and the Wilderness district set rules (`events/origin_events_wilderness.txt:296-`).
- Planet classes the scaffold names:
  - `pc_shattered` (`common/planet_classes/00_planet_classes.txt:1726-1746`, size 12-30, not colonizable)
  - `pc_barren` (`:587`)
  - `pc_asteroid` (`:498`)
  - `pc_gas_giant` (`:468`)
  - `pc_frozen` (`:664`)
  - `pc_gaia` (`:955`)
- The star class `sc_g` is at `common/star_classes/00_star_classes.txt:73`.

## 3. Tree of Life

- **Origin:** `origin_tree_of_life` (`common/governments/civics/00_origins.txt:2758-2793`):
  - `max_once_global = yes`; hive authority; not Devouring Swarm (`:2766-2768`); not volcanic; **`is_nomadic = no`** (`:2775`).
  - Modifier `ship_colonizer_food_cost_add = 500` and `ship_colonizer_alloys_cost_mult = -0.50` (`:2781-2784`). That is the "colony ships carry a sapling" cost.
  - No `playable` DLC gate in script. The DLC recommender ties it to the Plantoids pack (`dlc_metadata/dlc_recommendations/00_dlc_recommendations.txt:13`, `:36-46`), but whether the engine requires Plantoids is **unverified**.
  - Terravore is not excluded by Tree of Life; that civic key does not exist.
- **Deposits** (`common/deposits/07_federations_deposits.txt`):
  - `d_tree_of_life_home` (`:579-609`): `logistic_growth_mult` 0.15, `planet_jobs_society_research_produces_mult` 0.10, `planet_housing_add` 1000, `district_farming_max_add` 4, upkeep 4 food.
  - `d_tree_of_life_colony` (`:612-644`): 0.10 / 0.10 / 500 / 2, upkeep 2 food.
  - Both have `potential` requiring the **owner to have `origin_tree_of_life`** (`:585-590`, `:618-625`). Per the deposits README, `potential` decides whether the type can be picked when randomizing (`common/deposits/99_README_DEPOSITS.txt:4`) and is re-checked on terraforming (`:25-26`); nothing says it disables a deposit added by script.
  - The home deposit is placed at start (`common/scripted_effects/01_start_of_game_effects.txt:2408-2413`). New colonies get the colony deposit from `origin.3001` on `on_colonized` (`events/origin_events_3.txt:10-27`; `common/on_actions/00_on_actions.txt:1309`).
- **Lost tree:** static modifier `no_lifetree` (`common/static_modifiers/11_static_modifiers_federations.txt:331-338`): `planet_building_build_speed_mult` −0.5, `planet_jobs_produces_mult` −0.25, `planet_jobs_upkeep_mult` +0.10, `planet_stability_add` −5.
- **Destruction:**
  - `origin.3003` (`events/origin_events_3.txt:52-83`) triggers on `planet_devastation >= 50` (`:63`), removes the deposits and adds `no_lifetree`. It is fired from `on_planet_bombarded` (`common/on_actions/00_on_actions.txt:2157-2170`), so only bombardment devastation checks it.
  - `origin.3002` silently removes the tree on `on_colony_destroyed` and `on_colony_transfer` (`00_on_actions.txt:1343`, `:3377`).
  - `origin.3004` adds or removes `no_lifetree` on transfer (`:3378`; `events/origin_events_3.txt:85-128`).
- **Transplant:** `decision_transplant_tree` (`common/decisions/06_federation_decisions.txt:5-36`): `enactment_time = 180`, `cost = { food = 500 }`, `potential = { has_modifier = no_lifetree }`, and effect `add_deposit = d_tree_of_life_colony` + `remove_modifier = no_lifetree`.
- **Saplings for others:** a Tree of Life overlord's subjects can build the holding `holding_tree_of_life_sapling` (1000 food, `common/buildings/15_overlord_holdings.txt:1568-1600`). Vanilla has no "sapling deposit" that a contract could hand out.
- **Settling as Tree of Life:** when a nomad's planet is spun off into a vassal, the vassal becomes `origin_tree_of_life` if the nomad was (`events/nomads_events_1.txt:149-155`). No vanilla nomad can have that origin, because it requires `is_nomadic = no`.

## 4. Patch notes 4.4 to 4.5.2

Source: stellaris-wiki MCP `wiki_patch_notes` (filter "all") plus the wiki "Patch 4.x.X" pages for point releases. The full verbatim harvest is in `patch-notes-harvest-4.0-4.5.2.md`. The tool drops sub-bullets and point releases. 4.1.x and 4.2.x were not read. The install wins every conflict. Quotes are verbatim.

| Version | Date | Line | Relevance |
|---|---|---|---|
| 4.0 "Phoenix" | 2025-05-05 | released with BioGenesis | Wilderness origin |
| 4.0 | | "When a resource is spent to build a building or enact a decision, it is stored locally and can be accessed in script such as "on_queued" and "on_unqueued", see Wilderness buildings for an example" | biomass spend = kill pops (2.2) |
| 4.0.3 | 2025-05-06 | "Districts for Wilderness Empires now cost 50 Biomass" | matches `@biomass_cost = 50` (`05_wilderness_districts.txt:3`), on top of minerals |
| 4.0.17 | 2025-06-11 | "Wilderness empires now start with a Heartgrove specialisation instead of a Threadway specialisation" | Heartgrove is a zone |
| 4.0.22 | 2025-08-21 | "all Wilderness jobs are now automatically worked without requiring biomass. These jobs are worked by your primary species." | 2.3 |
| 4.3 "Cetus" | 2026-03-17 | (no Nomads content) | |
| 4.4 "Pegasus" | 2026-06-15 | "The patch was released alongside the Nomads expansion… patch 4.4.0 was never released to the public instead making patch 4.4.1 the first in the 4.4.X branch" | README version row |
| 4.4 | | "After completing the Adaptability or Versatility tradition, Nomadic empires can Settle on an empty planet, with options to settle partially or fully and leave the remainder as a subject. Settled empires can Embark onto Arkships through a mirrored process" | 1.6 |
| 4.4 Modding | | "Add is_nomadic country trigger"; "Add is_nomadic government restriction"; "Added is_nomadic option for create_country"; "new carrier_is_type trigger"; "Renamed system_colony script lists to system_planet_colony."; "Add time_to_accept and time_to_complete to missions"; "Added potential trigger for situation types"; "Added planet_artificial_max_districts_add…" | syntax |
| 4.4.4 | 2026-06-24 | "Arkship now have an inherent 40% habitability, matching that of habitats." | `pc_ark` `pop_environment_tolerance = 0.40` (`06_planet_classes_nomads.txt:101-103`) |
| 4.4.4 | | "Planet class modifiers now apply to Arkship (ship-carried) colonies." | H5 |
| 4.4.4 | | "Fixed numerous modifiers (such as the Stability from Bureaucrats granted by Byzantine Bureaucracy) not granting their modifiers to Arkships. Note to modders: Any scripted modifier needs to be updated to work on colonies rather than planets." | docs/04 §7; a bugfix note, not a "rework" |
| 4.4.4 | | "Nomad strategic-resource harvesting reworked. Researching a resource's mining technology now grants a +50% harvesting yield…" | `@arkship_mining_tech_harvest_bonus = 0.5` |
| 4.4.5 | 2026-07-07 | "Maximum Operational Reserves now scale with the number and tier of owned Arkships (base 15,000, plus 5,000 / 7,500 / 10,000 per Tier I / II / III Arkship)…"; "Civilian Arkships grant an additional 2,500 Operational Reserve capacity per Tier." | matches `10_scripted_variables_nomads.txt:543-550` |
| 4.4.5 | | "Reduced the opinion penalties empires hold against Nomadic Empires for strip-mining, reprocessing planets, and Exodus Jumps…" | |
| 4.4.5 Modding | | "Added home_colony and background_colony scopes…"; "Situations can define total_progress with per-stage section_weight…" | |
| 4.5 "Cygnus" | 2026-09-22 | "Scientific Arkships now have +5% Deep Scanning Success Chance per Tier" | `arkship_scan_success_bonus` |
| 4.5 | | "Unlocked Knights of the Toxic God for nomads" | precedent for a nomadic origin variant (1.3) |
| 4.5 | | "The Nomadic Gameplay toggle now grays out immediately for multiplayer clients…" | nomadic is a designer toggle (H1) |
| 4.5 | | "Wilderness and Synthetic Fertility now start with homeworlds of the proper size" | |
| 4.5 | | "Fixed the colony designation being ignored when a Nomad empire settles an Arkship on a planet." | |
| 4.5 | | "The End of the Cycle Creation Accord no longer grants Nomad empires a colonized planet they cannot hold." | planets and Arks do not coexist |
| 4.5 | | "Nomad AI now builds new Arkships and upgrades them… Tier upgrades were never triggered for mobile Arkships and now fire once the enabling technology is researched" | AI uses the vanilla upgrade path |
| 4.5 Modding | | "set_planet_flag, remove_planet_flag, set_timed_planet_flag and has_planet_flag now also work in colony, carrier, and ship scope (previously planet scope only), operating on the same flag store as the carrier_flag family." | planet flags on Ark colonies are valid |
| 4.5.2 | 2026-10-06 | "Nomadic Knights of the Toxic God can now settle once they reach the Toxic God system, and keep the Order's Keep and their Knights on the new homeworld." | settle precedent (`copy_deposit_from`) |
| 4.5.2 | | "Logistic Ships now show Waystations in the Megastructure Build Menu alongside their dedicated Build Waystation Button" | |
| 4.5.2 | | "Nomadic Synthetic Fertility no longer starts with a size 20 Arkship." | |

Not found in any notes read:
- "Tree of Life" (in 4.0-4.5.2).
- Any developer statement that planets and Arks cannot coexist. The script comment in `nomads.1` says so instead (1.6).
- The harvest efficiency percentages.
- "1000 pops".
- "colony size 9 to 18".

## 5. Searches that came back empty

| Searched for | Where | Result |
|---|---|---|
| `origin_voidfarers` | `common/`, `events/`, `localisation/english/` | 0 hits; Voidfarers is `origin_default_nomads` |
| `is_nomadic_empire`, `is_nomad_empire` | `common/scripted_triggers/` | 0; `is_nomadic` is a code trigger |
| `pc_arkship`, `pc_wilderness*` | `common/`, `events/` | 0; the Ark class is `pc_ark`; there is no Wilderness class |
| `common/contracts/`, `common/harvest_actions/` | install | folders do not exist |
| Arkship keys in `common/megastructures/` named `arkship_<class>_<tier>` | | none; megastructures are only the three construction sites plus `arkship_ruined` |
| `civic_hive_terravore` | `common/governments/` | 0 |
| `pop_growth_speed`, `planet_jobs_research_produces_mult` | `common/` | 0 uses; vanilla uses `logistic_growth_mult` and `planet_researchers_produces_mult` or the three `planet_jobs_*_research_produces_mult` |
| `is_using_situation_approach`, `on_deselect` | `common/`, `events/` | 0 |
| `GFX_situation_approach_default/aggressive/passive` | `interface/`, `gfx/` | not defined |
| hull-percent triggers `hull_percent`, `ship_hull_percent`, `has_hull` | `common/`, `events/` | 0; the real one is `has_hp_percentage` (ship scope, 0-1 fraction), e.g. `events/cosmic_storms_events_1.txt:822` |
| `nomadic_operational_reserves` loc / a separate reserves resource | `common/strategic_resources/` | not a resource; it is a resource converter |
| "Nectar", "Mycelial Comptroller" | `localisation/english/` | 0 |
| "cannot be bombarded" for Military Arkships | `29_nomads_dlc_ships.txt`, `static_modifiers` | not found |
| Starting Arkship spawn effect in any initializer | `common/solar_system_initializers/` | none; the engine creates it from the designer's `ship_size` |
