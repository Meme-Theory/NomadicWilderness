# 05 – Roadmap

## Phase 0 – Verify (no game launch needed)

- [x] Work through every H-item in `04-vanilla-hooks.md` against a local install. Done against v4.5.2 on 2026-10-15. Verdicts and H19–H38 are in `04-vanilla-hooks.md`; evidence is in `docs/research/install-audit-4.5.2.md`.
- [x] Move `descriptor.mod` to `supported_version="4.5.*"` and fix the wrong keys and syntax the audit found in the real-script files.
- [x] Decide between the standalone origin and Plan B (override `origin_wilderness`). Decided 2026-10-15: the standalone origin, with `zz_` overrides of `is_wilderness_empire` and two raw checks (D1-A). Recorded at the top of `02-mechanics.md` §1 and in the audit §6.
- [x] Settle the other audit decisions that block scripting. D2–D14 were decided on 2026-10-15 (audit §6), and the three calls they left open the same day (audit §10): at settle the empire takes `origin_wilderness` (D7-W), the Heartwood's flat growth is dropped, and Deep Taproot gates Husk collection.
- [ ] Replace every `<<VANILLA: ...>>` placeholder. `rg -n "<<VANILLA" common events localisation gfx` must return nothing. Search those folders, not `.`: the docs quote the marker. 7 remain as of 2026-10-15 (32 before the audit, 20 after it, 8 after the decision pass); each names the plan item that resolves it.
- [ ] Rebuild every file headed `# TEMPLATE` from its vanilla source, or delete it where a decision made it obsolete: `nw_tree_arkship_sizes.txt` (D2-B, after the first test load) and `nw_spinal_mounts.txt` (D8). Order in `docs/research/plan-missing-scripts.md`.

## Phase 1 – Loads

The decided start state (D2-B, D3-B, D12-B, D14):

- [ ] The mod appears in the launcher, and the empire designer offers the Uprooted origin to a Hive Mind with the Nomadic toggle on (H19).
- [ ] A new game starts with one tier-1 Ark of the class picked in the designer (D3-B). The vanilla Ark model is fine at this point; the tree culture comes in Phase 3.
- [ ] The capital Ark has its starting decks built (D14) and the Heartwood Core planted. `nw_uprooted.2` shows only the option for the Ark's class and applies its trait.
- [ ] The home system has the Rootscar. The empire owns no planets, one Seedwing Scout (science ship), one Seedwing (Logistic Ship) and no war fleet (D12-B).
- [ ] Biomass shows the Wilderness pop count after the first month (H21), and the Ark's jobs show their Wilderness names.
- [ ] `error.log` is clean apart from missing-texture warnings.

### First test load

The questions below can only be answered in game. Each names what depends on it.

1. **`zz_` override.** A same-key scripted trigger in `common/scripted_triggers/zz_nw_overrides.txt` replaces the vanilla one. The test: an Uprooted empire gets the biomass sync and the Wilderness job names, a vanilla Wilderness empire is unchanged, and `error.log` shows no duplicate-key error. (D1-A, plan P1)
2. **on_action ordering.** Whether the mod's `on_game_start_country` entry runs before or after vanilla `nomads.20` and `wilderness.100`. (P4, P5, `plan-event-timing.md` §3)
3. **Culture on an Ark.** Three checks, run with the vanilla-mesh proof in `03-arkship-models.md` §7:
   - `set_graphical_culture` with a mod culture works on an Ark whose size lists only its vanilla culture (H35);
   - the culture prefix applies to the Ark hull section and to the core frame entity (H36);
   - a two-step fallback (withered → tree → vanilla) resolves.

   (D2-B, P12)
4. **`set_starbase_size` within a class.** It moves an Ark from tier 1 to tier 2 without errors, and the Ark keeps its graphical culture. (D5-A, P9)
5. **`upgrade_possible` scope.** Which scope the trigger runs in on Ark starbase levels, so the level override (a same-path copy of `06_arkship_levels.txt`) can test the owner. (D5-A, P9)
6. *(Parked with P19; skip until Take Root is picked up.)* **`on_capital_changed` on settle.** Whether it fires when the vanilla settle moves the capital from the Ark to the planet. If not, `.100` uses the `.101` fallback. (D7-A, P19)
7. **Mid-game culture change.** A culture swap re-renders the Ark; the wither visual depends on it. (P7, P12)
8. **Ark animation states.** Whether Arks ever enter a combat state, and which actions play the `working` states. The Thornwood's combat flourish and the harvest animation depend on it. (`03-arkship-models.md` §2)
9. *(Parked with P19; skip until Take Root is picked up.)* **`set_origin` on a live Uprooted empire.** In a running game, run `set_origin = origin_wilderness` on an Uprooted empire, then check:
   - the country flags `uses_biomass`, `virtual_jobs`, `nw_uprooted` and `custom_start_screen`;
   - the authority, still shown as Mutualistic Intelligence;
   - the Wilderness job names;
   - the monthly biomass sync (`on_biomass_monthly` → `wilderness.10`), which should still set biomass to the pop count.

   The install does not say whether `set_origin` keeps, swaps or drops an origin's flags (audit §6 D7). (D7-W, P19)

## Phase 2 – Plays

- [ ] Opening chain: `nw_uprooted.1` fires, and `.2` flavours the designer-picked class (D3-B).
- [ ] Decks and zones produce under their Wilderness job names. Pop growth is positive from day 1; it is the biomass income (D4).
- [ ] Seeding a Roothold costs 100 biomass, paid in pops; Seedwings build them (D4).
- [ ] Seed Casting → the Seed site (`arkship_nw_seed`, 2000 biomass + 1500 food) → a tier-1 Ark of the dropping Ark's class (D6-A). The vanilla Arkship sites are hidden for the Uprooted.
- [ ] Nurture grows Sapling → Bole → Elder on food upkeep and pauses when food runs out. The vanilla upgrade is blocked for the Uprooted (D5-A).
- [ ] The Heartwood withers at 25% hull, 12.5% on the Thornwood (D11); Transplant restores it.
- [ ] Rootfeeding and Devouring Root work with the vanilla harvest numbers; Fruiting creates pops on Heartwood Arks (D4). Devouring Root leaves a Husk, and with Deep Taproot the Seedwings collect each Husk once for new pops. The Rootscar's starting Husk can be collected from the start.
- [ ] "Sap", "Rootfeeding" and "Devouring Root" show only in Uprooted text (D13).
- [ ] *Parked, lowest priority (plan P19).* Take Root uses the vanilla settle action (D7-A). The last Ark settles and the Heartwood carries over through `copy_deposit_from`. Without errors, the empire becomes a settled empire with `origin_wilderness` and the `nw_uprooted` flag (D7-W). From then on it colonises through the Wilderness terraform links, matching its new capital's climate.

## Phase 3 – Looks

The trees are a graphical culture on the vanilla Ark sizes (D2-B). Brief and pipeline: `03-arkship-models.md`.

- [ ] Pipeline proof: the `nw_tree_heartwood` culture and one entity on the vanilla mesh with a tinted texture show on an Uprooted Ark.
- [ ] Seed mesh and the three tier-1 hulls with placeholder textures, plus frame entities if the frame turns out to be culture-prefixed.
- [ ] Withered cultures: the Ark swaps to its withered model when the Heartwood withers, and back on Transplant.
- [ ] Tier 2 and 3 hulls; a Nurture tier-up keeps the tree culture.
- [ ] Optional parts: modules, the three spinal re-skins (Taproot, Heliotrope Crown, Heartlance), district pods, Roothold and Seedwing cultures. Until then the vanilla parts and the bioship models stand in.
- [ ] 2D set: origin icon and picture, class icons, deposit icons, four event pictures. Uprooted-only Ark portraits, headers and blueprints need the overrides listed in `03-arkship-models.md` §6.

## Phase 4 – Ships

- [ ] Symbioses contracts live for AI settled empires.
- [ ] AI plays the origin without stalling, including Nurture (observe 100 years at ×5).
- [ ] Balance pass against Voidfarers at years 30 / 60 / 100 using the targets in `02-mechanics.md` §13. Includes the flagged costs: the Seed's 2000 biomass (2000 pops) and every value marked "design value, balance pending".
- [ ] Workshop page, thumbnail, changelog.

## Stretch

- Arboreal tradition tree (Nomad-only, Wilderness-only) with a finisher that lets a Lumen Ark seed a Roothold at range.
- "World-Tree" ascension perk: Elder Arks gain a fourth growth stage. This needs a new ship size, which D2-B otherwise avoids.
- Grand Herald / Crisis interactions: the Uprooted as a target for the Blight's return.
- Non-hive variant for players who want the trees without the gestalt (would need its own economy and is likely a separate mod).
