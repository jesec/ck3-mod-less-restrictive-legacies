# CK3 - Less Restrictive Legacies

[![Game Version](https://img.shields.io/badge/CK3-1.18.*-blue.svg)](https://github.com/jesec/ck3-mod-base/blob/base/1.18.1/base/release-notes/1_18_1_0_2025-11-12.md)
[![Steam Workshop](https://img.shields.io/badge/Steam-Workshop-green.svg)](https://steamcommunity.com/sharedfiles/filedetails/?id=3601134767)

A Crusader Kings III mod that expands access to dynasty legacy tracks from all major DLCs, making them available based on thematic fit and mechanical benefit rather than strict ethnic or regional restrictions.

## About This Repository

This is a **git-based mod repository** built on [ck3-mod-base](https://github.com/jesec/ck3-mod-base), enabling:

- ✓ Version control for CK3 base game files
- ✓ Clean diffs showing only meaningful mod changes
- ✓ Automatic merging when CK3 updates
- ✓ Professional mod development workflow

## What the Mod Does

This mod relaxes cultural and governmental restrictions on dynasty legacy tracks across 6 major DLCs, allowing more rulers to access these bonuses when thematically and mechanically appropriate.

### Modified Legacy Tracks

| DLC | Legacy Track | Original Access | Extended Access |
|-----|-------------|----------------|-----------------|
| **Northern Lords** | Adventure | Norse only | + North Germanic heritage, pirate/seafaring cultures |
| | Pillage | Norse only | + North Germanic heritage, raiding cultures, tribals |
| **Fate of Iberia** | Metropolitan (Urbanism) | Iberian only | + Republican/city-focused cultures |
| | Coterie | Iberian only | + Family/community-focused cultures |
| **Legacy of Persia** | Brilliance (Khvarenah) | Iranian only | + Zoroastrians, enlightened cultures |
| **Khans of the Steppe** | Nomadic | Nomadic gov only | + Horse lord/steppe cultures, Mongolic heritage |
| **Roads to Power** | Bureaucracy (Admin) | Admin gov only | + All influence-based governments |
| **All Under Heaven** | Virtues (China) | Merit gov only | *(No change - requires merit mechanics)* |
| | Elegance (Japan) | Japanese gov only | + Japanese cultural traditions |
| | Divine Aspirations (SEA) | Mandala/Wanua only | + Southeast Asian cultural traditions |

[Full details in mod/README.md](mod/README.md)

## Building & Installation

### Prerequisites

- Git
- Bash (Linux/macOS) or WSL/Git Bash (Windows)

### Quick Start

```bash
# Clone this repository
git clone https://github.com/jesec/ck3-mod-less-restrictive-legacies.git
cd ck3-mod-less-restrictive-legacies

# Build the mod
./build.sh

# Install to CK3 mod folder
./install.sh
```

The build script detects changes in `base/game/` and combines them with files in `mod/`, generating the final mod in `out/`.

## Development

### Repository Structure

```
ck3-mod-less-restrictive-legacies/
├── base/
│   └── game/              # Modified vanilla files (tracked by git)
│       ├── common/
│       │   ├── dynasty_legacies/  # 6 legacy track files
│       │   └── dynasty_perks/     # 6 perk definition files
│       └── ...
├── mod/
│   ├── descriptor.mod     # Mod metadata
│   ├── README.md          # Mod description
│   └── thumbnail.png      # Mod icon (empty)
├── out/                   # Build output (git ignored)
├── build.sh               # Build script
└── install.sh             # Installation script
```

### Modified Files (12 total)

**Dynasty Legacy Tracks** (visibility conditions):
- `82_tgp_legacies.txt` - TGP: Southeast Asia & Japan
- `83_ep3_legacies.txt` - EP3: Administrative
- `92_mpo_legacies.txt` - MPO: Nomadic
- `95_fp3_legacies.txt` - FP3: Khvarenah (Persian)
- `96_fp2_legacies.txt` - FP2: Urbanism & Coterie (Iberian)
- `98_fp1_legacies.txt` - FP1: Adventure & Pillage (Norse)

**Dynasty Perks** (unlock requirements):
- `01_fp1_dynasty_perks.txt` - Northern Lords perks
- `03_fp2_dynasty_perks.txt` - Fate of Iberia perks
- `03_fp3_dynasty_perks.txt` - Legacy of Persia perks
- `06_ep3_dynasty_perks.txt` - Roads to Power perks
- `07_mpo_dynasty_perks.txt` - Khans of the Steppe perks
- `08_tgp_dynasty_perks.txt` - All Under Heaven perks

### Updating for New CK3 Versions

When CK3 updates, merge the new base version:

```bash
# Fetch new base versions
git fetch origin

# Merge new CK3 version
git merge base/1.19.0  # or whatever the new version is

# Resolve conflicts if any, then rebuild
./build.sh
```

The git-based workflow shows exactly what changed in vanilla files, making updates straightforward.

## Technical Details

- **Approach:** Modifies `is_shown` triggers (visibility) and `can_be_picked` requirements (unlock)
- **File Strategy:** Uses LIOS numbering (8x/9x) to override vanilla files
- **Balance:** No changes to costs, effects, or progression
- **Philosophy:** Vanilla + additions (never more restrictive)

**Diff Statistics:**
- +104 insertions, -61 deletions across 12 files
- 100% meaningful gameplay changes (zero noise)
- All line endings preserved from base game

## Compatibility

- **Game Version:** CK3 1.18.* (Crane update and later)
- **Ironman:** ✓ Compatible
- **Achievements:** ✓ Compatible
- **Multiplayer:** Compatible (all players need the mod)
- **Other Mods:** High compatibility (doesn't touch most game systems)

## Credits

**Original Concept:**
- "Less Restricted Northern Legacies" by [original author]
- "Less Restricted Iberian Legacies" by [original author]

**Extended & Maintained:**
- Updated for CK3 1.18
- Extended to all DLC legacy tracks
- Migrated to git-based workflow

**Build System:**
- [ck3-mod-base](https://github.com/jesec/ck3-mod-base) by jesec

## License

- **Mod Code:** MIT License (see LICENSE)
- **CK3 Game Content:** © Paradox Interactive (base game files)

## Links

- **Steam Workshop:** https://steamcommunity.com/sharedfiles/filedetails/?id=3601134767
- **Base System:** https://github.com/jesec/ck3-mod-base
- **Issues:** [GitHub Issues](../../issues)
- **CK3 Forum:** [Crusader Kings III Forum](https://forum.paradoxplaza.com/forum/forums/crusader-kings-iii.1064/)

---

**Note:** This mod only changes which dynasty legacies are visible/accessible. It doesn't change the effects, costs, or balance of any legacy perks.
