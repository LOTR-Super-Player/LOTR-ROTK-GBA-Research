# GBA The Lord of the Rings: The Return of the King

## Runtime Effect Audit of 14 Rare Backpack Uniques

Based on the release BLRE v1.0 ROM effect table, character-restriction metadata, and effect-application call chain

Research date: 2026-09-01 | Evidence standard: ROM static evidence as primary proof, cross-checked against historical guides and player testing

## 1. Executive Summary

This audit confirms that the item descriptions shown to players cannot be treated as the actual runtime parameters of these 14 Backpack Uniques. The release ROM contains an independent Type-E effect table; the backpack scanning routine reads those binary records and applies their effects directly to character runtime attributes. For many items, the displayed description and the final effect table clearly belong to different data revisions.

- Cracked Palantir is actually +3 All Primary Stats with -30% Corruption Resistance. It is not a displayed +4 being clipped to +3 at high character levels.

- Woven Goblin Doll directly applies +1 Strength, +1 Defense, and +1 Health. The observed +1/+1 Strength/Defense result matches the ROM exactly.

- Forgotten Palantir is actually +5 All Primary Stats and -50% Corruption Resistance.

- Celeborn's Phial is actually +75% Poison Resistance, not +50%.

- Galadriel's Phial applies +50% Fear Resistance and also has an undisclosed +50% Poison Resistance effect.

- Fletching Kit directly applies Legolas-only +3 generic Damage; Scrolls of Minas Tirith directly applies Gandalf-only +4 Wisdom.

- Oliphaunt Ivory Statuette applies +2 Melee Armor and +3 Health; Swollen Spider Glands applies +3 Damage; Featherwood Flute also has a hidden +5% Dodge effect.

- The original English description of Cyclopedia of Herbs already says that herbs heal +25 hit points, and the ROM likewise applies +25. The Chinese '+25%' wording is a secondary-source/translation error.

The hypothesis that high character level causes these discrepancies through attribute overflow can be rejected. The five primary attributes are stored in 16-bit RAM fields, while item bonuses are signed 8-bit values added into those 16-bit attributes. Historical CodeBreaker documentation also defines direct writes to these fields up to `0x3E7` (999). Normal gameplay values are nowhere near the 16-bit overflow boundary.

## 2. Research Scope, ROM Version, and Evidence Boundaries

The primary evidence in this report comes from the user-provided English release ROM: title RETURN KING, Game Code BLRE, Version 00 (v1.0), size 16,777,216 bytes, SHA-256 b5f556593c5fbe3531a4354b047f0bed364a124ceee43f278a48f4d26b0ffa9c.

The conclusions in this report strictly apply first to this BLRE v1.0 sample. BLRJ (Japanese release) and Chinese-localized builds based on BLRJ are likely to share the same core data structures, but until their corresponding binaries are compared byte-for-byte, BLRE closure must not be generalized into a claim that every regional build is fully verified.

The scope is the 14 target red Backpack Uniques specified for this project. The Type-E backpack range 0A-19 also contains Orc Drum (14 E) and Orc Head (17 E); those two are outside the current 14-item RedMask target set and are therefore excluded from the main table.

## 3. Why the ROM Effect Table Has Higher Evidentiary Weight Than Item Descriptions

The release program does not parse the English description text at runtime to determine item effects. Instead, it reads fixed binary effect records. The key structures and call chain are:

- The Type-E descriptor structure is at `0x08069180`; its effect-table pointer is `0x08064B84`, and the item-metadata-table pointer is `0x080653CC`.

- `FUN_0802AC24` scans character equipment/backpack slots. A backpack item proceeds to effect application only if it satisfies the backpack-active flag condition.

- `FUN_0802DB90` determines whether an effect is allowed for the current character according to item type, subtype ID, and character mask.

- `FUN_0802DD10` reads up to four effect-id/value pairs from each 12-byte effect record; effect-id `0x5A` denotes an empty slot.

- `FUN_0802DDC4` writes each signed value into the corresponding character attribute. Effect-id `0x3F` is the special All Primary Stats handler, simultaneously modifying Strength, Accuracy, Health, Defense, and Courage.

Accordingly, 'actual effect' below means the direct effect that the release program loads into the runtime attribute system. For attributes such as Damage, Wisdom, and Hit Points Regen that subsequently participate in combat or regeneration formulas, this report explicitly distinguishes the direct stored increment from the final per-hit, per-spell, or per-second derived outcome.

## 4. Final Comparison Table for All 14 Items

| Item / ID | Historical description (player-facing) | ROM direct runtime effect | Character restriction | Assessment | Evidence |
| --- | --- | --- | --- | --- | --- |
| Celeborn's Phial <br> 0A E | +50% Poison Resistance | +75% Poison Resistance | None | Clear mismatch: 50 -> 75 | A |
| Boromir's Horn <br> 0B E | +10% Experience, +3 Courage | +10% Experience Bonus; +3 Courage | None | Consistent | A |
| Cyclopedia of Herbs <br> 0C E | Herbs heal +25 hit points | Fixed +25 HP to herb healing | None | Original English description matches; '+25%' is a secondary Chinese mistranslation | A |
| Sam's Cookpot <br> 0D E | Health regeneration up to 25% | +4 Hit Points Regen (internal regeneration units) | Frodo / Sam | Data-layer mismatch; insufficient evidence that a downstream formula resolves this to 'up to 25%' | A- |
| Fletching Kit <br> 0E E | +10 Bow Damage | +3 generic Damage (not a dedicated Bow Damage field) | Legolas | Clear mismatch: both effect type and value changed | A |
| Scrolls of Minas Tirith <br> 0F E | All spells +10 Damage | +4 Wisdom | Gandalf | Clear mismatch: direct effect is not spell damage; Wisdom may affect spells indirectly | A |
| Evenstar of Arwen <br> 10 E | +20 Courage | +20 Courage | Aragorn | Consistent | A |
| Galadriel's Phial <br> 11 E | +50% Fear Resistance | +50% Fear Resistance; hidden +50% Poison Resistance | Frodo / Sam | Partially consistent; description omits the full second effect | A |
| Forgotten Palantir <br> 12 E | +4 All Primary Stats; Corrupt | +5 All Primary Stats; -50% Corruption Resistance | None | Clear mismatch: +4 -> +5; corruption penalty magnitude is hidden | A |
| Cracked Palantir <br> 13 E | +4 All Primary Stats; Corrupt | +3 All Primary Stats; -30% Corruption Resistance | None | Clear mismatch: +4 -> +3; not high-level overflow | A |
| Woven Goblin Doll <br> 15 E | +5 Strength; +5 Defense | +1 Strength; +1 Defense; hidden +1 Health | None | Clear mismatch: +5/+5 -> +1/+1, plus undisclosed +1 Health | A |
| Oliphaunt Ivory Statuette <br> 16 E | +5 Melee Toughness; +5 Health | +2 Melee Armor; +3 Health | None | Clear mismatch: both values changed | A |
| Swollen Spider Glands <br> 18 E | +9 Damage | +3 generic Damage | None | Clear mismatch: runtime table writes +3 Damage | A |
| Featherwood Flute <br> 19 E | +10% Shield Block | +10% Shield Block; hidden +5% Dodge | None | Partially consistent; description omits +5% Dodge | A |

## 5. Item-by-Item Technical Analysis

### 5.1 Celeborn's Phial (0A E)

Historical description: +50% Poison Resistance.

ROM direct effect: +75% Poison Resistance. Character restriction: none.

Effect record address: `0x08064BFC`; decoded as effect `0x2A` = +75.

Explanation: effect `0x2A` is cross-mapped through other equipment/resistance paths to Poison Resistance; value `0x4B` equals 75. This is not a display-rounding issue.

### 5.2 Boromir's Horn (0B E)

Historical description: +10% Experience, +3 Courage.

ROM direct effect: +10% Experience Bonus; +3 Courage. Character restriction: none.

Effect record address: `0x08064C08`; decoded as effect `0x22` = +10 and effect `0x15` = +3.

Explanation: effect `0x22` is Experience Bonus and is consumed as a percentage by the experience-award routine; effect `0x15` is Courage. Both effects agree with the description.

### 5.3 Cyclopedia of Herbs (0C E)

Historical description: Herbs heal +25 hit points.

ROM direct effect: herbs heal a fixed +25 HP. Character restriction: none.

Effect record address: `0x08064C14`; decoded as effect `0x40` = +25.

Explanation: the `0x40`=25 field is added directly to the base herb healing amount before the healing routine is called. It is therefore a fixed +25 HP, not +25%.

### 5.4 Sam's Cookpot (0D E)

Historical description: Health regeneration up to 25%.

ROM direct effect: +4 Hit Points Regen (internal regeneration-attribute units). Character restriction: Frodo / Sam.

Effect record address: `0x08064C20`; decoded as effect `0x1D` = +4.

Explanation: effect `0x1D` is the same Hit Points Regen effect ID used by Hawly's Sword of Leaves, Green Mace of the Outlander, and related items. This confirms that the item writes +4 HP Regen units. Historical player documentation often interprets this attribute as a regeneration-rate increment, but the periodic regeneration consumer has not yet been closed deeply enough to prove that '+4 units' necessarily means '+4%'; still less can the ROM record alone establish a fixed +25% value.

### 5.5 Fletching Kit (0E E)

Historical description: +10 Bow Damage.

ROM direct effect: +3 generic Damage (not a dedicated Bow Damage field). Character restriction: Legolas.

Effect record address: `0x08064C2C`; decoded as effect `0x05` = +3.

Explanation: effect `0x05` is the generic Damage field, shared with Whetstone and ordinary '+Damage' modifiers. This record does not contain a Bow Damage +10 effect. The character mask does, however, genuinely restrict the item to Legolas. Final per-arrow damage still passes through downstream combat formulas, but the direct item attribute is +3 generic Damage.

### 5.6 Scrolls of Minas Tirith (0F E)

Historical description: All spells +10 Damage.

ROM direct effect: +4 Wisdom. Character restriction: Gandalf.

Effect record address: `0x08064C38`; decoded as effect `0x34` = +4.

Explanation: effect `0x34` cross-maps to Wisdom through known items such as Glamdring. The ROM directly grants Gandalf +4 Wisdom rather than writing a spell-damage field. Wisdom may indirectly increase spell damage through downstream formulas, but that is not equivalent to a dedicated 'all spells +10 damage' attribute.

### 5.7 Evenstar of Arwen (10 E)

Historical description: +20 Courage.

ROM direct effect: +20 Courage. Character restriction: Aragorn.

Effect record address: `0x08064C44`; decoded as effect `0x15` = +20.

Explanation: effect `0x15`=20 is Courage +20, and the metadata character mask maps to Aragorn. This is one of the few target items whose displayed description and runtime effect table fully agree.

### 5.8 Galadriel's Phial (11 E)

Historical description: +50% Fear Resistance.

ROM direct effect: +50% Fear Resistance; hidden +50% Poison Resistance. Character restriction: Frodo / Sam.

Effect record address: `0x08064C50`; decoded as effect `0x29` = +50 and effect `0x2A` = +50.

Explanation: effect `0x29`=50 is Fear Resistance and effect `0x2A`=50 is Poison Resistance. The description discloses only the first effect, so the item is actually stronger than the visible text indicates.

### 5.9 Forgotten Palantir (12 E)

Historical description: +4 All Primary Stats; Corrupt.

ROM direct effect: +5 All Primary Stats; -50% Corruption Resistance. Character restriction: none.

Effect record address: `0x08064C5C`; decoded as effect `0x28` = -50 and effect `0x3F` = +5.

Explanation: effect `0x3F`=+5 invokes a dedicated handler that adds +5 to Strength, Accuracy, Health, Defense, and Courage simultaneously; effect `0x28`=-50 is Corruption Resistance. The word 'Corrupt' in the description is only a qualitative warning and does not disclose the -50 value.

### 5.10 Cracked Palantir (13 E)

Historical description: +4 All Primary Stats; Corrupt.

ROM direct effect: +3 All Primary Stats; -30% Corruption Resistance. Character restriction: none.

Effect record address: `0x08064C68`; decoded as effect `0x28` = -30 and effect `0x3F` = +3.

Explanation: effect `0x3F`=+3 directly writes all five primary attributes through the special handler; effect `0x28`=-30 is Corruption Resistance. Because +3 is hard-coded in the final ROM, there is no need to invoke a 'high-level overflow' hypothesis to explain the observed +3 result.

### 5.11 Woven Goblin Doll (15 E)

Historical description: +5 Strength; +5 Defense.

ROM direct effect: +1 Strength; +1 Defense; hidden +1 Health. Character restriction: none.

Effect record address: `0x08064C80`; decoded as effect `0x11` = +1, effect `0x14` = +1, and effect `0x13` = +1.

Explanation: the three active slots are `0x11`=Strength +1, `0x14`=Defense +1, and `0x13`=Health +1. The observed +1/+1 Strength/Defense result therefore matches the machine data exactly, and the visible description also omits +1 Health.

### 5.12 Oliphaunt Ivory Statuette (16 E)

Historical description: +5 Melee Toughness; +5 Health.

ROM direct effect: +2 Melee Armor; +3 Health. Character restriction: none.

Effect record address: `0x08064C8C`; decoded as effect `0x13` = +3 and effect `0x0D` = +2.

Explanation: effect `0x13` is Health +3 and effect `0x0D` is Melee Armor +2. Effect `0x0D` can be independently verified against ordinary armor records, so the historical '+5 Melee Toughness / +5 Health' wording does not match the final effect table.

### 5.13 Swollen Spider Glands (18 E)

Historical description: +9 Damage.

ROM direct effect: +3 generic Damage. Character restriction: none.

Effect record address: `0x08064CA4`; decoded as effect `0x05` = +3.

Explanation: effect `0x05` is generic Damage +3. This is the direct attribute increment; final extra damage per attack may still be transformed by weapon, skill, target-defense, and other combat formulas. The ROM, however, does not assign a +9 base Damage effect to this item.

### 5.14 Featherwood Flute (19 E)

Historical description: +10% Shield Block.

ROM direct effect: +10% Shield Block; hidden +5% Dodge. Character restriction: none.

Effect record address: `0x08064CB0`; decoded as effect `0x2E` = +10 and effect `0x2C` = +5.

Explanation: effect `0x2E`=Shield Block +10 is verifiable against ordinary shield effect records; effect `0x2C`=Dodge +5 cross-maps through Nimblefoot Rune and '+Dodge' modifiers. The visible description mentions only Shield Block.

## 6. Woven Goblin Doll: Why This Is Not High-Level Overflow

This is one of the most important corrections in this audit. Woven Goblin Doll's own 12-byte effect record contains three +1 entries: Strength +1, Defense +1, and Health +1. There is no stage in which the item first applies +5 and then gets clipped to +1 on a high-level character.

The primary-attribute runtime fields are contiguous: Strength `0x03003CC6`, Accuracy `0x03003CC8`, Health `0x03003CCA`, Defense `0x03003CCC`, and Courage `0x03003CCE`. `FUN_0802DDC4` stores these values as 16-bit shorts while each item value is added as a signed byte. A signed-16 wrap would require values near +/-32768, far beyond normal character values.

The 2006 CodeBreaker Guide likewise defines direct writes to these primary attributes up to `0x3E7` (999). Therefore, within the normal game range, the claim that 'the character is so high-level that +5 only displays as +1' is inconsistent with the program structure.

## 7. The Two Palantirs: The Cleanest Evidence of Data-Version Divergence

The two Palantirs form the cleanest controlled comparison. Both use the same effect-id `0x3F` (All Primary Stats), and the same function modifies all five primary attributes, yet the final ROM assigns +5 to one and +3 to the other while historical descriptions list both as +4.

- Forgotten: description +4; ROM +5; additionally -50 Corruption Resistance.

- Cracked: description +4; ROM +3; additionally -30 Corruption Resistance.

This pattern strongly fits a scenario in which the text asset was authored at a balance revision where both items were +4, after which the gameplay table was rebalanced to +5/+3 without synchronized text updates. It looks more like asset/data version divergence than a single typographical error.

## 8. Does This Explain Why the Items Became Hidden or Unobtainable?

At present, it is not justified to infer that 'the descriptions were wrong, therefore the designers deliberately abandoned all 14 items.' No developer interview, source-control history, prototype comparison, or internal design document establishes that causal relationship.

What can be objectively established are two separate release-build phenomena:

1.  Effect layer: for multiple items, the player-facing description is out of sync with the final effect table; some items contain undisclosed effects, rebalanced values, or even a different effect type.

2.  Acquisition layer: this project has previously closed the 14-entry RedMask eligibility gate, and the normal first-acquisition flow contains a clear eligibility/recording logic anomaly. A ROM-level bypass only permits eligibility; it does not increase the Type-E entry probability, the 1-in-10 gate, or any other RNG stage.

The more cautious development-history interpretation is therefore that the 14-item system reached a fairly advanced level of implementation but retained two late-stage integration problems: an unfinished acquisition-eligibility flow and unsynchronized text/final-effect data. Both may be late-development residue, but there is currently no evidence that one caused the other.

## 9. Direct Effects Must Be Distinguished from Final Combat Outcomes

This report treats the ROM effect table as the highest-grade evidence for direct runtime attributes. For the following effects, however, the stored value must not be mechanically equated with the final per-action gameplay number:

- Fletching Kit / Spider Glands: +3 generic Damage is the direct value entering the character damage system; the final per-arrow or per-melee-hit increment may still pass through weapon and skill formulas.

- Scrolls: the direct effect is +4 Wisdom; final spell damage depends on how Wisdom enters each spell's formula.

- Cookpot: the direct effect is +4 Hit Points Regen units; whether the historical phrase 'up to 25%' can be derived from a regeneration formula requires an independent static/dynamic closure of the periodic regen consumer.

This distinction matters in both directions: it prevents us from treating description text as program parameters, and it also prevents us from incorrectly rewriting an internal '+3' attribute as 'every attack always deals exactly 3 additional points of final damage.'

## 10. Effect-ID Reference Used in This Report

| Effect ID | Meaning               | Validation method                                                                |
|-----------|-----------------------|----------------------------------------------------------------------------------|
| `0x05`      | Damage                | Cross-mapped against ordinary +Damage effects, Whetstone, and modifiers          |
| `0x0D`      | Melee Armor           | Cross-mapped against ordinary armor records                                      |
| `0x11`      | Strength              | Known Uniques such as Narsil                                                     |
| `0x12`      | Accuracy              | Narsil / Nightfell Bow                                                           |
| `0x13`      | Health                | Hadhafang / Starfall Bow                                                         |
| `0x14`      | Defense               | Nightfell Bow                                                                    |
| `0x15`      | Courage               | Narsil / Evenstar                                                                |
| `0x1D`      | Hit Points Regen      | Hawly's Sword / Green Mace and related items                                     |
| `0x22`      | Experience Bonus      | Hawly's Sword; experience-award path                                             |
| `0x28`      | Corruption Resistance | Negative values used by Palantir / Corrupt effects                               |
| `0x29`      | Fear Resistance       | Galadriel / Shining Helm                                                         |
| `0x2A`      | Poison Resistance     | Celeborn / Shining Helm                                                          |
| `0x2C`      | Dodge                 | Nimblefoot Rune / Dodge modifiers                                                |
| `0x2E`      | Shield Block          | Ordinary shield effect table                                                     |
| `0x34`      | Wisdom                | Glamdring / Staff-class Uniques                                                  |
| `0x3F`      | All Primary Stats     | Special `FUN_0802DDC4` branch; modifies all five primary attributes simultaneously |
| `0x40`      | Herb healing bonus    | Added directly to healing amount in the herb-consumption path                    |

## 11. Evidence Grades

- A: the original release-ROM effect record plus the application function and/or same-ID cross-mapping is sufficient to confirm the direct runtime attribute.

- A-: the direct field written by the item is confirmed, but the downstream conversion from that internal unit to a player-visible percentage or per-second result is not yet fully closed. Only Sam's Cookpot is graded A- in this report.

- Historical guides / CodeBreaker documentation / player testing: used to establish what players historically saw or recorded and for independent cross-checking; these sources do not override ROM machine evidence.

## 12. Raw ROM Record Appendix (Reproducible)

Type-E effect table base = `0x08064B84`. Each subtype record is 12 bytes. The complete raw records for the 14 audited items are reproduced below. Active effect pairs occupy bytes +3/+4, +5/+6, +7/+8, and +9/+10; effect-id `0x5A` denotes an empty slot.

| ID   | Item                      | Effect record address | 12-byte raw record                  |
|------|---------------------------|-----------------------|-------------------------------------|
| 0A E | Celeborn's Phial          | `0x08064BFC`            | F6 02 09 2A 4B 5A 00 5A 00 5A 00 00 |
| 0B E | Boromir's Horn            | `0x08064C08`            | F7 02 09 22 0A 15 03 5A 00 5A 00 00 |
| 0C E | Cyclopedia of Herbs       | `0x08064C14`            | F8 02 09 40 19 5A 00 5A 00 5A 00 00 |
| 0D E | Sam's Cookpot             | `0x08064C20`            | F9 02 09 1D 04 5A 00 5A 00 5A 00 00 |
| 0E E | Fletching Kit             | `0x08064C2C`            | FA 02 09 05 03 5A 00 5A 00 5A 00 00 |
| 0F E | Scrolls of Minas Tirith   | `0x08064C38`            | FB 02 09 34 04 5A 00 5A 00 5A 00 00 |
| 10 E | Evenstar of Arwen         | `0x08064C44`            | FC 02 09 15 14 5A 00 5A 00 5A 00 00 |
| 11 E | Galadriel's Phial         | `0x08064C50`            | FD 02 09 29 32 2A 32 5A 00 5A 00 00 |
| 12 E | Forgotten Palantir        | `0x08064C5C`            | FE 02 09 28 CE 3F 05 5A 00 5A 00 00 |
| 13 E | Cracked Palantir          | `0x08064C68`            | FF 02 09 28 E2 3F 03 5A 00 5A 00 00 |
| 15 E | Woven Goblin Doll         | `0x08064C80`            | 01 03 09 11 01 14 01 13 01 5A 00 00 |
| 16 E | Oliphaunt Ivory Statuette | `0x08064C8C`            | 02 03 09 13 03 0D 02 5A 00 5A 00 00 |
| 18 E | Swollen Spider Glands     | `0x08064CA4`            | 04 03 09 05 03 5A 00 5A 00 5A 00 00 |
| 19 E | Featherwood Flute         | `0x08064CB0`            | 05 03 09 2E 0A 2C 05 5A 00 5A 00 00 |

## 13. Character-Restriction Metadata

These restrictions are also present in the item metadata rather than being additions made by guide authors. `FUN_0802DB90` uses the high-order character mask to determine whether a backpack effect applies to the current character; Sam is grouped with Frodo under the same character bit in this logic.

- Sam's Cookpot and Galadriel's Phial: Frodo / Sam.

- Fletching Kit: Legolas.

- Scrolls of Minas Tirith: Gandalf.

- Evenstar of Arwen: Aragorn.

- All other target items in this report use the unrestricted/all-character mask.

## 14. Final Conclusions

1\. The reported discrepancy is a real ROM-data issue, not an attribute overflow caused by high character level. Woven Goblin Doll and Cracked Palantir are both directly proven by the release ROM.

2\. These 14 items can no longer be studied under the assumption 'item description = actual effect.' The correct research method is to maintain separate Description Value and Runtime Effect fields.

3\. The most plausible technical explanation is that the balance/implementation table was changed late in development without synchronizing the text assets. The Palantirs' uniform +4 descriptions versus final +5/+3 values provide the strongest support for this model.

4\. There is currently no evidence that developers deliberately made the items unobtainable because their descriptions and effects disagreed. The acquisition-eligibility anomaly and the effect-text mismatch should be treated as two independent release-build defects unless a future prototype, source code, or developer testimony directly connects them.

5\. For players, the most useful final attribute reference is the 'ROM Direct Runtime Effect' column in this report. The downstream percentage interpretation of Cookpot and the exact translation of Damage/Wisdom into final combat output belong to the next layer of formula research and do not alter the direct-effect conclusions established here.

## 15. Sources

- User-provided: English The Lord of the Rings: The Return of the King ROM (BLRE v1.0), SHA-256 b5f556593c5fbe3531a4354b047f0bed364a124ceee43f278a48f4d26b0ffa9c.

- User-provided: corrected decompilation/disassembly notes for The Lord of the Rings: The Return of the King GBA (including key functions `FUN_0802AC24` / `FUN_0802DB90` / `FUN_0802DD10` / `FUN_0802DDC4`).

- User-provided: English-version code-hacking guide, including extracted/organized material from the historical CodeBreaker Guide v1.20.

- GameFAQs: gothmogsbane, Codebreaker Code Guide v1.20; Gothmog14, Item/Bonus Map Guide; Scizor_CT, Item Modifier Stats. Used only for historical descriptions and terminology cross-checks.

- Existing project technical handoff: the 14-entry RedMask eligibility system, Type-E entry path, and established evidence boundaries. This material is used to explain the hidden/unfinished acquisition flow, not to infer concrete item-attribute values.

## 16. Recommended Follow-up Research

To upgrade this direct-attribute audit into a final combat/regeneration-performance table, the following three controlled dynamic experiments have the highest value:

1. Cookpot: on the same Frodo/Sam character with fixed maximum HP and fixed damage taken, compare frame-by-frame or second-by-second healing for no Cookpot / a known +4 Regen item / Cookpot, thereby identifying the exact percentage formula consumed from effect `0x1D`.

2. Fletching Kit / Spider Glands: hold weapon, Strength, skills, enemy armor, and RNG constant; compare damage distributions to determine how +3 generic Damage is transformed into final arrow/melee output.

3. Scrolls: hold Gandalf's Wisdom and one spell level constant, compare a controlled +4 Wisdom injection against a baseline state, and measure final damage for each spell to determine whether the old '+10 all spells' wording ever approximated the intended design target.
