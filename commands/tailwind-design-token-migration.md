# Tailwind v4 Design Tokens Migration

Migrates any Tailwind v4 codebase to use semantic color tokens with CSS variables as the base and Tailwind utility classes for usage.

## Overview

This command systematically converts primitive color tokens (e.g., `--color-primary-600`, `--color-neutral-200`) and hardcoded colors to semantic design tokens following the `--color-{section}-{token}` naming convention.

**Architecture:**
```
base-semantic-color-variables.css  →  main.css (@theme)  →  Tailwind classes
      (source of truth)                (integration)         (usage in TSX)
```

**Target Files:**
- `src/**/*.{ts,tsx}` - Tailwind class replacements
- `src/**/*.css` - CSS variable replacements

## Usage

Use the `/tailwind-design-token-migration` slash command to start the automated migration process.

---

## Semantic Token Reference Table

Use this table as the definitive guide for mapping any color usage to semantic tokens:

### `background` — non-clickable page layers

| Token        | CSS Variable                    | Tailwind Class             | Use For                                  |
|--------------|---------------------------------|----------------------------|------------------------------------------|
| `layer-base` | `--color-background-layer-base` | `bg-background-layer-base` | Base layer (e.g., body background)       |
| `layer-2`    | `--color-background-layer-2`    | `bg-background-layer-2`    | Layer above base                         |
| `layer-3`    | `--color-background-layer-3`    | `bg-background-layer-3`    | Layer above layer-2                      |

### `brand` — strong interactive component backgrounds

| Token              | CSS Variable                       | Tailwind Classes                           | Use For                                 |
|--------------------|------------------------------------|--------------------------------------------|----------------------------------------|
| `primary`          | `--color-brand-primary`            | `bg-brand-primary`, `border-brand-primary` | Primary buttons, main CTA backgrounds  |
| `primary-hover`    | `--color-brand-primary-hover`      | `hover:bg-brand-primary-hover`             | Hover state for primary elements       |
| `primary-selected` | `--color-brand-primary-selected`   | `bg-brand-primary-selected`                | Selected/active state                  |

### `status` — state indicators (interactive or text)

| Token     | CSS Variable            | Tailwind Classes                       | Use For                                    |
|-----------|-------------------------|----------------------------------------|--------------------------------------------|
| `error`   | `--color-status-error`  | `bg-status-error`, `text-status-error` | Error buttons, critical alerts             |
| `info`    | `--color-status-info`   | `bg-status-info`, `text-status-info`   | Info buttons, informational alerts         |
| `success` | `--color-status-success`| `bg-status-success`, `text-status-success` | Success buttons, positive feedback     |

### `container` — muted component backgrounds

| Token            | CSS Variable                        | Tailwind Class                 | Use For                                           |
|------------------|-------------------------------------|--------------------------------|---------------------------------------------------|
| `paper`          | `--color-container-paper`           | `bg-container-paper`           | Cards, dialogs, sheets                            |
| `paper-hover`    | `--color-container-paper-hover`     | `hover:bg-container-paper-hover` | Hoverable component backgrounds                 |
| `paper-selected` | `--color-container-paper-selected`  | `bg-container-paper-selected`  | Selected component backgrounds                    |
| `info`           | `--color-container-info`            | `bg-container-info`            | Background for info elements (chips, banners)     |
| `success`        | `--color-container-success`         | `bg-container-success`         | Background for success states                     |
| `error`          | `--color-container-error`           | `bg-container-error`           | Background for error states                       |

### `text` — all typography colors

| Token       | CSS Variable             | Tailwind Class       | Use For                                    |
|-------------|--------------------------|----------------------|--------------------------------------------|
| `primary`   | `--color-text-primary`   | `text-text-primary`  | Main body text, highest contrast           |
| `secondary` | `--color-text-secondary` | `text-text-secondary`| Supporting text, descriptions              |
| `tertiary`  | `--color-text-tertiary`  | `text-text-tertiary` | Placeholder text, disabled text            |
| `link`      | `--color-text-link`      | `text-text-link`     | Hyperlinks, clickable text                 |
| `contrast`  | `--color-text-contrast`  | `text-text-contrast` | Text on dark/brand backgrounds             |

### `borders` — component outlines

| Token     | CSS Variable              | Tailwind Class         | Use For                               |
|-----------|---------------------------|------------------------|---------------------------------------|
| `default` | `--color-borders-default` | `border-borders-default` | Input borders, card outlines        |
| `focus`   | `--color-borders-focus`   | `border-borders-focus`   | Focus states, keyboard navigation   |

### `shadows` — elevation and focus effects

| Token    | CSS Variable             | Use For                                    |
|----------|--------------------------|-------------------------------------------|
| `small`  | `--color-shadows-small`  | Subtle hover effects, minor elevation     |
| `medium` | `--color-shadows-medium` | Card shadows, standard elevation          |
| `large`  | `--color-shadows-large`  | Floating panels, dropdown menus           |
| `focus`  | `--color-shadows-focus`  | Focus indicators, keyboard navigation     |

---

## Migration Process

### Phase 1: Discovery & Setup

**Step 1.1: Scan for All Color Usage**

Search the entire codebase for color usage in both TSX and CSS files:

**🔍 Required Search Commands:**
```bash
# Find all Tailwind color classes in TSX/JSX files
grep -rE "(bg|text|border|ring|shadow|outline|fill|stroke)-(primary|neutral|danger|success|warning|info|white|black|gray|red|blue|green)" src/ --include="*.tsx" --include="*.ts"

# Find hardcoded colors in className strings
grep -rE 'className.*#[0-9a-fA-F]{3,6}' src/ --include="*.tsx" --include="*.ts"

# Find inline style color values
grep -rE "style=.*color|backgroundColor" src/ --include="*.tsx" --include="*.ts"

# Find CSS variable usage in stylesheets
grep -r "var(--color" src/ --include="*.css"

# Extract unique Tailwind color classes
grep -rEo "(bg|text|border|ring)-(primary|neutral|danger|success|warning|info)-[0-9]*" src/ --include="*.tsx" | sort | uniq

# Find hardcoded colors in CSS files
grep -rE "#[0-9a-fA-F]{3,6}|rgb|hsl|rgba|hsla" src/ --include="*.css"
```

**⚠️ Critical Verification: Complete Color Analysis**

Document ALL color usage found, categorizing as:

**🎯 PRIORITY: For Semantic Migration:**
- Primitive Tailwind classes (e.g., `bg-primary-600`, `text-neutral-700`)
- CSS var() with primitives (e.g., `var(--color-primary-600)`)
- Hardcoded hex/rgb values

**✅ KEEP: Non-Color Tokens:**
- `--radius-*` (border radius)
- `--shadow-*` (shadows/elevation)
- `--breakpoint-*` (responsive breakpoints)

**Step 1.2: Create Semantic Tokens File**

Create `src/styles/base-semantic-color-variables.css`:

```css
/* src/styles/base-semantic-color-variables.css */
/* Base semantic color tokens - single source of truth */

:root {
  /* Brand */
  --color-brand-primary: #8547ff;
  --color-brand-primary-hover: #6d35d9;
  --color-brand-primary-selected: #5a2bb8;

  /* Background */
  --color-background-layer-base: #ffffff;
  --color-background-layer-2: #f8f9fa;
  --color-background-layer-3: #e9ecef;

  /* Text */
  --color-text-primary: #212529;
  --color-text-secondary: #6c757d;
  --color-text-tertiary: #adb5bd;
  --color-text-link: #8547ff;
  --color-text-contrast: #ffffff;

  /* Container */
  --color-container-paper: #ffffff;
  --color-container-paper-hover: #f8f9fa;
  --color-container-paper-selected: #e9ecef;
  --color-container-info: #e7f5ff;
  --color-container-success: #d3f9d8;
  --color-container-error: #ffe3e3;

  /* Status */
  --color-status-error: #dc3545;
  --color-status-success: #28a745;
  --color-status-info: #17a2b8;

  /* Borders */
  --color-borders-default: #dee2e6;
  --color-borders-focus: #8547ff;

  /* Shadows */
  --color-shadows-small: rgba(0, 0, 0, 0.05);
  --color-shadows-medium: rgba(0, 0, 0, 0.1);
  --color-shadows-large: rgba(0, 0, 0, 0.15);
  --color-shadows-focus: rgba(133, 71, 255, 0.2);
}
```

**Step 1.3: Set Up Import and @theme Reference**

Update `src/styles/main.css` to import and reference semantic tokens:

```css
/* At the top of main.css */
@import './base-semantic-color-variables.css';

@theme {
  /* Reference semantic tokens for Tailwind classes */
  --color-brand-primary: var(--color-brand-primary);
  --color-brand-primary-hover: var(--color-brand-primary-hover);
  --color-brand-primary-selected: var(--color-brand-primary-selected);

  --color-background-layer-base: var(--color-background-layer-base);
  --color-background-layer-2: var(--color-background-layer-2);
  --color-background-layer-3: var(--color-background-layer-3);

  --color-text-primary: var(--color-text-primary);
  --color-text-secondary: var(--color-text-secondary);
  --color-text-tertiary: var(--color-text-tertiary);
  --color-text-link: var(--color-text-link);
  --color-text-contrast: var(--color-text-contrast);

  --color-container-paper: var(--color-container-paper);
  --color-container-paper-hover: var(--color-container-paper-hover);
  --color-container-paper-selected: var(--color-container-paper-selected);
  --color-container-info: var(--color-container-info);
  --color-container-success: var(--color-container-success);
  --color-container-error: var(--color-container-error);

  --color-status-error: var(--color-status-error);
  --color-status-success: var(--color-status-success);
  --color-status-info: var(--color-status-info);

  --color-borders-default: var(--color-borders-default);
  --color-borders-focus: var(--color-borders-focus);

  /* Non-color tokens (keep as-is) */
  --radius-sm: 0.25rem;
  --radius-md: 0.5rem;
  --radius-lg: 1rem;
  /* ... */
}
```

**Step 1.4: Body Background Check**

Ensure body uses semantic background token:

```css
@layer base {
  body {
    background-color: var(--color-background-layer-base);
    color: var(--color-text-primary);
  }
}
```

#### 🛑 Phase 1 Completion Summary & Approval

**Before continuing to Phase 2, provide this summary:**

```
## Phase 1 Summary: Discovery & Setup

### Files Scanned:
- [List all files searched for color usage]

### Colors Found:
**Tailwind Classes:**
- [list all bg-*, text-*, border-* primitive classes found]

**CSS Variables:**
- [list all var(--color-*) primitive usage found]

**Hardcoded Colors:**
- [list all hex/rgb values found]

### Files Created:
- ✅ src/styles/base-semantic-color-variables.css (semantic tokens)
- ✅ Import and @theme reference added to main.css
- ✅ Body background verified with semantic token

### Next Phase Preview:
Phase 2 will map discovered colors to semantic tokens.

**Ready to proceed to Phase 2? Please confirm before continuing.**
```

**⏸️ STOP HERE and wait for user approval before proceeding to Phase 2.**

---

### Phase 2: Token Mapping & Creation

**🚨 CRITICAL: EVIDENCE-BASED MAPPING ONLY**

**Step 2.1: Mandatory Search for Each Semantic Token**

For each semantic token, search the codebase to verify actual usage:

1. **Read the semantic token's "Use For" description**
2. **Search codebase for that specific usage**
3. **Document the search results** - found or not found
4. **Map ONLY if evidence exists**

**Step 2.2: Document Search Results**

```
🔍 SEARCH RESULTS DOCUMENTATION:

--color-brand-primary (bg-brand-primary):
├── PURPOSE: "Primary buttons, main CTA backgrounds"
├── SEARCH: grep -r "bg-primary-600" src/
├── FOUND: ✅ ButtonNew.tsx:45 - `bg-primary-600 hover:bg-primary-700`
├── EVIDENCE: Primary button variant uses primary-600
└── MAPPING:
    CSS: --color-brand-primary: #8547ff
    CLASS: bg-primary-600 → bg-brand-primary

--color-text-secondary (text-text-secondary):
├── PURPOSE: "Supporting text, descriptions"
├── SEARCH: grep -r "text-neutral-600\|text-neutral-500" src/
├── FOUND: ✅ Sidebar.tsx:23, NotesList.tsx:89
├── EVIDENCE: Secondary text uses neutral-600
└── MAPPING:
    CSS: --color-text-secondary: #6c757d
    CLASS: text-neutral-600 → text-text-secondary

--color-container-info:
├── PURPOSE: "Background for info chips/banners"
├── SEARCH: grep -r "bg-info\|info.*background" src/
├── FOUND: ❌ NOT FOUND - No info chips/banners in codebase
├── EVIDENCE: No components match this purpose
└── MAPPING: ❌ SKIP - No usage found
```

**Step 2.3: Update Semantic Tokens File**

In `base-semantic-color-variables.css`, add file:line evidence as comments:

```css
:root {
  /* EVIDENCE-BASED MAPPINGS */
  --color-brand-primary: #8547ff;         /* ButtonNew.tsx:45 bg-primary-600 */
  --color-container-paper: #ffffff;       /* NoteEditor.tsx:12, SearchBar.tsx:58 */
  --color-text-primary: #212529;          /* global body text, NotesList.tsx:34 */
}

/* ==========================================
   SEMANTIC TOKENS NOT FOUND IN CODEBASE
   ==========================================

   ❌ --color-container-info: No info chips/banners found
   ❌ --color-container-success: No success chips/banners found

   These tokens are NOT included because they have no usage.
   ========================================== */
```

#### 🛑 Phase 2 Completion Summary & Approval

```
## Phase 2 Summary: Token Mapping

### Mappings Created:
[For each semantic token with evidence]
- --color-brand-primary: #8547ff ← bg-primary-600
- --color-text-primary: #212529 ← text-neutral-900
[continue for all mapped tokens]

### Class Replacements Planned:
- bg-primary-600 → bg-brand-primary
- text-neutral-600 → text-text-secondary
[continue for all class replacements]

### CSS var() Replacements Planned:
- var(--color-primary-600) → var(--color-brand-primary)
- var(--color-neutral-200) → var(--color-borders-default)
[continue for all var() replacements]

### Tokens Skipped (no usage found):
- [list tokens with no evidence]

**Ready to proceed to Phase 3? Please confirm before continuing.**
```

**⏸️ STOP HERE and wait for user approval before proceeding to Phase 3.**

---

### Phase 3: Implementation

Execute in this order to minimize dependencies:

**Step 3.1: UI Foundation (`src/components/ui/`)**

Replace all color usage in base components. Handle CVA variants carefully:

```tsx
// Before (ButtonNew.tsx)
const buttonVariants = cva("...", {
  variants: {
    variant: {
      primary: "bg-primary-600 hover:bg-primary-700 text-white",
      secondary: "bg-neutral-100 hover:bg-neutral-200 text-neutral-700",
    }
  }
});

// After
const buttonVariants = cva("...", {
  variants: {
    variant: {
      primary: "bg-brand-primary hover:bg-brand-primary-hover text-text-contrast",
      secondary: "bg-container-paper-hover hover:bg-background-layer-3 text-text-primary",
    }
  }
});
```

#### 🛑 Step 3.1 Summary & Approval

```
## Step 3.1 Summary: UI Foundation

### Files Updated:
[List all files in src/components/ui/]

### Replacements Made:
- ButtonNew.tsx: bg-primary-600 → bg-brand-primary
- Input.tsx: border-neutral-300 → border-borders-default
[continue for all replacements]

### CVA Variants Updated:
- [list all CVA definitions modified]

**Ready to proceed to Step 3.2 (Feature Components)? Please confirm.**
```

**⏸️ STOP HERE and wait for user approval.**

**Step 3.2: Feature Components (`src/components/`)**

Update all feature components:

```tsx
// Before (NotesList.tsx)
<div className="bg-primary-100 border-l-primary-600">

// After
<div className="bg-brand-primary/10 border-l-brand-primary">
```

#### 🛑 Step 3.2 Summary & Approval

```
## Step 3.2 Summary: Feature Components

### Files Updated:
[List all feature component files]

### Replacements Made:
[List all replacements]

**Ready to proceed to Step 3.3 (Pages & Global)? Please confirm.**
```

**⏸️ STOP HERE and wait for user approval.**

**Step 3.3: Pages, Global Styles & CSS Files**

Update page components, global styles, and any CSS files using var():

```css
/* Before (markdown-preview.css) */
border-bottom: 1px solid var(--color-neutral-200);
background-color: var(--color-neutral-100);

/* After */
border-bottom: 1px solid var(--color-borders-default);
background-color: var(--color-background-layer-2);
```

#### 🛑 Phase 3 Completion Summary & Approval

```
## Phase 3 Summary: Complete Implementation

### All Files Updated:
- UI Foundation: [count] files
- Feature Components: [count] files
- Pages & Global: [count] files
- CSS Files: [count] files
- Total: [count] files

### Total Replacements:
- Tailwind class replacements: [number]
- CSS var() replacements: [number]

**Ready to proceed to Phase 4 (Validation)? Please confirm.**
```

**⏸️ STOP HERE and wait for user approval.**

---

### Phase 4: Validation

**🔍 Required Verification Commands:**

```bash
# Verify NO primitive Tailwind classes remain (must be empty)
grep -rE "(bg|text|border)-(primary|neutral)-[0-9]+" src/ --include="*.tsx" --include="*.ts"

# Verify NO primitive CSS variables remain (must be empty)
grep -rE "var\(--color-(primary|neutral)-[0-9]+" src/ --include="*.css"

# Verify NO hardcoded colors remain outside base file
grep -rE "#[0-9a-fA-F]{3,6}" src/ --include="*.css" | grep -v "base-semantic-color-variables"

# Verify semantic tokens ARE being used
grep -rE "(bg|text|border)-(brand|text|container|background|status|borders)" src/ --include="*.tsx"
```

**⚠️ CRITICAL:** All primitive searches MUST return empty results.

**Additional Validation:**
- Run build: `npm run build`
- Run lint: `npm run lint`
- Visual inspection: verify appearance unchanged

#### 🛑 Phase 4 Completion Summary & Approval

```
## Phase 4 Summary: Validation

### Verification Results:
- ✅ No primitive Tailwind classes (grep returned empty)
- ✅ No primitive CSS variables (grep returned empty)
- ✅ No hardcoded colors outside base file (grep returned empty)
- ✅ Semantic tokens in use (grep found [N] usages)

### Build & Lint:
- ✅ npm run build: SUCCESS
- ✅ npm run lint: SUCCESS

**Ready to proceed to Phase 5 (Primitive Cleanup)? Please confirm.**
```

**⏸️ STOP HERE and wait for user approval.**

---

### Phase 5: Remove Primitive Tokens

**Step 5.1: Final Verification**

```bash
# Absolutely confirm no primitives used anywhere
grep -rE "(primary|neutral)-[0-9]+" src/ --include="*.tsx" --include="*.ts" --include="*.css"
```

**Step 5.2: Remove Primitives from @theme**

Edit `src/styles/main.css` and remove ALL primitive color tokens:

```css
@theme {
  /* ❌ REMOVE ALL OF THESE */
  /* --color-primary-100: #dfd3f5; */
  /* --color-primary-200: #c0a8eb; */
  /* ... */
  /* --color-neutral-50: #f8f9fa; */
  /* --color-neutral-100: #e9ecef; */
  /* ... */
  /* --color-danger: #dc3545; */
  /* --color-success: #28a745; */
  /* ... */

  /* ✅ KEEP ONLY SEMANTIC TOKENS */
  --color-brand-primary: var(--color-brand-primary);
  --color-brand-primary-hover: var(--color-brand-primary-hover);
  /* ... */

  /* ✅ KEEP NON-COLOR TOKENS */
  --radius-sm: 0.25rem;
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --breakpoint-md: 900px;
}
```

**Step 5.3: Final Build Verification**

```bash
npm run build
npm run lint
```

#### 🛑 Final Completion Summary

```
## Migration Complete: Final Summary

### ✅ Successfully Migrated Design Tokens

### Architecture:
- ✅ src/styles/base-semantic-color-variables.css (source of truth)
- ✅ src/styles/main.css (imports base, @theme references semantic tokens)
- ✅ All components use semantic Tailwind classes
- ✅ All CSS files use semantic var() tokens

### Primitive Tokens Removed:
- --color-primary-* (all shades)
- --color-neutral-* (all shades)
- --color-danger, --color-success, --color-warning, --color-info

### Quality Assurance:
- ✅ Visual appearance unchanged
- ✅ Build successful
- ✅ Lint passes
- ✅ All verification greps return empty

### Statistics:
- Semantic tokens created: [number]
- Files migrated: [number]
- Total replacements: [number]

**Design token migration completed successfully!**
```

---

## 🚨 Critical Rules - What NOT To Do

**These practices will BREAK the migration:**

❌ **NEVER keep primitive tokens alongside semantic:**
```css
/* BAD */
@theme {
  --color-primary-600: #8547ff;        /* ❌ Remove this */
  --color-brand-primary: #8547ff;      /* ✅ Keep only this */
}
```

❌ **NEVER use primitives in components:**
```tsx
/* BAD */
<button className="bg-primary-600">   /* ❌ */

/* GOOD */
<button className="bg-brand-primary"> /* ✅ */
```

❌ **NEVER reference primitives in base file:**
```css
/* BAD - base-semantic-color-variables.css */
:root {
  --color-brand-primary: var(--color-primary-600); /* ❌ */
}

/* GOOD */
:root {
  --color-brand-primary: #8547ff; /* ✅ Hardcoded value */
}
```

---

## Example Transformations

### Tailwind Classes (TSX)

**Before:**
```tsx
<button className="bg-primary-600 hover:bg-primary-700 text-white">
<div className="bg-neutral-50 border border-neutral-200">
<p className="text-neutral-600">
```

**After:**
```tsx
<button className="bg-brand-primary hover:bg-brand-primary-hover text-text-contrast">
<div className="bg-background-layer-2 border border-borders-default">
<p className="text-text-secondary">
```

### CSS Variables (CSS)

**Before:**
```css
.element {
  background: var(--color-neutral-100);
  border: 1px solid var(--color-neutral-200);
  color: var(--color-neutral-700);
}
```

**After:**
```css
.element {
  background: var(--color-background-layer-2);
  border: 1px solid var(--color-borders-default);
  color: var(--color-text-primary);
}
```

### CVA Variants

**Before:**
```tsx
const chipVariants = cva("...", {
  variants: {
    variant: {
      default: "bg-primary-100 text-primary-700",
      success: "bg-green-100 text-green-700",
    }
  }
});
```

**After:**
```tsx
const chipVariants = cva("...", {
  variants: {
    variant: {
      default: "bg-brand-primary/10 text-brand-primary",
      success: "bg-container-success text-status-success",
    }
  }
});
```

---

## Final File Structure

```
src/styles/
├── base-semantic-color-variables.css  # Source of truth (all hex values)
└── main.css                           # @theme references + non-color tokens
```

**base-semantic-color-variables.css** contains:
- All `--color-{section}-{token}` with hardcoded hex values
- Evidence comments for each mapping

**main.css** contains:
- `@import './base-semantic-color-variables.css'`
- `@theme { }` with `var()` references to semantic tokens
- Non-color tokens (radius, shadow, breakpoint)
- `@layer base/components/utilities` definitions
