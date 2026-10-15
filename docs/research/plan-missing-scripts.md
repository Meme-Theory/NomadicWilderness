# Plan: missing and broken scripts

*Written: 2026-10-15 | Updated for decisions D1-D14: 2026-10-15 | Game version: v4.5.2 | Inputs: `install-audit-4.5.2.md` (cited as audit, decisions D1-D14 in §6), `vanilla-nomads-4.5.2.md` (cited as VN), `docs/03-arkship-models.md` (cited as the brief)*

Vanilla citations are `path:line` relative to the install root. Every piece below gives:
- **Source:** the vanilla source
- **Target:** the mod file, in the correct folder
- **Keys:** the keys it defines or uses
- **Copy / change:** what to copy and what to change
- **Depends / risks / open:** dependencies, risks and open questions

Every piece follows the decided path (audit §6). Alternatives the decisions dropped are named once, as dropped. The three calls the decision pass left open were settled the same day and are written in (audit §10): D7-W (P19), the Heartwood's flat growth dropped (P4), and Deep Taproot gating Husk collection (P10, P15).

**Override style.** The install does not document how a duplicate key resolves for each object type, and some types merge instead of replacing (`common/country_limits/ownership_limits/00_ownership_limits.txt:4-7`). So:
- where the vanilla file holds only what the mod changes, or is small, the plan copies the whole file to the same path, which replaces it;
- same-key overrides in a `zz_` file are used only where a whole-file copy would carry too much unrelated vanilla content (the scripted triggers of P1). They are checked on the first test load.

## 0. Critical path

Roadmap Phase 1 is "the mod loads, and a new game starts with one Ark of the designer's class, its decks built and its Heartwood planted, the Rootscar, a science ship and a Logistic Ship, no war fleet and no planets". Ordered by what blocks what:

| # | Piece | Blocks | Decision | Status (2026-10-15) |
|---|---|---|---|---|
| P1 | Wilderness identity override | biomass sync, the Wilderness authority, job names, everything gated on `is_wilderness_empire` | D1-A | Three overrides written; post-settle raw checks moved to P19 |
| P2 | Origin completion (description, sprites) | the origin appearing correctly in the designer | — | Origin picture sprite exists; description and event-picture sprites open |
| P3 | Home-system initializer completion | the Rootscar start | — | Open |
| P4 | Start setup: capital Ark build-out, tree culture, no war fleet | the start state | D3-B, D12-B, D14 | Open |
| P5 | Opening events `.1`/`.2` | the start state | D3-B | `.2` done; `.1` delay open |
| P6 | Fixes already applied (§1.6) | — | — | Done |
| — | *Phase 1 done here* | | | |
| P7 | Wither hook, hull check, empire penalty | Transplant, `.20` | D11 | Thresholds done; hooks open |
| P8 | Transplant and emergency sapling | `.21`, `.30` | — | Open |
| P9 | Nurture: start decision, tier-up, upgrade block | Elder, Take Root | D4-B, D5-A | Food upkeep done; rest open |
| P10 | Tech gating | P9, P11, P15, P16 | D9 | Techs done; consumer checks open |
| P11 | Seed (new Ark) site | more Arks | D6-A | Open |
| P12 | Tree Ark visuals | Phase 3 art | D2-B | Brief done (`03-arkship-models.md`); scripts open |
| P13 | Decks and zones | Ark economy flavour | D4, D14 | Open |
| P14 | Spinal mount re-skins | Elder content | D8 | Open |
| P15 | Harvest names, Fruiting and Husk pops | — | D4, D9 | Open |
| P16 | Roothold cost, Spore Nursery, `.10` relay | — | D4 | `.10` reward done; rest open |
| P17 | Seedwings | — | D2-B | No script needed |
| P18 | Symbioses contracts | — | D10 | Open |
| P19 | Take Root | ending | D7-A, D7-W | Parked: lowest priority, after P21 (the user never settles Arks, 2026-10-15) |
| P20 | AI | Phase 4 | D5-A | Open |
| P21 | Captured-Ark guard | war safety | D1-A | Open |

## 1. Phase 1: loads and starts

### P1. Wilderness identity override (D1-A)

- **Done (decision pass):** `common/scripted_triggers/zz_nw_overrides.txt` (TEMPLATE header removed) re-copies three vanilla triggers verbatim, each with one added `has_origin = origin_nw_uprooted` branch:
  - `is_wilderness_empire` (`common/scripted_triggers/00_scripted_triggers.txt:1766-1769`)
  - `has_purity_ascension` (`common/scripted_triggers/05_scripted_triggers_biogenesis.txt:138-143`)
  - `has_one_genetic_ascension_choice` (`:189-195`)

  `has_reduced_pop_origin` (`00_scripted_triggers.txt:4671-4679`) has no users and is not overridden.
- **Dependents now switched on for the Uprooted:**
  - `wilderness.10` biomass sync (`events/origin_events_wilderness.txt:40-55`, trigger `:45-47`)
  - `auth_mutualistic_intelligence` (`common/governments/authorities/00_authorities.txt:1385-1409`)
  - biomass visibility (`common/strategic_resources/00_strategic_resources.txt:485-491`)
  - the job display names (`common/pop_jobs/04_gestalt_jobs.txt`)
- **Remaining raw checks** (`has_origin = origin_wilderness`, 41 in 16 files, VN §2.1):
  - The ten Wilderness terraform links matter after Take Root (`common/terraform/02_special_terraform_links.txt:5576-5856`). Under D7-W the settled empire has the raw origin, so they need no override (P19).
  - The planet-start checks (`common/inline_scripts/game_start/origin_wilderness.txt:10-40`) are replaced by P4.
  - The rest are AI weights and start-only content. Override one only if a test shows the Uprooted missing content.
- **Risks:**
  - Override semantics for a same-key scripted trigger are not documented in the install: first test load, item 1 (`05-roadmap.md`).
  - Every `is_wilderness_empire = no` gate now excludes the Uprooted, notably the nomad hive start (`authority_hive_nomad.txt:16`), which P4 replaces.
  - `wilderness.300` tags captured Arks (P21).
  - The three bodies must be re-copied after every patch.

### P2. Origin completion

- **Source:**
  - vanilla origins carry `description = "origin_tooltip_..._effects"` (`common/governments/civics/00_origins.txt:90`, `:644`)
  - origin pictures are sprites (`picture = GFX_origin_nomad_default`, `:60`)
  - Wilderness sets `room` and `city_graphical_culture` (`:596-597`)
- **Target:** `common/governments/civics/00_nw_origins.txt`; sprites in `interface/nw_uprooted.gfx`.
- **State:** `interface/nw_uprooted.gfx` now defines `GFX_origin_nw_uprooted` and its two zoom crops, pointing at `gfx/event_pictures/origins/origin_nw_uprooted.dds`, and the origin names that sprite (`00_nw_origins.txt:9`). Both files are new and untracked; they were not part of the decision pass.
- **Copy / change:**
  - add `description = origin_nw_uprooted_effects`; the loc key exists (`localisation/english/nw_uprooted_l_english.yml:5`)
  - optionally `room = "wilderness_room"` or a nomad room (`prescripted_countries/00_top_countries.txt:320` uses `nomads_science_room`)
  - add the event-picture sprites `GFX_evt_nw_*` to `interface/nw_uprooted.gfx`, pointing at placeholder art until Phase 3
- **Already done:** `is_nomadic = yes`, the biological shipset, the Wilderness civic NOR list, `uses_biomass`/`virtual_jobs`, `trait_wilderness`, the D4 growth modifier.
- **Risks:** missing sprite definitions show as errors rather than missing-texture warnings. Phase 1's "clean error.log" needs at least stub sprites.
- **Open:** keep Wilderness's `planet_building_capacity_add = 5` (`00_origins.txt:651-653`)? It is a Wilderness origin bonus not currently copied (design call).

### P3. Home system

- **Source:**
  - origin initializer shape: `toxic_knights_start` (`common/solar_system_initializers/toxoid_initializers.txt:15-45`), with `usage = origin`, `flags = { empire_home_system }`, and an `init_effect` that sets `empire_cluster` on neighbours and runs `generate_home_system_resources`
  - nomad starts do the same (`00_nomad_custom_initializers.txt:21-47`)
- **Target:** `common/solar_system_initializers/nw_initializers.txt`. `usage = origin` and `empire_home_system` are already applied.
- **Copy / change:** copy the `every_neighbor_system`/`empire_cluster` loop and `generate_home_system_resources = yes` into `init_effect` before the Rootscar target save.
- **Risk:**
  - `nomad_country_initial_setup_effect` strips `d_minerals_5`/`d_energy_5` and pre-depletes every harvestable body in the home system (`common/scripted_effects/01_start_of_game_effects.txt:5163-5194`). The Rootscar (`pc_shattered`, harvestable) will start depleted.
  - Decide whether that is wanted. If not, re-apply the Rootscar deposits after `nomads.20`.
- **Open:** whether the engine needs a `home_planet = yes` slot for a nomad start. `00_nomad_custom_initializers.txt` has none, so probably not.

### P4. Start setup: capital Ark build-out (D14), tree culture, no war fleet (D12-B)

- **Source:**
  - nomad hive capital contents: `common/inline_scripts/game_start/authority_hive_nomad.txt:20-44`, which the Uprooted no longer get
  - the Toxic Knights nomadic capital pattern: `common/inline_scripts/game_start/origin_toxic_knights.txt:114-145` (`set_carrier_flag`, `add_zone` with `replace = yes`, `add_building`)
  - the culture in ship scope: `set_graphical_culture` (`common/scripted_effects/01_start_of_game_effects.txt:5204-5218`), reached from the colony with `ship?` (`events/game_start.txt:1792`)
- **Target:**
  - `common/scripted_effects/nw_scripted_effects.txt`: new `nw_uprooted_start_setup_effect` (country scope) and `nw_setup_capital_ark` (colony scope)
  - called from `nw_uprooted.1`'s `immediate` (P5)
- **Keys:** `nw_uprooted_start_setup_effect`, `nw_setup_capital_ark`; country flag `nw_uprooted`.
- **Copy / change:**
  1. On `capital_scope`, the Ark colony (`capital_scope.solar_system` is used by vanilla, `events/nomads_events_1.txt:979-987`):
     - build the starting decks (D14): two Habitation Decks with Wilderness-flavoured zones, mirroring `authority_hive_nomad.txt:31-44`. Use the Uprooted decks once P13 exists, `district_ark_city` until then.
     - add the hive capital building. Wilderness's `building_capital_wilderness` (`origin_wilderness.txt:33-36`) is planet-oriented; test whether it is valid on `pc_ark`, otherwise keep `building_hive_capital`.
  2. Class flags, Heartwood and class trait are already applied by `nw_apply_starting_ark_class` from `.2` (P5); P4 does not repeat them.
  3. Tree culture (D2-B), once P12's cultures exist: `capital_scope = { ship? = { set_graphical_culture = nw_tree_<class> } }`, with the class read from the `starting_<class>_arkship` flag.
  4. `set_country_flag = nw_uprooted`, so the identity gate `nw_is_uprooted_empire` survives an origin change at Take Root (P19).
  5. Living Hull's hull regeneration (`02-mechanics.md` §3.1: +2%, design value): add `ship_hull_regen_add_perc = 0.02` (key used at `common/static_modifiers/25_static_modifiers_nomads.txt:131`) to `nw_thornwood_trait`. Whether a ship key on a colony's static modifier reaches the carrier Ark is unverified; the vanilla precedent is a situation `target_modifier` on an Ark (`common/situations/14_nomads_situations.txt:1347-1354`). If it does not reach the Ark, the regeneration needs a ship-side modifier.
  6. Delete the three unused static modifiers `nw_fruiting`, `nw_spore_survey` and `nw_living_hull` (wiring gap 6). Keep their localisation keys: the trait names reuse them (`nw_uprooted_l_english.yml:40-48`).
  7. Decide on `d_collapsed_burrows`, the vanilla hive blocker (`01_start_of_game_effects.txt:5251-5258`).
  8. No war fleet (D12-B): the vanilla start already gives a science ship and a Logistic Ship and no warships (`events/game_start.txt:1137-1168,1316-1353,1471-1476`). Nothing is added.
- **Heartwood flat growth: dropped (2026-10-15).** The design's "+5 biomass a month" had no vanilla flat pop-growth planet modifier to carry it (`bonus_pop_growth` appears only as a pop-group modifier, `common/deposits/08_overlord_deposits.txt:149-154`). The Heartwood keeps the Tree of Life colony values it already copies, including `logistic_growth_mult = 0.10`, and its placeholder is gone from `nw_deposits.txt`.
- **Depends on:** P1 (job names), P5 (the caller), P12 (culture), P13 (decks).
- **Risks:**
  - The capital Ark's zone and district slots (`common/zone_slots/00_zone_slots.txt:366-463`) must accept the chosen zones.
  - `generate_start_pops` already ran during empire init (`events/game_start.txt:1778`), so pops exist. Check that `trait_wilderness` pops count as biomass from day 1.
- **Open:** which buildings a Wilderness hive Ark should start with.

### P5. Opening events `.1` / `.2` (D3-B)

- **Done (decision pass):** `.2` "What Endured" shows only the option for the designer-chosen class. Each option has a `trigger` on `starting_<class>_arkship` (`01_start_of_game_effects.txt:5206-5217`); the Heartwood option is also the fallback when no class flag is set. The option calls `nw_apply_starting_ark_class`, which tags the Ark, plants the Heartwood and applies one class trait. Nothing is swapped.
- **Remaining:**
  - Fire `.1` one day after game start (`plan-event-timing.md` §3). An on_action cannot delay, so `on_game_start_country` fires a hidden relay `.3`, which fires `.1` with `days = 1` (syntax: `events/ancient_relics_arcsite_events_1.txt:4122`).
  - `.1`'s `immediate` runs `nw_uprooted_start_setup_effect` (P4) once, behind the existing `nw_opening_done` flag.
- **Source:** start ordering (VN §1.1). `on_game_start_country` (`common/on_actions/00_on_actions.txt:31-78`) also runs vanilla `nomads.20`, which moves every starting fleet to a random planet (`events/nomads_events_1.txt:973-975`), and `nomads.25`.
- **Depends on:** P4.
- **Dropped:** D3-A, a cross-class swap at start.

### P6. Already fixed (no further work)

First pass:
- `nw_is_tree_ark_colony` is now `is_planet_class = pc_ark`.
  - **Effect:** closes wiring gap 1 for the Heartwood deposit potential, the wither scan and Transplant. They no longer need the `nw_tree_ark` flag.
  - **Remaining:** new Arks (P11) and tier-ups (P9) must set the class and tier flags; the starting Ark gets them from `.2`.
- Iterators switched to `*_owned_colony`.
- Transplant search uses Ark fleets in the system.
- The Nurture "docked at Roothold" condition uses a Waystation-in-system test. This closes gap 3's unset `nw_docked_at_roothold` flag.

Decision pass (audit §9): the D1 overrides (P1), `.2` (P5), Nurture's food upkeep and starvation pause (P9), the D9 techs (P10), the D11 wither threshold (P7), the `.10`/`.40` pop rewards (D4), and the D4 growth modifiers on the origin, the Withered Heartwood, Memory of the Grove and the Heartwood trait.

## 2. Phase 2: systems

### P7. Wither: hook, hull check, empire penalty (D11)

- **Done:** `nw_check_wither` tests 25% hull, or 12.5% on the Thornwood (D11), with `ship? = { has_hp_percentage < … }`.
- **Source:**
  - hull test `has_hp_percentage` (`events/cosmic_storms_events_1.txt:822`)
  - hooks `on_fleet_exit_battle` (this = fleet, once per fleet, `common/on_actions/00_on_actions.txt:1758-1768`) and `on_ship_disabled` (this = ship, `:2038-2058`)
  - `add_modifier { modifier multiplier }` (`events/shroud_events.txt:4275-4278`)
- **Target:**
  - `events/nw_uprooted_events.txt`: `fleet_event` `nw_uprooted.6` and `ship_event` `nw_uprooted.7`
  - `common/on_actions/nw_on_actions.txt`
  - `common/static_modifiers/nw_static_modifiers.txt`: a new `nw_withered_grove` country modifier
- **Keys:** `nw_uprooted.6`, `.7`, `nw_withered_grove`.
- **Copy / change:**
  - `.6` trigger: `owner = { nw_is_uprooted_empire = yes }`, `is_arkship_fleet = yes` (D2-B keeps that vanilla trigger valid). Immediate: `every_owned_ship = { colony? = { nw_check_wither = yes } }`.
  - `.7` does the same for one disabled ship.
  - Drop `.5` from `on_monthly_pulse_country`. Keep a yearly safety pass on `on_yearly_pulse_country` (`00_on_actions.txt:403`) for storms and other out-of-combat hull loss.
  - Empire penalty: after any wither or transplant, recount withered Arks and re-apply `nw_withered_grove` with `multiplier`. If `multiplier` does not take a variable, use 3 tiered modifiers instead.
  - Visual: `nw_wither_heartwood` sets the withered culture on the Ark (`ship? = { set_graphical_culture = nw_tree_<class>_withered }`), and `nw_plant_heartwood_core` sets the healthy one back (P12).
- **Depends on:** the class flags (set by `.2`), P12 (cultures).
- **Risk:** `colony?` from a ship scope is used from starbase scope in vanilla (`common/scripted_triggers/09_scripted_triggers_nomads.txt:341`) and as `colony` from ship scope (`01_start_of_game_effects.txt:5234`). Test both.
- **Open:** whether an Ark disabled in battle (`never_lost`) should wither regardless of HP.

### P8. Transplant and the emergency sapling

- **Source:** vanilla `decision_transplant_tree` (`common/decisions/06_federation_decisions.txt:5-36`).
- **Target:** `common/decisions/nw_decisions.txt`.
- **Copy / change:**
  - The current decision is fixed (audit §3.7).
  - Add an `OR` in `allow`: `owner = { has_country_flag = nw_has_emergency_sapling }`.
  - In `effect`, if no intact Ark is present, remove that flag. This closes gap 5: the flag `.30` sets is now read.
  - Recount the empire penalty (P7). The healthy tree culture comes back through `nw_plant_heartwood_core` (P7, P12).
- **Depends on:** P7.
- **Open:** whether the emergency sapling also skips the 180 days.

### P9. Nurture: start, tier-up, upgrade block (D4-B, D5-A)

- **Done (decision pass):** Nurture's recurring cost is food upkeep on the approaches: 15 food a month, 45 under Forced Bloom, none under Deep Dormancy (design values). Progress stops while the food stockpile is empty (`resource_stockpile_compare`, syntax `common/scripted_actions/03_arkships.txt:648-652`). No drain event is needed, so `.51` is dropped.
- **Source:**
  - situation schema (`common/situations/99_README_SITUATIONS.txt`), including `total_progress` + `section_weight` (`:105-127`)
  - Ark-targeted template `situation_grand_refurbishment` (`common/situations/14_nomads_situations.txt:1225-1448`), started from a carrier event with a carrier flag guard (`events/nomads_arkship_events.txt:8369-8386`)
  - tier change (`events/game_start.txt:3487-3503`)
  - upgrade gate `upgrade_possible` (`common/starbase_levels/02_orbital_ring_levels.txt:18-25`)
- **Target:**
  - `common/decisions/nw_decisions.txt`: new `decision_nw_nurture`
  - `common/situations/nw_situations.txt`
  - `events/nw_uprooted_events.txt`: `.50` (completion)
  - `common/starbase_levels/06_arkship_levels.txt`: a same-path copy of the vanilla file (ten levels, 173 lines), D5-A
- **Keys:** `decision_nw_nurture`, `nw_nurturing` (carrier flag), `situation_nw_nurture_ark`.
- **Copy / change:**
  1. Decision `potential`: `nw_is_tree_ark_colony = yes`, not tier 3, `NOT = { has_carrier_flag = nw_nurturing }`.
     - `allow`: `tech_arkship_tier_2` for Sapling → Bole; `tech_arkship_tier_3` and Elder Growth for Bole → Elder (`02-mechanics.md` §11).
     - Effect: `set_carrier_flag = nw_nurturing` and `owner = { start_situation = { type = situation_nw_nurture_ark target = root } }`.
  2. Two-length progress: replace the fixed `end = 50/100` stages with `total_progress = { base = 100 modifier = { add = 100 target = { ship? = { is_arkship_tier_2 = yes } } } }` and `section_weight` stages.
  3. `on_progress_complete` (replaces the placeholder in `nw_situations.txt`):
     - from `target`, `fleet = { starbase? = { switch = { trigger = is_ship_size civilian_arkship_tier_1 = { set_starbase_size = civilian_arkship_tier_2 } … } } }` for all six transitions
     - `nw_tag_ark` with the new tier; `remove_carrier_flag = nw_nurturing`; fire `.50` with the Ark saved as an event target
     - re-set the tree culture if it does not survive the size change (first test load, item 4)
     - keep `destroy_situation = this`
  4. D5-A: in the copy, give the six `starbase_level_<class>_arkship_2/3` blocks (`common/starbase_levels/06_arkship_levels.txt:17-51,69-103,122-156`) `upgrade_possible = { owner = { nw_is_uprooted_empire = no } }`. The scope the trigger runs in is first test load, item 5.
  5. Add `on_abort` cleanup (remove `nw_nurturing`) and an `abort_trigger` for owner change, copied from `14_nomads_situations.txt:1423-1447`.
  6. Delete the static modifiers `nw_nurture_forced_bloom` and `nw_nurture_dormancy` from `nw_static_modifiers.txt`. The approaches apply their hull change through `target_modifier`, so nothing uses those two modifiers; the localisation keys stay because the approaches use them.
- **Depends on:** P10, D2-B (vanilla size names).
- **Risks:**
  - The scope of `upgrade_possible` on Ark levels is unverified (the vanilla example uses `planet = {}` from a starbase).
  - `set_starbase_size` by script bypasses the 5000-alloy cost, which is intended.
  - The AI only knows the vanilla upgrade, so it must be taught Nurture (P20).

### P10. Tech gating (D9) — wiring gap 4

- **Done (decision pass):** the four techs are tier 3-4, each hung off a vanilla Nomad gate (`common/technology/nw_technology.txt`; audit §6 D9).
- **Source:** the vanilla gates `tech_arkship_construction` / `_tier_2` / `_tier_3` (`common/technology/00_nomads_dlc_tech.txt:3-124`) and the Waystation techs (`:488-680`).
- **Remaining:** the `has_technology` checks in the consumers:
  - Seed Casting → the Seed site `potential` (P11)
  - Lattice Sensing → the Spore Nursery module `potential` (P16)
  - Deep Taproot → Husk collection (P15; confirmed 2026-10-15). This role is new: D8 kept the vanilla spinal techs, so Deep Taproot lost its original unlock.
  - Elder Growth → `decision_nw_nurture` `allow` for Bole → Elder (P9)
- **Risk:** none beyond balance.

### P11. Seed: the Uprooted's new-Ark site (D6-A)

- **Source:**
  - `civilian_arkship_megastructure` (`common/megastructures/29_nomad_arkships.txt:1-46`) and its inline script (`common/inline_scripts/megastructures/arkship.txt:1-86`)
  - `create_arkship_effect` (`common/scripted_effects/nomads_effects.txt:6982-6997`)
  - `on_build_start` on a megastructure (`common/megastructures/07_strategic_coordination_center.txt:421`)
  - the empire-wide pop kill `global_biomass_spender` (`common/scripted_effects/biogenesis_effects.txt:2810`)
- **Target:**
  - `common/megastructures/nw_tree_arkships.txt` (TEMPLATE, Seed block only)
  - `common/inline_scripts/megastructures/arkship.txt` (override, to hide the vanilla sites)
- **Keys:** `arkship_nw_seed`.
- **Copy / change:**
  - Copy the block and the inline body:
    - `entity`/`construction_entity` = `nw_tree_seed_entity`
    - `potential` adds `owner`/`from` Uprooted, `tech_nw_seed_casting`, and the Bole/Elder dropper (`from = { any_owned_ship = { NOT = { is_arkship_tier_1 = yes } } }`)
    - cost 2000 biomass + 1500 food, 720 days (D6-A; `02-mechanics.md` §3.3). **2000 biomass is 2000 pops: flagged for the balance pass, not re-tuned.**
    - the biomass kill. Megastructures have no `on_queued`, so `on_build_start` sets `local_spent_biomass` to 2000 on the owner and calls `global_biomass_spender`. No vanilla script sets that variable (vanilla only reads it in building, district, decision and terraform costs), so the mod sets it explicitly.
    - `on_build_complete` (vanilla: `arkship.txt:55-56`) creates the Ark with a copy of `create_arkship_effect` whose `create_ship` uses the dropper's class for `random_existing_design = <class>_arkship_tier_1` and sets `graphical_culture = nw_tree_<class>` (P12). It then tags the new colony (`nw_tag_ark` TIER 1, `nw_plant_heartwood_core`) and applies the class trait.
  - Hide the three vanilla Arkship sites for the Uprooted: override the shared inline's `potential` (`arkship.txt:33-40`) with `NOT = { nw_is_uprooted_empire = yes }`, keeping the rest of the body verbatim.
- **Depends on:** P10, P12.
- **Risks:**
  - The inline override applies to every nomad. Keep the vanilla branches verbatim and re-copy them after patches.
  - Refund on cancel: the vanilla Ark sites have no cancel hook, so whether the killed pops come back on a cancelled Seed is a test item.
- **Open:** whether the class stays "inherit the dropper's class", or becomes a confirmation event.

### P12. Tree Ark visuals (D2-B)

- **Brief:** `docs/03-arkship-models.md`, verified against the install.
- **Source:**
  - Ark section templates (`common/section_templates/nomads_dlc.txt:194-1647`)
  - culture prefixing and fallback (`common/HOW_TO_MAKE_NEW_SHIPS.txt:52-69`; `common/graphical_culture/00_graphical_culture.txt:2`)
  - Arkship cultures (`00_graphical_culture.txt:1742-1836`) and the size culture lists (`common/ship_sizes/29_nomads_dlc_ships.txt:108` and eight more)
  - `set_graphical_culture` (`01_start_of_game_effects.txt:5207,5211,5215`)
  - vanilla Ark entities, meshes and animations (`gfx/models/ships/other/_other_entities.asset:4713-9949`, `_other_meshes.gfx:1640-1666`, `_other_animations.asset:395-418`)
- **Target:**
  - `common/graphical_culture/nw_graphical_culture.txt` (new): `nw_tree_heartwood`, `nw_tree_lumen` and `nw_tree_thornwood`, each copying `civilian_arkship` (`00_graphical_culture.txt:1742-1772`) with `fallback` set to the matching vanilla Ark culture, plus a withered twin of each
  - `gfx/models/arkships/nw_tree/`: the `pdxmesh` definitions in a `.gfx` file under `objectTypes`; entities and animations in `.asset` files. Entity names follow the brief §1 (`nw_tree_heartwood_civilian_arkship_01_stage_1_ship_entity` and so on).
  - the culture setters: P4 (start), P11 (new Arks), P7/P8 (wither and transplant), P21 (capture)
- **Copy / change:**
  - The existing `nw_tree_arkships.asset` and `nw_tree_arkships_entities.asset` predate the decisions. They have `pdxmesh` blocks in an `.asset`, entity names the engine never looks up, a `combat` state that vanilla Arks do not have, and a "wither" shader float that the culture swap replaces. Rebuild them from the brief §1-§2.
  - Hulls need the vanilla hull locators (component slots, `district_01..27`); frames need `part1`, `module_01..06` and `spinal_mount` (brief §2).
- **Order:** first the vanilla-mesh proof (brief §7, step 1), then the Seed and the tier-1 hulls.
- **Risks:**
  - If the size culture lists reject the mod culture (H35), override the nine Ark sizes to add the mod cultures. The keys stay vanilla, but other mods that edit those sizes will conflict.
  - Whether the frame entity is prefixed, whether the culture survives `set_starbase_size`, and whether a mid-game swap re-renders are first test load items 3, 4 and 7.
- **Dropped:** D2-A, nine `nw_tree_*` sizes with their levels, section templates, trigger overrides, icons and starbase types.

### P13. Decks and zones (D4, D14)

- **Source:** Ark districts (`common/districts/07_ark_districts.txt`), Ark zones (`common/zones/05_arkship_zones.txt`), Wilderness biomass district costs (`common/districts/05_wilderness_districts.txt:48-59` + `common/inline_scripts/buildings/on_all_wilderness_buildings_districts.txt:13-43`).
- **Target:** `common/districts/nw_districts.txt` (TEMPLATE; its header lists the keys) and a new `common/zones/nw_zones.txt` for the `zone_nw_*` keys.
- **Copy / change:**
  - Copy the four Ark district blocks and the four Arkship Deck zones with `potential` on the Uprooted, and add `NOT nw_is_uprooted_empire` to the vanilla blocks. The vanilla edit is a full-block override of the `07_ark_districts.txt` entries.
  - One-off cost per D4: 50 biomass a deck (`02-mechanics.md` §2), with the vanilla `on_queued`/`on_unqueued` pop kill.
  - Fruiting Bough is the Arkship Deck itself (`district_ark_basic`); its Spore Garden zone comes from `zone_ark_food`.
  - D14's starting decks (P4) switch to these once they exist.
- **Risk:** the district override conflicts with any other Ark mod.
- **Dropped:** the "names only" option, because D4 re-costs the decks.

### P14. Spinal mount re-skins (D8)

- **Source:**
  - `common/starbase_modules/00_arkship_spinal_modules.txt:4-332`
  - vanilla names `sm_arkship_spinal_devastator_mount` "Devastator Mount", `sm_arkship_planetary_refinery` "Planetary Reprocessor", `sm_arkship_system_scanner` "Subspace Imaging Array" (`localisation/english/nomads_1_l_english.yml:3350,3362,3374`)
- **Target:** no new modules. Models come through the culture (P12), names through localisation.
- **Copy / change:**
  - Models: `<culture>_spinal_module_super_refinery_01_entity`, `<culture>_spinal_module_scanner_01_entity` and `<culture>_spinal_module_devastator_01_entity` (brief §1), as vanilla does per Ark class (`_other_entities.asset:9949`).
  - Names: Taproot, Heliotrope Crown and Heartlance, for the Uprooted only. The vanilla names are static keys, so an owner-dependent name needs the key to call a scripted localisation. Whether a module name is evaluated with a scope that reaches the owner is unverified; if it is not, the names stay vanilla and the re-skin is visual only.
  - Delete `common/component_templates/nw_spinal_mounts.txt` (TEMPLATE, obsolete).
  - The design's Heliotrope aura is dropped (D8).
- **Risk:** none for gameplay. Keys, effects, techs, the slot trigger and the AI design stay vanilla, so `arkship_reprocess_planet`'s `has_starbase_module = arkship_planetary_refinery` check (`03_arkships.txt:831-838`) keeps working.
- **Dropped:** new module keys with class gates, and the three overrides they needed.

### P15. Harvest names, the Fruiting credit and the Husk (D4, D9)

- **Source:** `arkship_strip_mine` / `arkship_reprocess_planet` (`common/scripted_actions/03_arkships.txt:478-938`); `on_harvest_resources` / `on_arkship_strip_mine` / `on_arkship_reprocess_planet` (`common/on_actions/02_component_on_actions.txt:219-245`; vanilla `nomads.4100` is a `carrier_event`, this = target planet, from = fleet, `events/nomads_events_1.txt:12979-13000`).
- **Target:** `events/nw_uprooted_events.txt`: a new hidden `carrier_event` `nw_uprooted.60`, plus on_action entries; `common/deposits/nw_deposits.txt` (`d_nw_husk_of_the_grove`); localisation for the Rootfeeding / Devouring Root names.
- **Copy / change:**
  - **Fruiting (D4, pop creation).** `.60` trigger: `from` is an Uprooted Heartwood Ark. Effect: `create_pop_group` on the Ark colony (`from` → ship → `colony`; syntax `common/scripted_effects/biogenesis_effects.txt:2916-2919`), because a stockpile add is undone monthly. The harvested amount is not exposed: approximate it with the same script values (`common/script_values/08_script_values_nomads.txt:722-786`), or grant a flat amount per harvest.
  - **Husk (D9; Deep Taproot gates collection, confirmed 2026-10-15).**
    - After `arkship_reprocess_planet` by an Uprooted owner, add `d_nw_husk_of_the_grove` to the resulting Shattered World. The Rootscar starts with one (`nw_initializers.txt`).
    - Collecting a Husk once, by Seedwing action or decision, requires `tech_nw_deep_taproot` and creates pops with `create_pop_group`. Then delete the deposit's `produces` block and its placeholder.
    - The Rootscar's starting Husk is exempt (decided 2026-10-15). The collection gate is `OR = { has_planet_flag = nw_rootscar  owner has tech_nw_deep_taproot }`; the initializer already flags the Rootscar `nw_rootscar`.
  - **Names (D13 approach).** Rootfeeding and Devouring Root for Uprooted owners need scripted or triggered localisation, or overrides of the action blocks, whose `context_menu_name`/`tooltip` are loc keys.
- **Open:** flat vs proportional Fruiting credit; how the Husk is collected.

### P16. Roothold cost, Spore Nursery, `.10` relay (D4)

- **Done (decision pass):** `.10`'s reward is pop creation (`create_pop_group`, 50 pops on the capital, design value).
- **Source:**
  - `waystation_megastructure` (`common/megastructures/30_nomad_waystation.txt:1-444`), whose cost already branches on `trigger` (`:14-51`)
  - `on_waystation_built` (`common/on_actions/00_on_actions.txt:5916-5928`, this = system, from = owner)
  - relay template `timeline.95` (`events/timeline_events.txt:3538-3560`)
  - module template `waystation_research_module` (`common/starbase_modules/00_waystation_modules.txt:650`)
- **Target:**
  - `events/nw_uprooted_events.txt`: `system_event` `nw_uprooted.11` relay → country `.10`
  - `common/on_actions/nw_on_actions.txt` (replaces the placeholder)
  - `common/megastructures/30_nomad_waystation.txt`: a same-path copy of the vanilla file, which holds only `waystation_megastructure`
  - `common/starbase_modules/nw_waystation_modules.txt`: `nw_spore_nursery`
- **Copy / change:**
  - Relay trigger: `from = { nw_is_uprooted_empire = yes NOT = { has_country_flag = nw_first_roothold_seen } }`, then `from = { country_event = { id = nw_uprooted.10 } }`.
  - Roothold cost (D4: 100 biomass in place of the alloy and food part, influence as vanilla; design value):
    - copy the block verbatim, add `nw_is_uprooted_empire = no` to the alloy and bioship food branches, and add a branch `trigger = { nw_is_uprooted_empire = yes } biomass = 100`
    - the kill goes in `on_build_start` for Uprooted owners, as in P11 (`local_spent_biomass` set explicitly, then `global_biomass_spender`)
    - keeping the vanilla key keeps every vanilla reference to Waystations working
  - Spore Nursery: gated on Lattice Sensing (P10).
  - Art: bioship cultures already get organic Waystations (`gfx/models/ships/starbases/_starbase_entities.asset:6655-6735`). A `nw_roothold` culture is optional (brief §5).
- **Risks:**
  - The Waystation override conflicts with any mod that edits `waystation_megastructure`.
  - The "survey adjacent systems" effect of Spore Nursery has no vanilla analogue. Arks survey with `arkship_survey` in their own system only (`common/scripted_actions/03_arkships.txt:66-131`), so the nursery needs a scripted survey of neighbour systems (`set_surveyed`, as `nomads.4300` does, `events/nomads_events_1.txt:13263-13283`).
- **Open:** survey scope and cost.
- **Dropped:** keeping the vanilla bioship cost.

### P17. Seedwings

- **Source:** the Logistic Ship `nomads_constructor` (`common/ship_sizes/29_nomads_dlc_ships.txt:1337-1443`) and the science ship (`common/ship_sizes/00_ship_sizes.txt:1410`). Both already have bioship food costs, and bioship cultures already have `biogenesis_01_constructor_entity` and `biogenesis_01_science_entity` (`gfx/models/ships/biogenesis_01/_biogenesis_01_ships_entities.asset:1036,1345`).
- **Target:** none required. Names through localisation (README naming rule); art through the bioship culture, or optionally a culture swap (brief §5).
- **Risk:** none.
- **Dropped:** `nw_seedwing` sizes. New sizes would not be "the" Logistic Ship that vanilla code expects (`nomads_constructor` in `30_nomad_waystation.txt:70-71`, `events/game_start.txt:1328`), and `common/ship_sizes/nw_tree_arkship_sizes.txt` is obsolete under D2-B.

### P18. Symbioses contracts (D10)

- **Source:**
  - schema (`common/missions/missions/99_README_MISSIONS.txt:1-126`)
  - `contract_clear_blockers` (`02_contracts.txt:1947-2139`) with its chain (`common/event_chains/00_event_chains_nomads.txt:412`) and project (`common/special_projects/06_projects_nomads.txt:1280`)
  - `contract_colony_startup` (`02_contracts.txt:3320-`, chain `00_event_chains_nomads.txt:490`)
  - internal contracts (`02_contracts.txt:3703-3740`, chains `00_event_chains_nomads.txt:166-184`)
- **Target:** `common/missions/missions/nw_contracts.txt`, `common/event_chains/nw_event_chains.txt`, `common/special_projects/nw_projects.txt`.
- **Keys:** `contract_nw_seed_a_grove`, `contract_nw_beg_a_sapling`. Reforest is vanilla `contract_clear_blockers`.
- **Copy / change:**
  - **Reforest:** no new contract. The vanilla block has a static `desc` (`02_contracts.txt:1949`), so override it to add a triggered `desc`/`desc_operator` (`99_README_MISSIONS.txt:23-34`) for Uprooted operators.
  - **Seed a Grove:** copy `contract_colony_startup`. Issuer potential is low-habitability colonies. The reward is a mod deposit (D10). The vanilla Tree of Life deposits name a ToL owner in `potential` (`07_federations_deposits.txt:618-625`), which per the deposits README only decides random placement (`common/deposits/99_README_DEPOSITS.txt:4`).
  - **Beg a Sapling:** an internal contract (operator = issuer), modelled on `FindResourceInternal`.
- **Risk:** contract AI weights decide whether settled AIs ever issue these.

### P19. Take Root (D7-A, D7-W)

**Parked 2026-10-15: lowest priority, after P21.** The user never settles Arks. Until this is picked up, the only requirement is that vanilla settling doesn't throw errors for an Uprooted empire. The D7 design below stays as the plan for when it is.

- **Source:**
  - the settle action `arkship_settle` (`common/scripted_actions/03_arkships.txt:1243-1329`), which fires `on_arkship_settle` when it completes (`common/on_actions/02_component_on_actions.txt:380-387`)
  - `nomads.4700`, which saves the settling Ark and opens `nomads.4705` (`events/nomads_events_1.txt:13520-13554`). The hive options run `arkship_settle_planet_effect` (`:13624-13670`; `common/scripted_effects/nomads_effects.txt:5053-5223`). That effect:
    - sets `settled_as_nomadic_empire` (`:5058`)
    - copies the Knights' deposits with `copy_deposit_from` (`:5156-5175`; the effect itself is `common/scripted_effects/00_scripted_effects.txt:9390-9399`)
    - moves the colony with `transfer_carrier … destroy_colony = yes` (`:5187-5194`)
  - `on_capital_changed` (`common/on_actions/00_on_actions.txt:3442-3454`); vanilla hangs `carrier_event`s on it, e.g. `origin.3205`, which moves the Progenitor deposit to a new capital (`events/origin_events_3.txt:433-468`)
  - the Wilderness start: `wilderness.100` (`events/origin_events_wilderness.txt:62-97`) and the capital setup (`common/inline_scripts/game_start/origin_wilderness.txt:10-61`)
- **Target:**
  - `common/decisions/nw_decisions.txt`: retire `decision_nw_take_root` (marked superseded in the decision pass)
  - `events/nw_uprooted_events.txt`: `.100`, plus new hidden `.101` and `.102`
  - `common/on_actions/nw_on_actions.txt`: `.101` on `on_arkship_settle`, `.102` on `on_capital_changed`
- **Copy / change:**
  1. Keep the vanilla action, its tradition gate and its colonizable target. "Last Ark only" matches vanilla: a hive settles its single Ark or scuttles the rest (`events/nomads_events_1.txt:13624-13670`).
  2. **`.101`, the Heartwood copy** (`planet_event` on `on_arkship_settle`; this = target planet, from = fleet).
     - For an Uprooted owner, save the colony ship as vanilla does: `from = { random_owned_ship = { limit = { is_colony = yes } … } }` (`nomads_events_1.txt:13539-13546`).
     - Then run `copy_deposit_from = { SCOPE = <that ship> DEPOSIT = d_nw_heartwood_core }` on the planet.
     - **Why here:** by the time the capital moves, `transfer_carrier` has destroyed the Ark's colony. If the test shows the old colony still exists when `on_capital_changed` fires, the copy can move to `.102` instead, following `origin.3205`.
     - A withered Ark has no Heartwood, so nothing is copied.
     - The deposit's `potential` (`is_planet_class = pc_ark`) should not block it, because `potential` only decides random placement (`common/deposits/99_README_DEPOSITS.txt:4`). That its modifier applies on the planet is a test item.
  3. **`.102`, the relay** (`carrier_event` on `on_capital_changed`; this = new capital colony).
     - Condition: the owner is Uprooted, has `settled_as_nomadic_empire` and not `nw_rooted`.
     - Fires `.100` on the owner with `days = 1`, so the vanilla settle effect has finished.
     - It keys on the flag rather than on `from`, because the old capital colony may be gone.
  4. **`.100`, in this order:**
     - **`set_origin = origin_wilderness` (D7-W).** Vanilla settling never changes the settling empire's origin; its two `set_origin` calls are for a released subject (`nomads_events_1.txt:13722,13782`). So nothing overwrites this.
     - **(a) Mod content stays on.** After `set_origin`, `has_origin = origin_nw_uprooted` is false. Run `set_country_flag = nw_uprooted`, which `nw_is_uprooted_empire` already accepts. Set it after `set_origin`, in case the engine drops the old origin's flags by name.
     - **(b) The `started_*` flag.**
       - `wilderness.100` set it at game start from the `pc_ark` capital, whose climate is "artificial" (`origin_events_wilderness.txt:78-93`; `06_planet_classes_nomads.txt:70`), so the Ark probably gave `started_cold`.
       - Remove `started_wet`, `started_dry` and `started_cold`, then fire `wilderness.100` again. Its trigger is `is_wilderness_empire`, which the raw origin now meets, and it sets the right flag from the new capital's climate.
       - Its other effects are harmless on a settled capital: it re-sets `benevolent_terms_preference` and removes `d_decrepit_dwellings`.
       - The ten Wilderness terraform links need the raw origin plus that flag (`common/terraform/02_special_terraform_links.txt:5576-5856`); under W both are now true.
     - **(c) Planet setup.**
       - Add `building_capital_wilderness` if the planet has no capital building (`origin_wilderness.txt:33-36`).
       - Which of the other start buildings (`:37-60`) to grant is a design call.
       - Leave out the start-only lines: `set_planet_size = 18`, `remove_all_buildings`, and the `wilderness_planet` marker, which only the start district placement reads (`common/inline_scripts/game_start/rural_districts_placement.txt:53-308`).
     - **Last:** `set_country_flag = nw_rooted` and the flavour. Replace the current `.100` body, which belongs to the retired decision: it acts on `event_target:nw_take_root_target`, which nothing sets under D7-A, and its `set_planet_size = 18` and commented `change_pc` go.
- **Origin flags across `set_origin`: checked in the install; not determinable.**
  - **Origin `flags` are country flags.**
    - The define calls `uses_biomass` a "Country flag" that turns on the monthly biomass on_action (`common/defines/00_defines.txt:1218`; `on_biomass_monthly` → `wilderness.10`, `common/on_actions/00_on_actions.txt:5523-5528`).
    - Script reads civic flags with `has_country_flag`: `suppress_vanilla_factions` is declared at `common/governments/civics/00_civics.txt:3976` and read at `common/scripted_triggers/00_scripted_triggers.txt:4063`.
  - **No script sets or removes `uses_biomass` or `virtual_jobs`.** The origins' `flags` blocks are their only source (`00_origins.txt:645-649`; `00_nw_origins.txt`).
  - **No `set_origin` call handles flags.** None of the 67 vanilla calls touches the old or new origin's declared flags; the flag edits within 25 lines of each call are unrelated. `set_origin_effect` handles none (`common/scripted_effects/00_scripted_effects.txt:7341`).
  - **So nothing in the install says** whether `set_origin` keeps the old origin's flags, swaps them for the new origin's, or drops them.
  - **For W:**
    - `uses_biomass` and `virtual_jobs` are on both origins, so they are lost only if the old flags are dropped and the new ones are not added.
    - `nw_uprooted` and `custom_start_screen` are only on `origin_nw_uprooted`, hence (a).
  - **Test:** first test load, item 9 (`05-roadmap.md`).
- **Also expected, untested:** the Uprooted origin's modifier (+5% unity, +10% pop growth) ends at settle, and Wilderness's +5 building slots (`00_origins.txt:651-653`) begin.
- **Risk:** whether `on_capital_changed` fires on settle is unverified (first test load, item 6). The fallback is a short daily poll started by `.101`, until `settled_as_nomadic_empire` is set.
- **Dropped:**
  - option B, a custom decision with `change_pc`
  - sub-choice D (keep `origin_nw_uprooted`), with its new terraform links
  - sub-choice T (`origin_tree_of_life`), which would end the Wilderness status

### P20. AI (D5-A)

- **Source:** Arkship megastructure AI weights (`common/megastructures/29_nomad_arkships.txt:9-45`), `@ai_arkship_build_cap = 8` (`common/scripted_variables/10_scripted_variables_nomads.txt:181`), situation approach `ai_weight` (README `:194`), decision `ai_weight` (`common/decisions/example.txt:53-59`).
- **Target:** the respective mod files.
- **Copy / change:**
  - Nurture decision `ai_weight` when the food stockpile is over 500 and no Nurture is running (`02-mechanics.md` §12).
  - Approach `ai_weight`: Slow Growth by default.
  - Seed site `ai_weight` copied from the civilian block.
  - Take Root is the vanilla settle action, so the AI settles by vanilla rules.
- **Risk:** with D5-A the AI cannot use the vanilla upgrade, so it never tiers up unless it is taught Nurture.

### P21. Captured-Ark guard (after P1)

- **Source:** `wilderness.300` (`events/origin_events_wilderness.txt:150-262`), `nomads.1` (`events/nomads_events_1.txt:20-38`), Ark transfer cleanup `arkship.1450` (`common/on_actions/00_on_actions.txt:3419`).
- **Target:** `common/on_actions/nw_on_actions.txt`, `events/nw_uprooted_events.txt`: a hidden `carrier_event` on `on_colony_transfer`.
- **Copy / change:** when `is_planet_class = pc_ark` and `fromfrom` is Uprooted:
  - remove `d_wilderness_city` and `d_wilderness_industrial`
  - if the new owner is not Uprooted: remove `nw_withered_heartwood`, the class trait modifiers and the Heartwood, and set the Ark back to its vanilla `<class>_arkship` culture (brief §3)
- **Risk:** event order within `on_colony_transfer` is vanilla-first or mod-first, unverified. Use `days = 1`.

## 3. Wiring gaps from the brief, mapped

| Gap | Status after the decision pass | Plan |
|---|---|---|
| 1. Nothing tags the starting Ark | Ark detection uses `pc_ark`; `.2` tags the starting Ark through `nw_apply_starting_ark_class`. New Arks and tier-ups still need it | P9, P11 |
| 2. Wither cannot fire | The hull test is real and follows D11; the hook is still the monthly pulse | P7 |
| 3. Nurture cannot start or finish | Syntax fixed, docked test real, `destroy_situation` added, food upkeep and starvation pause in (D4). Start, tier-up and two-length progress remain | P9 |
| 4. Techs gate nothing | Techs re-tiered (D9); the consumers' checks remain | P10 |
| 5. `.10`/`.30` untriggered; emergency sapling unread; `.100` placeholders | `.10` grants pops; hooks named; the relays remain; `.100` follows D7-A and D7-W | P8, P16, P19, plan-event-timing |
| 6. Two modifier names for one trait | `nw_apply_starting_ark_class` uses `nw_<class>_trait` and removes the other two. The originals `nw_fruiting`/`nw_spore_survey`/`nw_living_hull` are now unused | P4 (delete them) |
