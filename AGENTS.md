# AGENTS.md

## About this project

`mr-pr-template-workflow` is an AI agent skill for preparing GitLab merge requests
and GitHub pull requests with project-native templates, language/style consistency,
and safe CLI handling.

## Repository layout

```
skills/mr-pr-template-workflow/
├── SKILL.md              — skill instructions (EN canonical)
├── agents/openai.yaml    — OpenAI-compatible agent metadata
└── references/
    └── template-sources.md  — fallback template reference
docs/
├── en/SKILL.md           — English copy
└── i18n/zh-CN/SKILL.md   — Chinese translation
```

## Languages

- **English** — canonical source (`SKILL.md`, `docs/en/SKILL.md`)
- **中文** — translation (`docs/i18n/zh-CN/SKILL.md`, `README.zh-CN.md`)

## Contributing

1. Edit `SKILL.md` (English canonical) first
2. Sync changes to `docs/en/SKILL.md`
3. Update `docs/i18n/zh-CN/SKILL.md` Chinese translation
4. Keep language navigation links updated

## Commit conventions

```
feat: <summary>
fix: <summary>
docs: <summary>
chore: <summary>
```
