# Agent Skills 仓库

[English](README.md) | 中文

这个仓库用于存放可复用的 agent skills，主要用于 JavaScript/TypeScript 前端项目中的代码格式化与 lint 工作流配置。

当前包含的 skills：

- `skills/react-formatting`：用于非 Next.js 的 React 项目，覆盖 ESLint、Prettier、Husky、lint-staged、commitlint 的配置与校验。
- `skills/next-formatting`：用于 Next.js 项目，覆盖 ESLint、Prettier、Husky、lint-staged、commitlint 的配置与校验。

## 安装

使用 [Skills CLI](https://skills.sh/)（`npx skills`）安装：

```bash
# 列出本仓库中的 skills
npx skills add cp3hnu/agent-skills --list

# 安装全部 skills（项目级）
npx skills add cp3hnu/agent-skills --all

# 安装指定 skill
npx skills add cp3hnu/agent-skills --skill react-formatting
npx skills add cp3hnu/agent-skills --skill next-formatting
```

## 使用方式

1. 在 Agent 中打开目标项目，并安装 skills（见上方安装说明）。
2. Agent 会根据 `SKILL.md` 中的 `description` 自动发现已安装的 skills。当你处理 lint/format 配置时，它会自动匹配对应的 skill：
   - React 项目 → `react-formatting`
   - Next.js 项目 → `next-formatting`
3. 也可以手动触发 skill：
   - 输入 `/`，从斜杠菜单中选择 skill
   - 或在对话中直接描述需求，例如「帮这个 React 项目配置 ESLint 和 Prettier」
4. 按已安装的 `SKILL.md` 完成依赖安装、脚本配置、git hooks 和编辑器检查。


