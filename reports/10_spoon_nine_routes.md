# GBA The Lord of the Rings: The Return of the King - Updated Spoon Farming Checklist
## Target: a red-name Spoon with both Prefix and Suffix, non-Fragile, with a specified body color and flame/starlight VFX
*Code-Evidence Edition · 9 Routes · Suffix Included in Final 1/xxxxx Odds · Current ET Recalculation · 2026-08-24*
- [SCOPE LOCK] Every “final probability / 1/xxxxx / best route” in this document refers to a complete finished item: Spoon (000) × specified visual Prefix × any valid Suffix (not FF) × red item name × non-Fragile. A Spoon with only the Prefix and no real Suffix does not count.
- [IMPORTANT CORRECTION] FUN_0803A844 applies a special retention gate to type-0 candidate 0 (Spoon). After candidate 0 is rolled, rand(1,100) keeps the Spoon only when the result is exactly 100; the other ~99% of outcomes increment the index. The base Spoon therefore has an additional ~1% retention gate.
- [NEW VISUAL CORRECTION] (1) 7C Snowbird's: blue weapon body + white starlight particles; (2) 40 Shadowed: the weapon body is semi-transparent / stealth-like, not pure purple; its code effect includes Nightburst; (3) 2F Blackflame: the pure-purple visual route belongs to Blackflame, whose code effect includes Fireburst.
# 2026-08-27 empirical revision — scope correction
Long-term testing after the route-analysis draft adds three important empirical boundaries. These do not invalidate the code-derived probability chains below, but they prevent route recommendations from being read as exclusivity claims.
- Spoon usability has been observed for Frodo, Sam, Gandalf, Aragorn, and Eowyn. A route's recommended character is therefore an efficiency/model choice, not proof that other characters cannot use Spoon.
- Spoon has been empirically observed in Northern Ithilien, Helm's Deep Act II, and Moria Dwarven Halls Act I; a single-affix Spoon has also been observed at Helm's Deep. No route below should be read as a claim that Spoon exists on only one map.
- Snowbird's has also been observed in the White Mountains. The Ice Cave route below remains a modeled/recommended route for the specified completed target, not an exclusivity statement for the Snowbird's Prefix.
When empirical observations and an older guide statement conflict, the collector edition keeps the code-derived calculation but uses the newer empirical observation to correct scope/exclusivity wording.
## 1. Final Calculation Baseline
- Item = Drop Level; Final Baseline = L=52 (max-level collection baseline); Code Location / Basis = DAT_0300415C; FUN_0803A844; Meaning = type-0 candidate pool fully expands to 18 entries
- Item = Difficulty; Final Baseline = Standardized to Grueling; Code Location / Basis = DAT_03005820; FUN_0803AB4C / FUN_0803ACC4; Meaning = Base Suffix gate=4%; Prefix pool is not truncated
- Item = Frodo route; Final Baseline = Equipment Extra Treasure=+605%; no Keen Eyes → T=605%; Code Location / Basis = DAT_03003CE4; FUN_08032A5C; Meaning = Prefix gate capped at 100%; Suffix threshold=28, exact 28.002929688%
- Item = Gandalf / Éowyn; Final Baseline = Equipment Extra Treasure=+555% + Keen Eyes 5/5=+25% → T=580%; Code Location / Basis = 555% is equipment ET; Keen Eyes is a separate +25 quality modifier; Meaning = Prefix gate=100%; Suffix threshold=27, exact 27.001953125%
- Item = 0x53 loot-bearing chest; Final Baseline = P(source→equipment)=100%; Code Location / Basis = FUN_0803A77C: rand(0x47,0x61); Meaning = All 71–97 outcomes enter the equipment branch
- Item = Best Class-D enemy source; Final Baseline = P(source→equipment)=100%; Code Location / Basis = source table m0=+45; gear range 71–100; Meaning = If an on-screen enemy maps to this source, every kill is one full equipment roll
- Item = Normal Class-A enemy; Final Baseline = P(source→equipment)=16.4989624%; Code Location / Basis = 55% drop gate × 30% equipment gate (exact machine bucket); Meaning = Conservative reference while on-screen enemy→source mapping remains incomplete
## 2. Final Probability Chain (Suffix Included)
P(qualified final item) = P(source reaches equipment generator) × P(type0 | character) × P(candidate0) × P(Spoon retention gate) × P(Prefix generation) × P(target Prefix | map pool) × P(Suffix generation) × I[non-Fragile].
- With the full candidate pool, candidate0 has 1821/32768 = 5.557250977%. The Spoon retention gate is 327/32768 = 0.997924805%. Their product leaves only 0.055457186% for type0 to actually retain a Spoon.
- A normal Class-A enemy reaches the full equipment generator with an exact rate of 16.498962417%, or about one full equipment roll per 6.061 kills.
## 3. Nine Final Routes: Complete 1/xxxxx Odds
- Color / VFX = Red body + flame + starlight; Target Prefix + Suffix = 42 Molten + any valid Suffix; Best Map = Crack of Doom; Best Character = Frodo; Fixed Story Map? = Yes; fixed Frodo story route; Recommended Difficulty = Standardized to Grueling; the Prefix itself does not strictly require it; Total T = 605%; Target Prefix Pool = 89.99939%; 0x53/Class-D: Final 1/x = 1 / 572,223; Normal Class-A: Final 1/x = 1 / 3,468,237
- Color / VFX = Red body + flame; Target Prefix + Suffix = 96 Burning + any valid Suffix; Best Map = Two large chests, Ithilien North; Best Character = Gandalf; Fixed Story Map? = Story map; Gandalf/Aragorn/Legolas/Gimli can reach it; Recommended Difficulty = Standardized to Grueling; Total T = 580%; Target Prefix Pool = 89.99939%; 0x53/Class-D: Final 1/x = 1 / 890,244; Normal Class-A: Final 1/x = 1 / 5,395,759
- Color / VFX = Green body + green starlight; Target Prefix + Suffix = 70 Frog's + any valid Suffix; Best Map = Amon Sul (best Prefix pool); Best Character = Frodo (best baseline when freely selectable); Fixed Story Map? = No; bonus map allows existing characters; Recommended Difficulty = Standardized to Grueling; Prefix minimum level 7; Total T = 605%; Target Prefix Pool = 85.00061%; 0x53/Class-D: Final 1/x = 1 / 605,875; Normal Class-A: Final 1/x = 1 / 3,672,200
- Color / VFX = Gold body + gold starlight; Target Prefix + Suffix = 81 Goldsmith's + any valid Suffix; Best Map = Ice Cave; Best Character = Éowyn; Fixed Story Map? = Yes; fixed Éowyn story route; Recommended Difficulty = Grueling recommended; Goldsmith's itself enters on Hard; Total T = 580%; Target Prefix Pool = 95.00122%; 0x53/Class-D: Final 1/x = 1 / 1,686,231; Normal Class-A: Final 1/x = 1 / 10,220,223
- Color / VFX = Blue body + white starlight; Target Prefix + Suffix = 7C Snowbird's + any valid Suffix; Best Map = Ice Cave; Best Character = Éowyn; Fixed Story Map? = Yes; fixed Éowyn story route; Recommended Difficulty = Grueling recommended; Snowbird's itself is already available on Normal, but Grueling gives the best completed two-affix odds at T=580; Total T = 580%; Target Prefix Pool = 95.00122%; 0x53/Class-D: Final 1/x = 1 / 1,686,231; Normal Class-A: Final 1/x = 1 / 10,220,223
- Color / VFX = White starlight; Target Prefix + Suffix = 89 Consecrated + any valid Suffix; Best Map = Pelennor Beacon / Gandalf route; Best Character = Gandalf; Fixed Story Map? = Yes; fixed Gandalf story route; Recommended Difficulty = Standardized to Grueling; Total T = 580%; Target Prefix Pool = 100.00000%; 0x53/Class-D: Final 1/x = 1 / 801,508; Normal Class-A: Final 1/x = 1 / 4,857,929
- Color / VFX = Gold body + yellow starlight; Target Prefix + Suffix = 8E Magnificent + any valid Suffix; Best Map = Pelennor Beacon / Gandalf route; Best Character = Gandalf; Fixed Story Map? = Yes; fixed Gandalf story route; Recommended Difficulty = Grueling required (8E is late in the 12-entry pool); Total T = 580%; Target Prefix Pool = 100.00000%; 0x53/Class-D: Final 1/x = 1 / 801,214; Normal Class-A: Final 1/x = 1 / 4,856,150
- Color / VFX = Semi-transparent body + dark-star / Nightburst; Target Prefix + Suffix = 40 Shadowed + any valid Suffix; Best Map = Crack of Doom; Best Character = Frodo; Fixed Story Map? = Yes; fixed Frodo story route; Recommended Difficulty = Standardized to Grueling; the Prefix itself does not strictly require it; Total T = 605%; Target Prefix Pool = 89.99939%; 0x53/Class-D: Final 1/x = 1 / 572,223; Normal Class-A: Final 1/x = 1 / 3,468,237
- Color / VFX = Pure-purple visual + black flame / Fireburst; Target Prefix + Suffix = 2F Blackflame + any valid Suffix; Best Map = Gorgoroth; Best Character = Frodo; Fixed Story Map? = Yes; fixed Frodo story route; Recommended Difficulty = Grueling required (2F is the final entry in 24–2F); Total T = 605%; Target Prefix Pool = 85.00061%; 0x53/Class-D: Final 1/x = 1 / 606,097; Normal Class-A: Final 1/x = 1 / 3,673,545
- [TWO LARGE CHESTS IN ITHILIEN NORTH] Gandalf has 555% equipment ET + Keen Eyes 5/5, for T=580%. The Burning route is about 1/890,244 per chest. If each reset opens two equivalent 0x53 chests, the long-run approximation for at least one qualified item is about 0.000224657% per two-chest reset, or roughly 1/445,122 resets. The GBA PRNG is deterministic, so the two chests are not assumed to be truly independent in short sequences.
## 4. New Visual Identification and Affix Corrections
- ID / Prefix = 7C Snowbird's; In-Game Visual Classification = Blue body + white starlight particles; Code-Table Stats = +2 Min. Lvl.; +1 Strength; +10% Speed; Updated Interpretation = New blue-route entry. Visual classification is based on in-game observation; ID/stats are code-table confirmed.
- ID / Prefix = 40 Shadowed; In-Game Visual Classification = Semi-transparent / stealth-like body + dark-star / Nightburst; Code-Table Stats = +3 Min. Lvl.; Nightburst; Cursed; +2 All Primary Stats; Updated Interpretation = Important: this is not a pure-purple weapon.
- ID / Prefix = 2F Blackflame; In-Game Visual Classification = Pure-purple visual + black flame / Fireburst; Code-Table Stats = +4 Min. Lvl.; +2 Fire Damage; Fireburst; +3 All Primary Stats; Updated Interpretation = The pure-purple visual route belongs here, not to Shadowed.
Snowbird's difficulty detail: it sits in the 0x78–0x83 12-entry Weapon Prefix pool and is still present after Normal truncation to 0x78–0x7F. If only the Prefix matters, Normal gives 12.5% in-pool. But for the completed red-name two-affix target at T=580, the Suffix gates are ~13.000488% on Normal, 20.001221% on Hard, and 27.001953% on Grueling. The complete 0x53/Class-D odds are therefore about 1/2,335,147 (Normal), 1/1,897,726 (Hard), and 1/1,686,231 (Grueling). Grueling remains the best finished-item difficulty.
Evidence boundary for visuals: 'blue body + white starlight,' 'Shadowed is semi-transparent,' and 'Blackflame is the pure-purple route' are in-game visual observations. The code tables directly confirm the IDs, stats, Nightburst on Shadowed, and Fireburst on Blackflame. A complete symbol-level mapping from every visual Prefix to palette/particle resource pointers is still unfinished.
## 5. Route-by-Route Farming Notes and Source Interpretation
1. Red Body + Flame + Starlight - 42 Molten
- Qualified structure: 42 Molten Spoon + any real valid Suffix; the target Prefix itself is non-Fragile.
- Route: Crack of Doom; Frodo; standardized to Grueling.
- Source: no 0x53 large loot chest is confirmed for the area; the main value uses an optimal Class-D source / equivalent full equipment roll, with a Class-A reference also shown.
- Rule: keep only Molten Spoons with a real Suffix; single-affix versions do not count.
- Breakdown: type0=15.002441%; Spoon candidate×retention=0.055457186%; Prefix gate=100%; target map-pool×in-pool roll=7.500864659%; Suffix gate=28.002929688%.
2. Red Body + Flame - 96 Burning
- Qualified structure: 96 Burning Spoon + any real valid Suffix; the target Prefix itself is non-Fragile.
- Route: the two large chests in Ithilien North; Gandalf; Grueling.
- Source: the chest pair has been verified in actual play; loot-bearing chests are evaluated as source 0x53.
- Rule: prioritize repeat resets of the two large chests; keep only Burning Spoons with a real Suffix.
- Breakdown: type0=10.000610%; Spoon candidate×retention=0.055457186%; Prefix gate=100%; target map-pool×in-pool roll=7.500864659%; Suffix gate=27.001953125%.
3. Green Body + Green Starlight - 70 Frog's
- Qualified structure: 70 Frog's Spoon + any real valid Suffix; the target Prefix itself is non-Fragile.
- Route: Amon Sul for the best Prefix pool; Frodo is the best type-0 baseline when characters can be freely selected; Grueling.
- Source: Amon Sul source IDs have not yet been fully mapped to on-screen enemy names, so the table gives both optimal Class-D and conservative Class-A values.
- Rule: Amon Sul has no confirmed large chest; the six Nazgûl are the primary practical targets. Paths of the Dead chests can serve as an alternative when roll efficiency per reset matters.
- Breakdown: type0=15.002441%; Spoon candidate×retention=0.055457186%; Prefix gate=100%; target map-pool×in-pool roll=7.084248867%; Suffix gate=28.002929688%.
4. Gold Body + Gold Starlight - 81 Goldsmith's
- Qualified structure: 81 Goldsmith's Spoon + any real valid Suffix; the target Prefix itself is non-Fragile.
- Route: Ice Cave; Éowyn; Grueling recommended.
- Source: Ice Cave guides confirm loot/chests, but whether a specific chest is 0x53 still requires object-level verification. Both 0x53/Class-D and Class-A values are therefore shown.
- Rule: Éowyn with Keen Eyes 5/5; keep only Goldsmith's Spoons with a real Suffix.
- Breakdown: type0=5.001831%; Spoon candidate×retention=0.055457186%; Prefix gate=100%; target map-pool×in-pool roll=7.917734794%; Suffix gate=27.001953125%.
5. Blue Body + White Starlight - 7C Snowbird's
- Qualified structure: 7C Snowbird's Spoon + any real valid Suffix; the target Prefix itself is non-Fragile.
- Visual / stats: blue body + white starlight; +2 Min. Lvl.; +1 Strength; +10% Speed.
- Route: Ice Cave; Éowyn; Grueling is best for the complete two-affix target. Snowbird's itself already exists in the Normal-truncated pool.
- Source: same Ice Cave source boundary as Goldsmith's; loot/chests are present, but the specific 0x53 object mapping is not yet fully closed.
- Breakdown: type0=5.001831%; Spoon candidate×retention=0.055457186%; Prefix gate=100%; target map-pool×in-pool roll=7.917734794%; Suffix gate=27.001953125%. Final odds match Goldsmith's under the current baseline.
6. White Starlight - 89 Consecrated
- Qualified structure: 89 Consecrated Spoon + any real valid Suffix; the target Prefix itself is non-Fragile.
- Route: Pelennor Beacon / Gandalf route; Gandalf; Grueling.
- Source: relevant Pelennor-area guides confirm chests; if a chest is loot-bearing, it is evaluated under the 0x53 column.
- Rule: the 0x84–0x8F pool branches fully overlap in this area, giving a 100% target-group rate; keep only red-name two-affix results.
- Breakdown: type0=10.000610%; Spoon candidate×retention=0.055457186%; Prefix gate=100%; target map-pool×in-pool roll=8.331298828%; Suffix gate=27.001953125%.
7. Gold Body + Yellow Starlight - 8E Magnificent
- Qualified structure: 8E Magnificent Spoon + any real valid Suffix; the target Prefix itself is non-Fragile.
- Route: Pelennor Beacon / Gandalf route; Gandalf; Grueling is mandatory.
- Source: prioritize a repeatable loot-bearing chest; otherwise use an enemy mapped to a Class-D source.
- Rule: Hard truncates 8E, so the target must be farmed on Grueling.
- Breakdown: type0=10.000610%; Spoon candidate×retention=0.055457186%; Prefix gate=100%; target map-pool×in-pool roll=8.334350586%; Suffix gate=27.001953125%.
8. Semi-Transparent Body + Dark-Star / Nightburst - 40 Shadowed
- Qualified structure: 40 Shadowed Spoon + any real valid Suffix; the target Prefix itself is non-Fragile.
- Visual correction: Shadowed is a semi-transparent / stealth-like weapon, not a pure-purple weapon. The code table confirms Nightburst, Cursed, and +2 All Primary Stats.
- Route: Crack of Doom; Frodo; Grueling.
- Source: same as Molten; no confirmed 0x53 large chest in the area, so optimal Class-D and conservative Class-A values are shown separately.
- Breakdown: type0=15.002441%; Spoon candidate×retention=0.055457186%; Prefix gate=100%; target map-pool×in-pool roll=7.500864659%; Suffix gate=28.002929688%.
9. Pure-Purple Visual + Black Flame / Fireburst - 2F Blackflame
- Qualified structure: 2F Blackflame Spoon + any real valid Suffix; the target Prefix itself is non-Fragile.
- Visual correction: the pure-purple visual belongs to Blackflame. The code table confirms +2 Fire Damage, Fireburst, and +3 All Primary Stats.
- Route: Gorgoroth; Frodo; Grueling is mandatory because 2F is the final entry in 0x24–0x2F.
- Source: the area guide does not confirm a stable large loot chest; prioritize enemies mapped to Class-D sources, with Class-A used as the conservative fallback.
- Breakdown: type0=15.002441%; Spoon candidate×retention=0.055457186%; Prefix gate=100%; target map-pool×in-pool roll=7.081654854%; Suffix gate=28.002929688%.
## 6. Effect of Extra Treasure / Keen Eyes on Final Odds
At code level, Extra Treasure does not simply mean “one more item drops.” In FUN_0803AB4C and FUN_0803ACC4 it is used as a percentage quality parameter and processed through the integer-percent function FUN_08032A5C to raise Prefix/Suffix thresholds. Current implementation: Frodo has 605% equipment ET; Gandalf/Éowyn have 555% equipment ET plus +25 from Keen Eyes 5/5.
- Total Quality Modifier T = 0%; Grueling Suffix Threshold = 4; Exact Machine-Code Suffix Rate = 4.000854492%
- Total Quality Modifier T = 25%; Grueling Suffix Threshold = 5; Exact Machine-Code Suffix Rate = 5.001831055%; Explanation = Typical contribution from Keen Eyes 5/5 alone
- Total Quality Modifier T = 50%; Grueling Suffix Threshold = 6; Exact Machine-Code Suffix Rate = 6.002807617%
- Total Quality Modifier T = 75%; Grueling Suffix Threshold = 7; Exact Machine-Code Suffix Rate = 7.000732422%
- Total Quality Modifier T = 100%; Grueling Suffix Threshold = 8; Exact Machine-Code Suffix Rate = 8.001708984%; Explanation = Legacy unified Frodo baseline; retained for conversion
- Total Quality Modifier T = 125%; Grueling Suffix Threshold = 9; Exact Machine-Code Suffix Rate = 9.002685547%; Explanation = Legacy unified Gandalf/Éowyn baseline; retained for conversion
- Total Quality Modifier T = 150%; Grueling Suffix Threshold = 10; Exact Machine-Code Suffix Rate = 10.000610352%
- Total Quality Modifier T = 175%; Grueling Suffix Threshold = 11; Exact Machine-Code Suffix Rate = 11.001586914%
- Total Quality Modifier T = 200%; Grueling Suffix Threshold = 12; Exact Machine-Code Suffix Rate = 12.002563477%
- Total Quality Modifier T = 250%; Grueling Suffix Threshold = 14; Exact Machine-Code Suffix Rate = 14.001464844%
- Total Quality Modifier T = 300%; Grueling Suffix Threshold = 16; Exact Machine-Code Suffix Rate = 16.000366211%
- Total Quality Modifier T = 330%; Grueling Suffix Threshold = 17; Exact Machine-Code Suffix Rate = 17.001342773%
- Total Quality Modifier T = 355%; Grueling Suffix Threshold = 18; Exact Machine-Code Suffix Rate = 18.002319336%
- Total Quality Modifier T = 405%; Grueling Suffix Threshold = 20; Exact Machine-Code Suffix Rate = 20.001220703%; Explanation = Historical Treasure Hunter Gimli example: 330 equipment +25 Keen Eyes +50 Dwarf Sense
- Total Quality Modifier T = 555%; Grueling Suffix Threshold = 26; Exact Machine-Code Suffix Rate = 26.000976562%; Explanation = Current Gandalf/Éowyn equipment ET alone; Keen Eyes excluded
- Total Quality Modifier T = 580%; Grueling Suffix Threshold = 27; Exact Machine-Code Suffix Rate = 27.001953125%; Explanation = Current Gandalf/Éowyn practical T: 555% equipment ET +25 Keen Eyes
- Total Quality Modifier T = 605%; Grueling Suffix Threshold = 28; Exact Machine-Code Suffix Rate = 28.002929688%; Explanation = Current Frodo practical T: 605% equipment ET; no Keen Eyes
- [CURRENT QUALITY CONVENTION] Equipment Extra Treasure: Frodo=605%, Gandalf=555%, Éowyn=555%. Under the project’s existing convention, Keen Eyes 5/5 is not part of the equipment ET field; it contributes an additional +25 quality modifier. Practical T values are therefore 605%, 580%, and 580%, with Grueling Suffix thresholds of 28 and 27.
## 7. Best Sources: Code-Level Ranking
- Source Type = 0x53 loot-bearing chest; Drop Gate = Directly enters A77C; Equipment Gate = rand 71–97 → 100% gear; Base Prefix Bonus = L+20; Conclusion = Most stable “one open = one full equipment roll” source
- Source Type = Class-D +100 Prefix sources: 0x27, 0x3C–0x42, 0x44, 0x45, 0x50; Drop Gate = m0=+45 → 100%; Equipment Gate = 71–100 → 100%; Base Prefix Bonus = +100; Conclusion = Theoretical best enemy sources; on-screen enemy-name mapping remains incomplete
- Source Type = Other Class-D sources; Drop Gate = 100%; Equipment Gate = 100%; Base Prefix Bonus = +15 to +95; Conclusion = Still more stable than normal enemies; Prefix gate varies by source ID
- Source Type = Normal Class-A source; Drop Gate = ~54.9988%; Equipment Gate = ~29.9988%; Base Prefix Bonus = Usually lower; Conclusion = Total reach=16.4989624%; conservative reference
## 8. Conclusion: Nine Routes; Still a ~572k–1.686m Full-Roll Hunt at Current T
- With the current implemented ET values, the nine routes fall between about 1/572,223 and 1/1,686,231 per full 0x53/Class-D equipment roll; normal Class-A enemies range from about 1/3,468,237 to 1/10,220,223. These figures already include a real Suffix.
- Adding Snowbird's does not expand the numerical range because it shares the Ice Cave 0x78–0x83 Weapon Prefix pool with Goldsmith's, and both receive 2731/32768 buckets at the current Grueling roll. Their final two-affix odds are identical under this baseline.
- Older tens-of-thousands estimates mainly omitted the Spoon-specific ~1% retention gate. This version continues to use the actual FUN_0803A844 control flow.
- The most effective optimization order remains: prioritize a 0x53 chest or Class-D source → Grueling → high Extra Treasure → max Keen Eyes where available → reset fixed chests.
- For visual collection, keep the classification explicit: Snowbird's = blue body + white starlight; Shadowed = semi-transparent, not pure purple; the pure-purple route is Blackflame.
- Any target Spoon that shows only the Prefix and no real Suffix is excluded from the finished-item totals in this document.
## 9. Evidence and Boundaries
- Decompilation anchors: FUN_0803A77C, FUN_0803A844, FUN_0803AB4C, FUN_0803ACC4, FUN_0803AE8C, FUN_08032A5C.
- Codebreaker item/affix guide: Spoon=000; 7C Snowbird's = +2 Min. Lvl., +1 Strength, +10% Speed; 40 Shadowed = Nightburst, Cursed, +2 All Primary Stats; 2F Blackflame = +2 Fire Damage, Fireburst, +3 All Primary Stats; plus the remaining Prefix/Suffix tables.
- Historical guides are used to cross-check map routes, character access, Keen Eyes (+5% better items per level), Dwarf Sense (+10% better items per level), and chest locations.
- Visual statements ('Snowbird's blue body + white starlight,' 'Shadowed is semi-transparent,' 'Blackflame is the pure-purple route') come from in-game visual observation. A complete symbol-level palette/particle resource mapping has not yet been closed for every visual Prefix.
- Enemy-source boundary: the full on-screen enemy-name to source-ID mapping is still incomplete. The document therefore does not force an Orc/Nazgûl name onto source 0x27/0x3C/etc. without evidence, and continues to show both optimal Class-D and conservative Class-A columns.
