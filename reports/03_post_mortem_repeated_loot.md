# GBA The Lord of the Rings: The Return of the King
## Research Report on Post-Mortem Repeated Loot and Equipment Farming
*Research Result G | Unofficial reverse engineering / ROM, SAV, savestate, runtime-state, and gameplay analysis* | 
*Research status as of August 28, 2026*
### Research Scope
This report integrates the ROM disassembly, savestate differences, runtime entity-state tracking, repeated-loot gameplay tests, and Unique / RedMask / RNG research completed through August 28, 2026. The core goal is not to claim that 'hitting a corpse more often makes it luckier', but to distinguish four separate questions: whether a corpse can still be hit, whether death processing is re-entered, whether the equipment generator is actually reached, and whether the resulting drop can be physically collected.

# 1. Executive Summary
- At least for the tested ordinary-enemy samples in BLRE, The Return of the King contains a genuine post-mortem death-state re-entry loot exploit. A corpse can still register valid hits; damage processing can push an already-dead entity back into death processing, which can then execute the normal death reward/drop chain again and consume fresh RNG.
- The exploit does not merely duplicate the item generated on the first death, and there is no evidence that it raises the per-roll probability of a Unique item. Its real effect is to increase the number of independent death-reward and equipment-generation opportunities that can be produced per unit time.
- Runtime testing showed continued changes to corpse entity state and HP. A representative tracked entity was 0x020125E0, whose HP fell from roughly -193 to -607 under further corpse hits. New ground-item objects also appeared at coordinates matching the corpse between savestate captures.
- For tested targets, the mechanism can be summarized as: valid corpse hit -> re-entry into a death-processing state -> normal death-drop function -> resource/source reward routing -> RNG -> newly generated ground item.
- One attack swing is not one complete loot roll. Only a hit that is actually registered on the corpse, produces a re-death reward cycle, and then proceeds into the equipment generator counts as an equipment/Unique-level sample.
- Money, bread, fruit, herbs, potions, and other non-equipment rewards consume RNG but do not count as equipment-generation events and do not enter the Unique Category-E draw.
- RedMask - with a complete 14-target eligibility mask of 0xDBFF - is an eligibility gate for the rare backpack Unique set, not an RNG reset. Enabling RedMask does not reshuffle a future RNG trajectory already fixed by a savestate.
- The ability to hit a corpse and the ability to farm equipment from it must be separated. A key counterexample is the small spider in the Cirith Ungol / Frodo route: it can be corpse-hit, but testing did not produce equipment.
- Maximum practical efficiency is not achieved by overlapping corpses indefinitely. The real optimization target is re-death throughput x equipment-generation probability x collectability x player mobility x corpse persistence window.
# 2. Technical Naming and Research Upgrade
The early label 'corpse-hitting drops equipment' began as a gameplay observation: after an enemy died, continuing to attack the body appeared to produce more drops. Savestate analysis and code-path research now support classifying the behavior as a real gameplay exploit with a program-level mechanism rather than a random visual coincidence.
**Data block 2 (converted from a table for copy/paste compatibility)**
- Recommended technical name: Post-Mortem Death-State Re-entry Loot Exploit. A shorter operational label is 'post-mortem re-death repeated loot'. = Recommended technical name: Post-Mortem Death-State Re-entry Loot Exploit. A shorter operational label is 'post-mortem re-death repeated loot'.

The underlying defect is that the game does not enforce a strict, universal 'death may be settled only once' non-reentrancy guard across every damage/death path. Certain dead entities can therefore continue receiving effective damage and re-enter the normal death-settlement path.
# 3. Runtime and Savestate Evidence
**Data block 3 (converted from a table for copy/paste compatibility)**
- Strong mechanism chain observed in tested samples / valid corpse hit / damage handler / -> dead entity re-enters a death-processing state / -> normal death-drop entry FUN_0803A6E8 / -> reward routing FUN_0803A77C / -> if equipment is selected: FUN_0803AE8C / -> category / Unique candidate filtering, including FUN_0803A844 / -> newly generated ground item = Strong mechanism chain observed in tested samples / valid corpse hit / damage handler / -> dead entity re-enters a death-processing state / -> normal death-drop entry FUN_0803A6E8 / -> reward routing FUN_0803A77C / -> if equipment is selected: FUN_0803AE8C / -> category / Unique candidate filtering, including FUN_0803A844 / -> newly generated ground item

**Data block 4 (converted from a table for copy/paste compatibility)**
- Row 1: Evidence = Corpse still receives hits; Observation = An ordinary enemy entity continues to accept attack/damage after death.; Interpretation Supported = The dead entity is not immediately removed from all damage processing.
- Row 2: Evidence = HP continues to decrease; Observation = A representative corpse fell from roughly -193 HP to roughly -607 HP.; Interpretation Supported = Post-death attacks are not merely visual whiffs; entity state is actually being modified.
- Row 3: Evidence = State re-entry; Observation = Death-related state values, including observations around state 0x11 and state 6, reappear during the chain.; Interpretation Supported = A dead entity can re-enter death-processing logic.
- Row 4: Evidence = New ground item; Observation = A new item object appeared between st1 and st2 at coordinates matching the corpse.; Interpretation Supported = The loot is newly generated rather than a display duplicate of the first item.
- Row 5: Evidence = RNG consumption; Observation = Later outcomes are not fixed copies of the first drop and change when RNG consumption changes.; Interpretation Supported = Each re-death cycle can enter a fresh random reward decision.

**Data block 5 (converted from a table for copy/paste compatibility)**
- Evidence boundary: the chain above is sufficient to prove that the exploit exists in the tested cases. It must not be generalized to every enemy, every corpse, or every individual hit without enemy-type-specific validation. = Evidence boundary: the chain above is sufficient to prove that the exploit exists in the tested cases. It must not be generalized to every enemy, every corpse, or every individual hit without enemy-type-specific validation.

# 4. Why This Is Not a Simple Copy of the First Drop
- If the system merely copied the first-death drop, later events should reuse the same pre-existing item record. Instead, new ground-item objects are observed and their results can differ.
- The later chain continues to consume the game's main RNG, which means reward and equipment selection are rerun during re-death rather than copied from an existing item.
- Within one corpse-farming sequence, practical tests have produced money, food, ordinary equipment, blue-name Unique items, Orc Head, Spoon, and other different results. This is consistent with rerunning the reward tree.
- Extra Treasure and similar properties may affect the reward chain, but they are not the cause of corpse re-death. The root exploit is death-state reentrancy.
# 5. General Drop Chain and the Meaning of a Real Equipment-Generation Opportunity
Equipment-farming efficiency must not be measured by raw attack count. It should be measured by how often the game actually reaches the equipment generator. The common death-reward chain identified in the current disassembly is:
**Data block 6 (converted from a table for copy/paste compatibility)**
- FUN_0803A6E8(object) / -> read resource/source from object + 0x61 / -> RNG gate, including resource-related bonus logic / -> FUN_0803A77C reward router / -> if the route enters the equipment interval / -> FUN_0803AE8C equipment-category generator / -> Category E / ordinary equipment / affix logic = FUN_0803A6E8(object) / -> read resource/source from object + 0x61 / -> RNG gate, including resource-related bonus logic / -> FUN_0803A77C reward router / -> if the route enters the equipment interval / -> FUN_0803AE8C equipment-category generator / -> Category E / ordinary equipment / affix logic

Accordingly, the research distinguishes the following count layers:
**Data block 7 (converted from a table for copy/paste compatibility)**
- Row 1: Count Layer = Swing; Definition = The player presses an attack button once.; Equal to a Unique Opportunity? = No
- Row 2: Count Layer = Registered corpse hit; Definition = The attack actually hits the corpse and modifies entity state.; Equal to a Unique Opportunity? = Not necessarily
- Row 3: Count Layer = Re-death reward cycle; Definition = The corpse re-enters a death reward decision.; Equal to a Unique Opportunity? = Only if it continues into the equipment generator
- Row 4: Count Layer = Equipment-generation event; Definition = The equipment-category generator is actually invoked.; Equal to a Unique Opportunity? = Yes - this is the parent sample for the Unique entry

# 6. The Tested 55% / 16.5% Model
For one tested class of ordinary reward rows - historically recorded around type/resource sample 0x1F and related entries - each valid re-death reward cycle can be approximated as follows:
**Data block 8 (converted from a table for copy/paste compatibility)**
- ~55% passes the outer reward gate and enters some reward branch / within that set, ~30% routes to the equipment generator / / 0.55 x 0.30 ~= 0.165 / => ~16.5% true equipment-generation probability for this tested reward class = ~55% passes the outer reward gate and enters some reward branch / within that set, ~30% routes to the equipment generator / / 0.55 x 0.30 ~= 0.165 / => ~16.5% true equipment-generation probability for this tested reward class

Therefore, '400 corpse hits' can only be loosely translated to about 66 equipment-generation events if all 400 are genuine re-death reward cycles. Four hundred ordinary swings are nowhere near equivalent. Different resource/source rows have observed or inferred equipment-generation tiers around 0%, 16.5%, 44.1%, 85.7%, and 100%, so the 16.5% figure must not be applied universally.
# 7. Unique Entry, 53 Candidates, and the 14 Rare Backpack Items
- Once an equipment-generation event reaches FUN_0803AE8C, the logical entry into Unique Category E is 'roll 100 on a 1..100 draw'. Because the engine uses 15-bit integer scaling, the implementation is approximately 327/32768 = 0.9979%, which can be described as about 1% in ordinary discussion.
- Category E uses candidate indices 0..52, for 53 candidate entries.
- Within the special candidate region are 16 red/special backpack candidates. Fourteen target rare backpack Unique items are controlled by RedMask eligibility bits; two other positions behave as special exceptions.
- The complete eligibility mask for the 14 target items is 0xDBFF. It allows those candidates to pass eligibility review; it does not increase the Category-E entry probability.
- The target red candidates are also subject to an additional review gate of roughly 1/10, so being eligible does not imply that an item will appear quickly.
**Data block 9 (converted from a table for copy/paste compatibility)**
- Row 1: Probability Basis = Any one of the 14 target red items per true equipment-generation event; Approximate Value = About 14/53000 ~= 1/3785.7; Correct Interpretation = Long-run scale only; not a pity guarantee
- Row 2: Probability Basis = One specified target item per true equipment-generation event; Approximate Value = About 1/53000; Correct Interpretation = Near-uniform candidate model; small integer-scaling asymmetries may remain
- Row 3: Probability Basis = Unique Category-E entry; Approximate Value = Logical ~1%; implementation ~0.9979%; Correct Interpretation = Applies to real equipment-generation events, not to every attack

# 8. RedMask: Eligibility Gate, Not RNG Shuffler
RedMask is tightly related to corpse-farming research because a natural player hypothesis is: 'If a savestate already has a favorable rare-item trajectory, can I enable eligibility and force a different red item?' Testing corrected this assumption.
- The complete 14-target RedMask is 0xDBFF and is persisted in the character record. It decides whether certain red candidates are allowed to generate.
- A Helm's Deep savestate repeatedly produced a trajectory containing six Orc Heads. Loading the same savestate and then enabling RedMask still tended to reproduce the original six-head trajectory.
- Therefore, changing RedMask does not automatically reset or reseed RNG and does not guarantee that a fixed savestate will immediately generate a different loot sequence.
- To change the outcome, the actual RNG consumption path must change - for example through enemy AI timing, attack/wait order, extra kills, route changes, or other gameplay events - rather than merely changing the eligibility bits.
**Data block 10 (converted from a table for copy/paste compatibility)**
- Conclusion: RedMask ~= eligibility gate; RNG state ~= current random trajectory. They are different layers. = Conclusion: RedMask ~= eligibility gate; RNG state ~= current random trajectory. They are different layers.

# 9. Corpse-Hittable Does Not Mean Equipment-Farmable: Four-Layer Model
This four-layer model explains why enemies that all appear to accept corpse hits can have very different value for equipment farming.
**Data block 11 (converted from a table for copy/paste compatibility)**
- Row 1: Layer = L1 - Corpse hitability; Question = Can the dead entity still be hit by attack collision?; Typical Counterexample / Significance = Some entities can; some cannot.
- Row 2: Layer = L2 - Death re-entry; Question = Does a valid corpse hit re-enter death processing?; Typical Counterexample / Significance = A corpse can be hittable yet never produce a repeat reward.
- Row 3: Layer = L3 - Reward / equipment eligibility; Question = After re-death, is there a reward and can the route enter equipment generation?; Typical Counterexample / Significance = Small spider on the Frodo/Cirith Ungol route: corpse-hittable, but testing produced no equipment.
- Row 4: Layer = L4 - Collection reachability; Question = Can the player physically reach and pick up the generated item?; Typical Counterexample / Significance = Overlapped bodies, corners, and wall geometry can generate loot that cannot be collected.

# 10. Corpse Spatial Geometry: Throughput vs. Collectability
The early intuition was 'the tighter the corpse stack, the better, because one attack can hit more bodies.' Practical testing shows that this is incomplete. Perfect overlap can cause player-body collision, blocked movement, and items spawning inside a corpse cluster where they cannot be reached.
**Data block 12 (converted from a table for copy/paste compatibility)**
- Effective farming efficiency ~= valid re-death cycles / time x equipment-generation probability x collectability x corpse persistence window = Effective farming efficiency ~= valid re-death cycles / time x equipment-generation probability x collectability x corpse persistence window

- A tight line, a lightly staggered row, or a small arc is often more practical than perfect overlap.
- If corpses are too dispersed, one attack hits fewer entities and repeat-trigger throughput drops.
- If corpses are too dense, movement and pickup losses increase.
- It is therefore useful to distinguish RNG throughput, loot throughput, and retained-loot throughput - the rate of items that can actually be taken away.
# 11. Practical Farming Locations and Code-Level Interpretation
**Data block 13 (converted from a table for copy/paste compatibility)**
- Row 1: Area / Target = Helm's Deep; Observed Gameplay = Long corpse-farming sessions naturally produced more than ten ordinary Unique drops, including bursts of Unique items, Orc Head, and Spoon.; Research Interpretation / Boundary = Its strength comes from repeated death/reward throughput per unit time, not from a higher proven Unique probability per roll.
- Row 2: Area / Target = Dwarven Halls of Moria - Act I; Observed Gameplay = Near the shrine stairs and the ancient coffin area, Orc archers spawn in waves and can be corpse-hit.; Research Interpretation / Boundary = Terrain and wave structure are favorable for accumulating bodies; exact resource/source mapping remains object-specific.
- Row 3: Area / Target = Dwarven Halls - Act V; Observed Gameplay = Multiple waves of ordinary enemies/archers emerge from the upper central cave and the cave beside the upper-left passage.; Research Interpretation / Boundary = Large corpse counts can cause crowding and reduce pickup efficiency.
- Row 4: Area / Target = White Mountains cave; Observed Gameplay = Contains corpse-farmable targets; Snowbird's has also appeared in testing.; Research Interpretation / Boundary = Shows that affix/Unique outcomes should not be reduced to a simplistic 'map theme exclusivity' rule.
- Row 5: Area / Target = Small spiders on the Frodo route; Observed Gameplay = Corpse-hittable but did not drop equipment in user testing.; Research Interpretation / Boundary = Key counterexample where L1 is true but L3 equipment eligibility is false.
- Row 6: Area / Target = Elite enemies; Observed Gameplay = Most have not been reliably corpse-farmable with the current method.; Research Interpretation / Boundary = Even a high single-death equipment rate does not make an enemy useful if repeated re-death cannot be triggered.

# 12. Difficulty, Equipment Quality, and the Exploit
- Current code research has not found evidence that Hard or Very Hard directly raises the base Unique Category-E entry from roughly 1%.
- Difficulty does affect the available prefix/suffix ranges and quality tiers for ordinary equipment, so using higher difficulty for stronger normal affixes has code support.
- The main value of corpse farming is to increase effective equipment-generation opportunities per minute. It should not be described as making each individual roll more likely to become Unique.
- Saving the SAV does not itself raise or lower the base Unique formula. Save/exit/re-entry may alter concrete results indirectly by changing the subsequent RNG call path.
# 13. Probability Perspective: Why Long Dry Streaks Can Still Be Normal
Using the rough model of about 1/3785.7 per true equipment-generation event for any one of the 14 target red items, the low-probability tail remains very long. Even when the mechanism is working correctly, thousands of genuine equipment events can still yield no target item.
**Data block 14 (converted from a table for copy/paste compatibility)**
- Row 1: True Equipment-Generation Events = 500; Approximate Probability of Zero Target Red Items = about 87.6%
- Row 2: True Equipment-Generation Events = 1,000; Approximate Probability of Zero Target Red Items = about 76.8%
- Row 3: True Equipment-Generation Events = 2,000; Approximate Probability of Zero Target Red Items = about 59.0%
- Row 4: True Equipment-Generation Events = 3,000; Approximate Probability of Zero Target Red Items = about 45.3%
- Row 5: True Equipment-Generation Events = 5,000; Approximate Probability of Zero Target Red Items = about 26.8%
- Row 6: True Equipment-Generation Events = 10,000; Approximate Probability of Zero Target Red Items = about 7.2%
- Row 7: True Equipment-Generation Events = 20,000; Approximate Probability of Zero Target Red Items = about 0.5%

**Data block 15 (converted from a table for copy/paste compatibility)**
- Methodological implication: count newly created equipment objects / true equipment-generation events, not swings, corpse contacts, or kill count. If 10,000 real equipment events still produce zero target items, conditions should be rechecked. At 20,000 with zero targets, a pure-random explanation becomes weak and a second hidden condition should be prioritized. = Methodological implication: count newly created equipment objects / true equipment-generation events, not swings, corpse contacts, or kill count. If 10,000 real equipment events still produce zero target items, conditions should be rechecked. At 20,000 with zero targets, a pure-random explanation becomes weak and a second hidden condition should be prioritized.

# 14. Classification as a Gameplay Exploit and the Boundary with Cheating
- Technically, this behavior is a state-machine bug/mechanic defect in the original game code. It can be triggered without GameShark, CodeBreaker, ROM patches, or external memory modification. It is therefore distinct from directly forcing drop rates or item IDs.
- A suitable technical label is Major Gameplay Exploit / Major Glitch because it substantially amplifies the normal resource constraint of 'one death, one reward opportunity'.
- Whether a player chooses to regard the exploit as 'cheating' in personal play is a rules or ethics convention, not a program-level fact.
- For research that stays as close as possible to original-game rules, the cleanest setup is original probabilities, normal maps, and normal SAV files, with no forced-drop or rate-increase patch - only the repeatable exploit present in the game itself.
# 15. Version and Generality Boundaries
**Data block 16 (converted from a table for copy/paste compatibility)**
- Row 1: Target = BLRE USA/Europe; Current Conclusion = The strongest program-level and savestate evidence comes from this family. The exploit is clearly established for the tested targets.
- Row 2: Target = BLRJ Japan; Current Conclusion = The engine and drop code are highly homologous, but identical corpse re-entry behavior should be dynamically retested before declaring complete equivalence.
- Row 3: Target = Localized build; Current Conclusion = Based on the BLRJ line. Long-term practical corpse-farming experience supports the phenomenon, but exact addresses and object mappings must be confirmed per build.
- Row 4: Target = All enemies; Current Conclusion = Cannot be claimed. Corpse hitboxes, state handling, cleanup behavior, and reward sources differ across enemy types.

# 16. Open Questions
1. How corpse-hit handlers and 'first death only' filters differ among enemy types, and which classes are completely immune to re-death.
1. Whether every Registered corpse hit reruns FUN_0803A6E8, or whether there are frame cooldowns, count limits, or state windows.
1. How many times a typical corpse-farming sequence actually reaches FUN_0803AE8C - the true conversion rate from attack throughput to equipment throughput.
1. When one attack hitbox contacts multiple corpses, in what order the program processes them and whether each entity consumes RNG independently.
1. Why continuous attacks appear to delay cleanup for some bodies, and how the 'standing corpse / never disappears' end state relates to cleanup timers and animation state.
1. Whether the red archer 'double drop' is two full generic equipment-generation passes or a fixed-plus-random dual path.
1. A complete mapping from visible enemy name -> class/variant -> resource/source for every major farming location.
# 17. Twelve Conclusions That Can Be Frozen at This Stage
- Repeated post-mortem loot is not a player illusion; at least for tested objects, it is a real program-level death-state re-entry exploit.
- Corpses can continue to receive effective damage, and runtime HP/state values continue to change.
- A valid corpse hit can cause a dead entity to re-enter death processing.
- A re-death can rerun the normal reward/drop chain and consume RNG.
- Later drops are newly generated opportunities, not simple copies of the first-death item.
- One swing is not one full loot event; swing, registered hit, re-death, and equipment generation are distinct layers.
- Non-equipment rewards are not Unique equipment rolls.
- The exploit increases opportunity throughput; no evidence proves that it raises the Unique probability of each equipment roll.
- RedMask 0xDBFF is the eligibility gate for the 14 target red Unique items, not an RNG reseed operation.
- Corpse-hittability and equipment-drop eligibility are independent conditions; the small-spider counterexample proves the need to separate them.
- Perfect corpse overlap is not automatically optimal; collection reachability and player mobility must be included in efficiency.
- The mechanism can technically be classified as a Major Gameplay Exploit, while whether a player accepts it in personal play remains a rule-choice question.
# Appendix A. Key Functions and Fields
**Data block 17 (converted from a table for copy/paste compatibility)**
- Row 1: Name / Address = FUN_0803A6E8; Research Use = Common enemy death-drop entry; reads resource/source from object + 0x61 and applies the first reward gate.
- Row 2: Name / Address = FUN_0803A77C; Research Use = Reward router; selects ordinary reward, equipment, and related branches.
- Row 3: Name / Address = FUN_0803AE8C; Research Use = Equipment generator; enters equipment-category logic and the Category-E path.
- Row 4: Name / Address = FUN_0803A844; Research Use = Important function in Unique candidate indexing/filtering.
- Row 5: Name / Address = FUN_0803A4BC(min,max); Research Use = General RNG scaling function using a 15-bit value and integer scaling.
- Row 6: Name / Address = DAT_03003D8A + idx x 0x1AC; Research Use = Runtime field associated with character RedMask state; the exact active-character address depends on the loaded structure.
- Row 7: Name / Address = 0xDBFF; Research Use = Complete eligibility mask for the 14 target red rare backpack Unique items.
- Row 8: Name / Address = object + 0x61; Research Use = Key runtime resource/source ID field for an enemy entity.
- Row 9: Name / Address = Example tracked corpse entity 0x020125E0; Research Use = Representative entity used in Research Result G for state, HP, and coordinate tracking across test stages.
