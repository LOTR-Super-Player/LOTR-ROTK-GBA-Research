## Research Status, Evidence Policy, and Scope

### Current project truth set

#### Multiplayer

- First four true Multiplayer/Link groups: **5 / 1 / 5 / 1 = 12 visible stations**.
- Bonus Maps are a separate system.
- Host carry + Client normal save can persist additional Multiplayer progress in the Client SAV.
- A writer near `0x08018098` is strongly associated with Multiplayer progression; its `0..15` values are not yet proven to map one-to-one to visible stations.

#### Research Result G — corpse-hit death-state re-entry

- BLRE has the strongest dynamic closed loop: the same corpse entity can continue receiving damage, re-enter death state 6, call the normal death-drop path again, consume fresh RNG, and create new ground-item objects.
- Test entity example: `0x020125E0`; HP at `+0x5A` changed from about `-193` to `-607`; state at `+0x62` participates in death-state re-entry.
- The documented `0x020125E0` address is not a universal enemy slot.
- For one tested type/resource case, any-drop probability per valid re-death cycle was 55% and equipment-generator entry was 16.5%. This is not universal.
- Corpse-hittable and equipment-farmable are separate properties.
- RedMask eligibility does not itself reseed a reproducible savestate trajectory.

#### RedMask / rare eligibility

- Runtime RedMask: `char_base + 0x18A` (16-bit). Persistent counterpart: character record `+0x56`.
- Complete 14-target qualification state: `0xDBFF`.
- `0xDBFF` does not increase the type-E rate, the 1-in-10 random gate, or the underlying candidate-selection probability.

#### Drop probability

- Probability denominators must never be mixed.
- Type E machine share: `327 / 32768 = 0.997924805%` after full equipment generation has already been entered.
- 14 red backpack Uniques combined: approximately `0.0263547614%` per full equipment-generation event under the documented model.
- 37 blue-name group: approximately `0.060252064%` per full-equipment event under the Full-Eligibility Reference; exact per-item values remain state/candidate dependent.
- Ancient group rates are character-dependent and do not use type E.

#### Extra Treasure / Critical / farming targets

- Extra Treasure affects discrete Prefix/Suffix quality thresholds through integer arithmetic; it does not continuously increase every drop layer.
- Critical percentage has its own ROM attribute representation and must be separated from Critical Damage and Critical Armor.
- Spoon, Blood Pirate/of the Fates, and Dark Sapphire/of the Fates reports are route/affix-generation studies and should retain their map/source/difficulty assumptions.

### Evidence grades used in this archive

- **A:** direct machine-code, ROM constants, paired SAV/runtime evidence, or reproducible dynamic closure.
- **A/B:** strong static closure plus constrained practical assumptions.
- **B:** structurally strong interpretation where one display-name/source binding remains indirect.
- **C:** useful empirical hypothesis not yet closed by code or controlled dynamic comparison.

### Open work

- Complete enemy display-name -> source/resource -> equipment-entry-rate matrix.
- Exact machine-precise per-item probabilities for all 37 blue-name candidates.
- Full low-level mapping of Multiplayer progression storage semantics.
- Full 12-station real-link / dual-instance Host-Client progression matrix, including whether any progression is copied wholesale or only acquired through actual station play and normal saving.
- Per-enemy dynamic verification of corpse-hit compatibility and equipment-farmability.
- Independent runtime reproduction of Research Result G, Multiplayer progression, and the major probability chains on BLRJ / localized builds.
- Re-close the ordinary-combat Critical RNG call site on the current baseline; the drop-system 15-bit RNG must not be treated as independently proven combat-Critical RNG behavior.

## 2026-08-27 empirical scope corrections

- Spoon is empirically usable by Frodo, Sam, Gandalf, Aragorn, and Eowyn; recommended-character route models are not exclusivity rules.
- Spoon has been observed in Northern Ithilien, Helm's Deep Act II, and Moria Dwarven Halls Act I; a single-affix Spoon has also been observed at Helm's Deep.
- Snowbird's has been observed in the White Mountains, so an Ice Cave route must not be described as the only map where the Prefix can appear.
- Ordinary mobs are the primary long-term corpse-hit targets; broad boss/elite compatibility remains an empirical classification rather than a closed universal rule.
