[← 저장소 안내](../README.md)

# AI 코딩 에이전트 스킬 세트 (한국어)

Claude Code와 Codex에서 쓰는 스킬 모음의 한국어 버전입니다. 특정 프로그래밍 언어·프레임워크·OS에 묶이지 않도록 작성했으며, 두 묶음으로 구성됩니다.

- **계획 (before-plan → make-plan)**: 요구사항을 사용자와 합의해 PRD/SPEC을 만들고(`before-plan`), 이를 구현 계획(PLAN)으로 구체화합니다(`make-plan`).
- **검토·질의**: 코드·문서를 읽기 전용으로 검토하거나(`*-review`, `docs-*`) 근거를 확인해 질문에 답하고(`q`), 근거와 개선 방향을 보고합니다.

## 폴더 구조

```
.claude/skills/<name>/SKILL.md            # 스킬 원본. Claude Code가 읽는다
.claude/skills/before-plan/*_guide.md     # PROMPT·PRD·SPEC 작성 가이드
.agents/skills/<name>/SKILL.md            # Codex 래퍼(원본을 가리킴)
.agents/skills/<name>/agents/openai.yaml  # Codex 설정(표시 정보·자동 선택 정책)
```

## 스킬 목록

| 스킬 | 용도 | 모델의 자동 선택 | 파일 변경 |
|---|---|---|---|
| [before-plan](.claude/skills/before-plan/SKILL.md) | 계획 전에 사용자와 대화하며 PROMPT·PRD·SPEC 작성 | 가능 | 산출물 3종만. 코드는 읽기만 |
| [make-plan](.claude/skills/make-plan/SKILL.md) | PRD·SPEC을 구현 PLAN으로 구체화 | 가능 | PLAN 작성, 권장 입력이면 PROMPT·PRD·SPEC 갱신. 구현은 명시 요청 시에만 |
| [q](.claude/skills/q/SKILL.md) | 근거를 확인해 현재 작업 프로젝트에 관한 질문에 답변 | 불가 | 없음 |
| [basic-code-review](.claude/skills/basic-code-review/SKILL.md) | SOLID·YAGNI·인터페이스 설계 검토 | 불가 | 없음 |
| [concurrency-lifecycle-review](.claude/skills/concurrency-lifecycle-review/SKILL.md) | 동시성·작업 종료·자원 수명 검토 | 불가 | 없음 |
| [docs-clarity](.claude/skills/docs-clarity/SKILL.md) | 문서의 모호한 표현·AI가 오해할 여지 검토 | 불가 | 없음 |
| [docs-consistency](.claude/skills/docs-consistency/SKILL.md) | 문서 간 설명·규칙·상태·참조 불일치 검토 | 불가 | 없음 |
| [docs-duplication](.claude/skills/docs-duplication/SKILL.md) | 문서 내부·문서 간 중복 검토 | 불가 | 없음 |

- "모델의 자동 선택"이 "가능"인 스킬은 `/스킬명` 또는 `$스킬명`을 쓰지 않아도 요청 내용에 따라 선택될 수 있습니다. 이때도 각 스킬이 정한 명시 요청 조건을 충족해야 합니다. "불가" 스킬은 `/스킬명`(Codex는 `$스킬명`)으로 직접 호출합니다.
- `q`와 `basic-code-review`는 빌드·테스트를 실행하지 않습니다. `concurrency-lifecycle-review`는 실행의 부수 효과를 먼저 확인하고, 파일이나 DB·외부 서비스 상태를 바꿀 수 있거나 그 여부를 확인하지 못한 실행은 승인 요청 없이 제외합니다. 이 제한에 해당하지 않는 실행에 대해서만 사전에 사용자에게 묻습니다.

## 설치와 사용

1. 이 폴더의 `.claude/skills/`와 `.agents/skills/`에서 필요한 스킬 폴더를 사용할 프로젝트의 같은 위치로 복사합니다. 기존 설정·스킬은 그대로 둡니다.
   - 스킬마다 `.claude/skills/<name>/`을 복사하고, Codex에서도 쓰려면 같은 이름의 `.agents/skills/<name>/`도 함께 복사합니다.
   - `before-plan`과 `make-plan`은 서로 참조하므로 함께 복사합니다.
2. Claude Code에서는 `/<스킬명>`, Codex에서는 `$<스킬명>`으로 호출하고, 대상은 뒤에 적습니다.

Claude Code 예시:

```text
/before-plan 결제 취소 기능
/before-plan docs/agents/output/plan/260604_121053/ 이어서 진행
/make-plan docs/agents/output/plan/260604_121053/
/concurrency-lifecycle-review src/worker/
```

Codex 예시:

```text
$make-plan docs/agents/output/plan/260604_121053/
$make-plan docs/agents/output/plan/260604_121053/260604_121053_PLAN.md 이어서 갱신
$docs-consistency README.md docs/architecture.md
```

예시의 경로는 실제 검토 대상이나 생성된 산출물 경로로 바꿉니다.

스킬 안의 경로는 설치한 프로젝트의 루트 기준(`.claude/skills/...`)입니다.

- **Claude Code**: 다른 스킬 파일을 참조하는 `before-plan`·`make-plan`은 프로젝트에 설치합니다. 다른 스킬 파일을 참조하지 않는 검토·질의 원본(`q`, `*-review`, `docs-*`)은 사용자 전역 위치(`~/.claude/skills/`)에도 그대로 설치할 수 있습니다.
- **Codex**: 검토·질의 스킬을 포함한 모든 래퍼가 프로젝트의 `.claude/skills/...` 원본을 참조합니다. 현재 구성에서는 원본과 래퍼를 함께 프로젝트에 설치하는 위 절차를 따릅니다. 프로젝트에 원본이 없으면 래퍼만 전역에 복사해도 참조가 해결되지 않습니다.

## before-plan → make-plan 흐름

요구사항 합의가 필요한 작업에 쓰며, 사용자가 명시적으로 요청하면 작업 규모와 관계없이 진행합니다. `/before-plan`, `/make-plan` 외에 "PRD/SPEC 먼저 작성", "PRD/SPEC으로 PLAN 작성" 같은 요청으로도 호출될 수 있습니다.

```
before-plan                                make-plan
대화 → PROMPT·PRD → PRD 게이트 → SPEC  ──▶  입력 확인 → 근거 수집 → PLAN 작성 → 검증 계획 → 제시
```

before-plan이 끝나면 산출물 경로를 안내하며, make-plan은 사용자가 그 경로로 따로 실행합니다.

### before-plan

산출물은 `docs/agents/output/plan/{TS}/`에 저장합니다(`{TS}`는 셸 명령으로 만든 `yyMMdd_HHmmss`).

| 문서 | 역할 | 편집 방식 |
|---|---|---|
| `{TS}_PROMPT.md` | 사용자와의 대화 기록. 질문 ID(`Q1`…) 부여 | 덧붙이기만(append-only) |
| `{TS}_PRD.md` | 요구사항 정제본("무엇을·왜"). 요구사항 ID(`REQ-01`…) 부여 | 본문은 사용자 발언이 근거(현재 상태·리스크의 사실은 `파일:라인`도 가능) |
| `{TS}_SPEC.md` | 코드 탐색 기반 기술 명세("어떻게"). 요구사항별 검증 항목(`V-01`…) | 현행 구현은 `파일:라인`, 변경 예정 설계는 PRD 요구사항 근거 |

- PRD의 11절 완료 체크리스트 항목을 모두 근거와 함께 `[x]`로 체크한 뒤에 SPEC 작성으로 넘어갑니다(PRD 게이트).
- 완료 조건: PRD·SPEC 산출물의 11절 완료 체크리스트(형식은 각 가이드의 템플릿) 항목을 모두 근거와 함께 `[x]`로 체크하고, 미답 질문이 없을 것.
- PRD 머리말에 진행 상태(`진행 중` / `답변 대기` / `준비 완료`)와 재개 정보를 기록합니다. 답변을 기다리는 동안 문서를 저장해 두고, 같은 `{TS}` 폴더를 지정하면 이어서 진행합니다. 누락 산출물은 before-plan의 재개 규칙으로 보완하며, PROMPT의 과거 대화는 확인 가능한 원문만 복구하고 확인할 수 없는 내용은 누락·미확인으로 남깁니다.
- 작성 중 코드와 문서가 다르거나 질문이 생기면 [before-plan/SKILL.md](.claude/skills/before-plan/SKILL.md)의 "분기 기준"에 따라 SPEC·PRD·PROMPT 중 어디를 고칠지 정합니다.

### make-plan

| 입력 | 설명 |
|---|---|
| 권장 입력 | before-plan이 만든 `{TS}` 폴더의 PRD·SPEC |
| 대체 입력 | before-plan을 거치지 않은 경우, 사용자가 지정한 문서·대화 내용 중 PRD/SPEC에 해당하는 내용. 사용자 문서는 수정하지 않음 |

| 상황 | PLAN 저장 방식 |
|---|---|
| 권장 입력으로 신규 작성 | 입력 PRD/SPEC과 같은 `{TS}` 폴더에 `{TS}_PLAN.md` 작성 |
| 대체 입력으로 신규 작성 | before-plan의 저장·타임스탬프 규칙에 따른 새 `{TS}` 폴더에 `{TS}_PLAN.md`만 작성 |
| 기존 PLAN의 재개·갱신을 지정 | 입력 유형과 관계없이 지정한 기존 파일과 폴더에서 갱신. 참고자료로만 지정한 PLAN은 재개 대상으로 자동 간주하지 않음 |

- 대체 입력은 먼저 1차 검토합니다. 권장 입력의 PRD/SPEC이 누락되거나 완료 체크리스트가 미완료이면 그 사실을 알리고, before-plan을 마저 진행할지 1차 검토 후 진행할지 사용자에게 묻습니다. 1차 검토에서는 빠진 항목을 코드로 먼저 확인하고, 목표·범위·성공 조건처럼 사용자 판단이 필요한 것만 묻습니다. 답이 없는 항목은 PLAN의 미해결 이슈로 남기고 진행합니다.
- 권장 입력의 PROMPT가 없으면 복구 경로를 안내하고, 복구 전에는 요구사항·설계에 관한 추가 질의를 보류합니다. 새 요구사항을 반영할 PRD가 없으면 해당 요구사항에 영향을 받는 단계와 후속 단계를 미해결로 남깁니다. 두 경우 모두 기존 PRD/SPEC으로 근거를 확인할 수 있는 계획은 계속 작성할 수 있습니다. 누락 문서의 작성·복구는 같은 `{TS}` 폴더에서 before-plan이 맡습니다.
- 질문·답변은 권장 입력이면 PROMPT에, 대체 입력이면 PLAN의 결정 기록에 남깁니다. 추천안과 사용자의 결정을 구분하며, 추천이나 미응답을 사용자의 선택으로 기록하지 않습니다. PROMPT 누락으로 보류된 기록은 미기록 상태를 미해결 이슈로 표시하고, 복구 후 확인 가능한 원문을 기록합니다.
- PLAN에는 단계 ID(`S1`…)별 근거·대상(기존 파일은 `파일:라인`, 신규 파일은 `경로 (신규)`)·완료 조건, 수용기준·검증, 미해결 이슈(`ISSUE-01`…), 결정 기록을 적습니다.
- 검증 계획에는 Unit·Integration·E2E·수동 중 적용 계층의 방법·통과 기준을 적고, 프로젝트에 빌드·타입 검사·린트 명령이 있으면 선행 검사로 포함합니다. 비적용 계층은 `해당 없음`과 사유를, 자동 검증이 적용되지만 사용자도 명령을 정하지 못한 계층은 `미정의`와 사유를 적습니다. 계획 작성만으로 구현·검증을 완료 표시하지 않습니다.
- 구현은 사용자가 명시적으로 요청한 경우에만 그 범위에서 진행합니다.
- 구현 요청이 있으면 미해결 이슈가 있어도 기존 요구사항·확인한 근거·위임 범위에서 수행 가능한 작업은 진행합니다. 다만 사용자 결정이 필요한 사항이나 필요한 입력 근거의 누락에 의존하는 구현과 그 결과를 전제로 하는 후속 구현은 해당 결정·근거가 확보된 뒤 수행합니다. 기술 선택은 기존 요구사항과 위임 범위에서 자율적으로 결정하며, 이미 확보한 답변·위임에 추가 승인을 요구하지 않습니다.

## 공통 원칙

- 사용자가 명시적으로 요청할 때만 동작합니다.
- 근거는 출처에 맞게 제시합니다. 코드·문서 검토의 근거 위치는 `파일:라인`, 요구사항·설계의 근거는 사용자 발언이나 PRD/SPEC·대체 입력의 해당 ID·절·항목, 외부 자료는 링크로 제시합니다. 실행 결과의 인용은 각 스킬이 허용한 조건을 따릅니다. 확인한 사실·추론·추정·미확인을 구분합니다.
- 검토·질의 스킬은 결과만 보고합니다. 수정은 결과를 본 뒤 사용자가 요청하면 별도로 진행합니다.

## 수정할 때 지킬 규칙

- **이 폴더 안에서 `.claude/skills/`가 원본입니다.** `.agents` 래퍼의 frontmatter(`name`, `description`)는 원본에서 그대로 복사하고, 본문은 원본을 가리키는 한 줄과, 필요하면 Codex 전용 안내(예: `before-plan`의 `$make-plan` 안내)만 둡니다.
- **모델의 자동 선택 설정은 짝을 맞춥니다.** "불가" 스킬은 원본에 `disable-model-invocation: true`, 래퍼 `openai.yaml`에 `allow_implicit_invocation: false`를 둡니다.
- **같은 규칙을 공유하는 곳은 함께 고칩니다.** `basic-code-review`와 `concurrency-lifecycle-review`의 검토 대상·비교 기준·비교 불가 시 처리 규칙(각 SKILL.md 본문 앞의 세 항목).
- **스킬 파일 안에서는 아래 규칙마다 정본을 한 곳에만 두고**, 다른 스킬 파일은 참조만 합니다. 이 README는 요약이며, 스킬 파일과 다르면 스킬 파일을 따릅니다.

  | 규칙 | 정본 |
  |---|---|
  | 문제 발견 시 수정 경로(분기 기준) | [before-plan/SKILL.md](.claude/skills/before-plan/SKILL.md) |
  | PRD 편집 규칙 | [before-plan/prd_guide.md](.claude/skills/before-plan/prd_guide.md) |
  | PRD·SPEC 완료 기준 | [before-plan/prd_guide.md](.claude/skills/before-plan/prd_guide.md)·[spec_guide.md](.claude/skills/before-plan/spec_guide.md)의 완료 기준과 템플릿 11절 완료 체크리스트 |
  | PLAN 결정 기록에 남길 내용 | [make-plan/SKILL.md](.claude/skills/make-plan/SKILL.md)의 PLAN 골격("결정 기록")과 "입력별 처리"의 설명 기록 규칙 |

- 고친 뒤에는 `/docs-clarity`, `/docs-consistency`, `/docs-duplication`으로 해당 스킬을 검토합니다. 다른 언어 폴더와 함께 고치는 규칙은 [저장소 안내](../README.md)를 따릅니다.

## 현재 상태와 한계

- 실제 작업에 적용한 검증은 아직 진행하지 않았습니다.
- Codex에서 여러 줄 `description`(YAML `|`)이 스킬 목록에 정상 표시되는지는 확인이 필요합니다.
- 신규 산출물 경로(`docs/agents/output/plan/`)는 설정으로 바꿀 수 없습니다.
