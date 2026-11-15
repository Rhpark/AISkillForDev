---
name: make-plan
description: |
  사용자가 make-plan 또는 PRD/SPEC을 바탕으로 한 PLAN 작성을 명시적으로 요청했을 때 PRD/SPEC을 구현 PLAN으로 구체화한다.
  입력은 before-plan 산출물을 권장하며, 없으면 사용자가 알려준, PRD/SPEC에 해당하는 문서·내용으로 대신한다.
  특정 하네스의 PlanMode·SubAgent 도구에 의존하지 않는다.
  트리거: /make-plan, $make-plan, "PRD/SPEC으로 PLAN 작성".
---

# make-plan

프로젝트 루트의 `.claude/skills/make-plan/SKILL.md`를 완전히 읽고 공통 원본으로 따른다.
