# Patch-note harvest, 4.0 to 4.5.2 (raw)

Verbatim keyword lines from the stellaris-wiki MCP (`wiki_patch_notes` with filter "all", plus `wiki_page` for the "Patch 4.x.X" point-release pages), collected 2026-10-15. Patch notes are secondary evidence: `vanilla-nomads-4.5.2.md` §4 keeps the lines that matter and checks them against the install, which wins every conflict.
Versions available: 4.5, 4.4, 4.3, 4.2, 4.1, 4.0, 3.14, 3.13, 3.12, 3.11, 3.10, 3.9, 3.8, 3.7, 3.6, 3.5, 3.4, 3.3, 3.2, 3.1, 3.0, 2.8, 2.7, 2.6, 2.5, 2.4, 2.3, 2.2, 2.1, 2.0, 1.9, 1.8, 1.6, 1.5, 1.4, 1.3, 1.2, 1.1, 1.0, 1.2.5, 1.2.4, 1.2.3, 1.2.2, 1.2.1, 1.0.3, 1.0.2, 1.0.1, 1.0.0
(Note: wiki_patch_list lists minor versions only, and wiki_patch_notes parses only the minor-version page. It has no point releases and drops sub-bullets. Point releases live on the wiki pages "Patch 4.x.X", read with wiki_page from the same stellaris-wiki MCP. wiki_patch_diff was not used.)
Versions read:
- wiki_patch_notes, filter "all": 4.4, 4.5, 4.3, 4.0 (4.0 for the Wilderness claims only).
- wiki_page / wiki_page_section: 4.4 (header + Nomads Features sub-bullets), 4.4.2, 4.4.3, 4.4.4, 4.4.5, 4.4.6; 4.5 (header + dropped sub-bullets), 4.5.1, 4.5.2; 4.3 (header + dropped sub-bullets), 4.3.1–4.3.7; 4.0 (header + Wilderness lines); 4.0.2–4.0.23 (searched only for Wilderness / biomass / Tree of Life / devastation).
- Not read: 4.1.x, 4.2.x (out of scope). No tool errors. Several outputs exceeded the inline limit (4.5 patch notes; 4.0.X, 4.5 and 4.3 pages) and were read in full from the harness's saved tool-result files.
- Counts: 614 keyword lines (575 from the 4.3/4.4/4.5 families + 39 Wilderness-claim lines from 4.0/4.0.x), 24 [off-list] lines, 5 release-header lines.

Method notes:
- Quotes are the change text only. The tool's own annotations (direction tag such as [NEW]/[FIX], {system tags}, <<numbers>>) are stripped.
- "ark" is matched only as Ark/Arks/Arkship, not inside market, dark, marking and similar words.
- Lines tagged [off-list] have no keyword hit but are plainly Nomads/Arkship mechanics (mobile starbase, encampment, carrier). They are counted separately.

## 4.4 (wiki_patch_notes filter:"all"; 274 changes; codename/date not given by this tool)
Tool caveat: the "Nomads Features" entries are one-line headers. The tool dropped their sub-bullets (e.g. Stellar Cannon carries <<10%, 25%, 50%, 000 energy>> from text it does not show). There are no 4.4.x point-release sections in this output.

### Nomads Features
- [nomad] "Nomadic Empires introduced"
- [nomad] "Nomadic Enclave: Champions Forge Live!"
- [nomad, origin] "New Nomadic Origins"
- [nomad] "New Nomad Civics"

### Improvement
- [gestalt] "Gestalt pops have a weaker attachment to their current jobs overall."
- [origin] "Event option tooltips for ethic drift effects in the Costume Culture, Roots Reclaimed, and Fans No More First Contact events, and the Broken Shackles origin homecoming option, now show a single summary line instead of one entry per affected pop group."
- [origin] "The System Initialiser selector now hides systems you are not eligible for (for example, KotG Sol if you do not have the KotG origin)."
- [logistic ship, arkship] "It is now possible to queue the return order. Useful when issuing commands for the logistic ship and then you want it to return to the arkship to drop off the stockpile."
- [nomad] "Added a slider to set the amount of randomized nomadic empires that is spawned during galaxy generation."
- [arkship] "Fleets that return to or are built at a Juggernaut or Arkship now automatically follow it."

### Bugfix
- [reprocess] "Fixed an issue where pops could end up in "reprocessing" jobs forever."
- [district] "Fixed an issue where the Mindlink Metropolis district would appear in the wrong position on the capital planet screen after unlocking the Mind Meld perk."
- [zone] "Habitat Mining Station designation was incorrectly scoring energy zones instead of mining zones."
- [origin] "Fixed Necrophage origin tooltip incorrectly stating 1.2K prepatent Pops instead of 2.4K for non-genocidal empires."
- [origin] "The Necrophage origin tooltip display "prepatent" as intended and swap the wording of "Rulers" to "Elites""
- [origin] "The Storm Chasers origins modifier no longer has a :: instead of : in its description"
- [district] "Fixed colonisable planets sometimes generating with no energy generator districts."
- [district] "Fixed Volcanic worlds not having uncapped energy districts."
- [zone] "Fixed Production Unit ringworld segment missing correct zones for fallen machine empires."
- [wilderness] "The Symbiont Overdrive edict now unlocks correctly for Wilderness empires."
- [gestalt] "The Confidence in Variance agenda correctly appears in Domination Traditions for Gestalt empires, and it has a finisher effect."
- [situation, wilderness] "The Biomorphosis Situation no longer mentions Genomic Research Facilities if you are a Wilderness empire."
- [bioship] "Fixed empires with Biological Ships not getting access to G-slot defensive platforms"
- [bioship] "Bio-ship empires now correctly receive bio-ship technologies during the crisis tech grant, instead of silently getting nothing."
- [origin] "Fixed a possible crash on INF_ORIGIN_RED_GIANT_TRIANGULATION special project."

### UI
- [situation] "Situation Log UI improvements, including categorization of entries and new interface looks"
- [district] "Expansion planner sort buttons now display district type names instead of resource names."
- [district, blocker] "The Planet View UI now shows districts that are being built as part of the overview where the built, unused and blocker ones are to give a more accurate overview of the situation"

### AI
- [settle] "Empires that are still small will now expand more aggressively, so they more reliably claim their homeworld's guaranteed habitables and nearby good worlds before settling into build-up mode."
- [settle] "AI resettlement now moves pops from overpopulated worlds to underpopulated ones regardless of how those planets got that way, rather than only emptying out worlds with terrible habitability. New colonies should grow faster and veteran worlds should stagnate less."
- [district, zone] "On well-developed planets (2500+ pops), the AI is now more likely to round out its rural districts so it unlocks the building slots they provide. It still won't waste slots on a district whose specialization zones aren't available yet, so early-game behavior is unchanged."
- [zone] "The AI now proactively tears down and rebuilds urban zones that don't match a planet's designation - for example, a col_foundry world will swap out a generic zone_industrial for zone_foundry to double-specialize into its designation. Swaps apply a per-building penalty for each existing building that doesn't carry over through the new zone's included_building_sets, require a larger affinity margin when the slot is already specialized to prevent churn between near-equivalent candidates, and must clear a minimum-benefit floor so the construction cost is justified by the economic delta. Planets already rich in matched buildings stay put; planets with mismatched buildings restructure."
- [zone] "Colony designation affinity (the ai_building_set_affinity on colony types) now biases AI zone selection in addition to building selection. A designation that prefers the "foundry" building set will now also weight foundry-themed zones more heavily during construction planning."
- [district] "Nanotech Worlds: Fixed AI empires getting stuck after converting planets to Nanotech Worlds. The AI now prioritizes nanolabs, nanotech cauldrons, and nanite transmuters on pc_nanotech colonies instead of leaving rural districts unspecialized."

### Stability
- [situation] "Fixed an OOS happening on hotjoin when a situation has enough progress to go past the last stage."

### Modding
- [nomad] "Added is_nomadic option for create_country"
- [district] "Two new defines: AI_UNBUILT_DISTRICT_BOOST_POP_THRESHOLD (default 2500) and AI_UNBUILT_DISTRICT_BOOST_MULTIPLIER (default 2.0). The multiplier is applied to any districts that have 0 built when the planet meets the pop threshold, lacks that district type, and at least one of its specialization slots has a potential district specialization for the planet."
- [settle] "Defines AI_RESETTLE_FROM_LOW_HABITABILITY_THRESHOLD and AI_RESETTLE_TO_HIGH_HABITABILITY_THRESHOLD have been removed - they no longer drive behavior. The AI now only requires source pops ≥ 2× target pops, and rejects any target with habitability below AI_MIN_HABITABILITY_TO_COLONIZE."
- [zone, district] "New define AI_ZONE_SPECIALIZATION_AFFINITY_DELTA (default 0.5) in common/defines/00_defines.txt. This is the minimum affinity improvement a candidate zone must offer over the existing district specialization before the AI will propose a swap. Raising it makes the AI more reluctant to restructure existing zones; lowering it makes the AI more aggressive about perfecting specialization."
- [district] "Colony type ai_building_set_affinity entries now also apply to district specialization types. Any district specialization whose included_building_sets list includes a building set named in the colony's affinity table will receive the corresponding AI weight multiplier."
- [zone] "New define AI_ZONE_SWAP_BUILDING_LOSS_PENALTY (default 0.2) in common/defines/00_defines.txt. Each existing building in the slot that can't be carried over via included_building_sets subtracts this much from the candidate zone's effective affinity. With the 0.2 affinity delta default, a 1.5 -> 2.0 swap tolerates up to 2 displaced buildings but refuses at 3 or more. Raise to protect developed planets; lower to let the AI restructure more aggressively."
- [zone] "New define AI_ZONE_SWAP_RATCHET_MULT (default 1.5) in common/defines/00_defines.txt. Multiplier applied to AI_ZONE_SPECIALIZATION_AFFINITY_DELTA when the slot is already occupied by a specialized zone (affinity > 1.0). First-time swaps from a baseline 1.0 zone use the normal delta; swaps that would displace an already-specialized zone need a larger margin. Prevents ratcheting between near-equivalent specialized candidates."
- [zone] "New define AI_ZONE_SWAP_MIN_BUILD_WEIGHT (default 2.0) in common/defines/00_defines.txt. Minimum CalcBuildWeight a zone-swap proposal must reach before it's queued - any swap whose economic delta doesn't clear this floor is discarded even if the affinity delta would otherwise allow it. Prevents slot churn where multiple candidates cluster near the top and the AI keeps swapping for marginal improvements."
- [arkship] "Updated GetPlanetMoon to work for Habitats, Ringworlds, and Arkships"
- [situation] "Added `show_in_outliner` parameter to situation type"
- [contract] "Add has_contract and has_contract_of_type triggers"
- [district] "inherits_capped_modifiers_from has been removed from the game. It is replaced by shared_capacity_modifier which is now a pool that all districts on the planet share their capacity from. Allowing for new modding possibilities"
- [contract] "Add is_contract_type trigger to mission scope"
- [nomad] "Add is_nomadic government restriction"
- [nomad] "Add is_nomadic country trigger"
- [district] "Added planet_artificial_max_districts_add which increases the district cap on colonies for which the planet class has is_artificial_planet = yes"
- [situation] "Added potential trigger for situation types"

### Off-list (Nomads mechanics, no keyword)
- [off-list] "Fix mobile starbases' country modifiers not always being applied"
- [off-list] "Fixed fleets being unable to return to a mobile starbase while it is in FTL transit."
- [off-list] "Renamed system_colony script lists to system_planet_colony."
- [off-list] "Add carries_colony requirement to special projects"
- [off-list] "new carrier_is_type trigger"
- [off-list] "Hooked on_actions for encampment and packing up from code"
- [off-list] "Replaced is_moving trigger by is_system_locked"
- [off-list] "New on_action on_system_locked_ship_killed fired when an encamped ship is killed"
- [off-list] "Add time_to_accept and time_to_complete to missions"
- [off-list] "Added steal_planet_output effect"
<!-- END 4.4 -->

## 4.5 (wiki_patch_notes filter:"all"; 598 changes; codename/date not given by this tool)
Tool caveat: 632-line output. Same as 4.4: no 4.5.x point-release sections, and some headers ("Pop groups are no longer divided ... This means that:", "Consecrate World changes:", "Joining and Leaving War changes:") have their sub-bullets dropped.

### Feature
- [nomad] "Unlocked Knights of the Toxic God for nomads"
- [nomad, wayline] "Federations can now enact laws to grant members free, automatic Commercial Pacts (and, with the Nomads DLC, Wayline Pacts) or Research Agreements. Each law is unavailable to the federation type that already grants the benefit through its perk (Trade League / Research Cooperative)."
- [nomad, arkship, logistic ship, harvest, strip mining (variant "Strip Mine"), waystation] "Fleet automation menus now offer per-action options. Nomad Arkships and Logistic Ships can fine-tune Harvest, Strip Mine, and Deep Scan automation, including whether to target unsurveyed, depositless, waystation, friendly, or hostile-owned worlds, and whether to rescan."
- [nomad] "Cosmogenesis is now available for Nomads"
- [nomad] "Added a nomad compatible version of Storm Chasers"

### Improvement
- [contract] "Regular Empires can now cancel accepted contracts after 20 years, with a notification sent to the operator."
- [forever cruise, situation] "Added new event options to the Forever Cruise situation stage events, allowing players to accept temporary economic penalties to affect the situation's direction."
- [waystation] "Starbase, Orbital Ring, and Waystation weapon modules are now weapon-type-specific (lasers, mass drivers, swarmer missiles, particle launchers, psionic torpedoes, scourge missiles, and several strike-craft hangars), each gated by the matching weapon technology and automatically upgrading to your latest tier."
- [nomad, arkship] "Nomad empires can now benefit from the Composer of Strands, Whisperers in the Void, and End of the Cycle Greatest Patron accords, with Arkship-appropriate rewards."
- [situation] "Adaptive Evolution approach effect will be displayed as an active situation effect instead of just in the tooltip over the approach selection button."

### Balance
- [hive mind] "Hive minds which encounter genetic defects during cyberization can now choose a cyberisation policy."
- [arkship] "Scientific Arkships now have +5% Deep Scanning Success Chance per Tier"
- [arkship, zone] "Increased soldier alloy upkeep for arkship military zones by 0.1"
- [forever cruise, gestalt] "Rogue Servitor Forever Cruise Passengers now provide an additional +1% Complex Drone Job Efficiency on top of the base Gestalt bonus."
- [gestalt, nomad] "The Instrument and Cradle Chosen psionic leader traits now grant applicable bonuses to Gestalt and Nomad empires instead of slave-workforce, faction, and friendly-territory bonuses they could not use."
- [operational reserve] "All ships and cloned space fauna that have a mineral cost can no longer be built if your Operational Reserves are critical"
- [district] "The Dimensional Manipulator now grants a fixed number of 5 districts instead of a 33%"
- [bioship] "Offspring bio-ships now use the same Naval Capacity as the bio-ships they are grown from."
- [harvest] "Trade deposits now increase the resources gained from harvesting a planet."
- [arkship, harvest] "Reduced the arkship harvesting and action modifiers provided by harvester jobs."
- [zone] "Reduced the weapon damage each Ordnance Engineering weapon zone grants per soldier job from 5% to 1%."
- [bioship] "Biological ships are no longer affected by energy deficits."
- [bioship] "Biological ships are now affected by food deficits."

### Bugfix
- [situation] "The Branching Out modifier from the Cyberization situation now gives +10% Trader Job Efficiency."
- [waystation] "Automated starbase, waystation, and orbital ring designs now fit armor instead of shields in systems or on stations where shields are negated (pulsars, X-ray binaries, turbulent nebulae, and cloaking modules)."
- [arkship] "Fixed the crime events (among others) not firing on arkships"
- [forever cruise] "Clarified the Creativity Exhibition Vault's tooltip for Forever Cruise Rogue Servitors: its production and upkeep effects reach all passengers, not just first-class Bio-Trophies."
- [nomad, arkship] "Synthetic Fertility Nomads are no longer able to build arkships until they have researched droids"
- [nomad] "Added a missing nomad Archaeology notification string"
- [nomad] "Fixed the Ness treasure system not spawning for non nomad empires"
- [wilderness] "Wilderness and Synthetic Fertility now start with homeworlds of the proper size"
- [planet modifier] "'Delicious Titans' planet modifier tooltip now correctly says 'Farmers are Converted in Titan Hunters' and doesn't show a broken icon."
- [forever cruise, arkship] "Entertainer jobs on Forever Cruise Arkships now correctly come from the Entertainment Hub specialization instead of the Operations Center."
- [nomad, settle] "Fix for Nomadic Grand Archives being destroyed when a Settled Empire's system is destroyed."
- [nomad, waystation] "Fix for Nomadic Grand Archive not being destroyed when a Waystation is lost or downgraded below Tier 2."
- [forever cruise] "Permanent Employment in Forever Cruise now only zombifies the crew, not the passengers"
- [forever cruise, contract] "Removed the habitability requirement for the various Shore Leave internal Forever Cruise contracts."
- [forever cruise, origin] "Bio-trophies now give 10 Political Power when playing as Rogue Servitor with the Forever Cruise Origin"
- [forever cruise] "Machine Intelligence and Individualist Machine Forever Cruises with machine passengers now alternate assembly every month."
- [cruise] "Cruise crew and passengers no longer incorrectly assigned Grid Amalgamation or Livestock living standards and slavery types."
- [forever cruise] "Individualistic Machine Forever Cruise no longer gets an error from placing Robot Assembly Plants at game start."
- [forever cruise] "The Package Deal living standard in Forever Cruise now grants political power to employed Economy Class Passengers."
- [forever cruise, situation] "Added missing event sounds to the Forever Cruise situation"
- [arkship] "Fixed issues when dismantling Beastports on Arkships."
- [arkship] "Arkships without equipped weapons no longer charge into melee."
- [district] "Fixed issue with district indicators in the planet view"
- [harvest] "Fixed manually issued system-wide Harvest orders queueing when there were no valid targets."
- [arkship] "Fixed Arkships sometimes giving up on surveying."
- [nomad] "Nomadic Purge Vassals are now a special subject type that provide resources to their Overlord through subject taxes. Any remaining pops and resources are handed up to the overlord when the subject finishes its work, instead of being lost when the subject's colony was abandoned. A Purging Complete notification lists the pops and resources handed over to the Overlord when a Purge Vassal finishes its work, including any incidental resources that the planet may have produced during their stay."
- [nomad] "Fixed Nomad Overlords not receiving the Unity (Fanatic Purifiers, Determined Exterminators, and Necrophages) or Society Research (Devouring Swarms) generated when their temporary purge subjects purged pops - the reward now goes to the Overlord instead of being lost with the subject."
- [nomad] "Nomad purge subjects of assimilating empires (such as Driven Assimilators) now assimilate the pops they can rather than purging them, only exterminating pops that cannot be assimilated. The assimilated pops are handed up to the Overlord when the subject disbands, instead of the subject exterminating everyone or lingering forever as a separate empire."
- [nomad] "Nomadic Purge Vassals no longer establish Embassies, and other empires can no longer propose Embassies to them."
- [nomad] "Empires now correctly lower their opinion of a Nomad Overlord when its Purge Vassal Necrophages pops of their species"
- [arkship] "Fixed the Necrophage Elevation Ceremony failing on Arkships. Elevated pops now adapt to Arkship Habitability, and the Pops Necrophaged notification is now shown, highlighting the Arkship fleet"
- [nomad] "Nomadic empires attempting to invade the first pre-FTL encounter are now correctly told that they need to survey the planet, not that their pre-ftl interference policy is preventing them from invading the planet."
- [origin] "Fixed a wide-ranging regression that caused many anomalies, events, and origin features to silently stop working - most visibly the classic Precursor dig sites no longer appearing when surveying, and the Fear of the Dark origin's hostile empire failing to spawn."
- [arkship] "Arkship weapon and reactor specializations now clearly indicate in their tooltip that they add Strategic Resource upkeep, with a link to the Strategic Resources concept explaining what that means."
- [nomad] "Fixed several minor artifact and anomaly research projects (including the Precursor "Delve into the Secrets" actions, Master's Writings, and numerous other special projects) granting no reward when completed as a Nomad empire."
- [nomad, waystation] "Fixed an issue where a Nomad waystation disabled by hostile space fauna during a raid could remain disabled after the fauna left the system."
- [nomad] "Nomads can now reactivate and own gateway megastructures"
- [contract] "Scan Debris Contracts now automatically cancel when the target debris decays before it can be scanned, instead of remaining active indefinitely."
- [origin, arkship, nomad, district] "The Prosperous Unification origin now displays correct, Arkship-specific effects and flavor text for Nomadic empires, instead of planet-based districts and "Homeworld" terminology"
- [nomad] "Wars declared with the Nomadic Raid, Plunder, or Wipe Them Out war goals now include the war type in their name, consistent with other war goals"
- [nomad] "Nomads can now annihilate each other in Machine Uprising wars."
- [nomad, settle] "Fixing Nomadic Grand Archives being destroyed when taking control of a Settled Empire's Starbase."
- [arkship, zone] "Fixed metallurgist jobs on the Foundry Arkship zone always paying their extra upkeep in minerals. Empires with Catalytic Processing now correctly pay it in food instead."
- [nomad, situation] "Nomadic Empires can now be reborn as an exiled colony after failing the "End of the Cycle" situation."
- [forever cruise, gestalt, district] "Forever Cruise Gestalts can now properly upgrade their service districts"
- [contract] "Contracts that time out from no fault of the issuer, now display a clearer notification about it."
- [arkship, situation] "Arkships no longer automatically benefit from the "Foreign Occupation" Planetary Revolt situation modifier when crossing another empire's border."
- [nomad] "The Machine Uprising event will now trigger a Nomadic Machine Empire to spawn for Nomadic Empires."
- [nomad, operational reserve] "Nomad Vassals now correctly inherit Operational Reserves"
- [nomad, arkship] "The Eater of Worlds Digest now counts Nomad Arkship colonies in its tally."
- [nomad, arkship] "The End of the Cycle Creation Accord no longer grants Nomad empires a colonized planet they cannot hold. Nomads now receive a newly materialized Arkship of a type they have researched."
- [arkship (typo "Arkhships")] "Arkhships should no longer get deleted by Cetana's storm"
- [arkship] "Arkships should no longer be targeted instead of the proper planet in the Phaseshift even chain"
- [nomad, hive mind (variant "Hive-Minded")] "Nomadic Driven Assimilator purge vassals now Assimilate Hive-Minded pops when their Overlord has taken Inevitable Assimilation (or another tradition that unlocks Hive-Minded Assimilation), instead of being forced to purge them."
- [gestalt] "Cyber Hive purge vassals now Assimilate pops into the gestalt consciousness as their Overlord would, and receive the associated Cyber Hive Assimilation bonuses."
- [arkship] "Fixed a case where newly built arkships could get colony events. No you don't have subterraneans living in your soil."
- [nomad] "Fixed numerous events, decisions, and buildings silently failing or producing error log spam for Nomad empires."
- [nomad] "The Nomadic Gameplay toggle now grays out immediately for multiplayer clients when the host has the Nomads DLC disabled, without needing to reopen the Government & Ethics tab."
- [nomad] "Fixed an issue preventing nomadic empires from joining wars"
- [arkship] "Disabled Arkships no longer appear as colonization sources."
- [arkship] "Fixed special projects on Arkship colonies resolving to the wrong planet scope."
- [arkship] "Science Ships will no longer cluster with Arkships when auto-surveying."
- [nomad] "Nomadic empires with purging banned in their policies no longer create purge vassals when conquering planets."
- [district] "Fixed instances of the Synaptic Lathe receiving additional districts."
- [bioship (variant "bio-ships")] "Behemoth crisis objectives no longer grant five times the intended progress when destroying, breeding or capturing space fauna and bio-ships."
- [situation] "The Voidworm Plague situation and the energy reward for destroying enemy ships no longer scale five times higher than intended."
- [nomad, situation] "Nomadic Evolutionary Predators should now get situation progress from their temporary total war purge vassals."
- [nomad, arkship] "The Seismic Disturbances event chain no longer triggers on Nomad Arkships."
- [nomad, waystation] "The Lost Fleet event no longer fires for empires that had no mobile fleet in the destroyed system, such as a Nomad empire with only a Waystation present."
- [arkship] "The Developmental Pheromones for Arkships is now restricted to Arkships."
- [gestalt] "Gestalt capital buildings now properly lists Chronicle Drones when appropriate."
- [nomad] "Fixed Synaptic Lathe collapse warning not triggering for Nomadic Empires."
- [harvest, logistic ship] "Harvest automation no longer sends Logistic Ships to planets whose deposits can only be worked by a Research Station."
- [planet modifier, harvest] "Fixed the Unaligned Shield Projectors planet modifier showing the resource harvesting cooldown instead."
- [arkship] "The Viridescent Lightbringer can now be granted Exotic Gas and Volatile Mote deposits by the Surveyor relic and by Arkship Deep Scanning."
- [nomad] "Fixed a broken subject icon and an intermittent crash on Negotiate Terms when a Nomad empire inherited a Scholarium or other specialist subject by winning a subjugation war."
- [gestalt] "Restoring a culled Gestalt node no longer strips the extra starting trait granted by the Aptitude tradition tree or Optimized Neurology."
- [nomad] "Re-enabled Distribute Luxury Goods for Nomads. Let the people have their free smart phones!"
- [situation] "Fixed a crash that could occur when reopening the Situation Log after a situation ended and a new one began."
- [nomad] "Fixed Nomadic Machine Uprisings not getting all of the technologies they should"
- [nomad] "Nomadic empires that are not Storm Chasers can now get Storm technologies too."
- [gestalt] "Gestalt council nodes are now labeled with their position, instead of with a separate node name."
- [gestalt] "A gestalt node's description no longer names the node differently depending on where you hover."
- [district] "The Synaptic Lathe no longer gains extra districts from empire-wide modifiers, such as the Instrument of Desire blessing or the Corridor astral rift."
- [nomad, operational reserve, logistic ship, settle] "Separatists from a Nomad empire are now created as Nomad empires, with Operational Reserves and a Logistic Ship, instead of settled empires."
- [nomad, gestalt] "Nomad separatists no longer become gestalt consciousnesses far more often than intended."
- [gestalt] "Fixed gestalt empires patroned by the Instrument of Desire receiving no Psionic Choir policy."
- [nomad] "Fixed the Sector as Vassal deed being offered to Nomad empires."
- [nomad] "Fixed a Nomadic Princess option revealing a second wreck."
- [gestalt] "Fix Gestalt Nodes that were sometimes displaying as portraits"
- [district] "Planets colonized by the Contingency or the Prethoryn Scourge now show a complete set of districts, instead of missing their housing districts entirely."
- [origin] "The Claimant event in the Heirs of the Khan origin now correctly names the rival candidate's species."
- [origin] "While playing with both the Red Giant origin and Eager Explorers civic, orbital stations can be built on the star as intended."
- [origin] "Fixed Starlit Citadel origin failing to spawn its initial Deep Space Citadel."
- [situation, contract] "Situation Log entries for contracts, archaeological sites, astral rifts, first contacts and anomalies no longer go stale when one is replaced by a new entry of the same kind."
- [nomad] "Nomad empires are no longer missing the excavation option in the Infernals event chain."
- [nomad, situation] "Nomadic Evolutionary Predators now gain genetic material from the Pops held by their temporary purge vassals, so completing the Evolutionary Predators situation can offer the phenotype choices for species those vassals consumed."
- [waystation] "Fixed waystation modules that were available in systems with no matching resource, and unavailable in systems that had one."
- [origin, nomad] "Fixed the tooltip for the Prosperous Unification Origin for non-Nomadic Thermophile and Machine species."
- [arkship] "Storm Buildings will no longer throw errors when built on AI Arkships"
- [origin] "The Shroud-Forged origin no longer always starts with a red dwarf."
- [nomad] "Fixed savefiles where nomads inherited a subject with an invalid type."
- [origin] "Empires with the Evolutionary Predators origin can no longer gain species traits from DLCs that are not enabled."
- [nomad, contract, arkship] "Nomad Contract button in arkship view is disabled when there are no contracts to view."
- [nomad, contract] "A Nomadic empire with a subject on Limited Diplomacy now receives contracts again."
- [contract, nomad] "An empire with a subject on Limited Diplomacy now offers contracts to Nomadic empires again."
- [nomad] "The Under the Ice archaeological site no longer offers Nomad empires research they cannot use."
- [nomad] "Nomad empires now receive research from several archaeological sites and Ruler Insight events that previously gave them nothing."
- [forever cruise, contract] "Abandoning a Forever Cruise contract now states how it affects Passenger Satisfaction, instead of only reporting that no reward was earned."
- [nomad] "Nomadic empires are now consistently able to choose Astral Rift event options instead of having them automatically chosen for them."
- [nomad, settle, arkship] "Fixed the colony designation being ignored when a Nomad empire settles an Arkship on a planet."
- [district] "Upgrading the capital building on a Synaptic Lathe once again grants additional district slots."
- [arkship] "Events and decisions that check whether you founded a colony now behave the same on an Arkship as on a planet."
- [arkship] "The loss of an Arkship that you founded now mirrors losing a planet by raising Militarist attraction for pops and Militarist faction."
- [arkship] "Mirroring how it works on planets, separatists on a revolting Arkship can now approach the empire that founded it."
- [arkship] "Planetary Revolts can now happen on Arkships."
- [arkship] "Crime events now continue to happen on Arkships instead of stopping after the first one."
- [arkship] "Other colony events that never triggered on Arkships now work, including the Free Traders Manifesti chain."
- [arkship] "The Empire Designer displays the correct lighting for Arkships sprites."

### AI
- [nomad, waystation, wayline] "Nomad AI no longer builds a redundant Logistics Hub on nearly every Waystation. The build check now looks at the whole connected wayline network for remote collection instead of only directly neighboring systems, so the AI builds at most one hub per network and stops paying stacked upkeep for hubs that did nothing."
- [district, nomad, arkship] "The AI will no longer spam unspecialized districts. The first district of a type is never penalized, and it will consider all available district specializations in case they dramatically change the production of the district. This was most visible on Nomadic Arkships, where Habitation Districts cost Alloys but provided only Housing until specialized - but it spent all of its Alloys on Habitation Districts and could no longer afford the Specialization that would make it beneficial."
- [nomad, harvest, arkship, district] "Nomad AI now recognizes the strategic resources it gathers through harvesting. Previously it assumed it had no income of these resources and refused to build most military and advanced Arkship districts and buildings. It will now build them when its strategic resources stockpile can sustain the upkeep, and hold off when stockpiles run low."
- [nomad, waystation] "Improved Nomad AI Waystation placement. It now steers its network toward high value destinations such as inhabited worlds, resource rich systems and gateways, and favors extending long connected lines instead of scattering disconnected stubs. It also develops the Waystations it already owns before founding new low value ones, while still grabbing genuinely high value systems before rivals can."
- [nomad, arkship, waystation] "Nomad AI now builds new Arkships and upgrades them. Arkship construction was being starved of the single per week megastructure build slot by Waystation construction and now has its own slot. Tier upgrades were never triggered for mobile Arkships and now fire once the enabling technology is researched, spending the resources the AI budget already reserved for them."
- [nomad, arkship, district] "Nomad AI now develops secondary Arkship districts such as Reactor Arrays and Ordnance instead of building almost only Habitation Districts."
- [nomad, arkship] "Nomad AI no longer locks Influence and Alloys into the Arkship construction budget after it has built its full complement of Arkships, so the reservation releases at the cap instead of permanently starving the empire's Influence for decisions."
- [nomad, arkship, spinal] "Nomad AI now correctly saves for Arkship tier upgrades and the Exodus Jump module. These reservations never applied, which was preventing the AI from upgrading its Arkships even with a large Alloy surplus. It also no longer sets aside for the tier 2 and tier 3 upgrade at the same time, since it can only buy one of them. The Exodus Jump reservation is now made only when the empire has the technology and owns an Arkship with a free spinal mount slot, and is released once the module can no longer be built."
- [nomad] "Nomadic AI empires will no longer excavate Archaeological Sites in player owned space, even if you have a Research Agreement. (Note: If they were excavating a site before you claim the system, they will not abort the excavation. They were there first.) This does not prevent player Nomads with a Research Agreement or Archivism traditions from doing so."
- [nomad, arkship] "Nomad AI empires now upgrade an Arkship they already own before building another one. Previously they saved for a new Arkship while an upgrade they could afford went unbought."
- [nomad, district, arkship] "Nomad AI empires now set aside resources for the districts and buildings of every Arkship they own, instead of budgeting as though they had one."
- [nomad, operational reserve] "Nomad AI empires no longer stall their economic plan because they hold energy and minerals as Operational Reserves rather than as a stockpile."
- [district] "AI empires are less likely to leave Urban Expansion districts on Urban World colonies and now prefer specializing them into Commercial Nexus instead."
- [district] "AI empires no longer construct the Institute, Supercomputer, or advanced production center buildings on colonies without a matching research or industrial district."
- [bioship] "Bioship empires now have a food target at every stage of their economic plan, so they keep growing food production through the mid-game, similar to how regular empires have an ever-growing thirst for alloys."
- [settle] "AI empires now settle on a small surplus above each income target, instead of dipping in and out of deficit around it. They also act on a mineral or energy shortage while they still have a stockpile to recover with, and buy a resource on the market before it runs out rather than after."

### UI/UX
- [waystation] "Waystations now show the amount of resources collected every month."
- [waystation] "Improved Waystation stockpile tooltips."
- [nomad] "Science Ships, Construction Ships, and Nomad fleets now share one automation settings window, with options nested under each action in a scrollable list."
- [nomad] "Hints nomad players that they can excavate owned dig sites if they have the Frontier Archaeology tradition"
- [contract] "Fixed Contract notification toasts (accepted, completed, abandoned, failed) showing a redundant empire name and a stray line break in their titles."
- [arkship] "The Outliner now has options to group Arkship components together or break them out separately into Arkship Colonies, Arkship Controls, and Arkship Fleets."
- [origin] "Origin entries on the Empire Timeline now show your empire's origin icon, at every zoom level."
- [origin] "The empire origin's name now also appears at the medium zoom level."
- [origin] "The empire origin on the timeline now shrinks to the smallest zoom level like other entries."
- [gestalt] "Gestalt nodes no longer carry a separate name, and are renamed by their title alone."
- [planet modifier] "Enforced Nemma Slavery Planet Modifier now shows correct icon"
- [contract] "The contract offer and contract completion windows now use the same reward heading."

### Stability / Audio
- [nomad] "Fixed a crash when clicking the locked Nomad toggle in Empire Creation without the Nomads DLC."
- [nomad] "Audio credits updated from First Contact through Nomads."

### Modding
- [blocker] "Added the can_spawn_random_anomaly, can_spawn_random_archaeological_site, and can_add_random_non_blocker_deposit triggers, and a surveyor parameter to the spawn_random_anomaly effect."
- [district, zone, nomad] "Removed AI_PLANET_LOWER_THRESHOLD_AMENITY_CONSTRUCTION, AI_PLANET_UPPER_THRESHOLD_AMENITY_CONSTRUCTION and AI_PLANET_STABILITY_THRESHOLD_AMENITY_CONSTRUCTION, superseded by the new AI_PLANET_AMENITY_TARGET. Added AI_UNZONED_DISTRICT_WEIGHT_MULT, AI_PLANET_HOUSING_TARGET_BUFFER_FACTOR and AI_NOMAD_RARE_RESOURCE_UPKEEP_BUFFER_MONTHS."
- [arkship] "Added the has_upgradable_arkship and owns_arkship_fleets_at_least triggers."
- [district] "Planet classes now support inherit_country_district_modifiers, which blocks the general max district modifiers and leaves only the planet class specific one."
- [arkship] "The `original_owner` and `is_original_owner` triggers behave the same on an Arkship as on a planet."
- [arkship] "The `original_owner` pre-trigger now has the same meaning on an Arkship as on a planet."

### Off-list (Nomads mechanics, no keyword)
- [off-list] "Colony ships are correctly destroyed or transferred when Embarking"
- [off-list] "Added home_colony and background_colony scopes (similar to home_planet and background_planet, but scope to a colony on any carrier type)."
- [off-list] "set_planet_flag, remove_planet_flag, set_timed_planet_flag and has_planet_flag now also work in colony, carrier, and ship scope (previously planet scope only), operating on the same flag store as the carrier_flag family."
<!-- END 4.5 -->

## 4.3 (wiki_patch_notes filter:"all"; 353 changes; codename/date not given by this tool)
No Nomads/Arkship content (pre-Nomads), as expected. No 4.3.x point-release sections. Many Balance entries are collapsed headers ("Economic Rebalancing:", "Biomorphosis changes:", "Synthetic Fertility:", "Habitats:") whose sub-bullets the tool dropped.

### Improvement
- [district] "Job automation is now moved from district to planet. This means that “Technician Automation” will apply to all technician jobs on a planet instead of only jobs granted from the Generator District."
- [district] "Field Modulation, Eco Simulation, and Geothermal Fracking technologies are now marked as Gateway technologies for Rural District Specialization."

### Balance
- [bioship] "Adjusted ship sizes for Cosmogenesis bioships and hive FE bioships"
- [settle] "The Appropriation tradition now also grants +25% Auto Resettlement Chance. (And has an appropriate flavor swap for Egalitarians)"
- [district] "Lithoid Empires start with one more Mining district"
- [origin] "Necrophage Origin:"
- [district] "A mysterious ghost district no longer sometimes appears in the UI for the Synaptic Lathe."
- [settle] "Fixed resettlement costs being inconsistent."
- [district] "Nerfed Replication Hub and Spawning Nest district specializations"
- [origin] "Additional balance pass on origins (separate feature branch from other balancing):"
- [situation] "Obsessional Directive now grants +15% Artisan Drone Job Efficiency, and increased Consumer Goods storage. The Commodities Consolidation situation now grants Artisan Drone Job Efficiency and adds Unity production to Artisans (instead of all Unity)."
- [zone] "The Subterranean Contact Zone now provides job swaps for traders or logistic drones."
- [district] "The Mountain in the Sky deposit from the Gravity Storm has been rebalanced, but allows construction of a unique district specialization."
- [district] "The Aquaculture district specialization now provides 50 jobs of each type, not 75."
- [district] "Anglers and Pearl Divers are now swapped on wet worlds, regardless of district specializations."
- [origin] "Overtuned origin no longer has early access to genetic ascension."
- [district] "The Arcology Project and districts on Ecumenopoleis now cost alloys instead of minerals"

### Bugfix
- [district] "Housing from Mining district modifiers now works correctly on Volcanic, Machine and Hive Worlds"
- [wilderness] "Fixed some Purity traditions having the wrong effects for Wilderness."
- [situation, wilderness] "Fixed Biomorphosis situation showing Cloning and Mutation approaches for Wilderness"
- [situation, wilderness] "Fixed empty tooltip for Purity Approach for Biomorphosis situation for Wilderness"
- [origin] "The Catastrophic Damage on a ringworld from a pre-FTL civilization with the Shattered Ring origin can now be repaired correctly."
- [situation] "Fixed a couple of incorrect string calls in the Biomorphosis situation."
- [origin, district] "Fixed Subterranean origin not giving extra housing on mining districts."
- [planet modifier] "Wenkwort planet modifiers are now correctly removed if the planet stops being a gaia world."
- [gestalt] "Updated the Gestalt version of Chosen of the Eater to match the changes made to the non-gestalt version."
- [district] "Fixed several issues with secondary district names for Spiritualist empires and Ring World districts."
- [hive mind] "Fixed incorrect founder species have happiness calculation resulting in inverted behavior that caused for example hive minds getting happiness bonuses."
- [district] "Tankbound no longer get extra Production Overseers from volcanic generator or mining districts."
- [district] "Exotic Gas Capture district specialization no longer costs twice as much as other specializations."
- [district] "Spiritualist and Materialist empires will now attempt to place their starting Temple or Research Lab in the Archives district specialization."
- [origin, zone] "The Subterranean origin tooltip now mentions the existence of the Subterranean Urbanization zone."
- [wilderness] "Wilderness species should no longer be able to receive forbidden traits from FE interactions."
- [wilderness, biomass] "Wilderness empires now pay Biomass when terraforming a Volcanic World into a normal habitable planet"
- [situation] "Fixed the Galactic Crucible not triggering a notification / event choice after capture in certain situations (after the war)"
- [origin] "The "Scion" origin now blocks the "Scorched World Heralds" civic"
- [gestalt] "Gestalt Empires will no longer gain the "Form a Mercenary Enclave" focus."
- [gestalt] "The Generator Ring World designation is now available to non gestalt empires"
- [situation] "The "Population trials" option from the Synthetic Transformation situation now properly grants the biggest resource deposit."
- [origin, hive mind (variant "Hiveminds")] "Lost Colony Hiveminds now also get the +10% habitability granted by the non hive version of the origin."
- [planet modifier] "The unique planet modifier from Payback now gets properly removed if their capital is conquered before it expires."
- [district] "Thrall Worlds now properly swap out soldier jobs from districts and buildings into battle thralls"
- [origin] "Fixed "Planet Forgers" civics giving access to additional habitable worlds for origins that shouldn't get them (example Shattered Ring)"
- [district] "Fixed the max district tooltips always showing modifiers that grant additional districts to non-artificial worlds, even if you were viewing a habitat."
- [district] "Fixed the max district tooltips not showing percentage modifiers to max districts."
- [district] "Improved how habitat max districts are calculated."
- [blocker] "Planetscapers now gain the technologies to remove blockers on Volcanic Worlds"
- [blocker] "Ranger jobs now receive the correct amount of resources from Volcanic World blockers"
- [origin] "add_skill_without_trait_selection now correctly only levels up without adding traits. (This fixes issues with the initial traits for rulers of empires with the Under One Rule origin)."
- [harvest] "Removed remaining references to pre-4.0 strategic resource harvesting jobs that were converted into job swaps."
- [gestalt] "The Homesteader I trait now correctly grants non-angler/non-gestalt empires their farmers."

### Stability and Performance / UI
- [planet modifier] "Reduced repeated localization lookups when calculating planet modifiers"
- [planet modifier] "Moved more trigger executions from Planet modifier calculation to daily pulse."
- [planet modifier] "Fixed planet modifiers not updating correctly in the colonization view."
- [planet modifier] "Fixed planet modifier icons in Expansion Planner not updating correctly in some cases."

### Modding
- [district] "Added support for districts_per_building for District Specializations. This allows limiting the number of buildings based on the number of built districts but is not currently being used yet."
<!-- END 4.3 -->

## 4.0 (wiki_patch_notes filter:"all"; 276 changes; read ONLY for the Wilderness claims)
Codename given in the section header: "4.0 ‘Phoenix’ Features". DLC section header: "BioGenesis Features". No date. No 4.0.x point-release sections in this output, so 4.0.3 and 4.0.22 are not visible through wiki_patch_notes.
- [header] "4.0 ‘Phoenix’ Features"
- [origin] "Three New Origins" (BioGenesis Features; sub-bullets dropped by the tool, so the three origins are not named)
- [wilderness] "Wilderness-themed City Set, Diplomatic Room, and Advisor Voice"
- [wilderness] "City Sets no longer require Ships Sets to be defined, see Wilderness or Solarpunk as an example"
- [wilderness, biomass] "The resources that appear in the top bar are now scriptable and conditional. See how Wilderness handles the Consumer Goods/Biomass swap as an example"
- No line mentions Tree of Life, sapling, transplant, devastation-death, Wilderness districts or Wilderness jobs.
<!-- END 4.0 -->

## Point releases and dropped sub-bullets (read via stellaris-wiki wiki_page / wiki_page_section, same MCP)
wiki_patch_notes only parses the minor-version page and drops sub-bullets. The wiki pages "Patch 4.4", "Patch 4.5" and "Patch 4.4.X" carry the full text. Version history on those pages: Nomads 4.5 • 4.5.1 • 4.5.2 | 4.4 • 4.4.2 • 4.4.3 • 4.4.4 • 4.4.5 • 4.4.6 | Shadows of the Shroud 4.3 • 4.3.1–4.3.7 | BioGenesis 4.0 • 4.0.2–4.0.23.

## 4.4 header and Nomads Features sub-bullets (wiki_page "Patch 4.4", section 1)
- [header] "**4.4**, aka "Pegasus", was released on 2026-06-15[1] with the checksum 8e23*. The patch was released alongside the [Nomads.png] Nomads expansion. (**note**: patch 4.4.0 was never released to the public instead making patch 4.4.1 the first in the 4.4.X branch)."
- [arkship, spinal] (under "Arkships") "Massive mobile capitals that replace homeworlds entirely. Serve as colonies, shipyards, and warships simultaneously, with three specialization classes (Military, Scientific, Civilian) that can be upgraded through three tiers and customized with modules, systems, and late-game Spinal Mounts."
- [wayline, waystation, harvest, nomad] (under "Waylines & Waystations") "Construct Waystations to harvest resources from systems without claiming them. Adjacent Waystations automatically link into Waylines that propagate trade and modifier bonuses. Wayline Pacts allow two Nomadic empires to share each other's network benefits."
- [contract, settle, nomad, wayline] (under "Contracts") "Settled empires can issue task-driven Contracts (Military, Science, Resource, Diplomacy) for Nomadic empires to fulfill. Completing Contracts earns Trade, improves Opinion and Trust, and unlocks deeper diplomatic options including Wayline Pacts."
- [settle, nomad, arkship, waystation] (under "Settling and Embarking") "After completing the Adaptability or Versatility tradition, Nomadic empires can Settle on an empty planet, with options to settle partially or fully and leave the remainder as a subject. Settled empires can Embark onto Arkships through a mirrored process, converting their starbases into Waystations."
- [nomad] (under "Nomadic Enclave: Champions Forge Live!") "A roving gladiatorial enclave that hosts fleet combat tournaments every twenty years across the galaxy, with weight classes scaling from early- to late-game fleet sizes. Winners hold the Trophy relic until the next tournament. Also offers services including combat intelligence on rivals, tournament venue selection, and introductions to Nomadic Paragons."
- [voidfarer, nomad, origin] (under "New Nomadic Origins" / "Voidfarers") "The general-purpose Nomadic origin. Provides a flexible, unscripted start with no overriding narrative obligation, suited to players who want to define their own nomadic path from the ground up."
- [contract] (Heirs of the Khan) "Begin as survivors of a Khan's assassination, hunted by escalating Marauder bounty fleets. Complete unique military Contracts to weaken your hunters. Survive and potentially claim the throne yourself!"
- [origin] (The Sacred Path) "A Spiritualist origin built around surveying a series of sacred sites scattered across the galaxy by your ancestors. Each site presents a distinct choice about faith and history."
- [forever cruise, arkship, situation, planetfall] (Forever Cruise) "Two species share a single Arkship: a working Crew and a leisure-seeking Passenger class. Balance the Passenger Satisfaction situation to avoid Planetfall on one extreme and Mutiny on the other."
- [cruise] "We’ve also added a special “Hospitality Servitors” variant of the Rogue Servitors civic, where the Passengers are even more demanding and maintaining the cruise is more difficult."
- [settle] (Orun Veyth) "A scarred admiral who lost everything to a purge and has been settling the debt ever since."
- [arkship] (Th'aal Hask'Guron) "A warrior of considerable size and considerable joy, who treats every battlefield like a hunting ground and your Arkship like a trophy hall."
- [arkship] (Seralune) "She boards your Arkship with nothing but polite manners and the feeling she has been somewhere before. Follow where she points."
- [waystation, contract] (New Nomad Civics / Caravan Masters) "Every Waystation is a marketplace, every contract a relationship, and every relationship a potential profit margin."
- [waystation] (Void Reavers) "Not all who wander are lost, some are just waiting for you to let your guard down. Siphon resources from colonies in systems where your Waystations lurk, or skip the subtlety and plunder planets directly with the Plundering bombardment stance. Yarr!"
- [arkship] (Star Seekers) "Life between destinations is its own destination. Gather resources as you travel, then burn through your stockpiles in a galaxy-spanning festival that grounds one Arkship and celebrates everything your people have seen."
- [deck] (Deep Sleep) "Not every passenger needs to be awake for the journey. Manage your population through the long transits between stars, keeping only the hands you need on deck."
- [off-list] (Stellar Cannon) "A purely offensive Megastructure requiring a neutron star or pulsar, buildable without an Ascension Perk. Fires your entire energy stockpile at a target system after a 140-day charge, dealing ship damage and colony devastation (10%/25%/50% at each tier). Shots over 50,000 energy also shift all planets in the system one step up the dryness scale."

## 4.4.2 (released 2026-06-16, checksum 42fe; wiki_page "Patch 4.4.X")
- [forever cruise, situation] "Reduced the rate of the Forever Cruise situation speed on all difficulties."
- [situation] "Significantly reduced the amount that the situation increases based on game difficulty. (These are further reduced from last night’s Open Beta release.)"
- [contract, situation] "Doubled the grace period after completing a crew or passenger contract - it will now pause progress in the passenger satisfaction situation for 24 months."
- [waystation] "Preventing a waystation UI from crashing in some cases."
- [contract] "Fixed a crash that could occur while the AI was evaluating which contract to take on."
- [waystation] "Fix crashing on 800p and lower resolutions when clicking on Waystations."

## 4.4.3 (released 2026-06-17, checksum 0a3d)
- [nomad] "The Void Reavers civic no longer requires both First Contact and Nomads"
- [waystation] "Corrected the Trust and Opinion thresholds required to propose a Waystation Pact"

## 4.4.4 (released 2026-06-24, checksum 5505)
### Improvement
- [contract] "AI will now consider abandoning a contract after 20 years have passed since they have accepted it."
- [arkship, logistic ship, waystation, wayline] "Idle Arkships and Logistic ships will notify the player about having completed the waystation/wayline stockpile collection."
- [arkship, harvest, strip mining (variant "strip mine")] "Arkship and Logistic harvest and strip mine orders issued on a whole solar system now target higher-value planets first instead of simply the nearest."
- [arkship] "Arkship random names in empire creation now use namelists for ships, not planets."
- [arkship] "Fleets will continue repairing and upgrading while following the moving arkship within the system."
- [arkship] "Fleets will continue following the arkship after having completed its upgrades."
- [nomad] "Improved the randomization of nomadic empires in galaxy generation, so they no longer take up the bulk of the advanced AI empire slots."
- [nomad] "Becoming nomadic now embarks assault armies on the planet instead of destroying them."
### Balance
- [forever cruise] "Further reduced the effects that higher difficulties have on Forever Cruise progress."
- [origin, situation] "Deep Sleep civics can no longer be combined with the Synthetic Fertility origin to avoid breaking their starting situation and the identity repository."
- [arkship] "Rebalanced Arkship habitability from species traits."
- [arkship] "Arkship now have an inherent 40% habitability, matching that of habitats."
- [arkship, district] "Arkship Living Space Module grants +5% habitability in addition to the +1 district slot, but has an Exotic Gas cost and upkeep."
- [forever cruise] "Adjusted the starting conditions of Forever Cruise and upkeep of Passengers to not start you in massive deficits. Generally speaking, there are now fewer Specialist Crew jobs present and Passengers (except for Bio-Trophy Passengers) have reduced Consumer Goods upkeep."
- [forever cruise, situation] "Made the Forever Cruise center situation stage longer"
- [forever cruise, contract] "Added a Passenger Satisfaction penalty for abandoning Forever Cruise contracts"
- [nomad, harvest] "Nomad strategic-resource harvesting reworked. Researching a resource's mining technology now grants a +50% harvesting yield for that resource (Volatile Motes, Exotic Gases, Rare Crystals, Living Metal), replacing the previous penalty for lacking the technology. This means that the bonus from the technology now applies to resources inherent to the planet class instead of only those from deposits."
- [waystation] "Tribute Vault and Pirate Hideout waystation modules now add their resources to the waystation's stockpile instead of silently adding them to the empire stockpile."
### Bugfix
- [nomad] "Fixed opinion modifiers from Defender of the Galaxy applying when they shouldn't if the Nomads DLC is not enabled."
- [nomad, settle] "Nomad-exclusive technologies are now hidden if you decide to settle, and re-revealed if you then decide to embark and become nomadic again."
- [origin] "The gas giant created by the Primal Calling origin now starts surveyed."
- [nomad, harvest] "Corrected that mineral_insight_nomad did not provide job efficiency for astro-harvesting jobs."
- [waystation] "The Waystation Research Module now establishes communications with pre-FTL civilizations in its system."
- [arkship] "Planet class modifiers now apply to Arkship (ship-carried) colonies."
- [contract] "Fixed a crash that could occur while the AI was evaluating which contract to take on."
- [nomad, arkship] "Total Wars between Nomads now properly move arkships between owners"
- [waystation] "Preventing a waystation UI from crashing in some modded ui cases."
- [planet modifier, arkship] "Fixed the Planet Modifier for doomsday empires embarking not being applied to their arkship."
- [nomad] "In "Decipher Strange Signal", the nomadic empire needs to be in the system owner's empire to make contact"
- [nomad] "Fixed Nomadic empires not being able to enable L-Gates in some cases."
- [arkship, waystation, wayline] "Arkships no longer benefit twice from waystation network (wayline) modifiers."
- [deck] "The Utility Deck specialization now correctly supports industrial buildings."
- [nomad] "Fixed Specific Cetana events being broken for Nomads"
- [settle, nomad] "Fixed delayed starbase spawn upon settling as Nomads"
- [contract] "Fixed an instance of the Assist Colony special project not spawning properly upon accepting the contract"
- [situation] ""Cetana: Weak Links" entry stays inside the borders of the Situation Log window."
- [situation] "Changing the situation approach will now update the effect list faster - some edge cases used to delay the changes in the effects for a few in game days."
- [contract] "Abandoning an internal contract no longer sends an issuer toast"
- [contract] "The on_cancel effect only fires once for internal contracts"
- [nomad] "Ignore border restrictions for nomadic countries in uplift-species special projects"
- [nomad] "Fixed Recall paladin ability to work for nomad players"
- [contract] "Fixed Stellar Ignition Contract being impossible to complete"
- [situation] "Fixed Wanderlust event chain re-appearing in the situation log"
- [forever cruise, situation] "Lowered the Forever Cruise situation speed change based on difficulty"
- [situation] "Enabled the Sacred Path event chain to remain in the Situation Log after completing all Sacred Sites, so players can still find them on the galaxy map."
- [contract, forever cruise] "Fixed internal contract spawn chances for Forever Cruise"
- [arkship] "The Doomsday Arkship modifier now speeds up decisions, not buildings"
- [nomad] "Fixed some Patron callings and deeds that were inappropriate for Nomadic Empires showing up."
- [gestalt] "Fixed the tooltips for some Patron accords not showing the correct tooltips for gestalt empires."
- [arkship] "Fixed the tooltips for when an Arkship couldn't perform an action being confusing in some cases."
- [situation] "You can no longer distribute Zro or Astral Threads during the Psionic Ascension situation if you run out of Zro or Astral Threads."
- [situation, nomad] "Astral Scars spawned during the Psionic Ascension situation are now revealed to Nomadic Empires."
- [arkship, origin] "Arkship Preparation for the Doomsday origin now correctly reduces the time to embark."
- [situation] "Added a fallback if the Cyberization situation gets stuck with less than 100 pops to cyberize"
- [arkship] "Arkships can now research the special project in Crystal Manufactory"
- [situation] "The Breach the Shroud situation now recognizes all types of Psi Corps buildings"
- [nomad] "Players without the Nomads DLC can again create Nomadic empires in multiplayer when the host owns the DLC."
- [arkship] "Fixed Arkship afterburners not upgrading to tier 2 when Advanced Afterburners is researched."
- [devastator] "Fixed Stingers and the Devastator Mount not obeying the listed firing arcs for their weapons in some cases."
- [nomad, arkship] "Fixed civic-granted starting buildings not being added on Sacred Path Nomad Arkships (Experimentation Chambers, Sanctuary of Repose, Astrometeorology Observatory, Sacrificial Temple)"
- [nomad, arkship] "Fixed Experimentation Chambers not being added on Hive/Machine Nomad Arkship starts"
- [forever cruise] "Fixed Forever Cruise empires incorrectly receiving Luxury Residences on game start"
- [nomad] "Event generated Nomad empires will no longer get an asteroid"
- [arkship] "Fixed Arkship move button incorrectly disabled"
- [contract] "The Abandon Contract button now warns about the consequences in its tooltip."
- [nomad, settle] "Fixed duplicate colony names in the diplomacy tooltip after a nomad empire settles."
- [situation] "Fixed a Special Project icon that could appear underneath the star in certain situations."
- [contract] "Fixed accepted contracts showing "Available Until: 0" in the issuer's tooltip."
- [arkship] "Improved unhelpful modifier tooltip labels on Arkships."
- [situation] "Fixed situation approaches not working after a repeating situation completed a cycle."
- [harvest, arkship] "Harvesting modifiers for Civilian Arkships are now working correctly."
- [nomad] "Fixed some resource mining technologies being blocked for Nomadic empires."
- [settle, waystation] "Fixed issues in which settled empires that Embarked could retain starbases and borders. Clarified the tooltip for Waystation conversion in English."
- [nomad] "Expulsions wars will no longer move stations nomads are not allowed to build before reverting their ownership"
- [nomad, waystation] "Nomad Raids now properly transfer waystations on status quo's in both directions"
- [operational reserve] "Operational Reserves tooltip now mentions the 1-3 scaling of energy/minerals to operational reserves"
- [arkship] "Fixed numerous modifiers (such as the Stability from Bureaucrats granted by Byzantine Bureaucracy) not granting their modifiers to Arkships. Note to modders: Any scripted modifier needs to be updated to work on colonies rather than planets."
### AI
- [contract] "AI will no longer accept unreachable contracts and will abandon contracts that become unreachable."
- [nomad] "Fixed nomadic AI empires not merging their armies into a single transport fleet."
- [nomad] "Fixed AI not selecting hostile, domineering, threatened or imperious attitudes if either country is nomadic."
### UI/UX
- [spinal] "Selection box remains in the correct section after building a System or Spinal Mount"
- [waystation] "Fix waystation stockpile max capacity rounded to thousands"
- [situation] "Fixed the Situation Log selection jumping back to the first entry when scrolling."
- [contract] "Fixed the position of the remove-contract button."
- [waystation] "Waystation UI now shows income to two decimal places."
- [waystation] "Waystations that are collecting excessive resources now have more room to display their icons in the system map."
### Audio
- [nomad] "Stellaris Complete Soundtrack updated with Nomads tracks and album artwork."
- [arkship] "Correct sound now plays when arkships are attacked."
### Off-list (Nomads mechanics, no keyword)
- [off-list] "Added Logistics Hub indicator in the galaxy map."
- [off-list] "The Doomsday modifier you get for successfully embarking now grants +60% habitability"
- [off-list] "Fixed the Safe Haven modifier for embarking as a doomsday empire not having an icon."
- [off-list] "The Doomsday event to unlock Embarking once again unlocks embarking."
- [off-list] "Blocked Shroud-Forged from Embarking."
- [off-list] "Fixed the outliner planet tooltip incorrectly reading "Carried by"."
- [off-list] "Added collected_colony_resources allowing starbase modules to steal or clone resources from colonies in the same system."

## 4.4.5 (released 2026-07-07, checksum 2f57)
Note: many 4.4.5 lines reappear verbatim in the 4.5 notes; they are listed here under 4.4.5 as well, because that is where they first shipped.
### Feature
- [waystation] "Added Waystation Voidlure modules"
- [arkship] "Added Arkship Voidlure Array module that allows you to right-click on a system to lure space fauna."
- [nomad, arkship, logistic ship, harvest, strip mining (variant "Strip Mine"), waystation] "Nomad Arkships and Logistic Ships can fine-tune Harvest, Strip Mine, and Deep Scan automation, including whether to Harvest unsurveyed, depositless, waystation, friendly, or hostile-owned worlds, and whether to rescan when Deep Scanning."
- [arkship] "Added the Explore automation order to Arkships. Note: Arkships with the Explore order will attempt to navigate to Points of Interest if possible."
### Improvement
- [waystation] "Space Fauna now disable waystations instead of destroying them. They will be re-enabled once the fauna leaves or is destroyed."
- [logistic ship, harvest, waystation] "Logistic Ship automation now defaults to on for Harvest Resources and off for Waystation collection."
- [hive mind, forever cruise] "Hive Mind Forever Cruise empires with machine passengers can research machine assembly technologies to upgrade their passengers"
- [forever cruise] "Regular Forever Cruise empires with machine passengers start with the Robot Assembly Plants building"
- [forever cruise, situation] "Added new event options to the Forever Cruise situation stage events, allowing players to accept temporary economic penalties to affect the situation's direction."
### Balance
- [operational reserve] "Operational Reserves Changes:"
- [operational reserve] "Operational Reserves now track Energy and Minerals 1:1 (previously 3:1)."
- [operational reserve, arkship] "Maximum Operational Reserves now scale with the number and tier of owned Arkships (base 15,000, plus 5,000 / 7,500 / 10,000 per Tier I / II / III Arkship) instead of a fixed cap."
- [arkship, operational reserve] "Civilian Arkships grant an additional 2,500 Operational Reserve capacity per Tier."
- [arkship, waystation, nomad, operational reserve] "Resource Silos, Fallen Empire Resource Silos, Arkship Resource Silos, and Waystation Logistics Silos now raise a Nomadic empire's maximum Operational Reserves."
- [operational reserve] "Added a Critical stage to the Operational Reserves track with severe research, Unity, and Alloy penalties; the Festival, Conserve, and Burn approaches now gate on reserve stage rather than fixed thresholds."
- [operational reserve, reserves] "The Critical Operational Reserves stage now also penalizes the populations the reserves sustain: Lithoid pops lose Happiness, pop growth, and Job Efficiency, and Mechanical and Machine pops lose Job Efficiency."
- [arkship] "Rewards from the Arkship Readiness Checklist now last 20 years."
- [nomad, strip mining (variant "strip-mining"), reprocess] "Reduced the opinion penalties empires hold against Nomadic Empires for strip-mining, reprocessing planets, and Exodus Jumps, and made these penalties decay more quickly."
- [nomad, arkship] "Nomadic Primal Calling now starts with a Voidlure Array equipped on the Arkship"
- [nomad] "Removed the nomadic tradition swap for Domestication tradition finisher"
- [situation, forever cruise] "Rebalanced the Passenger Satisfaction situation for Forever Cruise to allow staying in the middle stage longer"
- [arkship, district, deck] "Arkship District Specializations that increase weapon damage (Laser Capacitors, Flight Decks, Ammunition Lines, or Missile Silos) now also add +0.15 strategic resource upkeep to Soldiers."
- [arkship] "Species with Mutagenic Habitability living on Arkships will now gain 50% habitability (like Regular Arkship Habitability and Habitat Auto-modded habitability) instead of 30%"
- [forever cruise, gestalt] "Rogue Servitor Forever Cruise Passengers now provide an additional +1% Complex Drone Job Efficiency on top of the base Gestalt bonus."
### Bugfix
- [arkship] "Arkships now have a 10 year cooldown after benefiting from a Max Aura effect, meaning you can't chain them to get a level 10 ascension Arkship super early."
- [nomad, waystation] "Nomads can now build Megastructures in a system containing one of their Waystations even if the system has not been surveyed."
- [harvest, contract] "Harvest Resources Contracts will now be cancelled if a different empire harvests the resources first." (line appears twice in 4.4.5)
- [settle] "Fixed a very rare case where if you settled and left the event open for too long another empire could claim the system. The fix is delaying them from claiming the system for 300 days. Please make a decision before then."
- [arkship] "Added a fallback if your arkship was in a hyperjump when progressing through the adAkkaria event chains"
- [arkship] "Due to legacy script your capital arkship was never able to find precursors"
- [contract] "Stopped Scan Debris Contract from auto-cancelling on accept"
- [nomad] "Blocked the Sectors Map Mode for Nomads."
- [district] "Fixed issues with Payback and Broken Shackles district specializations."
- [nomad] "Bio-Repurposing tradition now provides the nomadic effects for nomadic necromancers instead of the necromancer effects."
- [waystation] "Brought the cost and upkeep of the Nanite Hub waystation module in line with the equivalent starbase building."
- [harvest] "Fixed Astro Harvester job modifiers not applying in some cases."
- [harvest] "Harvesting a celestial body that has no deposit no longer incurs a diplomatic opinion penalty."
- [cruise, forever cruise] "The Creativity Exhibition Vault now boosts Cruise Passengers for Forever Cruise empires, instead of applying its bonus to Bio-Trophies they do not have."
- [arkship] "Buildings that can be built on a planet or its orbital ring (Psi Corps, Planetary Shield Generator, Galactic Stock Exchange, Embassy, Noble Estates, Slave Processing Facility) can no longer be built as both a colony building and an Arkship starbase building at the same time."
- [arkship] "Negative arkship modifiers from Grand Refurbishment and Operational Checklist are removed when another empire conquers the arkship, so you aren't stuck with a bunch of negative modifiers and no way to remove them."
- [arkship] "Arkship can now issue a survey command by clicking the survey button and clicking on the system in galaxy map"
- [waystation, arkship] "Fixed Waystations, Arkships and modded starbases that have forced components from starbase modules and buildings regressing their designs and losing components."
- [waystation] "Fixed a crash when updating waystation networks"
- [arkship] "Arkship construction notification now mentions an arkship, not a starbase"
- [nomad] "Fixed the End-Bringers' shrouded world failing to spawn in games featuring Nomadic empires."
- [arkship] "Fixed Arkships unintentionally syncing to the same auto-generated design. Building or upgrading one Arkship will no longer sometimes overwrite armaments or utilities on others of the same type."
- [situation, nomad] ""Secrets of the Past" event from "Biomorphosis" Ascension Situation blocked for Nomads"
- [nomad, waystation] "Nomads will no longer get double the benefit from their own resource producing megastructures. Megastructures will no longer be giving resources directly and a waystation will always be needed to collect those resources."
- [forever cruise] "Rogue Servitors can no longer take a machine species as their secondary species when playing as a Forever Cruise. The machine can not be pampered."
- [reserves (variant "Operation Reserves")] "(Beta only) Increased the Shrinkspace Depot and Extra-Dimensional Depot effects on Operation Reserves from 500 to 2500 and 1000 to 5000."
- [arkship] "Pops can now migrate from Arkships"
- [hive mind, forever cruise] "Hive Mind Forever Cruise empires with machine passengers start with the Machine Assembly Plants building"
- [hive mind, forever cruise] "Hive Mind Forever Cruise empires start with the passenger Population Controls set to No, which allows the species to grow or be assembled"
- [forever cruise, origin, cruise] "When starting a new game as a Forever Cruise Origin, crew species will correctly get assigned "cruise_founding_crew" flag. Various effects depends on it being set properly."
- [nomad, waystation] "Added tooltip saying that Nomadic Empires require a waystation in the system to open the L-Gate"
- [nomad, waystation] "Added tooltip saying that Nomadic Empires require a waystation in, or adjacent to, a system to exchange minor artifacts for precursor insight"
- [harvest, arkship, situation] "Added tooltip that highlights that resources need to be harvested with the Arkship in the "Arkship Readiness" situation"
- [nomad, hive mind] "Nomadic Hive Minds will no longer have access to the Evolutionary Predators decision make planets into hive worlds"
- [contract] "Fixes contracts that had no issuer correctly set"
- [contract] "Abort contracts if the issuer ceases to exist"
- [nomad] "Fixed Nomadic empires being blocked from excavating their story-locked dig sites that sat in foreign owned systems."
- [arkship] "Fixed Arkships sometimes giving up on Surveying."
- [arkship, nomad] "Arkship habitability preference can now be applied by species modification if your empire is nomadic."
- [forever cruise, origin] "Bio-trophies now give 10 Political Power when playing as Rogue Servitor with the Forever Cruise Origin"
- [forever cruise, contract] "Removed the habitability requirement for the various Shore Leave internal Forever Cruise contracts."
- [forever cruise] "Machine Intelligence and Individualist Machine Forever Cruises with machine passengers now alternate assembly every month."
- [cruise] "Cruise crew and passengers no longer incorrectly assigned Grid Amalgamation or Livestock living standards and slavery types."
- [forever cruise] "Individualistic Machine Forever Cruise no longer gets an error from placing Robot Assembly Plants at game start."
- [forever cruise] "The Package Deal living standard in Forever Cruise now grants political power to employed Economy Class Passengers."
- [forever cruise, situation] "Added missing event sounds to the Forever Cruise situation"
- [waystation, contract, nomad] "The Build Waystation contract is now longer automatically abandoned for players when at double station capacity, only for AI nomads"
- [forever cruise] "Permanent Employment in Forever Cruise now only zombifies the crew, not the passengers"
- [arkship] "Fixed issues when dismantling Beastports on Arkships."
- [wayline, waystation] "Fix waylines persisting on galaxy map after waystation removal"
### AI
- [arkship] "The AI will now once again assign leaders to sector capitals as well as newly built arkships."
### UI/UX
- [operational reserve, situation] "The Operational Reserves situation now shows its capacity breakdown and stored/total readout in the Situation Log instead of an empty Finisher Effects panel."
- [operational reserve] "The Operational Reserves topbar tooltip now shows a predicted monthly change with per-resource Energy and Minerals contributions, each with a resource icon and coloured +/- value, and a Hold-ALT hint to expand the produced/consumed breakdown."
- [nomad, arkship] "When enabling nomads in empire creation, the civilian arkship is now selected by default."
- [arkship, blocker] "Arkships no longer show false clearable-blocker icons in the outliner."
- [arkship] "Fixed incorrect Arkship button texts in the planet view."
- [situation] "Fixed a target visibility bug when switching between situation log entries."
- [contract, situation] "Fix Remove Contract button hover hitbox in Situation Log"
- [nomad] "Science Ships, Construction Ships, and Nomad fleets now share one automation settings window, with options nested under each action in a scrollable list."
### Modding
- [situation] "Situations can define total_progress with per-stage section_weight for progress bars whose length and stage bands scale at runtime, with auto-generated _max_progress_add/_mult country modifiers and a situation_progress_percent trigger."
- [situation] "Situations, stages, and approaches support custom_tooltip and custom_tooltip_with_modifiers (replacing the approach-only active_tooltip), and situations can override their Situation Log Active Effects and Finisher Effects panel titles and bodies."
- [arkship] "Added the orbital_ring_and_arkship_equivalent_absent scripted trigger."
### Off-list (Nomads mechanics, no keyword)
- [off-list] "Upgrading fleet will reissue upgrade order if one got interrupted because of the mobile starbase jumping away to another system."
- [off-list] "Added home_colony and background_colony scopes (similar to home_planet and background_planet, but scope to a colony on any carrier type)."

## 4.4.6 (released 2026-07-08, checksum fdde)
- No keyword hits (two automation bugfixes: Astral Rift science ships lost; automated ships dropping tasks when avoiding hostiles).
<!-- END 4.4.X -->

## 4.5 header, dropped sub-bullets and missed lines (wiki_page "Patch 4.5", saved full text; keyword lines diffed against this file)
- [header] "**Patch 4.5**, aka "Cygnus", was released on 2026-09-22[1] with the checksum 8697*."
- [wayline, nomad] (sub-bullet of the Federation laws entry) "Trade League federations now also provide free, automatic Wayline Pacts between all members. (Requires Nomads)"
- [arkship] (sub-bullet of "Added a nomad compatible version of Storm Chasers") "Storm Attraction is now fully functional for mobile arkships"
- [origin] (sub-bullet of "Consecrate World changes:") "Consecrate World can now be declared on a curated set of worlds tied to your empire's origin and traits (such as Gaia and relic worlds, and origin-specific worlds like toxic, shrouded, rift, or arc-furnace worlds)."
- [nomad] (sub-bullet of the soldier/knight swap entry) "Knight swaps should now work properly for nomads"
- [nomad, arkship, waystation] (sub-bullet of "Fixed numerous issues with Total Wars:") "Nomadic Empires will now gain ownership of defeated Arkships and Waystations after Total War status quo victory."
- [nomad] (same) "Nomadic Empires will now steal a percentage of the population from planets they destroy during Total Wars."
- [arkship, settle] (same) "Arkships defeated by Settled Empires are now destroyed after Total War status quo victory."
- [nomad] (same) "Nomadic Empires can now be defeated in Total Wars."
- [arkship] (top-level line missed in the first pass) "The "Assault Armies" and "Shipyard" buttons on the Arkship Ship Controls tab are now properly localized in all languages"
- [nomad] (missed) "Homicidal Nomad empires now have a valid Total War Behavior policy option."
- [bioship (variant "biological ships")] (missed) "While using the ship designer on biological ships, the designer window no longer blocks the player from interacting with the edit section buttons."
- [settle (resettlement)] (missed) "Several interface lists could keep showing outdated entries after what they listed was replaced. This affected the diplomacy vote and favors screens, envoy selection, the planet population and jobs views, resettlement, the species list, megastructure design selection, fleet home base selection, orbital lists and agreement terms."
- Note: the wiki page italicises a block of late 4.5 Bugfix lines (Astral Rift options through "The Empire Designer displays the correct lighting for Arkships sprites.") and the two `original_owner` Modding lines. They are already quoted above under 4.5.

## 4.5.1 (released 2026-09-24, checksum 358e; wiki_page "Patch 4.5.X")
- [nomad] "A Synaptic Lathe built by nomads in another empire's system no longer changes ownership to the owner of the Starbase."
- [harvest] "Removed the Harvesting Agreements Automation sub-menu setting, since the Trade Action has been removed"
- [waystation] "Fixed a rare crash when a waystation was destroyed after its controlling empire had already been eliminated."

## 4.5.2 (released 2026-10-06, checksum 9776)
### Feature
- [nomad, arkship] "Nomadic Knights of the Toxic God who tame or slay the Toxic God can now use their Arkships to Detoxify Toxic Terraforming Candidates into habitable worlds."
- [arkship, nomad] "Added the Weapon Preferences (Arkships) policy for nomadic empires. It sets the point defense of all Arkships and the Military Arkship Armaments."
- [waystation, nomad] "Added the Defense Preferences (Waystations) policy for nomadic empires"
- [logistic ship, waystation, arkship] "Logistic Ships now show Waystations in the Megastructure Build Menu alongside their dedicated Build Waystation Button for consistency with Arkships."
### Balance
- [operational reserve] "The Conserve approach for Operational Reserves now gives -50% Specialist Job Efficiency, instead of -50% output for Bureaucrats and Researchers."
- [gestalt] "For Gestalt empires, the Covenant with the Cradle of Souls now gives Unity from Psionic Pops, instead of a percentage bonus to Telepath Unity."
### Bugfix
- [arkship] "Fixed the ship components for Arkships not adding to their upkeep."
- [nomad] "Nomadic empires no longer have the starbase and defense platform preference policies"
- [waystation] "Updated waystation collection prioritisation by using a monthly cache for percentage of stockpile used."
- [nomad, settle] "Nomadic Knights of the Toxic God can now settle once they reach the Toxic God system, and keep the Order's Keep and their Knights on the new homeworld."
- [nomad, arkship] "Nomadic Knights of the Toxic God no longer get stuck when they lose the Order's Keep Arkship. The Quest for the Toxic God now locks while the Arkship is lost, and ends if it is destroyed."
- [nomad] "The extra moon of Earth for Nomadic Knights of the Toxic God starting in Sol is now named appropriately."
- [origin] "Fixed issues with the Knights of the Toxic God origin tooltips"
- [district, nomad, origin] "Fixed Prosperous Unification not giving additional districts for Nomadic empires. Also improved the tooltip for the Nomadic version of the origin."
- [bioship] "Bioship living metal armor now mirrors regular living metal armor, including hardening and hull regeneration."
- [nomad, arkship] "Nomadic Synthetic Fertility no longer starts with a size 20 Arkship."
- [origin] "Fixed civics and origins trying (and failing) to give traits to Envoys."
- [arkship] "Fixed the Adaptive Architecture tradition referring to Arkships for Void Dwellers"
- [district] "Fixed Void Dwellers ignoring the thermophile district initialization."
- [hive mind (variant "Hive-minds")] "Hive-minds no longer gain unworkable farmer jobs when choosing to eat titanic life."
- [nomad] "Fixed Gateways, Hyper-Relays and the Grand Archive not being buildable by Nomadic empires in some cases."
- [district] "Fixed Democratic Transference only increasing the energy upkeep of districts, not all upkeep."
- [situation] "The Planetary Revolt situation now names the empire that the colony will join in its outcome description."
- [hive mind] "Hive Mind and Machine Intelligence empires that back a revolt now receive the correct message."
- [nomad, arkship] "Fixed several issues with disabled Nomad Arkships being re-enabled ahead of time."
- [nomad, arkship] "Nomads that win a war as a secondary participant no longer keep a permanently disabled Arkship."
- [arkship, nomad] "A recovered Arkship comes back repaired, and the Recover Disabled Nomad Vessel special project can be offered again if the first attempt runs out of time."
- [situation] "Fixed high Councilor levels inverting the Test Subject losses of the Experimental Testing situation, which added Pops instead of removing them."
- [gestalt] "Covenant tooltips now show the correct effect for Machine and Gestalt empires."
- [wilderness] "Blocking the Kaleidoscope event from firing for Wilderness Empires in several different circumstances."
### AI
- [logistic ship] "Logistic Ships dropoff automation now does a monthly check of how full the ship's stockpile is, instead of using a step function"
### UI/UX
- [arkship, waystation] "Renamed the weapon and defense preference policies to Weapon Preferences and Defense Preferences, with the Fleets, Starbases, Defense Platforms, Arkships or Waystations they apply to in parentheses, so they appear together"
### Stability/Performance
- [waystation] "Fixed a waystation network sometimes keeping a stale reference after a starbase was destroyed under occupation."
- [nomad] "Reducing micro freezes in late game with nomad AIs"
### Modding
- [situation] "Scripted trigger `basic_eligibility_for_separatism_joining_trigger` is now `separatism_joining_eligibility_trigger`, and it takes the rebelling species and the situation as parameters."
<!-- END 4.5.X -->

## 4.3 header and dropped sub-bullets (wiki_page "Patch 4.3", saved full text; keyword lines diffed against this file)
- [header] "**Patch 4.3**, aka "Cetus", was released on 2026-03-17[1] with the checksum 2984*."
- Dropped as false positives: "Cruisers" lines (match "cruise") and "Genesis Preserves" lines (match "reserves").
- [settle (resettle)] "Resettlement costs are now the same across all strata. (Since demotion is now immediate at the end of the month, this prevents an undesired element of micromanagement where you would want to fire any pops you wanted to resettle.)"
- [bioship] "No bioships use Siege Combat Computers, as Mauler mandibles already do a fine job of eating Starbases."
- [situation] "Ascension situations now progress slower."
- [district] "More strategic resource production has been moved to the strategic resource producing district specializations. Significantly reduced the amount given for free by the strategic resource deposits without the specialization."
- [district] "Automation Buildings are now job specific, and no longer increase their upkeep based on district count."
- [reprocess] "The Metabolic Reprocessing now also gives robot assembly from Augmentor jobs and organic assembly from Roboticist jobs."
- [situation] "The Augmentation Bazaars building from the civic of the same name now counts as an Augmentation Center for the purposes of the Cybernetic ascension situation."
- [district] "Infernal Tankbounds now begin with three additional Generator districts. Other Tankbound empires will now convert one less Farm into a Generator."
- [district] "Tankbound Production Overseers now generate 2 Engineering Research instead of Unity, and grant +2% efficiency and +200 Amenities. They are only granted by Urban districts (or as Clerk swaps)."
- [district] "Habitat dwellers can now build rare crystal extraction specializations in their mining districts and both exotic gas and volatile mote extraction specializations in reactor districts."
- [hive mind] "Habitat capital buildings now provide hunter seeker drones for both machine intelligences and hive minds. They no longer provide logistics drones for machine intelligences."
- [district] "Known Issue: We are aware that Planetary Automation is not building enough Districts."
- [gestalt] "The Manifesti Social Commentary Piece specimen now increases Faction Output. (Reduces Deviancy by a small amount for Gestalts.)"
- [origin] "The origin now also comes with +15% Computing research speed and increased draw chance for the aforementioned technologies."
- [district] "Mutagenic Spas no longer scale with the number of districts and provide pop growth or assembly."
- [district] "Mutagenic Spas can now be placed in research or unity district specializations"
- [origin] "These changes mean that the pop growth weirdness of the origin should be gone and planets with Necrophyte Apprentices should retain a high rate of growth of the secondary species, with the once per decade rituals mass converting pops to the primary species."
- [hive mind (variant "hive-minded")] "When playing a hive-minded necrophage, the force spawned pre-ftl empires are no longer hive-minded."
- [origin] "The Survivor Species trait (from the Post-Apocalyptic Origin) now also grants a reduction to housing and amenities usage."
- [origin] "The Colonial Spirit modifier (from the Lost Colony Origin) now also grants 10% pop growth (pop assembly as appropriate)"
- [origin] "The Storm Chaser origin can no longer build storm shelters, but will instead benefit from their effects through their capital buildings as soon as they research the related technologies."

## 4.3.1 to 4.3.7 (wiki_page "Patch 4.3.X")
Dates: 4.3.1 2026-03-19 (f646), 4.3.2 2026-03-24 (171a), 4.3.3 2026-03-31 (456f), 4.3.4 2026-04-16 (5f74), 4.3.5 2026-04-29 (df6e), 4.3.6 2026-05-11 (6ccb), 4.3.7 2026-05-11 (4f3c). No Nomads content in any of them, so Nomads did not straddle a 4.3/4.4 boundary. 4.3.1 and 4.3.7 have no keyword hits.
### 4.3.2
- [wilderness] "Halved the Empire Size penalty from colonies for Wilderness"
- [bioship] "Machine, Lithoid, and Infernal empires that use Bioships now allocate their food budgets for colonization correctly."
### 4.3.3
- [district] "Automation will once again build districts, and will build buildings or districts that do not directly add jobs (like Mineral Processing Plants or City Districts) if they are in the build list for that type of colony."
- [gestalt] "The Specialist Training tradition now properly works for Gestalt empires"
- [situation] "The Ragged Flags planetary modifier from the pre-ftl plague situation now has a duration and will eventually expire"
- [gestalt] "Fixed Terraforming for Gestalt AI initiating concurrent hive/machine worlds terraformings leading to economic struggles"
- [blocker] "Adjusted AI Unity budgets so they won't always hold a third of their Unity in reserve just in case they get a Shroud blocker."
- [gestalt] "Non-Gestalts on Shattered Rings now have access to the Generator World colony designation"
- [district] "Improved AI weighting and decision-making around Urban Districts."
- [wilderness] "AI now realizes that Wilderness Harmonic Cores and Singing Cliffs produce Unity, Moltguts produce Alloys, and Threadways produce Trade."
### 4.3.4
- [district] "High tier habitat capitals can now gain +1 habitat district per major body. Corrected tooltips."
- [origin] "Fixed the "Volcanic Forge" technology from "Planet Forgers" not appearing for certain origins"
- [bioship] "Adjusted AI food budget so bio-ship empires will rebuild their fleets if they recently lost a war."
### 4.3.5
- [district] "Volcanic World districts no longer disappear when terraforming to a Machine World or Hive World."
- [district] "Machine Worlds can now be assigned a Trade Colony designation for individual Machine empires, and colony automation correctly handles trade districts and specializations on such worlds."
- [district] "The Virtuality tradition's bonus jobs now correctly scale per district rather than applying a flat bonus per planet."
- [origin, district] "Tankbound empires with the Void Dwellers origin now correctly receive reduced starting pops and properly converted habitat districts."
### 4.3.6
- [hive mind] "Megastructures, Habitats, Ascension Perks, Indoctrination, Advanced Slavery, Hive Mind empires, Fanatic Purifiers, and more." (sub-bullet of "Utopia contains:", Utopia rolled into the base game)
- [origin] "10 humanoid portraits, the Master Artificers, Pleasure Seekers, and Pompous Purists civics, the Clone Army origin, the humanoid shipset, and more." (sub-bullet of "Humanoids contains:")
<!-- END 4.3.X -->

## 4.0 header and 4.0.x Wilderness lines (Wilderness claims only; wiki_page "Patch 4.0" and "Patch 4.0.X")
- [header] "**Patch 4.0**, aka "Phoenix", was released on 2025-05-05[1] with the checksum 1ae3*. The patch was released alongside the [BioGenesis.png] BioGenesis expansion. (**note**: patch 4.0 was never released to the public instead making patch 4.0.1 the first in the 4.0.X branch)"
- [wilderness, gestalt] (sub-bullet of "Three New Origins") "Wilderness: Begin as a sapient planetary ecosystem, a living gestalt of countless lifeforms united in harmony, seeking to spread its consciousness to the stars."
- [wilderness] (Modding) "When a resource is spent to build a building or enact a decision, it is stored locally and can be accessed in script such as “on_queued” and “on_unqueued”, see Wilderness buildings for an example"
- [wilderness] (Modding, Game Rules) "Added the should_colonize_use_terraform game rule, used to get the behavior Wilderness has with terraforming"
- [wilderness] (Modding, Game Rules) "Added the should_hide_pop_group_size and should_hide_planet_sector_pop_amount which Wilderness uses to obfuscate the amount of pops on a planet"
### 4.0.3 (released 2025-05-06, checksum 3b8a)
- [wilderness, biomass] (Features) "Monthly change in Biomass is now shown in the resource bar for Wilderness empires."
- [wilderness, biomass] (Bugfix) "Devouring Wilderness now correctly generates biomass from eating pops"
- [wilderness, district] (Bugfix) "You will no longer be tasked with making an agricultural district if you are a Wilderness Empire and an Orchard Forest if you are a regular empire."
- [wilderness] (Bugfix) "Fixed some tooltips mentioning regular jobs for wilderness"
- [wilderness] (Bugfix) "Wilderness Deposits have been contained and will no longer be spawning across the galaxy. (Fixes systems generating with no resources.)"
- [wilderness] (Bugfix) "The Wilderness can no longer build multiple shield generators, effectively becoming immune to bombardment."
- [wilderness, biomass] (Bugfix) "Fixed numerous event buildings not having a biomass cost for Wilderness"
- [wilderness] (Balance) "Wilderness and Nascent Stage are now incompatible"
- [wilderness, district, biomass] (Balance) "Districts for Wilderness Empires now cost 50 Biomass"
- [wilderness, biomass] (Balance) "The expand planet decision for Wilderness now costs 5×(Planet Size)^2 Biomass and 50 Influence"
- [biomass] (Stability and Performance) "Significantly improved how Biomass is calculated"
### 4.0.5 (released 2025-05-08, checksum 408d)
- [wilderness, biomass] "FE buildings have been made available to Wilderness empires (under the same restrictions as normal), and have received an additional biomass cost."
- [wilderness] "Increased the amount of workforce Wilderness pops gain to 99999, this way if you ever have more than 1000 of a job the wilderness won't start trying to employ pops like a normal empire"
- [wilderness] "Wilderness Empires will no longer be able to build the automation buildings only to destroy them at the start of the next month"
- [wilderness, biomass] "Wilderness Biomass will no longer refuse to work simple drone jobs, back to work you malleable pile of goo!"
- [wilderness, biomass] "Reduced the biomass cost for planetary expansion for wilderness by roughly 50%"
### 4.0.6 to 4.0.17 (selected Wilderness lines)
- [wilderness] 4.0.6 (2025-05-09) "The Avatar Cortex building for wilderness should no longer delete itself upon being upgraded to max level."
- [wilderness, biomass] 4.0.10 (2025-05-15) "Bloomed Biomass is now prioritized if unqueueing a Building that costs Biomass on Gaia worlds."
- [wilderness, biomass] 4.0.10 "Fixed the Expand Planet Decision for Wilderness refunding the Biomass spent"
- [off-list: devastation] 4.0.14 (2025-05-27) "Planetary Habitability and Devastation should no longer overlap"
- [wilderness, strip mining (variant "Strip Mine")] 4.0.15 (2025-06-04) "The Wilderness will no longer be taunted by the Strip Mine Planet decision, it is now hidden."
- [wilderness, biomass] 4.0.16 (hotfix, 2025-06-05, 05ae) "Bugfix: Wilderness Empires will no longer destroy themselves when spending biomass"
- [wilderness] 4.0.17 (2025-06-11, b786) "Wilderness empires now start with a Heartgrove specialisation instead of a Threadway specialisation"
- [wilderness, district] 4.0.17 "Enabled the Heartgrove district specialisation for Wilderness empires"
- [wilderness, district] 4.0.17 "The Wilderness Subterranean Cache building can now be placed in urban and trade district specializations"
### 4.0.22 (released 2025-08-21, checksum b03b), Improvement section
- [wilderness] "Significant quality of life and system changes to Wilderness empires."
- [wilderness, biomass] (sub-bullet) "To prevent issues with biomass not being available on planets due to construction, all Wilderness jobs are now automatically worked without requiring biomass. These jobs are worked by your primary species."
- [wilderness] "Jobs for Wilderness are now also properly represented instead of being worked by a single pop with massive workforce bonuses, you now see exactly how the job is worked, and the sliders are also now usable to customize how much of each job will be worked."
- [wilderness] "These changes are intended to prevent situations where some jobs would go unworked, make Wilderness less bug-prone, and are intended to give you more control over your economy in deficit situations."
- [biomass] (later in 4.0.22) "Biomass and Neural Chip Processing and Unprocessing jobs now have unique names so it's easier to tell what's going on."
- [wilderness] (later in 4.0.22) "Wilderness empires now have a fallback if their capital buildings are deleted during the monthly tick."
- [wilderness, biomass] (later in 4.0.22) "Optimized the script side calculations of biomass. Non Wilderness empires will no longer try to tally up their citizens as potential biomass reserves."
- Searched 4.0, 4.0.2–4.0.23 for "tree of life", "sapling", "transplant", "tree", "plantoid", "devastation": the only hits are unrelated (Orchard Forest, tradition tree, the plantoid trait-unification Modding line, the 4.0.14 devastation line above).
<!-- END 4.0.X -->

## Specific claims
Scope searched: 4.4 (+4.4.2–4.4.6), 4.5 (+4.5.1–4.5.2), 4.3 (+4.3.1–4.3.7); 4.0 and 4.0.2–4.0.23 for the Wilderness/Tree of Life claims only. 4.1.x and 4.2.x not searched. 4.4.0 and 4.0.0 were never public (4.4.1 and 4.0.1 are the "4.4" and "4.0" notes).

1. Nomads/Arkships release version and date; codenames of 4.4 and 4.5 -> CONFIRMED. Evidence: Patch 4.4 page "**4.4**, aka "Pegasus", was released on 2026-06-15[1] with the checksum 8e23*. The patch was released alongside the [Nomads.png] Nomads expansion. (**note**: patch 4.4.0 was never released to the public instead making patch 4.4.1 the first in the 4.4.X branch)." Patch 4.5 page "**Patch 4.5**, aka "Cygnus", was released on 2026-09-22[1] with the checksum 8697*." The Nomads DLC page (wiki_search snippet) gives "Release date / Patch 2026-06-15 / 4.4". Latest: 4.5.2 "was released on 2026-10-06[2] with the checksum *9776*." For reference: 4.3 "Cetus" 2026-03-17; 4.0 "Phoenix" 2025-05-05 (BioGenesis).

2. 4.4.4 made scripted modifiers target colonies, not planets, for Arkship colonies -> CONFIRMED (worded as a bugfix plus a modder note, not a "rework"; the note covers every scripted modifier). Evidence: 4.4.4 (2026-06-24) Bugfix "Fixed numerous modifiers (such as the Stability from Bureaucrats granted by Byzantine Bureaucracy) not granting their modifiers to Arkships. Note to modders: Any scripted modifier needs to be updated to work on colonies rather than planets." Same patch: "Planet class modifiers now apply to Arkship (ship-carried) colonies." Related scope changes: 4.4.5 Modding "Added home_colony and background_colony scopes (similar to home_planet and background_planet, but scope to a colony on any carrier type)."; 4.5 Modding "set_planet_flag, remove_planet_flag, set_timed_planet_flag and has_planet_flag now also work in colony, carrier, and ship scope (previously planet scope only), operating on the same flag store as the carrier_flag family."; 4.5 "Fixed special projects on Arkship colonies resolving to the wrong planet scope."

3. "Mixing settled planets with Arkships might break; code assumes they never coexist" -> NOT FOUND in 4.3–4.3.7, 4.4–4.4.6, 4.5–4.5.2 (or the 4.0.x Wilderness search). No developer statement to that effect is in the patch notes. Closest lines, all consistent with "one empire never holds both": 4.4 "...Nomadic empires can Settle on an empty planet, with options to settle partially or fully and leave the remainder as a subject. Settled empires can Embark onto Arkships through a mirrored process, converting their starbases into Waystations."; 4.5 "The End of the Cycle Creation Accord no longer grants Nomad empires a colonized planet they cannot hold. Nomads now receive a newly materialized Arkship of a type they have researched."; 4.5 (Total Wars sub-bullet) "Arkships defeated by Settled Empires are now destroyed after Total War status quo victory."; 4.4.4 "Fixed issues in which settled empires that Embarked could retain starbases and borders. Clarified the tooltip for Waystation conversion in English."

4. "Wilderness district cost is 50 biomass (4.0.3 patch notes)" -> CONFIRMED. Evidence: 4.0.3 (2025-05-06, Balance) "Districts for Wilderness Empires now cost 50 Biomass". Same block: "The expand planet decision for Wilderness now costs 5×(Planet Size)^2 Biomass and 50 Influence" (4.0.5 then "Reduced the biomass cost for planetary expansion for wilderness by roughly 50%", which is the expand cost, not districts). No later line changes the Wilderness district cost in 4.0.4–4.0.23, 4.3–4.3.7 (including dropped sub-bullets), 4.4–4.4.6 or 4.5–4.5.2. 4.1.x/4.2.x not searched, so the current value still needs the install check.

5. "4.0.22 Wilderness rework: jobs auto-worked by the primary species with no biomass requirement" -> CONFIRMED. Evidence: 4.0.22 (2025-08-21, Improvement) "Significant quality of life and system changes to Wilderness empires." with sub-bullet "To prevent issues with biomass not being available on planets due to construction, all Wilderness jobs are now automatically worked without requiring biomass. These jobs are worked by your primary species." Also "Jobs for Wilderness are now also properly represented instead of being worked by a single pop with massive workforce bonuses, you now see exactly how the job is worked, and the sliders are also now usable to customize how much of each job will be worked." (This replaces 4.0.5's "Increased the amount of workforce Wilderness pops gain to 99999 ...")

6. "Tree of Life dies at 50% devastation"; "Tree of Life transplant costs 500 food / 180 days" -> NOT FOUND (both) in 4.0–4.0.23, 4.3–4.3.7, 4.4–4.4.6, 4.5–4.5.2. The string "Tree of Life" never appears in them, and neither do "sapling" or "transplant". The only devastation line is 4.0.14 "Planetary Habitability and Devastation should no longer overlap" (UI). The origin predates 4.0, so these numbers must come from the install (or the origin's wiki page).

7. Arkship numbers -> PARTIAL.
   - CONFIRMED, three classes × three tiers: 4.4 "...with three specialization classes (Military, Scientific, Civilian) that can be upgraded through three tiers and customized with modules, systems, and late-game Spinal Mounts."
   - CONFIRMED, Civilian +2,500 Operational Reserves capacity per tier: 4.4.5 (2026-07-07) "Civilian Arkships grant an additional 2,500 Operational Reserve capacity per Tier." Same patch: "Maximum Operational Reserves now scale with the number and tier of owned Arkships (base 15,000, plus 5,000 / 7,500 / 10,000 per Tier I / II / III Arkship) instead of a fixed cap." and "Operational Reserves now track Energy and Minerals 1:1 (previously 3:1)."
   - NOT FOUND: "Arks start with 1000 pops"; "colony size 9 to 18"; Civilian "+5% harvest yield per tier"; Scientific "+1 sensor/hyperlane range, +5% survey speed per tier"; Military "+5% fire rate and weapon range per tier"; "cannot be bombarded".
   - Adjacent: 4.5 "Scientific Arkships now have +5% Deep Scanning Success Chance per Tier" (a Scientific per-tier bonus missing from the mod's list); 4.4.4 "Harvesting modifiers for Civilian Arkships are now working correctly." (no number); 4.4.4 "Arkship now have an inherent 40% habitability, matching that of habitats."; 4.4.4 "Arkship Living Space Module grants +5% habitability in addition to the +1 district slot, but has an Exotic Gas cost and upkeep."; 4.5.2 "Nomadic Synthetic Fertility no longer starts with a size 20 Arkship." (Arkship colonies have a size, but no range is given); 4.5 "Reduced the arkship harvesting and action modifiers provided by harvester jobs."

8. Harvest action numbers (Strip Mining 250% / 50-year / 500 OR / 180 days; Reprocessing 600% / 200-year / Shattered World) -> NOT FOUND (no numbers anywhere). The actions themselves are confirmed: 4.4.5 "Reduced the opinion penalties empires hold against Nomadic Empires for strip-mining, reprocessing planets, and Exodus Jumps, and made these penalties decay more quickly."; 4.4.4 "Arkship and Logistic harvest and strip mine orders issued on a whole solar system now target higher-value planets first instead of simply the nearest."; 4.4.5 "Harvesting a celestial body that has no deposit no longer incurs a diplomatic opinion penalty."

9. Contracts "one per Arkship, five-year expiry, Inward Perfection cannot accept" -> NOT FOUND (all three). Contract lines that do exist: 4.4 "Settled empires can issue task-driven Contracts (Military, Science, Resource, Diplomacy) for Nomadic empires to fulfill. Completing Contracts earns Trade, improves Opinion and Trust, and unlocks deeper diplomatic options including Wayline Pacts."; 4.4.4 "AI will now consider abandoning a contract after 20 years have passed since they have accepted it."; 4.5 "Regular Empires can now cancel accepted contracts after 20 years, with a notification sent to the operator."; 4.4 Modding "Add time_to_accept and time_to_complete to missions"; 4.5 "A Nomadic empire with a subject on Limited Diplomacy now receives contracts again." and "An empire with a subject on Limited Diplomacy now offers contracts to Nomadic empires again."

10. Settling, converting, Waystation costs, Logistic Ships building Waystations, Wayline Logistics techs -> MIXED.
   - Settling an Arkship on a planet / Nomads converting to settled: CONFIRMED. 4.4 "After completing the Adaptability or Versatility tradition, Nomadic empires can Settle on an empty planet, with options to settle partially or fully and leave the remainder as a subject. Settled empires can Embark onto Arkships through a mirrored process, converting their starbases into Waystations."; 4.5 "Fixed the colony designation being ignored when a Nomad empire settles an Arkship on a planet."; 4.4.4 "Nomad-exclusive technologies are now hidden if you decide to settle, and re-revealed if you then decide to embark and become nomadic again."; 4.5.2 "Nomadic Knights of the Toxic God can now settle once they reach the Toxic God system, and keep the Order's Keep and their Knights on the new homeworld."
   - Logistic Ships building Waystations: CONFIRMED. 4.5.2 "Logistic Ships now show Waystations in the Megastructure Build Menu alongside their dedicated Build Waystation Button for consistency with Arkships."
   - Waystation cost (alloys?): NOT FOUND. Closest: 4.4.5 "Brought the cost and upkeep of the Nanite Hub waystation module in line with the equivalent starbase building." (a module, not the station); 4.5 AI "...Arkship construction was being starved of the single per week megastructure build slot by Waystation construction and now has its own slot..." (Waystations use the megastructure build slot); 4.5 AI "Nomad AI no longer locks Influence and Alloys into the Arkship construction budget..." (Arkship cost, not Waystation).
   - "Wayline Logistics" techs: NOT FOUND. Only "Waystation Logistics Silos" (4.4.5) and "Logistics Hub" (4.4.4 "Added Logistics Hub indicator in the galaxy map."; 4.5 AI Logistics Hub line).
