# 02 – Mechanics

Numbers in this document are design targets. Where a value mirrors vanilla, the vanilla source is named so it can be re-checked after each patch.

Vanilla values were verified against the installed game, v4.5.2, on 2026-10-15. Citations are paths relative to the Stellaris install. The evidence is in `docs/research/install-audit-4.5.2.md` and script keys are in `04-vanilla-hooks.md`. Numbers marked **design value, balance pending** were converted to the biomass rules of decision D4; they are starting points for the balance pass, not equivalents of the old numbers.

## 1. The origin: Uprooted

**Decision log (2026-10-15).** All fourteen audit decisions were accepted; the full record is in audit §6.

| ID | Decision |
|---|---|
| D1 | Standalone origin. `is_wilderness_empire` (and two same-pattern ascension triggers) gain an `origin_nw_uprooted` branch. |
| D2 | Tree Arks keep the vanilla Arkship sizes and classes; the tree look is a mod graphical culture. Fully new tree models (`03-arkship-models.md`). |
| D3 | The empire designer picks the Ark class; event `.2` is flavour and applies the class trait. |
| D4 | One-off costs are biomass (paid in pops); recurring costs and drains are food; one-off rewards are new pops; "biomass income" is pop growth. |
| D5 | Nurture replaces the vanilla Ark upgrade for the Uprooted; the AI is taught Nurture. |
| D6 | New Arks come from the Uprooted's own Seed site; the vanilla Arkship sites are hidden for them. |
| D7 | Take Root reuses vanilla settling; the Heartwood carries over. At settle the empire takes `origin_wilderness` and keeps the mod's content through the `nw_uprooted` flag (sub-choice W, §10). |
| D8 | The three spinal mounts are re-skins of vanilla modules; no class gates. |
| D9 | The four Uprooted techs sit at tiers 3-4, each after a vanilla Nomad tech. |
| D10 | Reforest is the vanilla Clear Blockers contract reflavoured; Seed a Grove gives a mod deposit; Beg a Sapling is an internal contract. |
| D11 | The Thornwood withers at 12.5% hull; the others at 25%. |
| D12 | No starting bioship war fleet. |
| D13 | Operational Reserves are called "Sap" for the Uprooted only. |
| D14 | The start event builds out the capital Ark. |

**Follow-ups (2026-10-15):** D7's sub-choice is W (above). The Heartwood's flat "+5 biomass a month" is dropped (§5). Deep Taproot gates Husk collection (§7, §11). Record: audit §10.

| Field | Value |
|---|---|
| Key | `origin_nw_uprooted` |
| Authority | Hive Mind only |
| Shipset | Biological (`biogenesis_01`/`02`), as Wilderness requires (`common/governments/civics/00_origins.txt:610-616`). This makes the empire a bioship user, so vanilla already charges it food for Logistic Ships, science ships and Waystations. |
| Excluded civics | Devouring Swarm, Tree of Life's rule (`00_origins.txt:2766-2768`). Terravore is the lithoid form of the same civic (`common/governments/civics/02_gestalt_civics.txt:355`). Also the 13 civics `origin_wilderness` excludes (`00_origins.txt:617-633`). |
| DLC | Nomads + BioGenesis required to pick it. Plantoids is checked at runtime for sapling art and is not required; the Tree of Life origin has no DLC gate in script (`00_origins.txt:2758-2793`). |
| Empire type | Nomadic: the player turns on the empire designer's Nomadic toggle, and the origin requires `is_nomadic = yes`, as Voidfarers (`origin_default_nomads`) does (`00_origins.txt:73-75`). |
| Wilderness identity | The origin carries `uses_biomass`, `virtual_jobs` and `trait_wilderness` like `origin_wilderness` (`00_origins.txt:645-661`). Vanilla Wilderness content sees the Uprooted through the D1 overrides (`common/scripted_triggers/zz_nw_overrides.txt`). |
| Starting colony | No planet. The capital is the starting Ark's colony (planet class `pc_ark`), which the engine creates from the designer's Arkship choice (`prescripted_countries/default.txt:23`). The start event builds its decks and plants its Heartwood (D14). The home system holds the Rootscar (Shattered World). |
| Starting fleet | The vanilla nomad start: one science ship and one Logistic Ship, the Seedwing Scout and the Seedwing (`events/game_start.txt:1137-1168`, `:1316-1353`). Being bioship users, both use the bioship models. No war fleet: the Ark is the warship (D12). |
| Starting Ark | The class the player picked in the empire designer: Civilian = Heartwood, Scientific = Lumen, Military = Thornwood. Vanilla records the choice as `starting_<class>_arkship` (`common/scripted_effects/01_start_of_game_effects.txt:5206-5217`); event `.2` reads it and applies the class trait (D3). |

**Why a standalone origin.**
- **What it gives:**
  - It sits beside vanilla Wilderness without changing it.
  - It gives a clean place for the Uprooted-only start (no homeworld, Rootscar, seed system).
- **How vanilla sees it:** the Wilderness identity trigger `is_wilderness_empire` is a one-line `has_origin = origin_wilderness` behind 1334 references (`common/scripted_triggers/00_scripted_triggers.txt:1766-1769`). The mod overrides it with one added `origin_nw_uprooted` branch, plus the two ascension triggers that follow the same pattern.
- **What is left:** about forty raw `has_origin = origin_wilderness` checks still miss the Uprooted. Most are planet-start content, AI weights or achievements. The ones that matter after Take Root are the Wilderness terraform links (§10).

## 2. Economy translation

Vanilla Nomads run on alloys, minerals, energy and Operational Reserves. Vanilla Wilderness runs on biomass, food and energy. The Uprooted take the Wilderness side of every fork.

**What biomass is.**
- Biomass is the Wilderness pop count. The stockpile is set to the number of `trait_wilderness` pops every month (`events/origin_events_wilderness.txt:40-55`).
- A biomass cost is paid by killing that many pops, the way vanilla Wilderness decisions and districts do it (`common/decisions/14_wilderness_decisions.txt:19-64`).

**Cost model (D4):**
- **One-off costs** (seeds, decks, Rootholds) are biomass: the hive spends its own body.
- **Recurring costs and drains** are food.
- **One-off rewards** are new pops.
- **"Biomass income"** is pop growth (`logistic_growth_mult`).

| Need | Vanilla Nomads (v4.5.2) | Uprooted |
|---|---|---|
| New Arkship | Megastructure built by an existing Ark: 10000 alloys + 500 influence, 720 days. Bioship empires pay 5000 alloys + 17500 food (`common/inline_scripts/megastructures/arkship.txt:10-31`). | **Seed**, the Uprooted's own site (D6): 2000 biomass + 1500 food, 720 days. 2000 biomass is 2000 pops; the balance pass checks it. See §3.3. |
| Tier upgrade | Starbase upgrade: 5000 alloys + 250 influence, 900 / 1080 days (`common/ship_sizes/29_nomads_dlc_ships.txt:328-334`, `:459-465`) | Blocked for the Uprooted. A **Nurture** situation grows the Ark, paid in food over time (D5, §3.3). |
| Deck districts | Alloys: 150, or 250 for Habitation Decks (`common/districts/07_ark_districts.txt:1-2`) | 50 biomass each, the vanilla Wilderness district biomass cost (`@biomass_cost`, `common/districts/05_wilderness_districts.txt:3`) |
| Deck buildings | Alloys for nomads (`common/inline_scripts/buildings/nomadic_cost_switcher.txt`) | Biomass, with the same tiering as vanilla Wilderness buildings |
| Ship hulls | Bioship empires pay half alloys plus food (`common/scripted_variables/08_scripted_variables_biogenesis.txt:14`) | The same: vanilla bioship costs |
| Ark Systems and Modules | Alloys | Alloys. Kept on purpose: the Orespine job line still outputs alloys ("Ironwood") and this keeps the Ark component UI untouched. |
| Operational Reserves | A combined view of minerals and energy run as a situation (`common/resource_converters/00_resource_converters.txt`; `common/situations/14_nomads_situations.txt:947`) | Same mechanic, called **Sap** in Uprooted text (D13). Vanilla tooltips name it through the static key `concept_operational_reserves` (`localisation/english/nomads_2_l_english.yml:352`) and will still say "Operational Reserves". |
| Waystation build | Megastructure built by a Logistic Ship or Ark: 100 influence + 250 alloys; bioship empires 125 alloys + 437.5 food (`common/megastructures/30_nomad_waystation.txt:14-51`) | **Roothold**: 100 biomass in place of the alloy and food part (spore seeding), influence as vanilla. Design value, balance pending. |

**Biomass income.**
- On an Ark, biomass income is pop growth, as on a Wilderness planet.
- The Heartwood Core (§5) adds +10% pop growth.
- The origin adds +10% pop growth (`logistic_growth_mult = 0.10`, design value, balance pending).
- "Arks start with 1000 pops" (an earlier wiki figure) is unverified.

**Failure state.**
- Vanilla Nomads treat low Operational Reserves as a failure state: the reserves situation has a critical stage, and 4.5 blocks mineral-cost ships while reserves are critical. The Uprooted inherit that unchanged.
- The Wilderness-specific pressure is food: Nurture runs on food, and growth pauses when the food stockpile runs dry.
- No death spiral beyond vanilla.

## 3. The three Tree Arks

Every Ark is one of three species of world-tree. They are the vanilla Civilian, Scientific and Military Arkships: same sizes, same classes, same per-tier bonuses, so balance stays close to Voidfarers (D2). The tree look comes from a mod graphical culture set on Uprooted Arks (`03-arkship-models.md`). Each tree adds one Wilderness-specific trait, applied as a colony modifier on its Ark.

### 3.1 Class table

| | Heartwood Ark (Civilian) | Lumen Ark (Scientific) | Thornwood Ark (Military) |
|---|---|---|---|
| Vanilla bonus kept (tier 1 / 2 / 3) | +2500 / 5000 / 7500 Operational Reserve capacity (`common/scripted_variables/10_scripted_variables_nomads.txt:548-550`); harvest yield +5 / 10 / 15% (`29_nomads_dlc_ships.txt:179,302,433`); sensor +1 | Sensor range +2 / 3 / 4, hyperlane detection +1 / 2 / 3, survey speed +5 / 10 / 15% (`:556-561`, `:681-686`, `:814-819`); Deep Scan success +5 / 10 / 15 (`common/script_values/08_script_values_nomads.txt:607-629`) | Fire rate and weapon range +5 / 10 / 15% (`:945-946`, `:1069-1070`, `:1201-1202`); sensor +1; extra medium and large turret slots on auto-generated designs (`common/section_templates/nomads_dlc.txt:729-1116`) |
| Wilderness trait | **Fruiting**: a share of everything the Ark harvests becomes new pops (biomass), plus +5% pop growth on the Ark (design value, balance pending) | **Spore Survey**: through a Spore Nursery, the Ark surveys systems along the Lattice without entering them (§6). Every vanilla Ark with a leader can already survey its own system (`common/scripted_actions/03_arkships.txt:66-131`). | **Living Hull**: +2% hull regeneration (design value), and the Heartwood withers only below 12.5% hull instead of 25% (§5) |
| Spinal mount re-skin | **Taproot**: the vanilla Planetary Reprocessor, which enables Devouring Root | **Heliotrope Crown**: the vanilla Subspace Imaging Array | **Heartlance**: the vanilla Devastator Mount |
| Role | Economy, Roothold seeding, biomass engine | Exploration, Symbioses with research empires | Escort, wither insurance, bounty-fleet answer |

**Spinal mounts (D8).**
- The three spinal mounts are the Uprooted's names and models for three vanilla starbase modules (`common/starbase_modules/00_arkship_spinal_modules.txt:4-43`, `:106-152`, `:197`).
- Effects and techs are vanilla, and any class can fit any of them, as in vanilla.
- The models come with the tree culture.
- The names need scripted or triggered localisation, because the vanilla names are static keys.

### 3.2 Tiers

| Tier | Name | Fantasy | District cap (vanilla) |
|---|---|---|---|
| 1 | Sapling | A seed that has cracked open. Thin trunk, sparse crown, roots still coiled. | base (`pc_ark` is size 9) |
| 2 | Bole | A full tree. Crown fills out, root-arms extend, habitable pods bud along the branches. | +9 districts |
| 3 | Elder | A world-tree. Crown is a canopy kilometres deep, roots are grasping arms, the spinal mount sits in the fork of the trunk. | +18 districts |

`pc_ark` has a fixed planet size of 9 (`common/planet_classes/06_planet_classes_nomads.txt:92`). Tiers add `planet_max_districts_add` 0 / 9 / 18 (`29_nomads_dlc_ships.txt:174,297,428`).

Tier bonuses per class scale exactly as vanilla, because they are the vanilla sizes.

### 3.3 Growth instead of construction

**Seeding a new Ark (D6).**
- An existing Bole or Elder Ark can drop a seed in any system where the empire has a Roothold, once Seed Casting is researched.
- The seed is the Uprooted's own construction site, `arkship_nw_seed`. Its shape is the vanilla Arkship site; its cost is 2000 biomass + 1500 food over 720 days (vanilla build time, `common/inline_scripts/megastructures/arkship.txt:10`).
- The vanilla Arkship sites are hidden from the Uprooted.
- Logistic Ships cannot seed. That is a vanilla rule: "Cannot build new Arkships, which only other Arkships can do" (`localisation/english/nomads_2_l_english.yml:390`).
- The seed grows into a Sapling of the dropping Ark's class.

**Nurture situation (`nw_nurture_ark`, D5).** It replaces the alloy upgrade button: the vanilla upgrade is blocked for the Uprooted.
- **Starting:** a decision on the Ark colony starts it. Starting costs nothing.
- **Upkeep:** food. 15 a month on Slow Growth, 45 on Forced Bloom (design values, balance pending).
- **Progress:** 1 per month.
  - +50% while the Ark shares a system with one of our Rootholds.
  - +100% while the Ark is in a system with a Gaia world or a Tree of Life.
- **Length:** Sapling to Bole is 100 progress, Bole to Elder is 200; Bole to Elder also needs Elder Growth.
- **Pauses** when the food stockpile runs dry.
- **On completion** the Ark moves up one tier through `set_starbase_size`, the same command vanilla uses (`events/game_start.txt:3487-3503`).
- Only one Nurture per Ark.
- **Approaches:**
  - *Slow Growth* (default)
  - *Forced Bloom*: double speed, triple upkeep, Ark gets -25% hull for the duration
  - *Deep Dormancy*: pause, no upkeep, Ark gets +20% hull

Because the model is a tree, tier is also the visual growth stage: each tier is its own stage model in the tree culture (`03-arkship-models.md`).

## 4. Decks

Vanilla Arks have four district types (`common/districts/07_ark_districts.txt`):
- **Habitation Decks** (`district_ark_city`)
- **Reactor Arrays** (`district_ark_generator`)
- **Arkship Deck** (`district_ark_basic`), specialised by zones: Ark Hydroponics, Socio-Ecological Observation, Harvesting Bay, Waystation Trade Hub (`common/zones/05_arkship_zones.txt:428-541`)
- **Ordnance Engineering** (`district_ark_military`)

There is no farming district; food comes from the Ark Hydroponics zone. The Uprooted re-skin these with the Wilderness job line. Each deck costs 50 biomass, paid in pops (D4).

The capital Ark's starting decks are built by the start event (D14), because vanilla's nomad hive start skips Wilderness empires (`common/inline_scripts/game_start/authority_hive_nomad.txt:12-19`).

| Vanilla deck | Uprooted deck | Jobs |
|---|---|---|
| Habitation Decks | **Heartgrove Hollow** | Housing only. Vanilla Wilderness starts with a Heartgrove specialisation, so the name is deliberately the same. |
| Reactor Arrays | **Sunleaf Canopy** | Photosymbiont (energy). Vanilla Wilderness "Solar Mycolungs" line. |
| Arkship Deck | **Fruiting Bough** | The deck itself: housing, specialised by the four zones below. Vanilla has no Ark farming district; this deck takes the old "farming" role through Spore Garden. |
| Arkship Deck: Ark Hydroponics | **Spore Garden** | Pollinosymbiont, the "Nectar Gatherer" (food), plus pop growth (design value, balance pending) |
| Arkship Deck: Harvesting Bay | **Root Bay** | Grubsymbiont (minerals, alloys as Ironwood). Vanilla Wilderness "Orespine" line. |
| Arkship Deck: Socio-Ecological Observation | **Sensory Bloom** | Evosymbiont researchers |
| Arkship Deck: Waystation Trade Hub | **Lattice Node** | Waystation traders, the "Mycelial Comptroller" (Roothold yield) |
| Ordnance Engineering | **Thorn Nursery** | Tamersymbiont, the Wilderness warrior drone (soldiers, naval capacity) |

**Vanilla note on names.** Wilderness has no job keys of its own. Gestalt jobs display Wilderness names whenever `is_wilderness_empire` is true (`common/pop_jobs/04_gestalt_jobs.txt`), so the Ark's jobs show them as soon as the D1 override is in:
- `technician_drone` → Photosymbiont (`:2354`)
- `mining_drone` → Grubsymbiont (`:2090`)
- `agri_drone` → Pollinosymbiont (`:2247`)
- `warrior_drone` → Tamersymbiont (`:2612`)
- researchers → Evosymbiont (`:703`, `:983`, `:1199`)

"Heartgrove", "Orespine" and "Solar Mycolungs" are vanilla Wilderness zone names (`localisation/english/wilderness_l_english.yml:285,300,303`). "Nectar Gatherer" and "Mycelial Comptroller" are the mod's own names.

All jobs are hive jobs, auto-worked by the primary species with no biomass requirement, matching the 4.0.22 Wilderness rework. This also keeps the Ark economy from deadlocking on an empty biomass stockpile.

## 5. The Heartwood Core

Each Ark colony carries a planetary feature, **Heartwood Core** (`d_nw_heartwood_core`), which is the Tree of Life for that Ark.
- **Bonuses:** it copies vanilla `d_tree_of_life_colony`: +10% pop growth (`logistic_growth_mult`), +10% society research, +500 housing, 2 food upkeep (`common/deposits/07_federations_deposits.txt:612-644`).

**Wither.**
- Vanilla Tree of Life dies at 50% devastation, checked on bombardment (`events/origin_events_3.txt:63`; `common/on_actions/00_on_actions.txt:2157-2170`).
- Arks do not take devastation the same way, so the trigger is hull. When the Ark's hull drops below 25%, the Heartwood Core is removed and the colony receives **Withered Heartwood**.
- The Thornwood's Living Hull holds out to 12.5% (D11).
- The checks run after every battle the Ark was in, when it is disabled, and once a year. The hull test is vanilla `has_hp_percentage` (`events/cosmic_storms_events_1.txt:822`).
- **Withered Heartwood** mirrors vanilla `no_lifetree` exactly: −50% building speed, −25% job output, +10% job upkeep, −5 stability (`common/static_modifiers/11_static_modifiers_federations.txt:331-338`). The withered Ark also loses half its pop growth (design value, balance pending).
- Empire-wide, each withered Ark adds a small unity penalty, so a fleet of dying trees is felt.
- **Visible:** the Ark switches to a withered version of its tree model (`03-arkship-models.md`).

**Transplant.**
- Any Ark that still has a Heartwood Core can transplant a sapling into a withered Ark in the same system: decision **Transplant Sapling**, 500 food, 180 days.
- That cost is vanilla `decision_transplant_tree` (`common/decisions/06_federation_decisions.txt:5-36`).
- On completion the withered Ark regains the core.
- An empire with every Ark withered has two sapling sources:
  - **the dormant sapling** the opening chain left in the Rootscar, which can be carried once (`.30`)
  - **Beg a Sapling**, an internal Symbiosis (§8)

This is the loop that makes a Thornwood escort matter, and it is the whole reason the Military tree exists.

## 6. Rootholds, the Lattice, and Seedwings

Vanilla (verified):
- One Waystation per system, which must be surveyed or covered by intel (`common/megastructures/30_nomad_waystation.txt:92-159`).
- Waystations in neighbouring systems form Waylines that propagate module effects along the line (`localisation/english/nomads_2_l_english.yml:378`).
- Logistic Ships build Waystations and collect their stockpiles (`:390`).
- Modules add resource collection, research and pre-FTL contact, logistics hubs and more (`common/starbase_modules/00_waystation_modules.txt`).
- Levels are gated by Improved and Advanced Wayline Logistics (`tech_waystation_2` / `_3`, `common/technology/00_nomads_dlc_tech.txt:520,603`).

The Uprooted keep all of that and change three things:

1. **Cost.** Seeding a Roothold costs 100 biomass, paid in pops, in place of the alloy and food part of the vanilla cost (D4).
2. **Art and names.** Wayline becomes the Mycorrhizal Lattice and the Logistic Ship becomes the Seedwing. Bioship empires already get organic Waystation, Logistic Ship and science ship models from vanilla (`gfx/models/ships/starbases/_starbase_entities.asset`; `gfx/models/ships/biogenesis_01/_biogenesis_01_ships_entities.asset:1036,1345`). The dedicated Roothold model (a mycelial mat around a rock) is an optional culture of its own (`03-arkship-models.md`).
3. **One new module, Spore Nursery.** A Roothold with a Spore Nursery lets a Lumen Ark survey adjacent systems without entering them, using the Spore Survey trait across the Lattice. Unlocked by Lattice Sensing.

Rootholds in systems owned by settled empires give the same opinion malus as vanilla Waystations; that malus was not traced in the install.

## 7. Harvest actions

Vanilla harvest actions are scripted actions in `common/scripted_actions/03_arkships.txt`; their numbers are in `common/scripted_variables/10_scripted_variables_nomads.txt:347-382`. The Uprooted use them unchanged under their own names.

| Vanilla | Uprooted | Change |
|---|---|---|
| Planetary Strip Mining (`arkship_strip_mine`, `:478-666`): 250% efficacy per its tooltip (`localisation/english/nomads_1_l_english.yml:3003`); depletion modifier 600 months = 50 years; Civilian tier 2+, the Planetary Engineering Lab or the Planetary Reprocessor; costs 500 energy; 180 days base | **Rootfeeding** | Same numbers. Fruiting (§3.1) turns a share of the yield into new pops. |
| Planetary Reprocessing (`arkship_reprocess_planet`, `:822-938`): 600% per its tooltip (`nomads_1_l_english.yml:3036`), though the script yields 1200 months = 10× a harvest; depletion 2400 months = 200 years; needs the Planetary Reprocessor (the Taproot); costs 5000 energy; 720 days; target becomes a Shattered World (`nomads_1_l_english.yml:3037`) | **Devouring Root** | Same numbers. The resulting Shattered World gets a Husk deposit. Once Deep Taproot is researched, Seedwings can collect a Husk once for a lump sum of new pops. This is the "Tree of Life explodes" image inverted: the tree is the thing that breaks the planet. |
| Consume World (`arkship_lithoid_consume_world` + `situation_arkship_lithoid_consume_planet`, lithoid Devouring Swarm only; `:1331`, `common/situations/14_nomads_situations.txt:1453`) | not available | Devouring Swarm is excluded by the origin. |

Targets follow the vanilla rule: not a star, not habitable, not artificial or a ringworld, and a harvestable class (`common/scripted_triggers/09_scripted_triggers_nomads.txt:538-564`). The action names are static vanilla localisation, so "Rootfeeding" and "Devouring Root" need scripted or triggered localisation, like Sap.

## 8. Symbioses (contracts)

Vanilla contracts (verified):
- They are missions in `common/missions/missions/02_contracts.txt`.
- Settled empires in contact issue them (`can_issue_contracts_trigger`).
- Nomads accept them unless Inward Perfection or homicidal (`can_accept_contracts_trigger`, `09_scripted_triggers_nomads.txt:405-449`).
- Many have a 1800-day (five-year) acceptance window (`@contract_time_base`).

All vanilla contracts remain available. The Uprooted's three Symbioses (D10):

| Symbiosis | Built from | What the Ark does | Reward |
|---|---|---|---|
| **Seed a Grove** | A new contract modelled on vanilla `contract_colony_startup` (`02_contracts.txt:3320`). Issuer: any settled organic empire with a planet below 60% habitability. | Ark spends 180 days in orbit | Issuer's planet gains a mod sapling deposit. The vanilla Tree of Life deposits' `potential` names a Tree of Life owner (`common/deposits/07_federations_deposits.txt:618-625`), though per the deposits README `potential` only gates random placement (`common/deposits/99_README_DEPOSITS.txt:4`). Uprooted gain 500 new pops and the focus progress (design value, balance pending). |
| **Reforest** | Vanilla `contract_clear_blockers` (`02_contracts.txt:1947-2139`), reflavoured for Uprooted operators through a triggered description | 240 days in orbit (vanilla's own timing applies) | Blockers removed; minerals and opinion as vanilla. |
| **Beg a Sapling** | An internal contract (vanilla has internal contracts, e.g. `contract_find_resources_*_internal`, `02_contracts.txt:3703-3740`), raised when no Ark has an intact Heartwood Core | An Ark travels to the capital of a Tree of Life empire it is in contact with | Ark regains its Heartwood Core. That empire gains a decaying opinion bonus and 300 unity. |

## 9. Event chain: The Uprooting

Hooks and timing are in `docs/research/plan-event-timing.md`.

| Event | Fires | Content |
|---|---|---|
| `nw_uprooted.1` | Day 1 | Opening narration. Player picks the Blight framing (dying star / visitation / overgrowth). Sets `nw_blight_*` flag. The start setup builds out the capital Ark (D14). |
| `nw_uprooted.2` | Right after 1 | **What Endured.** Flavour for the class the player picked in the designer: Heartwood, Lumen or Thornwood. Applies that class's trait and plants the Heartwood. |
| `nw_uprooted.10` | First Roothold seeded (`on_waystation_built`) | Flavour: the hive feels a root touch stone again. 50 new pops on the capital Ark (design value, balance pending). |
| `nw_uprooted.20` | First Heartwood wither | Flavour plus a tutorial pointer to the Transplant decision. |
| `nw_uprooted.21` | First successful transplant | Flavour. +100 unity. |
| `nw_uprooted.30` | An Ark is in the Rootscar system after year 20 (yearly check) | **Memory of the Grove.** Option to collect the dormant sapling (one-time emergency Heartwood source), or leave it for a permanent +5% pop growth empire modifier (design value, balance pending). |
| `nw_uprooted.40` | Mid-game (`on_mid_game_pulse_country`) | Hooks the vanilla Nomad mid-game beats if any apply. Otherwise a reflection event offering 500 unity for 500 new pops (design value, balance pending). |
| `nw_uprooted.100` | After Take Root (vanilla settling) | Ending, see §10. |

## 10. Take Root (settling, D7)

The Uprooted Take Root through vanilla settling:

1. **When.** Once the empire has finished the Adaptability or Versatility tradition (`common/scripted_triggers/09_scripted_triggers_nomads.txt:908-937`), an Ark can settle an unowned **colonizable** world. It is the vanilla Settle action: 250 influence, 360 days base (`common/scripted_actions/03_arkships.txt:1243-1329`). The lore is that the tree roots itself and becomes a planet's heart again.
2. **The empire converts.** The empire stops being nomadic and the Ark's colony moves onto the planet (`common/scripted_effects/nomads_effects.txt:5053-5223`).
   - **Other Arks:** if the empire has more, a hive mind's only vanilla options are to settle and scuttle them, because the subject options are for non-gestalt empires (`events/nomads_events_1.txt:13624-13760`). So the empire ends either way: settled, with no Arks left.
3. **The Heartwood comes down.** The Ark's Heartwood Core moves onto the new world, carried by `copy_deposit_from` as vanilla does for the Knights' deposits (`nomads_effects.txt:5156-5175`).
   - The copy runs when the settle action completes, while the Ark's colony still exists; vanilla destroys that colony when it moves the pops (`nomads_effects.txt:5187-5194`). A withered Ark brings no tree.
   - The world gets the Wilderness capital building the Wilderness start would have given it (`common/inline_scripts/game_start/origin_wilderness.txt:33-36`).
   - Event `.100` closes the story.
4. **The empire becomes Wilderness (decided 2026-10-15).** At settle the empire takes `origin_wilderness` (`set_origin`).
   - From then on vanilla treats it as any Wilderness empire. That includes the 41 raw `origin_wilderness` checks, among them the ten terraform links that are Wilderness colonisation (`common/terraform/02_special_terraform_links.txt:5576-5856`).
   - The mod's content stays on through the `nw_uprooted` country flag, which settling sets; `nw_is_uprooted_empire` accepts the flag as well as the origin.
   - The `started_wet/dry/cold` flag that those terraform links check is re-set from the new capital. At game start it came from the Ark (`events/origin_events_wilderness.txt:78-93`).
   - The Uprooted origin's own modifier (+5% unity, +10% pop growth) is expected to end, and Wilderness's +5 building slots (`common/governments/civics/00_origins.txt:651-653`) to begin.
   - The Tree of Life origin is not taken: it would end the empire's Wilderness status (audit §6 D7).

Dropped with D7:
- the Elder-only rule
- the barren target
- the terraform to a size-18 world (vanilla settling needs a colonizable world and keeps its size)
- the mod's own Take Root decision and its 3000 biomass + 1000 food cost

## 11. Technologies

Four Uprooted-only techs, all Society (Biology), each after a vanilla Nomad tech (D9):

| Tech | Tier | Requires | Effect |
|---|---|---|---|
| Seed Casting | 3 | `tech_arkship_construction` (`common/technology/00_nomads_dlc_tech.txt:3-38`) | Unlocks the Seed (§3.3). |
| Lattice Sensing | 3 | Seed Casting, `tech_waystation_2` "Improved Wayline Logistics" (`:520`) | Unlocks the Spore Nursery Roothold module. |
| Deep Taproot | 4 | Lattice Sensing, `tech_arkship_planetary_refinery` (the Taproot, `:268`) | Seedwings can collect Husks (§7). Under D8 the spinal mounts keep their vanilla techs, so this is Deep Taproot's role now (confirmed 2026-10-15). The Rootscar's starting Husk is exempt: Seedwings can gather it from the start (decided 2026-10-15). |
| Elder Growth | 4 | Seed Casting, `tech_arkship_tier_3` "Arkship Mastery" (`:81-124`) | Unlocks Bole to Elder Nurture. |

Vanilla Nomad techs remain researchable:
- Waystations: `tech_waystation_1/2/3` (`00_nomads_dlc_tech.txt:488,520,603`)
- Ark tiers: `tech_arkship_tier_2/3` (`:40`, `:81`); for the Uprooted these gate Nurture
- Ark classes: `tech_<class>_arkship` (`:126,172,218`)
- Spinal mounts: `:268-427`

## 12. AI

The AI can pick the origin. AI weights:
- Its Ark class is whatever its designer template picked.
- Seed Casting prioritised.
- Nurture started whenever the food stockpile allows. Without it the AI never grows its Arks, because the vanilla upgrade it knows is blocked (D5).
- Transplant whenever a withered Ark shares a system with an intact one.
- Symbioses use vanilla AI acceptance.

The design wants the AI never to Take Root. Vanilla settling is open to any nomad that finishes the traditions, and whether the vanilla AI uses it was not checked.

## 13. Balance targets

- **Year 30:** two Arks (one Bole, one Sapling), 6 to 8 Rootholds, pop growth that covers the Seed and Roothold costs, net food positive with Nurture running. Comparable to a Voidfarers empire of the same date with one Tier 2 Arkship.
- **Year 60:** three Arks including one Elder. First spinal mount.
- **Wither:** should happen to an unescorted Ark roughly once per war. Transplant should be the normal response, not Take Root.
- **Ceiling:** nothing here should out-scale Voidfarers by more than 10% on raw resource output at any date. The biomass economy is the upside; the lack of alloy-funded quick upgrades is the downside.
- **For the balance pass:**
  - Every number marked "design value, balance pending".
  - The Seed's 2000 biomass, which is 2000 pops.
  - The Wilderness authority's doubled empire size from districts (`empire_size_districts_mult = 2`, `common/governments/authorities/00_authorities.txt:1403-1408`), which applies to the Ark's decks.
