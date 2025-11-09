# Less Restrictive Legacies

A CK3 mod that expands access to dynasty legacy tracks from all major DLCs, making them available based on thematic fit and mechanical benefit rather than strict ethnic or regional restrictions.

## What It Does

This mod relaxes the cultural and governmental restrictions on dynasty legacy tracks, allowing more rulers to access these powerful bonuses when it makes thematic and mechanical sense.

### Northern Lords (FP1)

**Adventure Legacy** - Now accessible to:
- Norse culture (vanilla)
- North Germanic heritage
- Cultures with: Practiced Pirates, Seafaring, Diasporic, or Druzhina traditions

**Pillage Legacy** - Now accessible to:
- Norse culture (vanilla)
- North Germanic heritage
- Cultures with: Practiced Pirates, Seafaring, or Battlefield Looters traditions
- Tribal governments (raiding-capable)

### Fate of Iberia (FP2)

**Metropolitan (Urbanism) Legacy** - Now accessible to:
- Iberian Struggle participants (vanilla)
- Iberian heritage (vanilla)
- Characters with capital in Iberia region (vanilla)
- Cultures with: Republican Legacy, Parochialism, or City Keepers traditions

**Coterie Legacy** - Now accessible to:
- Iberian Struggle participants (vanilla)
- Iberian heritage (vanilla)
- Characters with capital in Iberia region (vanilla)
- Cultures with: Family Entrepreneurship, Tribe Unity, Strong Kinship, or Mystical Ancestors traditions
- Cultures with Communal ethos

### Legacy of Persia (FP3)

**Brilliance (Khvarenah) Legacy** - Now accessible to:
- Iranian heritage (vanilla)
- Zoroastrian faith (divine glory concept is Zoroastrian religious tradition)
- Cultures with Enlightened Magnates tradition (Persian administrative/poetic heritage)

### Khans of the Steppe (MPO)

**Nomadic Legacy** - Now accessible to:
- Nomadic government (vanilla)
- Cultures with Horse Lords or Steppe Tolerance traditions
- Mongolic heritage (quintessential nomads)
- **Note:** 4 of 5 perks require herds (nomadic-only mechanic), non-nomads only benefit from archer cavalry bonuses

### Roads to Power (EP3)

**Bureaucracy (Administrative) Legacy** - Now accessible to:
- Any government with the influence mechanic
- Includes: Administrative, Celestial, Meritocratic, Steppe Admin, Japanese (Ritsuryo)

### All Under Heaven (TGP)

**Virtues (China) Legacy** - Now accessible to:
- Governments with merit system: Meritocratic and Celestial (vanilla)
- **Note:** All perks require the merit mechanic (exams, disciples, movements, careers)

**Elegance (Japan) Legacy** - Now accessible to:
- Japanese governments (both Ritsuryo and Soryo) (vanilla)
- Cultures with TGP Japanese traditions: Ephemeral Grace or Dynastic Pragmatism

**Divine Aspirations (Southeast Asia) Legacy** - Now accessible to:
- Mandala and Wanua governments (vanilla)
- Cultures with TGP Southeast Asian traditions: Maritime Way of Life, Esoteric Power, or Barangay Confederations

## Technical Details

- **Only modifies** `is_shown` triggers for legacy tracks (visibility in UI)
- **Removes/relaxes** `can_be_picked` restrictions from first perks
- **Uses LIOS file numbering** (82, 83, 92, 95, 96, 98) to override vanilla files
- **No changes** to perk effects, costs, or balance
- **Maintains all vanilla conditions** as baseline (never more restrictive)

## Compatibility

- **Game Version:** 1.18.* (Majesty update and later)
- **DLC Required:** Each legacy track requires its respective DLC:
  - Northern Lords (FP1): Adventure and Pillage legacies
  - Fate of Iberia (FP2): Metropolitan and Coterie legacies
  - Legacy of Persia (FP3): Brilliance legacy
  - Khans of the Steppe (MPO): Nomadic legacy
  - Roads to Power (EP3): Bureaucracy legacy
  - All Under Heaven (TGP): Virtues, Elegance, and Divine Aspirations legacies
- **Mod Compatibility:** Compatible with most mods (doesn't touch vanilla files except legacy/perk definitions)
- **Ironman:** Compatible ✓
- **Achievements:** Compatible ✓

## Design Philosophy

This mod follows a "thematic fit + mechanical benefit" approach:

1. **Thematic Fit:** Access is granted when it makes cultural/historical sense
   - Example: Practiced Pirates can access Viking Adventure legacy
   - Example: Zoroastrians can access Khvarenah (divine glory) legacy

2. **Mechanical Benefit:** Access is granted when mechanics are actually useful
   - Example: Any government with influence can use Administrative legacy
   - Example: Non-nomads can access Nomadic legacy but only benefit from cavalry bonuses (not herd mechanics)

3. **Vanilla Baseline:** All vanilla access conditions are preserved
   - Never more restrictive than base game
   - Only adds alternative paths to access

## Credits

Based on the original Steam Workshop mods:
- "Less Restricted Northern Legacies" (Workshop ID: 2861054654)
- "Less Restricted Iberian Legacies" (Workshop ID: 2861053969)

Extended and updated for CK3 v1.18 to include all DLC legacy tracks.

**Steam Workshop:** https://steamcommunity.com/sharedfiles/filedetails/?id=3601134767
