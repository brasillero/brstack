# brstack

hey, i'm brasillero. i spend my days shipping software with agents doing a lot of the heavy lifting, and these are the skills that keep that work honest.

**agents should do the digging, people should get the short version.** let one agent investigate and another implement, but when something reaches a human, a review comment or an answer, it should read like a person wrote it.

**decisions deserve a paper trail.** "why did we do this?" should still have an answer next month, written next to the code, not lost in a chat.

nothing here is magic. it's the stuff i kept repeating to my agents, written down once.

## Skills

| Skill | Say | What it does |
| --- | --- | --- |
| [`council`](skills/council/SKILL.md) | "Council, review this" · "council" | Fixes known issues first, then asks three strong models from different providers to review in parallel. Scores each round out of 10, repeats until all are satisfied, keeps scope tight, and puts the scores in the PR description. |
| [`delegate`](skills/delegate/SKILL.md) | "Investigate this bug" · "Implement it" | Codex investigates, a Claude subagent implements, and the main session coordinates and reviews. Other models only when you name them. |
| [`review-comment-writing`](skills/review-comment-writing/SKILL.md) | (automatic) · "Post that review" | Writes clear review feedback and preserves the intended format: general comment, resolvable discussion, or thread on the code. Asks when placement is unclear. Only for people; agents get the full version. |
| [`task-decisions`](skills/task-decisions/SKILL.md) | "Start notes for ABC-123" · "Record a decision" · "What's still open?" | Keeps a backlog and a decision log per task in `.task-decisions/`, in plain Markdown, so "why did we do this?" still has an answer next month. |
| [`test-audit`](skills/test-audit/SKILL.md) | `/test-audit` · `$test-audit` (Codex) | Finds tests that protect nothing, slow tests, and gaps in coverage. Applies fixes and uses mutation testing to check that existing protections still hold. |

## Install

```sh
npx skills add brasillero/brstack
```

It asks which skills and agents you want. `task-decisions` needs Node.js 20+ and git for its scripts; the other skills are just instructions.

## How it's laid out

Each skill lives in `skills/<name>/`. Its `SKILL.md` starts with the `name` and `description` fields from the [Agent Skills specification](https://agentskills.io/specification#frontmatter). Supporting files live alongside it.

Invocation settings are client-specific. For `test-audit`, `disable-model-invocation: true` in `SKILL.md` makes it manual in Claude Code, Cursor, and VS Code. The [Codex policy](skills/test-audit/agents/openai.yaml) makes it manual there as well.

Tests for a skill's scripts live in `tests/<name>/`, outside the skill, so they don't get installed. Run them with:

```sh
node --test tests/*/*.test.mjs
```

## Make them yours

They're written for how I work, like my own model setup in `delegate`. Fork them and change whatever doesn't fit you.
