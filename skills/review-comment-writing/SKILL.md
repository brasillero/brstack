---
name: review-comment-writing
description: Write clear review feedback for people and, when asked to publish it, choose the intended general comment, resolvable discussion, or code-linked review thread. Use for PR/MR feedback shown to a developer or posted to a review. Not for findings passed between agents.
---

# Review comment writing

Take a review observation and rewrite it so the developer reading it
understands it quickly. The input, often written by an agent, is usually long,
heavily technical or full of hedging. For wording requests, return the comment
text only. Publish only when the user has asked you to; approving wording alone
does not ask you to post it.

Use this only for text a person will read. When the findings go to another
agent, keep them in their original detailed form.

## Choose where the feedback belongs

Wording and placement are separate choices. Preserve the user's requested
placement through rewrites and approval: "post that review" carries forward
the review context and any file/line already identified. Making the text more
conversational or phrasing it as a question does not turn it into a general
comment.

| Format | When to use it |
| --- | --- |
| General PR/MR comment | A status update, summary, or conversation the user wants on the overview, without a resolvable review thread. |
| Resolvable discussion without a code anchor | A point that needs follow-up and resolution but applies to the MR as a whole or spans several files. |
| Code-linked review thread | Feedback about a specific piece of code. Anchor it to the relevant file and line in the current diff so the author can reply and resolve it. |

Honor an explicit choice. For a code finding with a known location, a request
to publish the review calls for a code-linked thread. A request to "comment"
can also refer to that review thread; interpret it in the existing context.

If the intended format is still unclear, ask before publishing: "Do you want
a general comment, a resolvable discussion on the MR, or a review thread on
the code?" Ask in the user's language and offer only the applicable choices.
If resolution is already requested, clarify only whether it should be on the
code or on the MR as a whole. Do not ask again when the conversation already
establishes the choice.

A file link in a comment body does not create a code-linked review. If the
requested anchor is unavailable or the tool cannot create the requested
thread type, explain the limitation and ask how to proceed. Do not silently
fall back to a general comment.

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
  or soften or sharpen them. Preserve uncertainty; don't turn a suspected
  behavior into a confirmed defect.
- Inline code formatting for identifiers. Use a short code block when it shows
  the fix better than words.

## Simplify
- For a confirmed change request, lead with what should change and why.
- Follow the user's requested tone. If they want a question asking the author
  to test a scenario, ask naturally, include the concrete input and expected
  behavior, and explain the concern briefly. For example: "Can you test
  searching for `19/05/2026` when the option displays that date? Does it still
  find the option? The search appears to use the original ISO label."
- No labels like "Sugestão:", "Nice to have:", "nit:", or severity tags.
- Leave out internal history (commit hashes, earlier MRs, how it was found)
  and the reviewer's reasoning steps.
- No filler or AI-sounding phrasing. Keep qualifications that reflect the
  evidence. One idea per comment.

## Publish and verify

Use the host's operation for the chosen format. On GitLab:

- General comment: `POST /projects/:id/merge_requests/:iid/notes`.
- Resolvable MR discussion: `POST /projects/:id/merge_requests/:iid/discussions`
  without a diff position.
- Code-linked review: the same discussions endpoint with a `position` for
  the current diff. Fetch the latest MR version and use its base, head and
  start SHAs, `position_type: text`, both file paths, and the appropriate line
  numbers. Added lines use `new_line`, removed lines use `old_line`, and
  unchanged context uses both.

See the [GitLab Discussions API](https://docs.gitlab.com/api/discussions/#create-a-merge-request-thread)
for the position schema. On other hosts, use their review/thread operation;
do not assume a general comment has the same behavior.

Check the created object, or read it back, before reporting success. Verify
that a requested resolvable discussion is resolvable and that a code-linked
thread has the intended file/line position. If the result differs, report it
and ask before creating a replacement or deleting the original. Return a
direct link to the posted feedback.
