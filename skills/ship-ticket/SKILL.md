---
name: ship-ticket
description: Ship a ticket or plain-language requirement on a dedicated branch through implementation, separate reviews, commits, push, and a draft pull request, with tracker transitions when a ticket is supplied.
disable-model-invocation: false
---

# Ship Ticket

Drive one work item—a tracked ticket or a requirement supplied in the conversation—to a reviewed draft pull request. Keep the parent agent as orchestrator. Give implementation, each review, and every remediation to separate fresh subagents.

## 1. Require the five skills

Union every repository-local/project and global/user-installed skill folder or catalog exposed by the active harness. Match skill frontmatter names, not only directory names. Require exactly these workflow dependencies:

- `implement`
- `codebase-design`
- `code-review`
- `ponytail`
- `ponytail-review`

If any are missing, report every missing name and its source group, then stop. For Matt Pocock skills, direct the user to:

```bash
npx skills@latest add mattpocock/skills
```

For Ponytail skills, direct the user to the [official installation instructions](https://github.com/DietrichGebert/ponytail#install). Ask the user to restart the harness and invoke `$ship-ticket` again after installation.

Continue only when the refreshed local-plus-global union contains all five skills.

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

### Full validation gate

Before implementation, discover the full validation workflow from repository instructions, root and workspace manifests, task runners, scripts, and CI configuration. Inventory every available validation command, including tests, linters, formatters, type checks, builds, and any other repository checks. Use aggregate commands only after verifying they cover the inventory; run uncovered commands separately. Include every workspace and configured validation variant. Keep deployment, publishing, and other operational scripts outside this validation inventory.

Define `<validation-contract>` from this inventory and the following requirements; include it verbatim in every implementation and remediation subagent prompt:

> Refresh the inventory whenever validation scripts or configuration change. Run the entire validation inventory after implementation and after every subsequent change batch, including review fixes, manual edits, formatter output, and generated-file changes. Focused checks during development supplement this gate. If a command changes files, inspect the changes and rerun the entire workflow on the resulting state until all commands pass without further source changes. Return the exact commands, results, and validated commit SHA. Failed, unavailable, or skipped checks leave the gate blocked; report the blocker and retry point.

The parent verifies this evidence after every implementation or remediation subagent returns and after any other changes, before starting the next review or work item. Any later change invalidates validation and affected review results: pass this gate, commit the changes, and repeat those reviews. Before publishing and declaring completion, require a passing full validation run for final `HEAD` with all changes accounted for.

Spawn a fresh subagent with the full spec, linked context, repository instructions, `<work-base>`, branch, and commit format:

> Load and apply `implement`, `codebase-design`, and `ponytail` at full intensity. Implement every work-item requirement and acceptance criterion with the smallest correct change at the existing seam. Preserve validation, error handling, security, accessibility, and repository standards. Run focused checks during work and pass `<validation-contract>` before handoff. Commit only this work item's implementation on the work branch. End before `implement`'s review handoff; the parent orchestrator will give that review to a fresh agent. Return commit SHA, requirement evidence, changed-file accounting, validation commands with results, and blockers.

Wait for completion. Verify the commit exists on the work branch, follows the commit format, contains no unrelated work, and has evidence for every requirement. Stop on uncommitted or unaccounted changes.

## 4. Review and remediate

Run reviews in this order against `<work-base>...HEAD`.

### Correctness review

Spawn a fresh subagent with the spec, repository instructions, fixed point, commit list, and this brief:

> Load and apply `code-review`. Review `<work-base>...HEAD` against repository Standards and the full supplied spec. Return both axes separately with exact actionable findings. Pass only when Standards has no documented violation and Spec has no missing, partial, wrong, or unrequested behavior.

### Simplicity review

After correctness passes, spawn another fresh subagent with the diff and this brief:

> Load and apply `ponytail-review`. Review `<work-base>...HEAD` for unnecessary complexity. Return exact actionable findings. Pass only with `Lean already. Ship.`

### Remediation loop

When either review reports an issue, spawn a fresh remediation subagent with the spec, all current findings, repository instructions, fixed point, branch, and commit format:

> Load and apply `implement`, `codebase-design`, and `ponytail` at full intensity. Fix only the reported findings with the smallest correct change. Keep work-item requirements and repository standards binding. Run focused checks and pass `<validation-contract>` before handoff. Commit the fixes separately on the work branch and return the commit SHA, finding-by-finding evidence, changed-file accounting, and validation results.

After any remediation, restart with a fresh correctness-review subagent because the diff changed. Continue until correctness passes and a later fresh simplicity review returns `Lean already. Ship.`

## 5. Push and open the draft pull request

After both reviews pass:

1. Run the full validation workflow at final `HEAD` and verify the worktree has no unaccounted changes.
2. Verify every commit after `<work-base>` follows the commit format and belongs to the work item.
3. Push the work branch to the repository remote.
4. Open a draft pull request through the repository host's native mechanism, targeting `<base-branch>` from the work branch.
5. Use the commit subject format for the pull-request title. Limit the body to a `Summary` of 1–3 short sentences: lead with why the change is needed, then explain the key decision and its rationale. Add a native ticket link for tracked work. Omit change inventories, validation sections, and review sections.
6. **Tracked tickets only:** after draft pull-request creation succeeds, keep `<executor>` assigned and transition the ticket to `<review-handoff-state>` through the tracker's native lifecycle mechanism.

Keep the pull request in draft. Do not merge it. If push, pull-request creation, or an applicable final tracker transition fails, preserve completed state, report the exact failure and retry point, then stop.

## 6. Complete

Declare completion only when every work-item requirement has evidence, the full validation workflow passes at final `HEAD`, the worktree is accounted for, correctness passes, Ponytail returns `Lean already. Ship.`, every work-item commit is pushed, the pull request is open in draft against `<base-branch>`, and, for tracked tickets, the ticket remains assigned to `<executor>` in `<review-handoff-state>`.

Report the work item, branch, commit range, validation results, both review results, and draft pull-request link. Include executor and tracker transitions only for tracked tickets.
