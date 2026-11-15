---
name: before-plan
description: |
  구현 계획 전에 요구사항과 기술 명세를 정리하는 스킬. 사용자와 실시간 대화로 PROMPT/PRD/SPEC 3개 문서를
  docs/agents/output/plan/{타임스탬프}/ 에 작성한다. 사용자가 before-plan 또는 PRD/SPEC 작성을 명시적으로 요청한 경우에만 사용한다.
  트리거: /before-plan, "$before-plan", "PRD/SPEC 먼저 작성".
---

# before-plan

프로젝트 루트의 `.claude/skills/before-plan/SKILL.md`를 완전히 읽고 공통 원본으로 따른다.
make-plan 실행을 안내할 때는 Codex 형식인 `$make-plan`을 쓴다.
