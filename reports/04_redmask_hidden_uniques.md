# GBA The Lord of the Rings: The Return of the King

## Research Dossier on the 14 Hidden Red Backpack Uniques, RedMask Qualification Deadlock, and Natural-Drop Validation

*Based on ROM decompilation, exact RNG mapping analysis, raw SAV audits, and gameplay observations*

*Revised edition: 2026-08-17*

*New evidence: natural 15E Woven Goblin Doll drop at 13:06 on 2026-08-16 and audit of the resulting SAV*

- Core conclusion / RedMask=0xDBFF opens the legal qualification bits for all 14 target red backpack uniques; it does not increase drop rates. The natural 15E drop recorded at 13:06 on 2026-08-16, together with the post-drop SAV showing DBFF, a clean FE 15 FF FF item instance, and the standard 278-gem sale value, now forms an end-to-end chain from static code to persistent state to real gameplay output.

> **Current project note:** Older wording about an “8-byte reversal” should be treated only as a dump-representation note. It is not part of the RedMask mechanism. The persistent result that matters is the character-save field at +0x56 and the verified 0xDBFF target qualification state.

## 1. Scope and evidence discipline

This report covers the 14 hidden red backpack unique items in the GBA version of The Lord of the Rings: The Return of the King. Function names are decompiler labels, not official symbols. Conclusions are separated into code-proven, ROM-byte-proven, SAV-proven, dynamic observation, and high-confidence inference.

- Evidence class = Code-proven; Meaning = Directly supported by decompiled control flow or function behavior

- Evidence class = ROM-byte-proven; Meaning = Directly verified from ROM tables/bytes

- Evidence class = SAV-proven; Meaning = Directly parsed from raw 512-byte EEPROM saves

- Evidence class = Dynamic observation; Meaning = Observed during actual gameplay

- Evidence class = High-confidence inference; Meaning = Supported by converging evidence, but not stated by a single instruction

- Evidence boundary / The normal generation and pickup paths provide strong evidence for a first-item qualification deadlock, but this report does not claim that every obscure event in the entire ROM has been mathematically exhausted. Likewise, one natural red drop proves that the dynamic path is real, but it does not by itself prove perfectly equal frequencies across all 14 IDs.

## 2. Dynamic milestone: 13:06 on 2026-08-16

Research log: at 13:06 on 2026-08-16, under the English original game, original drop-rate conditions, and a persistent DBFF save, the player naturally obtained item 15E, Woven Goblin Doll, in Helm's Deep Act 2 while using normal corpse-hitting gameplay. The item later displayed a sale value of 278 gems.

- Field = Time; Recorded result = 2026-08-16 13:06

- Field = Location / method; Recorded result = Helm's Deep, Act 2; corpse hitting

- Field = Target item; Recorded result = 15E Woven Goblin Doll

- Field = Drop method; Recorded result = Normal random gameplay drop; no boosted drop rate

- Field = Observed sale value; Recorded result = 278 gems

- Field = Research significance; Recorded result = Connects DBFF static qualification analysis to a real original-rate drop

- What this result proves / It proves that at least one real execution path can move from a DBFF-qualified character into the type-E target-red branch and produce a legal 15E under original-rate conditions. Combined with the shared code path for the 14 target candidates and the fully enabled DBFF qualification mask, the 14 target IDs are supported as non-zero-probability reachable outcomes. Reachable does not mean guaranteed within any finite farming session.

## 3. Overall drop architecture

Monster death drops and at least one class of destructible treasure objects converge on the same top-level generator, FUN_0803a77c. A common monster-side entry is FUN_0803a6e8; the destructible-object routine FUN_08014a3c also calls FUN_0803a77c.

FUN_0803a6e8  ->  FUN_0803a77c
FUN_08014a3c  ->  FUN_0803a77c

FUN_0803a77c:
    iVar1 < 0x2E      -> FUN_0803adf8
    iVar1 < 0x47      -> FUN_0803ae48
    iVar1 <= 100      -> FUN_0803ae8c

The more accurate model is therefore a unified top-level drop generator with several outcome branches, not two completely independent 'monster equipment' and 'monster item' pools plus a separate container-only global mixed table. Source-specific parameters and tables can still alter the chance of reaching each branch.

## 4. Code path for the target red uniques

## 4.1 FUN_0803ae8c: selecting the item type

FUN_0803ae8c calls FUN_0803a4bc(1,100) and walks the character-class threshold row in DAT_0806dbac to select type 0 through E. In the class rows verified in this study, type E occupies the top result interval. The code also contains a special case where param_4 == 'S' remaps a would-be type E result to type 8, so probability statements must state their conditions.

iVar2 = FUN_0803a4bc(1,100);
...
bVar6 = (byte)uVar7;
if ((param_4 == 'S') && (uVar7 == 0xE)) {
    bVar6 = 8;
}
*param_1 = (*param_1 & 0xF0) | (bVar6 & 0x0F);
FUN_0803a844(param_1,param_2);

## 4.2 FUN_0803a844: type-E candidate, rare audit, and RedMask

When type E is selected, FUN_0803a844 sets the candidate maximum to 0x34, so the base candidate ID is drawn from 0x00 through 0x34 inclusive: 53 candidates. For candidates 0x0A through 0x19, the function performs FUN_0803a4bc(1,10). The current E candidate is retained only when the result is 10 and either the ID is one of the two special cases or the corresponding RedMask qualification bit is already set.

if (type == 0xE) {
    candidate = FUN_0803a4bc(0,0x34);
    ...
    if (0x0A <= candidate && candidate < 0x1A) {
        audit = FUN_0803a4bc(1,10);
        if (audit == 10 &&
            (candidate == 0x14 || candidate == 0x17 ||
             RedMask[metadata - 0x47] != 0)) {
            return current_E_item;
        }
    }
}

14E Orc Drum and 17E Orc Head are special cases and are not part of the 14 target hidden red backpack uniques.

## 5. RedMask and the first-item qualification deadlock

The generation path reads the character-specific DAT_03003d8a field. The pickup path for a qualifying unique item later ORs the corresponding bit into that same field. If a new character starts with the relevant bit clear and no independent event opens it, this creates a circular dependency: generation requires the bit before the normal pickup action can set it.

Generation:
RedMask >> (metadata - 0x47) & 1

Pickup:
RedMask |= 1 << (metadata - 0x47)

The bit index is syntactically derived from an item metadata byte, metadata-0x47, rather than directly from item ID. The verified metadata sequence for this 16-entry group aligns the two zero bits in DBFF with the two special 14E/17E positions.

- RedMask = 0x0000; 16-bit binary = 0000 0000 0000 0000; Meaning for target group = All 14 target qualification bits closed; Raises drop rate? = No

- RedMask = 0x0001; 16-bit binary = 0000 0000 0000 0001; Meaning for target group = Only the lowest target qualification is open; Raises drop rate? = No

- RedMask = 0xDBFF; 16-bit binary = 1101 1011 1111 1111; Meaning for target group = All 14 target qualifications open; bit 10 and bit 13 remain clear for the special positions; Raises drop rate? = No

## 6. Persistent SAV storage: +0x56 and checksum

Each character save record is 0x70 bytes. FUN_0803c294 loads the 16-bit value at record offset +0x56 into runtime DAT_03003d8a, and FUN_0803c6cc writes runtime DAT_03003d8a back to +0x56.

Load:
*(ushort *)(&DAT_03003d8a + character*0x1AC)
    = *(ushort *)(save_record + 0x56);

Save:
*(ushort *)(save_record + 0x56)
    = *(ushort *)(&DAT_03003d8a + character*0x1AC);

FUN_0803cdb4 sums the full 0x70-byte record as 16-bit halfwords. A valid stored record has a total sum of zero.

## 7. Binary audit of the natural 15E save

A read-only audit was performed on the file named '8 treasures, including rare backpack item Woven Goblin Doll, English ROTK.sav'.

- Audit item = File size; Result = 512-byte EEPROM SAV

- Audit item = SHA-256; Result = cf005f8fc1b82803454da428301db45f31d30e39f8dfd8672b772a3e12b28d30

- Audit item = Header; Result = ROTKGBA6 after reversing each stored 8-byte block

- Audit item = Character-record checksums; Result = All 4 of 4 0x70-byte records sum to zero

- Audit item = Character RedMask values; Result = All 4 of 4 are 0xDBFF

- Audit item = Complete 15E item instance; Result = FE 15 FF FF

- Audit item = Location; Result = Character record 4, backpack slot 6

- Canonical natural-instance form / The naturally dropped 15E is stored as FE 15 FF FF. The first two bytes identify the type/ID; the last two remain FF FF. This matches the rule that backpack unique items should not carry prefixes/suffixes and is consistent with FUN_0802ea38 initializing the entire four-byte item record to FF FF FF FF before generation.

## 8. 278 gems is the standard clean sale value for all 14 targets

FUN_0801f9bc hard-codes 500 for 14E and 300 for 17E. Other type-E items use the general pricing formula. ROM-byte verification in the studied USA/Europe build gives a type-E base byte of 251, and the price field for each of the 14 target records is 9:

251 + 9 x 3 = 278

The same pricing routine then inspects the hidden prefix/suffix bytes in the full 32-bit item instance. Non-FF values add extra price terms. Thus 278 is the standard clean-instance price, while a contaminated instance with the same item ID can display a different value.

- Conclusion = Standard clean price for each of the 14 targets; Value = 278 gems

- Conclusion = Total for all 14 standard items; Value = 3892 gems

- Conclusion = Natural 15E observed in-game; Value = 278 gems

- Conclusion = Canonical hidden bytes; Value = FF FF

## 9. The 4414-gem anomaly: hidden prefix/suffix contamination

Three comparison 512-byte SAV files were audited. All 12 character records passed checksum validation, but all 12 RedMask values were 0x0000. Multiple red backpack item instances carried valid equipment prefix/suffix byte values that should not be present on backpack uniques.

- ID = 0CE; Observed full instance = FE 0C 36 9B; Audit interpretation = Non-standard hidden affix bytes

- ID = 0DE; Observed full instance = FE 0D 92 7D; Audit interpretation = Non-standard hidden affix bytes

- ID = 0EE; Observed full instance = FE 0E 98 56; Audit interpretation = Non-standard hidden affix bytes

- ID = 0FE; Observed full instance = FE 0F 21 9B; Audit interpretation = Non-standard hidden affix bytes

- ID = 12E; Observed full instance = FE 12 16 9D / FE 12 93 95; Audit interpretation = Multiple contaminated instances

- ID = 13E; Observed full instance = FE 13 23 6F; also FE 13 FF FF; Audit interpretation = Dirty and clean copies of the same ID

- ID = 15E; Observed full instance = FE 15 9B 85; also FE 15 FF FF; Audit interpretation = Dirty and clean copies of the same ID

- ID = 16E; Observed full instance = FE 16 57 7C / FE 16 07 80; also FF FF; Audit interpretation = Several instance forms for the same ID

- ID = 19E; Observed full instance = FE 19 9B 94; also FE 19 FF FF; Audit interpretation = Dirty and clean copies of the same ID

This directly contradicts the idea that 4414 is a fixed official price attached to those item IDs. The more consistent explanation is instance-specific hidden high-word data being interpreted by FUN_0801f9bc.

- Evidence boundary / These saves prove non-standard hidden fields and repeated data patterns. They do not, by themselves, prove which specific cheat, editor, transfer process, or glitch produced them. What can be stated objectively is that the affected instances do not match the canonical FE ID FF FF form expected for naturally generated backpack uniques.

## 10. Exact RNG mapping: 1/100, 1/10, and 1/53 are approximations

FUN_0803a4bc maps a 15-bit PRNG value to an inclusive integer range by multiply-and-shift:

if (min != max) {
    u = FUN_0803a640() & 0x7FFF;
    result = min + ((u * ((max-min)+1)) >> 15);
}

For uniformly distributed 15-bit inputs 0 through 32767, the integer buckets are exactly:

- Call = FUN_0803a4bc(1,100); Target result = 100; 15-bit inputs = 327; Probability = 327/32768 = 0.9979248%

- Call = FUN_0803a4bc(1,10); Target result = 10; 15-bit inputs = 3276; Probability = 3276/32768 = 9.9975586%

- Call = FUN_0803a4bc(0,0x34); Target result = 53 candidates; 15-bit inputs = 618 or 619 per bucket; Probability = Not exactly 1/53

Among the 14 target IDs, 0BE, 0FE, 12E, and 16E receive 619 candidate inputs; the other ten receive 618. The 14 target buckets sum to 8656.

Under the specific downstream conditions that a normal FUN_0803ae8c call maps type E to roll 100 and does not trigger the param_4=='S' remap, the probability of any one of the 14 target red outcomes is:

(327/32768) x (8656/32768) x (3276/32768)
= 0.000263547614
~= 1 / 3794.38

A specified 618-bucket target is approximately 1/53145.89 per such opportunity; a 619-bucket target is approximately 1/53060.03. The difference is only about 0.16%, but it means that 'all 14 are exactly equal and each is exactly 1/53000' is not a precise statement.

- Critical probability boundary / These denominators are not kill counts and not corpse-hit counts. They describe a conditioned downstream item-generation opportunity. Whether a monster, container, or corpse interaction reaches FUN_0803a77c and then FUN_0803ae8c depends on upstream source parameters and drop gates. Real farming efficiency therefore requires dynamic call counting or controlled gameplay measurement.

## 11. Small candidate-weight differences across the 14 targets

- ID = 0AE; Item = Celeborn's Phial; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

- ID = 0BE; Item = Boromir's Horn; 53-way bucket = 619; Specified-item probability* = ~1/53060.03; Standard price = 278

- ID = 0CE; Item = Cyclopedia of Herbs; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

- ID = 0DE; Item = Sam's Cookpot; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

- ID = 0EE; Item = Fletching Kit; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

- ID = 0FE; Item = Scrolls of Minas Tirith; 53-way bucket = 619; Specified-item probability* = ~1/53060.03; Standard price = 278

- ID = 10E; Item = Evenstar of Arwen; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

- ID = 11E; Item = Galadriel's Phial; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

- ID = 12E; Item = Forgotten Palantir; 53-way bucket = 619; Specified-item probability* = ~1/53060.03; Standard price = 278

- ID = 13E; Item = Cracked Palantir; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

- ID = 15E; Item = Woven Goblin Doll; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

- ID = 16E; Item = Oliphaunt Ivory Statuette; 53-way bucket = 619; Specified-item probability* = ~1/53060.03; Standard price = 278

- ID = 18E; Item = Swollen Spider Glands; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

- ID = 19E; Item = Featherwood Flute; 53-way bucket = 618; Specified-item probability* = ~1/53145.89; Standard price = 278

*Conditioned on the normal downstream type-E selection assumptions described above; not a per-kill probability.

## 12. Conclusions that can now be stated formally

1. The 14 target red backpack uniques exist in the original ROM's type-E item data and generation logic.

1. For the 0x0A-0x19 group, FUN_0803a844 performs a rare audit and checks the character-specific DAT_03003d8a qualification state; 14E and 17E are special cases.

1. The pickup path ORs the corresponding bit into DAT_03003d8a, creating strong evidence for a first-item qualification deadlock in the normal path.

1. Character SAV offset +0x56 is the persistent storage field for this 16-bit mask; 0xDBFF is the verified complete qualification combination for the 14 targets.

1. DBFF does not modify the PRNG, the type-E entry roll, the 1-in-10 rare audit, or the upstream chance that a source generates an equipment/item instance.

1. The natural 15E at 13:06 on 2026-08-16 proves that the DBFF-qualified original-rate path is dynamically executable.

1. The resulting SAV stores the natural 15E as FE 15 FF FF; all four character RedMask values are DBFF and all four character checksums are valid.

1. The canonical clean sale price for all 14 target IDs is 278 gems; 4414-like prices are explained by non-standard hidden instance data, not by the item ID's standard price.

1. The 14 candidate IDs are not mathematically perfectly equal because the 53-way integer mapping creates 618/619-sized buckets.

1. Non-zero reachability does not imply that all 14 will be obtained within any finite farming duration.

## 13. Questions that still require dynamic measurement or more reverse engineering

- The actual number of FUN_0803a77c / FUN_0803ae8c calls per minute for each map, monster, destructible object, and corpse-hitting method.

- Whether each visually observed corpse hit produces a new valid item-generation event; this should be measured with breakpoints, trace logging, or call counters.

- Which source parameters invoke special cases such as param_4=='S' that can remap a would-be type-E result.

- The real-world time distribution for collecting all 14, which combines code probability with player execution speed and source density.

- A full-ROM search for any obscure event capable of independently setting the RedMask bits, if an absolute exhaustive proof of the first-item deadlock is desired.

## 14. Index of the 14 target red backpack uniques

- ID = 0AE; Item = Celeborn's Phial; Primary effect = +50% poison resistance; Standard price = 278

- ID = 0BE; Item = Boromir's Horn; Primary effect = +10% experience, +3 Courage; Standard price = 278

- ID = 0CE; Item = Cyclopedia of Herbs; Primary effect = Herbs heal +25 HP; Standard price = 278

- ID = 0DE; Item = Sam's Cookpot; Primary effect = Up to +25% health regeneration; Frodo/Sam only; Standard price = 278

- ID = 0EE; Item = Fletching Kit; Primary effect = +10 bow damage; Legolas only; Standard price = 278

- ID = 0FE; Item = Scrolls of Minas Tirith; Primary effect = All spells +10 damage; Gandalf only; Standard price = 278

- ID = 10E; Item = Evenstar of Arwen; Primary effect = +20 Courage; Aragorn only; Standard price = 278

- ID = 11E; Item = Galadriel's Phial; Primary effect = +50% fear resistance; Frodo/Sam only; Standard price = 278

- ID = 12E; Item = Forgotten Palantir; Primary effect = +4 to all primary stats; corrupt; Standard price = 278

- ID = 13E; Item = Cracked Palantir; Primary effect = +4 to all primary stats; corrupt; Standard price = 278

- ID = 15E; Item = Woven Goblin Doll; Primary effect = +5 Strength, +5 Defense; Standard price = 278

- ID = 16E; Item = Oliphaunt Ivory Statuette; Primary effect = +5 melee toughness, +5 Health; Standard price = 278

- ID = 18E; Item = Swollen Spider Glands; Primary effect = +9 Damage; Standard price = 278

- ID = 19E; Item = Featherwood Flute; Primary effect = +10% shield block; Standard price = 278

## 15. Key decompiled functions and fields

- Function / field = FUN_0803a4bc; Research role = Maps 15-bit PRNG output to inclusive integer ranges; determines exact bucket sizes for 1..100, 1..10, and 0..52

- Function / field = FUN_0803a6e8; Research role = Common monster-drop entry; calls FUN_0803a77c after upstream gating

- Function / field = FUN_08014a3c; Research role = Destructible treasure-object routine that also calls FUN_0803a77c

- Function / field = FUN_0803a77c; Research role = Unified top-level drop dispatcher

- Function / field = FUN_0803ae8c; Research role = Full equipment/item instance type selection using DAT_0806dbac

- Function / field = FUN_0803a844; Research role = Base item candidate selection; core type-E rare audit and RedMask check

- Function / field = FUN_0802ea38; Research role = Initializes the four-byte item record to FF FF FF FF

- Function / field = Pickup handler; Research role = Sets the corresponding DAT_03003d8a bit for qualifying unique items

- Function / field = FUN_0803c294 / FUN_0803c6cc; Research role = Loads/saves character fields, including SAV +0x56 <-> DAT_03003d8a

- Function / field = FUN_0803cdb4; Research role = 16-bit additive checksum over each 0x70-byte character record

- Function / field = FUN_0801f9bc; Research role = Sale-price calculation; reads the complete 32-bit item instance, including hidden prefix/suffix bytes

- Final definition / RedMask=0xDBFF is best understood as a qualification repair: it persistently opens the legal generation qualifications for the 14 target red backpack uniques while leaving the rest of the original random drop chain in place. It is not a drop-rate boost.
