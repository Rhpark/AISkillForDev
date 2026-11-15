# SPEC Writing Guide

## Role

The SPEC turns the PRD (requirements) into a technical specification **while exploring the code directly**. It covers "how."
Both the PRD and SPEC are inputs passed to `make-plan`.

## Core Principle: Write Based on Code Exploration

- With the PRD at hand, write the SPEC while **reading the related code directly** (do not specify based on guesses).
- Attach **file:line evidence** you verified directly for facts about the current implementation, call relationships, and dependencies. Link planned design to the corresponding PRD requirement ID (for existing documents, clear section/item references), and write it separately from the current implementation.
- If the exploration scope is wide, you may delegate to an exploration subagent (when available), but cross-check the results by reading the key files directly.

## Branching When Issues Are Found While Writing

- Follow the "Branching Rules" in `.claude/skills/before-plan/SKILL.md`.
- Whenever requirements are touched, confirm with the user regardless of how certain you are.

## Completion Criteria

The SPEC is complete when every item in the SPEC artifact's `## 11. Completion Checklist` is checked `[x]` with evidence.

- For evidence, write one line: file:line for the current implementation, the PRD requirement for planned design, or the reason for "N/A". Do not check `[x]` based on guesses.
- Items with details follow the corresponding section of the section template. Do not arbitrarily add or remove checklist items in the artifact.
- Use the mapping table in section 9 to confirm that each PRD requirement is linked to the technical specification and to verification scenarios and pass criteria. Do not arbitrarily add or change requirements or success criteria on the grounds of this table.

## Section Template

```md
# SPEC

## 1. Technical Feasibility
- Feasibility review per requirement (whether a new API is needed, etc.)

## 2. Public API Definition
- New/changed externally exposed (non-private) signatures (functions, classes). "None" if none.
- Note the impact on callers and module boundaries (include UI bindings, etc. if applicable)

## 3. Changes/Additions
- {class/function/file} : file:line — change (for new files, path + (new))

## 4. Reuse
- {existing utility/class} : file:line — how it is reused

## 5. Behavior Specification (if applicable)
- Flow: user/call behavior flow
- Business rules: conditions and policies
- Error handling: error/exception cases and how they are handled
- State changes: transitions of stateful features (required if there is state)
- Data definitions: required data structures

## 6. Permissions / Logging (if applicable)
- Required permissions and where they are declared (manifest, configuration files, etc.)
- Logging points

## 7. Impact / Breaking Changes
- Code/module perspective (user/feature impact is in the PRD)

## 8. Environment and Tooling Constraints
- Impact of language/runtime/framework versions, build configuration, and related external tools

## 9. Verification Strategy
- Unit / Integration / E2E / manual verification targets and scenarios (check the criteria in project documents and build/test configuration; if not found, confirm with the user)

| PRD requirement | Location in technical spec | Verification scenario and pass criteria |
|---|---|---|
| REQ-01 (for existing documents, clear section/item reference) | Corresponding section/item | V-01: check procedure and pass criteria per the PRD success criteria |

## 10. Risks / Unconfirmed Items
- ...

## 11. Completion Checklist
> For each item, mark `[x]/[ ]` and write one line of evidence (current implementation: file:line / planned design: PRD requirement) or the reason for "N/A". Do not check `[x]` based on guesses.
- [ ] Each PRD requirement mapped to technical items — evidence:
- [ ] Public API defined (new/changed contracts and caller impact; "None" if none) — evidence:
- [ ] Changes/additions identified by file:line (new files marked path + (new)) — evidence:
- [ ] Reuse targets listed (file:line) — evidence:
- [ ] Behavior specified (flow/rules/errors/state/data, if applicable) — evidence:
- [ ] Permissions/logging (if applicable) — evidence:
- [ ] Impact (code/module)/breaking change assessed — evidence:
- [ ] Environment/toolchain constraints checked — evidence:
- [ ] Verification strategy (Unit/Integration/E2E/manual) — evidence:
- [ ] No unconfirmed items about the current implementation and no undecided items in the planned design — evidence:
```

## Do Not

- No over-engineering: do not add abstractions or extension points whose necessity is not explained by current requirements, contracts, or usage. Judge the need for inheritance restrictions (`sealed`/`final`, etc.), abstract classes, and interfaces based on responsibilities and contracts.
