# GBA The Lord of the Rings: The Return of the King

## Data Basis and Calculation Method for Critical Rate

*Separate Verification of Critical % / Critical Damage / Critical Armor*

- Research baseline: original English RETURN KING / BLRE / Version 00; current strict reverse-engineering baseline SHA-256: b5f556593c5fbe3531a4354b047f0bed364a124ceee43f278a48f4d26b0ffa9c. Conclusions are tiered as 'ROM/machine code > static closure > guide cross-verification > player experience'.

- One-sentence conclusion: at the data level, '+20% Critical' means 'increase Critical chance by 20 percentage points', not 'multiply the current Critical rate by 1.20'. Equipment, affixes, and passive skills are interpreted additively; Gimli's Dwarven Rage temporarily forces a 100% Critical rate. The current evidence has closed the Critical attribute itself to attribute ID 0x23, but the final call site proving whether combat Critical checks directly use the known 15-bit RNG function has not been re-closed in the 2026-08-24 evidence package. Therefore, the drop-system RNG buckets must not be presented as proven combat-Critical RNG behavior.

*Version: 2026-08-24 | Technical Notes Edition*

> **Current project note:** The data-model conclusions remain publishable, but the final ordinary-combat Critical RNG call site has not yet been re-closed instruction-by-instruction on the latest baseline. Do not present the drop-system 15-bit RNG bucket formula as independently proven combat-Critical behavior.

## 1. First separate four concepts that are easy to confuse

- Concept = Critical %; What it changes = Probability that an attack becomes a Critical hit; Unit / behavior = Percentage / percentage points, e.g. +20% Critical; Included in the additive 'Critical rate' total? = Yes

- Concept = Critical Damage; What it changes = Additional Critical damage after a Critical occurs; Unit / behavior = Fixed damage value, e.g. +5 Critical Hit Damage; Included in the additive 'Critical rate' total? = No

- Concept = Critical Armor; What it changes = Reduces Critical damage received when critically hit; Unit / behavior = Defensive value, e.g. +12 Critical Armor; Included in the additive 'Critical rate' total? = No

- Concept = Dwarven Rage; What it changes = Temporarily turns attacks directly into Critical hits; Unit / behavior = Temporary state: 100% Critical; Included in the additive 'Critical rate' total? = Overrides the ordinary probability check

- Most common misconception: 'of the Fates +20% Critical' is not +20 Critical damage; Dwarf Rune III's '+5 Critical Hit Damage' is not +5% Critical rate; Critical Armor does not 'lower the opponent's Critical rate'. These three concepts must be kept separate.

## 2. Data basis of Critical rate: why the values can be added as percentage points

## 2.1 ROM attribute records: Critical has an independent attribute ID, 0x23

The current 2026-08-24 technical handoff package concludes that each base Item/Prefix/Suffix record is 12 bytes and contains a name text ID, minimum level, four groups of 'attribute ID + signed value', and flag bytes. Among the identified attributes, 0x23 = Critical %. This means Critical rate is not inferred from weapon damage; it is an explicit, enumerable, stackable attribute dimension in the ROM tables.

- Field / object = 12-byte Item/Prefix/Suffix record; Confirmed meaning = Up to four groups of (attribute_id, signed value); Evidence level = ROM table + static decompilation

- Field / object = attribute_id 0x23; Confirmed meaning = Critical %; Evidence level = ROM table + static decompilation

- Field / object = attribute_id 0x20; Confirmed meaning = Extra Treasure %; Evidence level = ROM table + static decompilation; distinct from Critical

- Field / object = Critical Armor; Confirmed meaning = A separate defensive attribute, not equivalent to 0x23 Critical %; Evidence level = Item table + guide cross-verification

## 2.2 Base equipment can itself carry Critical %

The CodeBreaker Item Table explicitly lists base Critical percentages for many weapons. For example:

- Base equipment = Spoon; Base Critical = +30%; Notes = 1-3 piercing damage; a typical example of an extremely high base Critical rate in the game

- Base equipment = Dagger; Base Critical = +25%; Notes = Can also carry Armor Piercing

- Base equipment = 5-Finger Knife; Base Critical = +35%; Notes = One of the high-base-Critical examples found

- Base equipment = Mattock; Base Critical = +20%; Notes = Two-handed; Knockback

- Base equipment = Shortsword; Base Critical = +15%; Notes = Ordinary sword example

- Base equipment = Longsword; Base Critical = +10%; Notes = Early-game sword example

- Base equipment = Short Staff; Base Critical = +5%; Notes = Low-base-Critical example

## 2.3 Prefixes / Suffixes also store Critical as '+N percentage points'

- Affix = Vein Crystal (specific Prefix table); Critical contribution = +40%; Other attributes on the same affix = +5 Strength、+9 Accuracy、+2 Defense

- Affix = of Law; Critical contribution = +25%; Other attributes on the same affix = +1 Health

- Affix = of the Wraiths; Critical contribution = +20%; Other attributes on the same affix = +3 Accuracy、Cursed

- Affix = of the Fates（0x9B）; Critical contribution = +20%; Other attributes on the same affix = +60% Extra Treasure

- Affix = of Luck; Critical contribution = +15%; Other attributes on the same affix = +1 Defense、+30% Extra Treasure

- Affix = of Wood Elves; Critical contribution = +10%; Other attributes on the same affix = +10 Spirit Points

- Affix = of Faramir; Critical contribution = +5%; Other attributes on the same affix = +2 damage to Orcs

- Important: Prefix tables are separated by equipment category. Vein Crystal, for example, is not a universal Prefix that can be freely attached to every weapon. The table above demonstrates value semantics and magnitude; it cannot be used to force arbitrary Prefixes onto arbitrary base items.

## 3. Skill-based sources of Critical rate

- Character / skill = Aragorn - Death Strike; Critical-rate rule = +3 percentage points per skill level; Max-level result = Level 5 = +15%; Nature of evidence = Consistent across multiple GBA guides

- Character / skill = Gimli - Death Strike; Critical-rate rule = +3 percentage points per skill level; Max-level result = Level 5 = +15%; Nature of evidence = Dedicated Gimli guide + general guide agree

- Character / skill = Legolas - Galadriel's Blessing; Critical-rate rule = +3 percentage points per skill level (also increases firing speed); Max-level result = Level 5 = +15%; Nature of evidence = General guide

- Character / skill = Gimli - Dwarven Rage; Critical-rate rule = All attacks are Criticals for the duration; Max-level result = Temporary 100%; Nature of evidence = Dedicated Gimli guide + general guide agree

## 3.1 Death Strike calculation

- C_DeathStrike = 3 x L (L = 0..5)

L is the current skill level; at level 5 it adds 15 percentage points.

The '+3%/level' wording should be understood as +3 percentage points per level. It uses the same probability semantics as +N% Critical on equipment. Historical Gimli guides even compare a maxed 15% Death Strike with replacing that benefit through Critical bonuses on a weapon and shield, further supporting the additive interpretation.

## 3.2 Gimli's Dwarven Rage is not 'another +100'; it temporarily forces Critical hits

- P(critical | Dwarven Rage active) = 100%

This is a state override, not an additional +100 added to the ordinary Critical value.

A dedicated Gimli guide states that the skill lasts 3 seconds at level 1 and gains 2 seconds for each additional level; the same guide gives 11 seconds at max level. The duration formula most consistent with that source is therefore:

- Duration(L) = 3 + 2 x (L - 1) seconds (L = 1..5)

L=1: 3 seconds; L=5: 11 seconds.

## 3.3 Do not add 'Critical Damage' skills into Critical %

Legolas's Accuracy is described in guides as '+1 Accuracy, +3 Critical Damage/level'; Dwarf Rune III - The Forge gives '+5 Critical Hit Damage'. These increase damage after a Critical occurs, not the probability of a Critical occurring. They must therefore be excluded when calculating Critical %.

## 4. Critical-rate formula under normal conditions

- Scope: the formula below is the nominal panel/logical Critical calculation. Equipment attribute IDs and source values are already closed, but the current 2026-08-24 decompilation handoff has not re-located the final combat random-check call site to a named function. This section therefore does not substitute the drop RNG's exact bucket rates for final combat-Critical machine probabilities.

- C_nominal = sum(C_base_item) + sum(C_prefix) + sum(C_suffix) + sum(C_passive_skill) + other confirmed Critical%

Every term is measured in percentage points.

For probability interpretation, values below 0 are equivalent to 0%; once the value reaches or exceeds 100, game-design semantics do not allow anything 'more certain than guaranteed'. For practical expectation calculations, use:

- P_nominal = min(max(C_nominal, 0), 100) / 100

This is a probability-interpretation formula; it is not a claim that an explicit clamp instruction has already been located in the ROM.

## 4.1 Calculation order

1. First read the inherent Critical % on the currently equipped base item.

1. Add the Critical % from every effective Prefix/Suffix on equipped items, in percentage points.

1. Add Critical % from character passive skills (for example Death Strike and Galadriel's Blessing).

1. Do not mix Critical Damage, Critical Armor, Dodge, Shield Block, or Accuracy into this total.

1. If Dwarven Rage is active, treat the current attack as a 100% Critical; ordinary C_nominal applies only after the skill ends.

## 4.2 Three directly usable examples

- Example = Gimli / Mattock of the Fates + Death Strike 5; Components = Mattock 20 + Fates 20 + Death Strike 15; Nominal Critical = 55%

- Example = Gimli / Spoon of the Fates + Death Strike 5; Components = Spoon 30 + Fates 20 + Death Strike 15; Nominal Critical = 65%

- Example = Legolas / Horn Bow of the Fates + Galadriel's Blessing 5; Components = Horn Bow 10 + Fates 20 + Blessing 15; Nominal Critical = 45%

These examples demonstrate only the addition method. Whether a specific 'base item + Prefix + Suffix' combination can actually generate naturally is constrained by equipment category, minimum level, Prefix/Suffix generation tables, and drop-system filters; the examples must not be reversed into drop-rate conclusions.

## 5. Why an 'of the Fates set' appears especially suitable for stacking Critical

This project has repeatedly cross-verified through the CodeBreaker Suffix Table and ROM affix research that 0x9B of the Fates = +60% Extra Treasure +20% Critical. Therefore, every equipped '... of the Fates' item whose Suffix is actually active contributes both treasure quality and 20 percentage points of Critical.

- C_from_Fates = 20 x N_fates

N_fates = number of currently equipped of-the-Fates items whose Suffix is actually active.

- Number of of-the-Fates items N = 1; Suffix contribution only = 20%; Additional total after adding Death Strike level 5* = 35%

- Number of of-the-Fates items N = 2; Suffix contribution only = 40%; Additional total after adding Death Strike level 5* = 55%

- Number of of-the-Fates items N = 3; Suffix contribution only = 60%; Additional total after adding Death Strike level 5* = 75%

- Number of of-the-Fates items N = 4; Suffix contribution only = 80%; Additional total after adding Death Strike level 5* = 95%

- Number of of-the-Fates items N = 5; Suffix contribution only = 100%; Additional total after adding Death Strike level 5* = 115% (probability interpretation capped at 100%)

* This column shows only the incremental contribution from 'Fates + Death Strike'; it does not include the weapon base item's inherent Critical or other Prefixes/Suffixes.

- Practical interpretation: the player impression that an all-Fates setup can skip Death Strike or rely less on Dwarven Rage has a mathematical basis. Multiple Fates items alone can build a very high nominal Critical rate. But to determine whether the total actually reaches 100%, you must include the current weapon's inherent Critical, other Critical affixes, and skills; you cannot estimate it from the number of 'red-name items' alone.

## 6. Exact machine-code probability: can the 15-bit RNG formula be applied directly to Critical?

We have already closed the code loop for FUN_0803A4BC in the drop/Prefix/Suffix systems. It takes a 15-bit random bucket 0..32767 and maps it to a closed interval. For a test written as 'rand(1,100) <= n', the exact probability is:

- P_bucket(n) = ceil(n x 32768 / 100) / 32768

This is the exact discrete-bucket formula for the known RNG helper.

- Nominal n = 5%; Exact bucket rate if combat also uses rand(1,100)<=n = 5.001831055%; Difference from nominal = +0.001831055 pp

- Nominal n = 10%; Exact bucket rate if combat also uses rand(1,100)<=n = 10.000610352%; Difference from nominal = +0.000610352 pp

- Nominal n = 15%; Exact bucket rate if combat also uses rand(1,100)<=n = 15.002441406%; Difference from nominal = +0.002441406 pp

- Nominal n = 20%; Exact bucket rate if combat also uses rand(1,100)<=n = 20.001220703%; Difference from nominal = +0.001220703 pp

- Nominal n = 30%; Exact bucket rate if combat also uses rand(1,100)<=n = 30.001831055%; Difference from nominal = +0.001831055 pp

- Nominal n = 55%; Exact bucket rate if combat also uses rand(1,100)<=n = 55.001831055%; Difference from nominal = +0.001831055 pp

- Nominal n = 65%; Exact bucket rate if combat also uses rand(1,100)<=n = 65.002441406%; Difference from nominal = +0.002441406 pp

- Nominal n = 100%; Exact bucket rate if combat also uses rand(1,100)<=n = 100.000000000%; Difference from nominal = 0

- Evidence boundary: the table above is a conditional calculation, not a proven combat-Critical measurement table. The project has confirmed FUN_0803A4BC only in the drop/equipment-generation probability chain; the latest 2026-08-24 handoff does not re-establish the full 'normal attack Critical check -> FUN_0803A4BC' call chain. The rigorous statement is therefore: nominal Critical values can be calculated exactly, while the final ~0.001%-level discrete bias of combat RNG still requires a dedicated call-site trace.

## 7. After a Critical occurs: relationship to damage and armor

## 7.1 Consensus at the guide level

GBA item/bonus guides describe Critical Hit as bypassing ordinary defense attributes, while Critical Armor reduces damage received from a Critical. Some long-standing player/guide descriptions summarize ordinary Criticals as 'double damage'. In addition, Dwarf Rune III provides '+5 Critical Hit Damage'.

## 7.2 How far the current code project can state this strictly

The combat reverse engineering as of 2026-08-24 has confirmed one Hook related to damage order: when Backstab succeeds, the weapon has Armor Piercing, and the target category=6 (Nazgul), core hit damage is multiplied by 2 first; this Hook occurs before later additive/Critical logic. This proves that Critical processing lies later in the damage chain, but the current handoff package has not re-derived the general Critical damage multiplier, Critical Damage bonus, and Critical Armor reduction as three independent machine-code formulas. This report therefore does not fabricate a closed-form 'final damage = ...' equation.

- Recommended next reverse-engineering step: to advance Critical from 'probability data closed' to 'full combat formula closed', trace later branches from the current hit-damage function to the read of attribute 0x23, the Critical state bit, the Critical Damage field, and the Critical Armor field; then run a dynamic matrix of normal hit/Critical, 0/several Critical Armor values, and 0/+5 Critical Damage.

## 8. Critical Armor and 'enemy Critical rate' are separate mechanisms

## 8.1 Critical Armor does not reduce the probability that you are critically hit

In item data, Critical Armor is a numeric defensive attribute: for example, a normal Helm can carry +12 Critical Armor, a Greathelm +20, and Unique/special helmets can carry +8, +12, and similar values. Its meaning is 'reduce Critical damage after being critically hit', not 'opponent Critical%-N'. Therefore, never subtract the target's Critical Armor when calculating the player's own Critical %.

## 8.2 Cursed affects the chance that enemies critically hit the player

An independent code conclusion completed earlier in the project is that the Cursed effect ID is 0x37. The incoming Critical rate from normal enemies is 10% when the player has no Cursed stack and becomes 20% when the player has at least one Cursed stack; additional Cursed stacks do not raise this probability further. This mechanism is not part of the same additive pool as the player's equipment Critical %.

- P_enemy_crit = 10% (no Cursed) ; 20% (Cursed count >= 1)

Multiple Cursed stacks do not further increase this normal incoming Critical rate.

- Do not mix the formulas: 'I have three Cursed items, so enemy Critical rate = 40%' is wrong; 'my Critical is 65% and the enemy has Critical Armor 12, so my chance is only 53%' is also wrong. The former is a Boolean-style trigger, while the latter mistakes a damage-mitigation attribute for a probability offset.

## 9. Practical expectation: converting Critical rate into an average number of Criticals

If we only ask how many Criticals occur on average over a number of attacks, and each attack's Critical check can be approximated as having the same probability, then:

- E[K] = N x P

N is the number of effective attacks, and P is the per-attack Critical probability.

- Critical rate P = 20%; Expected Criticals in 100 attacks = 20; Approximate probability of at least one Critical (N=10) = 1 - 0.8^10 = 89.26%

- Critical rate P = 45%; Expected Criticals in 100 attacks = 45; Approximate probability of at least one Critical (N=10) = 1 - 0.55^10 = 99.75%

- Critical rate P = 55%; Expected Criticals in 100 attacks = 55; Approximate probability of at least one Critical (N=10) = 1 - 0.45^10 = 99.97%

- Critical rate P = 65%; Expected Criticals in 100 attacks = 65; Approximate probability of at least one Critical (N=10) = 1 - 0.35^10 = 99.997%

- Critical rate P = 100%; Expected Criticals in 100 attacks = 100; Approximate probability of at least one Critical (N=10) = 100%

Note: this is only a statistical expectation. It does not establish whether the game's RNG has frame dependence, shared state across consecutive attacks, or special skill-specific checks. It is useful for comparing builds but cannot replace the machine-code call chain.

## 10. Most reliable calculation checklist at present

- Step = 1; What to do in practice = Check whether the weapon/base equipment has +N% Critical; Confidence = High: CodeBreaker table + ROM attribute table

- Step = 2; What to do in practice = Add every +N% Critical value from equipped Prefixes/Suffixes; Confidence = High: 0x23 attribute semantics + affix tables

- Step = 3; What to do in practice = Aragorn/Gimli Death Strike: add +3 per level; Legolas Blessing: +3 per level; Confidence = Medium-high: multiple guides agree; skill values were not re-traced to the skill machine table in this report

- Step = 4; What to do in practice = While Dwarven Rage is active, treat Critical rate as 100%; Confidence = Medium-high: multiple GBA guides agree; state-machine call site awaits dedicated decompilation

- Step = 5; What to do in practice = Calculate Critical Damage and Critical Armor separately; they do not enter Critical %; Confidence = High: item-attribute semantics are explicit

- Step = 6; What to do in practice = For exact sub-decimal machine probability, first locate the combat Critical RNG call site; Confidence = Highest rigor requirement: currently unresolved

## 11. Evidence levels and unresolved questions

- Conclusion = Critical attribute ID = 0x23; Status = Closed in code/ROM; Notes = Explicitly recorded in the latest technical handoff package

- Conclusion = Base equipment, Prefixes, and Suffixes can contain +N% Critical; Status = Strong cross-verification; Notes = CodeBreaker tables agree with the project's affix research

- Conclusion = of the Fates = +20% Critical; Status = Strong cross-verification; Notes = Repeatedly used as 0x9B in multiple project probability reports

- Conclusion = Death Strike = +3%/level; Status = Guide cross-verification; Notes = Aragorn and Gimli sources agree; 15% at max level

- Conclusion = Galadriel's Blessing = +3%/level; Status = Guide cross-verification; Notes = Legolas skill sources

- Conclusion = Dwarven Rage = temporary 100%; Status = Guide cross-verification; Notes = Dedicated Gimli guide and general guide agree

- Conclusion = Whether combat Critical calls FUN_0803A4BC; Status = Unresolved; Notes = Do not directly treat drop RNG bucket rates as combat conclusions

- Conclusion = Complete formula for the final general Critical damage multiplier; Status = Unresolved; Notes = Guides describe 'double damage / bypass ordinary defense', but the latest machine-code handoff does not fully close the chain

- Conclusion = Exact Critical Armor damage-reduction formula; Status = Unresolved; Notes = Semantics are clear; the exact arithmetic still requires tracing combat code

## 12. Main sources and evidence

- [R1] 2026-08-24 Technical Conclusions and Evidence Boundaries.md: current BLRE baseline, 12-byte records, attribute_id 0x23 = Critical %, RNG helper, combat Hook order, and evidence boundaries.

- [R2] gothmogsbane, 'The Lord of the Rings: The Return of the King - Codebreaker Code Guide' (GBA, GameFAQs FAQ 40090): Item Table, category-specific Prefix Tables, and Suffix Table.

- [R3] Scizor_CT, 'Item Modifier Stats' (GBA, GameFAQs FAQ 39712): game semantics of Critical %, Critical Armor, and related attributes.

- [R4] Yoni, 'Gimli Guide' (GBA, GameFAQs FAQ 28333): Death Strike, Dwarven Rage, skill duration, and Gimli gameplay notes.

- [R5] kingoftheelves, 'Skill Guide' (GBA, GameFAQs FAQ 28280): cross-check of character skills.

- [R6] zegota, 'Guide and Walkthrough' (GBA, GameFAQs FAQ 28833): Aragorn/Gimli Death Strike, Legolas's Galadriel's Blessing, Dwarven Rage, Dwarf Rune III, etc.

- [R7] Gothmog14, 'Item/Bonus Map Guide' (GBA, GameFAQs FAQ 39750): explanations of Critical and Critical Armor, plus cross-checks for weapons, affixes, and runes.

- [R8] Existing project report GBA Return of the King - Dark Sapphire of the Fates Accessory - Machine-Code Optimal Farming Method, Full Version: value and drop-research evidence anchors for 0x98 Dark Sapphire and 0x9B of the Fates.

- Final conclusion: at this stage, how Critical-rate values add is sufficiently clear: Critical is an independent percentage-point attribute; equipment, affixes, and corresponding passive skills add together; Dwarven Rage temporarily overrides the result to 100%. The only missing lower-level step is to re-close, instruction by instruction on the current baseline ROM, the final Critical RNG call in ordinary combat and the complete arithmetic chain for Critical Armor/Critical damage.
