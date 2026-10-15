# 02 – Mechanics

Numbers in this document are design targets. Where a value mirrors vanilla, the vanilla source is named so it can be re-checked after each patch. Script keys live in `04-vanilla-hooks.md`.

## 1. The origin: Uprooted

| Field | Value |
|---|---|
| Key | `origin_nw_uprooted` |
| Authority | Hive Mind only |
| Excluded civics | Devouring Swarm, Terravore (same rule as Tree of Life), plus any civic vanilla Nomads already exclude |
| DLC | Nomads + BioGenesis required to pick it. Plantoids is checked at runtime for sapling art and is not required. |
| Empire type | Nomadic. The origin carries the same nomadic fields vanilla gives Voidfarers. |
| Starting colony | None. One Ark in the home system, plus the Rootscar (Shattered World). |
| Starting fleet | Vanilla Wilderness bioship starting fleet (corvette equivalents) plus one Seedwing (Logistic Ship) and one Seedwing Scout (Science Ship). |
| Starting Ark | Chosen in the first event, see §9. Default is the Heartwood Ark (Civilian) for AI. |

**Why a standalone origin rather than a flag on vanilla Wilderness.** Two reasons. First, it works whether the Nomad empire type is a property of the origin (Voidfarers makes you nomadic) or a toggle that filters origins. Second, it gives a clean place for the Uprooted-only start (no homeworld, Rootscar, seed system). The cost is that vanilla scripts which test `has_origin = origin_wilderness` directly will not see us. The mitigation is in `04-vanilla-hooks.md` §2: override the Wilderness identity scripted trigger so it also matches `origin_nw_uprooted`. If a vanilla behaviour turns out to be hard-coded to the origin key, the fallback is Plan B in that document (override `origin_wilderness` itself to add the nomadic fields).

## 2. Economy translation

Vanilla Nomads run on alloys, minerals, energy and Operational Reserves. Vanilla Wilderness runs on biomass, food and energy. The Uprooted take the Wilderness side of every fork.

| Need | Vanilla Nomads | Uprooted |
|---|---|---|
| New Arkship | Megastructure build with alloys via an existing Arkship | **Seed** stage grown from an existing Ark: 2000 biomass + 1500 food, 720 days. See §3.3. |
| Tier upgrade | Alloys + time | Biomass over time via a **Nurture** situation. No alloy cost. |
| Deck districts | Minerals | 50 biomass each (vanilla Wilderness district cost, 4.0.3 patch notes) |
| Deck buildings | Minerals / alloys | Biomass, with the same tiering as vanilla Wilderness buildings |
| Ship hulls | Alloys | Food (vanilla Wilderness bioships) |
| Ark Systems and Modules | Alloys | Alloys. Kept on purpose: the Orespine job line still outputs alloys ("Ironwood") and this keeps the Ark component UI untouched. |
| Operational Reserves | Reserves from harvesting | Same resource, renamed **Sap** in localisation only. No mechanical change. |
| Waystation build | Logistic Ship + alloys | Seedwing + 100 biomass (spore seeding). Alloy cost removed via a cost modifier on the Roothold variant. |

Biomass income on an Ark comes from the same place it does on a Wilderness planet: pop growth. Arks start with 1000 pops (vanilla Arkship wiki) and grow, so the loop works without a planet. The Heartwood Core feature (§5) adds flat biomass so a freshly seeded Sapling Ark is not starved.

**Failure state.** Vanilla Nomads treat Operational Reserves at or near zero as a failure state. The Uprooted inherit that unchanged. The Wilderness-specific failure state is biomass starvation: at negative biomass stockpile the Ark stops growing and the Nurture situation pauses. No death spiral beyond vanilla.

## 3. The three Tree Arks

Every Ark is one of three species of world-tree. They occupy the vanilla Civilian, Scientific and Military Arkship slots and keep the vanilla per-tier bonuses so balance stays close to Voidfarers. Each adds one Wilderness-specific trait.

### 3.1 Class table

| | Heartwood Ark (Civilian) | Lumen Ark (Scientific) | Thornwood Ark (Military) |
|---|---|---|---|
| Vanilla bonus kept | +2500 Operational Reserves capacity, +5% harvest yield per tier | +1 sensor and hyperlane detection range, +5% survey speed per tier | +5% fire rate and weapon range per tier, extra auto-filled weapon slots, cannot be bombarded |
| Wilderness trait | **Fruiting**: 10% of harvested minerals and energy are also credited as biomass | **Spore Survey**: the Ark can survey the system it is in at half speed, as if it carried a science officer | **Living Hull**: +2% hull regen per month out of combat, +50% Heartwood wither threshold (see §5) |
| Tier 3 spinal mount | **Taproot** (the Planetary Reprocessor equivalent, enables Devouring Root) | **Heliotrope Crown** (sensor array + a Starbase-style aura: +10% sublight speed to the fleet) | **Heartlance** (Devastator Mount equivalent) |
| Role | Economy, Roothold seeding, biomass engine | Exploration, Symbioses with research empires | Escort, wither insurance, bounty-fleet answer |

### 3.2 Tiers

| Tier | Name | Fantasy | Colony size (vanilla Arkship range 9 → 18) |
|---|---|---|---|
| 1 | Sapling | A seed that has cracked open. Thin trunk, sparse crown, roots still coiled. | 9 |
| 2 | Bole | A full tree. Crown fills out, root-arms extend, habitable pods bud along the branches. | 13 |
| 3 | Elder | A world-tree. Crown is a canopy kilometres deep, roots are grasping arms, the spinal mount sits in the fork of the trunk. | 18 |

Tier bonuses per class scale exactly as vanilla (the "per tier" lines above).

### 3.3 Growth instead of construction

**Seeding a new Ark.** An existing Bole or Elder Ark can drop a seed in any system where the empire has a Roothold. The seed is the vanilla "build Arkship" megastructure path with the cost swapped to 2000 biomass + 1500 food and the build time to 720 days. Logistic Ships cannot seed (vanilla rule: Logistic Ships cannot build Arkships). The seed inherits the dropping Ark's class unless the player chooses otherwise in the confirmation event.

**Nurture situation (`nw_nurture_ark`).** Replaces the alloy upgrade button. Starting it costs nothing. While active it drains 15 biomass per month from the empire stockpile and adds 1 progress per month, with +50% progress while the Ark is docked at a Roothold and +100% while the Ark is in a system with a Gaia world or a Tree of Life. Sapling to Bole is 100 progress, Bole to Elder is 200. Progress pauses at negative biomass. Only one Nurture per Ark. Approaches: *Slow Growth* (default), *Forced Bloom* (double speed, triple biomass drain, Ark gets -25% hull for the duration), *Deep Dormancy* (pause, no drain, Ark gets +20% hull).

Because the model is a tree, tier is also the visual growth stage, which is what the art brief in `03-arkship-models.md` builds around.

## 4. Decks

Vanilla Arkships carry a city district, a generator district, and either a farming district or a special housing district, plus the Arkship Deck district with Arkship-only specialisations (Ark Hydroponics, Socio-Ecological Observation, Waystation Trade Hub, Harvesting Bay) and Ordnance Engineering in place of mining. The Uprooted re-skin these with the Wilderness job line.

| Vanilla deck | Uprooted deck | Jobs |
|---|---|---|
| City / Habitation Deck | **Heartgrove Hollow** | Housing only. Vanilla Wilderness starts with a Heartgrove specialisation, so the name is deliberately the same. |
| Generator | **Sunleaf Canopy** | Photosymbiont (energy). Vanilla Wilderness "Solar Mycolungs" line. |
| Farming | **Fruiting Bough** | Nectar Gatherer (food). |
| Arkship Deck: Harvesting Bay | **Root Bay** | Grubsymbiont (minerals, alloys as Ironwood). Vanilla Wilderness "Orespine" line. |
| Arkship Deck: Ark Hydroponics | **Spore Garden** | Nectar Gatherer, plus +biomass |
| Arkship Deck: Socio-Ecological Observation | **Sensory Bloom** | Synapse Drone (research) |
| Arkship Deck: Waystation Trade Hub | **Lattice Node** | Mycelial Comptroller (Roothold yield) |
| Ordnance Engineering | **Thorn Nursery** | Warrior Drone (soldiers, naval capacity) |

All jobs are hive jobs. All are auto-worked by the primary species with no biomass requirement, matching the 4.0.22 Wilderness rework so the Ark economy never deadlocks on an empty biomass stockpile.

## 5. The Heartwood Core

Each Ark colony carries a planetary feature, **Heartwood Core** (`d_nw_heartwood_core`), which is the Tree of Life for that Ark. It gives the Tree of Life bonus set (pop growth, sociology research, housing; exact vanilla values to be copied at implementation, see `04-vanilla-hooks.md` §4) plus +5 biomass per month.

**Wither.** Vanilla Tree of Life dies at 50% devastation. Arks do not take devastation the same way, so the trigger is hull: when the Ark's hull drops below 25% (37.5% for Thornwood thanks to Living Hull), the Heartwood Core is removed and the colony receives **Withered Heartwood**, a modifier that mirrors Lost Tree of Life (reduced output, reduced build speed, a stability penalty, biomass income halved on that Ark). Empire-wide, each withered Ark adds a small unity penalty, so a fleet of dying trees is felt.

**Transplant.** Any Ark that still has a Heartwood Core can transplant a sapling into a withered Ark in the same system: decision **Transplant Sapling**, 500 food, 180 days (vanilla Tree of Life transplant cost, to be re-verified). On completion the withered Ark regains the core. An empire with every Ark withered has no sapling source and must find one: the Symbioses system (§8) includes a "Beg a Sapling" contract an allied Tree of Life empire can issue, and the opening chain seeds one dormant sapling in the Rootscar for exactly this emergency.

This is the loop that makes a Thornwood escort matter, and it is the whole reason the Military tree exists.

## 6. Rootholds, the Lattice, and Seedwings

Vanilla: one Waystation per surveyed system, adjacent Waystations auto-form Waylines that harvest resources from the empire's activity in the system, Logistic Ships build and collect, Waystation modules unlock deposit collection, pre-FTL observation, megastructure construction, with levels gated by Improved and Advanced Wayline Logistics.

The Uprooted keep all of that and change three things:

1. **Cost.** Seeding a Roothold costs 100 biomass instead of alloys.
2. **Art and names.** Roothold model is a mycelial mat wrapped around the largest rocky body in the system. Wayline becomes the Mycorrhizal Lattice. Logistic Ship becomes the Seedwing, a bioship.
3. **One new module, Spore Nursery.** A Roothold with a Spore Nursery lets a Lumen Ark survey adjacent systems without entering them (uses the Spore Survey trait across the Lattice). Unlocked by the Uprooted-only tech Lattice Sensing.

Rootholds in systems owned by settled empires give the same opinion malus as vanilla Waystations. No change.

## 7. Harvest actions

| Vanilla | Uprooted | Change |
|---|---|---|
| Planetary Strip Mining (250% efficiency, 50-year depletion, Tier 2+ Civilian or Planetary Engineering Bay module, 500 Operational Reserves, 180 days) | **Rootfeeding** | Same numbers. Fruiting (§3.1) credits 10% of the yield as biomass. |
| Planetary Reprocessing (600%, 200-year depletion, Planetary Reprocessor spinal mount, target becomes a Shattered World) | **Devouring Root** | Same numbers, requires the Taproot spinal mount. The resulting Shattered World gets a Husk deposit that Seedwings can collect once for a biomass lump sum. This is the "Tree of Life explodes" image inverted: the tree is the thing that breaks the planet. |
| Consume World (Terravore situation on habitable planets) | not available | Terravore is excluded by the origin. |

Targets follow the vanilla rule: not a star, not a habitable world.

## 8. Symbioses (contracts)

Vanilla contracts: issued by settled empires after first contact, one per Arkship, five-year expiry, progress toward empire focuses on completion, Inward Perfection empires cannot accept. All vanilla contracts remain available. Three Uprooted-only contracts are added to the pool settled empires draw from:

| Symbiosis | Issuer condition | What the Ark does | Reward |
|---|---|---|---|
| **Seed a Grove** | Any settled organic empire with a planet below 60% habitability | Ark spends 180 days in orbit | Issuer's planet gains a Tree of Life Sapling feature (the vanilla sapling, if Plantoids is present; a generic +habitability feature otherwise). Uprooted gain 500 biomass and the focus progress. |
| **Reforest** | Any settled empire with a planet carrying 3+ blockers | 240 days in orbit | Blockers removed. Uprooted gain minerals and focus progress. |
| **Beg a Sapling** | A Tree of Life empire (vanilla origin) toward an Uprooted empire with no intact Heartwood Core | Ark travels to the issuer's capital | Ark regains its Heartwood Core. Issuer gains a decaying opinion bonus and 300 unity. |

## 9. Event chain: The Uprooting

| Event | Fires | Content |
|---|---|---|
| `nw_uprooted.1` | Day 1 | Opening narration. Player picks the Blight framing (dying star / visitation / overgrowth). Sets `nw_blight_*` flag. |
| `nw_uprooted.2` | Right after 1 | **Which aspect of the Grove endured?** Picks the starting Ark class. Heartwood (default), Lumen, or Thornwood. Replaces the vanilla Arkship with the chosen Tree Ark. |
| `nw_uprooted.10` | First Roothold seeded | Flavour: the hive feels a root touch stone again. +50 biomass. |
| `nw_uprooted.20` | First Heartwood wither | Flavour plus a tutorial pointer to the Transplant decision. |
| `nw_uprooted.21` | First successful transplant | Flavour. +100 unity. |
| `nw_uprooted.30` | An Ark enters the Rootscar system after year 20 | **Memory of the Grove.** Option to collect the dormant sapling (one-time emergency Heartwood source) or leave it, which grants a permanent +5% biomass from pop growth empire modifier. |
| `nw_uprooted.40` | Mid-game year reached | Hooks the vanilla Nomad mid-game beats if any apply. Otherwise a reflection event offering a unity-for-biomass trade. |
| `nw_uprooted.100` | Take Root decision completes | Ending, see §10. |

## 10. Take Root (settling)

Vanilla Nomads can order an Arkship to settle an uninhabited planet, personally or as a vassal. The Uprooted version:

1. Decision **Take Root** on an Elder Ark in orbit of an uninhabitable or barren rocky world (not gas giant, not a star, not already habitable). Costs 3000 biomass, 1000 food, 2 years.
2. On completion the planet becomes a size-18 Wilderness world with the vanilla Tree of Life feature on it, the Ark's pops move down, and the Ark megastructure is removed. The lore is that the tree rooted itself and became a planet's heart again.
3. If the empire has other Arks, it continues as a nomadic empire with one settled planet, which the Paradox developers warned mixes two code paths that assume they never coexist. The mod therefore blocks Take Root unless it is the empire's **last** Ark, in which case the empire converts to a settled Wilderness empire with Tree of Life: the vanilla settle flow handles the empire-type conversion, and the mod fires `nw_uprooted.100` afterwards to swap the origin-specific modifiers.
4. Vassalising the new world instead is allowed via the vanilla path and produces a settled Wilderness/Tree of Life subject. The parent stays nomadic.

## 11. Technologies

Four Uprooted-only techs, all Society (Biology), weighted so they appear in the first 60 years:

| Tech | Tier | Effect |
|---|---|---|
| Seed Casting | 1 | Enables seeding new Arks (the megastructure build). |
| Lattice Sensing | 2 | Unlocks the Spore Nursery Roothold module. |
| Deep Taproot | 3 | Unlocks the Taproot and Heliotrope Crown spinal mounts (Heartlance is unlocked by the vanilla military spinal mount tech, re-pointed). |
| Elder Growth | 3 | Unlocks Bole to Elder nurture. Mirrors whatever vanilla tech gates Arkship tier 3. |

Vanilla Nomad techs (Wayline Logistics line, Arkship tiers, modules) remain researchable and are the gates the above hang from.

## 12. AI

The AI can pick the origin. AI weights: Heartwood Ark always for the starting choice, Seed Casting prioritised, Nurture started whenever biomass stockpile exceeds 500, Transplant whenever a withered Ark shares a system with an intact one, Take Root never (the AI stays nomadic). Symbioses use vanilla AI acceptance.

## 13. Balance targets

- Year 30: two Arks (one Bole, one Sapling), 6 to 8 Rootholds, biomass income +20/month, net food positive. Comparable to a Voidfarers empire of the same date with one Tier 2 Arkship.
- Year 60: three Arks including one Elder. First spinal mount.
- Wither should happen to an unescorted Ark roughly once per war. Transplant should be the normal response, not Take Root.
- Nothing here should out-scale Voidfarers by more than 10% on raw resource output at any date. The biomass economy is the upside; the lack of alloy-funded quick upgrades is the downside.
