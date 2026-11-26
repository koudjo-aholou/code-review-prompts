# code-review-prompts

Three code-review prompts for LLMs, plus ready-made skills and agents for Claude Code.

| Prompt | What it looks at | Status |
|---|---|---|
| `revue-backend` | bugs, regressions, design problems in a backend diff (Node/TypeScript, Python) | measured (see below) |
| `revue-refacto` | naming and readability of one green TDD slice, at most 10 remarks | experimental |
| `revue-transversale` | what no single slice shows: code copied 3+ times, inconsistent API across files, at most 5 remarks | experimental |

The prompts and the reports they ask for are **in French**. Reports follow
[Conventional Comments](https://conventionalcomments.org/).

## Two layers

```
prompts/        the three prompts, plain markdown, no tool or provider assumed
claude-code/
  skills/       revue-code, revue-refacto, revue-transversale  (copy into ~/.claude/skills/)
  agents/       the same three as standalone agents            (copy into ~/.claude/agents/)
```

**`prompts/`** is model-agnostic text: use it as a system prompt, with the diff as the user
message, in any tool. A grep for vendor or tool names (Claude, Anthropic, Read, Grep, Glob,
`CLAUDE.md`) finds nothing in the three files.

**`claude-code/`** is specific to Claude Code (skill format, agent frontmatter).

## Install in Claude Code

Skills — copy a whole folder, nothing else to configure:

```sh
cp -r claude-code/skills/revue-code ~/.claude/skills/
cp -r claude-code/skills/revue-refacto ~/.claude/skills/
cp -r claude-code/skills/revue-transversale ~/.claude/skills/
```

Or per project, in `<project>/.claude/skills/`.

Agents — one file each:

```sh
cp claude-code/agents/*.md ~/.claude/agents/
```

Skill or agent? A skill runs inside your conversation: it uses your session's model, effort and
`CLAUDE.md`, has every tool (including the ones that write), and fills your context. An agent has
its own context, a pinned model (`claude-sonnet-5-5`), effort `high`, and only
`Read, Bash, Grep, Glob`. The agents are closer to the conditions the prompts were measured in.
Note the agents have `omitClaudeMd: true`: they do not load your own `CLAUDE.md`, but they do read
the reviewed repository's `CLAUDE.md` / `AGENTS.md` / `CONTRIBUTING.md`.

## What is measured, and what is not

Everything below is measured on **Claude Sonnet only**. Nothing is claimed for another model.
The one other result on record is unfavourable: `qwen2.5:14b` stayed behind a free-form prompt.
The `revue-backend` prompt weighs about 3,900 tokens; some local engines truncate it silently
below a 4,096-token window.

`revue-backend` (details in [`claude-code/skills/revue-code/README.md`](claude-code/skills/revue-code/README.md)):

| Measure | Result |
|---|---|
| Template rendered (13 cases, `claude-sonnet-4-6`, 2026-09-21) | 13/13 |
| Planted defects found, after re-reading | 7/8 |
| Restraint on rule cases | 5/5 |
| 50 real pull requests with no known defect (SWR-Bench) | commented on 72 % |

72 % is not an error rate: SWR-Bench calls a PR "clean" when it introduces no defect fixed later
in history, not when there is nothing to say. Twelve reports were read one by one and none
contained an invented remark. Detection varies from one run to the next: a report with no remark
on a point does not prove the point is sound. The reviewer no longer ranks severity: any remark
means `request changes`, which here means "a human should read this", not "this is broken".

`revue-refacto` and `revue-transversale` are **experimental**. They were exercised on Python
only, planted defects are more salient than real ones, and no figures are published here.

## Dataset

`claude-code/skills/revue-code/datasets/corpus_v1.json`: 13 short diffs, each with what the review
must find or stay silent about (`rb01`–`rb05` restraint, `rb06`–`rb13` detection). The defects are
planted, so they are more visible than in real code. Use it as examples, or as a first check on a
model you have never tried: it reliably eliminates a model that sees nothing, it does not certify
one that passes (detection is scored by keyword).

## Licence

MIT.
