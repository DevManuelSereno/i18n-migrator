---
name: polyglot
description: >
  One skill for the entire i18n journey. Sets up i18n from scratch OR migrates
  hardcoded strings to translation calls with minimal diffs. Use when creating
  i18n for the first time, or migrating strings across next-intl, react-i18next,
  vue-i18n, react-intl, i18next, angular, svelte, or lingui. Does NOT translate
  content, refactor existing i18n, or change component architecture.
when_to_use: >
  "add i18n", "setup i18n", "internationalize", "localize", "migrate strings",
  "hardcoded text", "translate", "i18n this component", "add translation keys",
  "create i18n", "configure i18n"
argument-hint: "[mode] [target]"
arguments:
  - mode
  - target
allowed-tools:
  - Read
  - Grep
  - Glob
  - Edit
  - Write
  - Bash(node *)
paths:
  - "**/*.{tsx,jsx,vue,svelte,ts,js}"
  - "**/locales/**"
  - "**/messages/**"
  - "**/i18n/**"
hooks:
  Stop:
    - hooks:
        - type: command
          command: "node ${CLAUDE_SKILL_DIR}/scripts/validate-keys.js"
---

# Polyglot

One skill for the entire i18n journey.

- **Setup** — create i18n from scratch when your project has none
- **Migrate** — surgically migrate hardcoded strings when i18n exists

## Project Context

```!
echo "=== i18n Detection ==="
echo "Library:"
grep -rE "useTranslation|useTranslations|useIntl|\$t\(|formatMessage" \
  --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" \
  --include="*.vue" --include="*.svelte" -l 2>/dev/null | head -3 || echo "  Not found"
echo "Files:"
ls locales/*.json messages/*.json i18n/*.json 2>/dev/null | head -5 || echo "  Not found"
```

## Arguments

- **$mode**: `setup` (create from scratch) or `migrate` (migrate strings). Auto-detected if omitted.
- **$target**: File(s) to migrate (migrate mode only)

## Routing

1. Run discovery → [discovery.md](discovery.md)
2. i18n detected → **Migrate**
3. No i18n → **Setup**
4. User passed explicit mode → follow it

## Scope Rules

### Setup
- Create i18n architecture (providers, config, translation files)
- Follow framework conventions
- Minimal scaffolding — no over-engineering

### Migrate
- Do not modify files outside scope
- Do not create new architecture
- Do not refactor or modernize
- Do not alter business logic
- Do not touch unrelated lines

## Workflow

### Phase 1: Discover

Detect stack → [discovery.md](discovery.md). Low confidence → invoke `/i18n-analyzer`.

### Phase 2: Route

- **Has i18n?** → Migrate
- **No i18n?** → Setup

### Phase 3A: Setup

Follow [setup.md](setup.md):
1. Recommend library based on framework
2. Install dependencies
3. Create config + translation files
4. Add provider to app root
5. Create example component

### Phase 3B: Migrate

Read reference + target + translation files. Extract: hook pattern, key convention, reusable keys.

### Phase 4: Identify (Migrate)

Find: labels, placeholders, errors, aria-labels, tooltips, buttons.
Exclude: constants, logs, identifiers, CSS, data attributes.

### Phase 5: Apply Patterns (Migrate)

Follow [patterns.md](patterns.md): interpolation, pluralization, formatting, rich text.
Smallest change only: replace strings, add hook/import if absent.

### Phase 6: Update Translations

- **Local**: Update files directly
- **Remote**: Output keys for user

### Phase 7: Validate

Auto-runs via Stop hook. Fix errors → re-validate.

### Phase 8: Respond

```
Mode: [setup|migrate]

Files changed:
- path/to/file.tsx

Changes:
- [setup] created i18n config with [library]
- [migrate] migrated N strings
- reused: [key.one]
- added: [key.two]

Validation: ✓ passed

Notes:
- <decisions only>
```

## Key Strategy (Migrate)

1. Check reference for equivalent keys
2. Check target (may be partially migrated)
3. Reuse when semantically equivalent

**Good**: `profile.header.title`, `profile:header.title`
**Bad**: `title`, `headerText`, `page.content.section.label.text`

## Large Files (20+ strings)

Batch by section. Ask scope. One batch at a time.

## Error Handling

| Scenario | Action |
|----------|--------|
| Detection fails | Auto-invoke `/i18n-analyzer` |
| Pattern unclear | Ask before proceeding |
| Validation fails | Fix, re-validate |
| No i18n + user wants migrate | Suggest setup first |

## Resources

- [discovery.md](discovery.md) — Detection + routing
- [setup.md](setup.md) — Scaffolding per library
- [patterns.md](patterns.md) — Interpolation, pluralization, Bom/Ruim
- [examples.md](examples.md) — Before/after for every library
