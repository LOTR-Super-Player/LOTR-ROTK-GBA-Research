# GBA The Lord of the Rings: The Return of the King
Dark Sapphire of the Fates - Optimal Item Farming Analysis

## Target: red-name + non-Fragile + dual-affix Prefix/Suffix
Dark Sapphire (0x98) + of the Fates (0x9B)

- Target finished item = Best map group; Dark Sapphire [any valid Item base] of the Fates = Ithilien, Map ID 0x3A-0x3D

- Target finished item = Current build used in this report; Dark Sapphire [any valid Item base] of the Fates = Gimli: equipment Extra Treasure +25%, Keen Eyes level 4, Dwarf Sense level 3 -> effective quality modifier T=75

- Target finished item = Current optimal difficulty; Dark Sapphire [any valid Item base] of the Fates = Grueling

- Target finished item = Current first-priority sources; Dark Sapphire [any valid Item base] of the Fates = 0x27, 0x3C-0x42, 0x44, 0x45, 0x50 (100% reach equipment generation + Prefix base 100)

- Target finished item = Current probability conclusion; Dark Sapphire [any valid Item base] of the Fates = type-A weight 8% Profile + T75 + best source: about 1/381,020 full equipment rolls; 0x53 about 1/732,703

*Note: This report reproduces the analytical scope and conclusions of the latest Dark Sapphire of the Fates item analysis. It does not mix in the Prefix tables used for Spoons, weapons, gloves, or footwear. The core evidence is the current English ROM, decompiled functions, and the CodeBreaker item / Prefix / Suffix tables.*

> **Current project note:** The later character-profile study closed the C0-C7 identity mapping. Any older statement below that treats C1 as unnamed is superseded: C1 = Legolas. The original probability derivation is retained for provenance.

## 1. Target locked: 0x98 + 0x9B, with neither affix being Fragile

Items and headgear share the Head/Item Prefix Table. 0x98 = Dark Sapphire: +5 damage to Nazgul, +4 Defense, +90% Extra Treasure, and no Fragile property.

0x9B = of the Fates: +60% Extra Treasure, +20% Critical, also with no Fragile property.

Combined value of the two affixes: +150% Extra Treasure.

The item table defines It = Item and Ps = Item is capable of having a prefix/suffix. The 12 ordinary type-A item bases (00A-0BA), from Collar, Keep Sake, Choker, Pendant, Necklace, and Amulet through Brooch and Firinga, are all marked [It-Ps]. Therefore, if the target does not require a specific base item, there is no additional 1/12 specified-base probability factor.

In other words, Collar / Pendant / Necklace / Amulet / Brooch / Firinga, etc. all count as long as the final item is "Dark Sapphire XXXXX of the Fates".

## 2. Actual program call chain

FUN_0803A77C  ->  FUN_0803AE8C  ->  DAT_0806DBAC selects equipment type
->  FUN_0803A844 selects the specific base item  ->  FUN_0803AB4C generates Prefix
->  FUN_0803ACC4 generates Suffix

FUN_0803AE8C first selects an equipment type from DAT_0806DBAC according to the character/Profile, then enters FUN_0803A844. As long as the resulting type is not type F, it subsequently calls the Prefix function FUN_0803AB4C and the Suffix function FUN_0803ACC4.

P(complete target) = P(reach equipment generation) x P(type A) x P(Prefix gate)
x P(0x98) x P(Suffix gate) x P(0x9B)

This analysis treats "red-name + dual-affix" as a completed item with a valid Prefix and a valid Suffix. It does not multiply by a separate independent red-name random gate.

## 3. All 28 Item Prefix map groups in the game

FUN_08037A74() directly uses DAT_0806D20C / DAT_0806D20E to assign Map IDs to 0x00-0x1B, for a total of 28 Prefix regions. It then uses the type-A (Item) Prefix range table. The following table is specifically for Items, not Weapons and not Handwear/Footwear.

- Group = 0; Map region = Anduin River; Map ID = 00–03; Item Prefix main pool = 84–8F; Contains 0x98? = No

- Group = 1; Map region = Orc Tower; Map ID = 04–05; Item Prefix main pool = 0C–17; Contains 0x98? = No

- Group = 2; Map region = Cirith Ungol; Map ID = 06–0D; Item Prefix main pool = 0C–17; Contains 0x98? = No

- Group = 3; Map region = Corsair Flagship; Map ID = 0E–0F; Item Prefix main pool = 84–8F; Contains 0x98? = No

- Group = 4; Map region = Crack of Doom; Map ID = 10–12; Item Prefix main pool = 3C–47; Contains 0x98? = No

- Group = 5; Map region = Dimholt; Map ID = 13–17; Item Prefix main pool = 60–6B; Contains 0x98? = No

- Group = 6; Map region = Docks; Map ID = 18–19; Item Prefix main pool = 84–8F; Contains 0x98? = No

- Group = 7; Map region = Mt. Doom; Map ID = 1A–1D; Item Prefix main pool = 30–3B; Contains 0x98? = No

- Group = 8; Map region = Dunharrow; Map ID = 1E–23; Item Prefix main pool = 60–6B; Contains 0x98? = No

- Group = 9; Map region = Entwood; Map ID = 24–27; Item Prefix main pool = 48–53; Contains 0x98? = No

- Group = 10; Map region = Gorgoroth; Map ID = 28–2B; Item Prefix main pool = 24–2F; Contains 0x98? = No

- Group = 11; Map region = Hornburg; Map ID = 2C–2F; Item Prefix main pool = 54–5F; Contains 0x98? = No

- Group = 12; Map region = Ice Cave; Map ID = 30–31; Item Prefix main pool = 78–83; Contains 0x98? = No

- Group = 13; Map region = Isengard; Map ID = 32–39; Item Prefix main pool = 48–53; Contains 0x98? = No

- Group = 14; Map region = Ithilien; Map ID = 3A–3D; Item Prefix main pool = 90–9B; Contains 0x98? = Yes

- Group = 15; Map region = Minas Tirith; Map ID = 3E–43; Item Prefix main pool = 84–8F; Contains 0x98? = No

- Group = 16; Map region = Balcony; Map ID = 44; Item Prefix main pool = 84–8F; Contains 0x98? = No

- Group = 17; Map region = Morgul; Map ID = 45–49; Item Prefix main pool = 84–8F; Contains 0x98? = No

- Group = 18; Map region = Dwarf Halls; Map ID = 4A–50; Item Prefix main pool = 00–0B; Contains 0x98? = No

- Group = 19; Map region = Black Gate; Map ID = 51–54; Item Prefix main pool = 00–0B; Contains 0x98? = No

- Group = 20; Map region = Paths of the Dead; Map ID = 55–59; Item Prefix main pool = 6C–77; Contains 0x98? = No

- Group = 21; Map region = Pelennor Beacon; Map ID = 5A–5C; Item Prefix main pool = 84–8F; Contains 0x98? = No

- Group = 22; Map region = Pelennor Battle; Map ID = 5D–62; Item Prefix main pool = 84–8F; Contains 0x98? = No

- Group = 23; Map region = Pelennor Fields; Map ID = 63–6B; Item Prefix main pool = 84–8F; Contains 0x98? = No

- Group = 24; Map region = Rohan; Map ID = 6C–72; Item Prefix main pool = 54–5F; Contains 0x98? = No

- Group = 25; Map region = Shelob's Lair; Map ID = 73–75; Item Prefix main pool = 18–23; Contains 0x98? = No

- Group = 26; Map region = Amon Sul / Weathertop; Map ID = 76–79; Item Prefix main pool = 6C–77; Contains 0x98? = No

- Group = 27; Map region = White Mountains; Map ID = 7A–81; Item Prefix main pool = 78–83; Contains 0x98? = No

Conclusion: among all 28 groups, only the Ithilien Item main pool directly contains 0x98.
Ithilien is the optimal map group for Dark Sapphire items.

## 4. Ithilien 10% / 85% / 5% Prefix bypass branches

FUN_0803AB4C does not always use the current map main group. Its logic is: first call FUN_08037A74(current_map), then rand(1,100).

- 1-10: character bypass A

- 11-95: current map group

- 96-100: character bypass B

For Ithilien Group 14, the bypass interpretation used in this report is: 10% bypass A -> Group 15 (84-8F); 85% main route -> Group 14 (90-9B); 5% bypass B -> Group 14 (90-9B). Therefore, the actual probability of entering the 90-9B group that contains 0x98 is not 100%.

P(enter target group 90-9B) = (27853 + 1638) / 32768
= 89.9993896484%

## 5. Difficulty: exclude Normal; Grueling is optimal at T=75

Ithilien's full Item Prefix pool is 0x90-0x9B, containing 12 entries. FUN_0803AB4C truncates the tail of the pool by difficulty: Normal removes the last 4, Hard removes the last 2, and Grueling does not truncate it.

- Difficulty = Normal; Actual Prefix pool = 90-97 (8 entries); 0x98 Dark Sapphire = Impossible; Exact in-pool machine-code probability = 0

- Difficulty = Hard; Actual Prefix pool = 90-99 (10 entries); 0x98 Dark Sapphire = Available; Exact in-pool machine-code probability = 3277/32768 = 10.0006103516%

- Difficulty = Grueling; Actual Prefix pool = 90-9B (12 entries); 0x98 Dark Sapphire = Available; Exact in-pool machine-code probability = 2730/32768 = 8.3312988281%

Looking only at the 0x98 Prefix, Hard is more concentrated. However, the final target also requires 0x9B of the Fates, so the complete Suffix chain must also be multiplied in.

## 6. of the Fates reverses the Hard / Grueling comparison at T=75

The base Suffix-generation gates in FUN_0803ACC4 are Normal=2, Hard=3, and Grueling=4. FUN_08032A5C is then called to apply integer percentage scaling:

Threshold = B + floor(B x T / 100)

The current build used in this report is: equipment Extra Treasure +25%, Keen Eyes level 4 = +20, Dwarf Sense level 3 = +30. Therefore:

T = 25 + 20 + 30 = 75

- Difficulty = Hard; Suffix threshold at T=75 = 3 + floor(3×0.75) = 5; Exact machine-code Suffix-generation rate = 5.0018310547%

- Difficulty = Grueling; Suffix threshold at T=75 = 4 + floor(4×0.75) = 7; Exact machine-code Suffix-generation rate = 7.0007324219%

0x9B is in the highest Suffix segment, 90-9F. With all 10 Suffix tier buckets available, the machine-code probability of first selecting the final tier is 3276/32768 = 9.9975585938%. Difficulty then further truncates the final Suffix pool:

- Difficulty = Hard; Final Suffix pool = 90-9D (14 entries); 0x9B in-pool probability = 2340/32768 = 7.1411132813%

- Difficulty = Grueling; Final Suffix pool = 90-9F (16 entries); 0x9B in-pool probability = 2048/32768 = 6.25%

After multiplying the complete Prefix + Suffix chain: at T=75, Grueling > Hard.

## 7. Enemy / source: current first priority is a +100 Prefix D-class source

FUN_0803A77C determines whether a drop source can ultimately reach the full equipment generator. For source < 0x53, the random range comes from the source table. For source == 0x53, the game uses fixed rand(0x47,0x61), i.e. 71-97; when roll >= 71, execution enters FUN_0803AE8C.

The theoretically strongest enemy sources in the current ROM are:

0x27, 0x3C-0x42, 0x44, 0x45, 0x50

These sources have the following properties: first-layer drop gate = 100%; reach equipment generator = 100%; Prefix base bonus = +100. Therefore, with these sources there is no need to raise T to 234 before capping the Prefix gate - the source itself already pushes the Prefix gate to 100%.

## 8. Why 0x53 is only the second choice at the current T=75

The advantage of 0x53 is P(reach equipment generation)=100%, but FUN_0803AB4C gives source>=0x53 a Prefix base value of DAT_0300415C + 0x14. At the maximum drop tier, DAT_0300415C=10, so the base value is 30.

T=75: 30 + floor(30 x 0.75) = 52
P(Prefix generation | source=0x53) = 52.001953125%

By contrast, the +100 Prefix D-class sources above have a 100% Prefix gate. Therefore, under the T=75 build used here: a correct D-class source enemy > 0x53.

## 9. The exact on-screen enemy still requires a runtime closed-loop mapping

The source ID -> drop-math properties are already locked down, but the Ithilien on-screen enemy name -> source ID mapping has not yet been dynamically closed for every enemy. Therefore, an Orc / Troll name must not be forcibly assigned to source 0x3C, etc. without runtime confirmation.

The most direct confirmation method is to set a breakpoint at FUN_0803A77C. When the target enemy is killed, read R3; R3 & 0xFF is the source ID. If the value is 27, 3C-42, 44, 45, or 50, it is a current first-priority farming target. If it is 53, it is a second-tier source: 100% equipment rolls, but with a lower Prefix gate at T=75.

There is also an Ithilien special branch: FUN_0803A77C applies special handling when Map ID=0x3A, state=1, and source is 0x07-0x09. These sources therefore cannot be treated by directly applying the ordinary probability model.

## 10. Passive skills: not eligibility switches, but strongly recommended at maximum

Keen Eyes and Dwarf Sense are not hard eligibility switches like an Ancient eligibility bit. Dark Sapphire of the Fates can still be generated without them. However, this target requires both Prefix and Suffix, so quality modifiers are extremely valuable.

Skill values used in this report: Keen Eyes gives +5% better items per level; Dwarf Sense gives +10% better items per level. At maximum:

Keen Eyes 5/5 = +25
Dwarf Sense 5/5 = +50
Combined = +75 equivalent quality modifier

Therefore, for Gimli the recommendation is to maximize both skills. At the current T=75, Grueling is optimal. If equipment ET remains 25% but both passives are maxed, then T=25+25+50=100; the complete mathematics reverses and the recommended difficulty becomes Hard.

## 11. How much Extra Treasure is actually needed?

For the best +100 Prefix enemy sources above, there is no hard "minimum ET required for the item to exist" because the source itself already caps the Prefix gate at 100%. Further ET mainly improves the Suffix-generation rate for of the Fates.

If farming source=0x53, the Prefix base is 30 at the maximum drop tier. To cap the Prefix gate at 100%, the requirement is:

30 + floor(30T/100) >= 100
-> T >= 234

234 refers to total quality modifier T, not equipment-panel ET alone. With the current passives used in this report - Keen Eyes level 4 (+20) and Dwarf Sense level 3 (+30), totaling 50 - if the passives are not upgraded, equipment ET must be at least 234-50=184%. Current equipment ET is 25%, so about +159% more is needed.

If both passives are first maxed to +75, capping the source=0x53 Prefix gate only requires equipment ET >= 234-75=159%, i.e. about +134% more than the current +25% equipment ET. Recommended upgrade order: first maximize Keen Eyes / Dwarf Sense, then continue stacking equipment ET.

## 12. Complete machine-code probability at T=75

The following calculation uses a character Profile with 8% type-A weight and assumes the drop level has reached the maximum tier used by this analysis.

## 12.1 First place: +100 Prefix D-class source + Grueling

- Probability stage = type A; Machine-code probability = 8.001708984%

- Probability stage = Ithilien -> target group 90-9B; Machine-code probability = 89.999389648%

- Probability stage = Prefix-generation gate; Machine-code probability = 100%

- Probability stage = Hit 0x98 Dark Sapphire; Machine-code probability = 8.331298828%

- Probability stage = Suffix-generation gate (T75, Grueling); Machine-code probability = 7.000732422%

- Probability stage = Enter highest Suffix segment 90-9F; Machine-code probability = 9.997558594%

- Probability stage = Hit 0x9B of the Fates; Machine-code probability = 6.25%

P(complete target) ~= 0.0002624536%
~= 1 / 381,020 full equipment rolls

## 12.2 Hard under the same conditions

~= 1 / 388,832

Therefore, at T=75, Grueling is clearly better than Hard.

## 12.3 source=0x53 + Grueling

Multiply the chain above by the current 0x53 Prefix gate of 52.001953125%:

~= 1 / 732,703

## 13. Best character: the strict code-level conclusion is Profile C1, not a forced character name

FUN_0803AE8C actually reads the eight DAT_0806DBAC Profiles indexed by DAT_03003D9F. Re-reading the table gives the following type-A / Item weights:

- Profile = C0; type A / Item weight = 8%

- Profile = C1; type A / Item weight = 9% (highest)

- Profile = C2; type A / Item weight = 8%

- Profile = C3; type A / Item weight = 8%

- Profile = C4; type A / Item weight = 8%

- Profile = C5; type A / Item weight = 8%

- Profile = C6; type A / Item weight = 8%

- Profile = C7; type A / Item weight = 8%

C1: 2949/32768 = 8.999633789%
The other seven profiles: 2622/32768 = 8.001708984%

The rigorous conclusion supported purely by code is therefore that C1 has the highest base Item weight. The earlier claim that C1 was directly "Legolas" lacked sufficient evidence, so the strict version no longer forces a character name. The current symbol/character mapping is not sufficient to name C0-C7 one by one without ambiguity.

## 14. Practical farming character: Gimli is still directly usable

In practice, Gimli can be used immediately. This is not because his type-A weight is proven to be the highest, but because the Gimli build used here already has Extra Treasure plus Keen Eyes / Dwarf Sense quality modifiers, which significantly improve the difficult Suffix gate for a dual-affix target.

Immediate route: Ithilien 0x3A-0x3D -> Grueling -> find enemies whose runtime source is 0x27 / 0x3C-0x42 / 0x44 / 0x45 / 0x50 -> keep only "Dark Sapphire XXXXX of the Fates".

Next stage: Keen Eyes 5/5 + Dwarf Sense 5/5 -> if equipment ET remains +25, T=100 -> switch to Hard.

Using the same "best D-class source + 8% type-A Profile" basis, T=100 + Hard gives a complete-target rate of about 1/323,994, roughly 17.6% better than T75 + Grueling at about 1/381,020.

If total T reaches 234, the Prefix gate for 0x53 sources also caps at 100%. At that point, an 8% type-A Profile + Hard is about 1/194,475.

## 15. Final execution checklist

- Character: use Gimli immediately.

- Map: Ithilien, Map ID 0x3A-0x3D.

- Current difficulty: Grueling (this report uses T=75).

- Current first-priority sources: 0x27, 0x3C-0x42, 0x44, 0x45, 0x50.

- 0x53: 100% equipment rolls, but only about a 52% Prefix gate at the current T75, so it is second priority.

- Keen Eyes and Dwarf Sense: continue leveling both to maximum.

- With both passives maxed and equipment ET still +25 -> T=100; switch to Hard.

- To cap the 0x53 Prefix gate: target total T=234.

- Final acceptance criterion: Dark Sapphire XXXXX of the Fates only.

- Neither affix is Fragile; the finished item itself provides a combined +150% Extra Treasure.

The only major missing piece is still the complete runtime closed-loop mapping between every specific on-screen enemy name in Ithilien and its source ID. Under the strict research standard, no specific enemy name is bound to a source ID without runtime verification.

# Appendix: evidence anchors

- GBA The Lord of the Rings: The Return of the King - Revised Decompiled Code: FUN_0803A6E8 / FUN_0803A77C / FUN_0803A844 / FUN_0803AB4C / FUN_0803ACC4 / FUN_0803AE8C; key section approximately pages 966-976.

- English Return of the King CodeBreaker Code Guide v1.20: 4.10 Item Table, 4.35 Head/Item Prefix Table, 4.40 Suffix Table.

- Key item-table values: 0x98 Dark Sapphire = +90% Extra Treasure; 0x9B of the Fates = +60% Extra Treasure +20% Critical; neither has Fragile.

- Equipment-type flags: It = Item; Ps = capable of having a prefix/suffix; all 12 ordinary Item bases from 00A-0BA are [It-Ps].

- Map-group function: FUN_08037A74; DAT_0806D20C / DAT_0806D20E.

- Character/Profile equipment-type weights: FUN_0803AE8C + DAT_0806DBAC.

- Enemy/source probability tiers and the +100 Prefix source list follow the ROM source-table conclusions already established in the code-evidence reports for precise special-item drop rates, the final Spoon analysis, and Blood Pirate of the Fates.
