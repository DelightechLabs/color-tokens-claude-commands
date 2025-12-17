# Design Tokens Migration

Migrates any codebase to use semantic color tokens regardless of current CSS setup.

## Overview

This command systematically converts hardcoded colors and non-semantic CSS variables to semantic design tokens following the `--color-{section}-{token}` naming convention.

**Target Files:** `src/**/*.{ts,tsx,js,jsx,scss,css}`

## Usage

Use the `/design-tokens-migration` slash command to start the automated migration process.

---

## Semantic Token Reference Table

Use this table as the definitive guide for mapping any color usage to semantic tokens:

### `background` — non-clickable page layers

| Token                          | Use For                                               |
|--------------------------------|-------------------------------------------------------|
| `layer-base` | Base layer (e.g. body background).                                      |
| `layer-2`    | Layer above base.                                                       |
| `layer-3`    | Layer above `layer-2` (add higher numbers as needed).                   |

### `brand` — strong interactive component backgrounds

| Token                           | Use For                                                    |
|---------------------------------|------------------------------------------------------------|
| `--color-brand-primary`         | Primary buttons, main call-to-action component backgrounds |
| `--color-brand-primary-hover`   | Hover state for primary interactive elements               |
| `--color-brand-primary-selected`| Selected/active state for primary elements                 |

### `status` — state indicators (interactive or text)

| Token                    | Use For                                                      |
|--------------------------|--------------------------------------------------------------|
| `--color-status-error`   | Error buttons, critical alerts, destructive actions         |
| `--color-status-info`    | Info buttons, neutral notifications, informational alerts   |
| `--color-status-success` | Success buttons, confirmations, positive feedback           |

### `container` — muted component backgrounds

| Token                              | Use For                                                                                                                 |
|------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| `--color-container-paper`          | Cards, dialogs, sheets, component backgrounds                                                                           |
| `--color-container-paper-hover`    | Hoverable component backgrounds                                                                                         |
| `--color-container-paper-selected` | Selected component backgrounds (e.g., selected item)                                                                    |
| `--color-container-info`           | Background for informational elements (e.g., default chips or default notice banners) - never same value as status info |
| `--color-container-success`        | Background for success / confirmation states (e.g., chips or notice banners) - never same value as status success       |
| `--color-container-error`          | Background for error / destructive states (e.g., chips or notice banners) - never same value as status error            |

### `text` — all typography colors

| Token                         | Use For                                                    |
|-------------------------------|------------------------------------------------------------|
| `--color-text-primary`        | Main body text, highest contrast text                     |
| `--color-text-secondary`      | Supporting text, descriptions, lower emphasis text        |
| `--color-text-tertiary`       | Placeholder text, disabled text, lowest emphasis          |
| `--color-text-link`           | Hyperlinks, clickable text elements                       |
| `--color-text-contrast`       | Text on dark/brand backgrounds (must meet WCAG AA)        |

### `borders` — component outlines

| Token                       | Use For                                               |
|-----------------------------|-------------------------------------------------------|
| `--color-borders-default`   | Input borders, card outlines, standard separators    |
| `--color-borders-focus`     | Focus states, active borders, keyboard navigation     |

### `shadows` — elevation and focus effects

| Token                      | Use For                                                |
|----------------------------|--------------------------------------------------------|
| `--color-shadows-small`    | Subtle hover effects, minor elevation                 |
| `--color-shadows-medium`   | Card shadows, modal shadows, standard elevation       |
| `--color-shadows-large`    | Floating panels, dropdown menus, high elevation       |
| `--color-shadows-focus`    | Focus indicators, keyboard navigation highlights       |

---

## Migration Process

### Phase 1: Discovery & Setup

**Step 1.1: Scan for All Color Usage**
Search the entire codebase for:
- CSS custom properties: `var(--any-color-var)`
- Hex colors: `#ffffff`, `#000`, etc.
- RGB/RGBA: `rgb(255, 255, 255)`, `rgba(0, 0, 0, 0.5)`
- HSL/HSLA: `hsl(0, 0%, 100%)`, `hsla(0, 0%, 0%, 0.5)`
- Named colors: `white`, `black`, `red`, etc.
- CSS-in-JS color values in TypeScript/JavaScript files

**🔍 Required Search Commands:**
```bash
# Find ALL CSS variables (regardless of prefix) to get complete inventory
grep -r "var(--[^)]\+)" src/ --include="*.css" --include="*.css"

# Extract unique CSS variable names for analysis
grep -r "var(--[^)]\+)" src/ --include="*.css" --include="*.css" -o | sed 's/.*var(//' | sed 's/).*//' | sort | uniq

# Find hardcoded colors
grep -r "#[0-9a-fA-F]\{3,6\}\|rgb\|hsl\|rgba\|hsla" src/ --include="*.css" --include="*.css"

# Find named colors (common ones)
grep -r "\bwhite\b\|\bblack\b\|\bred\b\|\bblue\b\|\bgreen\b\|\bgray\b\|\bgrey\b" src/ --include="*.css" --include="*.css"
```

**⚠️ Critical Verification: Complete CSS Variable Analysis**

Document ALL CSS variables found, categorizing them as:

**🎯 PRIORITY: Color Variables for Semantic Migration:**
- Non-semantic variables (e.g., `--background`, `--foreground`, `--border-color`, `--muted-foreground`)
- Primitive color variables (e.g., `--primary-*`, `--neutral-*`, `--danger`, `--success`)
- Any variables containing color values that don't follow `--color-{section}-{token}` pattern

**✅ KEEP: Non-Color Variables:**
- `--spacing-*` (spacing/layout)
- `--radius-*` (border radius)
- `--shadow-*` (shadows/elevation)
- `--transition-*` (animations)

**📋 Document Requirements:**
For every **color variable** found, document:
- Exact location (file:line)
- Current value/usage context
- Semantic token mapping target

**🚨 Migration Rule:**
ALL color variables must be replaced with semantic tokens following the reference table.
❌ Never keep legacy color tokens.
❌ Never leave hard-coded colors.
❌ Never create compatibility mappings.
✅ Replace explicitly with proper semantic tokens.

**Step 1.2: Create Semantic Tokens File**
1. Create `src/styles/semantic-color-variables.css`
2. Add the file structure with all semantic token definitions
3. Initially set all tokens to recognizable placeholder values for testing

**Step 1.3: Set Up Import**
Add import to your main CSS file (common locations: `src/styles/global.css`, `src/index.css`, `src/main.css`):
```css
@import './semantic-color-variables.css';
```

**Step 1.4: Body Background Check**
Check if the body element has a defined background color and ensure it uses the semantic background token:

1. **Search for body background definition:**
```bash
grep -r "body\s*{[^}]*background" src/ --include="*.css" --include="*.css"
grep -r "html,\s*body\s*{[^}]*background" src/ --include="*.css" --include="*.css"
```

2. **If body background is found:** Replace it with the semantic token:
```css
/* Replace any existing body background with semantic token */
body {
  background-color: var(--color-background-layer-base);
  /* ... other body styles */
}
```

3. **If body background is NOT found (assumes white):** Add the semantic background to your main CSS file:
```css
/* Add to global.css or main CSS file */
body {
  background-color: var(--color-background-layer-base);
}
```

4. **Verify the body background semantic token is correctly set** in `semantic-color-variables.css`:
```css
:root {
  --color-background-layer-base: #ffffff; /* or appropriate base background color based on body element background */
  /* ... other tokens */
}
```

⚠️ **Important:** The `--color-background-layer-base` token should be used for the main application background that appears behind all content.

#### 🛑 Phase 1 Completion Summary & Approval

**Before continuing to Phase 2, provide this summary:**

```
## Phase 1 Summary: Discovery & Setup

### Files Scanned:
- [List all files searched for color usage]

### Colors Found:
- CSS Variables: [list all var(--*) found]
- **⚠️ Non-Semantic Variables to Replace:** [list all --background, --muted-foreground, etc.]
- Hex Colors: [list all #colors found]
- RGB/RGBA: [list all rgb/rgba values found]
- Named Colors: [list all named colors found]
- Total unique colors discovered: [number]

### ✅ Critical Verification Passed:
- ✅ No compatibility mappings created in semantic tokens file
- ✅ All non-semantic variables documented for replacement
- ✅ Search commands executed and results documented

### Files Created:
- ✅ src/styles/semantic-color-variables.css (with placeholder tokens)
- ✅ Import added to [main CSS file]
- ✅ Body background verified/added with semantic token

### Next Phase Preview:
Phase 2 will map these [number] discovered colors to semantic tokens using the reference table.

**Ready to proceed to Phase 2? Please confirm before continuing.**
```

**⏸️ STOP HERE and wait for user approval before proceeding to Phase 2.**

---

### Phase 2: Token Mapping & Creation

**🚨 CRITICAL: EVIDENCE-BASED MAPPING ONLY**

**Step 2.1: Mandatory Search for Each Semantic Token**
For each semantic token in the reference table, you MUST search the codebase to verify actual usage before mapping:

1. **Read the semantic token's "Use For" description**
2. **Search codebase for that specific usage** (customize search terms for each token)
3. **Document the search results** - found or not found
4. **Map ONLY if evidence exists**

**Step 2.2: Document Search Results**
For every semantic token, document whether usage was found:

```
🔍 SEARCH RESULTS DOCUMENTATION:

--color-brand-primary:
├── PURPOSE: "Primary buttons, main call-to-action backgrounds"
├── SEARCH RESULT: ✅ FOUND - ButtonNew.module.css:13 primary button background
├── EVIDENCE: var(--primary-600) used for primary button
└── MAPPING: --color-brand-primary: #8547ff

--color-container-info:
├── PURPOSE: "Background for informational elements like chips or banners"
├── SEARCH RESULT: ❌ NOT FOUND - No info chips/banners exist in codebase
├── EVIDENCE: No components match this semantic purpose
└── MAPPING: ❌ SKIP - No usage found
```

**Step 2.3: Populate Semantic Tokens with Evidence**
In `src/styles/semantic-color-variables.css`, include file:line evidence for every mapping:

```css
:root {
  /* EVIDENCE-BASED MAPPINGS ONLY */
  --color-brand-primary: #8547ff;         /* EVIDENCE: ButtonNew.module.css:13 var(--primary-600) */
  --color-container-paper: #ffffff;       /* EVIDENCE: NoteEditor.module.css:5, SearchBar.module.css:58 white */
  --color-text-primary: #343a40;          /* EVIDENCE: global.css:7 var(--neutral-800) */
}

/* ==========================================
   SEMANTIC TOKENS NOT FOUND IN CODEBASE
   ==========================================

   The following semantic tokens from the reference table
   have NO actual usage found in this codebase:

   ❌ --color-container-info: No info chips/banners found
   ❌ --color-container-success: No success chips/banners found

   These tokens are NOT included above because they would
   violate the evidence-based mapping requirement.
   ========================================== */
```

**🚨 MANDATORY RULE: Every token must have file:line evidence OR be documented as "NOT FOUND"**

#### 🛑 Phase 2 Completion Summary & Approval

**Before continuing to Phase 3, provide this summary:**

```
## Phase 2 Summary: Token Mapping & Creation

### Color Mappings Created:
[For each semantic token, show the mapping]
- --color-background-layer-base: [value] ← [original source]
- --color-brand-primary: [value] ← [original source]
- --color-text-primary: [value] ← [original source]
[continue for all mapped tokens]

### Edge Cases Handled:
- Multiple values resolved: [list any conflicts and resolutions]
- New semantic tokens created: [list any custom tokens created]
- Complex values preserved: [list gradients/complex values]

### Semantic Tokens File Status:
- ✅ All discovered colors mapped to semantic tokens
- ✅ File compiles without errors
- Total semantic tokens defined: [number]

### Next Phase Preview:
Phase 3 will replace actual color usage in components with these semantic tokens.
Files to be updated: [list of file groups]

**Ready to proceed to Phase 3? Please confirm before continuing.**
```

**⏸️ STOP HERE and wait for user approval before proceeding to Phase 3.**

---

### Phase 3: Implementation

Execute in this order to minimize dependencies:

**Step 3.1: UI Foundation (`src/components/ui/`)**
Replace all color usage with semantic tokens in base components like Button, Input, Card.

#### 🛑 Step 3.1 Completion Summary & Approval

**After completing UI Foundation components, provide this summary:**

```
## Step 3.1 Summary: UI Foundation Components

### Files Updated:
[List all files in src/components/ui/ that were modified]

### Color Replacements Made:
- [original color] → [semantic token] in [component/file]
[continue for all replacements in this step]

### Components Now Using Semantic Tokens:
- [list all UI components updated]

### Verification:
- ✅ Visual appearance unchanged
- ✅ All hover/focus states work
- ✅ No build errors
- ✅ **No non-semantic variables remain in UI components** (run verification grep if needed)

**Ready to proceed to Step 3.2 (Feature Components)? Please confirm before continuing.**
```

**⏸️ STOP HERE and wait for user approval before proceeding to Step 3.2.**

**Step 3.2: Feature Components (`src/components/`)**
Update all feature components to use semantic tokens.

#### 🛑 Step 3.2 Completion Summary & Approval

**After completing Feature Components, provide this summary:**

```
## Step 3.2 Summary: Feature Components

### Files Updated:
[List all files in src/components/ that were modified]

### Color Replacements Made:
- [original color] → [semantic token] in [component/file]
[continue for all replacements in this step]

### Components Now Using Semantic Tokens:
- [list all feature components updated]

### Verification:
- ✅ Visual appearance unchanged
- ✅ All interactive states work
- ✅ No build errors
- ✅ **No non-semantic variables remain in feature components** (run verification grep if needed)

**Ready to proceed to Step 3.3 (Pages & Global)? Please confirm before continuing.**
```

**⏸️ STOP HERE and wait for user approval before proceeding to Step 3.3.**

**Step 3.3: Pages & Global (`src/pages/`, global styles, `src/App.tsx`)**
Update page-level components and global styles.

#### 🛑 Phase 3 Completion Summary & Approval

**After completing all implementation steps, provide this summary:**

```
## Phase 3 Summary: Complete Implementation

### All Files Updated:
- UI Foundation: [count] files
- Feature Components: [count] files
- Pages & Global: [count] files
- Total files modified: [count]

### Migration Statistics:
- Total color replacements made: [number]
- Semantic tokens in use: [number]
- Files now using only semantic tokens: [count]

### Verification:
- ✅ All components visually identical
- ✅ All interactions work properly
- ✅ Development server runs without errors
- ✅ No hard-coded colors remain (except in semantic-color-variables.css)

**Ready to proceed to Phase 4 (Final Validation)? Please confirm before continuing.**
```

**⏸️ STOP HERE and wait for user approval before proceeding to Phase 4.**

---

### Phase 4: Validation

**🔍 Required Verification Commands:**
```bash
# Verify NO non-semantic variables remain (result must be empty)
grep -r "var(--(?!color-|spacing-|radius-|shadow-|transition-)" src/ --include="*.css" --include="*.css"

# Verify no hardcoded colors remain outside semantic-color-variables.css
grep -r "#[0-9a-fA-F]\{3,6\}\|rgb\|hsl\|rgba\|hsla" src/ --exclude="semantic-color-variables.css" --include="*.css" --include="*.css"

# Verify no named colors remain
grep -r "\bwhite\b\|\bblack\b\|\bred\b\|\bblue\b\|\bgreen\b" src/ --exclude="semantic-color-variables.css" --include="*.css" --include="*.css"
```

**⚠️ CRITICAL:** All above commands MUST return empty results (except for semantic-color-variables.css)

**Additional Validation:**
- Ensure no color values remain outside of `semantic-color-variables.css`
- Run linting: `npm run lint` (if available)
- Run build: `npm run build` (if available)

#### 🛑 Final Completion Summary

**After completing all validation, provide this final summary:**

```
## Migration Complete: Final Summary

### ✅ Successfully Migrated Design Tokens

### Files Structure:
- ✅ src/styles/semantic-color-variables.css (contains all color definitions)
- ✅ [main CSS file] (imports semantic tokens)
- ✅ All component files use only semantic tokens

### Quality Assurance:
- ✅ Visual regression test passed
- ✅ All interactive states functional
- ✅ Build process successful
- ✅ Linting passes
- ✅ **CRITICAL VERIFICATION PASSED:**
  - ✅ No non-semantic CSS variables remain (grep command returned empty)
  - ✅ No hardcoded colors outside semantic-color-variables.css (grep command returned empty)
  - ✅ No named colors outside semantic-color-variables.css (grep command returned empty)
  - ✅ Only semantic tokens (--color-*) used throughout codebase

### Benefits Achieved:
- 🎨 Centralized color management
- 🔧 Maintainable design system
- ♿ Accessible color tokens
- 🚀 Future theming support enabled

### Semantic Tokens Created: [number]
### Files Migrated: [number]
### Total Color Replacements: [number]

**Design token migration completed successfully!**
```

---

## 🚨 Critical Rules - What NOT To Do

**These practices will BREAK the migration and MUST be avoided:**

❌ **NEVER create compatibility mappings in semantic-color-variables.css:**
```css
/* BAD - This defeats the entire purpose */
:root {
  --color-brand-primary: #0066cc;
  --background: var(--color-background-layer-base);  /* ❌ NO! */
  --foreground: var(--color-text-primary);           /* ❌ NO! */
  --muted-foreground: var(--color-text-secondary);   /* ❌ NO! */
}
```

❌ **NEVER leave non-semantic variables as bridges:**
```css
/* BAD - Find and replace these directly */
.component {
  color: var(--muted-foreground);  /* ❌ Replace with --color-text-secondary */
  background: var(--background);   /* ❌ Replace with --color-container-paper */
  border: 1px solid var(--border-color); /* ❌ Replace with --color-borders-default */
}
```

✅ **CORRECT approach - Direct semantic token usage:**
```css
/* GOOD - Only semantic tokens */
:root {
  --color-brand-primary: #0066cc;
  --color-background-layer-base: white;
  --color-text-secondary: #666;
}

.component {
  color: var(--color-text-secondary);
  background: var(--color-container-paper);
  border: 1px solid var(--color-borders-default);
}
```

**Final result requirement:** The ONLY CSS variables allowed in the final codebase are:
- `--color-{section}-{token}` (semantic tokens)
- `--spacing-*`, `--radius-*`, `--shadow-*`, `--transition-*` (non-color design tokens)
- Primitive color values in `semantic-color-variables.css` ONLY

---

## Implementation Rules

1. **Single Source of Truth**: Only `src/styles/semantic-color-variables.css` may contain actual color values

2. **Complete Replacement**: Replace ALL color usage (variables, hex, rgb, named colors) with semantic tokens

3. **Preserve Functionality**: The visual result must be identical before/after migration

4. **Use Reference Table**: Always refer to the semantic token table for proper mapping

5. **🚨 NEVER Create Compatibility Mappings**: Directly replace all color usage with semantic tokens. Never create bridges like `--background: var(--color-background-layer-base)`

6. **🔍 Work Only With What's Discovered**: Don't assume any existing CSS variables exist - systematically scan and work only with what's actually found in the codebase

7. **📝 Document Decisions**: Note any ambiguous mappings or new tokens created during migration

8. **✅ Verify at Every Phase**: Use the provided grep commands to verify no non-semantic variables remain after each major step

---

## Example Transformations

**Before:**
```css
.button-primary { background: #0066cc; }
.card { background: white; border: 1px solid #ddd; }
.text { color: #333; }
```

**After:**
```css
.button-primary { background: var(--color-brand-primary); }
.card {
  background: var(--color-container-paper);
  border: 1px solid var(--color-borders-default);
}
.text { color: var(--color-text-primary); }
```

**Semantic tokens file:**
```css
:root {
  --color-brand-primary: #0066cc;
  --color-container-paper: white;
  --color-borders-default: #ddd;
  --color-text-primary: #333;
}
```
