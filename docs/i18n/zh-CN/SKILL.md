---
name: pr-workflow
description: 使用项目原生模板、仓库语言/风格一致性、提交前预览和安全的 gh/glab CLI 操作，准备并可选择性创建 GitLab Merge Request 或 GitHub Pull Request。当用户要求准备、编写、预览、创建、提交或打开 MR/PR、合并请求、拉取请求或就绪分支时使用。
---

# PR/MR 工作流

使用此 Skill 端到端准备合并请求或拉取请求，不丢失项目约定，也不意外推送到错误分支。

基于对 16 个顶级项目（react、vscode、electron、turborepo、hermes-agent、biome、vllm、dify 等）的 PR/MR 模板和写作惯例的分析。

## 顶级项目的模板模式

每个项目的模板都属于以下某一层级。匹配项目，而非习惯。

### A 级：极简型（react、vscode）
`## Summary` + `## How did you test this change?`

两个章节，无复选框。用简洁的叙述取代检查清单形式主义。
React 明确规定：*"如果此处留空，你的 PR 极有可能被关闭。"*
VS Code 甚至没有提供章节——贡献者自发产出 5–10 个标注章节，因为项目文化要求如此。

### B 级：三章节型（biome、turborepo）
`## Summary` + `## Test Plan` + `## Docs`

文档被视为一等交付物。Biome 将文档更新与代码和测试并列作为必填章节。
Turborepo 贡献者编写迷你设计文档，包含 `### Why`、`### What I changed` 和 `### Notes`。

### C 级：检查清单驱动型（ohmyzsh、electron、open-webui）
以检查清单为主的模板，包含 7–12 项。Electron 强制执行：
*"未遵循此模板提交的 PR 将被自动关闭。"*

Electron 增加了必填的发布说明章节（`Notes: Fixed ...`）。
Open WebUI 要求完整的 Keep a Changelog 格式。

### D 级：内部结构化型（vllm、openai/codex）
`## Purpose` / `## Test Plan` / `## Test Result`

每个 PR 都粘贴实际测试输出。vLLM：普遍遵循，即使是草稿 PR 也包含 Purpose → Test Plan → Test Result。
Codex 内部 PR 增加 `## Why` / `## What` / `## Impact` / `## Validation`。

### E 级：守门型（open-webui、CopilotKit、opencode）
Issue 优先、讨论优先。Open WebUI 要求：
- 12 项检查清单
- 17 种允许的标题前缀
- 强制性 CLA 章节
- 首次贡献者必须先发起讨论

CopilotKit：*"在开始重大工作前，请务必先联系我们。"*
Opencode：*"AI 生成的描述可能被忽略或关闭。"*

## AI 辅助披露

16 个顶级项目中，5 个（31%）要求在 PR 中披露 AI 辅助。

| 项目 | 披露格式 |
|------|----------|
| ohmyzsh | 明确复选框："如果我使用了 AI 工具……我已在下方披露" |
| biome | 模板顶部 HTML 注释；"此 PR 使用 Codex AI 辅助实现" |
| dify | Summary 中的 `From <工具名称>` 行 |
| vllm | "使用了 AI 辅助；每行代码均按 AGENTS.md 进行了人工审查" |
| electron | 清单中的政策链接："使用编码 Agent / AI？请阅读政策" |

**规则**：如果 AI 工具（Hermes、Claude Code、Codex、Copilot 等）参与了 PR 编写，请披露。
使用项目首选的格式。若项目无专门格式，添加：
`> 🤖 AI 辅助：<工具名称>。每行代码已经过人工审查。`

## 核心规则

- 优先使用项目原生模板，而非通用模板。
- 原样保留模板章节标签和占位符；用选定语言填充内容。
- 创建前始终预览标题、正文、源分支、目标分支和操作。
- 仅在用户明确确认后创建 MR/PR。
- 当 `gh` 或 `glab` 可用时，预览中将正文渲染为普通 Markdown——不要放在代码块内。
- 若 `gh` 或 `glab` 不可用，输出手动表单并停止。
- 绝不将本地分支推送到 `main`、`master`、`dev`、`develop` 或发布分支（使用 `source:target` refspec），除非用户明确要求。
- 绝不编造 issue ID、测试结果、审批、发布说明或截图。

## 写作惯例

### 标题
匹配仓库惯例。16 个项目中 13 个使用 Conventional Commits：
```
feat(scope): 摘要
fix(scope): 摘要
docs: 摘要
refactor(scope): 摘要
```

例外：vLLM 使用 `[Area]` 前缀，Codex 内部使用 `[Module]`。

### 正文章节
在所有 16 个项目中，每个写得出色的 PR 正文都包含以下三个要素：
1. **What**（Summary / Purpose）— 1–3 句话。改了什么，为什么。
2. **How I tested**（Test Plan / Verification）— 确切的命令 + 结果。
   VS Code：`npm run typecheck-client && npm run eslint`
   vLLM：`pytest tests/config/test_config.py -v` 附带通过数量
3. **Related**（Fixes #NNNN / Closes #NNNN）— 真实的 issue 链接。

可选但常见：
- **Screenshots**（UI 变更）— Before/After 对比表
- **Risk / Rollout** — 迁移、环境变量、配置变更、外部服务
- **Docs** — 链接的文档 PR 或 "无需文档更新"

### 测试计划质量 — 顶级项目的实际做法
```markdown
## How did you test this change?

yarn test ReactDOMFloat-test --runInBand
  ✓ 84 passed, 0 failed

yarn lint
  ✓ No errors

yarn flow
  ✓ No errors
```

而非这样：
```markdown
## Testing
- [x] 手动测试
```

## 工作流

1. **检查仓库状态。**
   - `git status --short --branch`、`git remote -v`、`git branch --show-current`
   - 足够的 `git log`/`git diff` 以理解待提交内容
   - 识别源分支；识别目标分支（用户请求、上游配置或项目默认分支）
   - 检查源分支是否存在于远程仓库

2. **首先查找项目模板。**
   - GitLab：`.gitlab/merge_request_templates/*.md`，特别是 `Default.md`
   - GitHub：`.github/PULL_REQUEST_TEMPLATE.md`、`.github/pull_request_template.md`、`.github/PULL_REQUEST_TEMPLATE/*.md`
   - 将 `CONTRIBUTING.md` 视为补充约束，而非模板替代品
   - 若无项目模板：加载 `references/template-sources.md` 并从 A 级模式合成（Summary + Test Plan + Related）

3. **确定输出语言和风格。**
   - 最高优先级：用户明确的语言指令
   - 其次：最近同仓库 MR/PR 标题和描述（`gh pr list --limit 5 --json title,body`）
   - 再次：当前分支提交摘要（`git log --format=%s`）
   - 回退：当前对话语言
   - Conventional Commit 前缀保留英文；用选定语言编写可读文本

4. **草拟标题和正文。**
   - 标题：匹配仓库惯例（Conventional Commits、`[Area]` 或纯文本）
   - 正文：填写每个相关项目模板章节；按模板风格标记 N/A 章节
   - 验证：列出确切运行的命令及结果；若未运行，说明原因
   - 风险：提及迁移、环境变量、配置变更、部署影响，或"无已知风险"
   - 相关链接：仅包含实际找到的真实 URL

5. **如适用，添加 AI 辅助披露。**
   - 匹配上表中项目的首选格式
   - 对于未列出的项目：`> 🤖 AI 辅助：Hermes。每行代码已经过人工审查。`

6. **提交前预览。**
   以阅读导向格式展示：
   - 提供方 + 源/目标分支
   - 标题
   - 正文以普通 Markdown 渲染
   - 新建 vs. 更新
   - 将使用的 CLI
   - 默认不包含命令块——用户关心内容，而非 shell 语法

7. **仅在确认后提交。**
   - GitLab：`glab mr create` 使用 `--push`、`-H`、项目模板、基于文件的描述
   - GitHub：`gh pr create --base <target> --head <source> --title <title> --body-file <file>`
   - 长描述 → 临时文件 → `--body-file`；不要内联含反引号/`$`/Markdown 代码块的正文
   - 推送分支：`git push -u <remote> <source>`（绝不使用 `source:target`）

## CLI 注意事项

GitLab 使用 `glab`，GitHub 使用 `gh`。若两者均可用，从 `git remote -v` 判断。
GitLab 详细信息优先参考官方 `glab` Skill：
- https://gitlab.com/gitlab-org/ai/skills/-/blob/main/skills/glab/SKILL.md

## 手动回退

无可 CLI 时，提供可复制的独立块：

```text
提供方：
源分支：
目标分支：
```

```text
标题：
```

```markdown
描述：
```

然后停止。不要求用户确认在当前可用工具下无法执行的提交。

## 参考资料

仅在无项目原生模板或用户询问生成的模板为何包含这些章节时，加载 `references/template-sources.md`。
