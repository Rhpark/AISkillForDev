# PRD Writing Guide

## Role

The PRD is the **refined requirements** derived from the PROMPT (conversation log). It covers "what and why."
It is **written in parallel** with the PROMPT (updated as the conversation progresses) and is an input document passed to `make-plan`.

## Editing Rules

- It is an editable refinement (unlike the PROMPT, existing content can be rewritten).
- **Changes to the PRD body are based on user statements.** However, factual descriptions of the current state and risks may also be updated based on file:line you verified directly.
  - Reflecting what the user said (whether volunteered or in answer to your question) is done **freely**.
    That statement is itself the evidence and the approval; there is no separate approval step.
  - If requirements must change without a user statement (e.g., something found during code exploration), **ask first**, get an answer, and reflect it based on that answer.
  - Do not add, reduce, or change requirements on your own (without a user statement).
- Update operational information (the progress status, resume information, requirement IDs, checklist marks, and evidence references) based on verified facts and existing document content. Do not add, reduce, or change the meaning, scope, conditions, or exceptions of requirements on the grounds of such updates.

## Requirement Tracking

- Give each individual requirement in the target state, scope, constraints, and success criteria a unique ID such as `REQ-01` and its evidence (the initial request or a PROMPT question ID). When the same requirement is mentioned in another section, reference the same ID.
- Assigning IDs is notation for tracking. Keep the original conditions and exceptions, and do not reinterpret requirements while splitting or merging sentences. Do not reuse an existing ID for another requirement.
- If an existing document has no IDs, clear section/item references may be kept. Assign IDs without changing content, starting with the requirements being updated, and do not modify past PROMPT entries.

## Completion Criteria (exit gate)

When every item in the PRD artifact's `## 11. Completion Checklist` is checked `[x]` with evidence, proceed to the next step (SPEC writing and detailed code exploration).
Preliminary exploration before and during PRD writing is limited to confirming facts about current behavior and constraints, and does not replace finalizing requirements or planned design.

- For evidence, write one line with a user statement/PROMPT location (for the current state and risks, verified file:line is also allowed) or the reason for "N/A". Do not check `[x]` based on guesses.
- Each item's content follows the section with the same number in the section template. Do not arbitrarily add or remove checklist items in the artifact.

## Section Template

```md
# PRD

- before-plan status: In progress
- Resume info: last completed step / unanswered question IDs (none if none) / code baseline

## 1. Overview
- What is being changed

## 2. Background
- Why this change is needed

## 3. Current State
- How it behaves now

## 4. Target State
- REQ-01: What state it should be in after the change — evidence: initial request or PROMPT question ID

## 5. Scope
- In scope: ...
- Out of scope: ...

## 6. Impact (user/feature perspective)
- Which user flows/features are affected  (code/module impact is covered in the SPEC)

## 7. Constraints
- Environment, versions, compatibility, prohibitions

## 8. Success Criteria
- For each requirement ID (for existing documents, clear section/item references), what state counts as done (verifiable)

## 9. Risks
- Expected risks

## 10. Open Issues
- Remaining questions/pending decisions (if any, the PRD is incomplete)

## 11. Completion Checklist
> For each item, mark `[x]/[ ]` and write one line of evidence (user statement/PROMPT location; for the current state and risks, file:line is also allowed) or the reason for "N/A". No `[x]` without evidence.
- [ ] Overview — evidence:
- [ ] Background — evidence:
- [ ] Current state — evidence:
- [ ] Target state — evidence:
- [ ] Scope (in/out of scope) — evidence:
- [ ] Impact (user/feature) — evidence:
- [ ] Constraints — evidence:
- [ ] Success criteria (verifiable) — evidence:
- [ ] Risks — evidence:
- [ ] No open issues (if any, list them in section 10; PRD incomplete) — evidence:
```

## Do Not

- Do not write technical implementation methods (how) in the PRD → that belongs in the SPEC.
- Do not write guesses as confirmed requirements (if ambiguous, ask and record it in the PROMPT).
