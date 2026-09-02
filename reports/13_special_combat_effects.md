# GBA The Lord of the Rings: The Return of the King

## Code-Level Audit of Nine Special Combat Effects

Sunburst, Nightburst, Venomous, Fireburst, Immobilize, Knockback, Meditative, Armor Piercing, Backstab

| **Build**             | Retail English BLRE v1.0 (Version 00)                                                |
|-----------------------|--------------------------------------------------------------------------------------|
| **Research date**     | 2026-09-01                                                                           |
| **Evidence baseline** | Retail ROM effect records + corrected decompilation + instruction-level verification |
| **ROM SHA-256**       | b5f556593c5fbe3531a4354b047f0bed364a124ceee43f278a48f4d26b0ffa9c                     |

## 1. Executive summary

**Final code-derived results**

- Sunburst: one 10% roll after a qualifying kill; on success, applies state `0x1A` for 120 ticks to eligible living targets within radius threshold `0x280000`. It deals no direct HP damage.
- Nightburst: one 10% roll after a qualifying kill; on success, every eligible living target within `0x280000` directly loses 7 HP, bypassing the normal armor subtraction path.
- Venomous: adds a flat +5 damage after armor/minimum-damage resolution on every applicable hit. On the character-target path it also installs a base 90-tick poison timer, immediately deals 2 HP, then deals 2 HP at 32-tick timer boundaries.
- Fireburst: flat +4 damage on every applicable hit after armor; no dedicated burn-over-time timer exists for effect `0x38`.
- Immobilize: exactly 24% on an eligible hit when Knockback is absent, lasting 90 ticks. Targets in state `0x1A` take doubled base attack damage on subsequent hits before armor.
- Knockback: not RNG-based in the main `0x30` path. A surviving target is put into state `0x25` with a timer equal to the stored Knockback value; confirmed values include 10, 12, 15, and 20.
- Meditative: Elf Rune IV, effect `0x3E`. Heals exactly 2 HP every 30 consecutive eligible update ticks; no RNG. Nominally about 4 HP/s at ~60 Hz.
- Armor Piercing: does not set armor to zero. It uses integer armor / 3, effectively bypassing about two thirds of armor.
- Backstab: no fixed damage multiplier. When the rear-facing test succeeds, effective armor is set to zero.

**Hidden interaction:** Immobilize and Sunburst both use target state `0x1A`. Both main damage functions double base damage when the target is already in state `0x1A`, before armor is processed. The triggering hit itself is resolved before Immobilize is applied, so the doubling affects subsequent hits.

## 2. Master results table

| **Effect**     | **Effect ID** | **Runtime field** | **Stored form**  | **Trigger**                       | **Exact runtime behavior**                                                              | **Grade** |
|----------------|---------------|-------------------|------------------|-----------------------------------|-----------------------------------------------------------------------------------------|-----------|
| Sunburst       | `0x35`          | `0x03003D0E`        | 1 (flag)         | 10% after kill                    | 120 ticks; radius `0x280000`; AoE state `0x1A`; 0 direct damage                             | A         |
| Nightburst     | `0x36`          | `0x03003D10`        | 1 (flag)         | 10% after kill                    | Radius `0x280000`; direct -7 HP per target; no armor subtraction                          | A         |
| Venomous       | `0x3D`          | `0x03003D1E`        | 1 (flag)         | Every applicable hit              | Flat +5; character-target poison: 90 ticks, immediate -2, then -2 at 32-tick boundaries | A / A-    |
| Fireburst      | `0x38`          | `0x03003D14`        | 1 (flag)         | Every applicable hit              | Flat +4; no Fireburst-specific DOT                                                      | A         |
| Immobilize     | `0x31`          | `0x03003D06`        | 1 (flag)         | 24% if no Knockback               | 90-tick state `0x1A`; subsequent base damage x2                                           | A         |
| Knockback      | `0x30`          | `0x03003D04`        | 10/12/15/20 etc. | Surviving hit; no RNG             | State `0x25`; timer=stored value; movement speed arg `0x20000`                              | A-        |
| Meditative     | `0x3E`          | `0x03003D20`        | 1 (flag)         | 30 consecutive ticks in state 1/5 | +2 HP every 30 ticks; counter resets outside eligible states                            | A         |
| Armor Piercing | `0x32`          | `0x03003D08`        | 1 (flag)         | Every damage calculation          | effective armor = integer armor / 3                                                     | A         |
| Backstab       | `0x39`          | `0x03003D16`        | 1 (flag)         | Rear-facing condition             | effective armor = 0; no fixed multiplier                                                | A         |

## 3. Detailed code audit

### 3.1 Sunburst

**ROM record:** Starfall Bow at `0x08064BE4` contains effect `0x35` = +1.

**Trigger:** The main attack resolver calls `FUN_080116E8` only from the branch entered after target HP falls below 1.

**Probability:** `FUN_0803A4BC`(1,100) is inclusive. The condition is < `0x0B`, therefore values 1..10 succeed: exactly 10%.

**Area and duration:** `FUN_08001FC8` scans with threshold `0x280000`. Eligible living targets are assigned state `0x1A` and timer `0x78` = 120 ticks.

**Damage:** No HP subtraction occurs in the Sunburst routine itself.

**Combat consequence:** A target already in state `0x1A` has its base incoming attack damage doubled before armor in both main damage functions.

`FUN_080116E8`: RNG(1,100) < 11 -> 10%

`FUN_08001FC8`(target, ..., `0x280000`)

state = `0x1A`; timer = `0x78` (120)

### 3.2 Nightburst

**ROM record:** Nightfell Bow at `0x08064BF0` contains effect `0x36` = +1.

**Trigger and probability:** `FUN_080117E8` is called after a qualifying kill and performs one inclusive 1..100 roll; <11 is exactly 10%.

**Area:** Same proximity threshold `0x280000` as Sunburst.

**Damage:** Each qualifying living target in the scan is modified by HP -= 7. The routine does not read armor or call the normal armor calculation.

**Chaining:** The routine directly updates HP/death state and does not call Sunburst/Nightburst kill callbacks again; no recursive burst chain is visible in this function.

`FUN_080117E8`: RNG(1,100) < 11 -> 10%

target_HP = target_HP - 7

### 3.3 Venomous

**ROM record:** Weapon-prefix records use effect `0x3D` = +1; for example `0x080657F0` contains both `0x31`=1 and `0x3D`=1.

**Immediate damage:** `FUN_08010AD4` and `FUN_08010EDC` both add 5 after normal armor subtraction and the minimum-one-damage clamp. This +5 is not reduced by armor.

**Ordinary-enemy path:** `FUN_08010AD4` adds the +5 and poison audiovisual effect, but the effect-`0x3D` consumers do not install the 90-tick player-style poison timer on ordinary enemy structures.

**Character-target path:** `FUN_08010EDC` calls `FUN_08010D90`. It sets poison duration D = 90 - floor(90 * PoisonResistance / 100), sets the poison status flag, then immediately applies -2 HP.

**Periodic damage:** `FUN_08026370` decrements the poison timer each update. If the new timer is still positive and divisible by 32, another -2 HP is applied. Later DOT tick count is floor((D-1)/32).

**Zero resistance:** D=90 gives -2 immediately, then -2 at 64 and 32: 6 status HP total. Including the flat +5 hit bonus, a full character-target poison cycle contributes 11 extra HP loss over the otherwise identical hit.

**Resistance behavior:** Poison Resistance shortens the timer. It does not reduce the flat +5 in these functions, and the application-time -2 is executed after the duration adjustment without a separate resistance gate.

**Later multiplier order:** Both main damage functions check a separate `0x4000` x3 damage branch only after Fireburst/Venomous bonuses have been appended. If that independent branch is active, the current total including Venomous +5 is multiplied by 3. The x3 is not part of Venomous itself.

direct bonus: final_damage += 5

D = 90 - floor(90 * PoisonResistance / 100)

on application: HP -= 2

while active: if remaining_timer % 32 == 0, HP -= 2

### 3.4 Fireburst

**ROM record:** Fire Arrows at `0x08063D04` contain effect `0x38` = +1; Rune of Fire uses the same effect ID.

**Exact value:** Both main damage functions first compute max(1, raw damage - effective armor), then add exactly 4 when Fireburst is active.

**Probability:** No RNG is used for effect `0x38` in these damage consumers.

**Duration:** No dedicated Fireburst status timer or DOT consumer was found for runtime field `0x03003D14`. The effect is immediate +4 plus audiovisual feedback.

**Separation from Fire Damage:** A separate Fire Damage effect can coexist on the same item. Fire Arrows, for example, carry +3 Fire Damage and Fireburst as distinct records.

**Later multiplier order:** After Fireburst adds +4, the code checks a separate `0x4000` x3 damage branch. If that independent branch is active, the current total including the +4 is multiplied by 3. This x3 is not a Fireburst multiplier.

damage = max(1, raw_damage - effective_armor)

if Fireburst != 0: damage += 4

### 3.5 Immobilize

**ROM record:** Effect `0x31` is a +1 flag and appears on prefixes such as Venomous and Cold.

**Priority:** The main hit resolver checks Knockback first. Immobilize is only tested when Knockback is zero, the target survives, and the control eligibility check succeeds.

**Exact probability:** The roll is RNG(1,100) followed by < `0x19` (25). Since the RNG is inclusive, 1..24 succeed: exactly 24%, not 25%.

**Duration:** On success, target state is `0x1A` and timer is `0x5A` = 90 ticks.

**Damage interaction:** Subsequent attacks see state `0x1A` and double base damage before armor. Fireburst +4 and Venomous +5 are appended after armor and are not part of that state-based doubling.

if Knockback == 0 and target_alive and eligible:

if RNG(1,100) < 25: \# 1..24 -> 24%

state = `0x1A`; timer = 90

### 3.6 Knockback

**Runtime field:** Effect ID `0x30` is numeric, not merely boolean. Orcrist stores 10; confirmed records also store 12, 15, and 20.

**Proc behavior:** The main `0x30` branch contains no RNG. When the target survives and the stored field is nonzero, `FUN_08009AAC` is called directly.

**State mechanics:** `FUN_08009AAC` sets target state `0x25`, copies the low 8 bits of the stored value into the state timer at +`0x50`, and initializes movement away from the attacker with speed argument `0x20000`.

**Confirmed magnitudes:** 10 is common; Fierce stores 12; Savage and some heavy weapons store 15; Maul stores 20. Higher values keep the knockback state active for more update ticks; collision and target movement logic determine final displacement.

**Interaction with Immobilize:** A nonzero Knockback field takes the branch that bypasses the Immobilize roll in this resolver.

**Terminology warning:** Morgul Rune II has a separate historical “30% push foe back” field (effect `0x25` = 30). It is not the `0x30` Knockback runtime field audited here.

`FUN_08009AAC`: state = `0x25`

timer = KnockbackValue & `0xFF`

movement vector speed argument = `0x20000`

| Grade A- reflects only the lack of a single universal pixel-distance conversion across collision/body types. The stored values and the deterministic `0x30` branch itself are code-confirmed. |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

### 3.7 Meditative

**ROM record:** Rune table base is `0x080691A8`. Elf Rune IV, index 7, is at `0x080691C4` with raw bytes 3E 01 5A 00: effect ID `0x3E`, value +1.

**Runtime field:** The generic effect writer maps `0x3E` to `0x03003D20` for the current character (stride `0x1AC`).

**Timer logic:** `FUN_0802536A` decrements a dedicated counter only while the current action state is 1 or 5. At expiry it resets the counter to `0x1E` = 30. If Meditative > 0, current HP is below max HP, and HP is nonnegative, healing is called. Leaving states 1/5 resets the counter to 30.

**Exact heal amount:** The decompiler lost call arguments at this site, but retail Thumb instructions at `0x08025658`..`0x08025660` are MOV R0,R10; MOVS R1,#2; MOVS R2,#0; BL `FUN_08026148`. Therefore the heal is exactly +2 HP per proc.

**Rate:** Exactly +2 HP per 30 consecutive eligible update ticks. At nominal ~60 Hz this is about +2 every 0.5 s, or ~4 HP/s. The authoritative code unit remains 30 ticks.

**Probability:** No RNG is involved.

ROM `0x080691C4`: 3E 01 5A 00

`0x08025658`: MOV R0,R10

`0x0802565A`: MOVS R1,#2

`0x0802565C`: MOVS R2,#0

`0x0802565E`: BL `FUN_08026148`

### 3.8 Armor Piercing

**ROM record:** Effect ID `0x32` is a +1 flag; Bodkin Arrows at `0x08063CE0` contain `0x32`=1.

**Exact formula:** If Backstab does not succeed, the relevant melee/missile armor value is selected. When Armor Piercing is active, `FUN_0804E0CC`(armor,3) is called. For nonnegative armor, this is integer division by 3.

**Meaning:** effective armor = floor(original armor / 3). Roughly 66.7% of armor is bypassed, while roughly one third remains.

**Examples:** Armor 10 -> 3; 11 -> 3; 12 -> 4.

**Backstab interaction:** When Backstab succeeds, armor is set directly to zero and the Armor Piercing division branch is skipped.

if not Backstab:

armor = selected_armor

if ArmorPiercing: armor = armor / 3

else: armor = 0

### 3.9 Backstab

**ROM record:** Effect ID `0x39` is a +1 flag; Assassin's prefix at `0x08065784` contains `0x39`=1.

**Facing test:** `FUN_08011A88` reads 0..7 facing indices for attacker and target. It succeeds when target facing equals attacker facing or either adjacent index, with 0/7 wraparound.

**Exact effect:** On success, the damage function sets effective armor to 0. There is no standalone +50%, x2, or other Backstab damage multiplier.

**Practical gain:** The extra damage equals the armor that would otherwise have been subtracted. Backstab therefore gains more against high-armor targets and little against targets already at zero armor.

**Immobilize synergy:** If the target is also already in state `0x1A`, base damage is doubled first, then Backstab sets armor to zero.

target_facing in {attacker_facing-1, attacker_facing, attacker_facing+1} (mod 8)

if Backstab and facing_test: effective_armor = 0

## 4. Damage-resolution order and interactions

| **Order** | **Runtime operation**                                                                                                                                | **Relevant effects**                                |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------|
| 1         | Generate base weapon/skill damage and applicable direct bonuses.                                                                                     | —                                                   |
| 2         | If target state == `0x1A`, multiply base damage by 2.                                                                                                  | Immobilize, Sunburst                                |
| 3         | If Backstab succeeds: armor=0. Otherwise select armor; if Armor Piercing, armor/=3.                                                                  | Backstab, Armor Piercing                            |
| 4         | damage = max(1, base damage - effective armor).                                                                                                      | —                                                   |
| 5         | If Fireburst: damage += 4.                                                                                                                           | Fireburst                                           |
| 6         | If Venomous: damage += 5; character-target path may also install poison status.                                                                      | Venomous                                            |
| 7         | If independent attack-state flag `0x4000` is active, multiply the current total damage by 3; Fireburst +4 / Venomous +5 are already inside that total. | Independent x3 branch (not one of the nine effects) |
| 8         | Apply HP damage. If target survives, Knockback is checked first; only without Knockback can Immobilize roll.                                         | Knockback, Immobilize                               |
| 9         | If the target is killed, kill-trigger passives are checked; Sunburst and Nightburst each use their own 10% roll if present.                          | Sunburst, Nightburst                                |

## 5. Legacy-description corrections

- “Armor Piercing ignores armor” is not literally true in the retail code; it divides armor by 3.

- “Immobilize is about 25%” resolves to exactly 24% because the RNG is 1..100 inclusive and the condition is <25.

- Sunburst and Nightburst are kill-triggered in the main attack resolver, not ordinary per-hit procs.

- Fireburst is an immediate +4 in its direct consumers, not a dedicated burning DOT.

- The `0x30` Knockback path is deterministic when reached; a separate historical “30% push foe back” field should not be conflated with it.

## 6. Representative raw ROM records

| **Mechanic**          | **Representative source** | **ROM address** | **Raw bytes**                       |
|-----------------------|---------------------------|-----------------|-------------------------------------|
| Knockback             | Orcrist                   | `0x08064BA8`      | EF 02 09 00 11 30 0A 0A 08 5A 00 00 |
| Sunburst              | Starfall Bow              | `0x08064BE4`      | F4 02 09 01 14 35 01 13 03 11 05 00 |
| Nightburst            | Nightfell Bow             | `0x08064BF0`      | F5 02 09 01 14 36 01 12 05 14 03 00 |
| Armor Piercing        | Bodkin Arrows             | `0x08063CE0`      | A1 02 03 32 01 5A 00 5A 00 3C 01 00 |
| Fireburst             | Fire Arrows               | `0x08063D04`      | A4 02 07 02 03 38 01 5A 00 5A 00 00 |
| Backstab              | Assassin's prefix         | `0x08065784`      | 33 03 02 23 05 39 01 5A 00 5A 00 00 |
| Immobilize + Venomous | Venomous prefix           | `0x080657F0`      | 16 05 03 31 01 3D 01 5A 00 5A 00 00 |
| Meditative            | Elf Rune IV               | `0x080691C4`      | 3E 01 5A 00                         |

## 7. Evidence grades and boundaries

**Evidence grading**

- A: the retail ROM record, runtime field, and actual consumer function directly close the loop on the numeric value and core behavior.
- A-: the core mechanism and internal value are code-confirmed, but a universal pixel-distance conversion for Knockback or extension of the character-target poison timer to every ordinary PvE enemy is not supported by the current code path.
- Any duration in seconds is a nominal ~60 Hz conversion. The primary evidence unit is the engine update tick: 30, 90, or 120.
- The Venomous audit deliberately separates ordinary-enemy and character-target paths instead of assuming identical poison-status storage across different entity structures.

## 8. Final conclusions

1.  All nine mechanics have concrete locations in the BLRE v1.0 retail effect tables and runtime code. Legacy guides often name the effect without exposing its true probability, timer, formula, or processing order.

2.  The highest-impact corrections are: Sunburst/Nightburst = 10% after kill; Immobilize = 24% for 90 ticks; Armor Piercing = armor/3; Backstab = armor zero; Fireburst = +4 per hit with no dedicated DOT; Meditative = +2 HP every 30 eligible ticks.

3.  State `0x1A` is the hidden bridge between control and damage: both Immobilize and Sunburst can put targets into it, and subsequent base attack damage is doubled before armor.

4.  Knockback effect `0x30` is a numeric deterministic movement-state parameter, not the same field as the separate historical 30% push-back rune effect.

5.  Venomous is the most path-dependent mechanic: +5 immediate damage is stable, while the 90-tick poison status and periodic -2 HP ticks are explicitly implemented on the character-target path.

## 9. Supplementary code audit: Cursed stacks and on-kill healing

**Scope of the supplemental material:** The 16 user-supplied screenshots mainly discuss Cursed stacks, enemy criticals, extra damage against Ringwraith-class enemies, and percent HP restored per kill. The screenshots are treated as claims to verify; retail BLRE v1.0 code cross-references remain the controlling evidence.

### 9.1 Cursed runtime field and stack semantics

**Effect mapping:** The generic effect writer `FUN_08042F48` accumulates ordinary effect IDs at `0x03003CA4` + effect_id*2. Cursed is effect `0x37`, therefore its exact runtime field is `0x03003D12`.

**Why stacks exist:** `FUN_08042F48` performs field = field + value rather than boolean replacement. Multiple Cursed prefixes, suffixes, or equipped items can therefore accumulate. A typical Cursed record contributes +1, so the field value naturally becomes the stack count.

**Does critical chance keep scaling with stacks?** No. `FUN_0800FCE0` only tests whether `0x03003D12` is zero. The threshold is 10 with zero Cursed and 20 with any nonzero Cursed value. Two or more stacks do not raise it to 30%, 40%, and so on.

effect `0x37` -> `0x03003CA4` + `0x37`*2 = `0x03003D12`

Cursed == 0 : enemy critical threshold = 10

Cursed != 0 : enemy critical threshold = 20

### 9.2 Enemy criticals: 10% -> 20%, with base damage x2

**Exact probability:** `FUN_08011B00` rolls `FUN_0803A4BC`(1,100) and tests random_value <= threshold. Threshold 10 is therefore exactly 10%, and threshold 20 is exactly 20%. This differs from Immobilize, whose condition is <25 and therefore resolves to 24%.

**Normal damage spread:** The enemy attack path first obtains base damage. On a non-critical result it randomizes from base/2 through base, so ordinary hits already have an approximately 50%-100% base-damage spread.

**Critical damage:** The critical branch directly performs base_damage <<= 1, i.e. base damage x2, before later race/equipment modifiers and downstream damage processing.

**Practical risk:** The first Cursed stack doubles enemy critical frequency from 10% to 20%, but it does not add a fixed amount to every ordinary enemy hit. Perceived one-shot risk comes from higher critical frequency layered on top of the pre-existing 50%-100% normal-hit spread.

if not critical: damage = RNG(base/2, base)

if critical: damage = base * 2

### 9.3 +15 per stack against category 6 (mapped by the project as Nazgul/Ringwraith-class)

**Code condition:** After the critical/non-critical base value is formed, `FUN_0800FCE0` reads Cursed again. If the attacker enemy-category table byte equals 6, it performs damage += CursedStacks * 15.

**Exact stack scaling:** 0 stacks +0; 1 stack +15; 2 stacks +30; 3 stacks +45, and so on. This part scales linearly with stack count, unlike the binary 10%-to-20% critical threshold.

**Resolution-order warning:** The +15 per stack is inserted into the damage variable before downstream normal damage processing. The code therefore proves a +15-per-stack damage-input increment; it should not be stated as an unconditional +15 final HP loss under every defense state.

**Critical ordering:** The Cursed +15-per-stack addition occurs after the base x2 critical branch, so that +15 component is not doubled again by this particular enemy-critical branch.

**Semantic boundary:** The machine code directly proves category value 6. The name mapping “category 6 = Nazgul/Ringwraith-class” comes from the project enemy-category mapping and historical material, so the semantic label is graded A-, while the category-6 +15*stacks behavior is grade A.

if CursedStacks != 0 and enemy_category == 6:

damage += CursedStacks * 15

### 9.4 What Cursed does not automatically do

| **Question**                             | **Finding**            | **Code-level explanation**                                                                                                                     |
|------------------------------------------|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| Enemy critical chance                    | Yes                    | 0 stacks: 10%; >=1 stack: 20%; no further stacking                                                                                            |
| Damage input vs. category 6              | Yes                    | +15 per stack, linearly stacked                                                                                                                |
| Treasure/drop rate                       | No code evidence       | `0x03003D12` cross-references do not enter drop/chest generation                                                                                 |
| Corruption Resistance                    | No                     | Cursed `0x37` and Corruption Resistance `0x28` are separate effects                                                                                |
| Fear/Poison resistance or armor          | No automatic reduction | Each is represented by its own independent effect/runtime field                                                                                |
| Equipment removal lock                   | No code evidence       | Cursed is not referenced by removal-rejection logic; ordinary recalculation removes unequipped contributions                                   |
| Ordinary gear remains active in backpack | Normally no            | Normal equipment is aggregated from equipped slots; only item types/metadata explicitly allowing backpack effects can contribute from backpack |

**Cross-reference finding:** In the corrected decompilation, active reads of `0x03003D12` are concentrated in enemy attack resolution: the critical-threshold check, the category-6 test, and the +15*stacks addition. This is strong negative evidence against direct Cursed effects on loot or Corruption Resistance.

### 9.5 Exact formula for percent HP restored per kill

**Percentage field:** Effect `0x1B` maps to `0x03003CDA`. It is additively aggregated by `FUN_08042F48`, so multiple “X% HP per slain enemy” sources combine into a total percentage before the kill calculation.

**Trigger point:** In `FUN_0800F168`, local_30 = `FUN_08010AD4`(...) becomes the computed damage for the hit, local_30 is subtracted from target HP, and the on-kill heal executes only after target HP falls below 1.

**Exact arithmetic:** `FUN_08032A5C`(local_30, percent) invokes the GBA Div SWI on local_30 * percent / 100. For positive values this is integer truncation. If the quotient is zero, the code forces the heal to 1.

**Max-HP cap:** `FUN_08026148` adds the heal to current HP and clamps the result to the character maximum HP.

**Ordinary overkill:** On the ordinary kill path the full computed local_30 is used, not the victim’s pre-hit remaining HP. A target at 3 HP killed by a final 40-damage hit therefore yields 4 HP from a 10% modifier.

**Important exception:** `FUN_0800F168` also contains a separate instant-kill branch that first replaces local_30 with the target’s current HP before subtraction. A kill produced by that branch therefore bases percent healing on the replaced current-HP value rather than the previously computed larger damage.

**Flat on-kill healing stacks separately:** After the percentage heal, the code independently checks `0x03003CD6` (effect `0x19`), a fixed HP-per-kill field, and calls `FUN_08026148` again. Percent on-kill healing and flat on-kill healing are therefore separate paths that can both apply.

**Same-kill interaction with Sunburst/Nightburst:** Within the kill branch, healing is processed before the `0x03003D0E` Sunburst and `0x03003D10` Nightburst checks. A single kill can therefore heal first and then independently roll Sunburst and/or Nightburst; those 10% checks do not replace the heal.

heal_percent = max(1, floor(killing_hit_damage * total_percent / 100))

current_HP = min(max_HP, current_HP + heal_percent)

then, if flat_HP_per_kill != 0: heal(flat_HP_per_kill)

### 9.6 Final disposition of the supplemental claims

- The supplemental claim that the first Cursed stack raises enemy critical chance from 10% to 20%, while further stacks do not raise critical chance again, matches the retail code.

- The “+15 per Cursed stack against category 6” claim matches the code, with one precision correction: it is a +15-per-stack increment to the damage variable before downstream processing, not an unconditional final-HP delta.

- Cursed is not Corruption Resistance, and the current cross-references do not support claims that it directly lowers treasure rate, automatically reduces unrelated defenses, or prevents equipment removal.

- “Restore 10% HP per kill” is not 10% of maximum HP. On the ordinary kill path it is 10% of the killing hit’s computed final damage, integer-truncated with a minimum of 1 and capped by max HP; the explicit instant-kill branch is an exception.

- These supplemental findings do not alter the nine-effect conclusions, but they explain why Cursed equipment can sharply increase practical combat risk and why on-kill healing can look unintuitive relative to the text description.

## Sources and reproducibility

- User-supplied retail ROM: RETURN KING / BLRE / Version 00; 16,777,216 bytes; SHA-256: b5f556593c5fbe3531a4354b047f0bed364a124ceee43f278a48f4d26b0ffa9c

- Corrected decompilation document for LOTR ROTK GBA: primary source for function flow and RAM fields.

- Historical English CodeBreaker guide: used to map names and legacy descriptions; it does not override machine-code evidence.

- All probabilities are recomputed from the inclusive RNG behavior of `FUN_0803A4BC`. Durations are reported primarily in engine update ticks; seconds are only nominal ~60 Hz approximations.

Final research report · 2026-09-01 · BLRE v1.0
