---
name: ship-ponytail-ticket
description: Ship one assigned ticket on a dedicated branch through native tracker transitions, Ponytail implementation, separate correctness and simplicity reviews, commits, push, and a linked draft pull request.
disable-model-invocation: true
---

# Ship Ponytail Ticket

Drive one ticket to a reviewed draft pull request. Keep the parent agent as orchestrator. Give implementation, each review, and every remediation to separate fresh subagents.

## 1. Require the three skills

Union every repository-local/project and global/user-installed skill folder or catalog exposed by the active harness. Match skill frontmatter names, not only directory names. Require exactly these workflow dependencies:

- `ponytail`
- `ponytail-review`
- `code-review`

If any are missing, report every missing name and its source group, then stop. For `code-review`, direct the user to `npx skills@latest add mattpocock/skills`. For Ponytail skills, direct the user to the [official installation instructions](https://github.com/DietrichGebert/ponytail#install). Ask the user to restart the harness and invoke `$ship-ponytail-ticket` again after installation.

Continue only when the refreshed local-plus-global union contains all three skills.

## 2. Pin the ticket, lifecycle, and branch

1. Resolve the ticket from the invocation argument or current conversation. Fetch its full body, comments, acceptance criteria, and linked context through its tracker. Stop when the ticket is missing, ambiguous, blocked, or lacks enough detail to implement safely.
2. Resolve `<executor>` from the configured issue tracker's authenticated identity. Discover the ticket's native lifecycle. Resolve its native In Progress equivalent and `<review-handoff-state>`; prefer Review or In Review, then Testing or QA, then Done. Use existing native statuses or lifecycle labels instead of creating parallel labels. When only open/closed issue state exists, use open for In Progress and closed for `<review-handoff-state>`.
3. Read repository instructions and inspect the relevant code paths. Preserve unrelated work. Ask the user to isolate changes only when the ticket cannot be committed and reviewed independently.
4. Record the current branch as `<base-branch>` and its current `HEAD` as `<ticket-base>`.
5. Create and check out `feat/<ticket-id>-<short-slug>` from `<ticket-base>`. Reuse an existing branch only when its history and tracker references prove it belongs to this ticket and starts from `<ticket-base>`.
6. Define the commit subject as `<type>(<scope>): <terse imperative summary>`. Use a Jira key from the ticket as `<scope>`; otherwise use a stable repository-relevant scope. Keep ticket links and required evidence in the body.
7. Assign the ticket to `<executor>` with the tracker's native assignment mechanism, then transition it to the native In Progress equivalent. Stop before implementation when assignment or transition fails.

Continue only when the ticket, executor, lifecycle mapping, repository rules, clean change boundary, base branch, ticket base, ticket branch, commit format, assignment, and In Progress transition are confirmed.

## 3. Implement

Spawn a fresh subagent with the full ticket, linked context, repository instructions, `<ticket-base>`, branch, and commit format:

> Load and apply `ponytail` at full intensity. Implement every ticket requirement and acceptance criterion with the smallest correct change at the existing seam. Preserve validation, error handling, security, accessibility, and repository standards. Run focused checks during work and the full required suite at completion. Commit only this ticket's implementation on the ticket branch. Return commit SHA, requirement evidence, changed-file accounting, tests with results, and blockers.

Wait for completion. Verify the commit exists on the ticket branch, follows the commit format, contains no unrelated work, and has evidence for every requirement. Stop on uncommitted or unaccounted changes.

## 4. Review and remediate

Record `HEAD` as `<reviewed-head>`. Run the following read-only reviews in order against the full `<ticket-base>...<reviewed-head>` diff, keeping `<ticket-base>` fixed throughout the ticket.

### Ponytail review

Spawn a fresh subagent with the ticket, repository instructions, fixed point, reviewed commit, commit list, and diff:

> Load and apply `ponytail-review`. Review `<ticket-base>...<reviewed-head>` for unnecessary complexity. Return exact actionable findings. Pass only with `Lean already. Ship.`

### Correctness review

After Ponytail review passes, spawn another fresh subagent with the full ticket and linked context, repository instructions, fixed point, reviewed commit, commit list, and diff:

> Load and apply `code-review`. Review the full ticket diff against repository Standards and the full ticket Spec, using the skill's separate Standards and Spec subagents. Return both axes separately with exact actionable findings. Pass only when Standards has no documented violation and Spec has no missing, partial, wrong, or unrequested behavior.

### Remediation loop

When either review reports an issue, spawn a fresh remediation subagent with the ticket, all current findings, repository instructions, fixed point, branch, and commit format:

> Load and apply `ponytail` at full intensity. Fix only the reported findings with the smallest correct change. Keep ticket requirements and repository standards binding. Run focused checks and the full required suite. Commit the fixes separately on the ticket branch and return the commit SHA, finding-by-finding evidence, changed-file accounting, and test results.

Any subsequent ticket change invalidates both review results, including remediation, additional edits, and fixes during final verification or publication. Commit the changes separately, then restart this section from Ponytail review with fresh review subagents against the full ticket diff.

The review gate passes only when correctness passes and Ponytail returns `Lean already. Ship.` for the same `<reviewed-head>`, `HEAD` still equals that commit, and all ticket changes are committed.

## 5. Push and open the draft pull request

After the review gate in section 4 passes:

1. Run the full required test suite at final `HEAD` and verify the worktree has no unaccounted changes.
2. Verify every commit after `<ticket-base>` follows the commit format and belongs to the ticket.
3. Confirm the section 4 review gate still passes, then push the ticket branch to the repository remote.
4. Open a draft pull request through the repository host's native mechanism, targeting `<base-branch>` from the ticket branch.
5. Use the commit subject format for the pull-request title. Keep the body concise: implementation summary, tests, Standards result, Spec result, Ponytail result, and a native ticket link.
6. After draft pull-request creation succeeds, keep `<executor>` assigned and transition the ticket to `<review-handoff-state>` through the tracker's native lifecycle mechanism.

Keep the pull request in draft. Do not merge it. If push, pull-request creation, or the final tracker transition fails, preserve completed state, report the exact failure and retry point, then stop.

## 6. Complete

Declare completion only when every ticket requirement has evidence, the full required suite passes at final `HEAD`, the worktree is accounted for, the section 4 review gate passes at final `HEAD`, every ticket commit is pushed, the linked pull request is open in draft against `<base-branch>`, and the ticket remains assigned to `<executor>` in `<review-handoff-state>`.

Report the ticket, executor, tracker transitions, branch, commit range, tests, both review results, and draft pull-request link.
