---
name: mr-pr-template-workflow
description: Prepare and optionally create GitLab merge requests or GitHub pull requests with project-native templates, repository language/style consistency, preview-before-submit, and safe gh/glab CLI handling. Use when the user asks to prepare, write, preview, create, submit, or open an MR/PR, merge request, pull request, or review-ready branch.
---

# PR/MR Workflow

Use this skill to prepare a merge request or pull request end to end without
losing project conventions or accidentally pushing to the wrong branch.

Based on analysis of PR/MR templates and conventions from 16 top projects
(react, vscode, electron, turborepo, hermes-agent, biome, vllm, dify, and more).

## Template Patterns from Top Projects

Every project template falls into one of these tiers. Match the project, not
your habit.

### Tier A: Minimalist (react, vscode)
`## Summary` + `## How did you test this change?`

Two sections, no checkboxes. Forces concise prose over checklist theater.
React says: *"If you leave this empty, your PR will very likely be closed."*
VS Code doesn't even provide sections — contributors organically produce
5–10 labeled sections because the culture demands it.

### Tier B: Three-Section (biome, turborepo)
`## Summary` + `## Test Plan` + `## Docs`

Documentation is a first-class deliverable. Biome treats docs updates as a
required section alongside code and tests. Turborepo contributors write
mini-design-documents with `### Why`, `### What I changed`, and `### Notes`.

### Tier C: Checklist-Driven (ohmyzsh, electron, open-webui)
Checklist-heavy templates with 7–12 items. Electron enforces:
*"PRs submitted that do not follow this template will be automatically closed."*

Electron adds a mandatory Release Notes section (`Notes: Fixed ...`).
Open WebUI requires a full Keep a Changelog breakdown.

### Tier D: Internal-Structured (vllm, openai/codex)
`## Purpose` / `## Test Plan` / `## Test Result`

Every PR pastes actual test output. vLLM: universally followed, even draft
PRs include Purpose → Test Plan → Test Result. Codex internal PRs add
`## Why` / `## What` / `## Impact` / `## Validation`.

### Tier E: Gatekeeper (open-webui, CopilotKit, opencode)
Issue-first, discussion-first policies. Open WebUI requires:
- 12 checklist items
- 17 allowed title prefixes
- Mandatory CLA section
- Discussion-first for first-timers

CopilotKit: *"Please PLEASE reach out to us first before starting significant work."*
Opencode: *"AI-generated descriptions may be IGNORED or CLOSED."*

## AI Disclosure

5 of 16 top projects (31%) now require AI-assistance disclosure in PRs.

| Project | Disclosure format |
|---------|-------------------|
| ohmyzsh | Explicit checkbox: "If I used AI tools... I've disclosed it below" |
| biome | HTML comment at template top; "This PR was implemented with AI assistance from Codex" |
| dify | `From <Tool Name>` line in Summary |
| vllm | "AI assistance was used; every line was reviewed per AGENTS.md" |
| electron | Policy link in checklist: "Using a coding agent / AI? Read the policy" |

**Rule**: Add AI-assistance disclosure only when the project template,
`CONTRIBUTING`, or repository policy requires it. Use the project's preferred
format exactly. Do not add a generic disclosure line by default, and never
invent the tool name or reviewer statement.

## Core Rules

- Prefer project-native templates over any generic template.
- Preserve template section labels and placeholders exactly; fill content in the selected language.
- Always preview title, body, source branch, target branch, and action before creating.
- Create the MR/PR only after explicit user confirmation.
- When `gh` or `glab` is available, render the body as normal Markdown in preview — not in a fenced code block.
- If `gh` or `glab` is unavailable, output manual form fields and stop.
- Never push to `main`, `master`, `dev`, `develop`, or a release branch with `source:target` refspecs unless explicitly requested.
- Never invent issue IDs, test results, approvals, release notes, or screenshots.

## Writing Conventions

### Title
Match the repo's convention. 13 of 16 projects use Conventional Commits:
```
feat(scope): summary
fix(scope): summary
docs: summary
refactor(scope): summary
```

Exceptions: vLLM uses `[Area]` prefix, Codex internal uses `[Module]`.

### Body Sections
Every well-written PR body across all 16 projects has these three:
1. **What** (Summary / Purpose) — 1–3 sentences. What changed, why.
2. **How I tested** (Test Plan / Verification) — exact commands + results.
   VS Code: `npm run typecheck-client && npm run eslint`
   vLLM: `pytest tests/config/test_config.py -v` with pass counts
3. **Related** (Fixes #NNNN / Closes #NNNN) — real issue link.

Optional but common:
- **Screenshots** (UI changes) — Before/After table
- **Risk / Rollout** — migrations, env vars, config changes, external services
- **Docs** — linked doc PR or "No docs needed"

### Test Plan Quality — What Top Projects Actually Do
```markdown
## How did you test this change?

yarn test ReactDOMFloat-test --runInBand
  ✓ 84 passed, 0 failed

yarn lint
  ✓ No errors

yarn flow
  ✓ No errors
```

Not this:
```markdown
## Testing
- [x] Tested manually
```

## Workflow

1. **Inspect repository state.**
   - `git status --short --branch`, `git remote -v`, `git branch --show-current`
   - Enough `git log`/`git diff` to understand what's being submitted
   - Identify source branch; identify target branch from user request, upstream config, or project default
   - Check whether source branch exists on remote before deciding the create command

2. **Find project templates first.**
   - GitLab: `.gitlab/merge_request_templates/*.md`, especially `Default.md`
   - GitHub: `.github/PULL_REQUEST_TEMPLATE.md`, `.github/pull_request_template.md`, `.github/PULL_REQUEST_TEMPLATE/*.md`
   - Treat `CONTRIBUTING.md` as supplementary constraints, not template replacement
   - If no project template: load `references/template-sources.md` and synthesize from the Tier A pattern (Summary + Test Plan + Related)

3. **Determine output language and style.**
   - Highest: explicit user language instruction
   - Then: recent same-repo MR/PR titles and descriptions (`gh pr list --limit 5 --json title,body`)
   - Then: current branch commit subjects (`git log --format=%s`)
   - Fallback: current conversation language
   - Keep Conventional Commit prefixes in English; write human-readable text in selected language

4. **Draft title and body.**
   - Title: match repo convention (Conventional Commits, `[Area]`, or plain)
   - Body: fill every relevant project-template section; mark N/A sections per template style
   - Verification: list exact commands run and their results; say why if not run
   - Risk: mention migrations, env vars, config changes, deploy impact, or "none known"
   - Related links: only real URLs actually found
   - AI disclosure: include it only when required by the project template or repository policy.

5. **Add AI disclosure if applicable.**
   - Match the project's preferred format from the table above.
   - If the project has no disclosure requirement, do not add one by default.

6. **Preview before submission.**
   Show in reading-oriented format:
   - provider + source/target branch
   - title
   - body as normal Markdown
   - new vs. update
   - CLI that will be used
   - Do NOT include command blocks by default — user cares about content, not shell syntax

7. **Submit only after confirmation.**
   - GitLab: `glab mr create` with `--push`, `-H`, project templates, file-backed descriptions
   - GitHub: `gh pr create --base <target> --head <source> --title <title> --body-file <file>`
   - For GitLab `glab mr create`, do **not** assume GitHub-style file flags exist. Current verified `glab` supports `--description <text>`, but not `--description-file`; write long descriptions to a temporary file only as a staging aid, then pass the file content via `--description "$(cat "$file")"` or an equivalent safely quoted variable.
   - For GitHub `gh pr create`, pass long descriptions with `--body-file <file>`.
   - Do not inline bodies containing backticks, `$`, shell syntax, or Markdown code blocks unless they are already held in a safely quoted variable.
   - Push branch: `git push -u <remote> <source>` (never `source:target`)

## CLI Notes

Use `glab` for GitLab, `gh` for GitHub. If both available, decide from `git remote -v`.

Before using non-routine CLI flags, inspect the local command's `--help`. Do not assume GitHub `gh` flags exist in GitLab `glab`, and do not assume a newer online CLI reference matches the installed binary.

For GitLab command details, prefer the official GitLab AI Skills `glab` skill when installed locally. If it is not installed, use its public reference as guidance:
- https://gitlab.com/gitlab-org/ai/skills/-/blob/main/skills/glab/SKILL.md

## Manual Fallback

When no CLI available, provide copyable blocks:

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

Then stop. Don't ask for submission confirmation when it can't be performed.

## Reference

Load `references/template-sources.md` only when no project-native template
exists or when the user asks why the generated template has those sections.
