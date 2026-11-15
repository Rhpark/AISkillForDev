---
name: make-plan
description: |
  Turns the PRD/SPEC into an implementation PLAN when the user explicitly requests make-plan or writing a PLAN from a PRD/SPEC.
  before-plan artifacts are the recommended input; without them, documents or content the user provides that correspond to a PRD/SPEC are used instead.
  Does not depend on any specific harness's PlanMode or SubAgent tools.
  Triggers: /make-plan, $make-plan, "write PLAN from PRD/SPEC".
---

# make-plan — Write a PLAN from PRD and SPEC

## Purpose and Applicability

This procedure applies only when the user explicitly requests make-plan (`/make-plan`, `$make-plan`) or writing a PLAN from a PRD/SPEC.

The inputs are the PRD (requirements: what and why) and SPEC (technical specification: how).
- **Recommended input**: the PRD/SPEC in `docs/agents/output/plan/{TS}/` that `before-plan` created in the same session or that the user designates
- **Substitute input**: if before-plan was not used, the parts of documents or conversation the user designates that correspond to a PRD/SPEC. The plan must be based on this content.

`before-plan` owns the creation and format of the PRD/SPEC. This skill may update the PRD/SPEC of the recommended input according to the Branching Rules (step 3),
but does not create a new PROMPT/PRD/SPEC on its own.

## Handling by Input Type

| Item | Recommended input | Substitute input |
|---|---|---|
| Questions and answers | Append to the PROMPT as `[during make-plan]` entries | Record in the PLAN "Decision Log" |
| Changes from answers, differences between documents and code | Fix the PRD/SPEC per the Branching Rules and also update the evidence in the section 11 checklist | Do not modify the user's documents; record in the PLAN "Decision Log" |
| Issues without answers | PLAN "Open Issues" | PLAN "Open Issues" |
| PLAN location | New PLANs go in the same `{TS}` folder | New PLANs go in a new `{TS}` folder created with the location and timestamp rules in `.claude/skills/before-plan/SKILL.md` (create only the PLAN) |

When fixing the PROMPT, PRD, or SPEC, follow the rules in `.claude/skills/before-plan/prompt_guide.md`, `.claude/skills/before-plan/prd_guide.md`, and `.claude/skills/before-plan/spec_guide.md`.

- When updating the PRD/SPEC of the **recommended input** or appending questions and answers to the PROMPT, if a PRD exists, update the status and unanswered question IDs in the PRD header per the "Completion Conditions and Awaiting Answers" rules in `.claude/skills/before-plan/SKILL.md`.
- Updating the status does not change how make-plan proceeds: follow this skill's procedures for incomplete input (step 1) and for proceeding while recording open issues (step 3).
- If parts of the **recommended input** are missing, you may record an explanation of the missing documents, the user's choice of how to proceed, the evidence checked, and how they were handled in the PLAN "Decision Log". Distinguish user decisions, verified facts, and model proposals, and state their sources. This explanation preserves the source and history of the existing input; do not use it as a substitute input for the PRD/SPEC or as grounds for turning undecided proposals into confirmed requirements. Record the original questions and answers in the PROMPT. Follow step 1 for deferred recording and restoration when the PROMPT is missing.

## Procedure

1. **Check the input**
   - If the user designates resuming or updating an existing PLAN, first read that PLAN and its stated inputs and open issues, and update it in that file and folder regardless of input type. Do not automatically treat a PLAN designated only as reference material as the one to resume, and keep the rule protecting the original substitute-input documents.
   - **Recommended input**: Check whether the PROMPT, PRD, and SPEC exist in the `{TS}` folder, and check the section 11 completion checklists of the PRD and SPEC that exist. If a PLAN exists in the same folder, also check its open issues.
     - If the PROMPT is missing, report the missing log and deferred recording, and explain the path to restoring it through before-plan in the same `{TS}` folder. If the user chooses restoration, stop make-plan and follow before-plan's "Checks on Resume" rules. Until restoration, defer further questions about requirements and design, and write only plans supported by the existing PRD/SPEC. Record the pending logging of questions and answers, and affected work, as PLAN open issues; after restoration, record the verifiable original questions and answers in the PROMPT.
     - If the checklists have incomplete items or the PRD/SPEC files are missing, treat before-plan as unfinished, report the list, and ask whether to finish before-plan or to proceed after a first-pass review.
       - If before-plan is to be finished, make-plan stops and before-plan continues in the same `{TS}` folder. Create or restore missing documents under before-plan's "Checks on Resume" rules.
       - If proceeding after a first-pass review, observe the question-deferral rule above when the PROMPT is missing, and fill in the missing items the same way as the substitute input below, but follow the recommended-input rules for recording and fixing. Do not create missing PRD/SPEC files.
     - If there is no PRD in which to incorporate newly confirmed or changed requirements, record the answers in the PROMPT, but leave completing the PRD and the affected steps and downstream steps as open issues. Do not finalize or implement steps based on those requirements; develop only the scope supported by the existing PRD/SPEC. If the PROMPT is also missing, follow the deferred-recording rule above.
     - If the PROMPT exists and only open issues remain in the PLAN, re-ask the questions the user has not yet answered. For SPEC-side issues, re-check the code first and ask only about what the code cannot settle.
   - **Substitute input**: Do a first-pass review of those documents or content. Compare them against the completion criteria in `.claude/skills/before-plan/prd_guide.md` and `.claude/skills/before-plan/spec_guide.md`, and share your understanding with the user or ask questions if needed. For content that needs the user's judgment, such as goals, scope, priorities, and success criteria, check existing answers and ask only about what is missing. Check facts about the current state and verifiable risks, and SPEC items, against code and configuration first in step 2, and ask only about what needs more information or a user choice. Do not treat the current implementation as the user's desired policy. Record unanswered items as PLAN open issues and proceed.
   - **Common**: If you do not know where the input is, do not search for the latest folder or repository documents on your own; ask the user. Do not re-ask questions the user has already answered.
2. **Gather evidence from code and documents** — Read the related code and build/test configuration, and, if the project has them, code structure/design documents and verification rule documents. Base facts about the current implementation on file:line or verified execution results, and link planned design to the corresponding PRD/SPEC items.
3. **Branching on open questions**
   - Determine the handling path with the "Branching Rules" in `.claude/skills/before-plan/SKILL.md`.
   - Follow the "Handling by Input Type" table for recording and fixing.
   - If there is no answer, record it in the PLAN open issues and continue, and attach the issue ID to implementation steps that depend on it, as in `(depends on open issue: ISSUE-01)`.
   - For each issue, record the needed check or decision, its source, and the affected step IDs. Check both the steps that depend on it directly and the follow-up steps that assume their results, and when the issue is resolved, update the related steps, completion conditions, and verification plan.
4. **Write the PLAN** — For a new PLAN, write `{TS}_PLAN.md` in the location given in the "Handling by Input Type" table. When updating an existing PLAN, read it first and rewrite it to fit the changed input. Write targets and order in the "Implementation Steps" format of the PLAN skeleton.
   - Keep existing completion marks only after checking actual implementation/verification evidence and the impact of changes. Mark affected items or items with insufficient evidence as needing re-review, and do not mark implementation or verification as complete because the plan was written.
5. **Check the verification plan** — Write the methods and pass criteria for the applicable layers among Unit → Integration → E2E → manual verification. If the project has build, type-check, or lint commands, also list them as prerequisite checks.
   - Starting point: for the recommended input, refine SPEC section 9 (Verification Strategy) as the starting point.
   - Source of criteria: check project documents and build/test configuration. For layers clearly not applicable given the type of project, present `N/A` with the reason, and confirm with the user only the remaining layers whose criteria you could not find.
   - Value per layer: a command and pass criteria, `N/A` with the reason, or, if automated verification applies but the user cannot decide on a command either, `Undefined` with the reason.
   - If the input has no success criteria and no answer is available, derive pass criteria from the input's goals and scope, prefix them with `(Proposed)`, and record them in the open issues.
6. **Present** — Present the PLAN and the open issues. For the recommended input, also report the PRD/SPEC updated this time; for the substitute input, also report the differences between the documents and the actual code recorded in the Decision Log.
7. **Implementation boundary** — Continue with implementation, within that scope, only if implementation was explicitly requested; otherwise end after presenting.
   - Even with open issues, proceed with work that can be done within existing requirements, verified evidence, and the delegated scope. Do parts that depend on undecided matters the user must decide or on missing required input evidence, and follow-up implementation that assumes their results, after the decision or evidence is secured. Make technical choices autonomously within existing requirements and the delegated scope, and do not ask for additional approval of answers or delegation already secured.

## PLAN Skeleton

Link each key step to its evidence, targets, and verifiable completion conditions. Write prerequisites and open-issue dependencies only when they actually exist; information already clear from the task description need not be repeated in separate fields.
For evidence, reference PRD requirement IDs and SPEC sections/items, or the document locations and user statement locations of the substitute input. For detailed SPEC design, reference its location.
Derive completion conditions from the input and the verified project verification criteria, and do not add new requirements or policies on your own. For proposals when there are no success criteria, follow step 5.

```md
# <TS> PLAN

## Input
- Recommended input: paths of the PRD/SPEC actually used (state missing documents as not written/missing) / Substitute input: paths of the source documents, or a summary of the conversation content used as the source

## Implementation Steps
- [ ] S1. Task description
      Evidence: the corresponding PRD/SPEC ID, section, or item, or the source location in the substitute input
      Targets: `file:line` for existing files, `path (new)` for new files
      Prerequisites: prerequisite step IDs (when there is a dependency)
      Completion condition: a verifiable result based on the input and project criteria
      Open-issue dependency: (depends on open issue: ISSUE-01) (if applicable)

## Acceptance Criteria and Verification
- [ ] Prerequisite checks: build, type-check, and lint commands and pass criteria (if the project has them), or the reason for N/A
- [ ] Unit: verification command and pass criteria, or the reason for N/A/Undefined
- [ ] Integration: verification command and pass criteria, or the reason for N/A/Undefined
- [ ] E2E: verification command and pass criteria, or the reason for N/A/Undefined
- [ ] Manual: check procedure and pass criteria (status values, logs, screens, etc.), or the reason for N/A

## Open Issues
- All issues remaining at make-plan time (including those carried over from input documents; risks in SPEC section 10 are excluded and only referenced). `None` if there are none
- ISSUE-01: open item / needed check or decision / source / affected step IDs

## Decision Log
- Substitute input: questions and answers during make-plan, differences between documents and the actual code, and what the PLAN decided differently from the documents as a result. Distinguish user decisions, verified facts, and model proposals, and state their sources.
- Recommended input: if the handling of a partial input needs explaining, follow the explanation recording rule in "Handling by Input Type". Record the original questions and answers in the PROMPT; if there is no separate explanation, `N/A (recorded in PROMPT)`.
```

## Do Not

- Do not treat the absence of a specific harness's `EnterPlanMode`/`ExitPlanMode`/SubAgent as a failure.
- Do not modify code while writing the plan.
