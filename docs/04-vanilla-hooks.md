# 04 – Vanilla hooks: what must be verified before this loads

This mod was designed without read access to the 4.4 game files (the design environment could reach web summaries of the wiki and dev diaries but not the files themselves). Everything below is therefore a **named assumption with a verification step**, not a confirmed key. Do Phase 0 of `05-roadmap.md` before expecting the scaffold to load.

Files in the scaffold marked `# TEMPLATE` at the top contain placeholders of the form `<<VANILLA: ...>>`. The game will fail to parse them until they are replaced.

## 1. How to verify

Open the Stellaris install, then for each item run a text search in the folder named. `rg` or `grep -rn` across `common/`, `events/`, `gfx/` and `localisation/english/` is enough.

```
rg -n "origin_voidfarers" common/governments/civics/
rg -n "origin_wilderness" common/ events/ | wc -l
rg -ln "arkship" common/megastructures/ common/ship_sizes/ common/planet_classes/
rg -n "tree_of_life" common/deposits/ common/static_modifiers/ common/decisions/
```

## 2. Identity triggers (highest risk)

| ID | Assumption | Verify | Fallback |
|---|---|---|---|
| H1 | Nomadic status is carried by fields on the origin definition (whatever `origin_voidfarers` has that `origin_default` lacks). | Diff `origin_voidfarers` against a settled origin in `common/governments/civics/00_origins.txt`. Copy every extra field into `origin_nw_uprooted`. | If nomadic status comes from a separate empire-designer toggle that filters origins by a flag, copy that flag instead. |
| H2 | Vanilla identifies Wilderness empires through a scripted trigger (expected name `is_wilderness_empire`) in `common/scripted_triggers/`. | `rg -n "is_wilderness" common/scripted_triggers/`. If present, the override in `common/scripted_triggers/zz_nw_overrides.txt` must copy the vanilla body and add `has_origin = origin_nw_uprooted`. | **Plan B:** if most checks are raw `has_origin = origin_wilderness`, drop the standalone origin and instead override `origin_wilderness` itself in a `zz_` file, adding the nomadic fields from H1 and a `flags = { nw_uprooted }` so the mod's content gates on the flag. The event chain and Ark classes work unchanged. |
| H3 | Vanilla identifies Nomadic empires through a scripted trigger (expected `is_nomadic_empire`) and the Ark colony through a scripted trigger or planet-class check. | Same search for `is_nomad`. | Use `has_origin` lists if none exist. |

## 3. Arkship definitions

| ID | Assumption | Verify |
|---|---|---|
| H4 | Arkships are entries in `common/megastructures/` with three tiers per class and an associated `ship_size` in `common/ship_sizes/`. | List the keys. Expected shape: `arkship_civilian_1` … `arkship_military_3` or similar. The scaffold `common/megastructures/nw_tree_arkships.txt` must be rebuilt by copying each vanilla block and changing: key, `entity`, cost block, `upgrade_from`, and the `potential`/`possible` to require `nw_is_uprooted_empire`. |
| H5 | The Ark colony has a planet class (expected `pc_arkship` or per-class variants) and the 4.4.4 change that planet-class modifiers apply to Arkship colonies means our Heartwood Core deposit must be allowed on that class. | `rg -n "arkship" common/planet_classes/`. Put the class name(s) into `nw_deposits.txt` `potential`. |
| H6 | Arkship type bonuses (+5% harvest per tier etc.) are static modifiers applied by the megastructure stage. | Find them in `common/static_modifiers/`. Our class variants reuse them by name so balance stays identical. |
| H7 | Spinal mounts are `component_templates` restricted by `size_restriction` to the tier-3 ship size, and the Planetary Reprocessor / Devastator Mount are two such entries. | `rg -n "reprocessor\|devastator" common/component_templates/`. Copy both into `nw_spinal_mounts.txt` under the new keys. |
| H8 | Harvest actions (Strip Mining, Reprocessing) are scripted in a dedicated folder (expected `common/harvest_actions/` or inside `common/situations/`). | Locate; Rootfeeding and Devouring Root are copies with renamed localisation and the Fruiting biomass hook added. |
| H9 | Arkship colony backgrounds live under `gfx/interface/` keyed by planet class or arkship type. | Locate the vanilla Arkship background to know the file naming for the three tree interiors. |
| H10 | Districts on Arkships use the 4.x district + district-specialisation (zone) system and the Arkship Deck district has a dedicated key. | `rg -n "arkship" common/districts/ common/zones/ common/district_specializations/`. Fill the district keys in `nw_districts.txt`. |

## 4. Tree of Life

| ID | Assumption | Verify |
|---|---|---|
| H11 | Tree of Life is a deposit (`d_tree_of_life` or similar) with a sapling variant and a `Lost Tree of Life` static modifier, plus a transplant decision costing food. | `rg -n "tree_of_life" common/deposits/ common/static_modifiers/ common/decisions/ events/`. Copy the numeric bonuses into `d_nw_heartwood_core` and the malus into `nw_withered_heartwood`. The Seed a Grove contract hands out the vanilla sapling deposit by its real key. |
| H12 | Tree of Life destruction is scripted on devastation in an on_action or event. | Find the event; our wither trigger is a monthly pulse on hull percent instead, so nothing is reused, but the flavour text style should match. |

## 5. Wilderness

| ID | Assumption | Verify |
|---|---|---|
| H13 | Biomass is a strategic resource with key `biomass`, spendable in `cost` blocks. | `rg -n "^biomass" common/strategic_resources/`. |
| H14 | Wilderness jobs are keyed in `common/pop_jobs/` with names such as photosymbiont / grubsymbiont / synapse drone, and are auto-worked by the primary species. | List them; the Uprooted decks reference vanilla job keys rather than defining new ones where possible. |
| H15 | Wilderness ship construction uses food via a cost-swap on bioship sizes, not a global override. | Confirm the Seedwing can reuse the vanilla Wilderness construction-ship and science-ship sizes with new entities. |
| H16 | Vanilla Wilderness has a starting-system initializer and a Wilderness homeworld planet class. | We do not want the homeworld. The origin uses a custom initializer `nw_uprooted_home_system` that places a Shattered World and the starting Ark. The starting Ark placement must match how `origin_voidfarers` spawns its Arkship (see H1). |

## 6. Contracts and situations

| ID | Assumption | Verify |
|---|---|---|
| H17 | Contracts are defined in a `common/contracts/` folder (or similar) with issuer `potential`, acceptor `possible`, a duration, and reward effects. | Locate a Resource contract such as Remote Mineral Assessment and clone its structure for the three Symbioses. |
| H18 | Situations use the 4.x `common/situations/` format with approaches and monthly progress. | The Nurture situation in the scaffold is written to the 3.4+ situation format, which has been stable; confirm nothing changed in 4.4. |

## 7. Known compatibility risks

- **Mixing planets and Arks.** A developer answered on the 4.4 release-notes thread that mixing settled planets with Arkships "might break" because the code assumes they never coexist. The Take Root rule (last Ark only) exists because of this.
- **4.4.4 colony modifiers.** The hotfix notes say scripted modifiers must target colonies rather than planets for Arkship colonies. Every modifier in `nw_static_modifiers.txt` is written as a colony-scope modifier for that reason; verify the scope name used in vanilla.
- **Other Arkship mods.** Anything that overrides `common/ship_sizes/` or the Arkship megastructure keys conflicts. The scaffold only adds new keys and overrides the two identity scripted triggers.
- **Wilderness blockers.** Vanilla adds Wilderness blockers to Wilderness planets conquered by non-Wilderness empires. Arks captured in war should not get these; the capture flow is vanilla Nomad and needs a test.
