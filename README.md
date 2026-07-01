# Agent Skills Repository

English | [中文](README.zh-CN.md)

This repository contains reusable agent skills for formatting and linting workflows in JavaScript/TypeScript frontend projects.

Current skills:

- `skills/react-formatting`: Formatting and lint setup for React projects (non-Next.js), including ESLint, Prettier, Husky, lint-staged, and commitlint.
- `skills/next-formatting`: Formatting and lint setup for Next.js projects, including ESLint, Prettier, Husky, lint-staged, and commitlint.

## Install

Install skills with the [Skills CLI](https://skills.sh/) (`npx skills`):

```bash
# List available skills in this repo
npx skills add cp3hnu/agent-skills --list

# Install all skills (project scope)
npx skills add cp3hnu/agent-skills --all

# Install a specific skill
npx skills add cp3hnu/agent-skills --skill react-formatting
npx skills add cp3hnu/agent-skills --skill next-formatting
```

## Usage

1. Open your target project in agent and install the skills (see above).
2. The agent auto-discovers installed skills from their `description` in `SKILL.md`. When you work on lint/format setup, it can pick the right skill on its own:
   - React project → `react-formatting`
   - Next.js project → `next-formatting`
3. You can also invoke a skill explicitly:
   - Type `/` and select the skill from the slash menu
   - Or ask in chat, e.g. "set up ESLint and Prettier for this React project"
4. Follow the steps in the installed `SKILL.md` to configure dependencies, scripts, git hooks, and editor settings.

