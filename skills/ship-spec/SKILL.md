---
name: ship-spec
description: Ship an approved spec on a dedicated branch through native tracker transitions, separate implementation commits, independent review gates, and a linked draft pull request.
disable-model-invocation: false
---

# Ship Spec

Drive one approved spec to completion. Keep the parent agent as orchestrator; give implementation and review to separate fresh subagents.

Define `<terse-output-contract>` once and apply it to every human-readable artifact and message created by this run: tracker comments, commit messages, pull-request text, blocker reports, and completion reports.

> Write concise, normal prose. State only the outcome, required evidence, and next action. Use short headings and bullets. Preserve required identifiers, links, validation results, review outcomes, and blockers. Express context once at the narrowest useful level.

Keep internal evidence exhaustive. Terseness changes presentation, not gates or legwork.

## 1. Gate on required skills

Build the available-skill set by unioning every repository-local/project and global/user-installed catalog exposed by the active harness. Check that set for these Matt Pocock skills under **Engineering**:

- `ask-matt`
- `code-review`
- `codebase-design`
- `diagnosing-bugs`
- `domain-modeling`
- `grill-with-docs`
- `grill-me`
- `grilling`
- `implement`
- `improve-codebase-architecture`
- `prototype`
- `research`
- `setup-matt-pocock-skills`
- `tdd`
- `to-spec`
- `to-tickets`
- `triage`
- `wayfinder`
- `resolving-merge-conflicts`

Also require these Ponytail skills:

- `ponytail`
- `ponytail-review`

Resolve every missing skill before stopping. If an Engineering skill is missing, list the missing names and ask the user to run:

```bash
npx skills@latest add mattpocock/skills
```

Tell them to select every skill under **Engineering**.

If a Ponytail skill is missing, list the missing names and direct the user to the [official installation instructions](https://github.com/DietrichGebert/ponytail#install) for the active harness. For Codex, ask them to run:

```bash
codex plugin marketplace add DietrichGebert/ponytail
codex plugin add ponytail@ponytail
```

Tell them to review and trust the Ponytail hooks in `/hooks`.

When either group has missing skills, report all relevant setup actions together, tell the user to restart the harness and invoke `$ship-spec` again, then end the run. Pass this gate only when the refreshed catalog union contains every Engineering and Ponytail skill above.

## 2. Gate on repository setup

Check the repository-local instructions and `docs/agents/` configuration required by `setup-matt-pocock-skills`. If setup is missing or inconsistent, load and follow `setup-matt-pocock-skills` completely. Finish its user-confirmed setup before continuing.

Pass this gate only when the repository instruction file contains its `## Agent skills` configuration and the referenced issue-tracker, triage-label, and domain files exist and agree with it.

## 3. Pin the work and open the spec branch

Require a parent spec produced by `to-spec`. Child tickets produced by `to-tickets` are optional.

1. Resolve the parent spec from the invocation argument or current conversation. Fetch its full body and comments through the configured issue tracker.
2. Verify the spec contains the `to-spec` sections and an approved testing seam. If no valid spec is available, ask the user to run `$to-spec`, then end the run.
3. Resolve every child ticket through an exhaustive **union**, never a first-success fallback:
   - query the tracker's native child or sub-issue relationships;
   - parse explicit child references from the parent body and comments;
   - run a reverse-parent query across issues in every state, selecting tickets whose structured parent field resolves exactly to this parent.

   For GitHub, the reverse-parent query must paginate `repos/<owner>/<repo>/issues?state=all&per_page=100` with `gh api --paginate`, exclude pull requests, and inspect every issue body. Search may narrow candidates, but cannot prove absence. Within a structured `## Parent` section, normalize a canonical issue URL, `owner/repo#<number>`, or same-repository `#<number>` to repository plus issue number before exact comparison. Mentions elsewhere in a body do not establish parentage. A zero-result native query does not establish that no children exist.

   Union and deduplicate every validated result, then fetch each ticket's full body, comments, status, acceptance criteria, and blockers. Use blocking relationships only to build dependencies; they do not establish parentage.
4. Define the work items only after every supported child-discovery source completes successfully. Use each child ticket when children exist. Use the parent spec itself as one work item with no blockers only when the exhaustive union is empty.
5. Build the work-item dependency graph. Reject missing tickets, cycles, unresolved blockers, ambiguous parentage, or incomplete child discovery until corrected.
6. Resolve `<tracker-user>` from the authenticated identity of the configured issue tracker. Use its native self-assignment or assignee field for the parent spec and every child ticket.
7. Discover the tracker's native lifecycle for the parent spec and every child ticket. Resolve **In Progress** and a `<review-handoff-state>` for each item. Choose the first native transition available from In Progress in this order: **Review** or **In Review**, **Testing** or **QA**, then **Done**. If native status transitions are unavailable, choose an existing lifecycle label in the same order; if only issue state exists, represent In Progress as open and `<review-handoff-state>` as closed. Remove conflicting lifecycle values during each transition. Use the tracker-native mechanism instead of inventing parallel labels.
8. Inspect the worktree. Preserve unrelated user work. Ask the user to isolate changes only when safe ticket commits and reviews cannot be separated from them.
9. Record the current branch as `<base-branch>` and its current `HEAD` as `<spec-base>`. Create `<spec-branch>` from that exact commit, named `spec/<spec-id>-<short-slug>`. If the name exists, reuse it only when its history and tracker references prove it belongs to this spec and starts from `<spec-base>`; otherwise stop for a safe branch name.
10. Define `<commit-contract>` for every implementation and remediation commit: apply `<terse-output-contract>` and use a commitlint-compatible Conventional Commit subject, `<type>(<scope>): <terse imperative summary>`. If `<spec-branch>` contains a Jira key matching `[A-Z][A-Z0-9]+-[0-9]+`, use that exact key as `<scope>` for every commit. Otherwise use a stable repository-relevant scope. Add a body only for required evidence, context, or links.
11. Assign the parent spec and every child ticket to `<tracker-user>`. Transition the parent spec to In Progress. Keep all implementation and review commits on `<spec-branch>`.

Pass this gate only when the parent spec, child-discovery sources queried and completed, validated and deduplicated exhaustive work-item set, dependency graph, tracker user, lifecycle mapping, base branch, spec base, checked-out spec branch, and commit contract are known. A child-discovery, assignment, or transition operation that fails is an external blocker; preserve state and stop before implementation.

## 4. Work the frontier

Work one ready work item at a time. A work item is a child ticket or, when no children exist, the parent spec itself. A work item is ready when every blocker is technically complete. Re-read tracker status before choosing each work item.

Define `<ponytail-contract>` once and include it verbatim in every implementation, `codebase-design`, remediation, `code-review`, `ponytail-review`, and final-review subagent prompt.

> Load and apply `ponytail` at `full` intensity. Treat the spec, acceptance criteria, repository standards, deep-module boundaries, approved testing seam, validation, error handling, security, and accessibility as binding constraints. Minimize the implementation within those constraints. Account for every changed file and new abstraction.

Define `<ponytail-review-gate>` once and include it verbatim in every work-item and final-review subagent prompt.

> Load and apply `ponytail-review` beside `code-review`. Treat every Ponytail finding as blocking. Pass only when Ponytail reports `Lean already. Ship.`

Define `<work-item-completion-gate>` once. It passes only when:

- every applicable requirement or acceptance criterion has evidence;
- `<validation-contract>` passes;
- all implementation and review-fix work is committed;
- Standards has no documented violation;
- Spec has no missing, partial, wrong, or scope-crept behavior;
- `<ponytail-review-gate>` passes;
- every changed file and new abstraction is necessary to satisfy a binding requirement or verified constraint;
- every smell is fixed or explicitly adjudicated.

Assign the selected work item to `<tracker-user>` and transition it to In Progress before implementation. The parent-spec work item is already In Progress.

### Full validation gate

Before implementation, discover the full validation workflow from repository instructions, root and workspace manifests, task runners, scripts, and CI configuration. Inventory every available validation command, including tests, linters, formatters, type checks, builds, and any other repository checks. Use aggregate commands only after verifying they cover the inventory; run uncovered commands separately. Include every workspace and configured validation variant. Keep deployment, publishing, and other operational scripts outside this validation inventory.

Define `<validation-contract>` from this inventory and the following requirements; include it verbatim in every implementation and remediation subagent prompt:

> Refresh the inventory whenever validation scripts or configuration change. Run the entire validation inventory after implementation and after every subsequent change batch, including review fixes, manual edits, formatter output, and generated-file changes. Focused checks during development supplement this gate. If a command changes files, inspect the changes and rerun the entire workflow on the resulting state until all commands pass without further source changes. Return the exact commands, results, and validated commit SHA. Failed, unavailable, or skipped checks leave the gate blocked; report the blocker and retry point.

The parent verifies this evidence after every implementation or remediation subagent returns and after any other changes, before starting the next review or work item. Any later change invalidates validation and affected review results: pass this gate, commit the changes, and repeat those reviews. Before publishing and declaring completion, require a passing full validation run for final `HEAD` with all changes accounted for.

### Implement

Record `<work-item-base>` as the current `HEAD`. Spawn a fresh implementation subagent with the full work item, parent spec reference, repository instructions, and this brief:

> Implement this work item. Load and apply `implement`, `codebase-design`, and `ponytail`. Apply `<ponytail-contract>` and `<commit-contract>`. Treat its acceptance criteria, when present, and the parent spec as binding. Preserve deep-module boundaries and use the agreed testing seam. Run focused checks during work and pass `<validation-contract>` before handoff. Create a distinct implementation commit for this work item on `<spec-branch>` and reference its tracker identifier in the commit body when it is not already the commit scope. Keep other work items out of that commit. Scope ends at committed implementation plus evidence; independent review belongs to another agent. Return commit SHA(s), requirement evidence, Ponytail accounting, validation commands with results, and blockers.

Wait for completion. Verify the distinct implementation commit exists on `<spec-branch>`, satisfies `<commit-contract>`, references the work item, contains no other work item, the reported checks passed, every applicable requirement or acceptance criterion has evidence, every changed file and new abstraction is necessary, and no unrelated changes entered the commit.

When exhaustive child discovery is empty and the parent spec is the work item, proceed directly to section 5. Use the whole-spec review loop as the parent's sole independent review gate.

### Review child tickets

For a child-ticket work item, spawn a different fresh review subagent with `<work-item-base>`, the work item, the parent spec, repository instructions, and this brief:

> Load and apply `code-review`, `ponytail`, and `ponytail-review`. Apply `<ponytail-contract>` and `<ponytail-review-gate>`. Review `<work-item-base>...HEAD`. Use the child ticket as the immediate spec and apply all inherited parent-spec decisions. Return Standards, Spec, and Ponytail separately, plus exact actionable findings and Ponytail accounting.

Treat documented-standard violations and missing, partial, wrong, or unrequested spec behavior as blocking. Evaluate each baseline smell; fix it or record a concrete reason it is acceptable.

For blocking findings, spawn a fresh implementation subagent using `implement`, `codebase-design`, and `ponytail`, limited to the findings and work item. Apply `<ponytail-contract>` and `<commit-contract>`, commit review fixes separately with the same tracker reference, pass `<validation-contract>`, then spawn a different fresh review subagent using `code-review`, `ponytail`, and `ponytail-review` against the same `<work-item-base>`. Repeat until the work-item review passes.

After `<work-item-completion-gate>` passes, mark the child ticket technically complete, transition it to its `<review-handoff-state>` with the native lifecycle mechanism, and keep `<tracker-user>` assigned. Refresh the dependency graph and take the next frontier child ticket. Continue until every child ticket is technically complete.

## 5. Review the whole spec

Spawn a fresh final review subagent with `<spec-base>`, the full parent spec, every child ticket when present, repository instructions, and this brief:

> Load and apply `code-review`, `ponytail`, and `ponytail-review`. Apply `<ponytail-contract>` and `<ponytail-review-gate>`. Review `<spec-base>...HEAD` against the parent spec and all child tickets when present. Return Standards, Spec, and Ponytail separately, plus exact actionable findings and Ponytail accounting. This is the whole-spec release gate.

For blocking findings, spawn a fresh implementation subagent using `implement`, `codebase-design`, and `ponytail`. Apply `<ponytail-contract>` and `<commit-contract>`, commit focused fixes separately with the parent spec reference, pass `<validation-contract>`, and spawn another fresh final review subagent using `code-review`, `ponytail`, and `ponytail-review` from `<spec-base>`. Repeat until Standards, Spec, and `<ponytail-review-gate>` pass.

When the parent spec is the sole work item, continue this remediation and final-review loop until `<work-item-completion-gate>` also passes. Mark the parent technically complete, retaining In Progress until pull-request creation.

## 6. Publish the draft pull request

After the whole-spec review passes:

1. Pass `<validation-contract>` at final `HEAD`. Verify every implementation and remediation commit satisfies `<commit-contract>`, every work item has its own implementation commit, and every remediation commit names its work item or parent spec.
2. Push `<spec-branch>` to the repository remote.
3. Create a draft pull request through the repository host's native mechanism with `<base-branch>` as base and `<spec-branch>` as head. Leave it in draft state.
4. Format the pull-request title with the subject format defined by `<commit-contract>`, summarizing the whole parent spec. Apply `<terse-output-contract>` to a short, structured body containing only the implementation summary, validation results, final review outcome, and native links to the parent spec and every child ticket. Add native issue or development relationships when the host supports them. Link every issue without relying only on prose titles.
5. After pull-request creation succeeds, transition the parent spec to its `<review-handoff-state>` and keep `<tracker-user>` assigned. Reconcile every child ticket to its `<review-handoff-state>` and assigned to `<tracker-user>`.

Keep the parent spec In Progress when branch push or pull-request creation fails. Preserve commits and report the exact retry point.

## 7. Complete

Declare completion only when all conditions hold:

- every work item is technically complete, including the parent-spec work item when no child tickets exist;
- the dependency graph has no unfinished node;
- the full validation workflow passes at final `HEAD`;
- the worktree contains no unaccounted changes;
- the whole-spec Standards and Spec reviews pass;
- every implementation and remediation commit is included after `<spec-base>` and satisfies `<commit-contract>`;
- every work item has a distinct implementation commit;
- the parent spec and every child ticket are assigned to `<tracker-user>` and transitioned to their `<review-handoff-state>` through the tracker-native lifecycle mechanism;
- the pull-request title satisfies the subject format defined by `<commit-contract>`, and the pull request remains a draft, targets `<base-branch>` from `<spec-branch>`, and links the parent spec plus every child ticket.

Apply `<terse-output-contract>` to the completion report. Include the parent spec, completed work items, tracker transitions, assignee, branch, commit range by work item, final validation results, final review outcome, and pull-request link. External blockers pause completion: preserve state, report the exact blocker, attempted remedies, and retry point, obtain the smallest needed input, then resume this process.
