---
name: mr-pr-template-workflow
description: Prepare and optionally create GitLab merge requests or GitHub pull requests with project-native templates, repository language/style consistency, preview-before-submit, and safe gh/glab CLI handling. Use when the user asks to prepare, write, preview, create, submit, or open an MR/PR, merge request, pull request, or review-ready branch.
---

[English](SKILL.md) | [中文](../i18n/zh-CN/SKILL.md)

# MR/PR Template Workflow

Use this skill to prepare a merge request or pull request end to end without losing project conventions or accidentally pushing to the wrong branch.

## Core Rules

- Prefer project-native templates over every generic template.
- Preserve template section labels and placeholders exactly when using a project template; fill the content in the selected language.
- Always preview the title, body, source branch, target branch, and intended action before creating an MR/PR.
- Create the MR/PR only after explicit user confirmation.
- When `gh` or `glab` is available, optimize the preview for reading: render the MR/PR body as normal Markdown, not inside a fenced code block, and do not show shell commands unless the user asks.
- If `gh` or `glab` is unavailable, output manual form fields and stop. Do not ask for submit confirmation.
- When no suitable CLI is available, split manual form content into separate fenced code blocks so the user can copy fields directly.
- Never push a local branch to `main`, `master`, `dev`, `develop`, or a release branch by using `source:target` refspecs unless the user explicitly requests that exact push.
- Never invent issue IDs, test results, approvals, release notes, or screenshots.

## Workflow

1. Inspect repository state.
   - Run `git status --short --branch`, `git remote -v`, `git branch --show-current`, and enough `git log`/`git diff` commands to understand what will be submitted.
   - Identify source branch from the current branch unless the user specifies another branch.
   - Identify target branch from the user's request, upstream config, existing MR/PR, project default branch, or local release workflow. Ask if target branch selection is genuinely ambiguous.
   - Check whether the source branch exists on the intended remote before deciding the create command.

2. Find project templates first.
   - GitLab: check `.gitlab/merge_request_templates/*.md`, especially `Default.md`; if multiple templates match equally, ask the user to choose.
   - GitHub: check `.github/PULL_REQUEST_TEMPLATE.md`, `.github/pull_request_template.md`, `.github/PULL_REQUEST_TEMPLATE/*.md`, `PULL_REQUEST_TEMPLATE.md`, and `docs/PULL_REQUEST_TEMPLATE.md`.
   - Treat `CONTRIBUTING.md`, `.github/CONTRIBUTING.md`, docs, and repo instructions as supplementary constraints, not as replacements for MR/PR templates.
   - If no project template exists, load `references/template-sources.md` and synthesize a compact template from the fallback pattern.

3. Determine output language and style.
   - Highest priority: explicit user language instruction.
   - Then inspect recent same-repo MR/PR titles and descriptions using `glab mr list/view` or `gh pr list/view` when available.
   - Then inspect current branch commit subjects with `git log --format=%s <base>..HEAD`.
   - Fallback to the current conversation language.
   - Keep Conventional Commit prefixes in English when the repo uses them, but write the human-readable subject/body in the selected language.

4. Draft title and body.
   - Title: summarize the actual change set, matching repo commit/MR naming style.
   - Body: fill every relevant project-template section. Mark non-applicable sections as `N/A`, `None`, or the selected-language equivalent only when the existing template style supports it.
   - Verification: list exact commands run and their result. If not run, say so plainly with the reason.
   - Risk/rollout: mention migrations, env vars, config changes, external services, deploy impact, or say none known.
   - Related links: include real issue/MR/PR URLs only when found or provided.

5. Preview before submission.
   If `gh` or `glab` is available, show a reading-oriented preview:
   - provider: GitLab or GitHub
   - source branch
   - target branch
   - title
   - body rendered as normal Markdown
   - whether the action would create a new MR/PR or update an existing one
   - the CLI that will be used

   Do not include command blocks by default in the CLI-available path. The user cares about the MR/PR content and the action summary, not the exact shell command. Show exact commands only when the user asks, when diagnosing a CLI problem, or when no CLI is available.

   If no suitable CLI is available, show a copy-oriented manual preview with separate fenced code blocks for provider/branches, title, and description.

6. Submit only after confirmation.
   - GitLab: prefer `glab mr create`. Follow the official GitLab `glab` skill if available, especially its guidance to use `--push`, `-H`, project templates, and file-backed descriptions.
   - GitHub: prefer `gh pr create --base <target> --head <source> --title <title> --body-file <file>`.
   - Write long descriptions to a temporary file and pass the file content or `--body-file`; do not inline bodies containing backticks, `$`, shell syntax, or markdown code blocks.
   - If branch push is needed, push the branch by name: `git push -u <remote> <source>`. Do not use `<source>:<target>`.

## CLI Notes

Use `glab` for GitLab and `gh` for GitHub when available. If both are available and the remote provider is still unclear, decide from `git remote -v`; ask only if the same worktree has competing remotes and the intended host cannot be inferred.

For GitLab command details, prefer the official GitLab AI Skills `glab` skill when installed locally. If it is not installed, use its public reference as guidance:

- https://gitlab.com/gitlab-org/ai/skills/-/blob/main/skills/glab/SKILL.md

## Manual Fallback

When no suitable CLI is available, provide a manual form split into copyable blocks:

```text
Provider:
Source branch:
Target branch:
```

```text
Title:
```

```markdown
Description:
```

Then stop. Do not ask the user to confirm a submission that cannot be performed with available tools.

## Reference

Load `references/template-sources.md` only when no project-native template exists, when choosing a fallback structure, or when the user asks why the generated template has those sections.
