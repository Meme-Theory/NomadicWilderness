# Install audit against Stellaris 4.5.2

*Audited: 2026-10-15 | Install: v4.5.2 "Cygnus" (`launcher-settings.json` `rawVersion`) | Mod state: commit `4e31d39` plus the edits listed in §7*

Every vanilla citation is `path:line` relative to `C:/Program Files (x86)/Steam/steamapps/common/Stellaris`. The mechanics behind these verdicts are in `vanilla-nomads-4.5.2.md`, cited below as **VN §n**.

## 0. Summary

- **H-items:** 4 confirmed (H2, H10, H11, H12), 7 partly (H3, H5, H9, H13, H14, H17, H18), 7 refuted (H1, H4, H6, H7, H8, H15, H16).
  - H15 is refuted on its premise: no separate Wilderness sizes exist. Its conclusion (the Seedwing can reuse vanilla sizes) holds.
  - 16 new dependencies are recorded as H19-H34 in `docs/04-vanilla-hooks.md`.
- **The scaffold's object model was wrong in three places that drive the rest:**
  1. Nomadic is a designer toggle, not origin fields.
  2. An Arkship is a starbase-class ship carrying a `pc_ark` colony, not a megastructure.
  3. Biomass is the Wilderness pop count, so "biomass income/drain/credit" means pop growth/death.
- **Wilderness and Nomads are kept apart in vanilla on purpose** (VN §2.1). The mod has to bridge that: the identity override, the capital Ark setup, and the biomass handling.
- **Placeholders:** 32 in 13 files → 20 in 9 files. 12 were resolved from the install. The rest were rewritten to point at the real vanilla location, and four new ones mark invalid code that was removed.
- **Decisions:** all fourteen were decided on 2026-10-15 (§6), and so were the three calls left open (§10). D7's sub-choice is W (`set_origin = origin_wilderness` at settle), the Heartwood's flat growth is dropped, and Deep Taproot gates Husk collection. The decision pass took the placeholders from 20 in 9 files to 8 in 4 (§9), the follow-up to 7 (§10).

## 1. H1-H18 verdicts

| ID | Assumption (docs/04) | Verdict | What is true | Evidence |
|---|---|---|---|---|
| H1 | Nomadic status is carried by fields on the origin (`origin_voidfarers` vs `origin_default`) | **Refuted** | `origin_voidfarers` does not exist; Voidfarers is `origin_default_nomads`. Nomadic is a country code flag set by the designer's Nomadic toggle; origins only test it in `possible`. The doc's own fallback ("a separate empire-designer toggle that filters origins") is what is true. | `common/governments/civics/00_origins.txt:57-93` (`possible = { is_nomadic = yes }` at `:73-75`); `prescripted_countries/00_top_countries.txt:319`; `common/scripted_effects/nomads_effects.txt:6-8` (`set_country_code_flags = { is_nomadic = … }`); 4.5 note "Nomadic Gameplay toggle" |
| H2 | Vanilla identifies Wilderness through a scripted trigger `is_wilderness_empire` | **Confirmed** | The body is one line, `has_origin = origin_wilderness`. 1334 references in 191 files. 41 more raw `has_origin = origin_wilderness` checks in 16 files bypass it (VN §2.1). Plan B evidence: D1. | `common/scripted_triggers/00_scripted_triggers.txt:1766-1769` |
| H3 | Nomadic identity via a scripted trigger (`is_nomadic_empire`); the Ark colony via a trigger or class check | **Partly** | No nomad scripted trigger exists; `is_nomadic` is an engine trigger (4.4 Modding: "Add is_nomadic country trigger"). The Ark colony test is the class check `is_planet_class = pc_ark`. Ark ships, fleets and starbases are scripted triggers that list the ten vanilla sizes by name. | `common/inline_scripts/megastructures/arkship.txt:69`; `common/scripted_triggers/09_scripted_triggers_nomads.txt:159-369` |
| H4 | Arkships are `common/megastructures/` entries, three tiers per class, with an associated ship_size | **Refuted** | An Ark is a ship size (`<class>_arkship_tier_<n>`, `class = shipclass_starbase`, `carries_colony = pc_ark`). Tiers are starbase levels, upgraded like starbases. The only megastructures are the three construction sites that build a new Ark, plus `arkship_ruined`. | `common/ship_sizes/29_nomads_dlc_ships.txt:104-1251`; `common/starbase_levels/06_arkship_levels.txt`; `common/megastructures/29_nomad_arkships.txt:1-187` |
| H5 | The Ark colony has a planet class (`pc_arkship` or per class); 4.4.4 made planet-class modifiers apply | **Partly** | One class, `pc_ark` (size 9, `district_set = nomad`, 40% habitability), with per-size icons. 4.4.4: "Planet class modifiers now apply to Arkship (ship-carried) colonies." The deposit `potential` is now `is_planet_class = pc_ark`. | `common/planet_classes/06_planet_classes_nomads.txt:1-108` |
| H6 | Ark type bonuses are static modifiers applied by the megastructure stage | **Refuted** | They are `modifier`/`ship_modifier` blocks on each ship size. Reserve capacity comes from scripted variables, and the Scientific deep-scan bonus from a script value. | `29_nomads_dlc_ships.txt:167-180` etc.; `common/scripted_variables/10_scripted_variables_nomads.txt:543-550`; `common/script_values/08_script_values_nomads.txt:607-629` |
| H7 | Spinal mounts are `component_templates` with `size_restriction`; Reprocessor and Devastator are two entries | **Refuted** | They are starbase modules (`fits_on_slots = { spinal_mount_1 }`, `starbase_type = arkship`), with no class restriction. The Devastator module adds a component set. | `common/starbase_modules/00_arkship_spinal_modules.txt:4-332`; `common/component_templates/00_nomads_dlc_components.txt:6-47` |
| H8 | Harvest actions live in `common/harvest_actions/` or situations | **Refuted** | They are scripted actions with on_actions and effects. No `harvest_actions` folder exists. | `common/scripted_actions/03_arkships.txt:367,478,822,1651`; `common/on_actions/02_component_on_actions.txt:219-264`; `common/scripted_effects/nomads_effects.txt:4690-4747` |
| H9 | Ark colony backgrounds live under `gfx/interface/` keyed by planet class or Ark type | **Partly** | Keyed per ship size: `planet_view_header` (`interface/planet_view.gfx:4353-4364`), `hero_ship_fleet_background` (`interface/fleet_view.gfx:2758`), `arkship_picture` → `gfx/portraits/arkships/ships/arkship_civilian.dds`. The starbase-level `picture` blueprint (`interface/icons.gfx:734`) supports triggered pictures (`common/starbase_levels/00_documentation.txt:12-15`). Planet icons come from `pc_ark` `conditional_icon` (`interface/planet.gfx:198`). | `29_nomads_dlc_ships.txt:109-118` |
| H10 | Ark districts use the district + zone system, and the Arkship Deck has its own key | **Confirmed** | `district_ark_basic` "Arkship Deck"; zones are in `common/zones/05_arkship_zones.txt`. There is no `common/district_specializations/` folder. | `common/districts/07_ark_districts.txt:279-391` |
| H11 | Tree of Life is a deposit with a sapling variant, a "Lost Tree of Life" modifier and a food-cost transplant decision | **Confirmed** (keys differ) | `d_tree_of_life_home`/`_colony`; `no_lifetree`; `decision_transplant_tree` (500 food, 180 days). There is no sapling deposit; the "sapling" is the colony deposit. Both deposits' `potential` names an `origin_tree_of_life` owner, but per the deposits README `potential` only decides random placement (`common/deposits/99_README_DEPOSITS.txt:4`) and terraforming swaps (`:25-26`). | `common/deposits/07_federations_deposits.txt:579-644`; `common/static_modifiers/11_static_modifiers_federations.txt:331-338`; `common/decisions/06_federation_decisions.txt:5-36` |
| H12 | Tree destruction is scripted on devastation | **Confirmed** | `origin.3003`: `planet_devastation >= 50`, fired from `on_planet_bombarded`. `origin.3002` and `origin.3004` handle colony destruction and transfer. | `events/origin_events_3.txt:52-128`; `common/on_actions/00_on_actions.txt:1343,2170,3377-3378` |
| H13 | `biomass` is a strategic resource, spendable in cost blocks | **Partly** | The key exists and is spendable, but the spend only sticks if `on_queued` kills that many `trait_wilderness` pops. `wilderness.10` resets the stockpile to the pop count every month. | `common/strategic_resources/00_strategic_resources.txt:480-492`; `events/origin_events_wilderness.txt:40-55`; `common/decisions/14_wilderness_decisions.txt:19-64` |
| H14 | Wilderness jobs are their own keys (photosymbiont, grubsymbiont, synapse drone), auto-worked | **Partly** | No own keys: they are `swap_type` names on gestalt jobs, switched by `is_wilderness_empire`. `synapse_drone` has no Wilderness name. Auto-work is confirmed by the 4.0.22 note and the `virtual_jobs` flag. | `common/pop_jobs/04_gestalt_jobs.txt:2090,2247,2354,2612`; `common/defines/00_defines.txt:1548` |
| H15 | Wilderness ship costs are a per-size food swap; the Seedwing can reuse the Wilderness construction and science sizes | **Refuted** on the premise | No Wilderness ship sizes exist. Bioship costs are per-size `cost` branches keyed on `country_uses_bio_ships`, and the nomad Logistic Ship, science ship, Waystation and new-Ark megastructure already carry them. The conclusion holds: the Seedwing can reuse vanilla sizes. | `29_nomads_dlc_ships.txt:1390-1417`; `common/ship_sizes/00_ship_sizes.txt:1448-1475`; `common/megastructures/30_nomad_waystation.txt:31-50`; `common/inline_scripts/megastructures/arkship.txt:17-30` |
| H16 | Vanilla Wilderness has a start initializer and a homeworld class; the Ark is placed as Voidfarers places it | **Refuted** | `origin_wilderness` lists no initializers. There is no Wilderness planet class. Voidfarers has no initializer and no script spawns its Ark. An origin initializer uses `usage = origin`. | `00_origins.txt:591-662`; `common/solar_system_initializers/example.txt:27-30` |
| H17 | Contracts live in `common/contracts/` with issuer potential, acceptor possible, duration and rewards | **Partly** | They are missions in `common/missions/missions/02_contracts.txt` with `potential_issuer`/`possible_issuer`/`potential_operator`/`possible_operator`, `time_to_accept`, `event_chain` and `on_success`. "Remote Mineral Assessment" is the internal `contract_onboard_minerals_resource` (`:2809`). | `common/missions/missions/99_README_MISSIONS.txt:1-126` |
| H18 | Situations use the stable 3.4+ format | **Partly** | The format holds, but 4.4/4.5 added `potential`, `show_in_outliner` and `total_progress`/`section_weight`. The scaffold used four keys that do not exist (fixed, §3.10). | `common/situations/99_README_SITUATIONS.txt:1-322` |

## 2. New dependencies (H19+)

These are recorded in `docs/04-vanilla-hooks.md` with the same IDs.

| ID | Dependency | Evidence |
|---|---|---|
| H19 | The empire designer's Nomadic toggle; the origin must require `is_nomadic = yes`. Script can flip it with `set_country_code_flags`. | `00_origins.txt:73-75`; `nomads_effects.txt:6-8` |
| H20 | Colony and carrier scopes: `colony_event`, `carrier_event`, `*_owned_colony`, `ship?`, `fleet`, `colony?`, `solar_system?`, `carrier_is_type`. Planet flags work on colonies since 4.5. | `events/game_start.txt:1724-1796`; `events/nomads_arkship_events.txt:5005-5008`; `09_scripted_triggers_nomads.txt:341`; 4.5 Modding note |
| H21 | Biomass equals the Wilderness pop count; the `uses_biomass` flag; spending through `on_queued` kills | `events/origin_events_wilderness.txt:40-55`; `common/defines/00_defines.txt:1218`; `common/scripted_effects/biogenesis_effects.txt:2810,2899` |
| H22 | The engine creates the starting Ark from the designer `ship_size`; `home_arkship_initial_setup` sets `starting_<class>_arkship` | `prescripted_countries/default.txt:23`; `common/scripted_effects/01_start_of_game_effects.txt:5202-5260`; `common/on_actions/00_on_actions.txt:4055-4064` |
| H23 | The nomad hive start skips Wilderness; the Wilderness start is gated on the raw origin key | `common/inline_scripts/game_start/authority_hive_nomad.txt:12-19`; `common/inline_scripts/game_start/origin_wilderness.txt:10-40` |
| H24 | Tier change by `set_starbase_size`; a starbase level can carry `upgrade_possible` | `events/game_start.txt:3487-3503`; `common/starbase_levels/02_orbital_ring_levels.txt:18-25` |
| H25 | New-Ark construction megastructure: cost, potential, `create_arkship_effect` | `common/inline_scripts/megastructures/arkship.txt:1-86`; `nomads_effects.txt:6982-6997` |
| H26 | Hooks: `on_waystation_built` (system scope), `on_entering_system_fleet`, `on_fleet_exit_battle`, `on_ship_disabled`, `on_damage_taken`, `on_arkship_settle`, `on_capital_changed`, `on_colony_transfer` | `00_on_actions.txt:5916-5928,973-976,1758-1768,2038-2058,1492-1500,3442-3454,3369-3422`; `02_component_on_actions.txt:380-387` |
| H27 | Settle flow: `arkship_settle`, `nomads.4700`/`.4705`, `arkship_settle_planet_effect`, `nomad_can_settle_trigger`, `copy_deposit_from` | `03_arkships.txt:1243-1329`; `events/nomads_events_1.txt:13521-13780`; `nomads_effects.txt:5053-5223`; `09_scripted_triggers_nomads.txt:908-937` |
| H28 | Contract issue/accept triggers; `contract_clear_blockers` already does "Reforest" | `09_scripted_triggers_nomads.txt:405-449`; `02_contracts.txt:1947-2139` |
| H29 | Hull test `has_hp_percentage` (ship scope, 0-1) | `events/cosmic_storms_events_1.txt:822` |
| H30 | `wilderness.300` adds Wilderness deposits to colonies captured from a Wilderness empire | `events/origin_events_wilderness.txt:150-262`; `00_on_actions.txt:3413` |
| H31 | Tree of Life deposits name an `origin_tree_of_life` owner in `potential`. The deposits README says `potential` only decides random placement and terraforming swaps, so whether a script-added deposit works for any owner is a test item | `07_federations_deposits.txt:585-590,618-625`; `common/deposits/99_README_DEPOSITS.txt:4,25-26` |
| H32 | Operational Reserves are a resource converter plus a situation, not a resource | `common/resource_converters/00_resource_converters.txt:1-`; `common/situations/14_nomads_situations.txt:947`; `localisation/english/nomads_2_l_english.yml:353` |
| H33 | Arks with a leader can already survey | `03_arkships.txt:66-131` |
| H34 | Ark model selection: section entity plus graphical culture; Ark ship entities carry `district_01..09` attach points | `common/HOW_TO_MAKE_NEW_SHIPS.txt:52-69`; `common/section_templates/nomads_dlc.txt:194-237`; `gfx/models/ships/other/_other_entities.asset:4713-4743` |

## 3. Vanilla keys the scaffold references

Status: ✅ exists as used, 🔧 fixed in this pass, ⚠ exists but misused (left for the plan or a decision), ❌ does not exist (replacement given).

### 3.1 Origin (`common/governments/civics/00_nw_origins.txt`)

| Key | Status | Vanilla / replacement |
|---|---|---|
| `is_origin`, `icon`, `picture`, `playable`, `possible`, `initializers`, `flags`, `modifier`, `random_weight`, `advanced_start` | ✅ | `00_origins.txt:57-93`, `:229-238` (initializers) |
| `host_has_dlc = "Nomads"` / `"BioGenesis"` | ✅ | Inside `has_nomads_dlc` (`09_scripted_triggers_nomads.txt:88-91`) and `has_biogenesis_dlc` (`common/scripted_triggers/00_scripted_triggers.txt:482-485`) |
| `auth_hive_mind` | ✅ | `common/governments/authorities/00_authorities.txt:1201` |
| `civic_hive_devouring_swarm` | ✅ | `common/governments/civics/02_gestalt_civics.txt:227` |
| `civic_hive_terravore` | ❌ 🔧 removed | Terravore is the lithoid form of Devouring Swarm (`02_gestalt_civics.txt:289,355`) |
| Nomadic origin fields | ❌ 🔧 | None exist. Replaced with `is_nomadic = yes` in `possible` (H1) |
| `uses_biomass`, `virtual_jobs` flags; `trait_wilderness` | 🔧 added | `00_origins.txt:645-649,659-661` |
| Biological `graphical_culture`; the Wilderness civic NOR list; not volcanic | 🔧 added | `00_origins.txt:610-639` |
| `country_unity_produces_mult` | ✅ | 273 vanilla uses |
| `planet_jobs_biomass_produces_mult` | ⚠ commented out | No vanilla use; no-op even if the engine accepts it (D4) |
| `has_secondary_species = no` | ❌ 🔧 removed | Vanilla only uses the block form (`00_origins.txt:218-220`) |
| `GFX_origin_nw_uprooted`, the icon `.dds` | mod asset | Not defined yet (Phase 3) |

### 3.2 Initializer (`common/solar_system_initializers/nw_initializers.txt`)

| Key | Status | Vanilla / replacement |
|---|---|---|
| `usage = empire_init` | ❌ 🔧 `usage = origin` | `example.txt:23-30` |
| `flags` (`empire_home_system` added) | 🔧 | `toxoid_initializers.txt:24`; read by `01_planetary_deposits.txt:1158-1171` |
| `name`, `class = "sc_g"`, `max_instances`, `planet`, `moon`, `change_orbit`, `orbit_distance/angle`, `size`, `has_ring`, `init_effect` | ✅ | `example.txt:12-127`; `common/star_classes/00_star_classes.txt:73` |
| `pc_shattered` (size 18), `pc_barren`, `pc_asteroid`, `pc_gas_giant`, `pc_frozen` | ✅ | `common/planet_classes/00_planet_classes.txt:1726` (size 12-30), `:587`, `:498`, `:468`, `:664` |
| Starting-Arkship spawn | ❌ 🔧 removed | No initializer spawns one (H22) |

### 3.3 Triggers and effects (`nw_scripted_triggers.txt`, `nw_scripted_effects.txt`)

| Key | Status | Vanilla / replacement |
|---|---|---|
| `pc_arkship` | ❌ 🔧 | `is_planet_class = pc_ark` |
| `has_planet_flag`/`set_planet_flag` on Ark colonies | ✅ since 4.5 | 4.5 Modding note; vanilla also uses carrier flags (`set_carrier_flag`, `events/nomads_arkship_events.txt:8376`) |
| `every_owned_planet` for Arks | ❌ 🔧 | `every_owned_colony` (`events/nomads_arkship_events.txt:5005-5008`) |
| Hull trigger | ❌ 🔧 | `ship? = { has_hp_percentage < X }` (H29, H20) |
| `nw_tag_ark` documented as "country scope" | 🔧 comment | Sets planet flags, so it runs in colony scope |
| Megastructure swap for the start Ark | ❌ | `set_starbase_size` within a class only (H24); D2/D3 |
| `has_deposit`, `add_deposit`, `remove_deposit`, `add_modifier`, `remove_modifier`, `set_country_flag`, `country_event` | ✅ | Standard |

### 3.4 Deposits (`nw_deposits.txt`)

| Key | Status | Vanilla / replacement |
|---|---|---|
| `is_for_colonizable`, `category = deposit_cat_rare`, `potential`, `planet_modifier`, `drop_weight` | ✅ | `07_federations_deposits.txt:612-644`; `common/deposit_categories/00_deposit_categories.txt:14` |
| `pop_growth_speed` | ❌ 🔧 | `logistic_growth_mult` (`07_federations_deposits.txt:628`) |
| `planet_housing_add = 10` | ⚠ 🔧 500 | Housing is counted in hundreds: Tree of Life +500/+1000 (`:595,630`) |
| `planet_jobs_society_research_produces_mult` | ✅ | `:629` |
| `planet_jobs_biomass_produces_add` | ⚠ commented out | D4 |
| `resources { category = planet_deposits produces = { biomass } }` on a non-colonizable deposit | ⚠ | Station deposits use `orbital_mining_deposits` + `station` (`common/deposits/01_orbital_deposits.txt:64-72`); biomass is not harvestable (D4) |

### 3.5 Static modifiers (`nw_static_modifiers.txt`)

| Key | Status | Vanilla / replacement |
|---|---|---|
| `icon` (path), `icon_frame` | ✅ | `11_static_modifiers_federations.txt:331-333` |
| `planet_building_build_speed_mult`, `planet_jobs_produces_mult`, `planet_jobs_upkeep_mult`, `planet_stability_add` | ✅ | Values equal `no_lifetree` (`:334-337`) |
| `planet_jobs_research_produces_mult` | ❌ 🔧 | `planet_researchers_produces_mult` (`common/colony_types/03_wilderness_colony_types.txt:250`) |
| `ship_hull_mult` | ✅ | 146 vanilla uses |
| `planet_jobs_biomass_produces_mult` (×5) | ⚠ commented out | D4 |

### 3.6 Situation (`nw_situations.txt`), see §3.10 for the syntax fixes

| Key | Status | Vanilla / replacement |
|---|---|---|
| `is_using_situation_approach` | ❌ 🔧 | `current_situation_approach` (`14_nomads_situations.txt:1254`) |
| `on_deselect` | ❌ 🔧 | Approach `target_modifier` (README `:186`) |
| Approach-level `monthly_progress` | ❌ 🔧 | Situation-level `monthly_progress` modifiers |
| `on_monthly = { effect = … }` | ❌ 🔧 removed | `on_monthly` takes `events`/`random_events` (README `:200-207`) |
| `has_resource = { type = biomass … }` in country scope | ❌ 🔧 | `resource_stockpile_compare` (`03_arkships.txt:648-652`) |
| `GFX_situation_approach_default/aggressive/passive` | ❌ 🔧 | `GFX_situation_approach_heart/hungry/shrug` (`interface/situation_log.gfx`) |
| `GFX_situation_stage_1/2`, `category`, `start_value`, `stages`, `on_start`, `on_progress_complete`, `on_abort`, `set_situation_flag` | ✅ | README |
| `destroy_situation = this` | 🔧 added | README `:55-57` |
| `nw_docked_at_roothold` planet flag | ❌ 🔧 | `any_fleet_in_system = { is_waystation_ship = yes owner = … }` (`30_nomad_waystation.txt:155-157`) |
| `solar_system` from the Ark colony | 🔧 `solar_system?` | `15_nomads_dlc_decisions.txt:20,37` |

### 3.7 Decisions (`nw_decisions.txt`)

| Key | Status | Vanilla / replacement |
|---|---|---|
| `owned_planets_only`, `icon`, `enactment_time`, `resources { category = decisions cost }`, `potential`, `allow`, `effect`, `ai_weight` | ✅ | `common/decisions/example.txt:2-60` |
| Transplant `allow` via `any_system_planet` | ❌ 🔧 | Ark colonies are not system planets. Now searches our Ark fleets in the system (`any_fleet_in_system` → `any_owned_ship` → `colony?`) |
| Biomass cost without `on_queued` | ⚠ 🔧 | `on_queued`/`on_unqueued` copied from `14_wilderness_decisions.txt:34-64` |
| `count_owned_planet` for Arks | ❌ 🔧 | `count_owned_colony` (`common/pop_faction_types/00_xenoist.txt:743-748`) |
| Take Root target `pc_barren`, unowned | ⚠ | Vanilla settle needs `is_colonizable = yes` (`03_arkships.txt:1279-1282`); D7 |

### 3.8 Technology (`nw_technology.txt`)

| Key | Status | Vanilla / replacement |
|---|---|---|
| `@tier1cost1` … `@tier3weight1` | ✅ | `common/scripted_variables/00_scripted_variables.txt:229-264` |
| `area = society`, `category = { biology }`, `tier`, `prerequisites`, `weight`, `potential`, `weight_modifier { factor }`, `ai_weight { factor }` | ✅ | `common/technology/000_documentation.txt:7-62`; `00_nomads_dlc_tech.txt:28-30` |
| "vanilla tech that unlocks building additional Arkships" | 🔧 | `tech_arkship_construction` (`00_nomads_dlc_tech.txt:3-38`). It is tier 3 and Seed Casting is tier 1 (D9) |
| "Improved Wayline Logistics" / "tier-3 Arkship tech" | named | `tech_waystation_2` (`:520`), `tech_arkship_tier_3` (`:81`) |

### 3.9 Events and on_actions

| Key | Status | Vanilla / replacement |
|---|---|---|
| `on_game_start_country`, `on_monthly_pulse_country`, `on_mid_game_pulse_country` | ✅ | `00_on_actions.txt:31,328,660` |
| Waystation-built hook | named | `on_waystation_built` (`:5916-5928`, system scope; needs a `system_event` relay like `timeline.95`, `events/timeline_events.txt:3538-3560`) |
| Fleet-enters-system hook | named | `on_entering_system_fleet` (`:973-976`, fleet scope) |
| `country_event`, `id`, `title`, `desc`, `picture`, `show_sound`, `is_triggered_only`, `hide_window`, `trigger`, `immediate`, `option`, `ai_chance`, `custom_tooltip`, `hidden_effect`, `years_passed` | ✅ | Standard |
| `event_default`, `event_alien_nature` | ✅ | `sound/` (355 and 138 script uses) |
| `GFX_evt_nw_*` (7 pictures) | mod asset | Not defined yet |
| `pc_wilderness_homeworld` | ❌ commented out | No Wilderness class exists (D7) |
| Tree of Life deposit for Take Root | ⚠ | `d_tree_of_life_*` name an `origin_tree_of_life` owner in `potential` (H31, D7; `potential` only gates random placement, §10) |
| Settle effect | named | `arkship_settle_planet_effect` (H27) |
| `[Root.GetName]` in `.20`, `.21`, `.50` | ⚠ | Root is the country there, not the Ark (plan-event-timing) |
| `add_resource = { biomass = … }` in `.10`, `.40` | ⚠ | Reset by `wilderness.10` within a month (D4) |

### 3.10 Situation syntax fixes applied

`common/situations/99_README_SITUATIONS.txt` is the schema. Changes to `situation_nw_nurture_ark`:
- Approach `monthly_progress` blocks moved to situation-level modifiers keyed on `current_situation_approach`.
- `on_select`/`on_deselect` modifier pairs replaced by approach `target_modifier`.
- The invalid `on_monthly` effect replaced by a placeholder.
- `destroy_situation = this` added.
- Approach icons replaced with sprites that exist.
- The docked test and the stockpile test rewritten as above.

Still missing: the localisation keys `situation_nw_nurture_ark_type` and `situation_nw_nurture_ark_monthly_change_tooltip` (README `:28-32`); the plan covers them. The static modifiers `nw_nurture_forced_bloom` and `nw_nurture_dormancy` are now unused (plan P9).

### 3.11 TEMPLATE files: correct vanilla sources and folders

| Mod file | Correct vanilla source | Correct target folder |
|---|---|---|
| `common/megastructures/nw_tree_arkships.txt` | Seed: `29_nomad_arkships.txt` + `inline_scripts/megastructures/arkship.txt`. Ark tiers: `29_nomads_dlc_ships.txt`, `06_arkship_levels.txt`, `section_templates/nomads_dlc.txt` | Seed: `megastructures/`. Arks: `ship_sizes/`, `starbase_levels/`, `section_templates/` (or none, D2) |
| `common/ship_sizes/nw_tree_arkship_sizes.txt` | `29_nomads_dlc_ships.txt:104-1443`; `00_ship_sizes.txt:1410` | `ship_sizes/` |
| `common/component_templates/nw_spinal_mounts.txt` | `starbase_modules/00_arkship_spinal_modules.txt` | **`starbase_modules/`** |
| `common/districts/nw_districts.txt` | `07_ark_districts.txt`; zones in `05_arkship_zones.txt` | `districts/` + **`zones/`** for the `zone_nw_*` keys |
| `common/scripted_triggers/zz_nw_overrides.txt` | `00_scripted_triggers.txt:1766-1769` (plus `09_scripted_triggers_nomads.txt` lists if D2 adds sizes) | `scripted_triggers/` |

## 4. Factual claims in README and docs/01-05

✅ confirmed, ◐ partly, ❌ refuted, ? unverified.

### README.md

| Claim | Verdict | Evidence |
|---|---|---|
| Target patch 4.4 "Pegasus", Nomads released 2026-06-15 | ✅ then superseded | 4.4 notes header (VN §4). The install is 4.5.2 "Cygnus" |
| "4.4.4 reworked Arkship colony modifiers" | ◐ | 4.4.4 bugfix: "Planet class modifiers now apply to Arkship (ship-carried) colonies" plus a modder note on scripted modifiers. A fix, not a rework |
| Required DLC: Nomads, BioGenesis | ✅ | `has_nomads_dlc` gates Nomad origins (`00_origins.txt:68-71`); `origin_wilderness` `playable = { has_biogenesis_dlc = yes }` (`:599-601`) |
| Required DLC: Plantoids "for Tree of Life assets and the sapling events" | ❌ / ? | No script gate on `origin_tree_of_life` (`00_origins.txt:2758-2793`). No vanilla "sapling events" exist; transplant is a decision. The DLC recommender ties ToL to Plantoids (`dlc_metadata/dlc_recommendations/00_dlc_recommendations.txt:13,36-46`); whether the engine requires it is unverified |
| "Nomads and Arkships are a 4.4 feature" | ✅ | 4.4 notes |
| Vanilla terms: Arkship, Waystation, Wayline, Logistic Ship, Operational Reserves, Contract, Planetary Strip Mining, Planetary Reprocessing, Settle | ✅ | `localisation/english/nomads_2_l_english.yml:343-396`; `nomads_1_l_english.yml` (`arkship_strip_mine`, `arkship_reprocess_planet`, `arkship_settle`) |

### docs/01-concept.md

| Claim | Verdict | Evidence |
|---|---|---|
| Wilderness spends "biomass instead of minerals" | ◐ | Districts cost minerals **and** 50 biomass (`05_wilderness_districts.txt:48-55`) |
| Tree of Life: hive mind; devastation kills the tree; colony ships carry a sapling | ✅ | `00_origins.txt:2765,2781-2784`; `events/origin_events_3.txt:63` |
| Tree of Life is a Plantoids origin | ? | No script gate; the recommender only |
| An Arkship is "a mobile megastructure that is a ship and a colony" | ❌ | A starbase-class ship carrying a colony (H4) |
| Nomads never claim systems and leave Waystations | ✅ | `nomads_2_l_english.yml:343` |
| Destroyed at 50% devastation; Lost Tree modifier; Transplant decision (food + time) | ✅ | H11, H12 |
| "Devouring Swarm and Terravore are excluded" (ToL) | ◐ | ToL excludes Devouring Swarm only; Terravore is not a separate civic |
| Vanilla Nomads spend "alloys and minerals" | ◐ | A bioship nomad already pays food for Logistic Ships, Waystations and new Arks (H15). Tier upgrades, districts and modules are alloys |
| Vanilla Nomads can settle | ✅ | H27 |
| Forever Cruise "Planetfall" | ✅ | `09_scripted_triggers_nomads.txt:928-931` |

### docs/02-mechanics.md

| § | Claim | Verdict | Evidence |
|---|---|---|---|
| 1 | Excluded civics "Devouring Swarm, Terravore (same rule as Tree of Life)" | ◐ | as above |
| 1 | "The origin carries the same nomadic fields vanilla gives Voidfarers" | ❌ | H1 |
| 1 | Start fleet: Wilderness bioships + Seedwing + Seedwing Scout | ❌ | Nomads get no military start fleet (`events/game_start.txt:1471-1476`); they get 1 science ship + 1 Logistic Ship, 2 for Voidfarers (`:1137-1168`, `:1316-1353`) |
| 1 | Start Ark chosen in the first event | ◐ | The designer already picks the class (H22); D3 |
| 1 | Standalone origin works "whether … a property of the origin … or a toggle" | ✅ | It is a toggle (H1) |
| 2 | New Arkship: megastructure with alloys via an existing Ark | ✅ | 10000 alloys + 500 influence; bioship 5000 alloys + 17500 food; 720 days (`inline_scripts/megastructures/arkship.txt:10-31`) |
| 2 | Tier upgrade: alloys + time | ✅ | 5000 alloys + 250 influence, 900 or 1080 days (`29_nomads_dlc_ships.txt:328-334,459-465`) |
| 2 | Deck districts cost minerals | ❌ | Alloys (`07_ark_districts.txt:56-64,175-183`) |
| 2 | Wilderness district cost 50 biomass (4.0.3) | ◐ | 50 biomass **plus** 300-500 minerals (`05_wilderness_districts.txt:48-55,205-212`) |
| 2 | Operational Reserves are a resource "from harvesting" | ❌ | A minerals+energy converter with a situation (H32) |
| 2 | Waystation build: Logistic Ship + alloys | ◐ | 100 influence + 250 alloys; bioship 125 alloys + 437.5 food; built by Logistic Ships or Arks (`30_nomad_waystation.txt:14-51`) |
| 2 | "Arks start with 1000 pops (vanilla Arkship wiki)" | ? | `generate_start_pops` not traced |
| 2 | Biomass income comes from pop growth | ✅ | H21 |
| 2 | "at negative biomass stockpile…" | ❌ | The stockpile equals the pop count and cannot go negative (H21) |
| 3.1 | Civilian +2500 reserves, +5% harvest per tier | ✅ | `10_scripted_variables_nomads.txt:548-550`; `29_nomads_dlc_ships.txt:179,302,433` |
| 3.1 | Scientific +1 sensor and hyperlane range, +5% survey per tier | ◐ | Sensor 2/3/4, hyperlane 1/2/3, survey 5/10/15%; plus deep scan +5/10/15 (`08_script_values_nomads.txt:607-629`) |
| 3.1 | Military +5% fire rate and range per tier | ✅ | `29_nomads_dlc_ships.txt:945-946,1069-1070,1201-1202` |
| 3.1 | Military "extra auto-filled weapon slots" | ✅ (corrected 2026-10-15) | Military Ark sections add medium/large turret slots that civilian and science sections lack (`common/section_templates/nomads_dlc.txt:729-1116` vs `:195-728`). Ark designs are auto-generated (`is_designable = no`, `enable_default_design = yes`, `29_nomads_dlc_ships.txt:951,959`) |
| 3.1 | Military "cannot be bombarded" | ? | Not found |
| 3.1 | Spinal mounts: Planetary Reprocessor, sensor array, Devastator | ◐ | Names exist; they are starbase modules any class can fit (H7). The sensor mount is "Subspace Imaging Array" |
| 3.2 | Ark colony size range 9 → 18 (9/13/18) | ❌ | `pc_ark` is fixed at size 9; tiers add `planet_max_districts_add` 0/9/18 |
| 3.3 | Logistic Ships cannot build Arks | ✅ | `nomads_2_l_english.yml:390`; `arkship.txt:37-39` |
| 3.3 | Seed is the vanilla "build Arkship" megastructure, 720 days | ✅ | `arkship.txt:10` |
| 3.3 | Nurture drains biomass; pauses at negative biomass | ⚠ | D4 |
| 4 | Ark districts: city, generator, farming/special housing, Arkship Deck with four specialisations, Ordnance Engineering | ◐ | No farming district; Ark Hydroponics is a zone (VN §1.3) |
| 4 | Job names Photosymbiont, Grubsymbiont, Warrior Drone | ✅ | Gestalt swaps (H14) |
| 4 | Nectar Gatherer, Mycelial Comptroller | ❌ | Not vanilla (VN §2.3) |
| 4 | Synapse Drone | ◐ | A hive job with no Wilderness name |
| 4 | Heartgrove / Solar Mycolungs / Orespine | ◐ | Vanilla **zone** names, not jobs or districts (`wilderness_l_english.yml:285,300,303`) |
| 4 | Jobs auto-worked by the primary species, no biomass needed (4.0.22) | ✅ | 4.0.22 note |
| 5 | Tree of Life dies at 50% devastation | ✅ | `events/origin_events_3.txt:63` |
| 5 | Withered Heartwood mirrors Lost Tree of Life | ✅ | `11_static_modifiers_federations.txt:331-338` |
| 5 | Transplant 500 food, 180 days | ✅ | `06_federation_decisions.txt:5-18` |
| 5 | Heartwood Core "+5 biomass per month" | ❌ | D4 |
| 6 | One Waystation per surveyed system; adjacent ones form Waylines; Logistic Ships build and collect | ✅ | `30_nomad_waystation.txt:92-159`; `nomads_2_l_english.yml:378,390` |
| 6 | Levels gated by Improved and Advanced Wayline Logistics | ✅ | `00_nomads_dlc_tech.txt:520,603` |
| 6 | Modules unlock "megastructure construction" | ? | Not found as a module function |
| 6 | Same opinion malus as vanilla Waystations | ? | Not traced |
| 7 | Strip Mining 250%, 50-year depletion | ✅ | Tooltip `nomads_1_l_english.yml:3003`; modifier 600 months (`10_scripted_variables_nomads.txt:373`) |
| 7 | Strip Mining gate "Tier 2+ Civilian or Planetary Engineering Bay" | ◐ | Civilian T2+ **or** `arkship_mining_array` "Planetary Engineering Lab" **or** the Planetary Reprocessor (`03_arkships.txt:494-538`) |
| 7 | Strip Mining "500 Operational Reserves, 180 days" | ◐ | 500 **energy**; 180 progress divided by action speed (`03_arkships.txt:621,632-634`; `08_script_values_nomads.txt:666-669`) |
| 7 | Reprocessing 600%, 200-year depletion, needs the Reprocessor, leaves a Shattered World | ◐ | Tooltip 600% and "converted into a Shattered World" (`nomads_1_l_english.yml:3036-3037`). The script's 1200-month yield is 10× a harvest. Modifier 2400 months = 200 years. Cost 5000 energy, 720 progress |
| 7 | Targets: not a star, not habitable | ✅ | `09_scripted_triggers_nomads.txt:538-564` |
| 8 | Contracts issued by settled empires after contact | ✅ | `09_scripted_triggers_nomads.txt:405-416` |
| 8 | "one per Arkship" | ? | Not found |
| 8 | "five-year expiry" | ◐ | `time_to_accept = @contract_time_base` = 1800 days on many contracts (`02_contracts.txt:1955`) |
| 8 | Progress toward empire focuses | ? | Not traced |
| 8 | Inward Perfection cannot accept | ✅ | `09_scripted_triggers_nomads.txt:445-446` |
| 8 | Seed a Grove hands out "the vanilla sapling" | ❌ | No sapling deposit; the ToL deposits' `potential` names a ToL owner (H31; `potential` only gates random placement, §10) |
| 10 | Vanilla can settle "personally or as a vassal" | ◐ | Subject options need `is_gestalt = no` (`events/nomads_events_1.txt:13674-13683,13738-13744`); a hive gets settle or scuttle |
| 10 | "Paradox developers warned …" | ? / ◐ | No such statement in the patch notes. Script comment "Nomads should *never* have planets" (`nomads_events_1.txt:34`) |
| 10 | The vanilla settle flow handles the empire-type conversion | ✅ | `nomads_effects.txt:5056` |
| 11 | Elder Growth mirrors the tier-3 tech; Heartlance uses the military spinal tech | ✅ | `tech_arkship_tier_3`; Devastator needs `tech_arkship_tier_3` + `tech_military_arkship` |

### docs/03-arkship-models.md (first-pass verdicts; the decision pass rewrote the brief and applied them)

| Claim | Verdict | Evidence |
|---|---|---|
| Entity picked by `<gfx_culture>_<ship_size>_entity`; Arks "are megastructures and may use a direct entity" | ◐ | Ark sizes set `entity = <class>_arkship_01_stage_N_entity` (a frame mesh). The visible hull is the section entity `<class>_arkship_01_stage_N_ship_entity`, culture-prefixed per `HOW_TO_MAKE_NEW_SHIPS.txt:61-69` |
| Required states idle/moving/death | ✅ | `gfx/models/ships/other/_other_entities.asset:4717-4723` |
| Locators | ◐ | Vanilla slots use `part1`, `module_01..06`, `spinal_mount` (`29_nomads_dlc_ships.txt` `section_slots`), and Ark ship entities attach district models at `district_01..09` (`_other_entities.asset:4734-4742`), which the brief omits |
| Dev Diary: spinal mount "between the habitable arms" | ? | Not checkable from the install |
| Construction state for the Seed | ✅ | Megastructures take `construction_entity` (`inline_scripts/megastructures/arkship.txt:3`) |

### docs/04-vanilla-hooks.md §7

| Claim | Verdict | Evidence |
|---|---|---|
| A developer said mixing planets and Arks "might break" | ? | Not in any patch notes read; the script comment above is the in-game equivalent |
| 4.4.4: scripted modifiers must target colonies | ✅ | 4.4.4 note |
| Captured Wilderness worlds get blockers; captured Arks need a test | ✅ risk confirmed | `wilderness.300` adds `d_wilderness_city` and `d_wilderness_industrial` even with zero districts (`value < 4` branches, `events/origin_events_wilderness.txt:164-262`) once `is_wilderness_empire` matches the Uprooted |

### docs/05-roadmap.md

| Claim | Verdict |
|---|---|
| `rg -n "<<VANILLA" .` must return nothing | ❌ as written: the docs quote the marker. Scoped to `common events localisation gfx` |

## 5. Corrections for CLAUDE.md and `.claude/agents/*.md` (not applied; for the lead)

**CLAUDE.md**
1. "descriptor.mod targets `supported_version="4.4.*"`" → now `"4.5.*"`. The install is v4.5.2.
2. Build & Test step 1: `rg -n "<<VANILLA" .` → `rg -n "<<VANILLA" common events localisation gfx`. The docs quote the marker.
3. Identity gate: "switching to Plan B only touches `nw_scripted_triggers.txt`" is wrong. Plan B also needs:
   - a `zz_` override of `origin_wilderness` to drop `is_nomadic = no` (`00_origins.txt:640`)
   - handling of `game_start/origin_wilderness.txt` on a `pc_ark` capital (`common/inline_scripts/game_start/origin_wilderness.txt:10-40`)

   See D1.
4. Tree Arks: "megastructures `arkship_nw_seed` and `arkship_nw_{…}_{1,2,3}`, each a copy of the matching vanilla Arkship block" is wrong. Only the Seed is a megastructure; Ark tiers are ship sizes plus starbase levels (H4), or a re-skin (D2).
5. Arkship colony scope: "`nw_static_modifiers.txt` is written that way" overstates it. The 4.4.4 note is about scripted modifiers. Static modifiers use `planet_*` keys and are added in colony scope.
6. Take Root: "only the last Ark can settle, because vanilla code assumes planets and Arks never coexist" — the vanilla settle event already handles extra Arks: scuttle, or a subject (non-gestalt only). The "developer warning" is unsourced; cite `events/nomads_events_1.txt:34` instead.
7. Layout:
   - spinal mounts belong in `common/starbase_modules/`, not `component_templates/`
   - Ark zones belong in `common/zones/`
   - contracts belong in `common/missions/missions/`
8. Toolchain: `wiki_patch_notes` reads only minor-version pages and drops sub-bullets. Point releases (4.4.2-4.4.6, 4.5.1-4.5.2) need `wiki_page` on "Patch 4.x.X".
9. Key Systems could add the three facts that drive everything:
   - nomadic = designer toggle (H19)
   - Ark = ship + `pc_ark` colony (H4)
   - biomass = pop count (H21)

**`.claude/agents/numbers-nerd.md`**
1. Source list: `common/megastructures/29_nomad_arkships.txt` holds only the new-Ark construction sites. The numbers live in:
   - `29_nomads_dlc_ships.txt`
   - `10_scripted_variables_nomads.txt`
   - `06_arkship_levels.txt`
   - `03_arkships.txt` (harvest)
   - `08_script_values_nomads.txt`
2. Tree of Life numbers: `07_federations_deposits.txt`, `11_static_modifiers_federations.txt`, `06_federation_decisions.txt`.
3. Add the rule: biomass is the Wilderness pop count (`wilderness.10`). Any "biomass income/cost" number is pop growth/death and must be balanced as such.

**`.claude/agents/patch-watcher.md`**
1. File mapping:
   - harvest actions (H8) map to scripted actions, not `nw_situations.txt`
   - spinal mounts (H7) map to `starbase_modules`
   - contracts (H17) map to `common/missions/missions/`, which has no mod file yet
2. Process step 2: `wiki_patch_list` has no point releases. Add `wiki_page` "Patch 4.x.X" for point releases, and note that `wiki_patch_notes` drops sub-bullets.
3. Keywords: add `carrier`, `colony`, `encamp`, `pc_ark`, `is_nomadic`, `set_country_code_flags`.
4. `maxTurns: 15` with `model: haiku` is too small for a multi-version pass. The 4.3-4.5.2 harvest in this audit took 45 tool calls.

**`.claude/agents/save-analyst.md`**
1. Phase 1 "exactly one Ark megastructure (`arkship_nw_*`)" → the Ark is a fleet whose ship has an Arkship size, plus a colony of class `pc_ark`. Look in the fleet/ship and planet/colony sections, not `megastructures`.
2. "no owned planets" → no owned planets other than the `pc_ark` capital colony.
3. "a bioship fleet" is not a vanilla nomad start (`events/game_start.txt:1471-1476`); expect it only if the mod spawns one (D12).
4. Voidfarers baselines: filter on `origin_default_nomads`.

## 6. Decisions

All fourteen were decided on 2026-10-15; the user accepted every recommendation. One sub-choice in D7 stays open. Each entry gives the decision, then the evidence it rests on.

**D1. Identity — Decided 2026-10-15: A (standalone origin).**
- `origin_nw_uprooted` with `is_nomadic = yes`, `uses_biomass`/`virtual_jobs` and `trait_wilderness`.
- `is_wilderness_empire` is overridden with one added OR branch. So are the two raw checks that matter and follow the same pattern: `has_purity_ascension` and `has_one_genetic_ascension_choice`. All three are in `common/scripted_triggers/zz_nw_overrides.txt`.
- **Evidence:**
  - `is_wilderness_empire` is a one-line `has_origin = origin_wilderness` behind 1334 references (`00_scripted_triggers.txt:1766-1769`).
  - `origin_wilderness` requires `is_nomadic = no` (`00_origins.txt:640`), and its start script assumes a planet capital (`game_start/origin_wilderness.txt:10-40`).
  - `has_purity_ascension` (`05_scripted_triggers_biogenesis.txt:138-143`) feeds the ascension council agendas (`common/council_agendas/02_council_agendas_ascensions.txt:26` etc.). `has_one_genetic_ascension_choice` (`:189-195`) feeds `events/biogenesis_events.txt:156` etc.
  - `has_reduced_pop_origin` (`00_scripted_triggers.txt:4671-4679`) has no users, so it is not overridden.
  - The other raw checks are start-only, AI weights or post-settle content. The ten Wilderness terraform links matter after Take Root (see D7).

**D2. Tree Arks — Decided 2026-10-15: B (vanilla sizes and classes, mod graphical culture). The user will build fully new tree models.**
- The art brief is `docs/03-arkship-models.md`.
- **New evidence from this pass:**
  - **The culture prefix applies to Ark parts.** Ark module and spinal sections name bare entities (`construction_yard_01_entity`, `common/section_templates/nomads_dlc.txt:1118-1259`). The game resolves them to culture-prefixed ones (`civilian_arkship_construction_yard_01_entity`, `gfx/models/ships/other/_other_entities.asset:9833`).
  - **Explicit size entities are culture-prefixed too.** The Waystation size's `entity = "starbase_starport_entity"` (`common/ship_sizes/00_waystations.txt:5`) has per-culture versions (`gfx/models/ships/starbases/_starbase_entities.asset`), so the Ark frame entity is probably prefixed as well.
  - **Risk: each Ark size restricts its cultures,** e.g. `graphical_culture = { "civilian_arkship" }` (`29_nomads_dlc_ships.txt:108`). Whether `set_graphical_culture` with a mod culture survives that is the first test-load question. The fallback overrides the nine size blocks and adds the mod cultures to that list; keys stay vanilla.
  - **No vanilla hull-percentage visual state exists.** The entity states are idle/moving/working/working_looping/death. The wither visual is therefore planned as a culture swap.

**D3. Starting class — Decided 2026-10-15: B.**
- The designer picks the class. `.2` is flavour and applies the class trait from `starting_<class>_arkship` (`01_start_of_game_effects.txt:5206-5217`).
- Encoded in `events/nw_uprooted_events.txt` and `nw_apply_starting_ark_class`.

**D4. Biomass economy — Decided 2026-10-15: B for recurring costs and drains (food); A for one-off rewards (pop creation); one-off biomass `cost` blocks keep the vanilla `on_queued` kill.**
- Percentage "biomass income" modifiers become pop growth (`logistic_growth_mult`), as option A proposed.
- **Encoded:**
  - Nurture upkeep in food
  - `.10`/`.40` rewards as `create_pop_group`
  - origin, Withered Heartwood, Fruiting, Memory of the Grove and the Heartwood trait as `logistic_growth_mult`
  - every converted number marked "design value, balance pending"
- **Left planned:** the Husk and the Fruiting harvest credit, which need events (P15).
- **Dropped (follow-up, 2026-10-15):** the Heartwood's flat "+5 a month", which had no verified flat-growth planet modifier (§10).

**D5. Tier growth — Decided 2026-10-15: A.**
- The vanilla Ark upgrade is blocked for the Uprooted by `upgrade_possible` overrides of the six tier-2/3 levels (`common/starbase_levels/06_arkship_levels.txt`).
- Nurture calls `set_starbase_size` (`events/game_start.txt:3487-3503`), and the AI is taught Nurture.
- The scope of `upgrade_possible` is a first test-load check.

**D6. New Arks — Decided 2026-10-15: A.**
- An own `arkship_nw_seed` site with its own cost.
- The vanilla three sites are hidden for the Uprooted with a potential override of the shared inline (`common/inline_scripts/megastructures/arkship.txt:33-40`).
- The cost follows D4: 2000 biomass (one-off, so 2000 pops killed) + 1500 food. **2000 biomass = 2000 pops is flagged for the balance pass, not re-tuned.**

**D7. Take Root — Decided 2026-10-15: A (reuse vanilla settle; the Heartwood carries over with `copy_deposit_from`). Sub-choice decided 2026-10-15: W (`set_origin = origin_wilderness` at settle).**
- **Decided part:**
  - The player settles with `arkship_settle` on a colonizable world (`03_arkships.txt:1243-1329`). The Adaptability or Versatility tradition is required (`09_scripted_triggers_nomads.txt:908-937`).
  - Vanilla `nomads.4705` converts the empire and, for a hive, settles the single Ark or scuttles the rest (`events/nomads_events_1.txt:13624-13670`).
  - The mod copies `d_nw_heartwood_core` onto the planet (`copy_deposit_from`, `common/scripted_effects/00_scripted_effects.txt:9390-9399`; vanilla's use for the Knights, `nomads_effects.txt:5156-5175`).
  - `decision_nw_take_root` is retired (P19).

- **Sub-choice: W.** At settle the empire takes `origin_wilderness`. The audit had proposed W as the variant of D worth testing; the user chose it.
  - **What W gives.**
    - The 41 raw `has_origin = origin_wilderness` checks pass, including the ten Wilderness terraform links (`common/terraform/02_special_terraform_links.txt:5576-5856`). A settled Uprooted therefore colonises the Wilderness way with no new links.
    - `is_wilderness_empire` passes on the raw origin.
  - **What W needs (P19).**
    - **(a) Mod content stays on.** After `set_origin`, `has_origin = origin_nw_uprooted` is false. `nw_is_uprooted_empire` also accepts `has_country_flag = nw_uprooted` (`common/scripted_triggers/nw_scripted_triggers.txt`), so settling sets that flag explicitly, after `set_origin`.
    - **(b) The `started_*` flag.** `wilderness.100` set it at game start from the `pc_ark` capital (`events/origin_events_wilderness.txt:78-93`). Remove the three flags and fire `wilderness.100` again (`:62-97`), which sets the right one from the new capital.
    - **Planet setup:** the Wilderness capital building (`common/inline_scripts/game_start/origin_wilderness.txt:33-36`).
  - **Origin flags across `set_origin`: checked in the install; not determinable.**
    - **Origin `flags` are country flags.** The define calls `uses_biomass` a "Country flag" that turns on the monthly biomass on_action (`common/defines/00_defines.txt:1218`; `on_biomass_monthly` → `wilderness.10`, `common/on_actions/00_on_actions.txt:5523-5528`). Script reads civic flags with `has_country_flag`: `suppress_vanilla_factions` is declared at `common/governments/civics/00_civics.txt:3976` and read at `common/scripted_triggers/00_scripted_triggers.txt:4063`.
    - **No script sets or removes `uses_biomass` or `virtual_jobs`.** The origins' `flags` blocks are their only source (`00_origins.txt:645-649`, and the mod's origin).
    - **No `set_origin` call handles flags.** None of the 67 vanilla calls touches the old or new origin's declared flags: the flag edits within 25 lines of each call are unrelated flags. `set_origin_effect` handles none either (`common/scripted_effects/00_scripted_effects.txt:7341`).
    - **So nothing in the install says** whether `set_origin` keeps the old origin's flags, swaps them for the new origin's, or drops them.
    - **For W:**
      - `uses_biomass` and `virtual_jobs` are on both origins, so they are lost only if the old flags are dropped and the new ones not added.
      - `nw_uprooted` and `custom_start_screen` are only on `origin_nw_uprooted`, which is why (a) sets `nw_uprooted` itself.
      - First test load, item 9, checks this on a live empire.
  - **Also expected, untested:** the Uprooted origin's modifier (+5% unity, +10% pop growth) ends, and Wilderness's +5 building slots (`00_origins.txt:651-653`) begin.

- **Options compared before the choice.** Kept as the evidence.

  **T: `set_origin = origin_tree_of_life`.**
  - **The empire stops being Wilderness.** After D1, `is_wilderness_empire` matches only `origin_wilderness` or `origin_nw_uprooted` (`zz_nw_overrides.txt`), so these switch off:
    - biomass sync (`wilderness.10` needs `is_wilderness_empire`, `events/origin_events_wilderness.txt:45-47`)
    - biomass visibility (`common/strategic_resources/00_strategic_resources.txt:485-491`)
    - the Mutualistic Intelligence authority (`common/governments/authorities/00_authorities.txt:1391-1394`)
    - Wilderness job names and districts (`common/districts/05_wilderness_districts.txt:20-23`)
    - terraform colonisation (`should_colonize_use_terraform`, `common/game_rules/00_rules.txt:4461-4465`)
  - **What it gains:** vanilla Tree of Life works as designed. New colonies get a tree from `origin.3001` (`00_on_actions.txt:1309`), and colony ships carry the sapling cost (`00_origins.txt:2781-2784`). Vanilla already does this for nomad vassals (`events/nomads_events_1.txt:149-155`).
  - **Left over:** the species keeps `trait_wilderness` (`common/traits/15_biogenesis_species_traits.txt:1745-1783`).

  **D: the mod's own deposit (keep `origin_nw_uprooted`).**
  - The empire stays Wilderness through the D1 override, and the Heartwood is its tree.
  - **Its gaps:** colonisation (the ten terraform links require the raw origin plus a `started_*` flag, and the game rule forces terraform colonisation), the `started_*` reset, and planet setup.

  The audit recommended D, with W as the variant to test. W keeps D's Wilderness identity and closes its colonisation gap without new links; the `started_*` reset and planet setup carry over to W.

**D8. Spinal mounts — Decided 2026-10-15: re-skin the three vanilla modules for the Uprooted, no class gates.**
- Taproot = `arkship_planetary_refinery`, Heliotrope Crown = `arkship_system_scanner`, Heartlance = `arkship_spinal_devastator_mount`, each with vanilla effects and techs.
- **Models:** they come through the culture prefix (`_other_entities.asset:9949`).
- **Names:** they need scripted or triggered loc.
- **Dropped:** the design's Heliotrope aura (a gameplay change).
- **Deep Taproot's role:** it lost its unlock under D8. It now gates Husk collection (confirmed 2026-10-15, §10).

**D9. Techs — Decided 2026-10-15: keep all four, tiers 3-4, each hung off a vanilla Nomad gate.** Encoded in `common/technology/nw_technology.txt`:

| Tech | Tier | Prerequisites | Unlocks |
|---|---|---|---|
| Seed Casting | 3 | `tech_arkship_construction` | the Seed |
| Lattice Sensing | 3 | Seed Casting + `tech_waystation_2` | the Spore Nursery |
| Deep Taproot | 4 | Lattice Sensing + `tech_arkship_planetary_refinery` | Husk collection (confirmed 2026-10-15); this role is new, because D8 took away its original one |
| Elder Growth | 4 | Seed Casting + `tech_arkship_tier_3` | Bole → Elder Nurture |

**D10. Symbioses — Decided 2026-10-15.**
- **Reforest:** vanilla `contract_clear_blockers`, reflavoured through triggered loc. The contract has a static `desc` (`02_contracts.txt:1949`), but missions support triggered `desc`/`desc_operator` (`99_README_MISSIONS.txt:23-34`), so the block must be overridden to add one.
- **Seed a Grove:** gets a mod deposit.
- **Beg a Sapling:** becomes an internal contract.

**D11. Living Hull — Decided 2026-10-15: the Thornwood withers at 12.5% hull (the others at 25%).**
- Encoded in `nw_check_wither` and the `.2` tooltip. The trait text already says "withers only at far lower hull".

**D12. Starting fleet — Decided 2026-10-15: B (no starting bioship war fleet).**
- The start is the vanilla nomad one: science ship + Logistic Ship (`events/game_start.txt:1137-1168,1316-1353`).

**D13. "Sap" — Decided 2026-10-15: keep "Sap", Uprooted-only, through scripted or triggered loc.**
- **Limitation:** vanilla text names the reserves through the static key `concept_operational_reserves` (`localisation/english/nomads_2_l_english.yml:352`, 9 references). No vanilla scripted loc covers it (`common/scripted_loc/15_scripted_loc_nomads.txt`).
- "Sap" can therefore appear in mod text and in triggered descriptions the mod adds. Vanilla tooltips keep "Operational Reserves".

**D14. Capital Ark — Decided 2026-10-15: the start event builds out the capital Ark** (P4).

## 7. Edits applied in the first pass (2026-10-15)

Existing files edited (21): `descriptor.mod`, `README.md`, `docs/02-mechanics.md`, `docs/04-vanilla-hooks.md`, `docs/05-roadmap.md`, these 11 script files, and the 5 TEMPLATE headers listed below. New files: the four `docs/research/` documents plus `patch-notes-harvest-4.0-4.5.2.md` (raw patch-note quotes).

Script files:
- `common/governments/civics/00_nw_origins.txt`
- `common/solar_system_initializers/nw_initializers.txt`
- `common/scripted_triggers/nw_scripted_triggers.txt`
- `common/scripted_effects/nw_scripted_effects.txt`
- `common/on_actions/nw_on_actions.txt`
- `common/deposits/nw_deposits.txt`
- `common/static_modifiers/nw_static_modifiers.txt`
- `common/situations/nw_situations.txt`
- `common/decisions/nw_decisions.txt`
- `common/technology/nw_technology.txt`
- `events/nw_uprooted_events.txt`

The five TEMPLATE files had their headers rewritten:
- `common/megastructures/nw_tree_arkships.txt`
- `common/ship_sizes/nw_tree_arkship_sizes.txt`
- `common/component_templates/nw_spinal_mounts.txt`
- `common/districts/nw_districts.txt`
- `common/scripted_triggers/zz_nw_overrides.txt`

Localisation was not changed: no key was renamed. The BOM is intact (`EF BB BF`).

**Placeholders: 32 in 13 files → 20 in 9 files.**

Resolved from the install (12):
- nomadic fields
- the origin header note
- the initializer Ark spawn
- `nw_is_tree_ark_colony`
- two hull triggers
- the deposit potential
- the society-research key
- the research modifier key
- the biomass decision cost
- the tech prerequisite
- the TEMPLATE header note

Rewritten to point at the real location (16), new ones added for removed invalid code (4):
- origin biomass modifier
- 2 deposit biomass lines
- 3 static-modifier biomass lines
- the start-Ark swap
- the Waystation hook
- 4 event placeholders
- the overrides skeleton
- 5 megastructure skeleton lines
- 2 new situation placeholders: tier-up and biomass drain, replacing invalid `on_monthly` code

## 8. Could not verify

- Whether a same-key override in a `zz_` file replaces vanilla for scripted triggers and origins. Not documented in the install; check `error.log`.
- The relative order of `on_game_start` and `on_game_start_country`, and of vanilla vs mod events inside one on_action.
- Whether the engine auto-generates `planet_jobs_biomass_produces_*` modifiers.
- Whether a culture prefix re-skins Ark section entities (D2). Module and spinal sections clearly use it (`common/section_templates/nomads_dlc.txt:1118` → `gfx/models/ships/other/_other_entities.asset:9833`). Untested: the hull section and the core frame entity.
- Whether an Ark size's `graphical_culture = { "<class>_arkship" }` list (`common/ship_sizes/29_nomads_dlc_ships.txt:108`) rejects a mod culture set by `set_graphical_culture`. Also whether the culture survives `set_starbase_size`, and whether swapping cultures mid-game (the planned wither visual) re-renders the Ark.
- Whether `set_starbase_size` can change an Ark's class, and the scope semantics of `upgrade_possible` on Arkship levels.
- Whether settling fires `on_capital_changed`.
- The Tree of Life origin's DLC requirement.
- Starting Ark pop count ("1000 pops").
- Why the reprocess tooltip (600%) and the script (10×) disagree.

Added in the decision pass:
- Whether a `ship_*` key in a colony's static modifier reaches the carrier Ark (Living Hull's hull regeneration, plan P4). The only precedent is a situation `target_modifier` on an Ark (`common/situations/14_nomads_situations.txt:1347-1354`).
- Whether a starbase module's name is localised with a scope that reaches the owner, which Uprooted-only spinal names need (D8, plan P14).
- Whether a two-step graphical-culture fallback (withered → tree → vanilla) resolves, and whether Arks ever enter a combat state (`docs/03-arkship-models.md` §2-§3).
- How duplicate keys resolve per object type. Some types merge instead of replacing (`common/country_limits/ownership_limits/00_ownership_limits.txt:4-7`), so the plans prefer same-path file copies where the vanilla file is small.
- Whether the pops killed for a Seed or Roothold come back when construction is cancelled.
- Whether `set_origin` keeps, swaps or drops origin flags (§6 D7), and whether a deposit added by script works where its `potential` is false (H31).

## 9. Decision pass (2026-10-15)

The decisions in §6 were written into the docs, plans and scaffold.

**Scaffold edits.** Existing files only; no new script files.

| File | Change | Decision |
|---|---|---|
| `common/scripted_triggers/zz_nw_overrides.txt` | Verbatim `is_wilderness_empire`, `has_purity_ascension` and `has_one_genetic_ascension_choice`, each with one `origin_nw_uprooted` branch. TEMPLATE header removed | D1 |
| `events/nw_uprooted_events.txt` | `.2` shows only the designer-chosen class's option; `.10`/`.40` biomass rewards become `create_pop_group`; `.100` placeholders point at vanilla settle | D3, D4, D7 |
| `common/scripted_effects/nw_scripted_effects.txt` | `nw_swap_starting_ark` → `nw_apply_starting_ark_class` (capital Ark, one class trait); Thornwood wither threshold 0.125 | D3, D11 |
| `common/governments/civics/00_nw_origins.txt` | Biomass modifier → `logistic_growth_mult`; comments for D3, D12 | D4, D3, D12 |
| `common/static_modifiers/nw_static_modifiers.txt` | Four biomass lines → `logistic_growth_mult` | D4 |
| `common/situations/nw_situations.txt` | Nurture drain → approach food upkeep; starvation pause on food | D4 |
| `common/technology/nw_technology.txt` | Tiers 3-4 with vanilla gates | D9 |
| `common/deposits/nw_deposits.txt` | Placeholder text points at the decided path | D4 |
| `common/decisions/nw_decisions.txt` | Take Root marked superseded | D7 |
| `localisation/english/nw_uprooted_l_english.yml` | `.2` desc and tooltips; Nurture food costs; `nw_biomass_starved` → `nw_food_starved`; Deep Taproot description. BOM intact (`EF BB BF`) | D3, D4, D8/D9, D11 |
| TEMPLATE headers: `nw_tree_arkships.txt`, `nw_tree_arkship_sizes.txt`, `nw_spinal_mounts.txt`, `nw_districts.txt` | Rewritten to the decided design. The obsolete megastructure example skeleton was removed (5 placeholders) | D2, D4, D6, D8 |

**Placeholders: 20 in 9 files → 8 in 4 files.**
- **Resolved by a decision (12):**
  - the origin biomass modifier (D4)
  - three static-modifier biomass lines (D4)
  - the Nurture drain (D4)
  - the start-Ark swap (D3)
  - the overrides skeleton (D1)
  - five lines of the obsolete megastructure skeleton (D2)
- **Remaining (8):**
  - Heartwood flat growth (no verified key)
  - the Husk harvest event
  - the Waystation hook relay
  - the Nurture tier-up
  - four in events: the `.30` hook and three in `.100`

**Docs.** All describe the decided design:
- `docs/02`: decision log at the top of §1, decided mechanics throughout, converted numbers marked "design value, balance pending".
- `docs/01`: concept lines that the decisions changed.
- `docs/03`: rewritten as the D2-B art brief. It covers the verified assembly chain (size → sections → entities → meshes), the culture prefix, the required entity names per class and tier, locators, states, when the culture is set, and the open tooling questions.
- `docs/04`: decision notes on H2, H4, H7, H9, H13; new H35-H37 (size culture lists, culture prefix on Ark parts and mesh files, `set_graphical_culture`); risks updated.
- `docs/05`: Phase 0 decision items ticked; Phase 1 rewritten for the decided start; a "First test load" checklist; Phases 2-3 rewritten.
- `README.md`: status line, and the naming rule that Uprooted terms apply to Uprooted empires only.
- Both plans: the decided path is the plan, alternatives marked dropped; `.51` dropped; P9 and P16 use same-path file copies.
- `gfx/models/arkships/nw_tree/README.md`: points at the brief; the two `.asset` drafts there are marked for rebuild in P12.

## 10. Follow-up decisions (2026-10-15)

The user settled the three calls the decision pass left open.

| Call | Decision | Written into |
|---|---|---|
| D7 sub-choice | **W.** At settle, `set_origin = origin_wilderness`; the Heartwood carries over with `copy_deposit_from`; the `nw_uprooted` country flag keeps the mod's content on | §6 D7; `docs/02` §1, §10; plan P19; `plan-event-timing.md` `.100`-`.102`; `docs/05` first test load, item 9 |
| Heartwood flat "+5 a month" | **Dropped.** The Heartwood keeps its other bonuses | `docs/02` §5; `nw_deposits.txt` (placeholder removed); plan P4 |
| Deep Taproot | **Gates Husk collection** | `docs/02` §7, §11; plans P10, P15; `tech_nw_deep_taproot_desc` (BOM intact, `EF BB BF`) |

**Found while writing W in:**
- **Vanilla settling does not change the settling empire's origin.** Its two `set_origin` calls are for a released subject (`events/nomads_events_1.txt:13722,13782`), so `.100` is the only origin change.
- **The settle effect flags the country `settled_as_nomadic_empire`** before it moves the colony (`common/scripted_effects/nomads_effects.txt:5058`). The `.102` relay keys on that flag.
- **The Ark colony is destroyed when vanilla moves the pops** (`transfer_carrier … destroy_colony = yes`, `nomads_effects.txt:5187-5194`). So the Heartwood copy runs in `.101` at `on_arkship_settle`, before the settle popup's choice (`events/nomads_events_1.txt:13520-13554`).
- **A deposit's `potential` only decides random placement and terraforming swaps** (`common/deposits/99_README_DEPOSITS.txt:4,25-26`).
  - The Heartwood's `pc_ark` potential should therefore not stop the copied deposit working on the settled world; that is a test item.
  - H11 and H31 are softened to match.
- **The Deep Taproot gate, applied as worded, also locks the Rootscar's starting Husk until tier 4,** since it is the same deposit (`nw_initializers.txt`).

**Placeholders: 8 in 4 files → 7 in 4 files.** The Heartwood flat-growth placeholder went with the dropped design.
