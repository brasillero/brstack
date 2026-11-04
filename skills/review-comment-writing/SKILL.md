---
name: review-comment-writing
description: Rewrite a code-review observation into a short merge request comment that a person can read easily, without changing what it says. Use when a review comment will be read by a human (posted on an MR or shown to a developer). Not for review findings passed between agents.
---

# Review comment writing

Take a review observation and rewrite it so the developer reading it
understands it quickly. The input, often written by an agent, is usually long,
heavily technical or full of hedging. The output is the comment text only.

Use this only for text a person will read. When the findings go to another
agent, keep them in their original detailed form.

## Language
Before writing anything, make sure you know the language of the comment. Use
the one the user or the calling agent gave. If none was given, ask the user
which language to use, and use it for every comment in that request. Don't
guess it from the input: findings written by an agent are often in a different
language from the comment that gets posted.

## Keep
- Every technical fact: identifiers, file and function names, values, code
  snippets, and the proposed change.
- The meaning and the strength of the point. Don't add new points, drop any,
  or soften or sharpen them.
- Inline code formatting for identifiers. Use a short code block when it shows
  the fix better than words.

## Simplify
- Lead with what should change, then why, in one or two sentences.
- Be direct and state things ("these should use…"), don't ask ("should we
  use…?").
- No labels like "Sugestão:", "Nice to have:", "nit:", or severity tags.
- Leave out internal history (commit hashes, earlier MRs, how it was found)
  and the reviewer's reasoning steps.
- No filler, no hedging, no AI-sounding phrasing. One idea per comment.
