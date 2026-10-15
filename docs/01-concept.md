# 01 – Concept

## The fantasy

Vanilla Stellaris has three ideas that were clearly meant to meet and never did:

- **Wilderness** (BioGenesis): you are not a species on a planet. You *are* the planet, a mutualistic biosphere with a single mind. You grow instead of build, spend biomass instead of minerals, and colonise by throwing spores at other worlds.
- **Tree of Life** (Plantoids): a hive mind whose well-being is bound to one enormous tree. Lose the tree to devastation and the hive sickens. Every colony ship carries a sapling.
- **Nomads** (4.4): your capital is an Arkship, a mobile megastructure that is a ship and a colony at the same time. You never claim systems; you leave Waystations behind and keep moving.

The Uprooted is the fusion. The Wilderness grew a Tree of Life as its focal organism. When the homeworld failed, the Tree did not die. It hollowed its own trunk, drew the whole biosphere inside, and pushed its roots through the mantle until the planet cracked. What floated free of the debris was the first **Ark**: a tree the size of a moon, alive, awake, and homeless.

You play that tree.

## Lore sketch (for the opening event chain)

1. **The Grove.** A Wilderness world, old even by hive standards, whose entire surface is one organism. At its heart stands the Heartwood, the first and greatest Tree of Life. The hive does not live *near* the tree. The hive *is* the tree's root network, extended across a planet.
2. **The Blight.** Something killed the world. The mod leaves the cause deliberately vague in the opening text and lets the player pick one of three framings in the first choice event (dying star, a Grand Herald-style visitation, the hive's own overgrowth exhausting the crust). The choice only sets a flag that colours later flavour text.
3. **The Uprooting.** The Heartwood spent a century growing inward: lignin pressure vessels, sap bladders, mycelial reactors, a crown of sunleaf large enough to catch starlight. Then it pulled. The homeworld is still in the starting system as a **Shattered World** called the Rootscar, and its one remaining deposit is the Husk of the Old Grove.
4. **The Drift.** Everything the hive learns from here is learned on the move. New Arks are not built in shipyards. They are **seeds**: dropped from an existing Ark, nursed with biomass, and grown through three stages of life.

## Design pillars

1. **Grow, don't build.** Every Nomad verb gets a Wilderness noun. Arks are grown from seeds with biomass and food, not alloys. Waystations are Rootholds, spore-seeded mats of mycelium on asteroids and moons. Harvest actions are a tree feeding.
2. **The tree can die.** Tree of Life's defining tension (devastation kills the tree, the hive suffers until a sapling is replanted) survives the move to space. An Ark whose hull drops far enough sees its Heartwood core wither. Another Ark can transplant a sapling. A Military Ark exists specifically to keep that from happening.
3. **Three trees, three temperaments.** The Civilian, Science and Military Arkship classes become three species of world-tree with distinct silhouettes, not three paint jobs. See `03-arkship-models.md`.
4. **Nomad rules, Wilderness economy.** The mod does not re-implement Nomads. It rides the vanilla Nomad empire logic (Arkships, Waystations, Waylines, Logistic Ships, Contracts, Operational Reserves) and swaps the cost model, the jobs, the art and the flavour. Where vanilla Nomads spend alloys and minerals, the Uprooted spend biomass and food.
5. **An ending you choose.** Vanilla Nomads can settle. The Uprooted can **Take Root**: an Ark lands on a habitable world, becomes a Tree of Life on a freshly terraformed Wilderness planet, and the empire converts into a settled Wilderness empire with the Tree of Life bonuses. The Ark is consumed in the process. It is the Forever Cruise "Planetfall" beat, reframed as a homecoming.

## Why Tree of Life is the right skin

| Tree of Life element | Uprooted translation |
|---|---|
| Vast tree the hive lives in symbiosis with | The Ark itself |
| Tree is a planetary feature that can be destroyed at 50% devastation | Heartwood Core feature on the Ark colony, withers when the Ark's hull is badly damaged |
| Lost Tree of Life planet modifier | Withered Heartwood colony modifier |
| Colony ships carry a sapling | New Arks are grown from seeds; Rootholds are seeded with spores |
| Transplant Tree decision (food + time) | Transplant Sapling decision between Arks (food + time) |
| Devouring Swarm is excluded | Devouring Swarm and Terravore are excluded, but a milder "Devouring Root" harvest action exists for dead worlds |

## Scope boundaries

In scope: one origin, three Ark classes with tree models and three tiers each, Wilderness-flavoured Roothold and Seedwing variants, a biomass-based Ark growth system, the Heartwood wither/transplant loop, an opening event chain, a Take Root ending, localisation.

Out of scope for 1.0: new tradition trees, new ascension perks, AI personality tuning beyond "can the AI pick it", new Roothold modules, multiplayer balance passes. These are listed as stretch goals in `05-roadmap.md`.
