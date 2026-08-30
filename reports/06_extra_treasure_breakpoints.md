# GBA The Lord of the Rings: The Return of the King
Extra Treasure (ET) Machine-Code Probability Step Changes - Full Summary

## Code / ROM evidence standard · Prefix / Suffix discrete thresholds · Exact 15-bit RNG bucket rates

*Compiled: 2026-08-22*

- Item = Core behavior; Conclusion = ET does not increase Prefix/Suffix probability continuously or linearly. Integer division via floor() first converts it into discrete thresholds; the probability advances by one tier only when a specific T breakpoint is crossed.

- Item = Effective T; Conclusion = This project combines equipment Extra Treasure and the adopted better-items passive modifiers into the effective quality parameter T.

- Item = Suffix; Conclusion = The Normal / Hard / Grueling base gates are 2 / 3 / 4; threshold = B + floor(B×T/100).

- Item = Prefix; Conclusion = Threshold = base B + floor(B×T/100), then capped at 100. B is determined by drop level, source, and special branches.

- Item = 0x53 max drop tier; Conclusion = B = 10 + 20 = 30; the threshold first reaches the 100% cap at T=234.

- Item = Best +100 Prefix sources; Conclusion = The confirmed 0x27, 0x3C-0x42, 0x44, 0x45, and 0x50 sources already have a base ≥100 at the max tier, so Prefix is already 100% at T=0.

- Item = Exact machine-code rate; Conclusion = rand(1,100) does not produce mathematically exact 1% buckets. It maps 15-bit values 0..32767, so the actual pass rate for threshold n is ceil(n×32768/100)/32768.

## 1. Evidence chain: which functions ET actually enters

From the drop call chain, Extra Treasure / the effective quality parameter does not directly change the first-layer probability of whether a monster drops anything or whether a source enters the equipment generator. FUN_0803A6E8 first reads source from object+0x61 and completes the first-layer drop check; only afterward is the character quality parameter passed to FUN_0803A77C → FUN_0803AE8C, where FUN_0803AB4C (Prefix) and FUN_0803ACC4 (Suffix) use it.

FUN_0803A6E8
  source = *(u8 *)(object + 0x61)
  first drop/source gate
  -> FUN_0803A77C(..., quality_T, source)
      -> FUN_0803AE8C(..., quality_T, source)
          -> FUN_0803AB4C(..., quality_T, ..., source)   // Prefix
          -> FUN_0803ACC4(..., quality_T)                // Suffix

- Code anchors: decompilation document pp. 966-968, FUN_0803A6E8 / FUN_0803A77C; pp. 975-976, FUN_0803AE8C.

- Therefore, ET does not directly raise the full-equipment-roll entry rate of a normal Class-A enemy from 16.5%; it mainly improves Prefix/Suffix quality after equipment generation has already been entered.

## 2. Why the machine code produces step changes instead of continuous growth

The decompiled behavior of the core function FUN_08032A5C is essentially integer multiplication followed by division by 100: floor(param1×param2/100).

void FUN_08032A5C(int base, uint T) {
    return (base * (T & 0xFFFF)) / 100;   // integer division, rounded down
}

Therefore, when T rises from 630 to 631, 632, or 633, if floor(B×T/100) does not change, the internal game threshold is completely unchanged and so is the final probability. A probability step occurs only when the next integer tier is crossed.

- Code anchor: decompilation document p. 831, FUN_08032A5C.

## 3. Why a nominal threshold of n% is not always exactly n.000% with the 15-bit RNG

FUN_0803A4BC(a,b) maps the low 15 bits of the random value (0..32767) to the closed interval [a,b]. For rand(1,100), the exact number of buckets satisfying roll <= n is ceil(n×32768/100).

FUN_0803A4BC(1,100):
  return 1 + (((rng & 0x7FFF) * 100) >> 15)

P(roll <= n) = ceil(n * 32768 / 100) / 32768

- Threshold n = 2; Passing buckets / 32768 = 656; Exact machine-code rate = 2.001953125%

- Threshold n = 3; Passing buckets / 32768 = 984; Exact machine-code rate = 3.002929688%

- Threshold n = 4; Passing buckets / 32768 = 1311; Exact machine-code rate = 4.000854492%

- Threshold n = 5; Passing buckets / 32768 = 1639; Exact machine-code rate = 5.001831055%

- Threshold n = 6; Passing buckets / 32768 = 1967; Exact machine-code rate = 6.002807617%

- Threshold n = 7; Passing buckets / 32768 = 2294; Exact machine-code rate = 7.000732422%

- Threshold n = 8; Passing buckets / 32768 = 2622; Exact machine-code rate = 8.001708984%

- Threshold n = 9; Passing buckets / 32768 = 2950; Exact machine-code rate = 9.002685547%

- Threshold n = 10; Passing buckets / 32768 = 3277; Exact machine-code rate = 10.000610352%

- Threshold n = 20; Passing buckets / 32768 = 6554; Exact machine-code rate = 20.001220703%

- Threshold n = 21; Passing buckets / 32768 = 6882; Exact machine-code rate = 21.002197266%

- Threshold n = 22; Passing buckets / 32768 = 7209; Exact machine-code rate = 22.000122070%

- Threshold n = 28; Passing buckets / 32768 = 9176; Exact machine-code rate = 28.002929688%

- Threshold n = 29; Passing buckets / 32768 = 9503; Exact machine-code rate = 29.000854492%

- Threshold n = 30; Passing buckets / 32768 = 9831; Exact machine-code rate = 30.001831055%

- Threshold n = 50; Passing buckets / 32768 = 16384; Exact machine-code rate = 50.000000000%

- Threshold n = 52; Passing buckets / 32768 = 17040; Exact machine-code rate = 52.001953125%

- Threshold n = 99; Passing buckets / 32768 = 32441; Exact machine-code rate = 99.002075195%

- Threshold n = 100; Passing buckets / 32768 = 32768; Exact machine-code rate = 100.000000000%

- Code anchor: decompilation document p. 962, FUN_0803A4BC.

## 4. Unified definition of effective T

For dual-affix farming, this project consistently uses T to represent the effective quality modifier. Under the current research convention, equipment Extra Treasure and passive better-items modifiers that can be converted into this quality term are added together.

- Source = Equipment Extra Treasure; Conversion = Sum the actual affix values on equipped items; Notes = Added directly to T

- Source = Keen Eyes; Conversion = +5 / level; 5/5 = +25; Notes = Included in T under the project's established convention

- Source = Dwarf Sense; Conversion = +10 / level; 5/5 = +50; Notes = Included in T under the project's established convention

Note: the per-level values of Keen Eyes / Dwarf Sense come from the project's existing materials and cross-checks against guides; they were not independently re-identified in this pass from function symbol names. This report only summarizes the unified T convention already adopted for probability calculations.

## 5. Suffix: general formula for the three difficulty levels

The base gate in FUN_0803ACC4 is determined by difficulty: Normal=2, Hard=3, Grueling=4.

B = 2  // Normal
B = 3  // Hard
B = 4  // Grueling

SuffixThreshold(T) = B + floor(B * T / 100)
P_suffix(T) = P(rand(1,100) <= SuffixThreshold)

Unlike Prefix, the decompilation here has no explicit 'if >100 then =100'; however, rand(1,100) can never exceed 100, so once the threshold reaches or exceeds 100, the effective pass rate naturally becomes 100%.

## 6. Complete machine-code Suffix step table (threshold 2→100)

The table below gives every possible Suffix threshold and the minimum effective T needed for each difficulty to reach that threshold for the first time. Stacking additional ET between two adjacent first-T breakpoints does not change Suffix probability.

- Suffix threshold = 2; RNG buckets = 656/32768; Exact pass rate = 2.001953125%; Normal first T = 0; Hard first T = —; Grueling first T = —

- Suffix threshold = 3; RNG buckets = 984/32768; Exact pass rate = 3.002929688%; Normal first T = 50; Hard first T = 0; Grueling first T = —

- Suffix threshold = 4; RNG buckets = 1311/32768; Exact pass rate = 4.000854492%; Normal first T = 100; Hard first T = 34; Grueling first T = 0

- Suffix threshold = 5; RNG buckets = 1639/32768; Exact pass rate = 5.001831055%; Normal first T = 150; Hard first T = 67; Grueling first T = 25

- Suffix threshold = 6; RNG buckets = 1967/32768; Exact pass rate = 6.002807617%; Normal first T = 200; Hard first T = 100; Grueling first T = 50

- Suffix threshold = 7; RNG buckets = 2294/32768; Exact pass rate = 7.000732422%; Normal first T = 250; Hard first T = 134; Grueling first T = 75

- Suffix threshold = 8; RNG buckets = 2622/32768; Exact pass rate = 8.001708984%; Normal first T = 300; Hard first T = 167; Grueling first T = 100

- Suffix threshold = 9; RNG buckets = 2950/32768; Exact pass rate = 9.002685547%; Normal first T = 350; Hard first T = 200; Grueling first T = 125

- Suffix threshold = 10; RNG buckets = 3277/32768; Exact pass rate = 10.000610352%; Normal first T = 400; Hard first T = 234; Grueling first T = 150

- Suffix threshold = 11; RNG buckets = 3605/32768; Exact pass rate = 11.001586914%; Normal first T = 450; Hard first T = 267; Grueling first T = 175

- Suffix threshold = 12; RNG buckets = 3933/32768; Exact pass rate = 12.002563477%; Normal first T = 500; Hard first T = 300; Grueling first T = 200

- Suffix threshold = 13; RNG buckets = 4260/32768; Exact pass rate = 13.000488281%; Normal first T = 550; Hard first T = 334; Grueling first T = 225

- Suffix threshold = 14; RNG buckets = 4588/32768; Exact pass rate = 14.001464844%; Normal first T = 600; Hard first T = 367; Grueling first T = 250

- Suffix threshold = 15; RNG buckets = 4916/32768; Exact pass rate = 15.002441406%; Normal first T = 650; Hard first T = 400; Grueling first T = 275

- Suffix threshold = 16; RNG buckets = 5243/32768; Exact pass rate = 16.000366211%; Normal first T = 700; Hard first T = 434; Grueling first T = 300

- Suffix threshold = 17; RNG buckets = 5571/32768; Exact pass rate = 17.001342773%; Normal first T = 750; Hard first T = 467; Grueling first T = 325

- Suffix threshold = 18; RNG buckets = 5899/32768; Exact pass rate = 18.002319336%; Normal first T = 800; Hard first T = 500; Grueling first T = 350

- Suffix threshold = 19; RNG buckets = 6226/32768; Exact pass rate = 19.000244141%; Normal first T = 850; Hard first T = 534; Grueling first T = 375

- Suffix threshold = 20; RNG buckets = 6554/32768; Exact pass rate = 20.001220703%; Normal first T = 900; Hard first T = 567; Grueling first T = 400

- Suffix threshold = 21; RNG buckets = 6882/32768; Exact pass rate = 21.002197266%; Normal first T = 950; Hard first T = 600; Grueling first T = 425

- Suffix threshold = 22; RNG buckets = 7209/32768; Exact pass rate = 22.000122070%; Normal first T = 1000; Hard first T = 634; Grueling first T = 450

- Suffix threshold = 23; RNG buckets = 7537/32768; Exact pass rate = 23.001098633%; Normal first T = 1050; Hard first T = 667; Grueling first T = 475

- Suffix threshold = 24; RNG buckets = 7865/32768; Exact pass rate = 24.002075195%; Normal first T = 1100; Hard first T = 700; Grueling first T = 500

- Suffix threshold = 25; RNG buckets = 8192/32768; Exact pass rate = 25.000000000%; Normal first T = 1150; Hard first T = 734; Grueling first T = 525

- Suffix threshold = 26; RNG buckets = 8520/32768; Exact pass rate = 26.000976562%; Normal first T = 1200; Hard first T = 767; Grueling first T = 550

- Suffix threshold = 27; RNG buckets = 8848/32768; Exact pass rate = 27.001953125%; Normal first T = 1250; Hard first T = 800; Grueling first T = 575

- Suffix threshold = 28; RNG buckets = 9176/32768; Exact pass rate = 28.002929688%; Normal first T = 1300; Hard first T = 834; Grueling first T = 600

- Suffix threshold = 29; RNG buckets = 9503/32768; Exact pass rate = 29.000854492%; Normal first T = 1350; Hard first T = 867; Grueling first T = 625

- Suffix threshold = 30; RNG buckets = 9831/32768; Exact pass rate = 30.001831055%; Normal first T = 1400; Hard first T = 900; Grueling first T = 650

- Suffix threshold = 31; RNG buckets = 10159/32768; Exact pass rate = 31.002807617%; Normal first T = 1450; Hard first T = 934; Grueling first T = 675

- Suffix threshold = 32; RNG buckets = 10486/32768; Exact pass rate = 32.000732422%; Normal first T = 1500; Hard first T = 967; Grueling first T = 700

- Suffix threshold = 33; RNG buckets = 10814/32768; Exact pass rate = 33.001708984%; Normal first T = 1550; Hard first T = 1000; Grueling first T = 725

- Suffix threshold = 34; RNG buckets = 11142/32768; Exact pass rate = 34.002685547%; Normal first T = 1600; Hard first T = 1034; Grueling first T = 750

- Suffix threshold = 35; RNG buckets = 11469/32768; Exact pass rate = 35.000610352%; Normal first T = 1650; Hard first T = 1067; Grueling first T = 775

- Suffix threshold = 36; RNG buckets = 11797/32768; Exact pass rate = 36.001586914%; Normal first T = 1700; Hard first T = 1100; Grueling first T = 800

- Suffix threshold = 37; RNG buckets = 12125/32768; Exact pass rate = 37.002563477%; Normal first T = 1750; Hard first T = 1134; Grueling first T = 825

- Suffix threshold = 38; RNG buckets = 12452/32768; Exact pass rate = 38.000488281%; Normal first T = 1800; Hard first T = 1167; Grueling first T = 850

- Suffix threshold = 39; RNG buckets = 12780/32768; Exact pass rate = 39.001464844%; Normal first T = 1850; Hard first T = 1200; Grueling first T = 875

- Suffix threshold = 40; RNG buckets = 13108/32768; Exact pass rate = 40.002441406%; Normal first T = 1900; Hard first T = 1234; Grueling first T = 900

- Suffix threshold = 41; RNG buckets = 13435/32768; Exact pass rate = 41.000366211%; Normal first T = 1950; Hard first T = 1267; Grueling first T = 925

- Suffix threshold = 42; RNG buckets = 13763/32768; Exact pass rate = 42.001342773%; Normal first T = 2000; Hard first T = 1300; Grueling first T = 950

- Suffix threshold = 43; RNG buckets = 14091/32768; Exact pass rate = 43.002319336%; Normal first T = 2050; Hard first T = 1334; Grueling first T = 975

- Suffix threshold = 44; RNG buckets = 14418/32768; Exact pass rate = 44.000244141%; Normal first T = 2100; Hard first T = 1367; Grueling first T = 1000

- Suffix threshold = 45; RNG buckets = 14746/32768; Exact pass rate = 45.001220703%; Normal first T = 2150; Hard first T = 1400; Grueling first T = 1025

- Suffix threshold = 46; RNG buckets = 15074/32768; Exact pass rate = 46.002197266%; Normal first T = 2200; Hard first T = 1434; Grueling first T = 1050

- Suffix threshold = 47; RNG buckets = 15401/32768; Exact pass rate = 47.000122070%; Normal first T = 2250; Hard first T = 1467; Grueling first T = 1075

- Suffix threshold = 48; RNG buckets = 15729/32768; Exact pass rate = 48.001098633%; Normal first T = 2300; Hard first T = 1500; Grueling first T = 1100

- Suffix threshold = 49; RNG buckets = 16057/32768; Exact pass rate = 49.002075195%; Normal first T = 2350; Hard first T = 1534; Grueling first T = 1125

- Suffix threshold = 50; RNG buckets = 16384/32768; Exact pass rate = 50.000000000%; Normal first T = 2400; Hard first T = 1567; Grueling first T = 1150

- Suffix threshold = 51; RNG buckets = 16712/32768; Exact pass rate = 51.000976562%; Normal first T = 2450; Hard first T = 1600; Grueling first T = 1175

- Suffix threshold = 52; RNG buckets = 17040/32768; Exact pass rate = 52.001953125%; Normal first T = 2500; Hard first T = 1634; Grueling first T = 1200

- Suffix threshold = 53; RNG buckets = 17368/32768; Exact pass rate = 53.002929688%; Normal first T = 2550; Hard first T = 1667; Grueling first T = 1225

- Suffix threshold = 54; RNG buckets = 17695/32768; Exact pass rate = 54.000854492%; Normal first T = 2600; Hard first T = 1700; Grueling first T = 1250

- Suffix threshold = 55; RNG buckets = 18023/32768; Exact pass rate = 55.001831055%; Normal first T = 2650; Hard first T = 1734; Grueling first T = 1275

- Suffix threshold = 56; RNG buckets = 18351/32768; Exact pass rate = 56.002807617%; Normal first T = 2700; Hard first T = 1767; Grueling first T = 1300

- Suffix threshold = 57; RNG buckets = 18678/32768; Exact pass rate = 57.000732422%; Normal first T = 2750; Hard first T = 1800; Grueling first T = 1325

- Suffix threshold = 58; RNG buckets = 19006/32768; Exact pass rate = 58.001708984%; Normal first T = 2800; Hard first T = 1834; Grueling first T = 1350

- Suffix threshold = 59; RNG buckets = 19334/32768; Exact pass rate = 59.002685547%; Normal first T = 2850; Hard first T = 1867; Grueling first T = 1375

- Suffix threshold = 60; RNG buckets = 19661/32768; Exact pass rate = 60.000610352%; Normal first T = 2900; Hard first T = 1900; Grueling first T = 1400

- Suffix threshold = 61; RNG buckets = 19989/32768; Exact pass rate = 61.001586914%; Normal first T = 2950; Hard first T = 1934; Grueling first T = 1425

- Suffix threshold = 62; RNG buckets = 20317/32768; Exact pass rate = 62.002563477%; Normal first T = 3000; Hard first T = 1967; Grueling first T = 1450

- Suffix threshold = 63; RNG buckets = 20644/32768; Exact pass rate = 63.000488281%; Normal first T = 3050; Hard first T = 2000; Grueling first T = 1475

- Suffix threshold = 64; RNG buckets = 20972/32768; Exact pass rate = 64.001464844%; Normal first T = 3100; Hard first T = 2034; Grueling first T = 1500

- Suffix threshold = 65; RNG buckets = 21300/32768; Exact pass rate = 65.002441406%; Normal first T = 3150; Hard first T = 2067; Grueling first T = 1525

- Suffix threshold = 66; RNG buckets = 21627/32768; Exact pass rate = 66.000366211%; Normal first T = 3200; Hard first T = 2100; Grueling first T = 1550

- Suffix threshold = 67; RNG buckets = 21955/32768; Exact pass rate = 67.001342773%; Normal first T = 3250; Hard first T = 2134; Grueling first T = 1575

- Suffix threshold = 68; RNG buckets = 22283/32768; Exact pass rate = 68.002319336%; Normal first T = 3300; Hard first T = 2167; Grueling first T = 1600

- Suffix threshold = 69; RNG buckets = 22610/32768; Exact pass rate = 69.000244141%; Normal first T = 3350; Hard first T = 2200; Grueling first T = 1625

- Suffix threshold = 70; RNG buckets = 22938/32768; Exact pass rate = 70.001220703%; Normal first T = 3400; Hard first T = 2234; Grueling first T = 1650

- Suffix threshold = 71; RNG buckets = 23266/32768; Exact pass rate = 71.002197266%; Normal first T = 3450; Hard first T = 2267; Grueling first T = 1675

- Suffix threshold = 72; RNG buckets = 23593/32768; Exact pass rate = 72.000122070%; Normal first T = 3500; Hard first T = 2300; Grueling first T = 1700

- Suffix threshold = 73; RNG buckets = 23921/32768; Exact pass rate = 73.001098633%; Normal first T = 3550; Hard first T = 2334; Grueling first T = 1725

- Suffix threshold = 74; RNG buckets = 24249/32768; Exact pass rate = 74.002075195%; Normal first T = 3600; Hard first T = 2367; Grueling first T = 1750

- Suffix threshold = 75; RNG buckets = 24576/32768; Exact pass rate = 75.000000000%; Normal first T = 3650; Hard first T = 2400; Grueling first T = 1775

- Suffix threshold = 76; RNG buckets = 24904/32768; Exact pass rate = 76.000976562%; Normal first T = 3700; Hard first T = 2434; Grueling first T = 1800

- Suffix threshold = 77; RNG buckets = 25232/32768; Exact pass rate = 77.001953125%; Normal first T = 3750; Hard first T = 2467; Grueling first T = 1825

- Suffix threshold = 78; RNG buckets = 25560/32768; Exact pass rate = 78.002929688%; Normal first T = 3800; Hard first T = 2500; Grueling first T = 1850

- Suffix threshold = 79; RNG buckets = 25887/32768; Exact pass rate = 79.000854492%; Normal first T = 3850; Hard first T = 2534; Grueling first T = 1875

- Suffix threshold = 80; RNG buckets = 26215/32768; Exact pass rate = 80.001831055%; Normal first T = 3900; Hard first T = 2567; Grueling first T = 1900

- Suffix threshold = 81; RNG buckets = 26543/32768; Exact pass rate = 81.002807617%; Normal first T = 3950; Hard first T = 2600; Grueling first T = 1925

- Suffix threshold = 82; RNG buckets = 26870/32768; Exact pass rate = 82.000732422%; Normal first T = 4000; Hard first T = 2634; Grueling first T = 1950

- Suffix threshold = 83; RNG buckets = 27198/32768; Exact pass rate = 83.001708984%; Normal first T = 4050; Hard first T = 2667; Grueling first T = 1975

- Suffix threshold = 84; RNG buckets = 27526/32768; Exact pass rate = 84.002685547%; Normal first T = 4100; Hard first T = 2700; Grueling first T = 2000

- Suffix threshold = 85; RNG buckets = 27853/32768; Exact pass rate = 85.000610352%; Normal first T = 4150; Hard first T = 2734; Grueling first T = 2025

- Suffix threshold = 86; RNG buckets = 28181/32768; Exact pass rate = 86.001586914%; Normal first T = 4200; Hard first T = 2767; Grueling first T = 2050

- Suffix threshold = 87; RNG buckets = 28509/32768; Exact pass rate = 87.002563477%; Normal first T = 4250; Hard first T = 2800; Grueling first T = 2075

- Suffix threshold = 88; RNG buckets = 28836/32768; Exact pass rate = 88.000488281%; Normal first T = 4300; Hard first T = 2834; Grueling first T = 2100

- Suffix threshold = 89; RNG buckets = 29164/32768; Exact pass rate = 89.001464844%; Normal first T = 4350; Hard first T = 2867; Grueling first T = 2125

- Suffix threshold = 90; RNG buckets = 29492/32768; Exact pass rate = 90.002441406%; Normal first T = 4400; Hard first T = 2900; Grueling first T = 2150

- Suffix threshold = 91; RNG buckets = 29819/32768; Exact pass rate = 91.000366211%; Normal first T = 4450; Hard first T = 2934; Grueling first T = 2175

- Suffix threshold = 92; RNG buckets = 30147/32768; Exact pass rate = 92.001342773%; Normal first T = 4500; Hard first T = 2967; Grueling first T = 2200

- Suffix threshold = 93; RNG buckets = 30475/32768; Exact pass rate = 93.002319336%; Normal first T = 4550; Hard first T = 3000; Grueling first T = 2225

- Suffix threshold = 94; RNG buckets = 30802/32768; Exact pass rate = 94.000244141%; Normal first T = 4600; Hard first T = 3034; Grueling first T = 2250

- Suffix threshold = 95; RNG buckets = 31130/32768; Exact pass rate = 95.001220703%; Normal first T = 4650; Hard first T = 3067; Grueling first T = 2275

- Suffix threshold = 96; RNG buckets = 31458/32768; Exact pass rate = 96.002197266%; Normal first T = 4700; Hard first T = 3100; Grueling first T = 2300

- Suffix threshold = 97; RNG buckets = 31785/32768; Exact pass rate = 97.000122070%; Normal first T = 4750; Hard first T = 3134; Grueling first T = 2325

- Suffix threshold = 98; RNG buckets = 32113/32768; Exact pass rate = 98.001098633%; Normal first T = 4800; Hard first T = 3167; Grueling first T = 2350

- Suffix threshold = 99; RNG buckets = 32441/32768; Exact pass rate = 99.002075195%; Normal first T = 4850; Hard first T = 3200; Grueling first T = 2375

- Suffix threshold = 100; RNG buckets = 32768/32768; Exact pass rate = 100.000000000%; Normal first T = 4900; Hard first T = 3234; Grueling first T = 2400

## 7. All Suffix breakpoints in the high-frequency practical ET range (0-800)

For quick farming reference, the table below lists only T values from 0 to 800 at which at least one difficulty changes tier. The threshold on a row takes effect from that T onward and remains unchanged until the next breakpoint for that difficulty.

- T start = 0; Normal threshold/rate = 2 / 2.001953%; Hard threshold/rate = 3 / 3.002930%; Grueling threshold/rate = 4 / 4.000854%

- T start = 25; Normal threshold/rate = 2 / 2.001953%; Hard threshold/rate = 3 / 3.002930%; Grueling threshold/rate = 5 / 5.001831%

- T start = 34; Normal threshold/rate = 2 / 2.001953%; Hard threshold/rate = 4 / 4.000854%; Grueling threshold/rate = 5 / 5.001831%

- T start = 50; Normal threshold/rate = 3 / 3.002930%; Hard threshold/rate = 4 / 4.000854%; Grueling threshold/rate = 6 / 6.002808%

- T start = 67; Normal threshold/rate = 3 / 3.002930%; Hard threshold/rate = 5 / 5.001831%; Grueling threshold/rate = 6 / 6.002808%

- T start = 75; Normal threshold/rate = 3 / 3.002930%; Hard threshold/rate = 5 / 5.001831%; Grueling threshold/rate = 7 / 7.000732%

- T start = 100; Normal threshold/rate = 4 / 4.000854%; Hard threshold/rate = 6 / 6.002808%; Grueling threshold/rate = 8 / 8.001709%

- T start = 125; Normal threshold/rate = 4 / 4.000854%; Hard threshold/rate = 6 / 6.002808%; Grueling threshold/rate = 9 / 9.002686%

- T start = 134; Normal threshold/rate = 4 / 4.000854%; Hard threshold/rate = 7 / 7.000732%; Grueling threshold/rate = 9 / 9.002686%

- T start = 150; Normal threshold/rate = 5 / 5.001831%; Hard threshold/rate = 7 / 7.000732%; Grueling threshold/rate = 10 / 10.000610%

- T start = 167; Normal threshold/rate = 5 / 5.001831%; Hard threshold/rate = 8 / 8.001709%; Grueling threshold/rate = 10 / 10.000610%

- T start = 175; Normal threshold/rate = 5 / 5.001831%; Hard threshold/rate = 8 / 8.001709%; Grueling threshold/rate = 11 / 11.001587%

- T start = 200; Normal threshold/rate = 6 / 6.002808%; Hard threshold/rate = 9 / 9.002686%; Grueling threshold/rate = 12 / 12.002563%

- T start = 225; Normal threshold/rate = 6 / 6.002808%; Hard threshold/rate = 9 / 9.002686%; Grueling threshold/rate = 13 / 13.000488%

- T start = 234; Normal threshold/rate = 6 / 6.002808%; Hard threshold/rate = 10 / 10.000610%; Grueling threshold/rate = 13 / 13.000488%

- T start = 250; Normal threshold/rate = 7 / 7.000732%; Hard threshold/rate = 10 / 10.000610%; Grueling threshold/rate = 14 / 14.001465%

- T start = 267; Normal threshold/rate = 7 / 7.000732%; Hard threshold/rate = 11 / 11.001587%; Grueling threshold/rate = 14 / 14.001465%

- T start = 275; Normal threshold/rate = 7 / 7.000732%; Hard threshold/rate = 11 / 11.001587%; Grueling threshold/rate = 15 / 15.002441%

- T start = 300; Normal threshold/rate = 8 / 8.001709%; Hard threshold/rate = 12 / 12.002563%; Grueling threshold/rate = 16 / 16.000366%

- T start = 325; Normal threshold/rate = 8 / 8.001709%; Hard threshold/rate = 12 / 12.002563%; Grueling threshold/rate = 17 / 17.001343%

- T start = 334; Normal threshold/rate = 8 / 8.001709%; Hard threshold/rate = 13 / 13.000488%; Grueling threshold/rate = 17 / 17.001343%

- T start = 350; Normal threshold/rate = 9 / 9.002686%; Hard threshold/rate = 13 / 13.000488%; Grueling threshold/rate = 18 / 18.002319%

- T start = 367; Normal threshold/rate = 9 / 9.002686%; Hard threshold/rate = 14 / 14.001465%; Grueling threshold/rate = 18 / 18.002319%

- T start = 375; Normal threshold/rate = 9 / 9.002686%; Hard threshold/rate = 14 / 14.001465%; Grueling threshold/rate = 19 / 19.000244%

- T start = 400; Normal threshold/rate = 10 / 10.000610%; Hard threshold/rate = 15 / 15.002441%; Grueling threshold/rate = 20 / 20.001221%

- T start = 425; Normal threshold/rate = 10 / 10.000610%; Hard threshold/rate = 15 / 15.002441%; Grueling threshold/rate = 21 / 21.002197%

- T start = 434; Normal threshold/rate = 10 / 10.000610%; Hard threshold/rate = 16 / 16.000366%; Grueling threshold/rate = 21 / 21.002197%

- T start = 450; Normal threshold/rate = 11 / 11.001587%; Hard threshold/rate = 16 / 16.000366%; Grueling threshold/rate = 22 / 22.000122%

- T start = 467; Normal threshold/rate = 11 / 11.001587%; Hard threshold/rate = 17 / 17.001343%; Grueling threshold/rate = 22 / 22.000122%

- T start = 475; Normal threshold/rate = 11 / 11.001587%; Hard threshold/rate = 17 / 17.001343%; Grueling threshold/rate = 23 / 23.001099%

- T start = 500; Normal threshold/rate = 12 / 12.002563%; Hard threshold/rate = 18 / 18.002319%; Grueling threshold/rate = 24 / 24.002075%

- T start = 525; Normal threshold/rate = 12 / 12.002563%; Hard threshold/rate = 18 / 18.002319%; Grueling threshold/rate = 25 / 25.000000%

- T start = 534; Normal threshold/rate = 12 / 12.002563%; Hard threshold/rate = 19 / 19.000244%; Grueling threshold/rate = 25 / 25.000000%

- T start = 550; Normal threshold/rate = 13 / 13.000488%; Hard threshold/rate = 19 / 19.000244%; Grueling threshold/rate = 26 / 26.000977%

- T start = 567; Normal threshold/rate = 13 / 13.000488%; Hard threshold/rate = 20 / 20.001221%; Grueling threshold/rate = 26 / 26.000977%

- T start = 575; Normal threshold/rate = 13 / 13.000488%; Hard threshold/rate = 20 / 20.001221%; Grueling threshold/rate = 27 / 27.001953%

- T start = 600; Normal threshold/rate = 14 / 14.001465%; Hard threshold/rate = 21 / 21.002197%; Grueling threshold/rate = 28 / 28.002930%

- T start = 625; Normal threshold/rate = 14 / 14.001465%; Hard threshold/rate = 21 / 21.002197%; Grueling threshold/rate = 29 / 29.000854%

- T start = 634; Normal threshold/rate = 14 / 14.001465%; Hard threshold/rate = 22 / 22.000122%; Grueling threshold/rate = 29 / 29.000854%

- T start = 650; Normal threshold/rate = 15 / 15.002441%; Hard threshold/rate = 22 / 22.000122%; Grueling threshold/rate = 30 / 30.001831%

- T start = 667; Normal threshold/rate = 15 / 15.002441%; Hard threshold/rate = 23 / 23.001099%; Grueling threshold/rate = 30 / 30.001831%

- T start = 675; Normal threshold/rate = 15 / 15.002441%; Hard threshold/rate = 23 / 23.001099%; Grueling threshold/rate = 31 / 31.002808%

- T start = 700; Normal threshold/rate = 16 / 16.000366%; Hard threshold/rate = 24 / 24.002075%; Grueling threshold/rate = 32 / 32.000732%

- T start = 725; Normal threshold/rate = 16 / 16.000366%; Hard threshold/rate = 24 / 24.002075%; Grueling threshold/rate = 33 / 33.001709%

- T start = 734; Normal threshold/rate = 16 / 16.000366%; Hard threshold/rate = 25 / 25.000000%; Grueling threshold/rate = 33 / 33.001709%

- T start = 750; Normal threshold/rate = 17 / 17.001343%; Hard threshold/rate = 25 / 25.000000%; Grueling threshold/rate = 34 / 34.002686%

- T start = 767; Normal threshold/rate = 17 / 17.001343%; Hard threshold/rate = 26 / 26.000977%; Grueling threshold/rate = 34 / 34.002686%

- T start = 775; Normal threshold/rate = 17 / 17.001343%; Hard threshold/rate = 26 / 26.000977%; Grueling threshold/rate = 35 / 35.000610%

- T start = 800; Normal threshold/rate = 18 / 18.002319%; Hard threshold/rate = 27 / 27.001953%; Grueling threshold/rate = 36 / 36.001587%

## 8. Several ET examples repeatedly used in practice

- Effective T = 0; Normal = 2 / 2.001953%; Hard = 3 / 3.002930%; Grueling = 4 / 4.000854%

- Effective T = 25; Normal = 2 / 2.001953%; Hard = 3 / 3.002930%; Grueling = 5 / 5.001831%

- Effective T = 50; Normal = 3 / 3.002930%; Hard = 4 / 4.000854%; Grueling = 6 / 6.002808%

- Effective T = 75; Normal = 3 / 3.002930%; Hard = 5 / 5.001831%; Grueling = 7 / 7.000732%

- Effective T = 100; Normal = 4 / 4.000854%; Hard = 6 / 6.002808%; Grueling = 8 / 8.001709%

- Effective T = 125; Normal = 4 / 4.000854%; Hard = 6 / 6.002808%; Grueling = 9 / 9.002686%

- Effective T = 150; Normal = 5 / 5.001831%; Hard = 7 / 7.000732%; Grueling = 10 / 10.000610%

- Effective T = 200; Normal = 6 / 6.002808%; Hard = 9 / 9.002686%; Grueling = 12 / 12.002563%

- Effective T = 234; Normal = 6 / 6.002808%; Hard = 10 / 10.000610%; Grueling = 13 / 13.000488%

- Effective T = 300; Normal = 8 / 8.001709%; Hard = 12 / 12.002563%; Grueling = 16 / 16.000366%

- Effective T = 330; Normal = 8 / 8.001709%; Hard = 12 / 12.002563%; Grueling = 17 / 17.001343%

- Effective T = 355; Normal = 9 / 9.002686%; Hard = 13 / 13.000488%; Grueling = 18 / 18.002319%

- Effective T = 405; Normal = 10 / 10.000610%; Hard = 15 / 15.002441%; Grueling = 20 / 20.001221%

- Effective T = 605; Normal = 14 / 14.001465%; Hard = 21 / 21.002197%; Grueling = 28 / 28.002930%

- Effective T = 630; Normal = 14 / 14.001465%; Hard = 21 / 21.002197%; Grueling = 29 / 29.000854%

- Effective T = 634; Normal = 14 / 14.001465%; Hard = 22 / 22.000122%; Grueling = 29 / 29.000854%

- Effective T = 650; Normal = 15 / 15.002441%; Hard = 22 / 22.000122%; Grueling = 30 / 30.001831%

- Effective T = 667; Normal = 15 / 15.002441%; Hard = 23 / 23.001099%; Grueling = 30 / 30.001831%

- Effective T = 680; Normal = 15 / 15.002441%; Hard = 23 / 23.001099%; Grueling = 31 / 31.002808%

Several especially easy-to-misread points:

- T=605 and T=630: Hard remains at threshold 21 in both cases, so those additional 25 ET points provide no actual improvement to Hard Suffix probability.

- T=630 → T=634: Hard steps from 21 to 22; only then does a real probability increase occur.

- T=630 → T=650: Normal steps from 14→15 and Grueling from 29→30; Hard already made its step earlier at 634.

- T=667: Hard reaches threshold 23 for the first time.

- Grueling advances exactly one tier per +25 T; Normal per +50 T; because 100/3 is not an integer, Hard follows a repeating 34, 33, 33 breakpoint pattern.

## 9. Prefix: general formula and source dependence

ET affects FUN_0803AB4C in the same way as Suffix: it adds floor(B×T/100) to base value B, but Prefix is then explicitly capped at 100.

if (special item branch) {
    B = 20;
} else if (source < 0x53) {
    B = DAT_08057F7B[source*8] + DAT_0300415C;
} else {
    B = DAT_0300415C + 20;
}

PrefixThreshold = B + floor(B * T / 100);
if (PrefixThreshold > 100) PrefixThreshold = 100;

- Code anchor: decompilation document pp. 971-972, FUN_0803AB4C.

Therefore, Prefix breakpoints cannot be determined from ET alone; they also depend on the current source and drop level DAT_0300415C. The same T can produce completely different Prefix gates on a 0x53 container and on a +100 Prefix enemy source.

## 10. 0x53 / source≥0x53: all Prefix steps at the max drop tier with B=30

At the commonly used max drop tier DAT_0300415C=10, the Prefix base for source≥0x53 is B=10+20=30. The threshold starts at 30 and reaches the permanent 100 cap for the first time at T=234.

- Prefix threshold = 30; Effective T range = 0–3; RNG buckets = 9831/32768; Exact pass rate = 30.001831055%

- Prefix threshold = 31; Effective T range = 4–6; RNG buckets = 10159/32768; Exact pass rate = 31.002807617%

- Prefix threshold = 32; Effective T range = 7–9; RNG buckets = 10486/32768; Exact pass rate = 32.000732422%

- Prefix threshold = 33; Effective T range = 10–13; RNG buckets = 10814/32768; Exact pass rate = 33.001708984%

- Prefix threshold = 34; Effective T range = 14–16; RNG buckets = 11142/32768; Exact pass rate = 34.002685547%

- Prefix threshold = 35; Effective T range = 17–19; RNG buckets = 11469/32768; Exact pass rate = 35.000610352%

- Prefix threshold = 36; Effective T range = 20–23; RNG buckets = 11797/32768; Exact pass rate = 36.001586914%

- Prefix threshold = 37; Effective T range = 24–26; RNG buckets = 12125/32768; Exact pass rate = 37.002563477%

- Prefix threshold = 38; Effective T range = 27–29; RNG buckets = 12452/32768; Exact pass rate = 38.000488281%

- Prefix threshold = 39; Effective T range = 30–33; RNG buckets = 12780/32768; Exact pass rate = 39.001464844%

- Prefix threshold = 40; Effective T range = 34–36; RNG buckets = 13108/32768; Exact pass rate = 40.002441406%

- Prefix threshold = 41; Effective T range = 37–39; RNG buckets = 13435/32768; Exact pass rate = 41.000366211%

- Prefix threshold = 42; Effective T range = 40–43; RNG buckets = 13763/32768; Exact pass rate = 42.001342773%

- Prefix threshold = 43; Effective T range = 44–46; RNG buckets = 14091/32768; Exact pass rate = 43.002319336%

- Prefix threshold = 44; Effective T range = 47–49; RNG buckets = 14418/32768; Exact pass rate = 44.000244141%

- Prefix threshold = 45; Effective T range = 50–53; RNG buckets = 14746/32768; Exact pass rate = 45.001220703%

- Prefix threshold = 46; Effective T range = 54–56; RNG buckets = 15074/32768; Exact pass rate = 46.002197266%

- Prefix threshold = 47; Effective T range = 57–59; RNG buckets = 15401/32768; Exact pass rate = 47.000122070%

- Prefix threshold = 48; Effective T range = 60–63; RNG buckets = 15729/32768; Exact pass rate = 48.001098633%

- Prefix threshold = 49; Effective T range = 64–66; RNG buckets = 16057/32768; Exact pass rate = 49.002075195%

- Prefix threshold = 50; Effective T range = 67–69; RNG buckets = 16384/32768; Exact pass rate = 50.000000000%

- Prefix threshold = 51; Effective T range = 70–73; RNG buckets = 16712/32768; Exact pass rate = 51.000976562%

- Prefix threshold = 52; Effective T range = 74–76; RNG buckets = 17040/32768; Exact pass rate = 52.001953125%

- Prefix threshold = 53; Effective T range = 77–79; RNG buckets = 17368/32768; Exact pass rate = 53.002929688%

- Prefix threshold = 54; Effective T range = 80–83; RNG buckets = 17695/32768; Exact pass rate = 54.000854492%

- Prefix threshold = 55; Effective T range = 84–86; RNG buckets = 18023/32768; Exact pass rate = 55.001831055%

- Prefix threshold = 56; Effective T range = 87–89; RNG buckets = 18351/32768; Exact pass rate = 56.002807617%

- Prefix threshold = 57; Effective T range = 90–93; RNG buckets = 18678/32768; Exact pass rate = 57.000732422%

- Prefix threshold = 58; Effective T range = 94–96; RNG buckets = 19006/32768; Exact pass rate = 58.001708984%

- Prefix threshold = 59; Effective T range = 97–99; RNG buckets = 19334/32768; Exact pass rate = 59.002685547%

- Prefix threshold = 60; Effective T range = 100–103; RNG buckets = 19661/32768; Exact pass rate = 60.000610352%

- Prefix threshold = 61; Effective T range = 104–106; RNG buckets = 19989/32768; Exact pass rate = 61.001586914%

- Prefix threshold = 62; Effective T range = 107–109; RNG buckets = 20317/32768; Exact pass rate = 62.002563477%

- Prefix threshold = 63; Effective T range = 110–113; RNG buckets = 20644/32768; Exact pass rate = 63.000488281%

- Prefix threshold = 64; Effective T range = 114–116; RNG buckets = 20972/32768; Exact pass rate = 64.001464844%

- Prefix threshold = 65; Effective T range = 117–119; RNG buckets = 21300/32768; Exact pass rate = 65.002441406%

- Prefix threshold = 66; Effective T range = 120–123; RNG buckets = 21627/32768; Exact pass rate = 66.000366211%

- Prefix threshold = 67; Effective T range = 124–126; RNG buckets = 21955/32768; Exact pass rate = 67.001342773%

- Prefix threshold = 68; Effective T range = 127–129; RNG buckets = 22283/32768; Exact pass rate = 68.002319336%

- Prefix threshold = 69; Effective T range = 130–133; RNG buckets = 22610/32768; Exact pass rate = 69.000244141%

- Prefix threshold = 70; Effective T range = 134–136; RNG buckets = 22938/32768; Exact pass rate = 70.001220703%

- Prefix threshold = 71; Effective T range = 137–139; RNG buckets = 23266/32768; Exact pass rate = 71.002197266%

- Prefix threshold = 72; Effective T range = 140–143; RNG buckets = 23593/32768; Exact pass rate = 72.000122070%

- Prefix threshold = 73; Effective T range = 144–146; RNG buckets = 23921/32768; Exact pass rate = 73.001098633%

- Prefix threshold = 74; Effective T range = 147–149; RNG buckets = 24249/32768; Exact pass rate = 74.002075195%

- Prefix threshold = 75; Effective T range = 150–153; RNG buckets = 24576/32768; Exact pass rate = 75.000000000%

- Prefix threshold = 76; Effective T range = 154–156; RNG buckets = 24904/32768; Exact pass rate = 76.000976562%

- Prefix threshold = 77; Effective T range = 157–159; RNG buckets = 25232/32768; Exact pass rate = 77.001953125%

- Prefix threshold = 78; Effective T range = 160–163; RNG buckets = 25560/32768; Exact pass rate = 78.002929688%

- Prefix threshold = 79; Effective T range = 164–166; RNG buckets = 25887/32768; Exact pass rate = 79.000854492%

- Prefix threshold = 80; Effective T range = 167–169; RNG buckets = 26215/32768; Exact pass rate = 80.001831055%

- Prefix threshold = 81; Effective T range = 170–173; RNG buckets = 26543/32768; Exact pass rate = 81.002807617%

- Prefix threshold = 82; Effective T range = 174–176; RNG buckets = 26870/32768; Exact pass rate = 82.000732422%

- Prefix threshold = 83; Effective T range = 177–179; RNG buckets = 27198/32768; Exact pass rate = 83.001708984%

- Prefix threshold = 84; Effective T range = 180–183; RNG buckets = 27526/32768; Exact pass rate = 84.002685547%

- Prefix threshold = 85; Effective T range = 184–186; RNG buckets = 27853/32768; Exact pass rate = 85.000610352%

- Prefix threshold = 86; Effective T range = 187–189; RNG buckets = 28181/32768; Exact pass rate = 86.001586914%

- Prefix threshold = 87; Effective T range = 190–193; RNG buckets = 28509/32768; Exact pass rate = 87.002563477%

- Prefix threshold = 88; Effective T range = 194–196; RNG buckets = 28836/32768; Exact pass rate = 88.000488281%

- Prefix threshold = 89; Effective T range = 197–199; RNG buckets = 29164/32768; Exact pass rate = 89.001464844%

- Prefix threshold = 90; Effective T range = 200–203; RNG buckets = 29492/32768; Exact pass rate = 90.002441406%

- Prefix threshold = 91; Effective T range = 204–206; RNG buckets = 29819/32768; Exact pass rate = 91.000366211%

- Prefix threshold = 92; Effective T range = 207–209; RNG buckets = 30147/32768; Exact pass rate = 92.001342773%

- Prefix threshold = 93; Effective T range = 210–213; RNG buckets = 30475/32768; Exact pass rate = 93.002319336%

- Prefix threshold = 94; Effective T range = 214–216; RNG buckets = 30802/32768; Exact pass rate = 94.000244141%

- Prefix threshold = 95; Effective T range = 217–219; RNG buckets = 31130/32768; Exact pass rate = 95.001220703%

- Prefix threshold = 96; Effective T range = 220–223; RNG buckets = 31458/32768; Exact pass rate = 96.002197266%

- Prefix threshold = 97; Effective T range = 224–226; RNG buckets = 31785/32768; Exact pass rate = 97.000122070%

- Prefix threshold = 98; Effective T range = 227–229; RNG buckets = 32113/32768; Exact pass rate = 98.001098633%

- Prefix threshold = 99; Effective T range = 230–233; RNG buckets = 32441/32768; Exact pass rate = 99.002075195%

- Prefix threshold = 100; Effective T range = 234 and above; RNG buckets = 32768/32768; Exact pass rate = 100.000000000%

## 11. T required to cap Prefix: general conversion

If the Prefix base value B for a source at the current drop level is known, the cap condition is:

B + floor(B*T/100) >= 100
T_cap = ceil(100 * (100 - B) / B),   when B < 100
T_cap = 0,                            when B >= 100

The following examples use the max drop tier L=10. For source<0x53, if its table Prefix bonus is A, then B=10+A. This is a general mathematical conversion table; it does not imply that every possible A value necessarily exists in the ROM source table.

- Source-table Prefix bonus A = +0; Max-tier base B=10+A = 10; T first required for 100% = 900

- Source-table Prefix bonus A = +5; Max-tier base B=10+A = 15; T first required for 100% = 567

- Source-table Prefix bonus A = +10; Max-tier base B=10+A = 20; T first required for 100% = 400

- Source-table Prefix bonus A = +15; Max-tier base B=10+A = 25; T first required for 100% = 300

- Source-table Prefix bonus A = +20; Max-tier base B=10+A = 30; T first required for 100% = 234

- Source-table Prefix bonus A = +25; Max-tier base B=10+A = 35; T first required for 100% = 186

- Source-table Prefix bonus A = +30; Max-tier base B=10+A = 40; T first required for 100% = 150

- Source-table Prefix bonus A = +35; Max-tier base B=10+A = 45; T first required for 100% = 123

- Source-table Prefix bonus A = +40; Max-tier base B=10+A = 50; T first required for 100% = 100

- Source-table Prefix bonus A = +45; Max-tier base B=10+A = 55; T first required for 100% = 82

- Source-table Prefix bonus A = +50; Max-tier base B=10+A = 60; T first required for 100% = 67

- Source-table Prefix bonus A = +55; Max-tier base B=10+A = 65; T first required for 100% = 54

- Source-table Prefix bonus A = +60; Max-tier base B=10+A = 70; T first required for 100% = 43

- Source-table Prefix bonus A = +65; Max-tier base B=10+A = 75; T first required for 100% = 34

- Source-table Prefix bonus A = +70; Max-tier base B=10+A = 80; T first required for 100% = 25

- Source-table Prefix bonus A = +75; Max-tier base B=10+A = 85; T first required for 100% = 18

- Source-table Prefix bonus A = +80; Max-tier base B=10+A = 90; T first required for 100% = 12

- Source-table Prefix bonus A = +85; Max-tier base B=10+A = 95; T first required for 100% = 6

- Source-table Prefix bonus A = +90; Max-tier base B=10+A = 100; T first required for 100% = 0

- Source-table Prefix bonus A = +95; Max-tier base B=10+A = 105; T first required for 100% = 0

- Source-table Prefix bonus A = +100; Max-tier base B=10+A = 110; T first required for 100% = 0

Confirmed key real sources:

- Source category = 0x53 (and the source≥0x53 branch in AB4C); Prefix base = B=30（L=10）; ET cap point = Caps at T=234; Notes = A 100% equipment roll and a 100% Prefix gate are separate concepts; at low T, Prefix can still be below 100%.

- Source category = 0x27、0x3C–0x42、0x44、0x45、0x50; Prefix base = Prefix-table bonus +100, max-tier B≥100; ET cap point = Already capped at T=0; Notes = Theoretically strongest enemy sources; mapping on-screen enemy names to source IDs still requires runtime verification.

- Source category = Other Class-D sources; Prefix base = Known to range from +15 to +95; ET cap point = Calculate from each source's own B; Notes = Even with a 100% equipment roll, Prefix gates can differ.

## 12. Probability stages that ET explicitly does not change

- Stage = First-layer monster drop gate; Conclusion about ET's effect = Controlled by the source record and FUN_0803A6E8 / A77C; ET is not a direct multiplier here.

- Stage = source→full equipment-roll probability; Conclusion about ET's effect = For example, 0x53 has a 100% equipment roll while a normal Class-A source is about 16.498962417%; raising ET does not increase this entry gate itself.

- Stage = Character equipment-type weights; Conclusion about ET's effect = Determined by DAT_0806DBAC Profile; ET does not change type 0/A/C/D weights.

- Stage = Specific base-item candidate probability; Conclusion about ET's effect = Determined by the candidate pool and level logic in FUN_0803A844; ET is not a candidate-index weight.

- Stage = Map Prefix range and 85/10/5 bypasses; Conclusion about ET's effect = Determined by map region, Profile bypasses, and difficulty truncation; ET only determines whether a Prefix is generated, not the target Prefix's position within the pool.

- Stage = Suffix tier / in-pool probability of a specific 0x9B; Conclusion about ET's effect = ET raises the gate for whether a Suffix is generated; which suffix segment is entered and which suffix within that segment is selected still depend on other randomness and difficulty truncation.

## 13. Quick formula for deciding whether a little more ET helps

When farming equipment, first decide whether the quantity you care about is Prefix or Suffix:

Suffix:
  q_now = B + floor(B*T/100)
  T_next = ceil(100 * (q_now + 1 - B) / B)

Prefix:
  q_now = min(100, B + floor(B*T/100))
  if q_now == 100: additional ET no longer affects Prefix
  otherwise T_next = ceil(100 * (q_now + 1 - B) / B)

As long as the new T is still below T_next, you are in a range where the displayed value increases but the machine-code probability does not change at all.

- T example = 75; Normal = Threshold 3; next step T=100; Hard = Threshold 5; next step T=100; Grueling = Threshold 7; next step T=100

- T example = 100; Normal = Threshold 4; next step T=150; Hard = Threshold 6; next step T=134; Grueling = Threshold 8; next step T=125

- T example = 234; Normal = Threshold 6; next step T=250; Hard = Threshold 10; next step T=267; Grueling = Threshold 13; next step T=250

- T example = 405; Normal = Threshold 10; next step T=450; Hard = Threshold 15; next step T=434; Grueling = Threshold 20; next step T=425

- T example = 605; Normal = Threshold 14; next step T=650; Hard = Threshold 21; next step T=634; Grueling = Threshold 28; next step T=625

- T example = 630; Normal = Threshold 14; next step T=650; Hard = Threshold 21; next step T=634; Grueling = Threshold 29; next step T=650

- T example = 680; Normal = Threshold 15; next step T=700; Hard = Threshold 23; next step T=700; Grueling = Threshold 31; next step T=700

## 14. Why the 'best difficulty' for a target item can change with T

ET step changes control only one part of the Prefix/Suffix generation gates. The final product probability must also be multiplied by the probability of the target Prefix within the difficulty-truncated pool and the probability of the target Suffix within its truncated pool. Therefore, whether Hard or Grueling is better is not a fixed rule; the ranking can reverse at a T breakpoint.

- Target example = Blood Pirate + of the Fates gloves/boots; Previously derived difficulty result = Hard has a narrower 0x51 Prefix pool; prior high-T analysis found Hard slightly better; Reason = You cannot judge only from Grueling's higher base Suffix gate.

- Target example = Dark Sapphire + of the Fates accessory; Previously derived difficulty result = Grueling is slightly better at T=75; in the existing analysis, Hard overtakes at T=100; Reason = This is a target-specific result caused by the combined effects of Prefix concentration and the Suffix gate.

- Target example = Spoon + any valid Suffix; Previously derived difficulty result = Grueling is usually used to preserve late-pool visual Prefixes; Reason = Some Prefixes are truncated out on Hard/Normal; ET cannot restore candidates directly removed by the difficulty cutoff.

## 15. Most important practical rules

- Calculate effective T first instead of looking only at the equipment panel's ET value.

- Determine the source's Prefix base B first; the same T can have more than a twofold difference in Prefix effect across different sources.

- After Prefix is capped at 100%, the main value of further ET shifts to the Suffix gate.

- Suffix always changes in integer steps; ET that does not cross the next breakpoint gives no probability benefit.

- Hard is the easiest difficulty to get stuck between breakpoints: for example, T630→633 changes nothing, and T634 is the first real improvement.

- Grueling has the cleanest Suffix breakpoints: every +25 T raises the base gate by 1.

- Normal has the sparsest Suffix breakpoints: every +50 T raises it by 1.

- Difficulty truncation directly removes candidates from the tail of the pool. No amount of ET can restore Prefix/Suffix candidates removed by the difficulty cutoff.

- ET does not change character type weights, source→equipment entry rates, base-item candidate probabilities, or map-pool structure, so not every improvement in drop rate should be attributed to ET.

## 16. Evidence anchors and scope boundaries

- No. = E1; Evidence / purpose = GBA Return of the King Decompilation Code - Corrected Version, p. 962: FUN_0803A4BC, implementation mapping a 15-bit random value to a closed interval.

- No. = E2; Evidence / purpose = Same document, pp. 971-972: FUN_0803AB4C, Prefix base B, ET percentage adjustment, 100 cap, map region/difficulty truncation.

- No. = E3; Evidence / purpose = Same document, pp. 972-973: FUN_0803ACC4, Normal/Hard/Grueling base gates 2/3/4 and ET adjustment.

- No. = E4; Evidence / purpose = Same document, p. 831: FUN_08032A5C, essentially integer (base×T)/100, which creates the discrete step changes.

- No. = E5; Evidence / purpose = Same document, pp. 966-968 and 975-976: FUN_0803A6E8 / A77C / AE8C, showing the call order between the source entry logic and the ET quality parameter.

- No. = E6; Evidence / purpose = GBA Return of the King - Final Spoon Farming Checklist - Dual-Affix Red-Name Non-Fragile VFX, Full Probability Version: common ET/Suffix tiers and the 0x53/Class-D source summary.

- No. = E7; Evidence / purpose = GBA Return of the King - Blood Pirate of the Fates Gloves/Boots - 15 Conclusions, Code-Evidence Version: 0x53 max-tier B=30, Prefix cap at T=234, and the established Keen Eyes/Dwarf Sense conversion convention.

- No. = E8; Evidence / purpose = GBA Return of the King - Dark Sapphire of the Fates Accessory - Machine-Code Optimal Farming Method, Full Version: target-specific difficulty reversals at T=75 and T=100, plus source-priority examples.

Locked conclusion: ET does not mean 'every +1% gives +1% probability'; it takes effect only when an integer machine threshold is crossed.
