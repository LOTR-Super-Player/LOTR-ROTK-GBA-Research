# ROTK GBA Rare Drop Rate Research

## Ancient · Orc Head / Orc Drum · 14 Rare Backpack Uniques · 37 Blue-name Rares

## Research target and static ROM verification

- Research target: 2026-08-28 final Ultimate ROM.

- ROM size: `17,825,792` bytes.

- SHA-256: `fac4b2ec6d0555cd41112d3dc3615e237dc965b5811b12ce4c3e489e5c02adc2`.

- Header game code: `BLRJ`.

- Ancient-related project patch byte: `ROM+0x03A904 = 06 E0`.

- 14-target backpack Unique RedMask rejection-branch bypass: `ROM+0x03A9EE = C0 46`.

- Eight-character loot Profile table remains at `ROM+0x006DB58`; its 128 bytes match the established research baseline byte-for-byte.

> **Important — Probability denominators**

>

> “Drop rate” is incomplete unless the parent sample is stated. This game has at least four distinct denominators: per kill, per death/re-death reward cycle, per entry into the full equipment generator, and per entry into the type-E special pool. Mixing those denominators can exaggerate a probability by tens or hundreds of times.

## 1. Executive result: which rare group is actually more common?

Using the most comparable denominator — one opportunity that has already entered the full equipment generator — the current model gives:

- **10 Ancient items combined:** approximately **5.1570% to 7.0654%** per full equipment-generation event, depending on character Profile. Highest: Frodo, Sam, Gollum. Lowest: Gandalf.

- **Orc Head:** approximately **0.537335%** per full equipment-generation event, or about **1 in 186** such events.

- **Orc Drum:** approximately **0.092638%**, or about **1 in 1,079**.

- **14 rare red-name backpack Uniques combined:** exact 15-bit bucket model approximately **0.0263548%**, or about **1 in 3,794.38**.

- **37 ordinary blue-name Rare/Unique items combined:** current Full-Eligibility Reference approximately **0.0602521%**, or about **1 in 1,659.69**. The first 10 candidates are affected by the `+0x188` state field, so this is a full-eligibility reference rather than an absolute constant for every SAV state.

Group-total ranking per full equipment event:

**Ancient group >> Orc Head >> Orc Drum >> 37 blue-name group >> 14 red-backpack group**

This compares group totals. It must not be confused with comparing ten Ancient items against one individual red Unique.

## 2. Probability chain: why “type E = 1%” does not mean “one specific Unique = 1%”

The final interval of every C0-C7 character Profile is `99→100`, so the table assigns 1% to type E. Because this game uses a 15-bit random-bucket mapping, the actual machine probability is:

`P(type E | full equipment generation) = 327 / 32768 = 0.997924805%`

After type E is selected, the game still applies special-candidate selection, a 1-in-10 or 1-in-20 audit, Mask/RedMask logic, special fallback branches, and related filters. Type E means “entered the special pool,” not “a Unique must appear.”

A previously validated neutral Class-A enemy reference enters the full equipment generator on `16.498962417%` of kills. Therefore the reference scale is approximately `0.164647238%` for a type-E event per neutral Class-A kill. This `16.498962417%` value is only a conversion reference for that source class, not a universal rate for all enemies.

## 3. The 10 Ancient items

Ancient items do not use type E. They are distributed among ordinary equipment categories `0/1/2/3/4/6/8/9/B`. Candidate records carrying flag `0x40` enter the Ancient eligibility check; if the required state is absent, the candidate can be downgraded. The Ultimate ROM makes the Ancient qualification path reachable while preserving normal character type weights, candidate pools, map/source gates, and RNG.

The following values assume: Ancient eligibility enabled + the relevant candidate pool fully available + one full equipment-generation event has already been entered.

### Combined Ancient rate by character

- **Frodo / C0:** 7.065365190% per full equipment event; average 1 / 14.15; neutral Class-A reference ≈ 1.165712% per kill.

- **Legolas / C1:** 5.638189588%; average 1 / 17.74; neutral Class-A reference ≈ 0.930243% per kill.

- **Aragorn / C2:** 6.574203574%; average 1 / 15.21; neutral Class-A reference ≈ 1.084675% per kill.

- **Gandalf / C3:** 5.157031857%; average 1 / 19.39; neutral Class-A reference ≈ 0.850857% per kill.

- **Éowyn / C4:** 6.574203574%; average 1 / 15.21; neutral Class-A reference ≈ 1.084675% per kill.

- **Gimli / C5:** 6.422688423%; average 1 / 15.57; neutral Class-A reference ≈ 1.059677% per kill.

- **Sam / C6:** 7.065365190%; average 1 / 14.15; neutral Class-A reference ≈ 1.165712% per kill.

- **Gollum / C7:** 7.065365190%; average 1 / 14.15; neutral Class-A reference ≈ 1.165712% per kill.

Overall ranking: **Frodo = Sam = Gollum > Aragorn = Éowyn > Gimli > Legolas > Gandalf.**

### Individual Ancient base rates per full equipment-generation event

- **0B0 5-Finger Knife (type 0 / 18):** Frodo 0.833333%; Legolas 0.555556%; Aragorn 0.277778%; Gandalf 0.555556%; Éowyn 0.277778%; Gimli 0.222222%; Sam 0.833333%; Gollum 0.833333%.

- **0D0 Cultellus (type 0 / 18):** Frodo 0.833333%; Legolas 0.555556%; Aragorn 0.277778%; Gandalf 0.555556%; Éowyn 0.277778%; Gimli 0.222222%; Sam 0.833333%; Gollum 0.833333%.

- **039 Splinted Shield (type 9 / 8):** Frodo 1.250000%; Legolas 0.375000%; Aragorn 1.875000%; Gandalf 0.375000%; Éowyn 1.875000%; Gimli 1.375000%; Sam 1.250000%; Gollum 1.250000%.

- **082 Imperial Sword (type 2 / 9):** Frodo 0.333333%; Legolas 0.333333%; Aragorn 0.555556%; Gandalf 0.333333%; Éowyn 0.555556%; Gimli 0.333333%; Sam 0.333333%; Gollum 0.333333%.

- **083 Bardiche (type 3 / 11):** Frodo 0.909091%; Legolas 0.272727%; Aragorn 0.454545%; Gandalf 0.272727%; Éowyn 0.454545%; Gimli 1.363636%; Sam 0.909091%; Gollum 0.909091%.

- **034 Long Mace (type 4 / 10):** Frodo 1.000000%; Legolas 0.300000%; Aragorn 0.500000%; Gandalf 0.300000%; Éowyn 0.500000%; Gimli 1.000000%; Sam 1.000000%; Gollum 1.000000%.

- **178 Footman Plate (type 8 / 24):** Frodo 0.625000%; Legolas 0.708333%; Aragorn 0.625000%; Gandalf 0.625000%; Éowyn 0.625000%; Gimli 0.625000%; Sam 0.625000%; Gollum 0.625000%.

- **081 Prestige Sword (type 1 / 11):** Frodo 0.272727%; Legolas 0.272727%; Aragorn 1.000000%; Gandalf 0.909091%; Éowyn 1.000000%; Gimli 0.272727%; Sam 0.272727%; Gollum 0.272727%.

- **07B Tabard (type B / 9):** Frodo 0.777778%; Legolas 1.111111%; Aragorn 0.777778%; Gandalf 1.000000%; Éowyn 0.777778%; Gimli 0.777778%; Sam 0.777778%; Gollum 0.777778%.

- **086 Horn Bow (type 6 / 13):** Frodo 0.230769%; Legolas 1.153846%; Aragorn 0.230769%; Gandalf 0.230769%; Éowyn 0.230769%; Gimli 0.230769%; Sam 0.230769%; Gollum 0.230769%.

## 4. Orc Head and Orc Drum

Machine-code control flow shows that the two special candidate positions can return before the normal RedMask check, and the shared fallback region contains fixed generation exits. Cross-report item-table/gameplay identification binds these paths to Orc Head and Orc Drum; the branch structure itself is stronger evidence than the display-name binding of numeric IDs `0x51/0x54`.

- **Orc Head:** 53.845283019% per type-E event; 0.537335435% per full equipment event; average ≈ 1 / 186.10; neutral Class-A reference ≈ 0.088654772% per kill, or ≈ 1 / 1,128 kills.

- **Orc Drum:** 9.283018868% per type-E event; 0.092637548% per full equipment event; average ≈ 1 / 1,079.48; neutral Class-A reference ≈ 0.015284234% per kill, or ≈ 1 / 6,543 kills.

- **Combined:** ≈ 63.128301887% per type-E event; ≈ 0.629972983% per full equipment event; neutral Class-A reference ≈ 0.103939006% per kill, or ≈ 1 / 962 kills.

Orc Head is much more common because the dominant fallback branch assigns roughly 59% to the Head path, while roughly 10% goes to the Drum path. Both are therefore far more common within type E than any one of the 14 target red backpack Uniques.

## 5. The 14 rare red-name backpack Uniques

The 14 targets occupy the type-E candidate region `0x0A–0x19`, excluding the two special Orc Head/Drum candidate positions. The normal target path is:

`type E roll -> 53-way candidate -> 1-in-10 rare audit -> RedMask qualification -> return item`

The 53-way 15-bit mapping is not mathematically uniform: each candidate receives either 618 or 619 of the 32,768 input buckets. The 14 target candidates total 8,656 buckets. A `1..10` roll landing on 10 occupies `3,276 / 32,768` buckets.

`P(any of 14 | one full equipment opportunity under normal type-E assumptions) = (327/32768) × (8656/32768) × (3276/32768) = 0.000263547614 = 0.0263547614% ≈ 1 / 3,794.38`

Using the neutral Class-A outer reference: approximately `0.004348262%` per kill, or approximately `1 / 22,998` kills. This is not a universal enemy rate.

### Individual bucket groups

- **619-bucket targets:** `0BE` Boromir’s Horn; `0FE` Scrolls of Minas Tirith; `12E` Forgotten Palantir; `16E` Oliphaunt Ivory Statuette. Each ≈ 1 / 53,060.03 full equipment events; neutral Class-A reference ≈ 1 / 321,596 kills.

- **The other 10 targets:** 618 buckets each. Each ≈ 1 / 53,145.89 full equipment events; neutral Class-A reference ≈ 1 / 322,117 kills.

- `0AE` Celeborn's Phial: 618 candidate buckets; ≈ 1 / 53,145.89 full equipment opportunities.

- `0BE` Boromir's Horn: 619 candidate buckets; ≈ 1 / 53,060.03.

- `0CE` Cyclopedia of Herbs: 618 candidate buckets; ≈ 1 / 53,145.89.

- `0DE` Sam's Cookpot: 618 candidate buckets; ≈ 1 / 53,145.89.

- `0EE` Fletching Kit: 618 candidate buckets; ≈ 1 / 53,145.89.

- `0FE` Scrolls of Minas Tirith: 619 candidate buckets; ≈ 1 / 53,060.03.

- `10E` Evenstar of Arwen: 618 candidate buckets; ≈ 1 / 53,145.89.

- `11E` Galadriel's Phial: 618 candidate buckets; ≈ 1 / 53,145.89.

- `12E` Forgotten Palantir: 619 candidate buckets; ≈ 1 / 53,060.03.

- `13E` Cracked Palantir: 618 candidate buckets; ≈ 1 / 53,145.89.

- `15E` Woven Goblin Doll: 618 candidate buckets; ≈ 1 / 53,145.89.

- `16E` Oliphaunt Ivory Statuette: 619 candidate buckets; ≈ 1 / 53,060.03.

- `18E` Swollen Spider Glands: 618 candidate buckets; ≈ 1 / 53,145.89.

- `19E` Featherwood Flute: 618 candidate buckets; ≈ 1 / 53,145.89.

The Ultimate ROM’s `0xDBFF` / RedMask engineering goal is to make all 14 targets legally eligible, not to increase their random rate. The 1-in-10 audit, candidate RNG, and outer source gates still apply.

## 6. The 37 ordinary blue-name Rare/Unique items

- **Current Full-Eligibility Reference:** 6.037735849% per type-E event.

- Multiplied by the actual machine type-E bucket share (`0.997924805%`): approximately **0.060252064% per full equipment-generation event**, or ≈ **1 / 1,659.69**.

- Neutral Class-A outer reference: approximately **0.009940965% per kill**, or ≈ **1 / 10,059 kills**.

The 37 items cannot be modeled as “group total / 37.” The first 10 low-index blue candidates pass a 1-in-20 audit and read `char_base+0x188`, whose bit direction is opposite to RedMask. The later 27 candidates in `0x1A–0x38` pass a 1-in-10 audit and are not limited by the `+0x188/+0x18A` pair. The 53-way candidate mapping also has 618/619-bucket asymmetry, while fallback may redirect failures to Orc Head/Drum or a normal/replacement result.

The rigorous public wording is therefore:

> “The 37 blue-name group has an approximately 6.037735849% type-E group rate under the Full-Eligibility Reference model. Exact per-item probabilities require candidate index, `+0x188` state, and exact 15-bit bucket accounting.”

## 7. Unified quick reference

- **Ancient:** does not use type E. Combined ≈ 5.1570%–7.0654% per full equipment event, strongly character-dependent.

- **Orc Head:** 53.845283019% per type E; 0.537335435% per full equipment event; neutral Class-A reference ≈ 0.088654772% per kill.

- **Orc Drum:** 9.283018868% per type E; 0.092637548% per full equipment event; neutral Class-A reference ≈ 0.015284234% per kill.

- **14 red backpack targets:** exact machine-bucket total ≈ 0.0263547614% per full equipment event; neutral Class-A reference ≈ 0.004348262% per kill.

- **37 blue-name items:** Full-Eligibility Reference ≈ 0.060252064% per full equipment event; neutral Class-A reference ≈ 0.009940965% per kill.

With the post-mortem re-death exploit, the correct interpretation is not “each roll becomes easier.” The exploit increases the number of independent death/reward opportunities per unit time; whether this produces more equipment events still depends on that enemy’s source/resource and reward routing.

## 8. Practical farming implications

- **Ancient farming:** choose the character Profile deliberately. Frodo/Sam/Gollum have the highest combined Ancient rate, but the best character for one specific Ancient can differ.

- **Orc Head/Drum:** once type E is reached, especially Orc Head is not the hardest result. The main time cost can be how often the selected source reaches full equipment generation and then type E.

- **14 red backpack Uniques:** qualification repair converts an unreachable/deadlocked state into a reachable one; the remaining RNG is still extremely low, so long dry streaks for one item are normal.

- **37 blue-name items:** do not use only the group-total rate. The first 10 are state-sensitive through `+0x188`. A future per-item extreme-farming model should build a candidate-index + state + exact-bucket matrix.

- Every “per kill” estimate must name the enemy/source. The `16.498962417%` Class-A value is a neutral reference only and must not be applied to every enemy, chest, or corpse.

## 9. Evidence levels and unresolved work

- **A:** eight-character Profile mapping, type-E machine buckets, 14-red 15-bit candidate buckets, and RedMask field/qualification path have strong ROM/SAV/machine-code evidence.

- **A/B:** Ancient item and group rates are established under the “eligibility enabled + full candidate pool” condition; practical map/level conditions can still determine whether the candidate pool is fully available.

- **B:** Orc Head/Drum branch probabilities are structurally clear, but pure machine-code binding of `0x51/0x54` to the displayed names is weaker than the cross-report/game item-table identification.

- **B:** the 37-blue Full-Eligibility Reference group total is usable; exact machine-precise per-item rates for all 37 remain worth building.

- **Unresolved:** complete enemy display-name -> source/resource -> equipment-entry-rate matrix. Therefore there is no valid universal “per kill” final rate for the whole game.

## 10. Related reports and internal research sources

This report synthesizes and extends findings established in the earlier RedMask, drop-eligibility, Extra Treasure, and character-profile reports.

- `GBA_Return_of_the_King_Eight_Character_Inherent_Loot_Profile_Complete_Analysis_2026-08-22_EN.docx`

- `LOTR_ROTK_GBA_14_Red_Backpack_Uniques_RedMask_Research_2026-08-17_EN.docx`

- `LOTR_Return_of_the_King_Drop_Eligibility_Mechanism_Flowchart_PseudoC_ItemID_Reference_EN.docx`

- `GBA_Return_of_the_King_ET_Machine_Code_Probability_Step_Changes_Full_Summary_2026-08-22_EN.docx`

- 2026-08-28 final-ROM static verification: SHA-256, Profile table, and Ancient/RedMask key patch bytes.
