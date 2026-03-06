# Claude Deep Refactor

A Claude Code plugin that performs deep code analysis and comprehensive refactoring. It diagnoses issues first, then systematically fixes them — all without changing functional behavior.

## What it does

The plugin runs a **two-phase workflow**:

### Phase 1 — Diagnosis (read-only)

Scans the entire file and reports:

- Dead code (unused imports, variables, functions, commented-out code)
- Duplicated code and extraction candidates
- Weak TypeScript (`any`, missing interfaces, generic types)
- Bloated components (>150 lines, nested JSX, multiple responsibilities)
- Performance issues (missing `useCallback`/`useMemo`, unnecessary re-renders)
- Poor state management (prop drilling, duplicated state)
- Misused hooks (wrong dependencies, missing custom hook extraction)
- Mixed logic and presentation (API calls in UI components)
- Missing error handling (async without try/catch, silent errors)
- Accessibility issues (missing labels, alt text, semantics)
- Hardcoded data (magic numbers, inline strings, URLs, colors, static arrays)
- Lint and formatting errors (ESLint/Prettier violations, naming conventions)

### Phase 2 — Refactoring (applies changes)

Based on the diagnosis:

1. **Lint fixes** — auto-fix + manual resolution to zero errors/warnings
2. **Hardcoded data elimination** — extracts strings, magic numbers, configs to constants files
3. **Cleanup** — removes dead code, extracts duplications, replaces `any` with proper types
4. **Performance** — adds `React.memo`, `useCallback`, `useMemo` where it matters
5. **Architecture** — extracts business logic to custom hooks, adds error handling, fixes a11y

## Installation

```bash
# Clone the repository
git clone https://github.com/lmeireles085/claude-deep-refactor.git

# Copy to Claude Code plugins directory
mkdir -p ~/.claude/plugins/marketplaces/custom-plugins/plugins/
cp -r claude-deep-refactor ~/.claude/plugins/marketplaces/custom-plugins/plugins/deep-refactor
```

Restart Claude Code after installing.

## Usage

### Command: `/refatorar`

```
/refatorar src/components/MyComponent.tsx
```

Runs the full diagnosis + refactoring workflow on the specified file.

### Automatic trigger

The `deep-refactor` skill also activates automatically when you ask Claude Code to:

- "refactor this file"
- "clean up this component"
- "remove dead code from this page"
- "analyze code quality of src/pages/Dashboard.tsx"
- "extract constants from this file"
- "fix lint errors in this component"
- "improve this code"

## Plugin structure

```
deep-refactor/
├── .claude-plugin/
│   └── plugin.json              # Plugin metadata
├── skills/
│   └── deep-refactor/
│       └── SKILL.md             # Main skill (full analysis + refactoring prompt)
└── commands/
    └── refatorar.md             # /refatorar command (shortcut)
```

## Rules the plugin follows

- **Never changes functional behavior** — the code works exactly the same after refactoring
- **Never adds new dependencies** without asking first
- **Zero `any`** — enforces TypeScript strict mode
- **Zero lint errors** — final code must pass the linter clean
- Constants use `UPPER_SNAKE_CASE` (primitives) and `camelCase` (objects/arrays)
- Adapts to the project's existing folder structure

## Output

After running, you get:

1. Diagnosis report (every issue found, organized by category)
2. Linter result **before** refactoring
3. All changes applied to files
4. Linter result **after** refactoring (must be zero)
5. Summary: lines removed/moved, lint errors fixed, hardcoded values extracted, gains achieved

## License

MIT

## Author

Leopoldo Meireles
