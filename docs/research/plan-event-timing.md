# Plan: event timing

*Written: 2026-10-15 | Updated for decisions D1-D14: 2026-10-15 | Game version: v4.5.2 | Companion to `plan-missing-scripts.md` (P-numbers) and `install-audit-4.5.2.md` (D-numbers)*

Vanilla citations are `path:line` relative to the install root. Every hook named here exists in `common/on_actions/00_on_actions.txt` or `02_component_on_actions.txt`; scopes are quoted from their comments.

## 1. Event table

Existing events keep their IDs. New IDs (`.3`, `.6`, `.7`, `.11`, `.31`, `.60`, `.101`, `.102`, `.110`) come from `plan-missing-scripts.md`. `.51` was planned as the Nurture drain and is dropped: under D4 the drain is food upkeep.

| Event | Type | Fired by (verified) | Scope chain | Delay | Conditions | Notes |
|---|---|---|---|---|---|---|
| `.1` The Uprooting | `country_event` | the `.3` relay, one day after game start. Today hooked directly on `on_game_start_country` in `nw_on_actions.txt` | country | **`days = 1`** (§3) | `nw_is_uprooted_empire`, `NOT nw_opening_done` | Runs the P4 start setup in `immediate` |
| `.2` What Endured | `country_event` | option of `.1` | country | 0 | — | **Done (D3-B):** only the option for the designer-chosen class shows (`starting_<class>_arkship`, `common/scripted_effects/01_start_of_game_effects.txt:5206-5217`); it calls `nw_apply_starting_ark_class` |
| `.3` (new, hidden) start relay | `country_event` | `on_game_start_country` (`00_on_actions.txt:31-78`; this = country), replacing the direct `.1` entry | country | 0 | `nw_is_uprooted_empire` | Fires `.1` with `days = 1`, since an on_action cannot delay (syntax: `events/ancient_relics_arcsite_events_1.txt:4122`) |
| `.5` wither scan | `country_event`, hidden | **today** `on_monthly_pulse_country` (`00_on_actions.txt:328`) | country → `every_owned_colony` | — | `nw_is_uprooted_empire` | Replace with `.6`/`.7` plus a yearly pass (§4) |
| `.6` (new) wither after battle | `fleet_event`, hidden | `on_fleet_exit_battle` ("This = fleet … fired once for every fleet that was in combat", `00_on_actions.txt:1758-1768`) | fleet → `every_owned_ship` → `colony?` → `nw_check_wither` | 0 | **Order:** `owner = { nw_is_uprooted_empire = yes }` first, then `is_arkship_fleet = yes` | P7 |
| `.7` (new) wither on disable | `ship_event`, hidden | `on_ship_disabled` ("This = Ship", `00_on_actions.txt:2038-2058`) | ship → `colony?` | 0 | owner Uprooted, `is_arkship_ship = yes` | P7. Vanilla `arkship.1023` also listens here |
| `.10` A Root Touches Stone | `country_event` | `.11` relay | country | 0 | `NOT nw_first_roothold_seen` | **Done (D4):** the grant is 50 new pops on the capital (`create_pop_group`) |
| `.11` (new) Roothold relay | `system_event`, hidden | `on_waystation_built` ("this = galactic object/solar system, from = waystation owner, fromfrom = waystation starbase", `00_on_actions.txt:5916-5928`) | system; `from` = owner country | 0 | `from = { nw_is_uprooted_empire = yes NOT = { has_country_flag = nw_first_roothold_seen } }` | Pattern: `timeline.95` (`events/timeline_events.txt:3538-3560`) |
| `.20` The Core Goes Dark | `country_event` | `nw_wither_heartwood` (first time) | colony → owner country | 0 | `nw_first_wither_seen` guard inside the effect | **Fix:** `[Root.GetName]` is the country. Save the Ark first (`save_event_target_as = nw_event_ark` in colony scope) and use `[nw_event_ark.GetName]` |
| `.21` The Core Beats Again | `country_event` | Transplant decision `effect` (first time) | colony → owner | 0 | `nw_first_transplant_seen` | Same `GetName` fix |
| `.30` Memory of the Grove | `country_event` | the `.31` yearly self-check (§4) | country | — | `years_passed > 20`, `NOT nw_memory_done`, an Uprooted Ark in the `nw_rootscar_system` system | The placeholder in the event file also names `on_entering_system_fleet`; use it only if the delay of up to a year matters (§4) |
| `.31` (new, hidden) Rootscar check | `country_event` | `on_yearly_pulse_country` (`00_on_actions.txt:403`), or self-rescheduling from year 20 | country → `any_owned_fleet` | 0 | `nw_is_uprooted_empire`, `years_passed > 20`, `NOT nw_memory_done` | Fires `.30` when `any_owned_fleet = { is_arkship_fleet = yes solar_system = { has_star_flag = nw_rootscar_system } }` |
| `.40` The Long Drift | `country_event` | `on_mid_game_pulse_country` (`00_on_actions.txt:660-664`) | country | 0 | `NOT nw_midgame_done` | Mid-game start defaults to 100 years (`MID_GAME_START_DEFAULT`, `common/defines/00_defines.txt:482`); docs/02 implied earlier. **Done (D4):** option a costs 500 unity and creates 500 pops on the capital |
| `.50` A Ring Closes | `country_event` | Nurture `on_progress_complete` (`nw_situations.txt`) | situation → `target` (colony) → owner | 0 | — | Same `GetName` fix: save `target` as `nw_event_ark` before firing |
| `.51` Nurture drain (dropped) | — | — | — | — | — | **Dropped (D4):** Nurture's drain is food upkeep on the approaches, and the starvation pause is a `monthly_progress` modifier (`nw_situations.txt`) |
| `.60` (new, hidden) Fruiting credit (D4: new pops) | `carrier_event` | `on_harvest_resources`, `on_arkship_strip_mine`, `on_arkship_reprocess_planet` (`02_component_on_actions.txt:219-245`; vanilla `nomads.4100` is a `carrier_event`, this = target, from = fleet, `events/nomads_events_1.txt:12979-13000`) | planet; `from` = Ark fleet → ship → `colony` | 0 | `from` owner Uprooted and Heartwood class | P15 |
| `.100` Taking Root | `country_event` | the `.102` relay, after the player has picked a `nomads.4705` option | country | **`days = 1`** | owner Uprooted, `settled_as_nomadic_empire`, `NOT nw_rooted` | D7-A, D7-W. In order: `set_origin = origin_wilderness`; `set_country_flag = nw_uprooted`; remove the `started_*` flags and re-fire `wilderness.100` (`events/origin_events_wilderness.txt:62-97`); Wilderness capital building; `nw_rooted` (P19) |
| `.101` (new, hidden) Heartwood copy | `planet_event` | `on_arkship_settle` ("THIS = planet, FROM = triggering fleet", `02_component_on_actions.txt:380-387`), next to vanilla `nomads.4700` | planet; `from` = Ark fleet → its colony ship | 0 | `from` owner Uprooted | Copies `d_nw_heartwood_core` onto the planet now, because `transfer_carrier` destroys the Ark colony later (`common/scripted_effects/nomads_effects.txt:5187-5194`). Starts the fallback poll if `on_capital_changed` does not fire on settle (unverified) |
| `.102` (new, hidden) capital relay | `carrier_event`, like vanilla `origin.3205` (`events/origin_events_3.txt:433-435`) | `on_capital_changed` ("This = Colony (new capital), From = Colony (old capital)", `00_on_actions.txt:3442-3454`) | colony → owner | 0 | owner Uprooted, `settled_as_nomadic_empire` (set by the settle effect, `nomads_effects.txt:5058`), `NOT nw_rooted` | Fires `.100` on the owner with `days = 1`. Keys on the flag, not on `from`, because the old capital colony may be gone |
| `.110` (new, hidden) captured-Ark cleanup | `carrier_event` | `on_colony_transfer` ("This = Colony scope, From = new owner, FromFrom = previous owner", `00_on_actions.txt:3369-3422`) | colony | **`days = 1`** | `is_planet_class = pc_ark`, `fromfrom` Uprooted, `from` not Uprooted | P21; runs after vanilla `wilderness.300`; also resets the Ark's graphical culture |

## 2. Hooks already in `common/on_actions/nw_on_actions.txt`

| Entry | Verdict |
|---|---|
| `on_game_start_country → nw_uprooted.1` | Valid hook (`00_on_actions.txt:31`). Replace with `.3`, which fires `.1` with `days = 1` (§3) |
| `on_monthly_pulse_country → nw_uprooted.5` | Valid hook (`:328`); replace per §4 |
| `on_mid_game_pulse_country → nw_uprooted.40` | Valid hook (`:660`) |
| Waystation-built placeholder | `on_waystation_built` (`:5916-5928`), system scope, needs `.11` |

## 3. Ordering at game start

The engine runs empire initialisation hooks before any game-start on_action. The comments at `00_on_actions.txt:4047`, `:4055` and `:4066` say "called during empire initialization when starting a new game".

| Step | Hook | Vanilla events | What exists afterwards |
|---|---|---|---|
| 1 | `empire_init_add_technologies` (country, `:4047-4053`) | `game_start.8` | starting techs |
| 2 | `empire_init_capital_colony` (**colony**, `:4055-4064`) | `game_start.12` (`events/game_start.txt:1724-1796`) | the Ark capital colony, start pops and districts (none for a Wilderness nomad, H23); `home_arkship_initial_setup` sets `starting_<class>_arkship`, assigns a commander and adds `d_collapsed_burrows` for hives (`01_start_of_game_effects.txt:5202-5260`) |
| 3 | `empire_init_create_ships` (country, `:4066-4072`) | `game_start.9` | science ship + Logistic Ship, no warships (`events/game_start.txt:1137-1168,1316-1353,1471-1476`) |
| 4 | `on_game_start` (no scope, `:2-29`) | `game_start.1` → `nomad_country_initial_setup_effect` (`events/game_start.txt:457-469`) | `nomads.50` checklist popup; `arkship.1` director queued with `random = 20` (quiet for 5 years, `events/nomads_arkship_events.txt:19-22`); reserves converter; home-system deposits stripped and harvestables pre-depleted (`01_start_of_game_effects.txt:5163-5194`). `wilderness.150` fires only for the raw `origin_wilderness` (`events/game_start.txt:451-455`) |
| 5 | `on_game_start_country` (country, `:31-78`) | `wilderness.100` (`:67`); `nomads.1000` (Forever Cruise only); `nomads.20` (`:73`: reserves situation, every fleet moved to a random planet orbit); `nomads.25`; **mod `.1`** | With P1, `wilderness.100` also runs for the Uprooted. It sets `started_wet/dry/cold` from `capital_scope`, which is a `pc_ark` colony with `climate = "artificial"` (`06_planet_classes_nomads.txt:70`), so probably `started_cold`. It also removes `d_decrepit_dwellings` |

**Not stated in the files (unverified):**
- the order of steps 4 and 5
- whether mod entries in an on_action run before or after vanilla's

**Recommendation:**
- Fire `.1` with `days = 1`, through the `.3` relay. The start setup (P4) then runs after `nomads.20` has moved the fleets and after `wilderness.100`, and the opening popup follows the `nomads.50` checklist instead of racing it.
- The starting Ark itself exists from step 2, so any delay is safe.

## 4. Cost problems and cheaper hooks

| Today | Problem | Proposal |
|---|---|---|
| `.5` on `on_monthly_pulse_country` | Runs for every country every month. The trigger fails fast for non-Uprooted, but the Uprooted scan runs every month whether or not anything happened, and a wither can lag a month | `.6` on `on_fleet_exit_battle` and `.7` on `on_ship_disabled`: they fire only when hull can have changed in combat. Keep a **yearly** pass (`.5` moved to `on_yearly_pulse_country`, `00_on_actions.txt:403`) for out-of-combat losses such as cosmic storms, whose monthly `damage_ship` hits every ship in the storm above 20% HP (`events/cosmic_storms_events_1.txt:863-880`; `@storm_hp_perc_limit = 0.2`, `common/scripted_variables/07_scripted_variables_cosmic_storms.txt:58`). `on_damage_taken` (`:1492-1500`) fires per hit and is too frequent |
| `.30` on fleet movement | `on_entering_system_fleet` fires for every fleet entering any system, galaxy-wide, all game. Even a cheap trigger is evaluated constantly | **Plan:** `.31` on the Uprooted's own yearly pulse, starting after year 20 and stopping once `nw_memory_done` is set. Latency up to one year, which the narrative tolerates. If immediacy matters, use `on_entering_system_fleet` with `from = { has_star_flag = nw_rootscar_system }` as the **first** condition |
| `.6` on `on_fleet_exit_battle` | Fires for every fleet after every battle | Order the trigger owner flag → `is_arkship_fleet`; non-Uprooted fleets exit on the first check |
| `.11` on `on_waystation_built` | Fine; only nomads build Waystations | — |
| `.60` on harvest on_actions | Fine; fires once per completed harvest | Check `from.owner` first |

## 5. Timeline, day 0 to endgame

| When | What fires | Mod event |
|---|---|---|
| Empire init | `game_start.8` → `.12` → `.9` | — |
| Day 0 | `game_start.1` (`nomads.50` popup), `wilderness.100`, `nomads.20`, `nomads.25` | `.3` relay |
| Day 1 | — | `.1` → start setup (P4) → `.2` |
| Days 0-20 | `arkship.1` director scheduled; no Ark events for 5 years (`events/nomads_arkship_events.txt:19-22`) | — |
| First Waystation, usually year 0-2 | `on_waystation_built` | `.11` → `.10` |
| Any battle involving an Ark | `on_fleet_exit_battle` / `on_ship_disabled` | `.6`/`.7` → `nw_wither_heartwood` → `.20` the first time |
| Yearly | `on_yearly_pulse_country` | `.5` safety scan; `.31` once past year 20 |
| Transplant completes (180 days after queueing) | decision effect | `.21` the first time |
| Tech: Seed Casting, Elder Growth | — | unlocks the P11 Seed and Bole → Elder Nurture (P9) |
| Nurture running | food upkeep on the chosen approach (no event) | `.50` on completion (100 or 200 progress) |
| Year 20+ | Ark in the Rootscar system | `.30` |
| Mid-game, default year 100 (`00_defines.txt:482`) | `on_mid_game_pulse_country` | `.40` |
| Endgame, after Adaptability or Versatility (`09_scripted_triggers_nomads.txt:908-937`) | `arkship_settle` → `on_arkship_settle` → `nomads.4700` → `nomads.4705` | `.101` copies the Heartwood; after the player's choice, `on_capital_changed` → `.102` → `.100` a day later |
| Any time an Ark changes hands | `on_colony_transfer` (`wilderness.300`, `nomads.1`, `arkship.1450`) | `.110` (1 day later) |

## 6. Open questions

- Whether mod `on_game_start_country` entries run before or after vanilla's (§3).
- Whether `on_capital_changed` fires when `transfer_carrier` moves the capital colony during settling.
- Whether `set_origin` keeps, swaps or drops origin flags (P19; first test load, item 9). The `started_*` question is answered: the terraform links check the flag, so `.100` re-sets it.
