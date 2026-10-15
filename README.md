# Nomadic Wilderness: The Uprooted

A Stellaris mod that lets the **Wilderness** origin (BioGenesis) play as a **Nomadic empire** (Nomads expansion), with the whole thing themed after the **Tree of Life** origin from the Plantoids pack.

The pitch in one line: *the sapient biosphere didn't lose its world-tree; it became one, pulled its roots out of the crust, and left.* Your capital is not a planet. It is a tree the size of a moon, drifting between stars, and every Arkship you will ever own is a seed it dropped.

## Status

Design complete, scaffold scripted, nothing tested in-game yet. See `docs/05-roadmap.md` for the build order and `docs/04-vanilla-hooks.md` for the list of vanilla keys that must be verified against a local game install before the scaffold will load cleanly.

## Version target

| Thing | Value |
|---|---|
| Stellaris patch | 4.4 "Pegasus" (Nomads released 2026-06-15; 4.4.4 reworked Arkship colony modifiers) |
| Required DLC | Nomads, BioGenesis, Plantoids Species Pack (for Tree of Life assets and the sapling events) |
| Mod key prefix | `nw_` |

The request named "v4.3". Nomads and Arkships are a 4.4 feature, so everything here targets 4.4.x.

## What is in this repo

```
docs/
  01-concept.md          Lore, pillars, fantasy, how it maps Tree of Life onto Nomads
  02-mechanics.md        Origin, economy, the three Tree Arks, Rootholds, contracts, events, endgame
  03-arkship-models.md   Art brief for the three tree Arkships (Civilian/Science/Military) x 3 tiers
  04-vanilla-hooks.md    Every vanilla key the mod depends on, and how to verify it
  05-roadmap.md          Milestones from "loads without errors" to "Workshop release"
common/, events/, localisation/, gfx/
  Script scaffold. Files marked TEMPLATE at the top need vanilla values filled in.
descriptor.mod
```

## Installing for development

1. Clone or symlink this folder into `Documents/Paradox Interactive/Stellaris/mod/NomadicWilderness`.
2. Copy `descriptor.mod` to `Documents/Paradox Interactive/Stellaris/mod/NomadicWilderness.mod` and add a `path=` line pointing at the folder.
3. Run the Phase 0 checklist in `docs/04-vanilla-hooks.md` before launching. The TEMPLATE files will not load until their placeholders are replaced.
4. Launch with `-debug_mode` and read `error.log` after reaching the main menu.

## Naming

| Vanilla term | Uprooted term |
|---|---|
| Arkship | Ark (Heartwood Ark, Lumen Ark, Thornwood Ark) |
| Arkship tier 1 / 2 / 3 | Sapling / Bole / Elder |
| Waystation | Roothold |
| Wayline | Mycorrhizal Lattice |
| Logistic Ship | Seedwing |
| Operational Reserves | Sap |
| Contract | Symbiosis |
| Planetary Strip Mining | Rootfeeding |
| Planetary Reprocessing | Devouring Root |
| Settle | Take Root |
