---
name: ship-ponytail-ticket
description: Ship a ticket or plain-language requirement on a dedicated branch through Ponytail implementation, separate correctness and simplicity reviews, commits, push, and a draft pull request, with tracker transitions when a ticket is supplied.
disable-model-invocation: false
---

# Ship Ponytail Ticket

Drive one work item—a tracked ticket or a requirement supplied in the conversation—to a reviewed draft pull request. Keep the parent agent as orchestrator. Give implementation, each review, and every remediation to separate fresh subagents.

## 1. Require the three skills

Union every repository-local/project and global/user-installed skill folder or catalog exposed by the active harness. Match skill frontmatter names, not only directory names. Require exactly these workflow dependencies:

- `ponytail`
- `ponytail-review`
- `code-review`

If any are missing, report every missing name and its source group, then stop. For `code-review`, direct the user to `npx skills@latest add mattpocock/skills`. For Ponytail skills, direct the user to the [official installation instructions](https://github.com/DietrichGebert/ponytail#install). Ask the user to restart the harness and invoke `$ship-ponytail-ticket` again after installation.

Continue only when the refreshed local-plus-global union contains all three skills.

## 2. Pin the work item and branch

1. Resolve the work item from the invocation argument or current conversation:
   - **Tracked ticket:** when the user supplies a ticket reference as the work item, fetch its full body, comments, acceptance criteria, and linked context through its tracker. Stop if the ticket is ambiguous, retrieval fails, the ticket is blocked, or its requirements lack enough detail to implement safely; switching to prompt-only work requires the user's direction.
   - **Prompt-only:** use the supplied requirement and relevant conversation as the spec. State its scope and checkable acceptance criteria without adding requirements. Proceed when these are clear; ask only for missing details that prevent safe implementation. This mode needs no issue, tracker configuration, authenticated tracker identity, or tracker operations.
2. Read repository instructions and inspect the relevant code paths. Preserve unrelated work. Ask the user to isolate changes only when the work item cannot be committed and reviewed independently.
3. Record the current branch as `<base-branch>` and its current `HEAD` as `<work-base>`.
4. Create and check out a dedicated branch from `<work-base>`, following repository or harness naming rules; otherwise use `feat/<ticket-id>-<short-slug>` for tracked tickets or `feat/<short-slug>` for prompt-only work. Reuse an existing branch only when its history and work-item context prove it belongs to this work and starts from `<work-base>`.
5. Define the commit subject as `<type>(<scope>): <terse imperative summary>`. Use a Jira key from a tracked ticket as `<scope>`; otherwise use a stable repository-relevant scope. Keep required evidence and any ticket link in the body.
6. **Tracked tickets only:** resolve `<executor>` from the tracker's authenticated identity and discover its native lifecycle. Resolve In Progress and `<review-handoff-state>`; prefer Review or In Review, then Testing or QA, then Done. Use existing native statuses or lifecycle labels; when only open/closed state exists, use open for In Progress and closed for handoff. Assign the ticket to `<executor>`, then transition it to In Progress. Stop before implementation if assignment or transition fails.

Continue only when the spec, acceptance criteria, repository rules, clean change boundary, base branch, work base, work branch, and commit format are known, plus confirmed assignment, lifecycle mapping, and In Progress transition for tracked tickets.

Pass the resolved spec and input mode to every implementation, review, and remediation subagent. For prompt-only work, this spec replaces downstream skills' tracker setup and issue lookup steps, including `code-review`'s requirement for `docs/agents/issue-tracker.md`. Review the supplied spec directly and run both Standards and Spec axes.

## 3. Implement

Spawn a fresh subagent with the full spec, linked context, repository instructions, `<work-base>`, branch, and commit format:

> Load and apply `ponytail` at full intensity. Implement every work-item requirement and acceptance criterion with the smallest correct change at the existing seam. Preserve validation, error handling, security, accessibility, and repository standards. Run focused checks during work and the full required suite at completion. Commit only this work item's implementation on the work branch. Return commit SHA, requirement evidence, changed-file accounting, tests with results, and blockers.

Wait for completion. Verify the commit exists on the work branch, follows the commit format, contains no unrelated work, and has evidence for every requirement. Stop on uncommitted or unaccounted changes.

## 4. Review and remediate

Record `HEAD` as `<reviewed-head>`. Run the following read-only reviews in order against the full `<work-base>...<reviewed-head>` diff, keeping `<work-base>` fixed throughout the work item.

### Ponytail review

Spawn a fresh subagent with the spec, repository instructions, fixed point, reviewed commit, commit list, and diff:

> Load and apply `ponytail-review`. Review `<work-base>...<reviewed-head>` for unnecessary complexity. Return exact actionable findings. Pass only with `Lean already. Ship.`

### Correctness review

After Ponytail review passes, spawn another fresh subagent with the full spec and linked context, repository instructions, fixed point, reviewed commit, commit list, and diff:

> Load and apply `code-review`. Review the full work-item diff against repository Standards and the full supplied spec, using the skill's separate Standards and Spec subagents. Return both axes separately with exact actionable findings. Pass only when Standards has no documented violation and Spec has no missing, partial, wrong, or unrequested behavior.

### Remediation loop

When either review reports an issue, spawn a fresh remediation subagent with the spec, all current findings, repository instructions, fixed point, branch, and commit format:

> Load and apply `ponytail` at full intensity. Fix only the reported findings with the smallest correct change. Keep work-item requirements and repository standards binding. Run focused checks and the full required suite. Commit the fixes separately on the work branch and return the commit SHA, finding-by-finding evidence, changed-file accounting, and test results.

Any subsequent work-item change invalidates both review results, including remediation, additional edits, and fixes during final verification or publication. Commit the changes separately, then restart this section from Ponytail review with fresh review subagents against the full work-item diff.

The review gate passes only when correctness passes and Ponytail returns `Lean already. Ship.` for the same `<reviewed-head>`, `HEAD` still equals that commit, and all work-item changes are committed.

## 5. Push and open the draft pull request

After the review gate in section 4 passes:

1. Run the full required test suite at final `HEAD` and verify the worktree has no unaccounted changes.
2. Verify every commit after `<work-base>` follows the commit format and belongs to the work item.
3. Confirm the section 4 review gate still passes, then push the work branch to the repository remote.
4. Open a draft pull request through the repository host's native mechanism, targeting `<base-branch>` from the work branch.
5. Use the commit subject format for the pull-request title. Keep the body concise: implementation summary, tests, Standards result, Spec result, Ponytail result, and the spec source: a native ticket link for tracked work, or the requirement and acceptance criteria for prompt-only work.
6. **Tracked tickets only:** after draft pull-request creation succeeds, keep `<executor>` assigned and transition the ticket to `<review-handoff-state>` through the tracker's native lifecycle mechanism.

Keep the pull request in draft. Do not merge it. If push, pull-request creation, or an applicable final tracker transition fails, preserve completed state, report the exact failure and retry point, then stop.

## 6. Complete

Declare completion only when every work-item requirement has evidence, the full required suite passes at final `HEAD`, the worktree is accounted for, the section 4 review gate passes at final `HEAD`, every work-item commit is pushed, the pull request is open in draft against `<base-branch>`, and, for tracked tickets, the ticket remains assigned to `<executor>` in `<review-handoff-state>`.

Report the work item, branch, commit range, tests, both review results, and draft pull-request link. Include executor and tracker transitions only for tracked tickets.
