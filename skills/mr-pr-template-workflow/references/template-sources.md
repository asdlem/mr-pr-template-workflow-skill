# Template Sources

Load this reference only when the repository has no native MR/PR template, when the user asks for template rationale, or when a generic fallback structure is needed.

## Priority

1. Repository template files are authoritative.
2. Recent same-repository MR/PR descriptions define local style and language.
3. Current branch commits define change intent and wording.
4. Open-source template patterns are fallback structure only.

Do not translate labels from an existing repository template. If synthesizing a new fallback template, choose section labels in the selected output language unless the repository history strongly prefers English labels.

## Official Skill References

- GitLab AI Skills repository: https://gitlab.com/gitlab-org/ai/skills
- GitLab `glab` skill: https://gitlab.com/gitlab-org/ai/skills/-/blob/main/skills/glab/SKILL.md
- GitLab Agent Skills docs: https://docs.gitlab.com/user/duo_agent_platform/customize/agent_skills/
- GitHub Agent Skills docs: https://docs.github.com/en/copilot/concepts/agents/about-agent-skills
- GitHub `awesome-copilot`: https://github.com/github/awesome-copilot

## Open-Source Template Patterns

Use these as patterns, not as text to copy verbatim.

- Kubernetes: PR type/kind, why the change is needed, related issues, reviewer notes, release notes, and docs.
- Node.js: contribution rules, commit formatting, tests/docs responsibility, and DCO.
- GitLab default MR template: what/why, references, screenshots, validation steps, and acceptance checklist.
- Prometheus: fixed issue, release notes, changelog type, DCO, and testing comments.
- Grafana: what/why/who, related issue, reviewer checklist, docs, and release/What's New impact.
- Flutter: description, issue link, pre-launch checklist, tests, docs, and AI contribution responsibility.

## Compact Fallback Shape

When no project template exists, prefer this compact structure unless repository history suggests a better one:

```markdown
## Summary

<One to three sentences describing the user-visible or reviewer-visible change.>

## Changes

- <Key change 1>
- <Key change 2>

## Verification

- <Exact command and result, or say not run with reason>

## Risk and rollout

- <Migrations, env vars, deployment impact, external services, or none known>

## Related

- <Issue/MR/PR links, or N/A>
```

For Chinese output with no project template, use:

```markdown
## 摘要

<一到三句话说明本次变更。>

## 主要改动

- <关键改动 1>
- <关键改动 2>

## 验证

- <实际执行的命令和结果；未执行则说明原因>

## 风险与发布影响

- <迁移、环境变量、部署影响、外部服务依赖；没有则说明未发现>

## 关联

- <issue/MR/PR 链接；没有则写无或 N/A>
```

## Wording Rules

- Do not claim CI passed unless checked.
- Do not claim tests were run unless the exact command was executed.
- Do not invent issue numbers, reviewers, assignees, or acceptance criteria.
- Keep generated content concise; reviewers should understand the change without reading implementation details twice.
- Match the repository's dominant wording for "MR", "merge request", "PR", or "pull request".
