# Auditor brief

Replace each bracketed field, then send this text as the sub-agent's task.

Audit the test suite of [repo path] at [commit]. Do not change the repo. Work in a pinned snapshot. To make it, run `git archive [commit] | tar -x -C <tmp dir>` with a temporary directory of your choice, and copy the dependencies instead of linking them. Read the repo's agent docs and test docs first.

Read these inputs from [audit folder]:
- `protections.md`, the protection families that the suite must keep catching;
- `baseline.md` and the JSON report with each test's duration;
- `mutations.md`, the mutation set with the tests that catch each mutation and the known gaps.

The user worries that the suite holds tests that pin accidental behaviour, tests built around configurations nobody uses, and tests that cost more time than they protect. Treat this as a routine check.

Write `audit-report.md` in [audit folder] with three sections.

The first section is an inventory. Map each test file to one primary protection family. Give the test count and the summed seconds per family. Use `mutations.md` to show which tests catch a mutation.

The second section lists the findings. Give each finding a file and line, a class, a concrete failure case, a recommendation and a risk. The recommendation is one of keep, delete, merge, parametrize, speed up, move to deep or rewrite. The classes are these:
- **Redundant.** The same property is asserted in several places, and the extra copies catch no mutation that the first copy misses.
- **Over-specified.** The test pins a detail that a correct refactor would change, such as an exact order, a sample count, wording users do not rely on, or wall-clock time.
- **Circular.** The expected value comes from the code under test, so a wrong constant changes the code and the expectation together.
- **Artificial edge.** The test pins a configuration that no user reaches, or a past bug, and no longer catches any mutation.
- **Weak.** The test cannot fail, or it asserts too little. Prove it with a mutation that the test misses.
- **Slow.** For each of the heaviest tests, describe how to keep what it catches at a lower cost. Options are shared fixtures, a smaller equivalent case, a lower resolution, a few targeted extremes instead of a full sweep, or a move to deep.
- **Gap.** A protection family, or one of its mutations, that no gate test catches.

The third section is a plan ordered by priority. Give each action its estimated saving in test count, local time and CI time, and its risk. Name the tests whose assertions the changes must not weaken.

Also write `findings.json` in [audit folder]. It is an array of objects with these fields: `id`, `file`, `line`, `class`, `family`, `recommendation`, `risk`, `est_seconds_saved` and `evidence`. Put the failure case in `evidence`, and the mutation result when the finding has one. The orchestrator uses this file for decisions.

Run the suite with timing per file. Try one or two of your proposed speed-ups in the snapshot to calibrate the estimates. Back every claim of the class redundant or weak with a mutation result.
