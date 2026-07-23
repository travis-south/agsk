---
name: ship-spec
description: Ship an approved spec on a dedicated branch through native tracker transitions, separate implementation commits, independent review gates, and a linked draft pull request.
disable-model-invocation: true
---

# Ship Spec

Drive one approved spec to completion. Keep the parent agent as orchestrator; give implementation and review to separate fresh subagents.

Define `<terse-output-contract>` once and apply it to every human-readable artifact and message created by this run: tracker comments, commit messages, pull-request text, blocker reports, and completion reports.

> Write concise, normal prose. State only the outcome, required evidence, and next action. Use short headings and bullets. Preserve required identifiers, links, test results, review outcomes, and blockers. Express context once at the narrowest useful level.

Keep internal evidence exhaustive. Terseness changes presentation, not gates or legwork.

## 1. Gate on the Engineering skill set

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

If any skill is missing, list the missing names and ask the user to run:

```bash
npx skills@latest add mattpocock/skills
```

Tell them to select every skill under **Engineering**, reload the harness, and invoke `$ship-spec` again. End the run here. Pass this gate only when the refreshed catalog union contains the full set.

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
7. Discover the tracker's native lifecycle. Prefer native status transitions equivalent to **In Progress** and **Done**. Otherwise use existing lifecycle labels; if only issue state exists, represent In Progress as open and Done as closed. Remove conflicting lifecycle values during each transition. Use the tracker-native mechanism instead of inventing parallel labels.
8. Inspect the worktree. Preserve unrelated user work. Ask the user to isolate changes only when safe ticket commits and reviews cannot be separated from them.
9. Record the current branch as `<base-branch>` and its current `HEAD` as `<spec-base>`. Create `<spec-branch>` from that exact commit, named `spec/<spec-id>-<short-slug>`. If the name exists, reuse it only when its history and tracker references prove it belongs to this spec and starts from `<spec-base>`; otherwise stop for a safe branch name.
10. Define `<commit-contract>` for every implementation and remediation commit: apply `<terse-output-contract>` and use a commitlint-compatible Conventional Commit subject, `<type>(<scope>): <terse imperative summary>`. If `<spec-branch>` contains a Jira key matching `[A-Z][A-Z0-9]+-[0-9]+`, use that exact key as `<scope>` for every commit. Otherwise use a stable repository-relevant scope. Add a body only for required evidence, context, or links.
11. Assign the parent spec and every child ticket to `<tracker-user>`. Transition the parent spec to In Progress. Keep all implementation and review commits on `<spec-branch>`.

Pass this gate only when the parent spec, child-discovery sources queried and completed, validated and deduplicated exhaustive work-item set, dependency graph, tracker user, lifecycle mapping, base branch, spec base, checked-out spec branch, and commit contract are known. A child-discovery, assignment, or transition operation that fails is an external blocker; preserve state and stop before implementation.

## 4. Work the frontier

Work one ready work item at a time. A work item is a child ticket or, when no children exist, the parent spec itself. A work item is ready when every blocker is complete. Re-read tracker status before choosing each work item.

Define `<minimal-change-contract>` once and include it verbatim in every implementation, `codebase-design`, `code-review`, remediation, and final-review subagent prompt. Implementation agents follow it; review agents treat any violation as blocking.

> Choose the simplest correct implementation with the smallest change surface. Reuse existing code, patterns, and seams. Introduce an abstraction, dependency, configuration, or generalized behavior only when a binding requirement or verified constraint requires it. Explain why every changed file and new abstraction is necessary to the work item.

Define `<simplicity-review-gate>` once and include it verbatim in every work-item and final-review subagent prompt. Review agents apply it as a blocking gate.

> Fail review when a smaller change surface can satisfy the same binding requirements, tests, and deep-module boundaries. Report the exact deletion, reuse, inlining, or collapse. Pass only when no such simplification remains.

Assign the selected work item to `<tracker-user>` and transition it to In Progress before implementation. The parent-spec work item is already In Progress.

### Implement

Record `<work-item-base>` as the current `HEAD`. Spawn a fresh implementation subagent with the full work item, parent spec reference, repository instructions, and this brief:

> Implement this work item. Load and apply `implement` and `codebase-design`. Apply `<minimal-change-contract>` and `<commit-contract>`. Treat its acceptance criteria, when present, and the parent spec as binding. Preserve deep-module boundaries and use the agreed testing seam. Run focused checks during work and the full required suite at completion. Create a distinct implementation commit for this work item on `<spec-branch>` and reference its tracker identifier in the commit body when it is not already the commit scope. Keep other work items out of that commit. Scope ends at committed implementation plus evidence; independent review belongs to another agent. Return commit SHA(s), requirement evidence, minimal-change accounting, tests run with results, and blockers.

Wait for completion. Verify the distinct implementation commit exists on `<spec-branch>`, satisfies `<commit-contract>`, references the work item, contains no other work item, the reported checks passed, every applicable requirement or acceptance criterion has evidence, every changed file and new abstraction is necessary, and no unrelated changes entered the commit.

### Review

Spawn a different fresh review subagent with `<work-item-base>`, the work item, the parent spec, repository instructions, and this brief:

> Load and apply `code-review`. Apply `<minimal-change-contract>` and `<simplicity-review-gate>`. Review `<work-item-base>...HEAD`. Use the child ticket as the immediate spec when present; otherwise use the parent spec. Apply all inherited parent-spec decisions. Return the Standards and Spec reports separately, plus the exact actionable findings and minimal-change accounting.

Treat documented-standard violations and missing, partial, wrong, or unrequested spec behavior as blocking. Evaluate each baseline smell; fix it or record a concrete reason it is acceptable.

For blocking findings, spawn a fresh implementation subagent using `implement` and `codebase-design`, limited to the findings and work item. Apply `<commit-contract>` and commit review fixes separately with the same tracker reference, then spawn a different fresh `code-review` subagent against the same `<work-item-base>`. Repeat until the work-item review passes.

Mark the work item complete only after:

- every applicable requirement or acceptance criterion has evidence;
- required tests pass;
- all implementation and review-fix work is committed;
- Standards has no documented violation;
- Spec has no missing, partial, wrong, or scope-crept behavior;
- `<simplicity-review-gate>` passes;
- every changed file and new abstraction is necessary to satisfy a binding requirement or verified constraint;
- every smell is fixed or explicitly adjudicated.

For a child ticket, transition it to Done with the native lifecycle mechanism and keep `<tracker-user>` assigned. For the parent-spec work item, retain the passing implementation and review evidence while the parent stays In Progress until pull-request creation. Refresh the dependency graph and take the next frontier work item. Continue until every work item is complete.

## 5. Review the whole spec

Spawn a fresh final review subagent with `<spec-base>`, the full parent spec, every child ticket when present, repository instructions, and this brief:

> Load and apply `code-review`. Apply `<minimal-change-contract>` and `<simplicity-review-gate>`. Review `<spec-base>...HEAD` against the parent spec and all child tickets when present. Return Standards and Spec separately, plus exact actionable findings and minimal-change accounting. This is the whole-spec release gate.

For blocking findings, spawn a fresh implementation subagent using `implement` and `codebase-design`. Apply `<commit-contract>`, commit focused fixes separately with the parent spec reference, run the full required suite, and spawn another fresh final `code-review` subagent from `<spec-base>`. Repeat until Standards, Spec, and `<simplicity-review-gate>` pass.

## 6. Publish the draft pull request

After the whole-spec review passes:

1. Verify every implementation and remediation commit satisfies `<commit-contract>`, every work item has its own implementation commit, and every remediation commit names its work item or parent spec.
2. Push `<spec-branch>` to the repository remote.
3. Create a draft pull request through the repository host's native mechanism with `<base-branch>` as base and `<spec-branch>` as head. Leave it in draft state.
4. Use the parent spec title for the pull-request title. Apply `<terse-output-contract>` to a short, structured body containing only the implementation summary, test results, final review outcome, and native links to the parent spec and every child ticket. Add native issue or development relationships when the host supports them. Link every issue without relying only on prose titles.
5. After pull-request creation succeeds, transition the parent spec to Done and keep `<tracker-user>` assigned. Reconcile every child ticket to Done and assigned to `<tracker-user>`.

Keep the parent spec In Progress when branch push or pull-request creation fails. Preserve commits and report the exact retry point.

## 7. Complete

Declare completion only when all conditions hold:

- every work item is complete, including the parent-spec work item when no child tickets exist;
- the dependency graph has no unfinished node;
- the full required test suite passes at final `HEAD`;
- the worktree contains no unaccounted changes;
- the whole-spec Standards and Spec reviews pass;
- every implementation and remediation commit is included after `<spec-base>` and satisfies `<commit-contract>`;
- every work item has a distinct implementation commit;
- the parent spec and every child ticket are assigned to `<tracker-user>` and transitioned to Done through native tracker state;
- the pull request remains a draft, targets `<base-branch>` from `<spec-branch>`, and links the parent spec plus every child ticket.

Apply `<terse-output-contract>` to the completion report. Include the parent spec, completed work items, tracker transitions, assignee, branch, commit range by work item, final tests, final review outcome, and pull-request link. External blockers pause completion: preserve state, report the exact blocker, attempted remedies, and retry point, obtain the smallest needed input, then resume this process.
