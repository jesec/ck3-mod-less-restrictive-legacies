# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Project Overview

This is a Crusader Kings III mod called **"Less Restrictive Legacies"** that expands player choice by removing ethnic/cultural gatekeeping from dynasty legacy systems. It's built on the [ck3-mod-base](https://github.com/jesec/ck3-mod-base) git-based workflow.

### Base Repository Setup

**Critical:** This repository depends on the base repository at https://github.com/jesec/ck3-mod-base

The base repository is configured as a git remote named `base`:
```bash
git remote -v
# base    https://github.com/jesec/ck3-mod-base.git (fetch)
# origin  https://github.com/jesec/ck3-mod-less-restrictive-legacies.git (fetch)
```

**Before working with this repository, you MUST fetch from the base remote:**
```bash
# Fetch latest base tags and branches
git fetch base --tags

# This brings in tags like base/1.18.0, base/1.18.1, etc.
```

Without fetching from the base remote, you won't have access to CK3 version tags needed for merging.

### Core Philosophy (Critical to Understand)

**The mod EXPANDS access, never restricts it.**

```
IF (vanilla allows access) OR (mod allows access)
THEN (final result MUST allow access)
```

This is a **union** operation, never an intersection. We add possibilities, never remove them.

**What this means:**
- Vanilla: "Only Norse can have Adventure legacy"
- Mod adds: "...OR cultures with seafaring/piracy traditions OR North Germanic heritage"
- Result: Norse still have access (preserved) + new alternatives added

**Never remove vanilla unlock paths. Only add alternatives.**

---

## Understanding This Repository

### Don't Assume - Discover

**DO NOT hardcode assumptions about:**
- How many files are modified
- Which specific files are modified
- How many references the mod uses
- What those references are

**Instead, discover the current state:**

```bash
# Find the current base version
git describe --tags --match "base/*" HEAD

# See what files the mod modifies
git diff $(git describe --tags --match "base/*" HEAD) HEAD --name-only

# See the actual changes
git diff $(git describe --tags --match "base/*" HEAD) HEAD --stat

# Read a specific modified file to understand what it does
cat base/game/common/dynasty_legacies/some_file.txt
```

### Repository Structure

```
ck3-mod-less-restrictive-legacies/
├── base/                      # CK3 base game (tracked by git)
│   └── game/                  # Vanilla game files
│       └── common/            # Game logic
│           ├── dynasty_legacies/   # Legacy visibility conditions
│           └── dynasty_perks/      # Perk unlock requirements
├── mod/                       # Pure mod content (metadata, docs)
├── out/                       # Build output (gitignored)
├── build.sh -> base/scripts/build.sh    # Build script (symlink)
└── install.sh -> base/scripts/install.sh # Install script (symlink)
```

**To understand what the mod does:**
1. Read `README.md` for high-level explanation
2. Use `git diff` to see actual changes vs vanilla
3. Read the modified files to understand the patterns

---

## Build System

### Commands

```bash
# Build mod
./build.sh

# Install to CK3
./install.sh
```

The build script:
1. Auto-detects CK3 base version from git history
2. Exports modified files from `base/game/`
3. Copies files from `mod/`
4. Generates final mod in `out/`

**If the build succeeds, the mod is syntactically valid.**

---

## Git-Based Workflow

### Key Concept

The repository uses git tags from the **base remote** to track different CK3 versions:

- Tags like `base/1.18.0`, `base/1.18.1`, etc. come from the `base` remote (jesec/ck3-mod-base)
- These tags represent vanilla CK3 game files for that version
- The `master` branch = Your mod (vanilla + modifications)

**To see what the mod changes:**
```bash
# Current base version
BASE=$(git describe --tags --match "base/*" HEAD | grep -o "base/[0-9.]*")

# Files modified by mod
git diff $BASE HEAD --name-only

# Detailed diff
git diff $BASE HEAD
```

### When CK3 Updates

New CK3 versions arrive as new git tags in the base repository. To update the mod:

```bash
# Fetch new versions from base repository
git fetch base --tags

# Merge new version
git merge base/1.XX.X
```

**This will either:**
1. Merge cleanly (base didn't touch our files)
2. Conflict (base modified something we also modified)
3. Break references (base removed something we depend on)

---

## The Mod's Pattern

### Discovery Process

Before making changes or merging, understand what the mod currently does:

**1. Find modified files:**
```bash
BASE=$(git describe --tags --match "base/*" HEAD | grep -o "base/[0-9.]*")
MODIFIED=$(git diff --name-only $BASE HEAD | grep "base/game/")
echo "$MODIFIED"
```

**2. For each modified file, understand the change:**
```bash
# See what changed
git diff $BASE HEAD -- path/to/file.txt

# Read the full current version
cat path/to/file.txt
```

**3. Extract what the mod depends on:**

Look for patterns in the modified files:
- `tradition_*` - Cultural traditions
- `heritage_*` - Cultural heritages
- `government_*` - Government type flags
- `ethos_*` - Cultural ethos
- `religion:*` - Religions
- Other game references

Example:
```bash
# Extract all tradition references from modified files
git diff --name-only $BASE HEAD | xargs grep -oh "tradition_[a-z_0-9]*" | sort -u
```

### Understanding the Modifications

The mod makes two types of changes:

**Type 1: Legacy Definitions** (in `dynasty_legacies/*.txt`)

These control **visibility** - who can SEE a legacy track.

Pattern to look for:
```
legacy_track = {
  is_shown = {
    dynasty = {
      OR = {
        # Original vanilla condition (PRESERVED)
        dynast = { ... vanilla check ... }

        # Already unlocked (PRESERVED)
        has_dynasty_perk = first_perk

        # MOD ADDITIONS (new OR block added)
        dynast = {
          OR = {
            culture = { has_cultural_tradition = tradition_X }
            culture = { has_cultural_pillar = heritage_Y }
          }
        }
      }
    }
  }
}
```

**Type 2: Perk Definitions** (in `dynasty_perks/*.txt`)

These control **unlock requirements** - who can BUY perks.

Pattern to look for:
```
perk = {
  legacy = legacy_track

  can_be_picked = {
    # Mod typically REMOVES overly restrictive checks here
    # Keeps: DLC ownership (has_XXX_dlc_trigger = yes)
    # Removes: Cultural/governmental restrictions
  }

  # Effects unchanged
}
```

---

## Merging New CK3 Versions

### Philosophy

When merging a new base version, ask these questions:

1. **What changed in the base game?**
   - `git diff base/OLD_VERSION..base/NEW_VERSION`

2. **Did base modify any files we modified?**
   - `git diff base/OLD_VERSION..base/NEW_VERSION -- $(git diff --name-only base/OLD_VERSION HEAD)`

3. **Do our dependencies still exist?**
   - Extract references from our modified files
   - Search for each in new base game

### Merge Procedure

**Step 1: Discover Current State**
```bash
# Ensure base remote is up to date
git fetch base --tags

# Understand current state
BASE=$(git describe --tags --match "base/*" HEAD | grep -o "base/[0-9.]*")
git diff --stat $BASE HEAD

# What files are modified?
git diff --name-only $BASE HEAD

# What changed in base between versions?
git diff --stat $BASE..base/NEW_VERSION
```

**Step 2: Attempt Merge**
```bash
# Merge the new base version tag
git merge base/NEW_VERSION
```

**Step 3: HIGH-EFFORT Validation (ALWAYS Required)**

**CRITICAL:** Perform thorough validation even if there are no conflicts. Clean merges can still break dependencies.

### Validation Philosophy

Don't validate against a hardcoded list. Instead:
1. **Discover** what kinds of references the mod actually uses
2. **Extract** all instances of each reference type
3. **Validate** each one exists in the base game
4. **Understand** what changed and why

### Discovery-Based Validation Process

**1. Discover Reference Types**

Read the modified files and understand what they reference:
```bash
# Get modified files
BASE=$(git describe --tags --match "base/*" HEAD | grep -o "base/[0-9.]*")
MODIFIED=$(git diff --name-only $BASE HEAD | grep "base/game/")

# Look at the content to see what patterns exist
for file in $MODIFIED; do
  echo "=== $file ==="
  cat "$file" | head -50
done
```

**2. Extract References Dynamically**

Based on what you see in the files, extract references. Common patterns include but are not limited to:
```bash
# Examples - adapt based on what you actually find
grep -oh "tradition_[a-z_0-9]*" $MODIFIED | sort -u
grep -oh "heritage_[a-z_0-9]*" $MODIFIED | sort -u
grep -oh "government_has_[a-z_0-9_]*" $MODIFIED | sort -u
grep -oh "ethos_[a-z_0-9]*" $MODIFIED | sort -u
grep -oh "religion:[a-z_0-9]*" $MODIFIED | sort -u
grep -oh "has_[a-z_0-9_]*_dlc" $MODIFIED | sort -u
# ... discover other patterns
```

**3. Validate Each Reference Type**

For each reference type you found, determine WHERE it should be defined and check:
```bash
# Example: Validate traditions
# First, understand where traditions are defined
find base/game/common -name "*.txt" -path "*/traditions/*" | head -3

# Then validate each tradition reference
for tradition in $(grep -oh "tradition_[a-z_0-9]*" $MODIFIED | sort -u); do
  if grep -rq "^${tradition}[[:space:]]*=" base/game/common/culture/traditions/; then
    echo "✓ $tradition"
  else
    echo "✗ MISSING: $tradition"
  fi
done
```

The key is to adapt this for whatever reference types you discover.

**4. Structural Validation**

Beyond references, validate file structure:
```bash
# Check brace balance in each modified file
for file in $MODIFIED; do
  OPEN=$(grep -o '{' "$file" | wc -l)
  CLOSE=$(grep -o '}' "$file" | wc -l)
  if [ $OPEN -eq $CLOSE ]; then
    echo "✓ $file: $OPEN pairs"
  else
    echo "✗ $file: $OPEN open, $CLOSE close (MISMATCH)"
  fi
done

# Check for UTF-8 BOM (CK3 uses UTF-8 with BOM)
for file in $MODIFIED; do
  if head -c 3 "$file" | od -A n -t x1 | grep -q "ef bb bf"; then
    echo "✓ $file: UTF-8 BOM present"
  else
    echo "⚠ $file: No BOM (may be OK if ASCII-only)"
  fi
done
```

**5. Build Test**

```bash
# Build test - verifies packaging works, files are accessible
./build.sh

# This does NOT validate syntax - it just packages files
# Syntax errors will only be caught when CK3 loads the mod
```

**6. Content Preservation Check**

```bash
# Verify mod additions are still present in the merged result
# Look at what we added vs the base
git diff $BASE HEAD -- $MODIFIED

# Manually inspect a sample file to verify mod logic is present
# (adapt based on what you discovered in Phase 1)
```

**7. Fix Problems Intelligently**

Don't just validate - actively fix issues you find.

**If a reference is missing:**
- Understand what it was used for (read the code, understand the game mechanic)
- Research what happened to it in the base game (git log, grep, read new files)
- Find the semantic equivalent (what achieves the same thematic goal now?)
- Update the mod with the correct reference
- Add comments explaining what you did

**If there's a structural issue:**
- Fix braces, indentation, formatting
- Add UTF-8 BOM if missing
- Clean up any corruption

**If you're uncertain:**
- Research more deeply
- Read actual game definitions, not just reference names
- Look at how base game uses things
- Think about what makes sense for CK3 gameplay

**Update all affected documentation:**

This is NOT just about version numbers. Think about what ACTUALLY changed and update ALL relevant documentation.

**Step 1: Understand what changed in the base game**

Read the Paradox patch notes (linked in base/.ck3-version.json). Ask yourself:
- Did they rename any traditions, heritages, or government types the mod uses?
- Did they change how any game mechanics work (influence, merit, herds, etc.)?
- Did they add new DLCs or legacy tracks?
- Did they rework how legacies function?
- Did they add/remove/change cultural pillars, traditions, or ethos?

**Step 2: Update mod/README.md with actual changes**

This file documents WHAT THE MOD DOES. If base game changed, the documentation must reflect that:

Examples:
- If "Practiced Pirates" was renamed to "Pirate Tradition": update all mentions
- If a new DLC added a legacy: consider if the mod should expand access to it, update docs
- If government types changed: update the government lists in the documentation
- If Paradox changed how influence works: update the description of how Administrative legacy access works
- If new traditions were added that fit thematically: document them

Read the actual mod/README.md - it lists specific traditions, heritages, governments, and mechanics. These aren't just decorative - they're what players care about.

**Step 3: Update version metadata**

1. **README.md** (root) - Update game version badge:
   ```markdown
   [![Game Version](https://img.shields.io/badge/CK3-X.Y.*-blue.svg)](https://github.com/jesec/ck3-mod-base/blob/base/X.Y.Z.W/base/release-notes/...)
   ```

2. **mod/descriptor.mod** - Update supported_version:
   ```
   supported_version="X.Y.*"  # Update if crossing major versions
   version="your.mod.version"  # Increment mod version
   ```

3. **mod/README.md** - Update compatibility section:
   ```markdown
   - **Game Version:** X.Y.* (Release Name)
   ```

**Step 4: Search for stale references**

Don't assume you caught everything:
```bash
# Search for old version strings
grep -r "1\.18" . --include="*.md" --include="*.mod"

# Search for potentially renamed game elements
grep -r "tradition_" mod/ | grep -v ".txt:"  # Find tradition names in docs
grep -r "heritage_" mod/ | grep -v ".txt:"   # Find heritage names in docs
```

**Document your work:**

Write a commit message explaining:
- What changed in base game (new features, reworks, renamed content)
- What issues you found and how you fixed them
- Why your fixes preserve the mod's intent
- What you validated
- **What documentation you updated and WHY**:
  - Not just "updated version numbers"
  - List specific changes: "Updated tradition_xyz to tradition_abc throughout", "Added new DLC section", "Updated mechanic descriptions for influence system changes"

Be confident if you solved problems intelligently. Only express uncertainty if you genuinely couldn't figure something out after thorough investigation.

### If Conflicts Occur

**Analyze each conflict:**

1. **What did base change?**
   - Read the base version: `git show base/NEW_VERSION:path/to/file.txt`

2. **What did we change?**
   - Read our version: `git show HEAD:path/to/file.txt`

3. **What was the common ancestor?**
   - Read ancestor: `git show $BASE:path/to/file.txt`

**Resolution Strategy:**

The goal is to **combine** base changes + mod changes, not choose one over the other.

```
Final Result = Base Game Structure + Base Conditions + Mod Conditions
```

**Example resolution:**

```
Base added: New required condition at top of is_shown block
Mod added: Alternative unlock conditions in OR block

Resolution:
  is_shown = {
    [New base requirement]        ← Accept from base
    dynasty = {
      OR = {
        [Vanilla unlock]           ← Preserve from base
        [Base additions]           ← Accept from base
        [Mod alternatives]         ← Preserve from mod
      }
    }
  }
```

**Key principles:**
- Accept structural changes from base
- Preserve vanilla unlock paths
- Preserve mod's alternative paths
- Never make access MORE restrictive

**After resolving conflicts:**
1. Mark resolved: `git add resolved_file.txt`
2. Validate using same checks as no-conflict path
3. Build test
4. Commit with detailed explanation

### When Issues Are Found

**Philosophy:** Fix problems, don't just report them.

**Your approach:**

1. **Investigate thoroughly**
   - What exactly is wrong?
   - Why did it break?
   - What changed in the base game?

2. **Research the solution**
   - Use git tools to understand history
   - Read actual game files to understand mechanics
   - Look for patterns and semantic equivalents
   - Think about what makes sense for the game

3. **Apply intelligent fixes**
   - Update references to correct ones
   - Fix structural issues
   - Preserve mod intent
   - Document what you did and why

4. **Only escalate if truly stuck**
   - You cannot understand the conflict even after research
   - Would require removing core mod functionality
   - Genuinely cannot figure out what to do

**If you must escalate**, explain what you tried:
- What you investigated
- What fixes you attempted
- Why you're stuck
- What specific knowledge or decision is needed

---

## Validation Principles

### Don't Validate Against Hardcoded Lists

**Wrong:**
```bash
# Don't do this - assumes specific references
check_if_tradition_practiced_pirates_exists()
check_if_tradition_seafaring_exists()
...
```

**Right:**
```bash
# Extract what the mod ACTUALLY uses, then validate
MODIFIED=$(git diff --name-only $BASE HEAD)
REFERENCES=$(echo "$MODIFIED" | xargs grep -oh "tradition_[a-z_0-9]*" | sort -u)

for ref in $REFERENCES; do
  if ! grep -rq "^${ref}" base/game/common/culture/traditions/; then
    echo "ERROR: $ref not found in base game"
  fi
done
```

### Understand Before Validating

Before running any validation:
1. **Discover** what files are modified
2. **Read** those files to understand the pattern
3. **Extract** what dependencies exist
4. **Search** for those dependencies in base game
5. **Report** findings

---

## Key Invariants (Always True)

These principles hold regardless of how the mod evolves:

### 1. Additive Philosophy
```
Access After Merge ⊇ Access Before Merge
```
The set of players who can access a feature can only grow or stay the same, never shrink.

### 2. Union of Conditions
When base and mod both modify the same file, the result is:
```
Final Conditions = Base Conditions ∪ Mod Conditions
```

### 3. Structure from Base
Accept structural changes from base game (new required fields, reorganization, etc.)

### 4. Preserve Intent
The intent is "expand access through thematic requirements". The exact form may change, but the intent must be preserved.

### 5. Build is Truth
If it builds successfully, the syntax is valid. The build script is the ultimate validator.

---

## Common Scenarios

### Scenario 1: Clean Merge

**Signs:**
- `git merge base/X.X.X` completes without conflicts
- Base didn't touch files we modified

**Action:**
1. Verify dependencies exist (extract and check)
2. Verify syntax (braces balanced, no corruption)
3. Build test
4. Commit

### Scenario 2: Compatible Conflict

**Signs:**
- Conflicts in files we both modified
- Base added something, we added something different
- Can see how to combine both

**Action:**
1. Analyze what each side added
2. Combine both changes (accept base structure + preserve mod additions)
3. Validate result
4. Build test
5. Commit with explanation

### Scenario 3: Breaking Change

**Signs:**
- Base removed a reference we use
- Base completely restructured in incompatible way
- Can't determine safe resolution

**Action:**
1. **STOP** - do not proceed
2. Create detailed escalation report
3. Abort merge: `git merge --abort`
4. Let human investigate

---

## Working Examples

### Example: Check What Mod Actually Modifies

```bash
# Get base version
BASE=$(git describe --tags --match "base/*" HEAD | grep -o "base/[0-9.]*")

# List modified files
git diff --name-only $BASE HEAD

# See statistics
git diff --stat $BASE HEAD

# See actual changes
git diff $BASE HEAD
```

### Example: Extract Dependencies

```bash
# Find all cultural traditions the mod uses
git diff --name-only $BASE HEAD | \
  xargs grep -oh "tradition_[a-z_0-9]*" | \
  sort -u

# Find all heritages
git diff --name-only $BASE HEAD | \
  xargs grep -oh "heritage_[a-z_0-9]*" | \
  sort -u

# Find all government flags
git diff --name-only $BASE HEAD | \
  xargs grep -oh "government_[a-z_0-9_]*" | \
  sort -u
```

### Example: Validate a Reference Exists

```bash
# Check if a tradition exists in base game
TRADITION="tradition_practiced_pirates"
if grep -rq "^${TRADITION}[[:space:]]*=" base/game/common/culture/traditions/; then
  echo "✓ $TRADITION exists"
else
  echo "✗ $TRADITION MISSING"
fi
```

### Example: Check Brace Balance

```bash
FILE="base/game/common/dynasty_legacies/some_file.txt"
OPEN=$(grep -o '{' "$FILE" | wc -l)
CLOSE=$(grep -o '}' "$FILE" | wc -l)

if [ $OPEN -eq $CLOSE ]; then
  echo "✓ Braces balanced: $OPEN pairs"
else
  echo "✗ Brace mismatch: $OPEN open, $CLOSE close"
fi
```

---

## Success Criteria

A merge/modification is successful when:

✅ **Builds successfully** - `./build.sh` completes without errors
✅ **Dependencies exist** - All references used by mod exist in base game
✅ **Syntax valid** - Files are well-formed (braces balanced, no corruption)
✅ **Philosophy preserved** - Access is still expanded, not restricted
✅ **Testable** - Changes can be verified by reading the code

---

## Final Principles

### Think, Don't Memorize

- Don't memorize that "the mod modifies 12 files"
- DO understand how to discover what files are modified
- Don't memorize "the mod uses 37 references"
- DO understand how to extract what references are used

### Understand, Don't Assume

- Don't assume the mod structure is static
- DO read the files to understand the current structure
- Don't assume references won't change
- DO extract current references when validating

### Adapt, Don't Hardcode

- Don't write scripts that check specific hardcoded lists
- DO write scripts that discover current state then validate
- Don't document specifics that will become stale
- DO document how to discover those specifics

### Preserve Intent, Not Form

The mod's intent: **Expand access to dynasty legacies through thematic requirements.**

The exact files, references, and structure may evolve. The intent remains constant.

When in doubt, ask: *Does this preserve the player's ability to access content through thematic fit?*

If yes → proceed. If no → stop. If uncertain → escalate.

---

**Remember:** You're not maintaining a list of modifications. You're maintaining a philosophy of expanded player choice. Discover the current state, understand the intent, and preserve it through changes.
