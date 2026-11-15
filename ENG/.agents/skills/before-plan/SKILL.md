---
name: before-plan
description: |
  A skill that organizes requirements and the technical specification before implementation planning. Through live conversation with the user, it writes three documents, PROMPT/PRD/SPEC,
  to docs/agents/output/plan/{timestamp}/. Use only when the user explicitly requests before-plan or writing a PRD/SPEC.
  Triggers: /before-plan, "$before-plan", "write PRD/SPEC first".
---

# before-plan

Read `.claude/skills/before-plan/SKILL.md` at the project root in full and follow it as the common source.
When guiding the user to run make-plan, use the Codex form `$make-plan`.
