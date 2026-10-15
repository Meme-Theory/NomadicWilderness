# 03 – Arkship models: three trees, three tiers

This is the art brief for the Tree Arks: three classes × three tiers, plus the Seed and the shared 2D set. It is written for someone building fully new tree models while learning the Stellaris model pipeline. That is the plan under decision D2-B (2026-10-15): the Uprooted keep the vanilla Arkship sizes and classes, and the trees arrive as a **graphical culture**, a named set of models that replaces the vanilla Ark parts on Uprooted Arks only.

Everything in §0-§3 was read from the installed game (v4.5.2); paths are relative to the install. Items marked **[test load]** can only be confirmed by loading the game (`05-roadmap.md`, "First test load"). Tooling questions are open research items (§7).

## 0. How a vanilla Ark is put together

An Ark on screen is assembled from four layers. The Civilian tier-1 Ark is the example; the other classes and tiers follow the same pattern.

1. **Ship size** `civilian_arkship_tier_1` (`common/ship_sizes/29_nomads_dlc_ships.txt:104`)
   - `graphical_culture = { "civilian_arkship" }` (`:108`): the cultures this size accepts.
   - `entity = civilian_arkship_01_stage_1_entity` (`:153`): the **core entity**, an invisible frame that holds the skeleton and the attachment locators.
   - `section_slots` (`:154-158`): slot `"mid"` hangs the hull section on locator `part1`; slots `"1"`, `"2"` hang modules on `module_01`, `module_02`.
     - Tier 2 adds `module_03`/`module_04` (`:274-`).
     - Tier 3 adds `module_05`/`module_06` and slot `"spinal_mount_1"` on locator `spinal_mount` (`:402-411`).
2. **Section templates** (`common/section_templates/nomads_dlc.txt`)
   - **Hull:** `civilian_arkship_tier_1_section` (`:195-237`) fits slot `mid` and names the visible entity `civilian_arkship_01_stage_1_ship_entity` (`:199`). Its `component_slot`s name the locators where hangars and turrets attach (§2).
   - **Modules** (`:1118-1571`) fit slots `1`-`6`.
   - **Spinal mounts** (`:1572-1647`) fit `spinal_mount_1`. They name bare entities such as `construction_yard_01_entity` and `spinal_module_super_refinery_01_entity`.
3. **Entities** (`gfx/models/ships/other/_other_entities.asset`)
   - **Frame entity** `civilian_arkship_01_stage_1_entity` (`:4713`): the frame mesh; states `idle`, `moving`, `death`.
   - **Hull entity** `civilian_arkship_01_stage_1_ship_entity` (`:4730`):
     - the visible mesh
     - `attach = { district_01 = "civilian_arkship_district_living_space_01_entity" }` for every district slot (`:4734-4742`)
     - the states listed in §2
     - particle and sound events on named nodes (`:4745-4830`)
4. **Meshes and animations**
   - `pdxmesh` blocks live in a `.gfx` file inside `objectTypes = { }` (`gfx/models/ships/other/_other_meshes.gfx:1`).
   - `civilian_arkship_01_stage_1_mesh` (`:1640-1666`) points at `gfx/models/ships/other/civilian_arkship_01_stage_1.mesh`. It maps animation ids `death`, `idle`, `working_start`, `working_loop` and `closing` to animation definitions, and gives one `meshsettings` per material with `texture_diffuse`, `texture_normal`, `texture_specular` and a shader (`PdxMeshShip`, or `PdxMeshShipDiffuseEmissive` for the second material).
   - Animations are `animation = { name file = "....anim" }` entries in an `.asset` file (`gfx/models/ships/other/_other_animations.asset:395-418`).

**The culture prefix.** When the game needs an entity for a ship, it first looks for `<ship's graphical culture>_<entity name>`, then the culture's `fallback`, then the bare name (`common/HOW_TO_MAKE_NEW_SHIPS.txt:52-55`, `:61-69`; `common/graphical_culture/00_graphical_culture.txt:2`). Arks use this:
- **Module and spinal parts:** sections name bare entities, and vanilla supplies culture-prefixed ones, e.g. `civilian_arkship_construction_yard_01_entity` (`_other_entities.asset:9833`) and `civilian_arkship_spinal_module_super_refinery_01_entity` (`:9949`). That is how one module looks different on each Ark class.
- **Hull and frame:** the hull section names `civilian_arkship_01_stage_1_ship_entity`. No `civilian_arkship_civilian_arkship_…` entity exists, so vanilla resolves it through the bare-name fallback.
- **Explicit `entity =` on a size:** a size's explicit `entity` is prefixed too. The Waystation size names `starbase_starport_entity` (`common/ship_sizes/00_waystations.txt:5`), and the shipset cultures supply their own `<culture>_starbase_starport_entity` (20 of them in `gfx/models/ships/starbases/_starbase_entities.asset`). **[test load]** That the Ark frame follows the same rule.

**Where the culture comes from.**
- The starting Ark gets `set_graphical_culture = <class>_arkship` in ship scope during empire setup (`common/scripted_effects/01_start_of_game_effects.txt:5204-5218`, called from `events/game_start.txt:1792-1794`).
- New Arks are created with `create_ship = { … graphical_culture = $TYPE$ }` (`common/scripted_effects/nomads_effects.txt:6986-6996`).
- The three Ark cultures are defined at `common/graphical_culture/00_graphical_culture.txt:1742-1836`: not selectable, `fallback = avian_01`, their own lighting.

## 1. What the mod supplies

**Cultures.** A new file `common/graphical_culture/nw_graphical_culture.txt`, copying the `civilian_arkship` block (`00_graphical_culture.txt:1742-1772`):
- `nw_tree_heartwood`, `fallback = civilian_arkship`
- `nw_tree_lumen`, `fallback = science_arkship`
- `nw_tree_thornwood`, `fallback = military_arkship`
- a withered twin of each (`nw_tree_heartwood_withered` …) for the wither visual (§3)

Per-class cultures matter: anything the tree culture does not supply (a module, a turret) falls back to that class's vanilla part rather than to another class's. **[test load]** Whether a fallback chain longer than one step (withered → healthy → vanilla) resolves; if not, each withered culture falls back straight to the vanilla class culture and must define every hull entity itself.

**Required entities: hull and frame, per class and tier.** Same names as vanilla with the culture prefix in front:

| Class | Culture | Frame (core) entity, tier N = 1, 2, 3 | Hull entity, tier N = 1, 2, 3 |
|---|---|---|---|
| Heartwood (Civilian) | `nw_tree_heartwood` | `nw_tree_heartwood_civilian_arkship_01_stage_N_entity` | `nw_tree_heartwood_civilian_arkship_01_stage_N_ship_entity` |
| Lumen (Scientific) | `nw_tree_lumen` | `nw_tree_lumen_science_arkship_01_stage_N_entity` | `nw_tree_lumen_science_arkship_01_stage_N_ship_entity` |
| Thornwood (Military) | `nw_tree_thornwood` | `nw_tree_thornwood_military_arkship_01_stage_N_entity` | `nw_tree_thornwood_military_arkship_01_stage_N_ship_entity` |

Vanilla counterparts:
- Civilian `_other_entities.asset:4713,4730` (stage 1), `:5176,5185` (2), `:5783,5792` (3)
- Military `:6386,6399` / `:6761,6770` / `:7273,7282`
- Science `:8312,8325` / `:8693,8702` / `:9257,9266`

**[test load]** Whether the frame is culture-prefixed. If it is not, the tree hull must keep the vanilla frame's locator positions (§2).

**Optional entities.** Without these, the vanilla class part shows through the fallback. Each name is `<culture>_` + the vanilla bare name:
- **Modules** (any module slot): `construction_yard_01_entity`, `hangar_factory_01_entity`, `living_space_01_entity`, `science_01_entity`, `module_turret_small_01_entity`, `module_turret_medium_01_entity`, `module_turret_large_01_entity`, `module_turret_XL_01_entity` (`common/section_templates/nomads_dlc.txt:1118-1571`).
- **Spinal mounts:** the three re-skins of decision D8, plus three more vanilla spinals.
  - Taproot = `spinal_module_super_refinery_01_entity`
  - Heliotrope Crown = `spinal_module_scanner_01_entity`
  - Heartlance = `spinal_module_devastator_01_entity`
  - the other vanilla spinals: `spinal_module_construction_yard_01_entity`, `spinal_module_stellar_igniter_01_entity`, `spinal_module_mini_catapult_01_entity` (`:1572-1647`)
- **Weapons** (the set vanilla gives `civilian_arkship`): `small/medium/large_kinetic_gun_entity`, `small/medium/large_laser_gun_entity`, `turret_missile_small/medium/large_entity`, `turret_point_defence_entity`. Each turret needs a `turret_muzzle_01` locator (`common/HOW_TO_MAKE_NEW_SHIPS.txt:73`).
- **District pods:** the hull entity names its own attachment, as vanilla's `civilian_arkship_district_living_space_01_entity` does. The tree's version is the habitable pods and fruit (§4).

**The Seed.** The Seed is a megastructure, and a megastructure names its entities directly: `entity = "nw_tree_seed_entity"` and `construction_entity = "nw_tree_seed_entity"`. The vanilla Arkship sites do the same with the tier-1 hull entity (`common/inline_scripts/megastructures/arkship.txt:2-3`, `common/megastructures/29_nomad_arkships.txt:4`).

**Count.**
- **Required:** 9 hull meshes, the Seed, and 9 frame meshes if the frame is culture-prefixed (a frame can be an empty mesh carrying only locators).
- **Withered:** the withered set can reuse the healthy meshes with a second texture set in its own `pdxmesh` entries.
- **Optional:** modules, spinals and pods.

## 2. The engine contract: locators, attach points, states

**Frame mesh locators**, fixed by the size's `section_slots`:

| Tier | Locators |
|---|---|
| 1 | `part1`, `module_01`-`module_02` |
| 2 | `part1`, `module_01`-`module_04` |
| 3 | `part1`, `module_01`-`module_06`, `spinal_mount` |

The vanilla stage-1 frame also names nodes `death_1` and `death_2`, but only in commented-out death events (`_other_entities.asset:4721-4722`).

**Hull mesh locators**, fixed by the section templates' `component_slot`s (`common/section_templates/nomads_dlc.txt:195-1116`):

| Class | Tier 1 | Tier 2 | Tier 3 |
|---|---|---|---|
| Civilian, Scientific | `hangar_01-02`, `pd_turret_01-04` | `hangar_01-04`, `pd_turret_01-08` | `hangar_01-06`, `pd_turret_01-16` |
| Military | `hangar_01-02`, `medium_turret_01-04`, `pd_turret_01-04` | `hangar_01-04`, `medium_turret_01-04`, `large_turret_01-04`, `pd_turret_01-08` | `hangar_01-06`, `large_turret_01-08`, `pd_turret_01-16` |

**District attach points** on the hull: `district_01`-`district_09` (tier 1), `-18` (tier 2), `-27` (tier 3). The counts match the districts each tier allows: the Ark colony is size 9 (`common/planet_classes/06_planet_classes_nomads.txt:92`) and the sizes add 0, 9 and 18 (`planet_max_districts_add`, `29_nomads_dlc_ships.txt:174,297,428`).

**Effect nodes.** The vanilla hull entities fire particles and sounds on:
- `engine_large_*`, `engine_medium_*` (Scientific also `engine_small_01-04`)
- `bot_1..4_exhaust`, `bot_1..4_welding`
- `explosion_locator_*` (32 / 36 / 55 by tier; 37 on the Military tier 2)
- `frontSection_joint`, `ring_02_joint` / `ring_03_joint`

The tree entities define their own events (sap vents instead of engines), so they need only the nodes they reference.

**States.**
- **Hull:** `idle`, `moving` and `death`, plus three action states:
  - `working` (animation `working_start`)
  - `working_looping` (`working_loop`)
  - a closing pass back to `idle` (`closing`)

  The pdxmesh maps these animation ids (`_other_meshes.gfx:1644-1648`). The scripts do not say which Ark actions play them: the harvest and survey actions only name a VFX type (`uses_vfx_type = harvest_effect` / `survey_effect`, `common/scripted_actions/03_arkships.txt:299,630`). **[test load]** Which actions trigger the `working` states.
- **Frame:** `idle` and `death`; the stage-1 frames also have `moving`.
- **No combat state:** vanilla Ark entities have none. **[test load]** Whether the engine sends Arks a combat state at all.

**Textures and shaders.** Per material: diffuse, normal and specular `.dds`, with shader `PdxMeshShip`, or `PdxMeshShipDiffuseEmissive` for an emissive second material (`_other_meshes.gfx:1650-1666`). How the empire colour mask and emissive channel are packed is open research (§7).

## 3. When Uprooted Arks get the culture

| Moment | Scope | How |
|---|---|---|
| Game start | ship (the capital Ark) | Vanilla sets `<class>_arkship` during empire setup (`01_start_of_game_effects.txt:5204-5218`). The mod's start setup (plan P4, run from `nw_uprooted.1` with a one-day delay) then sets the tree culture: from the country, `capital_scope = { ship? = { set_graphical_culture = nw_tree_<class> } }`. `ship?` from a colony is used by vanilla at `events/game_start.txt:1792`. |
| A Seed hatches | country → new fleet | The Seed's `on_build_complete` creates the Ark with `create_ship = { … graphical_culture = nw_tree_<class> }`, as `create_arkship_effect` does (`nomads_effects.txt:6986-6996`). |
| Tier up (Nurture) | ship | `set_starbase_size` swaps the size and so the stage entities. **[test load]** Whether the culture survives the swap. |
| Wither / transplant | ship | `nw_wither_heartwood` sets `nw_tree_<class>_withered`; `nw_plant_heartwood_core` sets `nw_tree_<class>` back. **[test load]** That a mid-game culture change re-renders the Ark. |
| Captured by a non-Uprooted owner | ship | Reset to the vanilla `<class>_arkship` culture (plan P21). |

**Risk, the first thing to test.**
- Each vanilla Ark size accepts only its own culture (`graphical_culture = { "civilian_arkship" }`, `29_nomads_dlc_ships.txt:108`; the other eight at `:227,355,487,609,739,873,994,1123`). A tenth size, `military_arkship_champions_forge` (`:1252-1255`), backs the global design at `common/global_ship_designs/nomad_global_ship_designs.txt:4`; whether a player can own one is unverified, so it gets no tree model.
- If the game ignores or rejects `set_graphical_culture = nw_tree_heartwood` because of that list, the fallback is an override of the nine size blocks that adds the mod cultures to the list. The keys stay vanilla, so D2-B holds, but other Ark mods that edit those sizes would conflict.

## 4. Creative brief

### 4.1 Visual grammar shared by all three

The silhouette reads as **a tree floating root-first through space**: roots forward (they are the grasping, docking, feeding end), trunk as the body, crown aft catching the star. Engines are not nozzles. Thrust comes from **sap vents** along the root collar, visible as a slow blue-green glow, and the crown acts as a solar sail.

Materials, three sets reused across all nine meshes with per-class tints:

| Set | Content | Notes |
|---|---|---|
| Bark | Trunk, root arms, buttresses | High roughness, normal map with deep fissures. Emissive channel carries **sap veins**. |
| Canopy | Leaves, fronds, flowers, thorns | Alpha-tested cards layered over a low-poly canopy shell. |
| Pod | Habitable pods, fruiting bodies, seed cases | Translucent amber with internal lights. These are the "habitable arms" and read as inhabited cities; the district attach points (§2) are where pods grow as the Ark builds districts. |

Empire colour goes into the sap vein emissive and the pod interior light, not the bark, so a red empire gets a tree with red veins rather than a red tree. How vanilla packs the colour mask is open research (§7).

**Withering.** This is the single most important visual in the mod: a player should be able to see at a glance that their tree is dying.
- Vanilla has no hull-percentage visual state; Ark entities have only idle, moving, working and death.
- So the wither is a **model swap**: the withered culture shows the same tree with a desaturated canopy, dimmed veins and dark pods, and the core glow at the trunk fork out (§3).
- The swap happens when the Heartwood withers (25% hull, 12.5% for the Thornwood) and reverses on transplant.
- Leaf fall at the moment of withering can be a particle event on the withered hull's `idle` state.

### 4.2 The three species

#### Heartwood Ark (Civilian)

**Species reference:** banyan / ficus with strangler roots. Wide, low, sprawling.

- **Tier 1 Sapling:** a single trunk, a loose crown, a cluster of aerial roots hanging forward. Four amber pods at the base of the crown. Reads as "a seed that has just opened".
- **Tier 2 Bole:** the aerial roots have thickened into a cage of secondary trunks ahead of the main trunk, forming a hollow that docked ships sit inside. Crown doubles in width. Pods line the main branches like fruit.
- **Tier 3 Elder:** the strangler cage has become a second, inverted crown of roots ahead of the tree. It is big enough that the Taproot (the Planetary Reprocessor re-skin) hangs down its centre like a stalactite at `spinal_mount`. Dozens of pods. The canopy is so wide it reads as a disc from above, a floating forest.
- **Modules** sit at the `module_NN` locators on the outer root cage.
- **Harvest animation** (Rootfeeding / Devouring Root): the root cage opens and the Taproot extends toward the target body. Build it as the `working` → `working_looping` → closing sequence (§2); which actions play it is a **[test load]** item.

#### Lumen Ark (Scientific)

**Species reference:** silver birch crossed with a magnolia. Tall, slender, pale, luminous.

- **Tier 1:** a thin white trunk, a sparse crown of large single flowers that face forward like dishes. One pod.
- **Tier 2:** the trunk splits into three leaders. Each carries flower-dishes; the central leader carries a bud that will become the Heliotrope Crown. Pods bud in the forks.
- **Tier 3:** the central bud has opened into the Heliotrope Crown, the Subspace Imaging Array re-skin at `spinal_mount`. It is a flower several hundred metres across whose petals track the local star. Bioluminescence runs along every branch. At night-side angles the tree is mostly light.
- Survey and scan visual: a faint spore cloud drifts off the crown during the `working` states.
- Modules attach as galls on the three leaders. The sensor-range bonus is the fantasy of the flowers.

#### Thornwood Ark (Military)

**Species reference:** baobab trunk, hawthorn and acacia armament. Heavy, armoured, aggressive.

- **Tier 1:** a squat trunk wrapped in plated bark, a crown that is more thorn than leaf, roots clenched into a fist. It should look dangerous even at tier 1.
- **Tier 2:** the root fist opens into four talons. Thorns along the trunk are the weapon hardpoints: the military sections' `medium_turret_NN` and `large_turret_NN` locators (§2) sit at thorn tips. Burls (armour galls) stud the trunk.
- **Tier 3:** the talons are now arms with their own thorn batteries. The Heartlance, the Devastator Mount re-skin, sits at `spinal_mount` in the trunk fork: a single enormous hardened thorn the length of the tree. The crown is a dense dome of interlocking thorns that reads as a shield.
- Living Hull visual: bark fissures glow brighter.
- Combat flourish (talons flex, thorns extend, veins go bright) is wanted. Whether Arks receive a combat state is a **[test load]** item; if not, it moves to the `working` states for the Devastator.

### 4.3 Tier progression as growth

Because tier equals growth stage, the three tiers of one class must read as the **same individual** at three ages. Practical rules:

- Keep the trunk's silhouette curve identical across tiers; only its diameter and the number of leaders changes.
- Pods present at tier 1 are still present, in the same places, at tier 3. New pods are added, old ones are never moved. District attach points follow the same rule: `district_01`-`09` stay where they are at tiers 2 and 3.
- Nurture completes with `set_starbase_size`, which swaps the whole stage entity; there is no morphing. Keep the tier-N and tier-N+1 root collars in the same place so the swap does not pop. A particle burst on the new stage's first `idle` hides the cut.

### 4.4 The Seed

A new Ark under construction uses its own model, **the Seed**: a closed seed case the size of a tier-1 trunk, veins pulsing, with a single root probing forward. It is the construction-site model for all three classes (§1). On completion the site is removed and the new Ark appears as a tier-1 tree, which reads as the seed cracking open. Class is not visible on the Seed, which fits the fiction that the dropping Ark chooses what the seed becomes.

## 5. Roothold and Seedwing

**Roothold** (Waystation).
- **Where the vanilla models come from:** Waystations use the sizes `starbase_waystation_1..3`, with core entities `starbase_starport_entity`, `starbase_starhold_entity`, `starbase_starfortress_entity` (`common/ship_sizes/00_waystations.txt:5,86,197`) and the matching `_section_entity` names (`common/section_templates/waystation.txt:7,19,31`), all culture-prefixed.
- **No work needed for an organic look:** a bioship empire already gets `biogenesis_01_starbase_*` models (`gfx/models/ships/starbases/_starbase_entities.asset:6655-6735`).
- **For the dedicated mycelial mat:** a `nw_roothold` culture set on each Waystation when it is built (`on_waystation_built`, plan P16), with entities `nw_roothold_starbase_starport_entity` etc. Optional; three meshes, one per level; levels add thread density.

**Seedwing** (Logistic Ship) and **Seedwing Scout** (Science Ship).
- **Where the vanilla models come from:** the Logistic Ship names `constructor_entity` (`29_nomads_dlc_ships.txt:1354`), and the science ship uses its size name.
- **No work needed for an organic look:** a bioship empire already gets `biogenesis_01_constructor_entity` and `biogenesis_01_science_entity` (`gfx/models/ships/biogenesis_01/_biogenesis_01_ships_entities.asset:1036,1345`).
- **For the samara-style winged seed:** a culture swap on those ships. Optional.

## 6. 2D set

| Asset | Where it is used (vanilla key) | Notes |
|---|---|---|
| Origin icon | `icon` on the origin → `gfx/interface/icons/origins/origins_nw_uprooted.dds` (`origins_` as Wilderness uses, `common/governments/civics/00_origins.txt:593`) | The file now exists (untracked). Brief: a tree with roots trailing into stars. |
| Origin picture | sprite `GFX_origin_nw_uprooted` | Now defined in `interface/nw_uprooted.gfx` with two zoom crops, over `gfx/event_pictures/origins/origin_nw_uprooted.dds` (untracked; source in `CREDITS.md`). |
| Ark portrait | `arkship_picture = "arkship_civilian"` on each size (`29_nomads_dlc_ships.txt:117`); the file `gfx/portraits/arkships/ships/arkship_civilian.dds` matches the name | Shared with every nomad: Uprooted-only art needs the size override of §3. |
| Planet view header | `planet_view_header` on each size (`:118`) → sprites at `interface/planet_view.gfx:4353-4364` | Shared, as above. |
| Fleet view background | `hero_ship_fleet_background` (`:110`) → sprite at `interface/fleet_view.gfx:2758` | Shared, as above. |
| Ark blueprint (systems and modules screen) | starbase level `picture` → `interface/icons.gfx:734` | Supports `picture = { trigger = { } picture = … }` (`common/starbase_levels/00_documentation.txt:12-15`). An Uprooted version needs the level override that D5 already plans. |
| Colony icons | `pc_ark` `conditional_icon` by ship size (`common/planet_classes/06_planet_classes_nomads.txt:6-68`) | Uprooted icons need a planet-class override. |
| Heartwood Core / Withered Heartwood icons | deposit `icon`, modifier `icon` | A glowing heartwood ring; the same ring cracked and grey. |
| Event pictures (×4) | sprites `GFX_evt_nw_*` | The Grove, The Uprooting, The Rootscar, Take Root. |
| Spinal mount icons (×3) | module `icon` | The vanilla modules' icons are fixed per module; Uprooted-only icons would need module overrides (D8 keeps vanilla). |

## 7. Pipeline, open research, and what only a load can tell

**File layout**, following vanilla:
- `pdxmesh` entries go in a `.gfx` file under `objectTypes = { }`. In vanilla, 300 `.gfx` files define `pdxmesh` blocks and no `.asset` file does; `.asset` files only name meshes from entities.
- `animation` entries go in an `.asset` file.
- `entity` entries go in an `.asset` file.

The current `gfx/models/arkships/nw_tree/nw_tree_arkships.asset` holds `pdxmesh` entries in an `.asset` without `objectTypes`. It must become a `.gfx` file, and its entity names must change to the §1 table (plan P12).

**Order of work:**
1. Prove the mechanism with a vanilla mesh first. Define `nw_tree_heartwood` and one entity named `nw_tree_heartwood_civilian_arkship_01_stage_1_ship_entity` that points at the vanilla `civilian_arkship_01_stage_1_mesh` with a tinted texture. Set the culture in game. If the tint shows, the pipeline is open.
2. Build the Seed and the three tier-1 hulls with placeholder textures.
3. Wire the withered culture and check the swap in play at 100%, 25% and 12.5% hull.
4. Tiers 2 and 3, then modules and spinals if wanted, then Roothold and Seedwing.

**Open research** (external tools; not researched this pass):
- Which Blender add-on currently imports and exports Stellaris `.mesh` and `.anim` files for 4.x, and whether it keeps locator (empty) names and animation ids intact.
- Whether a Maya/Clausewitz exporter is still distributed, and whether it is needed for animated meshes.
- How vanilla Ark meshes name their locator nodes inside the `.mesh` (the scripts only show the names, §2), and how to inspect a vanilla `.mesh` to copy its frame layout.
- Texture formats the 4.5 renderer accepts (BC7 or not), mip and LOD support, and sensible polygon budgets. The earlier 60k / 35k / 20k triangle targets were guesses.
- How `PdxMeshShip` packs the empire colour mask and emissive in the specular texture, and whether an animated vein pulse is possible without custom shaders.

**[test load] items for art.** Also listed in `05-roadmap.md`:
1. An Ark size accepts a mod culture through `set_graphical_culture`.
2. The culture prefix applies to the Ark hull section and to the frame entity.
3. A fallback chain longer than one step resolves.
4. The culture survives `set_starbase_size`.
5. A mid-game culture change re-renders the Ark.
6. Whether Arks ever enter a combat state, and which actions play the `working` states.
