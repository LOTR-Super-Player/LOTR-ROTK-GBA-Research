# The Lord of the Rings: The Return of the King
Drop Eligibility Mechanism - Technical Reference

## Complete Flowchart · Pseudo-C Reconstruction · Mask / Item-ID Reference Tables

*Reconstructed from ARM7TDMI / Thumb control flow in the user-supplied ROMs
Document date: 2026-08-20*

- Evidence labels: [CONFIRMED] means the statement is directly supported by machine code, constants, or branch targets. [HIGH-CONFIDENCE INFERENCE] means the interpretation is reconstructed from control-flow semantics but is not directly named by a symbol table or item-text table.

# Analysis Baseline

- ROM / Build = English ROM - Two Towers Legacy Expansion Beta1 - Original RedMask Mechanism; SHA-256 = d6fd601cdbb87c5506d6c05877e932ce915a0f552a94e3b86288c79948e1c0a5

- ROM / Build = English ROM - Two Towers Legacy Expansion Beta1 - RedMask Bypass; SHA-256 = dae812e0c56928284f92b15fc8e8b3c7620dc6e851b934c93cac93d4110bb723

- ROM / Build = Chinese-localized ROM - Baseline; SHA-256 = 8d0b5d4fb7095150740da252ea73b98fea70e3b79f72aad36e289b703d23b1a5

- ROM / Build = Chinese-localized ROM - Permanent Rare-Drop Eligibility Enabled; SHA-256 = 2948f9f4c1dfcec69136d6224eb568e1ef5f051b24f20e6daafa90a831145039

## Core Character-Record Layout

char_base = 0x03003C00 + character_index * 0x1AC

u16 Mask188 = *(u16 *)(char_base + 0x188);
u16 RedMask = *(u16 *)(char_base + 0x18A);

// The same function also directly accesses:
// char_base + 0x18E   (another 16-bit special bitmap)
// char_base + 0x1A9   (byte; at least bit 0 is used)

- Important correction: the meaning of bit=1 is opposite between +0x188 and +0x18A. For +0x18A / RedMask, bit=1 permits the current item. In the +0x188 branch, bit=0 permits direct acceptance, while bit=1 transfers control into the fallback chain.

# Part I - Complete Drop Eligibility Flowchart

## 1.1 Overall Decision Order

Starting from the reconstructed drop-generation function at 0x0803A844, the game does not consult one universal eligibility table. Instead, it first builds the candidate index and processes the Ancient flag, then obtains item_id, branches by type and candidate_index, passes through the +0x188 and +0x18A filters, handles ordinary rare and 0x57/0x58 special branches, and only then reaches the fixed 0x51 / 0x54 fallback exits or the downstream normal/replacement generator.

## Flowchart Legend

- Node / Field = Ancient; Meaning in the Flowchart = Pre-downgrade gate after candidate generation; Key Code-Level Evidence = candidate.flags & 0x40; global-state bit 0x8000

- Node / Field = +0x188; Meaning in the Flowchart = 16-bit filter bitmap in the low candidate-index branch; Key Code-Level Evidence = bit=item_id-0x3D; bit=0 returns directly, bit=1 falls back

- Node / Field = +0x18A; Meaning in the Flowchart = RedMask; Key Code-Level Evidence = bit=item_id-0x47; bit=1 returns directly, bit=0 falls back

- Node / Field = 0x14 / 0x17; Meaning in the Flowchart = Two special candidate_index values; Key Code-Level Evidence = Accepted before the RedMask load/check

- Node / Field = 0x51 / 0x54; Meaning in the Flowchart = Two fixed item_id exits in the shared fallback chain; Key Code-Level Evidence = 0x14 -> 0x51; 0x17 -> 0x54

- Node / Field = 0x57 / 0x58; Meaning in the Flowchart = Additional fixed IDs in the higher-index path; Key Code-Level Evidence = Controlled by +0x1A9 bit0 / +0x18E bitmap respectively

- Node / Field = FALLBACK; Meaning in the Flowchart = Not synonymous with "no drop"; Key Code-Level Evidence = May still produce 0x54, 0x51, or enter normal/replacement generation

Address quick reference: main function block 0x0803A844-0x0803AB4A; +0x188 check 0x0803A9DC-0x0803AA00; RedMask check 0x0803AA34-0x0803AA58; fixed 0x54/0x51 exits 0x0803AAE8-0x0803AB0E.

- The flowchart term "fallback chain" does not mean the result must become an ordinary item. The chain can still produce 0x54 or 0x51, while 0x57/0x58 can enter the same shared exit region with the direct-return flag already set.

![Rare-item drop eligibility flowchart](images/01_Drop_Eligibility_Flowchart.png)

*Figure: Rare-item drop eligibility flowchart.*

Figure 1. Complete drop / eligibility control flow reconstructed from Thumb machine code

## 1.2 Ancient Pre-Eligibility Gate

[CONFIRMED] Candidate records contain a 0x40 flag bit. Ancient eligibility handling is entered only when candidate.flags & 0x40 != 0.

// Mode-dependent global state source
if (*(u32 *)0x03004144 & 0x20)
    ancient_state = *(u32 *)0x030069DC;
else
    ancient_state = *(u32 *)0x0300581C;   // 0x030057F0 + 0x2C

if (candidate.flags & 0x40) {
    if ((ancient_state & 0x8000) == 0) {
        candidate_index--;
        if ((u8)candidate_index == 0xFF)
            candidate_index = 0;
    }
}

Therefore, failure of the Ancient gate does not directly return an empty item. If a candidate carries the Ancient flag but the relevant global state lacks bit 0x8000, the current candidate index is decremented by one and saturated at zero on underflow. The most accurate high-level description is therefore an "Ancient candidate downgrade."

- Correction to an earlier simplified interpretation: the D1 -> E0 patch at ROM offset 0x003A905 in the localized build forces one branch of the Ancient state-source selection. It does not simply delete the later 0x8000 test. This document follows the complete reconstructed control flow.

## 1.3 type == 0x0E: Main Rare / Special Drop Tree

[CONFIRMED] The function then reads item_id from candidate record +0x18 and evaluates ctx[0] & 0x0F. Only when the low nibble equals 0x0E does execution enter the complex rare/special drop tree shown below.

- candidate_index Range = 0x00-0x09; Random Gate = rand(1,20)==20; Additional Gate = Mask +0x188; bit=item_id-0x3D; Success / Failure Path = bit=0 returns directly; bit=1 enters fallback

- candidate_index Range = 0x0A-0x19; Random Gate = rand(1,10)==10; Additional Gate = index 0x14/0x17 special case; otherwise RedMask +0x18A; Success / Failure Path = special indices return directly; RedMask bit=1 returns, bit=0 falls back

- candidate_index Range = 0x1A-0x38; Random Gate = rand(1,10)==10; Additional Gate = No +0x188 / +0x18A check; Success / Failure Path = hit returns directly; miss falls back

- candidate_index Range = >0x38; Random Gate = rand(1,5)==5 then +0x1A9 bit0; separate +0x18E bitmap path; Additional Gate = Can create 0x57 / 0x58; Success / Failure Path = then proceeds into shared fallback/special-exit region

## 1.4 +0x188 Bitmap: Bit Direction Is Opposite to RedMask

if (candidate_index <= 0x09) {
    if (rand_range(1, 20) != 20)
        goto fallback;

    u16 m = *(u16 *)(char_base + 0x188);
    unsigned bit = item_id - 0x3D;

    if (((m >> bit) & 1) != 0)
        goto fallback;      // bit=1: enter downstream fallback

    return item_id;         // bit=0: accept directly
}

[CONFIRMED] This means +0x188 should not be described as a conventional "bit=1 grants eligibility" table. In this function, it behaves more like an occupied/recorded/exclusion bitmap: bit=0 allows the current candidate to pass immediately, while bit=1 redirects the candidate into the downstream fallback-generation chain.

[HIGH-CONFIDENCE INFERENCE] Because this field is used in the low-index rare-candidate region, +0x188 most likely tracks a group of ordinary blue-name rare items or their exclusion/collection status. Without the item-text table and the write-side function for this bitmap, however, it should not be hard-labeled as a "blue rare eligibility mask."

## 1.5 +0x18A / RedMask: 14-Bit Red-Item Eligibility Gate

if (candidate_index >= 0x0A && candidate_index <= 0x19) {
    if (rand_range(1, 10) != 10)
        goto fallback;

    // Two candidate positions bypass RedMask completely
    if (candidate_index == 0x14 || candidate_index == 0x17)
        return item_id;

    u16 red_mask = *(u16 *)(char_base + 0x18A);
    unsigned bit = item_id - 0x47;

    if (((red_mask >> bit) & 1) == 0)
        goto fallback;

    return item_id;         // bit=1: permitted
}

[CONFIRMED] +0x18A is read as a 16-bit field, and the bit-index formula is item_id - 0x47. Because 0x47 through 0x54 contain exactly 14 consecutive values, they naturally map to RedMask bits 0 through 13.

- The RedMask-bypass ROM changes only the branch at 0x0803AA56 from "BEQ fallback" to a Thumb NOP. It does not modify RedMask storage, the item-ID table, or the preceding 1-in-10 random gate.

## 1.6 Fixed Special Exits: 0x51 / 0x54

r = rand_range(1, 100);

if (r <= 59) {
    ctx->candidate_index = 0x17;
    item_id = 0x54;
    return item_id;
}

if (r <= 69) {
    ctx->candidate_index = 0x14;
    item_id = 0x51;
    return item_id;
}

// 70..100: continue to normal/replacement generation
ctx->type |= 0x0F;
...;
item_id = 0x0E;

[CONFIRMED] 0x51 and 0x54 are not merely numeric members of the RedMask-addressable 0x47-0x54 range. They also have independent fixed-generation exits: 0x54 is paired with candidate_index 0x17, and 0x51 is paired with candidate_index 0x14. In the earlier RedMask branch, those same two candidate indices are explicitly accepted before RedMask is read.

[HIGH-CONFIDENCE INFERENCE] These two dedicated exits are the strongest candidates for the special backpack items referred to as the Orc Head and Orc Drum. Machine code alone does not determine which display name belongs to 0x51 and which belongs to 0x54; an item-text table or in-game inventory cross-check is still required for exact name binding.

## 1.7 Other Special IDs: 0x57 / 0x58

The complete control flow also exposes two additional fixed item IDs: 0x57 and 0x58. Generation of 0x57 depends on bit0 at char_base+0x1A9. Generation of 0x58 depends on the 16-bit bitmap at char_base+0x18E. Once either branch succeeds, the internal direct-return flag is cleared so that entry into the shared fallback area immediately returns the current item_id instead of running the 0x51 / 0x54 lottery.

# Part II - Full Pseudo-C Reconstruction

The following pseudo-C preserves the observed Thumb branch order as closely as possible. Register values and unknown structure fields are given semantic working names for readability. rand_range() is a working name for the random helper called from this function; whether every call uses strictly inclusive bounds would require a separate reconstruction of that helper.

uint8_t generate_drop(DropCtx *ctx, uint8_t character_index)
{
    uint8_t direct_flag = 1;               // original R9
    uint8_t candidate_index;
    uint8_t item_id;

    /* ---------------------------------------------------------
       A. Select candidate_index from the current drop table
       --------------------------------------------------------- */
    candidate_index = choose_candidate_index(ctx);
    ctx->candidate_index = candidate_index;

    /* ---------------------------------------------------------
       B. Ancient pre-gate
       Candidate-record flags bit 0x40 triggers this check
       --------------------------------------------------------- */
    uint32_t ancient_state;

    if (*(uint32_t *)0x03004144 & 0x20)
        ancient_state = *(uint32_t *)0x030069DC;
    else
        ancient_state = *(uint32_t *)0x0300581C;

    Candidate *cand = lookup_candidate(ctx, candidate_index);

    if (cand->flags & 0x40) {
        if ((ancient_state & 0x8000) == 0) {
            uint8_t x = candidate_index - 1;
            if (x == 0xFF)
                x = 0;
            candidate_index = x;
            ctx->candidate_index = x;
            cand = lookup_candidate(ctx, x);
        }
    }

    /* Under one additional condition, there is a 1/100-style
       random gate that can increment the candidate index. */
    if ((ctx->flags16 & UNKNOWN_MASK) == 0) {
        if (rand_range(1, 100) != 100)
            ctx->candidate_index++;
    }

    candidate_index = ctx->candidate_index;
    cand = lookup_candidate(ctx, candidate_index);
    item_id = cand->item_id;               // candidate record +0x18

    uint8_t type = ctx->type & 0x0F;

    /* ---------------------------------------------------------
       C. Only type == 0x0E enters the rare/special drop tree
       --------------------------------------------------------- */
    if (type != 0x0E) {
        if (type == 0x07)
            ctx->extra = rand_range(10, 40);
        return item_id;
    }

    uint8_t idx = candidate_index;
    uint8_t *char_base = (uint8_t *)0x03003C00
                       + character_index * 0x1AC;

    /* ---------------------------------------------------------
       D. idx 0x00..0x09: +0x188 bitmap
       --------------------------------------------------------- */
    if (idx <= 0x09) {
        if (rand_range(1, 20) != 20)
            goto FALLBACK;

        uint16_t mask188 = *(uint16_t *)(char_base + 0x188);
        unsigned bit = item_id - 0x3D;

        // Important: this direction is opposite to RedMask.
        if (((mask188 >> bit) & 1) != 0)
            goto FALLBACK;

        return item_id;                    // bit=0 passes directly
    }

    /* ---------------------------------------------------------
       E. idx 0x0A..0x19: RedMask / +0x18A
       --------------------------------------------------------- */
    if (idx <= 0x19) {
        if (rand_range(1, 10) != 10)
            goto FALLBACK;

        // Two special candidate indices bypass RedMask entirely.
        if (idx == 0x14 || idx == 0x17)
            return item_id;

        uint16_t red_mask = *(uint16_t *)(char_base + 0x18A);
        unsigned bit = item_id - 0x47;

        if (((red_mask >> bit) & 1) == 0)
            goto FALLBACK;

        return item_id;                    // RedMask bit=1 permits
    }

    /* ---------------------------------------------------------
       F. idx 0x1A..0x38: no +0x188 / +0x18A check
       --------------------------------------------------------- */
    if (idx <= 0x38) {
        if (rand_range(1, 10) != 10)
            goto FALLBACK;

        return item_id;
    }

    /* ---------------------------------------------------------
       G. Higher indices: special 0x57 / 0x58 exits
       --------------------------------------------------------- */
    if (rand_range(1, 5) == 5) {
        if ((*(uint8_t *)(char_base + 0x1A9) & 1) == 0) {
            direct_flag = 0;
            item_id = 0x57;
            goto FALLBACK;
        }
    }

    {
        unsigned n = rand_range(0, 14);
        uint16_t mask18e = *(uint16_t *)(char_base + 0x18E);

        if (((mask18e >> n) & 1) == 0) {
            ctx->candidate_index = (uint8_t)n;
            direct_flag = 0;
            item_id = 0x58;
        }
    }

FALLBACK:
    /* 0x57/0x58 clear direct_flag, so they return here immediately. */
    if (direct_flag == 0)
        return item_id;

    /* ---------------------------------------------------------
       H. Fixed special exits 0x54 / 0x51
       --------------------------------------------------------- */
    {
        unsigned r = rand_range(1, 100);

        if (r <= 59) {
            ctx->candidate_index = 0x17;
            return 0x54;
        }

        if (r <= 69) {
            ctx->candidate_index = 0x14;
            return 0x51;
        }
    }

    /* ---------------------------------------------------------
       I. Remaining cases: normal / replacement generation
       --------------------------------------------------------- */
    ctx->type |= 0x0F;
    {
        unsigned x = transform_index(ctx->candidate_index,
                                     character_index);
        x += 0xFA;
        apply_generated_item(ctx, x);
    }

    return 0x0E;
}

## 2.1 Five Invariants Worth Remembering

- The character-record stride is fixed at 0x1AC. +0x188, +0x18A, and +0x18E all sit near the tail of the same per-character record.

- Ancient eligibility is independent of RedMask. It is a separate pre-gate driven by candidate flag 0x40 plus global-state bit 0x8000.

- +0x188: bit=0 accepts the current item directly; bit=1 enters the fallback chain.

- +0x18A / RedMask: bit=1 accepts the current item directly; bit=0 enters the fallback chain.

- 0x51 / 0x54 have independent fixed-generation exits and use candidate_index 0x14 / 0x17, which bypass RedMask before the mask is read.

# Part III - Item-ID and Mask Reference Tables

## 3.1 Mask Field Summary

- Field = +0x188; Width = 16-bit; Bit-Index Formula = item_id - 0x3D; bit=0 = Accept current item_id directly; bit=1 = Enter FALLBACK; Safest Current Interpretation = Record/exclusion bitmap for low-index rare candidates; not a conventional bit=1 eligibility table

- Field = +0x18A; Width = 16-bit; Bit-Index Formula = item_id - 0x47; bit=0 = Enter FALLBACK; bit=1 = Accept current item_id directly; Safest Current Interpretation = RedMask; eligibility gate for 14 consecutive red-item IDs

- Field = +0x18E; Width = 16-bit; Bit-Index Formula = random n = 0..14; bit=0 = May generate item_id 0x58; bit=1 = Does not generate 0x58 on this branch; Safest Current Interpretation = Another special bitmap; exact semantic name unresolved

- Field = +0x1A9; Width = 8-bit (bit0 observed); Bit-Index Formula = fixed bit0; bit=0 = The 1-in-5 branch may generate 0x57; bit=1 = Blocks that 0x57 branch; Safest Current Interpretation = Special state byte

## 3.2 RedMask Bit Mapping for Item IDs 0x47-0x54

- item_id = 0x47; RedMask Bit = bit 0; Bit Value = 0x0001; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x48; RedMask Bit = bit 1; Bit Value = 0x0002; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x49; RedMask Bit = bit 2; Bit Value = 0x0004; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x4A; RedMask Bit = bit 3; Bit Value = 0x0008; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x4B; RedMask Bit = bit 4; Bit Value = 0x0010; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x4C; RedMask Bit = bit 5; Bit Value = 0x0020; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x4D; RedMask Bit = bit 6; Bit Value = 0x0040; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x4E; RedMask Bit = bit 7; Bit Value = 0x0080; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x4F; RedMask Bit = bit 8; Bit Value = 0x0100; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x50; RedMask Bit = bit 9; Bit Value = 0x0200; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x51; RedMask Bit = bit 10; Bit Value = 0x0400; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = Also has a fixed exit: ctx.index=0x14 -> 0x51; index 0x14 is accepted before the RedMask check.

- item_id = 0x52; RedMask Bit = bit 11; Bit Value = 0x0800; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x53; RedMask Bit = bit 12; Bit Value = 0x1000; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = No independent fixed exit identified in this function

- item_id = 0x54; RedMask Bit = bit 13; Bit Value = 0x2000; Original Mechanism = bit=1 returns directly; bit=0 falls back; Bypass ROM = BEQ is replaced by NOP, so mask result no longer blocks return; Special Note = Also has a fixed exit: ctx.index=0x17 -> 0x54; index 0x17 is accepted before the RedMask check.

## 3.3 The Dual Role of 0x51 / 0x54

- Numerically, 0x51 and 0x54 lie inside the 0x47-0x54 range and therefore correspond to RedMask bit10 and bit13. The code also provides a separate path that generates these IDs directly, assigns candidate_index 0x14 / 0x17, and exploits the special-case acceptance before RedMask. They therefore should not be treated as ordinary RedMask-only entries.

- item_id = 0x54; RedMask Bit = bit13; Fixed-Generation Condition = rand(1,100) <= 59; Forced candidate_index = 0x17; RedMask Behavior = index=0x17 returns before RedMask is read

- item_id = 0x51; RedMask Bit = bit10; Fixed-Generation Condition = 60 <= rand(1,100) <= 69; Forced candidate_index = 0x14; RedMask Behavior = index=0x14 returns before RedMask is read

If rand_range(1,100) is the usual inclusive implementation, the theoretical weights inside this particular fixed fallback exit are 59/100 for 0x54, 10/100 for 0x51, and 31/100 for normal/replacement generation. These are conditional weights only after execution has reached the shared FALLBACK with direct_flag still equal to 1; they are not overall monster drop rates.

## 3.4 The 0x3D-Based Range Used by +0x188

The +0x188 code uses bit=item_id-0x3D. A 16-bit bitmap can therefore address item IDs 0x3D through 0x4C in theory. In this function, however, the field is consulted only in the candidate_index 0x00-0x09 branch, so the actual item IDs that occur still depend on the relevant candidate table.

- Theoretical item_id = 0x3D; Mask188 Bit = bit 0; Bit Value = 0x0001; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x3E; Mask188 Bit = bit 1; Bit Value = 0x0002; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x3F; Mask188 Bit = bit 2; Bit Value = 0x0004; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x40; Mask188 Bit = bit 3; Bit Value = 0x0008; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x41; Mask188 Bit = bit 4; Bit Value = 0x0010; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x42; Mask188 Bit = bit 5; Bit Value = 0x0020; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x43; Mask188 Bit = bit 6; Bit Value = 0x0040; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x44; Mask188 Bit = bit 7; Bit Value = 0x0080; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x45; Mask188 Bit = bit 8; Bit Value = 0x0100; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x46; Mask188 Bit = bit 9; Bit Value = 0x0200; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x47; Mask188 Bit = bit 10; Bit Value = 0x0400; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x48; Mask188 Bit = bit 11; Bit Value = 0x0800; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x49; Mask188 Bit = bit 12; Bit Value = 0x1000; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x4A; Mask188 Bit = bit 13; Bit Value = 0x2000; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x4B; Mask188 Bit = bit 14; Bit Value = 0x4000; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

- Theoretical item_id = 0x4C; Mask188 Bit = bit 15; Bit Value = 0x8000; Meaning in This Function = bit=0 can return directly; bit=1 transfers to FALLBACK

## 3.5 Evidence Boundary for Display-Name Mapping

The current binary analysis can determine item-ID values, candidate indices, mask offsets, random gates, branch directions, fixed exits, and their relationships with high confidence. The English ROM does not expose the relevant item names as a simple directly readable ASCII table in this code region. Therefore, the following display-name mappings are not proven one-to-one by this machine-code segment alone:

- Which of 0x51 and 0x54 is specifically displayed as Orc Head and which is displayed as Orc Drum.

- The exact equipment name corresponding to each red-item ID from 0x47 through 0x54.

- The exact blue-name rare-item name corresponding to each theoretical +0x188 bit / item ID from 0x3D through 0x4C.

Resolving those names requires locating the item-data / text-index tables or cross-checking against in-game inventory displays and known SAV item records. This document therefore keeps numeric mechanism findings separate from unresolved display-name labels.

# Appendix A - Key Thumb Instruction Evidence

## A.1 +0x188 Check

0803A9EA  MOVS R3,#0xC4
0803A9EC  LSLS R3,R3,#1        ; 0x188
0803A9EE  ADDS R0,R0,R3
0803A9F0  LDRH R0,[R0]
0803A9F2  MOVS R1,R5
0803A9F4  SUBS R1,#0x3D
0803A9F6  ASRS R0,R1
0803A9F8  MOVS R1,#1
0803A9FA  ANDS R0,R1
0803A9FC  CMP  R0,#0
0803A9FE  BNE  0x0803AAE2      ; bit=1 -> fallback
0803AA00  B    0x0803AB3E      ; bit=0 -> return item_id

## A.2 +0x18A / RedMask Check

0803AA42  MOVS R3,#0xC5
0803AA44  LSLS R3,R3,#1        ; 0x18A
0803AA46  ADDS R0,R0,R3
0803AA48  LDRH R0,[R0]
0803AA4A  MOVS R1,R5
0803AA4C  SUBS R1,#0x47
0803AA4E  ASRS R0,R1
0803AA50  MOVS R1,#1
0803AA52  ANDS R0,R1
0803AA54  CMP  R0,#0
0803AA56  BEQ  0x0803AAE2      ; bit=0 -> fallback
0803AA58  B    0x0803AB3E      ; bit=1 -> return item_id

## A.3 The Only Difference in the RedMask-Bypass ROM

Original RedMask mechanism @ ROM 0x0003AA56:
44 D0    ; BEQ 0x0803AAE2

RedMask-bypass ROM:
C0 46    ; Thumb NOP (MOV R8,R8)

The next instruction 71 E0 remains unchanged, so regardless of the
RedMask test result, execution continues with:
B 0x0803AB3E  -> return current R5 / item_id

## A.4 Fixed 0x54 / 0x51 Exits

0803AAE8  MOVS R0,#1
0803AAEA  MOVS R1,#100
0803AAEC  BL   rand_helper
0803AAF2  CMP  R2,#0x3B        ; 59
0803AAF4  BGT  next
0803AAF6  MOVS R0,#0x17
0803AAF8  STRB R0,[R4,#1]
0803AAFA  MOVS R5,#0x54
0803AAFC  B    return_item

0803AB04  CMP  R2,#0x45        ; 69
0803AB06  BGT  ordinary_fallback
0803AB08  MOVS R0,#0x14
0803AB0A  STRB R0,[R4,#1]
0803AB0C  MOVS R5,#0x51
0803AB0E  B    return_item

# Appendix B - Conclusion Summary

- Finding = Ancient; Code-Level Meaning = Candidate flags bit 0x40 triggers the check. A mode-dependent global-state source supplies bit 0x8000; when not qualified, the candidate index is downgraded.

- Finding = +0x188; Code-Level Meaning = 16-bit bitmap; bit=item_id-0x3D. In this function, bit=0 accepts directly and bit=1 enters the fallback chain.

- Finding = +0x18A / RedMask; Code-Level Meaning = 16-bit field; bit=item_id-0x47. bit=1 accepts and bit=0 falls back. The bypass ROM only NOPs the failure branch.

- Finding = 0x47-0x54; Code-Level Meaning = Fourteen consecutive addressable IDs corresponding to RedMask bits 0-13. Exact display names still require item-table evidence.

- Finding = 0x51 / 0x54; Code-Level Meaning = Also have fixed special-generation exits paired with candidate_index 0x14 / 0x17; those indices are accepted before the RedMask check.

- Finding = Ordinary blue-name rare items; Code-Level Meaning = Most likely concentrated in the low-index rare-candidate region governed by +0x188, but the display-name mapping for each bit is not proven by this code alone.

- Finding = Orc Head / Orc Drum; Code-Level Meaning = The code proves that 0x51 and 0x54 are two fixed special exits; exact head-versus-drum name binding still requires item-text or in-game evidence.
