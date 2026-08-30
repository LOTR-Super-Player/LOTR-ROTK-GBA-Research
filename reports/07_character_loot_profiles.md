# GBA The Lord of the Rings: The Return of the King

## Inherent Treasure Drop Profiles of the Eight Characters

*Complete Profile C0-C7 Mapping · Equipment-Type Weights · Ancient Probabilities · Type E Rare Pool · Neutral-Enemy Reference*

- Item = Verified Targets; Details = BLRE English original + current BLRJ Chinese Ultimate Collection / Nazgûl-enhanced build

- Item = Original ROM SHA-256; Details = b5f556593c5fbe3531a4354b047f0bed364a124ceee43f278a48f4d26b0ffa9c

- Item = Current Nazgûl-Enhanced Build SHA-256; Details = 4b0d27569245337a63480b5687430fb2368f85ff289aa09b800df6cf4db69edd

- Item = Character-Mapping Basis; Details = Direct binary closure between the high 4 bits of save record +0x65 (Profile) and named character SAV files

- Item = Primary Probability Basis; Details = Each full equipment-generator call (full equipment roll)

- Item = Version Date; Details = 2026-08-22

*Important: In this report, "inherent treasure drop rate" refers to the equipment-category Profile built into each character in the ROM. It is not the Extra Treasure value on equipment, nor is it an enemy's outer-layer drop rate.*

## 0. Key Conclusions

- The eight characters do not each have an independent fixed percentage for "total equipment drops." For the same enemy/source, the outer layer—whether a drop occurs and whether the full equipment generator is entered—is determined mainly by the enemy source. The character Profile determines how the roll is distributed among the 15 equipment categories after the equipment generator has been entered.

- All eight Profiles can now be mapped completely to the eight characters: C0=Frodo, C1=Legolas, C2=Aragorn, C3=Gandalf, C4=Éowyn, C5=Gimli, C6=Sam, and C7=Gollum.

- All eight characters have the same weight for the type E special/Unique category: the table value is 1%. Under the actual 32,768-bucket implementation of rand(1,100), this corresponds to 327/32768 = 0.997924805%. Therefore, type E weight alone cannot tell us which character is inherently more likely to obtain the 14 red-name backpack rares or the ordinary blue-name rares.

- What actually differentiates each character's "loot structure" is the category distribution across type 0 through D. This can materially change the base hit rate for a specific Ancient, Spoon, bow, axe, shield, item/accessory, handwear, footwear, and so on.

- With Ancient eligibility enabled and the candidate pool fully expanded, the highest combined probability for any of the 10 Ancients belongs to Frodo/Sam/Gollum at 7.065365190% per equipment-generation event; the lowest belongs to Gandalf at 5.157031857%.

- Extra Treasure, Keen Eyes, and Dwarf Sense belong to the "quality modifier" system. They mainly improve Prefix/Suffix quality gates and must not be conflated with the inherent character-category Profile as a single "drop rate."

## 1. Which Probability Layer Does "Inherent Treasure Drop Rate" Actually Refer To?

- Probability Layer = Layer 1: Enemy/Chest Source; Code Meaning = Determines whether death produces a drop entity and whether the full equipment generator is entered.; Effect on "Character Loot Rate" = A character usually cannot directly turn an ordinary Class-A source into a Class-D source.

- Probability Layer = Layer 2: Character Profile; Code Meaning = FUN_0803AE8C uses the character Profile to read 15 cumulative thresholds from DAT_0806DBAC and determines which category, type 0 through E, the current item belongs to.; Effect on "Character Loot Rate" = This is what this report defines as the character's "inherent drop structure."

- Probability Layer = Layer 3: In-Type Candidate; Code Meaning = Within the selected equipment type, a specific base item is drawn according to the current drop level/candidate pool; Ancients are additionally controlled by flags 0x40 and a global eligibility bit.; Effect on "Character Loot Rate" = Therefore, a given Ancient does not always have the same probability for the same character at different levels.

- Probability Layer = Layer 4: Prefix/Suffix Quality; Code Meaning = Extra Treasure / Keen Eyes / Dwarf Sense enter the quality-threshold calculation.; Effect on "Character Loot Rate" = They affect completion rates for high-end red-name/double-affix items, but they are not Profile category weights.

- Probability Layer = Layer 5: Special Eligibility Bits; Code Meaning = Within type E, the first 10 blue-name rares, the 14 red-name items, and Orc Head/Drum still pass through Mask / RedMask / fallback logic.; Effect on "Character Loot Rate" = Even under the same Profile, different save eligibility states can still produce different rare-item outcomes.

## 2. Profile ↔ Eight Characters: Direct Save-File Closure Completed

The decompiled save-reading function explicitly performs DAT_03003D9F[character] = save_record[0x65] >> 4. In other words, the high 4 bits of +0x65 in each 0x70-byte character save record are the character Profile ID. Binary verification against clearly named SAV files then maps C0-C7 to specific characters.

- Profile = C0; Character = Frodo; SAV Binary Evidence = Target character record in the "Level 52 max-level Frodo" save; Conclusion = Direct

- Profile = C1; Character = Legolas; SAV Binary Evidence = Target character record in the "Level 46 Legolas" save; Conclusion = Direct

- Profile = C2; Character = Aragorn; SAV Binary Evidence = Target character record in the "Level 40 Aragorn" save; Conclusion = Direct

- Profile = C3; Character = Gandalf; SAV Binary Evidence = Target character record in the "Level 39 Gandalf" save; Conclusion = Direct

- Profile = C4; Character = Éowyn; SAV Binary Evidence = Target character record in the "Level 49/52 Éowyn" save; Conclusion = Direct

- Profile = C5; Character = Gimli; SAV Binary Evidence = Single-character "Level 52 max-level Gimli" save, Slot 1; Conclusion = Direct; the other three slots are empty, making this the cleanest sample

- Profile = C6; Character = Sam; SAV Binary Evidence = Target character record in the "Level 52 max-level Sam" save; the Frodo storyline also contains special C0↔C6 switching code; Conclusion = Direct + code cross-check

- Profile = C7; Character = Gollum; SAV Binary Evidence = Target character record in the "Level 17 Gollum" save; Conclusion = Direct

Verified save package: modified red_mask state.zip, SHA-256 = ebaa09e26561796cf4cb53b0cf60ee7cac761100aefbd4f0b3f1545d8cd40338

## 3. Eight-Character Overview: Inherent Category Profile + Total Ancient Rate

- Character = Frodo; Profile = C0; Highest-Weight Categories (Table Values) = Short blade/small weapon 15%; armor 15%; axe 10%; blunt weapon/mace 10%; Item A / Machine-Exact = 8.001708984%; type E / Machine-Exact = 0.997924805%; Any of 10 Ancients / Full Pool / per Equipment Roll = 7.065365190%; Neutral Class-A Enemy / Any-Ancient Reference = 1.165712%

- Character = Legolas; Profile = C1; Highest-Weight Categories (Table Values) = Armor 17%; bow 15%; short blade/small weapon 10%; arrows 10%; Item A / Machine-Exact = 8.999633789%; type E / Machine-Exact = 0.997924805%; Any of 10 Ancients / Full Pool / per Equipment Roll = 5.638189588%; Neutral Class-A Enemy / Any-Ancient Reference = 0.930243%

- Character = Aragorn; Profile = C2; Highest-Weight Categories (Table Values) = Armor 15%; shield 15%; one-handed sword 11%; item/accessory 8%; Item A / Machine-Exact = 8.001708984%; type E / Machine-Exact = 0.997924805%; Any of 10 Ancients / Full Pool / per Equipment Roll = 6.574203574%; Neutral Class-A Enemy / Any-Ancient Reference = 1.084675%

- Character = Gandalf; Profile = C3; Highest-Weight Categories (Table Values) = Armor 15%; short blade/small weapon 10%; one-handed sword 10%; staff 10%; Item A / Machine-Exact = 8.001708984%; type E / Machine-Exact = 0.997924805%; Any of 10 Ancients / Full Pool / per Equipment Roll = 5.157031857%; Neutral Class-A Enemy / Any-Ancient Reference = 0.850857%

- Character = Éowyn; Profile = C4; Highest-Weight Categories (Table Values) = Armor 15%; shield 15%; one-handed sword 11%; item/accessory 8%; Item A / Machine-Exact = 8.001708984%; type E / Machine-Exact = 0.997924805%; Any of 10 Ancients / Full Pool / per Equipment Roll = 6.574203574%; Neutral Class-A Enemy / Any-Ancient Reference = 1.084675%

- Character = Gimli; Profile = C5; Highest-Weight Categories (Table Values) = Axe 15%; armor 15%; shield 11%; blunt weapon/mace 10%; Item A / Machine-Exact = 8.001708984%; type E / Machine-Exact = 0.997924805%; Any of 10 Ancients / Full Pool / per Equipment Roll = 6.422688423%; Neutral Class-A Enemy / Any-Ancient Reference = 1.059677%

- Character = Sam; Profile = C6; Highest-Weight Categories (Table Values) = Short blade/small weapon 15%; armor 15%; axe 10%; blunt weapon/mace 10%; Item A / Machine-Exact = 8.001708984%; type E / Machine-Exact = 0.997924805%; Any of 10 Ancients / Full Pool / per Equipment Roll = 7.065365190%; Neutral Class-A Enemy / Any-Ancient Reference = 1.165712%

- Character = Gollum; Profile = C7; Highest-Weight Categories (Table Values) = Short blade/small weapon 15%; armor 15%; axe 10%; blunt weapon/mace 10%; Item A / Machine-Exact = 8.001708984%; type E / Machine-Exact = 0.997924805%; Any of 10 Ancients / Full Pool / per Equipment Roll = 7.065365190%; Neutral Class-A Enemy / Any-Ancient Reference = 1.165712%

"Neutral Class-A enemy reference" = 16.498962417% (chance per kill of reaching the full equipment generator) × full-pool total Ancient rate. This is used only to convert "per equipment generation" into an intuitive per-kill figure; an actual enemy source may be far above or below this value.

## 4. Raw Table Values for the 15 Equipment Categories (DAT_0806DBAC)

- Character/Profile = Frodo / C0; 0 = 15%; 1 = 3%; 2 = 3%; 3 = 10%; 4 = 10%; 5 = 3%; 6 = 3%; 7 = 3%; 8 = 15%; 9 = 10%; A = 8%; B = 7%; C = 6%; D = 3%; E = 1%

- Character/Profile = Legolas / C1; 0 = 10%; 1 = 3%; 2 = 3%; 3 = 3%; 4 = 3%; 5 = 3%; 6 = 15%; 7 = 10%; 8 = 17%; 9 = 3%; A = 9%; B = 10%; C = 3%; D = 7%; E = 1%

- Character/Profile = Aragorn / C2; 0 = 5%; 1 = 11%; 2 = 5%; 3 = 5%; 4 = 5%; 5 = 3%; 6 = 3%; 7 = 3%; 8 = 15%; 9 = 15%; A = 8%; B = 7%; C = 7%; D = 7%; E = 1%

- Character/Profile = Gandalf / C3; 0 = 10%; 1 = 10%; 2 = 3%; 3 = 3%; 4 = 3%; 5 = 10%; 6 = 3%; 7 = 3%; 8 = 15%; 9 = 3%; A = 8%; B = 9%; C = 10%; D = 9%; E = 1%

- Character/Profile = Éowyn / C4; 0 = 5%; 1 = 11%; 2 = 5%; 3 = 5%; 4 = 5%; 5 = 3%; 6 = 3%; 7 = 3%; 8 = 15%; 9 = 15%; A = 8%; B = 7%; C = 7%; D = 7%; E = 1%

- Character/Profile = Gimli / C5; 0 = 4%; 1 = 3%; 2 = 3%; 3 = 15%; 4 = 10%; 5 = 3%; 6 = 3%; 7 = 3%; 8 = 15%; 9 = 11%; A = 8%; B = 7%; C = 7%; D = 7%; E = 1%

- Character/Profile = Sam / C6; 0 = 15%; 1 = 3%; 2 = 3%; 3 = 10%; 4 = 10%; 5 = 3%; 6 = 3%; 7 = 3%; 8 = 15%; 9 = 10%; A = 8%; B = 7%; C = 6%; D = 3%; E = 1%

- Character/Profile = Gollum / C7; 0 = 15%; 1 = 3%; 2 = 3%; 3 = 10%; 4 = 10%; 5 = 3%; 6 = 3%; 7 = 3%; 8 = 15%; 9 = 10%; A = 8%; B = 7%; C = 6%; D = 3%; E = 1%

Type mapping: 0 short blade/small weapon; 1 one-handed sword; 2 two-handed sword; 3 axe; 4 blunt weapon/mace; 5 staff; 6 bow; 7 arrows; 8 armor; 9 shield; A item/accessory; B cloak; C handwear; D footwear; E special/Unique pool. Each row sums to 100% in the table.

## 5. Actual Machine-Code Bucket Probabilities: Why Does "15%" Become 15.002441%?

FUN_0803AE8C uses a random value from 1..100, but the underlying RNG implementation is based on 32,768 discrete buckets. Therefore, the true probability for cumulative threshold n is ceil(n×32768/100)/32768. The difference is tiny, but for million-roll-scale targets the "table value" and the "machine-code bucket value" should be distinguished.

- Character/Profile = Frodo / C0; 0 = 15.0024; 1 = 2.9999; 2 = 2.9999; 3 = 10.0006; 4 = 9.9976; 5 = 2.9999; 6 = 2.9999; 7 = 2.9999; 8 = 15.0024; 9 = 9.9976; A = 8.0017; B = 7.0007; C = 5.9998; D = 2.9999; E = 0.9979

- Character/Profile = Legolas / C1; 0 = 10.0006; 1 = 2.9999; 2 = 2.9999; 3 = 2.9999; 4 = 2.9999; 5 = 2.9999; 6 = 15.0024; 7 = 9.9976; 8 = 17.0013; 9 = 2.9999; A = 8.9996; B = 10.0006; C = 2.9999; D = 7.0007; E = 0.9979

- Character/Profile = Aragorn / C2; 0 = 5.0018; 1 = 10.9985; 2 = 5.0018; 3 = 4.9988; 4 = 5.0018; 5 = 2.9999; 6 = 2.9999; 7 = 2.9999; 8 = 14.9994; 9 = 14.9994; A = 8.0017; B = 6.9977; C = 7.0007; D = 7.0007; E = 0.9979

- Character/Profile = Gandalf / C3; 0 = 10.0006; 1 = 10.0006; 2 = 2.9999; 3 = 2.9999; 4 = 2.9999; 5 = 10.0006; 6 = 2.9999; 7 = 2.9999; 8 = 14.9994; 9 = 2.9999; A = 8.0017; B = 8.9996; C = 10.0006; D = 8.9996; E = 0.9979

- Character/Profile = Éowyn / C4; 0 = 5.0018; 1 = 10.9985; 2 = 5.0018; 3 = 4.9988; 4 = 5.0018; 5 = 2.9999; 6 = 2.9999; 7 = 2.9999; 8 = 14.9994; 9 = 14.9994; A = 8.0017; B = 6.9977; C = 7.0007; D = 7.0007; E = 0.9979

- Character/Profile = Gimli / C5; 0 = 4.0009; 1 = 2.9999; 2 = 2.9999; 3 = 14.9994; 4 = 10.0006; 5 = 2.9999; 6 = 2.9999; 7 = 2.9999; 8 = 15.0024; 9 = 10.9985; A = 8.0017; B = 6.9977; C = 7.0007; D = 7.0007; E = 0.9979

- Character/Profile = Sam / C6; 0 = 15.0024; 1 = 2.9999; 2 = 2.9999; 3 = 10.0006; 4 = 9.9976; 5 = 2.9999; 6 = 2.9999; 7 = 2.9999; 8 = 15.0024; 9 = 9.9976; A = 8.0017; B = 7.0007; C = 5.9998; D = 2.9999; E = 0.9979

- Character/Profile = Gollum / C7; 0 = 15.0024; 1 = 2.9999; 2 = 2.9999; 3 = 10.0006; 4 = 9.9976; 5 = 2.9999; 6 = 2.9999; 7 = 2.9999; 8 = 15.0024; 9 = 9.9976; A = 8.0017; B = 7.0007; C = 5.9998; D = 2.9999; E = 0.9979

All values in the table are percentages. For example, type E is 0.997924805% for every character; C1 (Legolas) has 8.999633789% for item/accessory type A, while the others have 8.001708984%.

## 6. Frodo — C0

Inherent Profile: C0. The highest weights are short blade/small weapon 15%, armor 15%, axe 10%, blunt weapon/mace 10%, and shield 10%. These percentages describe where the equipment category lands after the equipment generator has already been called; they are not direct per-kill equipment-drop percentages.

- Key Category = type 0 Short Blade/Small Weapon; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 15.002441406%

- Key Category = type 6 Bow; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type 8 Armor; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 15.002441406%

- Key Category = type 9 Shield; ROM Table Value = 10%; Actual Machine-Code Bucket Probability = 9.997558594%

- Key Category = type A Item/Accessory; ROM Table Value = 8%; Actual Machine-Code Bucket Probability = 8.001708984%

- Key Category = type C Handwear; ROM Table Value = 6%; Actual Machine-Code Bucket Probability = 5.999755859%

- Key Category = type D Footwear; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type E Special/Unique; ROM Table Value = 1%; Actual Machine-Code Bucket Probability = 0.997924805%

- This Profile has type 0=15% and armor=15%, while axe, blunt weapon, and shield are each around 10%, giving it a very broad distribution across "short-blade/Spoon-type items + multiple forms of melee defense."

- Combined rate for any of the 10 Ancients (eligibility enabled, full candidate pool) = 7.065365190% per equipment-generation event, tied for the highest among the eight characters.

- The 128-byte Profile rows for Frodo, Sam, and Gollum (C0/C6/C7) are identical in probability structure. Therefore, if skill, equipment, and route differences are ignored, their category probabilities and full-pool total Ancient rates are exactly the same.

## 7. Legolas — C1

Inherent Profile: C1. The highest weights are armor 17%, bow 15%, short blade/small weapon 10%, arrows 10%, and cloak 10%. These percentages describe where the equipment category lands after the equipment generator has already been called; they are not direct per-kill equipment-drop percentages.

- Key Category = type 0 Short Blade/Small Weapon; ROM Table Value = 10%; Actual Machine-Code Bucket Probability = 10.000610352%

- Key Category = type 6 Bow; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 15.002441406%

- Key Category = type 8 Armor; ROM Table Value = 17%; Actual Machine-Code Bucket Probability = 17.001342773%

- Key Category = type 9 Shield; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type A Item/Accessory; ROM Table Value = 9%; Actual Machine-Code Bucket Probability = 8.999633789%

- Key Category = type C Handwear; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type D Footwear; ROM Table Value = 7%; Actual Machine-Code Bucket Probability = 7.000732422%

- Key Category = type E Special/Unique; ROM Table Value = 1%; Actual Machine-Code Bucket Probability = 0.997924805%

- Its clearest signature is bow 15% + arrows 10% + armor 17%, making it the most bow- and armor-weighted of the eight Profiles. It is also the only Profile with item/accessory type A at about 9%.

- Combined rate for any of the 10 Ancients = 5.638189588%; Horn Bow alone = 1.153846% per equipment-generation event, the highest among the eight characters.

- Therefore, Legolas is not "more likely to get every rare item overall"; rather, his equipment rolls are more likely to be allocated to categories such as bows, arrows, and armor.

## 8. Aragorn — C2

Inherent Profile: C2. The highest weights are armor 15%, shield 15%, one-handed sword 11%, item/accessory 8%, and cloak 7%. These percentages describe where the equipment category lands after the equipment generator has already been called; they are not direct per-kill equipment-drop percentages.

- Key Category = type 0 Short Blade/Small Weapon; ROM Table Value = 5%; Actual Machine-Code Bucket Probability = 5.001831055%

- Key Category = type 6 Bow; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type 8 Armor; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 14.999389648%

- Key Category = type 9 Shield; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 14.999389648%

- Key Category = type A Item/Accessory; ROM Table Value = 8%; Actual Machine-Code Bucket Probability = 8.001708984%

- Key Category = type C Handwear; ROM Table Value = 7%; Actual Machine-Code Bucket Probability = 7.000732422%

- Key Category = type D Footwear; ROM Table Value = 7%; Actual Machine-Code Bucket Probability = 7.000732422%

- Key Category = type E Special/Unique; ROM Table Value = 1%; Actual Machine-Code Bucket Probability = 0.997924805%

- With one-handed sword 11%, armor 15%, shield 15%, and both handwear and footwear at 7%, this is a relatively balanced sword-and-shield/defense Profile. The entire C2 row is identical to C4.

- Combined rate for any of the 10 Ancients = 6.574203574%; Splinted Shield = 1.875000% and Prestige Sword = 1.000000% per equipment-generation event.

- Aragorn and Éowyn have the same "inherent drop structure." Any practical difference in farming results should first be explained by accessible maps, skills, equipment ET, combat efficiency, and related factors—not by this Profile table.

## 9. Gandalf — C3

Inherent Profile: C3. The highest weights are armor 15%, short blade/small weapon 10%, one-handed sword 10%, staff 10%, and handwear 10%. These percentages describe where the equipment category lands after the equipment generator has already been called; they are not direct per-kill equipment-drop percentages.

- Key Category = type 0 Short Blade/Small Weapon; ROM Table Value = 10%; Actual Machine-Code Bucket Probability = 10.000610352%

- Key Category = type 6 Bow; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type 8 Armor; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 14.999389648%

- Key Category = type 9 Shield; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type A Item/Accessory; ROM Table Value = 8%; Actual Machine-Code Bucket Probability = 8.001708984%

- Key Category = type C Handwear; ROM Table Value = 10%; Actual Machine-Code Bucket Probability = 10.000610352%

- Key Category = type D Footwear; ROM Table Value = 9%; Actual Machine-Code Bucket Probability = 8.999633789%

- Key Category = type E Special/Unique; ROM Table Value = 1%; Actual Machine-Code Bucket Probability = 0.997924805%

- type 0=10%, one-handed sword=10%, staff=10%, armor=15%, handwear=10%, with cloak and footwear at 9% each. This is the Profile with the highest staff weight among the eight.

- Combined rate for any of the 10 Ancients = 5.157031857% (the lowest of the eight characters). This does not mean "Gandalf has bad loot"; rather, the category distribution of the current 10 Ancients does not fully overlap with his high-weight categories. For example, the staff category itself is absent from this 10-Ancient list.

- Tabard = 1.000000% per equipment-generation event, one of this Profile's relative strengths.

## 10. Éowyn — C4

Inherent Profile: C4. The highest weights are armor 15%, shield 15%, one-handed sword 11%, item/accessory 8%, and cloak 7%. These percentages describe where the equipment category lands after the equipment generator has already been called; they are not direct per-kill equipment-drop percentages.

- Key Category = type 0 Short Blade/Small Weapon; ROM Table Value = 5%; Actual Machine-Code Bucket Probability = 5.001831055%

- Key Category = type 6 Bow; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type 8 Armor; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 14.999389648%

- Key Category = type 9 Shield; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 14.999389648%

- Key Category = type A Item/Accessory; ROM Table Value = 8%; Actual Machine-Code Bucket Probability = 8.001708984%

- Key Category = type C Handwear; ROM Table Value = 7%; Actual Machine-Code Bucket Probability = 7.000732422%

- Key Category = type D Footwear; ROM Table Value = 7%; Actual Machine-Code Bucket Probability = 7.000732422%

- Key Category = type E Special/Unique; ROM Table Value = 1%; Actual Machine-Code Bucket Probability = 0.997924805%

- With one-handed sword 11%, armor 15%, shield 15%, and both handwear and footwear at 7%, this is a relatively balanced sword-and-shield/defense Profile. The entire C2 row is identical to C4.

- Combined rate for any of the 10 Ancients = 6.574203574%; Splinted Shield = 1.875000% and Prestige Sword = 1.000000% per equipment-generation event.

- Aragorn and Éowyn have the same "inherent drop structure." Any practical difference in farming results should first be explained by accessible maps, skills, equipment ET, combat efficiency, and related factors—not by this Profile table.

## 11. Gimli — C5

Inherent Profile: C5. The highest weights are axe 15%, armor 15%, shield 11%, blunt weapon/mace 10%, and item/accessory 8%. These percentages describe where the equipment category lands after the equipment generator has already been called; they are not direct per-kill equipment-drop percentages.

- Key Category = type 0 Short Blade/Small Weapon; ROM Table Value = 4%; Actual Machine-Code Bucket Probability = 4.000854492%

- Key Category = type 6 Bow; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type 8 Armor; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 15.002441406%

- Key Category = type 9 Shield; ROM Table Value = 11%; Actual Machine-Code Bucket Probability = 10.998535156%

- Key Category = type A Item/Accessory; ROM Table Value = 8%; Actual Machine-Code Bucket Probability = 8.001708984%

- Key Category = type C Handwear; ROM Table Value = 7%; Actual Machine-Code Bucket Probability = 7.000732422%

- Key Category = type D Footwear; ROM Table Value = 7%; Actual Machine-Code Bucket Probability = 7.000732422%

- Key Category = type E Special/Unique; ROM Table Value = 1%; Actual Machine-Code Bucket Probability = 0.997924805%

- Axe 15%, armor 15%, shield 11%, and blunt weapon 10% clearly bias the Profile toward the axes and heavier defensive equipment commonly associated with Gimli.

- Combined rate for any of the 10 Ancients = 6.422688423%; Bardiche = 1.363636%, Splinted Shield = 1.375000%, and Long Mace = 1.000000% per equipment-generation event.

- In actual play, Gimli can also use quality modifiers such as Keen Eyes / Dwarf Sense to improve the completion rate of high-end affixes, but that belongs to a later quality layer and must not be back-calculated into the 15 base category weights here.

## 12. Sam — C6

Inherent Profile: C6. The highest weights are short blade/small weapon 15%, armor 15%, axe 10%, blunt weapon/mace 10%, and shield 10%. These percentages describe where the equipment category lands after the equipment generator has already been called; they are not direct per-kill equipment-drop percentages.

- Key Category = type 0 Short Blade/Small Weapon; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 15.002441406%

- Key Category = type 6 Bow; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type 8 Armor; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 15.002441406%

- Key Category = type 9 Shield; ROM Table Value = 10%; Actual Machine-Code Bucket Probability = 9.997558594%

- Key Category = type A Item/Accessory; ROM Table Value = 8%; Actual Machine-Code Bucket Probability = 8.001708984%

- Key Category = type C Handwear; ROM Table Value = 6%; Actual Machine-Code Bucket Probability = 5.999755859%

- Key Category = type D Footwear; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type E Special/Unique; ROM Table Value = 1%; Actual Machine-Code Bucket Probability = 0.997924805%

- This Profile has type 0=15% and armor=15%, while axe, blunt weapon, and shield are each around 10%, giving it a very broad distribution across "short-blade/Spoon-type items + multiple forms of melee defense."

- Combined rate for any of the 10 Ancients (eligibility enabled, full candidate pool) = 7.065365190% per equipment-generation event, tied for the highest among the eight characters.

- The 128-byte Profile rows for Frodo, Sam, and Gollum (C0/C6/C7) are identical in probability structure. Therefore, if skill, equipment, and route differences are ignored, their category probabilities and full-pool total Ancient rates are exactly the same.

## 13. Gollum — C7

Inherent Profile: C7. The highest weights are short blade/small weapon 15%, armor 15%, axe 10%, blunt weapon/mace 10%, and shield 10%. These percentages describe where the equipment category lands after the equipment generator has already been called; they are not direct per-kill equipment-drop percentages.

- Key Category = type 0 Short Blade/Small Weapon; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 15.002441406%

- Key Category = type 6 Bow; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type 8 Armor; ROM Table Value = 15%; Actual Machine-Code Bucket Probability = 15.002441406%

- Key Category = type 9 Shield; ROM Table Value = 10%; Actual Machine-Code Bucket Probability = 9.997558594%

- Key Category = type A Item/Accessory; ROM Table Value = 8%; Actual Machine-Code Bucket Probability = 8.001708984%

- Key Category = type C Handwear; ROM Table Value = 6%; Actual Machine-Code Bucket Probability = 5.999755859%

- Key Category = type D Footwear; ROM Table Value = 3%; Actual Machine-Code Bucket Probability = 2.999877930%

- Key Category = type E Special/Unique; ROM Table Value = 1%; Actual Machine-Code Bucket Probability = 0.997924805%

- This Profile has type 0=15% and armor=15%, while axe, blunt weapon, and shield are each around 10%, giving it a very broad distribution across "short-blade/Spoon-type items + multiple forms of melee defense."

- Combined rate for any of the 10 Ancients (eligibility enabled, full candidate pool) = 7.065365190% per equipment-generation event, tied for the highest among the eight characters.

- The 128-byte Profile rows for Frodo, Sam, and Gollum (C0/C6/C7) are identical in probability structure. Therefore, if skill, equipment, and route differences are ignored, their category probabilities and full-pool total Ancient rates are exactly the same.

## 14. The 10 Ancients by Character (Full Candidate Pool, Eligibility Enabled)

Ancients do not use type E. A normal candidate record enters the Ancient eligibility check only when flags 0x40 is set; if the global Ancient eligibility bit 0x8000 is not enabled, the candidate is downgraded. Therefore, the table below answers only the base probability for each character to hit a given Ancient per full equipment-generation event when eligibility is enabled and that type's candidate pool is fully expanded.

- Code = 0B0; Translated Name = 5-Finger Knife; English Name = 5-Finger Knife; type/N = 0/18; Frodo = 0.833333%; Legolas = 0.555556%; Aragorn = 0.277778%; Gandalf = 0.555556%; Éowyn = 0.277778%; Gimli = 0.222222%; Sam = 0.833333%; Gollum = 0.833333%

- Code = 0D0; Translated Name = Cultellus; English Name = Cultellus; type/N = 0/18; Frodo = 0.833333%; Legolas = 0.555556%; Aragorn = 0.277778%; Gandalf = 0.555556%; Éowyn = 0.277778%; Gimli = 0.222222%; Sam = 0.833333%; Gollum = 0.833333%

- Code = 039; Translated Name = Splinted Shield; English Name = Splinted Shield; type/N = 9/8; Frodo = 1.250000%; Legolas = 0.375000%; Aragorn = 1.875000%; Gandalf = 0.375000%; Éowyn = 1.875000%; Gimli = 1.375000%; Sam = 1.250000%; Gollum = 1.250000%

- Code = 082; Translated Name = Imperial Sword (Two-Handed); English Name = Imperial Sword; type/N = 2/9; Frodo = 0.333333%; Legolas = 0.333333%; Aragorn = 0.555556%; Gandalf = 0.333333%; Éowyn = 0.555556%; Gimli = 0.333333%; Sam = 0.333333%; Gollum = 0.333333%

- Code = 083; Translated Name = Bardiche (Two-Handed); English Name = Bardiche; type/N = 3/11; Frodo = 0.909091%; Legolas = 0.272727%; Aragorn = 0.454545%; Gandalf = 0.272727%; Éowyn = 0.454545%; Gimli = 1.363636%; Sam = 0.909091%; Gollum = 0.909091%

- Code = 034; Translated Name = Long Mace; English Name = Long Mace; type/N = 4/10; Frodo = 1.000000%; Legolas = 0.300000%; Aragorn = 0.500000%; Gandalf = 0.300000%; Éowyn = 0.500000%; Gimli = 1.000000%; Sam = 1.000000%; Gollum = 1.000000%

- Code = 178; Translated Name = Footman Plate; English Name = Footman Plate; type/N = 8/24; Frodo = 0.625000%; Legolas = 0.708333%; Aragorn = 0.625000%; Gandalf = 0.625000%; Éowyn = 0.625000%; Gimli = 0.625000%; Sam = 0.625000%; Gollum = 0.625000%

- Code = 081; Translated Name = Prestige Sword; English Name = Prestige Sword; type/N = 1/11; Frodo = 0.272727%; Legolas = 0.272727%; Aragorn = 1.000000%; Gandalf = 0.909091%; Éowyn = 1.000000%; Gimli = 0.272727%; Sam = 0.272727%; Gollum = 0.272727%

- Code = 07B; Translated Name = Tabard; English Name = Tabard; type/N = B/9; Frodo = 0.777778%; Legolas = 1.111111%; Aragorn = 0.777778%; Gandalf = 1.000000%; Éowyn = 0.777778%; Gimli = 0.777778%; Sam = 0.777778%; Gollum = 0.777778%

- Code = 086; Translated Name = Horn Bow; English Name = Horn Bow; type/N = 6/13; Frodo = 0.230769%; Legolas = 1.153846%; Aragorn = 0.230769%; Gandalf = 0.230769%; Éowyn = 0.230769%; Gimli = 0.230769%; Sam = 0.230769%; Gollum = 0.230769%

- Character = Frodo; Profile = C0; Combined Rate for Any of 10 Ancients = 7.065365190%; Average Equipment-Generation Wait = about 1 / 14.15 equipment-generation events; Neutral Class-A Enemy Per-Kill Reference = 1.165712%

- Character = Legolas; Profile = C1; Combined Rate for Any of 10 Ancients = 5.638189588%; Average Equipment-Generation Wait = about 1 / 17.74 equipment-generation events; Neutral Class-A Enemy Per-Kill Reference = 0.930243%

- Character = Aragorn; Profile = C2; Combined Rate for Any of 10 Ancients = 6.574203574%; Average Equipment-Generation Wait = about 1 / 15.21 equipment-generation events; Neutral Class-A Enemy Per-Kill Reference = 1.084675%

- Character = Gandalf; Profile = C3; Combined Rate for Any of 10 Ancients = 5.157031857%; Average Equipment-Generation Wait = about 1 / 19.39 equipment-generation events; Neutral Class-A Enemy Per-Kill Reference = 0.850857%

- Character = Éowyn; Profile = C4; Combined Rate for Any of 10 Ancients = 6.574203574%; Average Equipment-Generation Wait = about 1 / 15.21 equipment-generation events; Neutral Class-A Enemy Per-Kill Reference = 1.084675%

- Character = Gimli; Profile = C5; Combined Rate for Any of 10 Ancients = 6.422688423%; Average Equipment-Generation Wait = about 1 / 15.57 equipment-generation events; Neutral Class-A Enemy Per-Kill Reference = 1.059677%

- Character = Sam; Profile = C6; Combined Rate for Any of 10 Ancients = 7.065365190%; Average Equipment-Generation Wait = about 1 / 14.15 equipment-generation events; Neutral Class-A Enemy Per-Kill Reference = 1.165712%

- Character = Gollum; Profile = C7; Combined Rate for Any of 10 Ancients = 7.065365190%; Average Equipment-Generation Wait = about 1 / 14.15 equipment-generation events; Neutral Class-A Enemy Per-Kill Reference = 1.165712%

Ranking (total Ancient rate): Frodo = Sam = Gollum > Aragorn = Éowyn > Gimli > Legolas > Gandalf. Note that this ranks only the combined total of the 10 Ancients; it does not represent overall superiority for all high-end red-name or blue-name equipment.

## 15. Type E Rare/Special Pool: Identical Inherent Weight for All Eight Characters

The final cumulative interval in DAT_0806DBAC is 99→100 for every Profile C0-C7, so the table value is 1% for all of them. The actual machine-code bucket value is 327/32768 = 0.997924805%. Thus, in terms of the inherent chance to enter type E, there is no difference among the eight characters.

- Final type E Outcomes (Full-Eligibility Reference) = 37 Ordinary Blue-Name Rare Items; Conditional on One type E Event = 6.037735849% / type E event; Conversion Note = 0.060377358490 × type E weight; Mechanism Notes = The first 10 are affected by +0x188 state; the latter 27 are not limited by this bitmap

- Final type E Outcomes (Full-Eligibility Reference) = 14 Rare Red-Name Backpack Items; Conditional on One type E Event = 2.641509434% / type E event; Conversion Note = 0.026415094340 × type E weight; Mechanism Notes = The original mechanism requires the corresponding RedMask bit to be valid; Orc Head/Drum are not included in these 14 items

- Final type E Outcomes (Full-Eligibility Reference) = Orc Head; Conditional on One type E Event = 53.845283019% / type E event; Conversion Note = 0.538452830190 × type E weight; Mechanism Notes = Includes direct candidate + unified 59% fallback branch

- Final type E Outcomes (Full-Eligibility Reference) = Orc Drum; Conditional on One type E Event = 9.283018868% / type E event; Conversion Note = 0.092830188680 × type E weight; Mechanism Notes = Includes direct candidate + unified 10% fallback branch

- Final type E Outcomes (Full-Eligibility Reference) = Normal/Replacement Generation; Conditional on One type E Event = 28.192452830% / type E event; Conversion Note = 0.281924528300 × type E weight; Mechanism Notes = fallback 70..100 enters normal/replacement generation

Original eligibility boundary: direct success for the first 10 ordinary blue-name rares is also affected by the +0x188 bitmap; the 14 red-name backpack items are controlled by 14 valid RedMask bits; Orc Head / Orc Drum receive special handling at their candidate positions before RedMask and also have unified fallback exits. Therefore, "same Profile" does not mean "identical final special-item probabilities under every save-state condition."

## 16. If We Convert to "Per Ordinary Neutral Enemy Killed," Where Do Characters Differ?

For an intuitive reference, this section uses the previously machine-code-verified neutral Class-A benchmark: chance per kill of reaching the full equipment generator = 16.498962417%. This is not a universal drop rate for all enemies; it is only a conversion yardstick.

- Character = Frodo; Profile = C0; Enemy → Equipment Generation = 16.498962417%; type E / Equipment Generation = 0.997924805%; type E Event / Kill = 0.164647238%; Any Ancient / Kill Reference = 1.165712%

- Character = Legolas; Profile = C1; Enemy → Equipment Generation = 16.498962417%; type E / Equipment Generation = 0.997924805%; type E Event / Kill = 0.164647238%; Any Ancient / Kill Reference = 0.930243%

- Character = Aragorn; Profile = C2; Enemy → Equipment Generation = 16.498962417%; type E / Equipment Generation = 0.997924805%; type E Event / Kill = 0.164647238%; Any Ancient / Kill Reference = 1.084675%

- Character = Gandalf; Profile = C3; Enemy → Equipment Generation = 16.498962417%; type E / Equipment Generation = 0.997924805%; type E Event / Kill = 0.164647238%; Any Ancient / Kill Reference = 0.850857%

- Character = Éowyn; Profile = C4; Enemy → Equipment Generation = 16.498962417%; type E / Equipment Generation = 0.997924805%; type E Event / Kill = 0.164647238%; Any Ancient / Kill Reference = 1.084675%

- Character = Gimli; Profile = C5; Enemy → Equipment Generation = 16.498962417%; type E / Equipment Generation = 0.997924805%; type E Event / Kill = 0.164647238%; Any Ancient / Kill Reference = 1.059677%

- Character = Sam; Profile = C6; Enemy → Equipment Generation = 16.498962417%; type E / Equipment Generation = 0.997924805%; type E Event / Kill = 0.164647238%; Any Ancient / Kill Reference = 1.165712%

- Character = Gollum; Profile = C7; Enemy → Equipment Generation = 16.498962417%; type E / Equipment Generation = 0.997924805%; type E Event / Kill = 0.164647238%; Any Ancient / Kill Reference = 1.165712%

As shown, for the same neutral enemy the type E event rate remains identical across all eight characters. The per-kill Ancient reference differs because Ancients are distributed among ordinary types 0/1/2/3/4/6/8/9/B, and the characters have different weights for those categories.

## 17. Extra Treasure / Keen Eyes / Dwarf Sense: Do Not Conflate Them with the "Inherent Profile"

- Variable = Inherent Character Profile; Code Layer = DAT_03003D9F → DAT_0806DBAC; Actual Effect = Determines category allocation across type 0 through E; Relation to This Report = Inherent to the character; the main subject of this report

- Variable = Extra Treasure; Code Layer = DAT_03003CE4 and related quality-parameter chain; Actual Effect = Participates in percentage multiplication for Prefix/Suffix success thresholds; Relation to This Report = Can be heavily stacked through equipment; it does not mean "one extra item drops"

- Variable = Keen Eyes; Code Layer = Passive skill; Actual Effect = About +5% better items per level; +25% total at 5/5; Relation to This Report = Quality modifier; does not change type E=1%

- Variable = Dwarf Sense; Code Layer = Passive skill (commonly discussed for Gimli in practice); Actual Effect = Under this project's established calculation convention, about +10% per level; +50% at max level; Relation to This Report = Quality modifier; does not alter the Profile row

- Variable = enemy/source; Code Layer = DAT_08057F78 and related source tables + death-drop chain; Actual Effect = Determines whether this kill has a chance to enter the equipment generator; Relation to This Report = Differences between enemies can be far larger than differences between character Profiles

Therefore, the correct complete probability for farming a specific piece of equipment should be written as: P(enemy/source reaches equipment generator) × P(type | character Profile) × P(candidate | current level pool) × P(Prefix/Suffix/eligibility gate). Calling any one layer by itself the "final drop rate" loses essential conditions.

## 18. Compatibility Verification for the Current Nazgûl-Enhanced Build

This analysis directly searched the binary for the 8×16 = 128-byte Profile cumulative-threshold table from the English BLRE original. In the English original, the table is at file offset 0x006DBAC; an identical 128-byte sequence was found in the current "Chinese Ultimate Collection / Nazgûl-Enhanced" build at file offset 0x006DB58.

- ROM = English BLRE v1.0; Profile-Table File Offset = 0x006DBAC; SHA-256 = b5f556593c5fbe3531a4354b047f0bed364a124ceee43f278a48f4d26b0ffa9c; Conclusion = Baseline Profile table

- ROM = Current BLRJ Chinese Ultimate Collection / Nazgûl-Enhanced Build; Profile-Table File Offset = 0x0006DB58; SHA-256 = 4b0d27569245337a63480b5687430fb2368f85ff289aa09b800df6cf4db69edd; Conclusion = The 128-byte Profile table is byte-for-byte identical to the English baseline

Therefore, the Nazgûl HP/Attack/Defense modifications and the "Backstab + Armor Piercing" damage patch did not alter the eight characters' inherent drop Profiles. The C0-C7 data in this report can be applied directly to the current Nazgûl-enhanced build.

## 19. Technical Appendix: Raw Cumulative Thresholds for the Eight Profiles

FUN_0803AE8C does not store 15 independent percentages directly; it stores 15 cumulative upper bounds. The selected type is determined by the interval between two cumulative thresholds in which the random value falls. Each row ends at 100, followed by a zero byte used as a terminator/placeholder.

- Profile = C0; ROM 16 Bytes (hex) = 0F 12 15 1F 29 2C 2F 32 41 4B 53 5A 60 63 64 00; Cumulative Thresholds (decimal) = 15 / 18 / 21 / 31 / 41 / 44 / 47 / 50 / 65 / 75 / 83 / 90 / 96 / 99 / 100

- Profile = C1; ROM 16 Bytes (hex) = 0A 0D 10 13 16 19 28 32 43 46 4F 59 5C 63 64 00; Cumulative Thresholds (decimal) = 10 / 13 / 16 / 19 / 22 / 25 / 40 / 50 / 67 / 70 / 79 / 89 / 92 / 99 / 100

- Profile = C2; ROM 16 Bytes (hex) = 05 10 15 1A 1F 22 25 28 37 46 4E 55 5C 63 64 00; Cumulative Thresholds (decimal) = 5 / 16 / 21 / 26 / 31 / 34 / 37 / 40 / 55 / 70 / 78 / 85 / 92 / 99 / 100

- Profile = C3; ROM 16 Bytes (hex) = 0A 14 17 1A 1D 27 2A 2D 3C 3F 47 50 5A 63 64 00; Cumulative Thresholds (decimal) = 10 / 20 / 23 / 26 / 29 / 39 / 42 / 45 / 60 / 63 / 71 / 80 / 90 / 99 / 100

- Profile = C4; ROM 16 Bytes (hex) = 05 10 15 1A 1F 22 25 28 37 46 4E 55 5C 63 64 00; Cumulative Thresholds (decimal) = 5 / 16 / 21 / 26 / 31 / 34 / 37 / 40 / 55 / 70 / 78 / 85 / 92 / 99 / 100

- Profile = C5; ROM 16 Bytes (hex) = 04 07 0A 19 23 26 29 2C 3B 46 4E 55 5C 63 64 00; Cumulative Thresholds (decimal) = 4 / 7 / 10 / 25 / 35 / 38 / 41 / 44 / 59 / 70 / 78 / 85 / 92 / 99 / 100

- Profile = C6; ROM 16 Bytes (hex) = 0F 12 15 1F 29 2C 2F 32 41 4B 53 5A 60 63 64 00; Cumulative Thresholds (decimal) = 15 / 18 / 21 / 31 / 41 / 44 / 47 / 50 / 65 / 75 / 83 / 90 / 96 / 99 / 100

- Profile = C7; ROM 16 Bytes (hex) = 0F 12 15 1F 29 2C 2F 32 41 4B 53 5A 60 63 64 00; Cumulative Thresholds (decimal) = 15 / 18 / 21 / 31 / 41 / 44 / 47 / 50 / 65 / 75 / 83 / 90 / 96 / 99 / 100

## 20. Evidence Anchors and Reproducible Verification Path

- Decompilation: FUN_0803AE8C reads character value DAT_03003D9F, indexes DAT_0806DBAC with profile×0x10, and uses rand(1,100) to compare against cumulative thresholds step by step.

- Save deserialization: character Profile = save_record[0x65] >> 4; during serialization, DAT_03003D9F is written back into the high 4 bits of +0x65.

- Named SAV files: clearly named Frodo/Legolas/Aragorn/Gandalf/Éowyn/Gimli/Sam/Gollum saves were used to complete the C0-C7 mapping. Gimli additionally has a clean single-character sample in Slot 1 with the other three slots empty.

- Item categories: entries in the CodeBreaker Item Table—000 Spoon, 001 Longsword, 002 T-sword, 003 Hatchet, 004 Club, 005 Short staff, 006 Shortbow, 007 Arrows, 008 Armor, 009 Shield, 00A Item, 00B Cloak, 00C Handwear, 00D Footwear, 00E Unique, etc.—can be used to confirm the category semantics of type 0 through E.

- Ancient: normal candidate flags & 0x40 trigger the Ancient eligibility check; when eligibility bit 0x8000 is not enabled, the candidate is downgraded. Every Ancient probability in this report is explicitly under "eligibility enabled + candidate pool fully expanded."

- Type E: blue-name items, red-name items, Orc Head, and Orc Drum within the 53-slot special candidate pool still have their own random gates, Mask/RedMask checks, or fallback paths. Therefore, type E=1% only means entry into the special pool; it does not mean any particular rare item has a 1% rate.

## 21. Final One-Sentence Summary

If the question is, "Given that the same high-value source has already granted one full equipment roll, which type is each of the eight characters inherently most likely to allocate that roll to?"—use the C0-C7 Profiles. If the question is, "Who is most likely to obtain any one of the 10 Ancients?"—with full-pool eligibility enabled, Frodo/Sam/Gollum are highest. If the question is, "Who is inherently more likely to enter type E, which contains the 14 red-name items and 37 blue-name rares?"—all eight characters are identical: the table value is 1% (machine bucket value 0.997924805%).
