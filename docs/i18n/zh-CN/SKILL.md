---
name: mr-pr-template-workflow
description: 使用项目原生模板、仓库语言/风格一致性、提交前预览和安全的 gh/glab CLI 操作，准备并可选择性创建 GitLab Merge Request 或 GitHub Pull Request。当用户要求准备、编写、预览、创建、提交或打开 MR/PR、合并请求、拉取请求或就绪分支时使用。
---

[English](../../../skills/mr-pr-template-workflow/SKILL.md) | [中文](SKILL.md)

# MR/PR 模板工作流

使用此 Skill 端到端准备合并请求或拉取请求，不丢失项目约定，也不意外推送到错误分支。

## 核心规则

- 优先使用项目原生模板，而非通用模板。
- 使用项目模板时，原样保留模板章节标签和占位符；用选定语言填充内容。
- 创建 MR/PR 前始终预览标题、正文、源分支、目标分支和预期操作。
- 仅在用户明确确认后创建 MR/PR。
- 当 `gh` 或 `glab` 可用时，优化预览为阅读模式：将 MR/PR 正文渲染为普通 Markdown，不放在代码块内，除非用户要求否则不显示 shell 命令。
- 若 `gh` 或 `glab` 不可用，输出手动表单字段并停止。不询问提交确认。
- 无可 CLI 时，将手动表单内容拆分到独立的代码块中，方便用户逐字段复制。
- 除非用户明确请求，否则绝不将本地分支推送到 `main`、`master`、`dev`、`develop` 或发布分支（通过 `source:target` refspec）。
- 绝不编造 issue ID、测试结果、审批、发布说明或截图。

## 工作流

1. 检查仓库状态。
   - 运行 `git status --short --branch`、`git remote -v`、`git branch --show-current` 以及足够的 `git log`/`git diff` 命令以理解待提交内容。
   - 从当前分支识别源分支，除非用户指定其他分支。
   - 从用户请求、上游配置、现有 MR/PR、项目默认分支或本地发布工作流中识别目标分支。仅在选择确实模糊时询问。
   - 在决定创建命令之前，检查源分支是否存在于目标远程仓库。

2. 首先查找项目模板。
   - GitLab：检查 `.gitlab/merge_request_templates/*.md`，特别是 `Default.md`；如果有多个模板同等匹配，请用户选择。
   - GitHub：检查 `.github/PULL_REQUEST_TEMPLATE.md`、`.github/pull_request_template.md`、`.github/PULL_REQUEST_TEMPLATE/*.md`、`PULL_REQUEST_TEMPLATE.md` 和 `docs/PULL_REQUEST_TEMPLATE.md`。
   - 将 `CONTRIBUTING.md`、`.github/CONTRIBUTING.md`、文档和仓库说明视为补充约束，而非 MR/PR 模板的替代品。
   - 若没有项目模板，则加载 `references/template-sources.md` 并从回退模式合成一个简洁模板。

3. 确定输出语言和风格。
   - 最高优先级：用户明确的语言指令。
   - 然后通过 `glab mr list/view` 或 `gh pr list/view`（如果可用）检查最近同仓库的 MR/PR 标题和描述。
   - 然后检查当前分支提交摘要：`git log --format=%s <base>..HEAD`。
   - 回退到当前对话语言。
   - 若仓库使用 Conventional Commit 前缀，保留英文前缀，但用选定语言编写人类可读的主题/正文。

4. 草拟标题和正文。
   - 标题：总结实际变更集，匹配仓库的提交/MR 命名风格。
   - 正文：填写每个相关的项目模板章节。仅当现有模板风格支持时，将不适用的章节标记为 `N/A`、`None` 或选定语言的等效内容。
   - 验证：列出确切运行的命令及结果。若未运行，明确说明及原因。
   - 风险/部署：提及迁移、环境变量、配置变更、外部服务、部署影响，或注明无已知风险。
   - 相关链接：仅当找到或提供时，包含真实的 issue/MR/PR URL。

5. 提交前预览。
   若 `gh` 或 `glab` 可用，展示阅读导向的预览：
   - 提供方：GitLab 或 GitHub
   - 源分支
   - 目标分支
   - 标题
   - 正文以普通 Markdown 渲染
   - 操作是创建新 MR/PR 还是更新已有 MR/PR
   - 将使用的 CLI

   CLI 可用时，默认不包含命令块。用户关心的是 MR/PR 内容和操作摘要，而非确切的 shell 命令。仅在用户要求、诊断 CLI 问题或无可 CLI 时显示确切命令。

   若无可 CLI，展示复制导向的手动预览，以独立代码块分开展示提供方/分支、标题和描述。

6. 仅在确认后提交。
   - GitLab：优先使用 `glab mr create`。若有官方 GitLab `glab` Skill，遵循其指导——特别是使用 `--push`、`-H`、项目模板和基于文件的描述。
   - GitHub：优先使用 `gh pr create --base <target> --head <source> --title <title> --body-file <file>`。
   - 将长描述写入临时文件并传递文件内容或 `--body-file`；不要内联包含反引号、`$`、shell 语法或 Markdown 代码块的正文。
   - 若需推送分支，按名称推送：`git push -u <remote> <source>`。不使用 `<source>:<target>`。

## CLI 注意事项

当可用时，GitLab 使用 `glab`，GitHub 使用 `gh`。若两者均可用但远程提供方仍不明确，从 `git remote -v` 判断；仅当同一工作树有竞争远程且无法推断目标主机时才询问。

关于 GitLab 命令细节，若本地安装了官方 GitLab AI Skills `glab` Skill，优先使用。若未安装，使用其公共参考作为指导：

- https://gitlab.com/gitlab-org/ai/skills/-/blob/main/skills/glab/SKILL.md

## 手动回退

当无可 CLI 时，提供拆分为可复制块的手动表单：

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

然后停止。不要求用户确认一个在当前可用工具下无法执行的提交。

## 参考资料

仅在无项目原生模板、选择回退结构或用户询问生成的模板为何包含这些章节时，加载 `references/template-sources.md`。
