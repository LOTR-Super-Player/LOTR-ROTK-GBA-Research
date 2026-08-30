# GBA The Lord of the Rings: The Return of the King
Blood Pirate Gloves / Footwear of the Fates - 15 Conclusions

## Target: red-name + non-Fragile + dual-affix Prefix/Suffix - Code/ROM Evidence Edition

- Core lock: Blood Pirate (0x51) + of the Fates (0x9B). Neither affix has Fragile. If generated on a glove/footwear base that can carry affixes, the result satisfies red-name + dual-affix + non-Fragile.

*Scope: This report fully records the 15 conclusions established in this analysis. Core probabilities and Prefix/Suffix ranges are based primarily on the current English ROM, decompiled functions, and the CodeBreaker item table. Web guides are used only to assist with enemy/chest routes and are not treated as core evidence for the probability chain.*

## 1. Final optimal route: Anduin River

*The preferred region is Anduin River, especially Corsair Camp, Corsair Chase, and Anduin Banks. The key reason is not subjective drop feel; it is that the Handwear/Footwear map Prefix group places this region directly in 0x48-0x53.*

- Handwear / Footwear Prefix range = 0x48-0x53 / Blood Pirate = 0x51

*The main pool and both character bypass branches for this region still land in groups containing 0x51. Therefore, the probability of entering a Prefix group that contains Blood Pirate can reach 100%. Only after that does the game randomly select the specific 0x51 entry within the group.*

*Practical farming priority: Anduin River > most ordinary maps. The route also benefits from high enemy density, hero enemies, and multiple chests.*

## 2. Co-best map: Corsair Flagship

*Corsair Flagship uses the same 0x48-0x53 main pool, and both the 10% and 5% bypass branches also return to groups containing 0x51. Therefore, it likewise achieves a 100% chance of entering a Prefix group containing Blood Pirate.*

*Considering Prefix-hit conditions alone, Anduin River and Corsair Flagship are tied for first. When enemy count, route length, chests, and practical reset/farming convenience are included, Anduin River remains the preferred farming route.*

## 3. All 28 Prefix map groups have been reorganized from the ROM grouping function

*The decompiled function FUN_08037A74() uses DAT_0806D20C / DAT_0806D20E to merge Map IDs into 28 Prefix regions. The Prefix function then uses the current map group as the main pool and adds 10% and 5% character bypass branches.*

- uVar1 = 0; / if (3 < map_id) { / do { / uVar1++; / if (0x1B < uVar1) break; / } while (map_id < DAT_0806D20C[...] || DAT_0806D20E[...] < map_id); / }

- Group = 0; Map region = Anduin River; Map ID = 00–03; Handwear/Footwear main Prefix pool = 48–53; Contains 0x51? = Yes

- Group = 1; Map region = Orc Tower; Map ID = 04–05; Handwear/Footwear main Prefix pool = 00–0B; Contains 0x51? = No

- Group = 2; Map region = Cirith Ungol; Map ID = 06–0D; Handwear/Footwear main Prefix pool = 00–0B; Contains 0x51? = No

- Group = 3; Map region = Corsair Flagship; Map ID = 0E–0F; Handwear/Footwear main Prefix pool = 48–53; Contains 0x51? = Yes

- Group = 4; Map region = Crack of Doom; Map ID = 10–12; Handwear/Footwear main Prefix pool = 18–23; Contains 0x51? = No

- Group = 5; Map region = Dimholt; Map ID = 13–17; Handwear/Footwear main Prefix pool = 30–3B; Contains 0x51? = No

- Group = 6; Map region = Docks; Map ID = 18–19; Handwear/Footwear main Prefix pool = 48–53; Contains 0x51? = Yes

- Group = 7; Map region = Mt. Doom; Map ID = 1A–1D; Handwear/Footwear main Prefix pool = 18–23; Contains 0x51? = No

- Group = 8; Map region = Dunharrow; Map ID = 1E–23; Handwear/Footwear main Prefix pool = 30–3B; Contains 0x51? = No

- Group = 9; Map region = Entwood; Map ID = 24–27; Handwear/Footwear main Prefix pool = 24–2F; Contains 0x51? = No

- Group = 10; Map region = Gorgoroth; Map ID = 28–2B; Handwear/Footwear main Prefix pool = 0C–17; Contains 0x51? = No

- Group = 11; Map region = Hornburg; Map ID = 2C–2F; Handwear/Footwear main Prefix pool = 30–3B; Contains 0x51? = No

- Group = 12; Map region = Ice Cave; Map ID = 30–31; Handwear/Footwear main Prefix pool = 48–53; Contains 0x51? = Yes

- Group = 13; Map region = Isengard; Map ID = 32–39; Handwear/Footwear main Prefix pool = 24–2F; Contains 0x51? = No

- Group = 14; Map region = Ithilien; Map ID = 3A–3D; Handwear/Footwear main Prefix pool = 54–5F; Contains 0x51? = No

- Group = 15; Map region = Minas Tirith; Map ID = 3E–43; Handwear/Footwear main Prefix pool = 54–5F; Contains 0x51? = No

- Group = 16; Map region = Balcony; Map ID = 44; Handwear/Footwear main Prefix pool = 54–5F; Contains 0x51? = No

- Group = 17; Map region = Morgul; Map ID = 45–49; Handwear/Footwear main Prefix pool = 54–5F; Contains 0x51? = No

- Group = 18; Map region = Dwarf Halls; Map ID = 4A–50; Handwear/Footwear main Prefix pool = 00–0B; Contains 0x51? = No

- Group = 19; Map region = Black Gate; Map ID = 51–54; Handwear/Footwear main Prefix pool = 00–0B; Contains 0x51? = No

- Group = 20; Map region = Paths of the Dead; Map ID = 55–59; Handwear/Footwear main Prefix pool = 3C–47; Contains 0x51? = No

- Group = 21; Map region = Pelennor Beacon; Map ID = 5A–5C; Handwear/Footwear main Prefix pool = 54–5F; Contains 0x51? = No

- Group = 22; Map region = Pelennor Battle; Map ID = 5D–62; Handwear/Footwear main Prefix pool = 54–5F; Contains 0x51? = No

- Group = 23; Map region = Pelennor Fields; Map ID = 63–6B; Handwear/Footwear main Prefix pool = 54–5F; Contains 0x51? = No

- Group = 24; Map region = Rohan; Map ID = 6C–72; Handwear/Footwear main Prefix pool = 30–3B; Contains 0x51? = No

- Group = 25; Map region = Shelob's Lair; Map ID = 73–75; Handwear/Footwear main Prefix pool = 0C–17; Contains 0x51? = No

- Group = 26; Map region = Amon Sul / Weathertop; Map ID = 76–79; Handwear/Footwear main Prefix pool = 3C–47; Contains 0x51? = No

- Group = 27; Map region = White Mountains; Map ID = 7A–81; Handwear/Footwear main Prefix pool = 48–53; Contains 0x51? = Yes

*Important: this is the Handwear/Footwear table, not the Weapon table used in the earlier Spoon research. The same map can correspond to completely different Prefix ranges for different equipment types. For example, in Ithilien the Weapon main pool is 90-9B, while the Handwear/Footwear main pool is 54-5F.*

## 4. Actual map ranking for Blood Pirate

*Looking only at the 85% main pool, Groups 0, 3, 6, 12, and 27 all contain 0x51. After also accounting for the Prefix selector's 85% main route + 10% bypass + 5% bypass, the ranking separates further:*

*Anduin River: all three branches still contain 0x51 -> 100% enter the target Prefix group.*

*Corsair Flagship: all three branches still contain 0x51 -> 100%.*

*Docks: about 95.0012% enter the target group.*

*Ice Cave: about 95.0012% enter the target group.*

*White Mountains: about 89.9994% enter the target group.*

*Therefore, the full-game map priority is: Anduin River = Corsair Flagship > Docks ~= Ice Cave > White Mountains > other maps.*

## 5. First key difficulty conclusion: Blood Pirate 0x51 is more concentrated on Hard

*The target Prefix sits in the 12-entry pool 0x48-0x53. Normal removes the last four entries, Hard removes the last two, and Grueling retains all 12 entries.*

- Normal : 0x48-0x4F -> 0x51 is truncated -> impossible / Hard : 0x48-0x51 -> 10 entries, 0x51 retained / Grueling: 0x48-0x53 -> 12 entries

*Normal is therefore eliminated. On Hard, Blood Pirate is the last entry in a 10-entry pool, with an exact machine-code bucket probability of about 9.997558594%. On Grueling it is one of 12 entries, about 8.334350586%. Looking only at the Prefix, Hard is better than Grueling.*

## 6. of the Fates 0x9B is generated independently from the Prefix

*The Suffix is generated by a separate function. of the Fates = 0x9B, located in the 90-9F Suffix segment. To obtain the target Suffix, the game must first enter the 90-9x segment and then hit 0x9B within that segment.*

*Difficulty also affects the Suffix gate: Normal base gate = 2, Hard = 3, Grueling = 4; total Treasure then scales that base. Conceptually:*

- Suffix threshold = base + floor(base × TotalTreasure / 100)

*Therefore, Blood Pirate's Hard pool-truncation advantage competes with the higher Grueling Suffix gate for of the Fates. The final decision must use the complete finished-item probability, not a single affix in isolation.*

## 7. Best character: Gimli, not Frodo/Sam

*Spoon is type0, which is why Frodo/Sam were strong on earlier Spoon routes. This target, however, is gloves and footwear, corresponding to Handwear (type C) and Footwear (type D). The CodeBreaker item table confirms Hw=Handwear, Fw=Footwear, and ordinary gloves/footwear have Ps (can carry Prefix/Suffix).*

*Under the current character equipment-type Profiles, Gimli is about 7% for both type C and type D, so he supports both Blood Pirate Gloves of the Fates and Blood Pirate Footwear of the Fates. Some other characters have asymmetric C/D weights.*

*Considering access to the Anduin route, glove/footwear type weights, and Treasure-related passives together, the optimal practical character is Gimli.*

## 8. Passive skills: maximize both Keen Eyes and Dwarf Sense

*When Gimli farms these two targets, the quality-related passives should be treated as part of the optimal build:*

*Keen Eyes 5/5: +25% better items in total.*

*Dwarf Sense: under the calculation convention used in this report, maximum rank contributes +50% better items in total.*

*Together they provide about +75% quality modifier. Because this target requires both Prefix and Suffix, the modifier not only affects the Prefix-generation gate but also continues improving the Suffix-generation gate. The goal should not be merely to reach the point where Blood Pirate can appear.*

## 9. Key Extra Treasure threshold: 234% caps the 0x53 Prefix gate

*For the high-value source=0x53 loot source, this analysis uses a Prefix base threshold of loot tier + 20. At the maximum loot tier=10, the base value is 30. After Total Treasure is applied:*

- Prefix threshold = 30 + floor(30 x T / 100) / Requirement for 100: 30 + floor(0.30T) >= 100 / Minimum Total T ~= 234%

*Therefore, at total T of about 234%, the Prefix-generation gate for source=0x53 can cap at 100%. For Gimli, if Keen Eyes + Dwarf Sense contribute 75 in total, equipment needs about 159% ET to reach this Prefix-cap threshold.*

*However, 234% does not mean further ET is useless. Once the Prefix gate is capped, the Suffix gate still continues to benefit from ET, so higher total Treasure remains better.*

## 10. Complete finished-item rate for standard Treasure Hunter Gimli (total T ~= 405%)

*Using the Treasure Hunter Gimli build in this analysis at total T ~= 405%, and choosing maps such as Anduin / Corsair Flagship that have a 100% chance of entering a group containing Blood Pirate, the complete chain is:*

*type C (Gloves) ~= 7.000732422%; type D (Footwear) ~= 7.000732422%.*

*On Hard, the exact in-pool bucket probability for Blood Pirate 0x51 is ~= 9.997558594%.*

*At T ~= 405%, the Prefix-generation gate for source 0x53 is already capped at 100%.*

*On Hard, the Suffix gate is calculated from base=3 and total T, after which the game enters the 90-9x segment and hits 0x9B.*

*Final result: Blood Pirate Gloves of the Fates ~= 1 / 133,395 full equipment rolls; Blood Pirate Footwear of the Fates ~= 1 / 133,395 full equipment rolls. Gimli has the same weight for type C and D, so both finished-item rates are identical.*

## 11. Higher ET continues to improve the finished-item rate substantially

*If total T rises to about 605%, the complete-target rate on Hard improves to about 1 / 95,288 for each item.*

*If equipment itself already supplies 605%, then adding Gimli's two passives at about +75 gives total T ~= 680%, improving Hard further to about 1 / 87,007 full equipment rolls per item.*

*Therefore, these two items are especially well suited to a snowballing Treasure Hunter build, not merely a vaguely "high ET" build: obtaining either completed item first makes the second one easier to farm.*

## 12. Why Hard remains the general recommendation instead of mechanically using Grueling

*This target sits at the special position Blood Pirate=0x51. Hard removes 0x52 Royal and 0x53 Illustrious while retaining 0x51, compressing the target Prefix from roughly 1 out of 12 to roughly 1 out of 10.*

*Although Grueling increases the Suffix-generation gate, it also puts 0x52 and 0x53 back into the Prefix pool. After the two effects compete, representative ET tiers in this analysis are:*

- Total T = 405%; Hard = about 1/133,395; Grueling = about 1/137,137

- Total T = 605%; Hard = about 1/95,288; Grueling = about 1/97,951

*Therefore, at practical tiers such as 405% and 605%, Hard is slightly better. Because the formula contains floor-based integer breakpoints, Grueling can briefly overtake at isolated integer ET values, but the rule "always use Grueling for this target" is incorrect.*

## 13. Enemy / drop-source priority: seek full equipment rolls first, rather than blindly clearing ordinary enemies

*The final finished-item probability must first be multiplied by whether the source actually reaches the full equipment generator. Therefore, the strongest source priorities are:*

*First priority: runtime-confirmed source=0x53 loot-bearing chest / equivalent high-value full-equipment-roll source.*

*Second priority: high-value source enemies that reach the equipment generator 100% of the time.*

*Third priority: ordinary A-class enemies, relying only on kill volume.*

*The previously established exact machine-code probability for an ordinary A-class enemy to reach the equipment generator is about 16.498962417%. If the target rate per full equipment roll is about 1/133,395, multiplying by that entrance gate worsens the per-enemy rate to roughly 1/808,508.*

*Evidence boundary: in Anduin, Corsair Hero, Half-Troll, and other high-value targets can be tested first, but the screen enemy name <-> source ID runtime mapping is not yet complete. Therefore, no specific on-screen enemy should be assigned a source ID without runtime verification.*

## 14. Optimal farming method that can be executed immediately

*If the goal is to obtain red-name + non-Fragile + Blood Pirate + of the Fates gloves and footwear as quickly as possible, use the following plan:*

*Character: Gimli.*

*Map: Anduin River, prioritizing Corsair Chase / Corsair Camp; Corsair Flagship is the co-best Prefix alternative.*

*Difficulty: Hard.*

*Keen Eyes: 5/5.*

*Dwarf Sense: maximize as far as possible.*

*Total Treasure: reach at least about 234% to cap the 0x53 Prefix gate; in practice continue stacking higher, because the Suffix keeps benefiting.*

*Source: prioritize 0x53 chests or verified 100%-equipment-generation sources.*

*Keep only "Blood Pirate XXXXX of the Fates". Prefix-only and Suffix-only versions do not count as the final target.*

*Equipment-type identification: type C = Handwear (gloves), type D = Footwear.*

*The two affixes are also highly valuable for ET: Blood Pirate gives +45% Extra Treasure; of the Fates gives +60% Extra Treasure. The completed item therefore provides +105% Extra Treasure by itself, making it an excellent snowball item for farming the second piece.*

## 15. Final lock and conclusion boundaries

*The optimal solution can be condensed to:*

- Blood Pirate Gloves of the Fates = Blood Pirate Footwear of the Fates; Gimli + Anduin River / Corsair Flagship + Hard + high ET + max Keen Eyes + max Dwarf Sense = Exactly the same optimal setup as the gloves

*With the standard Treasure Hunter Gimli build used in this report at total T ~= 405%, and treating every attempt as a full equipment roll: gloves ~= 1/133,395; footwear ~= 1/133,395. At total T ~= 605%, this improves to about 1/95,288 per item.*

*Important boundary: this report analyzes the C/D-class Blood Pirate of the Fates gloves/footwear target. It does not mix in the type0 Spoon probability chain. Although Spoon was mentioned near the end of the original discussion, all 15 conclusions in this report apply only to the Blood Pirate + of the Fates glove/footwear target.*

*Code and source evidence anchors (for future verification)*

*FUN_08037A74: Map ID -> 28 Prefix regions.*

*DAT_0806D20C / DAT_0806D20E: range table for the 28 regions.*

*Prefix-generation function: 85% current map group + 10% bypass A + 5% bypass B, with difficulty-based truncation.*

*Handwear / Footwear Prefix Table: 0x51 = Blood Pirate (+45% Extra Treasure).*

*Suffix Table: 0x9B = of the Fates (+60% Extra Treasure, +20% Critical).*

*CodeBreaker Item Table: Hw=Handwear, Fw=Footwear, Ps=can carry Prefix/Suffix.*

*DAT_0300415C-related generation path: total Treasure modifier feeds into Prefix/Suffix thresholds.*

*source -> equipment-generator chain: source quality determines how often each kill / chest opening actually produces a full equipment roll.*

*Note: the full-map runtime mapping between on-screen enemy names and source IDs is not yet complete. Therefore, this report preserves the evidence boundary for specific enemy IDs and does not substitute speculation for ROM/runtime verification.*
