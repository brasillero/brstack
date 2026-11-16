# brstack

hey, i'm brasillero. i spend my days shipping software with agents doing a lot of the heavy lifting, and these are the skills that keep that work honest.

**agents should do the digging, people should get the short version.** let one agent investigate and another implement, but when something reaches a human, a review comment or an answer, it should read like a person wrote it.

**decisions deserve a paper trail.** "why did we do this?" should still have an answer next month, written next to the code, not lost in a chat.

nothing here is magic. it's the stuff i kept repeating to my agents, written down once.

## Skills

| Skill | Say | What it does |
| --- | --- | --- |
| [`delegate`](skills/delegate/SKILL.md) | "Investigate this bug" · "Implement it" | Splits the work: Codex investigates, a Claude subagent implements, Claude orchestrates and does the final review. Other models only when you name them. |
| [`review-comment-writing`](skills/review-comment-writing/SKILL.md) | (automatic) · "Post that review" | Writes clear review feedback and preserves the intended format: general comment, resolvable discussion, or thread on the code. Asks when placement is unclear. Only for people; agents get the full version. |
| [`task-decisions`](skills/task-decisions/SKILL.md) | "Start notes for ABC-123" · "Record a decision" · "What's still open?" | Keeps a backlog and a decision log per task in `.task-decisions/`, in plain Markdown, so "why did we do this?" still has an answer next month. |

## Install

```sh
npx skills add brasillero/brstack
```

It asks which skills and agents you want. `task-decisions` needs Node.js 20+ and git for its scripts; the other two are just instructions.

## How it's laid out

Each skill lives in `skills/<name>/` and follows the [Agent Skills spec](https://agentskills.io/specification): a `SKILL.md`, plus `scripts/`, `references/`, `assets/` and `evals/` when it needs them. Tests for a skill's scripts live in `tests/<name>/`, outside the skill, so they don't get installed. Run them with:

```sh
node --test tests/*/*.test.mjs
```

## Make them yours

They're written for how I work, like my own model setup in `delegate`. Fork them and change whatever doesn't fit you.
