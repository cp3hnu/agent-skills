---
name: react-formatting
description: Use when setting up or fixing lint/format inconsistencies in a React project without Next.js (Vite, CRA, etc.), when commit messages fail conventional-commit checks, or when configuring eslint-plugin-react, Prettier, Husky, lint-staged, commitlint, and VSCode/Cursor plugins.
disable-model-invocation: true
---

# React Code Formatting

## Overview

Unifies CLI lint and save-time formatting for **React (non-Next)** projects using:
- ESLint flat config (`eslint-plugin-react` + TypeScript; `eslint-config-prettier` disables conflicting rules)
- Prettier (formatting via Prettier CLI / editor, not `eslint-plugin-prettier`)
- Husky + lint-staged + commitlint
- VSCode/Cursor extension and settings checks

For **Next.js** projects, use the **next-formatting** skill instead.

## When to Use

Use when `package.json` has `react` but **no** `next`, and:
- `npm run lint` conflicts with editor save formatting
- the project needs standardized formatting and commit hooks
- commit messages are rejected or inconsistent
- VSCode/Cursor plugins may be missing or misconfigured

## Workflow

1. Install and configure ESLint + Prettier (React-specific)
2. Configure Husky + lint-staged + commitlint
3. Verify VSCode/Cursor plugins and settings
4. Run verification commands and report gaps

## 1) ESLint + Prettier

### Install

```bash
npm install -D prettier eslint-config-prettier \
  @eslint/js typescript-eslint eslint-plugin-react eslint-plugin-react-hooks \
  eslint-plugin-jsx-a11y globals
```

If Tailwind is used:

```bash
npm install -D prettier-plugin-tailwindcss
```

### Prettier

Create `prettier.config.mjs`:

```js
const config = {
  singleQuote: false,
  tabWidth: 2,
  trailingComma: "all",
  printWidth: 100,
  semi: true,
  arrowParens: "avoid",
  bracketSameLine: true,
  plugins: ["prettier-plugin-tailwindcss"],
};

export default config;
```

Create `.prettierignore` for generated artifacts (`node_modules`, `dist`, `build`, etc.).

### ESLint (flat config)

Create `eslint.config.mjs`:

```js
import js from "@eslint/js";
import globals from "globals";
import react from "eslint-plugin-react";
import reactHooks from "eslint-plugin-react-hooks";
import jsxA11y from "eslint-plugin-jsx-a11y";
import tseslint from "typescript-eslint";
import eslintConfigPrettier from "eslint-config-prettier/flat";

export default [
  js.configs.recommended,
  ...tseslint.configs.recommended,
  {
    files: ["**/*.{js,jsx,ts,tsx}"],
    languageOptions: {
      globals: { ...globals.browser, ...globals.node },
      parserOptions: { ecmaFeatures: { jsx: true } },
    },
    plugins: { react, "react-hooks": reactHooks, "jsx-a11y": jsxA11y },
    settings: { react: { version: "detect" } },
    rules: {
      ...react.configs.recommended.rules,
      ...reactHooks.configs.recommended.rules,
      ...jsxA11y.configs.recommended.rules,
    },
  },
  eslintConfigPrettier,
];
```

Package roles:
- **`@eslint/js`** — base JavaScript recommended rules
- **`typescript-eslint`** — TypeScript parser and rules
- **`eslint-plugin-react`** / **`react-hooks`** / **`jsx-a11y`** — React, Hooks, and accessibility (similar coverage to what `eslint-config-next` bundles for Next)
- **`globals`** — browser/node global names for `no-undef`
- **`eslint-config-prettier`** — turns off ESLint rules that conflict with Prettier; formatting is handled by Prettier separately (not via `eslint-plugin-prettier`)

## 2) Scripts + Husky + lint-staged + commitlint

### Scripts

```json
{
  "scripts": {
    "lint": "eslint .",
    "lint-fix": "eslint . --fix",
    "format-fix": "prettier --write .",
    "commitlint": "commitlint --edit",
    "prepare": "husky"
  }
}
```

### Husky + lint-staged

```bash
npm install -D husky lint-staged
npx husky init
```

`.husky/pre-commit`:

```sh
#!/usr/bin/env sh
npx lint-staged
```

`lint-staged.config.mjs`:

```js
const lintStagedConfig = {
  "*.{js,jsx,ts,tsx}": ["npm run lint-fix"],
  "*.{json,css,md}": ["npm run format-fix"],
};

export default lintStagedConfig;
```

### Commitlint

```bash
npm install -D @commitlint/cli @commitlint/config-conventional
```

`commitlint.config.mjs`:

```js
/** @type {import("@commitlint/types").UserConfig} */
const config = {
  extends: ["@commitlint/config-conventional"],
};

export default config;
```

`.husky/commit-msg`:

```sh
#!/usr/bin/env sh
npx --no -- commitlint --edit "$1"
```

Hooks: **`pre-commit`** → lint-staged; **`commit-msg`** → commitlint.

Conventional Commits format: `<type>(<scope>): <description>` — types include `feat`, `fix`, `docs`, `chore`, etc.

## 3) VSCode/Cursor Checks (Required)

### Required extensions

- `esbenp.prettier-vscode`
- `dbaeumer.vscode-eslint`
- `rvest.vs-code-prettier-eslint`

Check with `cursor --list-extensions` or `code --list-extensions`. If missing, tell the user which IDs to install.

### Workspace settings

Verify `.vscode/settings.json` has:
- `editor.formatOnSave: true`
- language-specific formatters (Prettier ESLint for JS/TS/React when using that workflow)
- `eslint.validate` includes JS/TS/React variants
- `"*.css": "tailwindcss"` in `files.associations` when using Tailwind

Propose exact JSON edits if missing or conflicting.

## 4) Verification Before Completion

```bash
npm run lint
npm run lint-fix
npm run format-fix
```

Test hooks (run `npm run prepare` first if hooks do not fire):

```bash
echo "bad commit message" | npx commitlint          # fail
echo "chore: verify commitlint" | npx commitlint    # pass
git commit --allow-empty -m "invalid message"       # commit-msg hook fail
git commit --allow-empty -m "chore: verify hook"    # pass
```

Stage a file and commit to confirm **pre-commit** runs lint-staged.

Report what was configured, what was verified, and any unresolved gaps.
