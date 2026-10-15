# 03 – Arkship models: three trees, three tiers

This is the art brief for the nine Ark meshes (three classes × three tiers) plus the shared 2D set. It is written so a modeller who has never opened Stellaris can start, and so the entity definitions in `gfx/models/arkships/nw_tree/` line up with it.

## 0. Hard constraints from the game

- **Same footprint as vanilla.** Each tier must match the bounding box and locator set of the vanilla Arkship of the same tier, because Systems, Modules, the spinal mount and docked-ship positions are placed by locator name. Export the vanilla `.mesh` for each tier, note its extents and locators, and build inside them. The vanilla Dev Diary describes the tier-3 spinal mount as sitting "between the habitable arms", so the fork of the trunk is where the Taproot / Heliotrope Crown / Heartlance goes.
- **One entity per class per tier**, named `nw_tree_<class>_0<tier>_entity`, selected through the ship size. Vanilla picks ship entities by `<gfx_culture>_<ship_size>_entity`; Arkships are megastructures and may use a direct `entity =` on the ship size or the megastructure stage instead. Which one applies is item H1 in `04-vanilla-hooks.md`.
- **States.** Every entity needs at least `idle`, `moving`, `death` and, for the Thornwood, `combat`. Megastructure entities typically also need a `construction`/`build` state; for the Ark that is the Seed (see §4).
- **Budget.** Arkships are the largest thing an empire will stare at. Target 60k triangles at LOD0 for tier 3, 35k for tier 2, 20k for tier 1, with two LODs below each. 4k textures for the trunk and canopy sets at tier 3, 2k elsewhere.

## 1. Visual grammar shared by all three

The silhouette reads as **a tree floating root-first through space**: roots forward (they are the grasping, docking, feeding end), trunk as the body, crown aft catching the star. Engines are not nozzles. Thrust comes from **sap vents** along the root collar, visible as a slow blue-green glow, and the crown acts as a solar sail.

Materials, three sets reused across all nine meshes with per-class tints:

| Set | Content | Notes |
|---|---|---|
| Bark | Trunk, root arms, buttresses | PBR, high roughness, normal map with deep fissures. Emissive channel carries **sap veins** that pulse (shader timer, 8-second period). |
| Canopy | Leaves, fronds, flowers, thorns | Alpha-tested cards layered over a low-poly canopy shell. Subsurface tint, wind-sway vertex animation via the standard Stellaris foliage shader, scaled down for scale. |
| Pod | Habitable pods, fruiting bodies, seed cases | Translucent amber with internal lights. These are the "habitable arms" and read as inhabited cities. |

Empire colour goes into the sap vein emissive and the pod interior light, not the bark, so a red empire gets a tree with red veins rather than a red tree.

Damage is shown by **withering**: the shader exposes a `wither` float driven by hull percentage (see `nw_tree_arkships.asset` for the parameter) that desaturates the canopy, dims the veins and crumbles the pod lights. At the Heartwood wither threshold the canopy drops leaves (particle emitter) and the core glow at the trunk fork goes out. This is the single most important visual in the mod: a player should be able to see at a glance that their tree is dying.

## 2. The three species

### Heartwood Ark (Civilian)

**Species reference:** banyan / ficus with strangler roots. Wide, low, sprawling.

- **Tier 1 Sapling:** a single trunk, a loose crown, a cluster of aerial roots hanging forward. Four amber pods at the base of the crown. Reads as "a seed that has just opened".
- **Tier 2 Bole:** the aerial roots have thickened into a cage of secondary trunks ahead of the main trunk, forming a hollow that docked ships sit inside. Crown doubles in width. Pods line the main branches like fruit.
- **Tier 3 Elder:** the strangler cage has become a second, inverted crown of roots ahead of the tree, big enough that the Taproot spinal mount hangs down its centre like a stalactite. Dozens of pods. The canopy is so wide it reads as a disc from above, a floating forest.
- **Modules** attach to the outer root cage. **Systems** are internal and appear as glowing galls on the trunk.
- **Harvest animation** (Rootfeeding / Devouring Root): the root cage opens and the Taproot extends toward the target body. Use the `combat` state slot for this on the Civilian tree since it never otherwise fires.

### Lumen Ark (Scientific)

**Species reference:** silver birch crossed with a magnolia. Tall, slender, pale, luminous.

- **Tier 1:** a thin white trunk, a sparse crown of large single flowers that face forward like dishes. One pod.
- **Tier 2:** the trunk splits into three leaders. Each carries flower-dishes; the central leader carries a bud that will become the Heliotrope Crown. Pods bud in the forks.
- **Tier 3:** the central bud has opened into the Heliotrope Crown, a flower several hundred metres across whose petals track the local star (slow rotation on a locator). Bioluminescence runs along every branch. At night-side angles the tree is mostly light.
- Spore Survey visual: a faint spore cloud drifts off the crown while the Ark surveys (particle emitter tied to the survey state flag if available, else always-on at low intensity).
- Modules attach as galls on the three leaders. The sensor-range bonus is the fantasy of the flowers.

### Thornwood Ark (Military)

**Species reference:** baobab trunk, hawthorn and acacia armament. Heavy, armoured, aggressive.

- **Tier 1:** a squat trunk wrapped in plated bark, a crown that is more thorn than leaf, roots clenched into a fist. It should look dangerous even at tier 1, which is the vanilla art team's stated goal for the Military Arkship.
- **Tier 2:** the root fist opens into four talons. Thorns along the trunk are weapon hardpoints: the vanilla "extra auto-filled weapon slots" map to thorn clusters with muzzle locators at the tips. Burls (armour galls) stud the trunk.
- **Tier 3:** the talons are now arms with their own thorn batteries. The Heartlance sits in the trunk fork, a single enormous hardened thorn the length of the tree. The crown is a dense dome of interlocking thorns that reads as a shield.
- Living Hull visual: bark fissures glow brighter while regenerating.
- `combat` state: talons flex, thorns extend, veins go bright.

## 3. Tier progression as growth

Because tier equals growth stage, the three tiers of one class must read as the **same individual** at three ages. Practical rules:

- Keep the trunk's silhouette curve identical across tiers; only its diameter and the number of leaders changes.
- Pods present at tier 1 are still present, in the same places, at tier 3. New pods are added, old ones are never moved.
- The Nurture situation plays a short growth transition: the mod fires a particle burst and swaps the entity, there is no morphing. Keep the tier-N and tier-N+1 root collars in the same place so the swap does not pop.

## 4. The Seed

A new Ark under construction uses a tenth mesh, **the Seed**: a closed seed case the size of a tier-1 trunk, veins pulsing, with a single root probing forward. It is the `construction` state for all three classes. On completion it cracks open (particle + entity swap to tier 1). Class is not visible on the Seed, which fits the fiction that the dropping Ark chooses what the seed becomes.

## 5. Roothold and Seedwing

- **Roothold** (Waystation): a mycelial mat wrapped around a rock, white threads with amber nodes, one glowing node per installed module. Levels add thread density. Three meshes (one per level).
- **Seedwing** (Logistic Ship): a winged seed, samara-style, two-metre-thick "wing" membranes and a bulbous body. Reuses the vanilla Wilderness bioship material set.
- **Seedwing Scout** (Science Ship): same body, longer wings, a flower-dish at the nose.

## 6. 2D set

| Asset | Path | Notes |
|---|---|---|
| Origin icon | `gfx/interface/icons/origins/origins_nw_uprooted.dds` | A tree with roots trailing into stars. |
| Origin picture | `gfx/interface/origins/nw_uprooted.dds` | Same framing as vanilla origin pictures. |
| Ark class icons (×3) | `gfx/interface/icons/nw_ark_*.dds` | Reuse the three silhouettes. |
| Colony background (×3 classes) | per vanilla Arkship colony view path, item H9 | Interior of the trunk: amber light, pods, hive drones. |
| Heartwood Core / Withered Heartwood deposit icons | `gfx/interface/icons/deposits/` | A glowing heartwood ring; the same ring cracked and grey. |
| Event pictures (×4) | `gfx/event_pictures/nw_*.dds` | The Grove, The Uprooting, The Rootscar, Take Root. |
| Spinal mount icons (×3) | `gfx/interface/icons/components/` | Taproot, Heliotrope Crown, Heartlance. |

## 7. Pipeline

1. Model in Blender. Scale reference: import the vanilla tier mesh via the community `.mesh` importer, build to it, keep the vanilla locator empties and rename nothing.
2. Export through the Clausewitz Maya exporter or the community Blender `.mesh` exporter. One `.mesh` per tier per class, plus the Seed, the three Rootholds and two Seedwings. Fourteen meshes total.
3. Textures as DDS (BC7 for colour, BC5 for normals). Three material sets per class, shared across tiers.
4. Register meshes in `gfx/models/arkships/nw_tree/nw_tree_arkships.asset` (pdxmesh entries) and entities in `nw_tree_arkships_entities.asset`. Both files exist as scaffolds with every name already in place.
5. Test with the `-debug_mode` entity viewer. Check wither at 100%, 50%, 25% hull.
