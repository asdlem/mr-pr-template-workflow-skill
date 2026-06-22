[English](README.md) | [中文](README.zh-CN.md)

# MR/PR Template Workflow Skill

Agent Skill for preparing GitLab merge requests and GitHub pull requests with:

- project-native MR/PR templates first
- repository language and style consistency
- readable preview before submission
- safe `gh` and `glab` handling
- copy-friendly manual fallback when no CLI is available

## Install

Copy the skill directory into your user-level skills folder:

```bash
mkdir -p ~/.agents/skills
cp -a skills/mr-pr-template-workflow ~/.agents/skills/
```

## Contents

```text
skills/mr-pr-template-workflow/
├── SKILL.md
├── agents/openai.yaml
└── references/template-sources.md
```
