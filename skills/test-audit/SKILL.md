---
name: test-audit
description: Check a test suite for tests that protect nothing, tests that are too slow, and protections that are missing, then fix it and prove with mutations that no protection was lost.
disable-model-invocation: true
---

# Test audit

You orchestrate four roles. You decide. The auditor finds problems, the implementer applies the changes, and the verifier checks them. Each of these three is a sub-agent with a fresh context. The auditor and the verifier get their instructions from a brief file in this folder.

Create one audit folder outside the repo, for example `<scratch>/test-audit-<date>/`. Every role reads and writes its files there, so no result lives only in a chat message.

These terms have a fixed meaning in this skill:
- **Protection.** A property that the suite must keep catching when the code breaks it.
- **Mutation.** A small, temporary change to production code that a real bug could cause. Run it to prove that a test catches it, then restore the code.
- **Gate and deep.** The gate is the suite that runs on every push. Deep is the exhaustive suite that runs on a schedule.

## 1. Agree on the protections

Read the repo's agent docs, contributing guide, specs and test conventions, and the code. List the protection families, which are the properties the suite exists to catch. Examples are published interface values, contracts between components, file formats and their migrations, concurrency and ordering, and performance budgets.

Show the list to the user. Ask if the list is complete. Ask how much autonomy you have: decide and apply, or bring the plan for approval first.

This step is done when the user confirms both. Write them to `protections.md`.

## 2. Measure the baseline and its mutations

Measure the suite with one method that you will reuse unchanged in step 5:
- the number of tests;
- the wall time, run twice on an idle machine;
- the duration of each test, as the test runner's JSON report;
- the CI step time of the last green run.

Save the numbers to `baseline.md` and keep the raw files.

Then build the mutation set. Write at least one mutation per protection family, and more for families with many tests. Good mutations change one constant, drop one branch, reverse an order or drop a field. Run each mutation against the gate and record which tests fail. Restore the code after each one, and confirm with `git status` that the tree is clean.

Write `mutations.md` with one row per mutation: the file, the change, the protection family and the tests that failed. A mutation that the gate does not catch is a gap. Record it as a gap in the same file.

This step is done when every family has at least one mutation that the gate catches or that is recorded as a gap, and the tree is clean.

## 3. Audit

Spawn the auditor on a pinned, read-only snapshot. Its instructions are in [auditor-brief.md](auditor-brief.md). Fill in the repo path, the commit and the audit folder. The auditor writes `audit-report.md` and `findings.json`.

This step is done when both files exist and every finding has a file and line, a class, a failure case, a recommendation and a risk.

## 4. Decide

For each finding in `findings.json`, write a decision in `decisions.md`: apply, defer or reject, with a reason. Defer a finding that needs an input you do not have, and name that input. Reject a finding that would remove the last gate test of a protection family. Move that coverage instead of dropping it.

If the user chose approval first, show the decisions table and wait for the answer.

This step is done when every finding has a decision and a reason in `decisions.md`.

## 5. Apply

Spawn an implementer and give it the decisions, the protections, the mutation set and the baseline method. It follows these rules:
- **Mutation proof before each cut.** Before it shrinks a test or moves it to deep, it runs every mutation of that family from `mutations.md` and shows that the gate still fails. It records the proof in the commit message and in `changes.md`.
- **One row per change in `changes.md`.** Each row has the action, the test count and seconds before and after, the protection kept and the proof.
- **A deep tier.** It tags the exhaustive sweeps, adds a script that runs them, such as `test:deep`, and adds a scheduled CI job for that script. The CI that runs on each push stays unchanged. It documents both in the repo's agent docs.
- **A green gate and local commits only.** It commits locally and does not push. At the end it measures again with the baseline method and writes `after.*` and `deep.*`.

This step is done when the gate passes, `changes.md` is complete and the new measurements exist.

## 6. Verify

Spawn a new verifier on a pinned snapshot of the result. Its instructions are in [verifier-brief.md](verifier-brief.md). It runs the whole mutation set against the new gate and adds mutations of its own. A mutation that the old gate caught and the new gate misses blocks the result.

Send each blocker back to the implementer, then run the verifier again. This step is done when the verifier reports no blocker.

## 7. Report

Push only if the user allows it, and then record the CI times. The report contains these parts:
- the gate test count, the local wall time, the CI step time, and the deep test count and time, each before and after;
- a chart of seconds per protection family before and after, using the family of each test file from the inventory in `audit-report.md`;
- a chart of the number of changes per action;
- each deferred finding with the input it needs;
- each mutation that exposed a weakness in the baseline suite, such as a gap or a weak test.
