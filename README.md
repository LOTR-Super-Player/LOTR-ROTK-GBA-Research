# The Lord of the Rings: The Return of the King (GBA) — Reverse-Engineering Research Archive

This repository is an unofficial technical research archive for the Game Boy Advance version of **The Lord of the Rings: The Return of the King**.

This research archive integrates the project's fifteen English research reports into one evidence-graded structure. It documents Multiplayer progression, the corpse-hit death-state re-entry exploit, drop eligibility, RedMask, Extra Treasure breakpoints, character loot profiles, rare-item probabilities, Critical-rate evidence, and several high-end farming targets.

**No commercial ROM image is included or required in this public archive.**

## Start Here

For the strongest current findings, read these reports first:

1. [Multiplayer Progression](reports/01_multiplayer_progression.md)
2. [Research Result G: Corpse-Hit Loot Exploit](reports/02_research_result_g_corpse_hit_loot_exploit.md)
3. [RedMask and 14 Hidden Red Backpack Uniques](reports/04_redmask_hidden_uniques.md)
4. [Rare Drop Rates](reports/08_rare_drop_rates.md)

## Full Report Order

1. [Multiplayer Progression: 12 Stations, Host Carry, and Persistent Client Progression](reports/01_multiplayer_progression.md)
2. [Research Result G: Corpse-Hit Death-State Re-entry / Repeated Loot Exploit](reports/02_research_result_g_corpse_hit_loot_exploit.md)
3. [Post-Mortem Repeated Loot and Equipment Farming](reports/03_post_mortem_repeated_loot.md)
4. [RedMask and the 14 Hidden Red Backpack Uniques](reports/04_redmask_hidden_uniques.md)
5. [Drop Eligibility Control Flow, Pseudo-C, and Item/Mask References](reports/05_drop_eligibility.md)
6. [Extra Treasure Machine-Code Probability Breakpoints](reports/06_extra_treasure_breakpoints.md)
7. [Eight-Character Inherent Loot Profiles](reports/07_character_loot_profiles.md)
8. [Rare Drop Rates: Ancient, Orc Head/Drum, 14 Red Uniques, and 37 Blue Rares](reports/08_rare_drop_rates.md)
9. [Critical Rate: Data Basis and Calculation Method](reports/09_critical_rate.md)
10. [Spoon Farming: Nine Code-Evidence Routes](reports/10_spoon_nine_routes.md)
11. [Blood Pirate / of the Fates: 15 Conclusions](reports/11_blood_pirate_fates.md)
12. [Dark Sapphire / of the Fates: Optimal Farming Analysis](reports/12_dark_sapphire_fates.md)
13. [Code-Level Audit of Nine Special Combat Effects](reports/13_special_combat_effects.md)
14. [Runtime Effect Audit of 14 Rare Backpack Uniques](reports/14_backpack_unique_runtime_effects.md)
15. [Grueling Full-Eye Ringwraith: Accuracy, Defense, Dodge, Shield Block, and Practical Build](reports/15_grueling_full_eye_ringwraith_build.md)

## Strongest Current Findings

### Multiplayer Progression

- The first four true Link/Multiplayer groups have a confirmed visible structure of **5 / 1 / 5 / 1 = 12 stations**.
- A fully unlocked Host can carry a lower-progress Client into a later station; after actual play and a normal save, the Client can retain additional Multiplayer progression.

### Corpse-Hit Exploit

- On tested compatible BLRE enemies, a corpse can remain hittable and re-enter death resolution after a valid post-death hit.
- The repeated death-resolution path consumes fresh RNG and can create new ground-item objects; it is not a copy of the first drop.
- A documented entity at `0x020125E0` continued taking damage (`HP -193 -> -607`) and new item objects appeared at the same coordinates between savestates.
- **Corpse-hittable does not automatically mean equipment-farmable.** Source/resource routing still decides whether an equipment opportunity exists.

### Rare-Item Qualification and Probability

- The complete 14-target RedMask qualification state is `0xDBFF`; it opens eligibility but does not raise the random rate.
- The type-E machine share after full equipment generation is `327/32768 = 0.997924805%`.
- Group totals per full equipment-generation event are not equal: Ancient items are much more common as a group than Orc Head/Drum, the 37-blue reference group, or the 14 red backpack Uniques.

### Evidence Discipline

- Every probability must name its parent sample: per kill, per re-death reward cycle, per full equipment-generation event, or per type-E event.
- Dynamic test addresses are not automatically stable global addresses.
- Regional/static homology is not the same as separate runtime reproduction.

## Archive Philosophy

The project separates observed gameplay, SAV/runtime evidence, ROM/machine-code evidence, high-confidence inference, and open questions.

When a result is not closed, it is labeled as unresolved rather than promoted to a fixed fact.

## Citation Guidance

When referencing a probability, include both the exact denominator and the report revision/date.

When referencing a ROM-specific patch or checksum, identify the exact build rather than treating the value as a universal retail-ROM constant.

## Latest Empirical Scope Corrections

- Route recommendations are not map/character exclusivity claims.
- Spoon has broader observed character/map coverage than some older guide assumptions.
- Snowbird's has been observed in the White Mountains as well as the previously modeled Ice Cave route.
