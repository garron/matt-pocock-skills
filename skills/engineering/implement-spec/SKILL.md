---
name: implement-spec
description: "Implement the result of /to-spec and /to-tickets in code."
disable-model-invocation: true
---

You have been provided a spec. This spec should have tickets associated with it, describing how to implement the spec.

The issue tracker should have been provided to you. If not, tell the user to run `/setup-matt-pocock-skills`.

The goal is the entire spec implemented on a single **integration branch**, with every ticket resolved the way the issue tracker closes work.

The tickets are not a list of steps. They are a **task graph** with blocking relationships between them. This means there is always a **frontier** of tickets which are ready to be grabbed.

Communication to and from subagents should be sparse. Communicate primarily through **context pointers**: to the spec, tickets, research notes, and previous commits. Don't duplicate information already available via pointers.

**Implementer subagents** should run in the background where possible. Parallelize ready tickets only when their write surfaces (files and interfaces they change) are independent; serialize overlapping work.

Follow any project-local delivery procedure. Plan approval and intermediate wave reviews apply when requested by the user or required by the repository; a separate delivery policy is not a prerequisite. Scope intermediate reviews to the completed work so later tickets are not treated as missing implementation.

## Steps

1. Read the spec and tickets to understand the task graph. Record acceptance checklists for the spec and each ticket, and identify overlapping write surfaces before dispatch.

2. (optional) Use an **exploration subagent** to conduct any exploration required by the tickets - relevant codebase files or external documentation. Ensure the exploration subagent can save files - it should save its markdown notes in a directory outside the repo, accessible by all future subagents. This lets **implementer subagents** focus on implementation rather than exploration.

3. Create the integration branch. If the issue tracker closes work through PRs, or the user asks for one, open a draft PR after the first merge in step 5 (a branch with no commits ahead of main can't open one), marked as closing the spec and tickets.

4. Use **implementer subagents** to implement each ticket, each in its own worktree on its own branch. Each implementer subagent:
   - confirms its worktree is based on the integration branch before starting, and resets onto it if not;
   - calls the Skill tool with `tdd` to build the ticket;
   - merges the integration branch tip into its own branch and resolves conflicts in its worktree;
   - runs focused tests during development and repository-required verification after updating from the integration tip;
   - reports done only with committed, clean, verified work. Required checks that are skipped or unavailable remain verification gaps, not passing results.

5. Once an **implementer subagent** completes, merge its work to the integration branch with a **merger subagent**. Integrate only committed, clean, verified work; if the integration tip has advanced, update and reverify the ticket branch before landing it.

6. Compute the **frontier** from tickets already integrated, rather than waiting for tracker closure. Kick off more **implementer subagents** for newly ready tickets whose write surfaces are independent of work still running.

7. Once all tickets are integrated, run repository-required final verification and call the Skill tool with `code-review` on the integration branch against its fixed starting base. Address valid findings in a single **implementer subagent**, recording the disposition of each finding. After edits, rerun required verification and use a fresh reviewer subagent for one targeted re-review of the findings and affected behavior. If blocking findings or verification gaps remain, pause and report them with any PR still draft; do not start another broad review loop.

8. Confirm the spec and ticket acceptance checklists against the final result. If any acceptance criterion remains unmet, report the gap, keep any PR draft, and leave incomplete work open. Once acceptance, verification, and review pass, mark any draft PR ready for review; for branch-only delivery, resolve each ticket the way the issue tracker closes work and report the integration branch.

9. Clean up **implementer subagent** worktrees only after their commits are durably integrated. Preserve worktrees containing unintegrated work and report their paths.
