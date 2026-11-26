# Verifier brief

Replace each bracketed field, then send this text as the sub-agent's task.

Verify the result of a test audit in [repo path], from [baseline commit] to [result commit]. Do not change the repo. Make one pinned snapshot of the result and one of the baseline commit. Copy the dependencies instead of linking them.

Read these inputs from [audit folder]: `protections.md`, `mutations.md`, `audit-report.md`, `decisions.md`, `changes.md`, and the baseline and after measurements.

Confirm that the gate lost no protection and that the new and changed tests have none of the flaws that the checks below look for. Do these checks:
1. **Mutation regression.** Run every mutation in `mutations.md` against the new gate, and restore the code after each one. A mutation that the old gate caught and the new gate misses is a blocker, even if deep catches it.
2. **New mutations.** Add at least one mutation per protection family. Aim them at tests that were shrunk, merged, moved or rewritten. Prefer mutations that leave summary numbers unchanged, such as volume, count, bounds or length, while the result is wrong. Run each one against the old gate and the new gate, and report every mutation that passes the new gate.
3. **Recorded proofs.** Run again at least six of the proofs recorded in `changes.md`.
4. **New test helpers.** Check that the helpers use no production constant as an expected value, and that they check structure as well as content.
5. **Deep tier.** Check that deep tests run under the deep script and are skipped in the gate. Check that the scheduled job runs, that the CI of each push is unchanged, and that a failing deep test fails the scheduled job and shows in its logs.
6. **Flaky tests.** Look for assertions on wall-clock time. Run the changed files in shuffled order. Check that shared fixtures are read-only and released after use.
7. **Numbers.** Run the gate and deep, and report their test counts and times.

Report each finding as blocker, should-fix or nit, with the file and line, the mutation or failure case, and a fix. Then list what you verified. Add your new mutations to `mutations.md` so the next audit reuses them.
