---
name: deep-refactor
description: "Deep code analysis and refactoring. Use PROACTIVELY when the user asks to refactor, refatorar, clean up, analyze code quality, remove dead code, fix lint errors, extract constants, improve performance, or do a code review of a specific file or page. Also triggers on requests like 'analise esse arquivo', 'limpa esse codigo', 'melhora esse componente', 'remove codigo morto', 'extraia constantes', or any request to systematically improve code quality of a file."
version: 1.0.0
---

# Deep Code Analysis & Refactoring

Analyze the target file deeply and execute a complete refactoring following these phases in order.

## Phase 1 — Diagnosis (DO NOT change anything yet)

Read the entire file and list:

1. **Dead code**: unused imports, declared but never read variables, uncalled functions, unjustified commented-out code
2. **Duplicated code**: repeated blocks, functions, or patterns — candidates for extraction
3. **Weak TypeScript**: use of `any`, props without interface/type, overly generic types
4. **Bloated components**: components with >150 lines, deeply nested JSX, multiple responsibilities
5. **Performance issues**: unnecessary re-renders, functions recreated every render without useCallback, expensive computations without useMemo, large lists without virtualization
6. **Poor state management**: excessive prop drilling, duplicated state, useState where useReducer would be better
7. **Misused hooks**: missing/excess dependencies in useEffect/useMemo/useCallback, complex logic that should be a custom hook
8. **Mixed logic and presentation**: API calls directly in UI components, business rules embedded in JSX
9. **Missing error handling**: async calls without try/catch, silent errors, missing user feedback
10. **Accessibility**: interactive elements without labels, images without alt, incorrect HTML semantics
11. **Hardcoded data**: identify ALL fixed values embedded directly in code, including:
    - User-visible text strings (titles, labels, placeholders, error/success messages, tooltips)
    - API URLs, endpoints, base URLs
    - Magic numbers (timeouts, limits, sizes, breakpoints, animation durations)
    - Colors defined inline or outside the design system/theme
    - Fixed configs (keys, feature flags, route names, analytics event names)
    - Static arrays or objects used to render lists, menus, tabs, select options
    - Validation messages written directly in code
    - Column names, table headers, form labels
12. **Lint and formatting errors**: run the project linter and identify ALL issues, including:
    - ESLint/Biome/Prettier violations configured in the project
    - Variables declared with `var` that should be `const` or `let`
    - Use of `==` instead of `===`
    - Console.log/console.warn/console.error left in production code
    - Formatting inconsistencies: mixed indentation, trailing commas, inconsistent semicolons
    - Names violating project convention (camelCase, PascalCase, UPPER_SNAKE_CASE)
    - Disordered imports or imports not following linter pattern
    - Functions with high cyclomatic complexity (many nested if/else/switch)
    - Implicit types that the linter flags as needing explicit annotation
    - React-specific: missing key prop in lists, incorrect hook dependencies, index used as key
    - Promises without await or `.catch()`
    - Unnecessary or always truthy/falsy conditions

## Phase 2 — Refactoring (apply the changes)

Based on the diagnosis, execute:

### Lint and Formatting Fixes (DO THIS FIRST)
- [ ] Run `npx eslint [FILE_PATH] --fix` (or biome/prettier per project) and apply auto-fixes
- [ ] Manually fix ALL errors that auto-fix did not resolve
- [ ] Replace all `var` with `const` (or `let` if reassigned)
- [ ] Replace `==` with `===` and `!=` with `!==` in all comparisons
- [ ] Remove ALL debug `console.log`, `console.warn`, `console.error` — if any are needed in production, replace with an appropriate logger
- [ ] Normalize formatting: consistent indentation, trailing commas, semicolons per project config
- [ ] Standardize naming: components in PascalCase, hooks with `use` prefix, primitive constants in UPPER_SNAKE_CASE, functions/variables in camelCase
- [ ] Add proper `key` props in all list iterations (NEVER use index as key if items can reorder)
- [ ] Simplify complex conditionals: extract to descriptively named functions or use early returns
- [ ] Add `await` to every Promise that needs to be awaited or handle with `.catch()`
- [ ] Ensure `npx eslint [FILE_PATH]` returns **ZERO errors and ZERO warnings** at the end

### Hardcoded Data Elimination
- [ ] Extract ALL UI strings to a dedicated constants file (`constants/strings.ts` or `constants/[pageName].ts`)
- [ ] Move URLs and endpoints to a centralized config file (`config/api.ts` or `services/endpoints.ts`)
- [ ] Replace magic numbers with clearly named constants (e.g., `const DEBOUNCE_DELAY_MS = 300`, `const MAX_UPLOAD_SIZE_MB = 5`, `const ITEMS_PER_PAGE = 20`)
- [ ] Extract static data arrays/objects (menus, tabs, select options, table columns) to `data/` or `constants/` files with strong typing
- [ ] Move inline colors to the project theme/design tokens — if no theme system exists, create `constants/theme.ts`
- [ ] Centralize validation messages in a file (`constants/validationMessages.ts` or alongside the validation schema)
- [ ] Extract configs (feature flags, limits, timeouts) to `config/settings.ts`
- [ ] Ensure each exported constant has `as const` when applicable for maximum type-safety

### Cleanup and Quality
- [ ] Remove ALL dead code (imports, variables, functions, useless comments)
- [ ] Extract duplicated code into utility functions or custom hooks in separate files
- [ ] Replace all `any` with specific types; create interfaces for all props
- [ ] Break large components into focused subcomponents (max ~150 lines each)

### Performance
- [ ] Add `React.memo()` to components that receive stable props but re-render
- [ ] Wrap handlers with `useCallback()` and derived calculations with `useMemo()` where it makes a real difference
- [ ] Move static objects and arrays OUTSIDE the component body — defining inside causes recreation every render and breaks dependency references

### Architecture
- [ ] Extract business logic and API calls to custom hooks (`useHookName.ts`)
- [ ] Add proper error handling with visual user feedback
- [ ] Fix accessibility issues (aria-labels, alt texts, semantics)
- [ ] Organize imports: React first, external libs, internal by path — remove unused ones

## Mandatory Rules

- **DO NOT change functional behavior** — the final result must work exactly the same
- **DO NOT add new dependencies** without asking first
- Maintain **consistent naming** with the rest of the project
- Use **TypeScript strict** — zero `any`, zero unnecessary `as unknown as X`
- Extracted constants must use **UPPER_SNAKE_CASE** for primitive values and **camelCase** for complex objects/arrays
- Each new file created must have a descriptive name and be in the correct project folder
- Prefer `as const` in constant objects and arrays for literal type inference
- The final code MUST pass the linter with **zero errors and zero warnings**
- At the end, list a summary of what was done and what improved

## Expected File Structure for Created Files

```
src/
├── constants/
│   ├── strings.ts
│   ├── validationMessages.ts
│   └── [pageName].ts
├── config/
│   ├── api.ts
│   └── settings.ts
├── data/
│   └── [pageName]Data.ts
├── hooks/
│   └── use[HookName].ts
└── utils/
    └── [utilName].ts
```

Adapt paths to the project's existing structure. If the project already has a `constants/`, `hooks/`, or `utils/` folder, use those instead of creating new ones.

## Expected Output

1. **Diagnosis report** (Phase 1) — with a dedicated section for each item including lint errors and hardcoded values
2. **Linter result BEFORE** refactoring (number of errors/warnings)
3. **Refactored code** applied to files
4. **Linter result AFTER** refactoring (must be zero)
5. **Final summary**: what changed, how many lines removed/moved, how many lint errors fixed, how many hardcoded values extracted, and what performance/readability/maintainability gains were achieved
