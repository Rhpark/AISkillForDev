---
name: before-plan
description: |
  A skill that organizes requirements and the technical specification before implementation planning. Through live conversation with the user, it writes three documents, PROMPT/PRD/SPEC,
  to docs/agents/output/plan/{timestamp}/. Use only when the user explicitly requests before-plan or writing a PRD/SPEC.
  Triggers: /before-plan, "$before-plan", "write PRD/SPEC first".
---

# before-plan — Write PROMPT, PRD, and SPEC

## Purpose

Create the PRD/SPEC in advance only when the user explicitly requests **before-plan** or writing a PRD/SPEC.
Use it for work whose requirements must be clearly agreed on; if the user explicitly requested it, proceed regardless of the size of the work.

This skill changes only the designated PROMPT/PRD/SPEC. Explore code and configuration read-only,
and handle implementation work within the scope of a separate user request.

## Artifacts (3)

| Document | Role                             | Editing                                     |
|--------|----------------------------------|-------------------------------------------|
| PROMPT | Record of the conversation with the user (Q&A log) | **append-only** — only append as is; no summarizing, reinterpreting, or deleting |
| PRD    | Refined requirements (success criteria, scope, constraints) | **editable** — body changes need a user statement as evidence (factual descriptions of the current state and risks may also cite verified file:line). Operational information is updated per the editing rules in the PRD guide |
| SPEC   | Technical specification (based on code exploration) | **editable** — technical edits are autonomous; the current implementation needs file:line and planned design needs PRD evidence |

- PROMPT is a record and is not used as the basis of the implementation plan. **Only the PRD/SPEC are inputs to `make-plan`**.

## Location / Naming

- Folder: `docs/agents/output/plan/{timestamp}/`
- Timestamp format: `yyMMdd_HHmmss` (e.g., `260604_121053`)
  - **Generate it only with a shell command** (no guessing or arbitrary input). E.g., PowerShell `Get-Date -Format "yyMMdd_HHmmss"`, bash `date +%y%m%d_%H%M%S`
  - Generate it once at the start of the skill and apply it consistently to the three files. If told to continue an existing `{TS}` folder, do not create a new one; read the existing PROMPT/PRD/SPEC in that folder and continue writing them
- File names: timestamp prefix — `{TS}_PROMPT.md`, `{TS}_PRD.md`, `{TS}_SPEC.md`
  - e.g., `260604_121053_PROMPT.md`

## Overall Flow

```
1. Generate the timestamp + create the folder (skip when continuing an existing {TS} folder)
2. Converse with the user → record the PROMPT (log) and PRD (refined) in parallel
   - Read-only preliminary exploration to confirm current behavior and constraints is allowed before and during PRD writing.
     Do not finalize or change the user's goals, scope, or success criteria based on exploration results alone.
   - Exit gate: the PRD is complete (all completion criteria in `.claude/skills/before-plan/prd_guide.md` met)
3. Write the SPEC while exploring the code directly based on the PRD
   - If errors or ambiguities are found during exploration, handle them per the "Branching Rules" below
4. When the completion conditions are met, save the final versions of the three documents (also save to file each time you update them while writing)
5. Tell the user to run `make-plan`
   - **Explicitly print** the created `docs/agents/output/plan/{TS}/` path and the PRD/SPEC file names.
   - Explain that this `{TS}` path is the input to `make-plan`, and that if the session grows long and context is lost, the user can pass this path as is.
```

## Question Principles

- Before asking, check the existing conversation, related documents, and code for the answer. Explore facts that the code can confirm first.
- Ask about what needs the user's judgment, such as goals, scope, priorities, and success criteria. Do not infer the user's intent from the existing implementation.
- Present questions a few at a time, grouped by relation, starting with those that block progress. When a choice is needed, present a recommendation and the differences between the options, but mark it as a proposal until answered.
- Re-ask an answered question only when its premise has changed or answers conflict, and state the reason.

## Branching Rules (when issues are found during SPEC writing or make-plan)

| Situation                                          | Handling                                                    | Note |
|----------------------------------------------|-------------------------------------------------------| --- |
| Only the SPEC's description of the current implementation differs from the actual code (PRD description still valid, no user question needed) | **Fix only the SPEC**                                  | No change to PROMPT/PRD |
| Facts written in the PRD's current state or risks differ from the code (requirements unchanged) | **Fix the PRD's factual description and the related SPEC** based on verified evidence | Keep the PROMPT original |
| A technical question that code and documents cannot resolve, asked to the user | Question → answer → append to PROMPT → **fix the SPEC** | No change to PRD |
| A question whose answer touches requirements                     | Question → answer (= evidence) → append to PROMPT + fix the PRD → fix the SPEC | The answer is the evidence |

- Follow the table above for the fix path. Distinguish requirement changes from factual corrections to the PRD's current state and risks.
- "No change to PRD" in the table means the requirements and current factual descriptions stay as they are. Operational information is updated per the PRD editing rules.
- PRD changes follow the editing rules in `.claude/skills/before-plan/prd_guide.md`.
- When the PRD/SPEC is changed, re-check the evidence for the affected technical items, verification strategy, and completion checklist. Revert items that no longer hold to `[ ]`.

## Completion Conditions and Awaiting Answers

Complete when all three are met:
- The PRD is complete (all completion criteria in `.claude/skills/before-plan/prd_guide.md` met)
- The SPEC is complete (all completion criteria in `.claude/skills/before-plan/spec_guide.md` met)
- No unanswered questions

Manage the progress status with the following rules.

- The before-plan status in the PRD header shows whether the current before-plan completion conditions are met, based on the documents in the same folder and the latest answers to questions.
- Record the overall before-plan status in the PRD header as `In progress` / `Awaiting answers` / `Ready`. Use `Ready` only when all three conditions above are met.
- If a needed answer is missing, record the question ID and its unanswered state in the PROMPT. Observe each stage's gate and continue work that does not depend on the answer; when you cannot proceed further, save the documents and report the `Awaiting answers` status, remaining questions, impact, and resume point.
- Awaiting answers is **incomplete**. Do not resolve unanswered questions arbitrarily or treat them as `N/A`, and do not repeat the same unanswered question.
- Do not present incomplete documents as finished make-plan input. If the user separately requests make-plan, follow that skill's rules for incomplete input.

## Checks on Resume

- Leave the last completed step, unanswered question IDs, and the code baseline together in the PRD header. For the code baseline, record the related commit and uncommitted change state when possible, or the key files and the time of the check if Git is not used.
- Read the documents and follow-up answers in the designated folder, and judge whether each question is unanswered based on its latest answer. If the existing documents lack resume information, fill it in to the extent verified.
- Create or restore missing artifacts in the designated folder within that same `{TS}` folder, following the writing and editing rules of this skill and each guide. When restoring a PROMPT, record only conversation text whose original wording can be verified, with its source; mark past requests, questions, and answers that cannot be verified as missing or unverified. Do not reconstruct past conversation by inference from requirements or code. Restoring an artifact alone does not satisfy the completion conditions.
- Check whether the related code has changed, and re-check the affected facts, file:line evidence, and checklist items. Do not change requirements because of code changes alone; follow the Branching Rules above for the fix path.

## Detailed Writing Guides (refer when needed)

When **actually writing** each document, read the corresponding guide and follow its rules.

- PROMPT: `.claude/skills/before-plan/prompt_guide.md`
- PRD: `.claude/skills/before-plan/prd_guide.md`
- SPEC: `.claude/skills/before-plan/spec_guide.md`
