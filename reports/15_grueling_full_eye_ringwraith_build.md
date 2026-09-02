# GBA The Lord of the Rings: The Return of the King

## Grueling + Fully Open Eye Ringwraith: Accuracy, Defense, Dodge, Shield Block, and Practical Build Code Audit

Research date: 2026-09-02 | Primary target: retail BLRE v1.0 | Difficulty terminology normalized to Grueling

> **Core conclusion:** against the standard Grueling + fully open-Eye Ringwraith (Accuracy 250 / Defense 250), player Accuracy 295 reaches the 95% standard-hit hard cap, while player Defense 290 reduces the Ringwraith to the 10% standard-hit hard floor. Raising Defense to 300 or 350 does not improve that hit-check layer; further survivability must come from independent layers such as Dodge and Shield Block.

## 1. Scope, terminology, and evidence grading

This report consolidates the earlier full-Eye Ringwraith Accuracy/Defense audit with the later work on Defense + Dodge + Shield Block efficiency and practical equipment construction. The difficulty label used throughout is Grueling.

Primary evidence is the project BLRE v1.0 decompilation, enemy data, and the ROM-closed backpack Unique effect table. The historical CodeBreaker equipment guide is used separately for specific item, prefix, suffix, and rune values. Machine-code conclusions and guide-derived build inputs are not conflated.

- Grade A (machine code / ROM): standard hit formula; 10%-95% clamp; enemy `0x3A` base stats; Grueling scaling; +25% Eye enhancement; Dodge/Block ordering; per-layer 75% probability cap; Featherwood Flute +10% Shield Block and hidden +5% Dodge.

- Equipment-table cross-check: Tower Shield, Isildur's prefix, of the Crow suffix, Nimblefoot Rune, and similar entries come from the supplied historical CodeBreaker Guide. They are useful for build budgeting, but this report does not upgrade every exact affix combination to a proven natural-drop result.

- All combined-avoidance calculations below model only the requested three-layer subset: Accuracy/Defense -> Dodge -> Shield Block. Scripted damage, guaranteed-hit abilities, unrelated status checks, and other special defensive branches are outside that formula.

## 2. Actual standard hit formula

`FUN_08011ac0` computes attacker Accuracy + 50 - defender Defense, clamps the result to 10 through 95, then rolls a random integer from 1 through 100 and succeeds when the roll is less than or equal to the threshold.

> **Standard hit formula:** `HitChance = clamp(AttackerAccuracy + 50 - DefenderDefense, 10, 95)%`

- Any raw result below 10 becomes 10%.

- Any raw result above 95 becomes 95%.

- The standard path therefore cannot reach 100% hit chance.

- Defense alone cannot produce 100% evasion because the attacker retains a 10% floor.

## 3. Why the Grueling + full-Eye Ringwraith is 250 / 250

1. The fully open Eye spawns enemy ID `0x3A`. Its retail enemy record has base Accuracy 100 and base Defense 100.

2. Grueling applies a +100% increment to those stats: 100 -> 200.

3. The Eye enhancement state then adds 25% of the current value: 200 + 50 = 250.

4. The Eye 91-100 fully open phase enables the highest danger state and the `0x3A` spawn, but the Accuracy/Defense routines do not apply another tier above the +25% Eye stat increase.

> **Standard Grueling + fully open-Eye Ringwraith:** Accuracy = 250; Defense = 250.

A separate global state, `DAT_03003bac`, can add +20 in a different special-enemy path. That is not an inherent bonus attached to enemy ID `0x3A`, so the standard full-Eye Ringwraith should be modeled as 250/250, not 270/270.

## 4. Exact difference between 240, 250, and 260 Accuracy

Because the target has Defense 250, player standard hit chance simplifies to clamp(PlayerAccuracy - 200, 10, 95)%.

| **Player Accuracy** | **Standard hit chance** | **Meaning**          |
|---------------------|-------------------------|----------------------|
| 200                 | 10%                     | floor protection     |
| 220                 | 20%                     |                      |
| 240                 | 40%                     |                      |
| 250                 | 50%                     |                      |
| 260                 | 60%                     |                      |
| 270                 | 70%                     |                      |
| 280                 | 80%                     |                      |
| 290                 | 90%                     |                      |
| 294                 | 94%                     |                      |
| 295                 | 95%                     | 95% hard cap reached |
| 300                 | 95%                     | 95% hard cap reached |
| 350                 | 95%                     | 95% hard cap reached |
| 999                 | 95%                     | 95% hard cap reached |

- 240 Accuracy = 40%.

- 250 Accuracy = 50%, +10 percentage points over 240 and about 25% more successful hits in relative terms.

- 260 Accuracy = 60%, another +10 points and about 20% more successful hits relative to 250.

- 295 is the saturation point against Defense 250; 295, 300, 350, and 999 all resolve to 95%.

## 5. Exact difference between 250, 300, and 350 Defense

The Ringwraith has Accuracy 250, so its standard hit chance against the player is clamp(300 - PlayerDefense, 10, 95)%.

| **Player Defense** | **Ringwraith hit** | **Base miss** | **Meaning**            |
|--------------------|--------------------|---------------|------------------------|
| 205                | 95%                | 5%            | attacker 95% cap       |
| 220                | 80%                | 20%           |                        |
| 230                | 70%                | 30%           |                        |
| 240                | 60%                | 40%           |                        |
| 250                | 50%                | 50%           |                        |
| 260                | 40%                | 60%           |                        |
| 270                | 30%                | 70%           |                        |
| 280                | 20%                | 80%           |                        |
| 289                | 11%                | 89%           |                        |
| 290                | 10%                | 90%           | 10% hard floor reached |
| 300                | 10%                | 90%           | 10% hard floor reached |
| 350                | 10%                | 90%           | 10% hard floor reached |
| 999                | 10%                | 90%           | 10% hard floor reached |

- 250 Defense: the Ringwraith still has a 50% standard hit chance.

- 290 Defense: the Ringwraith is reduced to the 10% hard floor.

- 300 Defense: still 10%.

- 350 Defense: still 10%.

- Against this standard 250-Accuracy target, 290 -> 300 -> 350 provides no additional benefit in the standard hit-check layer.

## 6. Defense is not Armor

Defense controls whether an attack passes the Accuracy-versus-Defense hit check. It does not directly reduce the numeric damage of a hit that already connected. Post-hit damage is handled by Melee Armor, Missile Armor, Critical Armor, and other damage-processing rules. Once Defense reaches 290 against this target, further Defense neither reduces the 10% floor nor directly reduces connected-hit damage.

## 7. Real ordering of Defense + Dodge + Shield Block

The standard enemy attack path first performs the Accuracy/Defense hit check. After that succeeds, the relevant later layers check Dodge (effect `0x2C`); if Dodge fails and the shield/block condition is satisfied, Shield Block (effect `0x2E`) is checked. The code also contains an earlier unrelated `0x2B` check and other special branches, so the formula below intentionally models only the three layers requested here.

> **Three-layer subset:** standard hit -> Dodge -> Shield Block -> later damage processing.

`FUN_08011b4c` performs the 1-100 probability check for positive effect values and clamps any value above `0x4B` = 75 to 75. Therefore, the effective per-check cap is 75% for Dodge and 75% for Shield Block.

The 75% figure is a code cap, not proof that normal equipment can necessarily reach 75% Shield Block. Shield Block is also conditional on a valid shield/block state; without that condition the layer does not participate.

## 8. Combined formula and efficiency

For the Grueling full-Eye Ringwraith, define H = clamp(300 - Defense, 10, 95)/100; D = min(Dodge,75)/100; B = min(ShieldBlock,75)/100. Considering only these three layers:

> **Three-layer formula:** `Penetration P = H x (1 - D) x (1 - B); three-layer avoidance = 1 - P.`

The probabilities are multiplicative, not additive. At Defense 290, Dodge 50%, Block 50%: 10% x 50% x 50% = 2.5% penetration, or 97.5% three-layer avoidance.

| **Defense** | **Dodge** | **Block** | **Layer-1 hit** | **Penetration** | **Total avoidance** |
|-------------|-----------|-----------|-----------------|-----------------|---------------------|
| 250         | 0%        | 0%        | 50.0%           | 50.000%         | 50.000%             |
| 250         | 25%       | 45%       | 50.0%           | 20.625%         | 79.375%             |
| 250         | 45%       | 45%       | 50.0%           | 15.125%         | 84.875%             |
| 290         | 0%        | 0%        | 10.0%           | 10.000%         | 90.000%             |
| 290         | 15%       | 45%       | 10.0%           | 4.675%          | 95.325%             |
| 290         | 25%       | 45%       | 10.0%           | 4.125%          | 95.875%             |
| 290         | 30%       | 45%       | 10.0%           | 3.850%          | 96.150%             |
| 290         | 45%       | 45%       | 10.0%           | 3.025%          | 96.975%             |
| 290         | 55%       | 45%       | 10.0%           | 2.475%          | 97.525%             |
| 290         | 65%       | 45%       | 10.0%           | 1.925%          | 98.075%             |
| 290         | 75%       | 45%       | 10.0%           | 1.375%          | 98.625%             |
| 290         | 75%       | 75%       | 10.0%           | 0.625%          | 99.375%             |

## 9. Why deliberately balancing Dodge and Block is not mathematically optimal

If each percentage point has identical opportunity cost and neither layer is capped, the two later layers provide avoidance D + B - D x B. For a fixed D+B budget, maximizing avoidance means minimizing D x B, which favors concentrating points into one layer rather than forcing an even split.

| **Same total budget** | **Dodge** | **Block** | **Two-layer penetration** | **Two-layer avoidance** |
|-----------------------|-----------|-----------|---------------------------|-------------------------|
| 100 points            | 50        | 50        | 25.00%                    | 75.00%                  |
| 100 points            | 75        | 25        | 18.75%                    | 81.25%                  |

Real equipment costs are not interchangeable, so this is not an absolute gearing command. The practical method is to compare actual percentage gained per slot. With the currently located equipment sources, first reach Defense 290, then exploit the strong base shield plus Featherwood Flute to raise Block, and use the broader set of Dodge sources for further gains.

## 10. Located equipment sources

| **Source**                           | **Effect**                                      | **Evidence**                 | **Interpretation**                                                                                                        |
|--------------------------------------|-------------------------------------------------|------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Featherwood Flute                    | +10% Shield Block; hidden +5% Dodge             | Grade A ROM                  | Type-E record: effect `0x2E` +10 and `0x2C` +5.                                                                               |
| Tower Shield                         | +35% Shield Block; -5 Accuracy                  | Historical CodeBreaker table | Entry is marked prefix/suffix and rune compatible; this report did not re-decode the ordinary shield record byte-by-byte. |
| Isildur's prefix (Armor/Shield `0x46`) | +12 Defense                                     | Historical CodeBreaker table | Very efficient Defense source for a shield.                                                                               |
| of the Crow suffix (`0x43`)            | +10% Dodge                                      | Historical CodeBreaker table | Strong Dodge suffix target.                                                                                               |
| Elf Rune II - Nimblefoot             | +10% Dodge                                      | Historical CodeBreaker table | Tower Shield is marked rune compatible.                                                                                   |
| of Udun suffix (`0x94`)                | +7 Defense; +5% Dodge                           | Historical CodeBreaker table | Excellent hybrid breakpoint filler.                                                                                       |
| of the Towers suffix (`0x99`)          | +9 Defense; +1 Wisdom                           | Historical CodeBreaker table | High Defense density.                                                                                                     |
| Legendary cloak prefix (`0x20`)        | +4 Defense; +2 Courage                          | Historical CodeBreaker table | Clean Defense filler.                                                                                                     |
| Dragon's hand/foot prefix (`0x21`)     | +4 Strength; +4 Defense; +40% Treasure          | Historical CodeBreaker table | Clean +4 Defense source.                                                                                                  |
| Ethereal head/item prefix (`0x77`)     | +5 Accuracy; +7 Defense; -10% Experience        | Historical CodeBreaker table | Strong but has an XP tradeoff.                                                                                            |
| Peculiar head/item prefix (`0x95`)     | -27 Strength; +32 Defense; -50% Fear Resistance | Historical CodeBreaker table | Extreme Defense with severe penalties; not recommended for a normal endgame build.                                        |

## 11. Practical build: conservative defensive core

Without assuming that every affix combination necessarily appears as a natural random drop, the most conservative core is:

1. Tower Shield: 35% Shield Block.

2. Insert Nimblefoot Rune into the Tower Shield: +10% Dodge.

3. Keep Featherwood Flute in the backpack: +10% Shield Block +5% Dodge.

| This core alone provides 45% Shield Block + 15% Dodge, with the Tower Shield imposing -5 Accuracy. |
|----------------------------------------------------------------------------------------------------|

If final player Defense is already 290, this core yields 10% x 85% x 55% = 4.675% three-layer penetration, or about 95.325% three-layer avoidance against the standard 250-Accuracy Ringwraith.

## 12. Practical build: high-efficiency shield candidate

Within the historical guide's prefix + suffix + rune construction framework, a highly efficient target shield is: Isildur's Tower Shield of the Crow + Nimblefoot Rune, with Featherwood Flute in the backpack.

| **Component**      | **Defense** | **Dodge** | **Shield Block** | **Accuracy change** |
|--------------------|-------------|-----------|------------------|---------------------|
| Isildur's prefix   | 12          | 0         | 0                | 0                   |
| Tower Shield       | 0           | 0         | 35               | -5                  |
| of the Crow suffix | 0           | 10        | 0                | 0                   |
| Nimblefoot Rune    | 0           | 10        | 0                | 0                   |
| Featherwood Flute  | 0           | 5         | 10               | 0                   |
| Total              | 12          | 25        | 45               | -5                  |

Once other equipment brings final Defense to 290, this core corresponds to 10% x 75% x 55% = 4.125% penetration, or about 95.875% three-layer avoidance.

This is not a claim of guaranteed natural-drop availability. It is a target attribute construction supported by the historical compatibility table. Natural generator eligibility, minimum-level filters, and random affix weighting remain a separate verification layer.

## 13. If current Defense is only 250: how to reach 290

Going from 250 to 290 requires +40 Defense. Isildur's shield prefix supplies +12, leaving +28. The efficient approach is to distribute the remaining Defense across other slots, preferably using hybrid Defense+Dodge affixes, rather than accepting extreme penalties just to reach a round number.

| **Candidate source**      | **Defense** | **Additional effect / cost** | **Use**                             |
|---------------------------|-------------|------------------------------|-------------------------------------|
| of the Towers suffix      | 9           | +1 Wisdom                    | High-density Defense                |
| of Udun suffix            | 7           | +5% Dodge                    | Very high priority hybrid           |
| Ethereal head/item prefix | 7           | +5 Accuracy; -10% Experience | Strong with XP tradeoff             |
| Legendary cloak prefix    | 4           | +2 Courage                   | Clean filler                        |
| Dragon's hand/foot prefix | 4           | +4 Strength; +40% Treasure   | Clean filler                        |
| of the Crow suffix        | 0           | +10% Dodge                   | Prioritize after Defense breakpoint |

The practical formula is simple: Defense still needed = max(0, 290 - current final Defense). Once 290 is reached, any item that adds only Defense but no Armor, HP, Dodge, Block, or offense should be re-evaluated for opportunity cost.

## 14. Recommended target tiers

| **Tier**             | **Defense** | **Dodge** | **Block** | **Three-layer avoidance** | **Interpretation**                                                 |
|----------------------|-------------|-----------|-----------|---------------------------|--------------------------------------------------------------------|
| Practical core       | 290         | 15%       | 45%       | 95.325%                   | Tower Shield + Rune + Flute creates the later-layer core           |
| Efficient sweet spot | 290         | 25%-45%   | 45%       | 95.875% - 96.975%         | Add Dodge after the Defense breakpoint                             |
| Theoretical ceiling  | 290         | 75%       | 75%       | 99.375%                   | Code-layer ceiling only; not proof normal gear can reach both caps |

Given the currently located normal equipment sources, a realistic endgame shield target is roughly Defense 290 + Shield Block around 45% + Dodge 25%-55%, balanced against Accuracy, Armor, HP, and offense.

## 15. Characters or setups without a shield

If the character or weapon setup cannot satisfy the Shield Block condition, do not spend budget on paper Block. The model becomes P = H x (1-Dodge). At Defense 290, Dodge 50% yields 5% penetration (95% avoidance), while Dodge 75% yields 2.5% penetration (97.5% avoidance).

## 16. Code evidence index

| **Evidence**              | **Function / field**      | **Code-level conclusion**                                                                               |
|---------------------------|---------------------------|---------------------------------------------------------------------------------------------------------|
| Standard hit check        | `FUN_08011ac0`              | Accuracy + 50 - Defense; clamp to 10-95; random 1-100 compared to threshold.                            |
| Percentage helper         | `FUN_08032a5c`              | Computes (value x percent) / 100.                                                                       |
| Enemy Defense             | `FUN_0801113c`              | Reads base Defense, applies difficulty percentage, then +25% Eye enhancement.                           |
| Enemy Accuracy            | `FUN_080111e0`              | Reads base Accuracy, applies difficulty percentage, then +25% Eye enhancement.                          |
| Full-Eye spawn            | `FUN_080354fc`              | Spawns enemy ID `0x3A`.                                                                                   |
| Dodge / Block probability | `FUN_08011b4c`              | Positive-value chance check; values above 75 are clamped to 75.                                         |
| Enemy attack chain        | Relevant combat routine   | After standard hit, checks `0x2C` Dodge; if shield condition is satisfied, then checks `0x2E` Shield Block. |
| Player Accuracy RAM       | `0x03003CC8`                | 16-bit Accuracy field.                                                                                  |
| Player Defense RAM        | `0x03003CCC`                | 16-bit Defense field.                                                                                   |
| Featherwood Flute         | Type-E `0x19E` / `0x08064CB0` | `0x2E` +10 Shield Block; `0x2C` +5 Dodge.                                                                   |

## 17. Final conclusions

1. The standard Grueling + fully open-Eye Ringwraith should be modeled as Accuracy 250 / Defense 250.

2. Player Accuracy 295 reaches the 95% standard-hit hard cap against this target; standard 100% hit is impossible through Accuracy alone.

3. Player Defense 290 reduces the Ringwraith to the 10% standard-hit hard floor; 300 and 350 are identical to 290 in this layer.

4. After Defense reaches 290, survivability gains should come from independent layers such as Dodge, Shield Block, Armor, and HP.

5. Dodge and Shield Block are sequential multiplicative checks, not additive percentages; with equal point cost there is no mathematical reason to force an even split.

6. The code caps each Dodge/Block check at 75%, but the currently located normal equipment sources only establish a practical Shield Block core of at least 45% (Tower Shield 35% + Featherwood Flute 10%). The code cap must not be confused with a proven normal-gear cap.

7. A realistic shield-build target is approximately Defense 290 + Shield Block around 45% + Dodge 25%-55%, then balance remaining slots around Accuracy, Armor, HP, and offense.

## 18. Sources and limitations

- User-supplied retail BLRE v1.0 decompilation and enemy data.

- The uploaded earlier full-Eye Ringwraith Accuracy/Defense English report; this revision normalizes the difficulty terminology to Grueling and integrates the later defensive-layer and build research.

- User-supplied historical CodeBreaker Guide for Tower Shield, prefix, suffix, rune values, and compatibility tags.

- The project backpack Unique ROM audit for Featherwood Flute actual +10% Shield Block and hidden +5% Dodge.

- This report does not claim that every code-valid prefix/suffix combination has been independently proven as a natural random drop. Converting target builds into a natural-drop endgame checklist requires a separate generator eligibility and weighting audit.
