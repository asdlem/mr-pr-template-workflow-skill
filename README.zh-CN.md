[English](README.md) | [中文](README.zh-CN.md)

# MR/PR 模板工作流 Skill

用于准备 GitLab Merge Request 和 GitHub Pull Request 的 Agent Skill，具备：

- 优先使用项目原生 MR/PR 模板
- 仓库语言和风格一致性
- 提交前可读预览
- 安全的 `gh` 和 `glab` 处理
- 无可 CLI 时的可复制手动回退

## 安装

将 skill 目录复制到用户级 skills 文件夹：

```bash
mkdir -p ~/.agents/skills
cp -a skills/mr-pr-template-workflow ~/.agents/skills/
```

## 内容

```text
skills/mr-pr-template-workflow/
├── SKILL.md
├── agents/openai.yaml
└── references/template-sources.md
```
