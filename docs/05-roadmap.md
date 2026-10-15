# 05 – Roadmap

## Phase 0 – Verify (no game launch needed)

- [ ] Work through every H-item in `04-vanilla-hooks.md` against a local 4.4.x install.
- [ ] Decide between the standalone origin and Plan B (override `origin_wilderness`). Record the decision at the top of `02-mechanics.md` §1.
- [ ] Replace every `<<VANILLA: ...>>` placeholder. `rg -n "<<VANILLA" .` must return nothing.

## Phase 1 – Loads

- [ ] Mod appears in the launcher, empire designer shows the Uprooted origin under Hive Mind.
- [ ] A new game starts with one Ark (vanilla Arkship model is fine at this point), the Rootscar, bioship fleet, Seedwing, Seedwing Scout, and no planets.
- [ ] `error.log` is clean apart from missing-texture warnings.

## Phase 2 – Plays

- [ ] Opening chain (`nw_uprooted.1`, `.2`) fires and the class choice swaps the Ark.
- [ ] Decks and jobs produce; biomass income positive from day 1.
- [ ] Roothold seeding costs biomass. Seedwings build them.
- [ ] Seed Casting tech → new Ark via seed. Nurture situation grows Sapling → Bole → Elder.
- [ ] Heartwood wither triggers at the hull threshold; Transplant restores it.
- [ ] Rootfeeding and Devouring Root work with the vanilla harvest numbers and the Fruiting biomass credit.
- [ ] Take Root converts the last Ark into a Wilderness + Tree of Life planet and the empire into a settled empire without errors.

## Phase 3 – Looks

- [ ] Seed mesh + three tier-1 meshes in-game with placeholder textures.
- [ ] Wither shader parameter wired to hull.
- [ ] Tier 2 and 3 meshes, Roothold, Seedwing.
- [ ] 2D set: origin icon and picture, class icons, deposit icons, four event pictures, three colony backgrounds.

## Phase 4 – Ships

- [ ] Symbioses contracts live for AI settled empires.
- [ ] AI plays the origin without stalling (observe 100 years at ×5).
- [ ] Balance pass against Voidfarers at years 30 / 60 / 100 using the targets in `02-mechanics.md` §13.
- [ ] Workshop page, thumbnail, changelog.

## Stretch

- Arboreal tradition tree (Nomad-only, Wilderness-only) with a finisher that lets a Lumen Ark seed a Roothold at range.
- "World-Tree" ascension perk: Elder Arks gain a fourth growth stage.
- Grand Herald / Crisis interactions: the Uprooted as a target for the Blight's return.
- Non-hive variant for players who want the trees without the gestalt (would need its own economy and is likely a separate mod).
