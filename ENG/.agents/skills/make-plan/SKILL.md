---
name: make-plan
description: |
  Turns the PRD/SPEC into an implementation PLAN when the user explicitly requests make-plan or writing a PLAN from a PRD/SPEC.
  before-plan artifacts are the recommended input; without them, documents or content the user provides that correspond to a PRD/SPEC are used instead.
  Does not depend on any specific harness's PlanMode or SubAgent tools.
  Triggers: /make-plan, $make-plan, "write PLAN from PRD/SPEC".
---

# make-plan

Read `.claude/skills/make-plan/SKILL.md` at the project root in full and follow it as the common source.
