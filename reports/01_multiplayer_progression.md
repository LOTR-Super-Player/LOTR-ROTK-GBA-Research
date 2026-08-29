# GBA The Lord of the Rings: The Return of the King
## Research Report on Multiplayer Map Stations and Progression Unlocking
*Unofficial reverse engineering / ROM, SAV, savestate, and observed gameplay analysis*
*Research status as of August 28, 2026*

**Data block 1 (converted from a table for copy/paste compatibility)**
- Scope: This report summarizes the research completed through August 28, 2026 on the first four true Multiplayer / Link map groups in The Return of the King, including station structure, Host/Client authority, SAV persistence, and progression-writing logic. Any point not closed by machine-code analysis or paired SAV comparison is explicitly marked as a model or open question rather than presented as a proven storage format. = Scope: This report summarizes the research completed through August 28, 2026 on the first four true Multiplayer / Link map groups in The Return of the King, including station structure, Host/Client authority, SAV persistence, and progression-writing logic. Any point not closed by machine-code analysis or paired SAV comparison is explicitly marked as a model or open question rather than presented as a proven storage format.

# 1. Executive Summary
- The first four true multiplayer map groups are not Dwarven Halls of Moria, Helm's Deep, Weathertop, or Fangorn Forest; those belong to the Bonus Map system. The four multiplayer groups under study are Paths of the Dead, Pelennor Fields, Minas Tirith, and Mount Doom.
- Their confirmed menu structure is 5 / 1 / 5 / 1, for a total of 12 target stations.
- Completing the single-player game with every character is not a necessary condition for unlocking all 12 multiplayer stations, nor has it been proven to be a unique master switch.
- Multiplayer station progression is persistent player state stored in the SAV. The ROM supplies the map assets, menus, rules, and progression reader/writer code.
- A fully unlocked Host can take a low-progress Client directly into a later station that the Client could not select on its own. After the Client actually plays that station and performs a normal save, the Client's own SAV permanently gains multiplayer progression.
- In the Paths of the Dead route, a test involving the fourth station, Anduin River, caused the Client to obtain that station and the earlier stations as well. This proves prefix-closure behavior at the gameplay level.
- In BLRE, a multiplayer progression writer has been located near CPU address 0x08018098. It is gated by a multiplayer-mode flag, accepts a progression ID in the range 0-15, updates a global unlock/progression region, and then invokes save logic.
- The 0-15 progression IDs cannot yet be honestly mapped one-to-one onto the 12 visible menu stations. The underlying representation may be a route threshold, a cumulative bitmask, or a hybrid structure. The exact write moment - entry, completion, exit, or another checkpoint - remains open.
# 2. Correcting the Research Object: Bonus Maps vs. True Multiplayer
Early work temporarily conflated bonus/reward maps with the first four true multiplayer groups. That interpretation was corrected using observed in-game menus, official regional ROMs, fully unlocked and partially unlocked SAV files, and disassembly results. All conclusions in this report refer specifically to the actual Multiplayer / Link map groups.
**Data block 2 (converted from a table for copy/paste compatibility)**
- Row 1: Order = 1; Multiplayer Map Group = Paths of the Dead; Station Count = 5; Confirmed Stations = Isengard; Dunharrow; Paths of the Dead; Anduin River; Ford
- Row 2: Order = 2; Multiplayer Map Group = Pelennor Fields; Station Count = 1; Confirmed Stations = Rohan
- Row 3: Order = 3; Multiplayer Map Group = Minas Tirith; Station Count = 5; Confirmed Stations = Isengard; Rohan; White Mountains; Battle of Pelennor Fields; Minas Tirith
- Row 4: Order = 4; Multiplayer Map Group = Mount Doom; Station Count = 1; Confirmed Stations = Lair

**Data block 3 (converted from a table for copy/paste compatibility)**
- Naming note: map-group and station labels differ slightly among regional releases and fan-localized text. This English report uses the final normalized structure adopted for the research. = Naming note: map-group and station labels differ slightly among regional releases and fan-localized text. This English report uses the final normalized structure adopted for the research.

# 3. Responsibilities of ROM, SAV, and Runtime RAM
**Data block 4 (converted from a table for copy/paste compatibility)**
- Row 1: Layer = ROM; Current Best Interpretation = Contains map resources, multiplayer menus, unlock checks, the progression writer, and the Link / Host / Client flow. Playing a station does not rewrite the ROM itself.; Evidence Strength = High
- Row 2: Layer = SAV; Current Best Interpretation = Stores persistent multiplayer progress/unlock state. After a Host carries a Client through a station, a normal Client save persists the new progression.; Evidence Strength = High: behavioral tests + code
- Row 3: Layer = RAM; Current Best Interpretation = Holds the runtime copy of the global save structure and current multiplayer state. In BLRE research, the global SAV runtime base has repeatedly been located near 0x030057F0.; Evidence Strength = High confidence

Earlier analysis sometimes used an '8-byte block reversal' description to explain the appearance of a 512-byte EEPROM dump. Later work refined the distinction between file-byte representation and bus-level ordering. Accordingly, this report does not treat 8-byte reversal as a foundational premise of the unlock mechanism.
# 4. Core Code Lead: Multiplayer Progression Writer near 0x08018098
BLRE disassembly revealed a function near CPU address 0x08018098 that is strongly associated with writing multiplayer progression. The following properties were established during the research:
- The function is gated by a Multiplayer mode flag, recorded during the research as 0x04. This strongly indicates that it is not a generic single-player mission writer.
- It accepts a progression ID in the range 0-15, uses switch/branch logic to update a global unlock/progression region, and then calls save-related logic.
- The fully unlocked SAV is not a simple pattern with all 16 bits set, so the 0-15 IDs cannot be interpreted as 'one bit per visible station' without further evidence.
- The same global unlock area also carries other flags such as Artifacts and Bonus Maps. Therefore the whole region near save base + 0x2C must not be mislabeled as a pure 'multiplayer station table'.
**Data block 5 (converted from a table for copy/paste compatibility)**
- BLRE research anchors / runtime save base ~ 0x030057F0 / Multiplayer progression writer ~ 0x08018098 / writer input progression ID 0..15 / effect update global unlock/progression flags -> save = BLRE research anchors / runtime save base ~ 0x030057F0 / Multiplayer progression writer ~ 0x08018098 / writer input progression ID 0..15 / effect update global unlock/progression flags -> save

# 5. Strongest Gameplay Finding: Host Carry and Persistent Client Inheritance
Observed behavior materially changed the model of how multiplayer unlocking works. A typical test proceeded as follows:
1. The Client loaded a SAV in which later multiplayer stations were not available.
1. The Host loaded an English-region SAV with the first four multiplayer groups fully unlocked and selected a later station that the Client could not select independently.
1. Both players successfully entered and played that station together.
1. The Client performed a normal in-game save, then exited and later linked with another player.
1. The Client's own multiplayer menu now contained stations reached during the prior session, and the change remained present in later sessions.
**Data block 6 (converted from a table for copy/paste compatibility)**
- Conclusion: session entry authority shows a strong Host-authority characteristic - the Host can determine a later starting point when its own progress allows it. Persistent acquisition, however, occurs only when the Client's own SAV is written. = Conclusion: session entry authority shows a strong Host-authority characteristic - the Host can determine a later starting point when its own progress allows it. Persistent acquisition, however, occurs only when the Client's own SAV is written.

# 6. The Anduin River Test and Prefix Closure
The strongest behavioral evidence comes from the Paths of the Dead route. Anduin River is the fourth station. When a low-progress Client was carried there by a fully unlocked Host, played normally, and saved, the Client later showed Anduin River together with the earlier stations as unlocked.
**Data block 7 (converted from a table for copy/paste compatibility)**
- Behavioral model: / reach / complete station 4 => stations 1, 2, 3, and 4 are treated as unlocked = Behavioral model: / reach / complete station 4 => stations 1, 2, 3, and 4 are treated as unlocked

This directly rules out the simplest 'unlock only the single station just visited' model and supports the interpretation that route progression has prefix-closure behavior.
# 7. Three Candidate Low-Level Models
**Data block 8 (converted from a table for copy/paste compatibility)**
- Row 1: Model = Maximum-progress / threshold model; Form = progress_new = max(progress_old, reached); How It Explains Prefix Unlocking = The menu exposes station_index <= progress.; Current Assessment = Most natural behavioral model; strong candidate
- Row 2: Model = Cumulative bitmask model; Form = flags |= S1 | S2 | ... | Sn; How It Explains Prefix Unlocking = Reaching station n sets all prefix bits at once.; Current Assessment = Also fully compatible; requires SAV diff to distinguish
- Row 3: Model = Hybrid model; Form = Route threshold + independent flags; How It Explains Prefix Unlocking = Main-route advancement uses a threshold while special states use bits.; Current Assessment = Compatible with both the 0-15 writer and the shared unlock region

**Data block 9 (converted from a table for copy/paste compatibility)**
- Do not overclaim: behavior proves that a later station can cause all earlier stations to be treated as unlocked. It does not yet prove that the SAV contains one scalar max-progress integer. The max() formula should be read as a behaviorally equivalent model, not a decoded field formula. = Do not overclaim: behavior proves that a later station can cause all earlier stations to be treated as unlocked. It does not yet prove that the SAV contains one scalar max-progress integer. The max() formula should be read as a behaviorally equivalent model, not a decoded field formula.

# 8. Normal Unlocking in an Unmodified Game: Best Current Answer
For the practical question 'How are all stations normally unlocked?', the most defensible answer is:
- True Multiplayer progression must be separated from single-player completion conditions and Bonus Map requirements such as kill counts or Artifact totals. Those conditions are not a direct master requirement for the 12 stations.
- Under normal rules, two players should enter Multiplayer, advance from an already available route start/checkpoint, allow the game to write progression while in multiplayer mode, and then perform a normal save.
- If one player has a fully unlocked Host SAV, a lower-progress Client can be carried directly into a later station. After actual play and a normal save, the Client can acquire progression - including prefix unlocking - without ROM modification or cheat codes.
- Completing every single-player character may influence other global unlock systems, but existing code evidence and gameplay tests do not support it as a necessary universal switch for the first four multiplayer groups.
# 9. Regional Applicability: USA/Europe, Japan, and Localized Builds
**Data block 10 (converted from a table for copy/paste compatibility)**
- Row 1: Version = BLRE USA/Europe; Current Evidence = The supplied USA/Europe release is one official binary. Most writer and SAV-behavior research was completed on BLRE.; Conclusion Level = A - strongest
- Row 2: Version = BLRJ Japan; Current Evidence = Same-generation engine with strongly homologous structures. Later feature-porting work confirmed many corresponding logic paths.; Conclusion Level = B+ / high confidence; native progression still merits dynamic retesting
- Row 3: Version = Localized build; Current Evidence = Based on the BLRJ line. Real Host/Client cross-save tests and station-unlock behavior were observed.; Conclusion Level = A- / B+ behaviorally; addresses depend on build

# 10. Engineering Validation: Forced 5/1/5/1 Menu Visibility while Preserving Link Boundaries
During later ROM engineering, two behaviors were deliberately separated: (1) how many stations the multiplayer menu exposes, and (2) whether a single-player path can bypass Link and enter multiplayer maps. The final engineering design retained 5/1/5/1 visibility only inside genuine Multiplayer / Link mode and removed the single-player bypass. This provided an additional structural validation:
- Menu station visibility can be controlled at ROM level independently of the SAV's original progression.
- Real Link communication, Host/Client connection, map startup, synchronization, and save chains can remain intact while menu visibility policy is changed.
- The question 'Can single player enter a multiplayer map?' is not the same switch as 'Which stations are visible in the multiplayer menu?'.
**Data block 11 (converted from a table for copy/paste compatibility)**
- The engineering patch is a research and validation tool. It must not be used to infer the official game's normal unlock conditions. = The engineering patch is a research and validation tool. It must not be used to infer the official game's normal unlock conditions.

# 11. Evidence-Grade Summary
**Data block 12 (converted from a table for copy/paste compatibility)**
- Row 1: Proposition = The true first four groups contain 12 stations in a 5/1/5/1 structure.; Current Grade = A; Comment = Menu observations, gameplay tests, and later engineering agree.
- Row 2: Proposition = Single-player completion by every character is not a required universal condition.; Current Grade = A-; Comment = Host carry works; independent Multiplayer writer exists.
- Row 3: Proposition = Progression persists in SAV.; Current Grade = A; Comment = Client retains it after normal save across later sessions.
- Row 4: Proposition = A Host can carry a low-progress Client into a later station.; Current Grade = A; Comment = Repeated observed behavior.
- Row 5: Proposition = Anduin River caused stations 1-4 to become available.; Current Grade = A; Comment = Key direct test.
- Row 6: Proposition = Prefix-closure behavior exists.; Current Grade = A; Comment = Directly proven at the behavioral level.
- Row 7: Proposition = The low-level format is definitely one scalar max-progress integer.; Current Grade = C+ / strong model; Comment = Bitmask and hybrid structures remain viable.
- Row 8: Proposition = 0x08018098 IDs 0-15 are fully mapped one-to-one to the 12 visible stations.; Current Grade = C; Comment = Not closed.
- Row 9: Proposition = The exact write moment is entry, completion, or exit.; Current Grade = C; Comment = Requires breakpoint / node-specific SAV experiments.
- Row 10: Proposition = All four routes use exactly the same prefix rule.; Current Grade = B- / pending; Comment = Anduin River is strong evidence; other routes need systematic retesting.

# 12. Highest-Value Next Experiments
1. Using the same zero/low-progress Client SAV, let the Host select station 3 and station 5 in separate runs, then compare Client SAVs before and after to test 1-3 and 1-5 prefix closure.
2. For Anduin River, capture three checkpoints: immediately after entry, mid-combat, and after reaching the exit/clear state. Save or capture RAM at each point to locate the true progression write moment.
3. Preserve before/after SAVs for both Host and Client in the same session to determine whether Host progression is also written, whether Client writes are independent, and whether any synchronized flags are exchanged.
4. Trace every caller of 0x08018098 and the menu reader, recording progression ID, route ID, station index, and target save field.
5. Repeat the minimum experiment on BLRJ and the localized build to verify behavioral isomorphism rather than only code-layout homology.
# 13. Conclusions That Can Be Frozen at This Stage
- The first four true Multiplayer groups contain 12 target stations in a 5/1/5/1 structure.
- Unlock status is player progression, not a permanent rewrite of the ROM.
- Completing all single-player characters is not required to unlock all multiplayer stations.
- Host authority is strong enough to carry a low-progress Client into later stations.
- After actual joint play and a normal save, the Client's own SAV permanently gains multiplayer progression.
- At least on the Paths of the Dead route, reaching a later station can create prefix unlocking.
- The original game contains a dedicated Multiplayer progression writer; the exact semantics of its 0-15 IDs remain unresolved.
- Future reporting should continue to separate observed behavior from inferred low-level storage models.
# Appendix A. Key Research Anchors
**Data block 13 (converted from a table for copy/paste compatibility)**
- Row 1: Item = BLRE ROM baseline (historical research); Research Anchor / Sample = SHA-256 b5f556593c5fbe3531a4354b047f0bed364a124ceee43f278a48f4d26b0ffa9c
- Row 2: Item = BLRJ Japan baseline (historical research); Research Anchor / Sample = SHA-256 f250158fe77e6a267b02fecb5a2182291b4ff4761565662400ca5c60b5edb7d0
- Row 3: Item = Fully unlocked European SAV; Research Anchor / Sample = SHA-256 24e645f986fdb3854fc43fa816175baacc18e09979793f3b8d777915f6ef4cce
- Row 4: Item = Partially unlocked European SAV; Research Anchor / Sample = SHA-256 233099230b2e9401aa766f7608b35db0de8432567e39a328c0a3ef01a8a60b22
- Row 5: Item = Runtime global SAV base in BLRE research; Research Anchor / Sample = Approximately 0x030057F0
- Row 6: Item = Multiplayer progression writer; Research Anchor / Sample = Approximately 0x08018098
