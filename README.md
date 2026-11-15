# AI Coding Agent Skill Set · AI 코딩 에이전트 스킬 세트

Skills for Claude Code and Codex that do not depend on a specific programming language, framework, or OS: a planning flow that turns agreed requirements into an implementation plan (`before-plan` → `make-plan`), and read-only review and Q&A skills.

Claude Code와 Codex에서 쓰는 스킬 모음입니다. 특정 프로그래밍 언어·프레임워크·OS에 묶이지 않으며, 요구사항을 합의해 구현 계획으로 구체화하는 흐름(`before-plan` → `make-plan`)과 읽기 전용 검토·질의 스킬로 구성됩니다.

| Language · 언어 | Folder · 폴더 | Documentation · 설명 |
|---|---|---|
| 한국어 | [KOR/](KOR/) | [KOR/README.kor.md](KOR/README.kor.md) |
| English | [ENG/](ENG/) | [ENG/README.eng.md](ENG/README.eng.md) |

## Usage · 사용

- Pick one language folder and copy the skill folders you need from its `.claude/skills/` (and `.agents/skills/` for Codex) into the same locations in your project. See the folder's README for details.
- 언어 폴더를 하나 골라, 그 안의 `.claude/skills/`(Codex는 `.agents/skills/`도)에서 필요한 스킬 폴더를 프로젝트의 같은 위치로 복사합니다. 자세한 내용은 언어 폴더의 README를 보세요.

## Maintaining this repository · 저장소 관리

- **Change both languages together after English is available.** Once the English version is available, update corresponding files in both language folders with equivalent meaning as part of the same change. Keep the file correspondence, section structure and order, identifiers (such as `REQ-01` and `ISSUE-01`), and path rules aligned. Titles, descriptions, status labels, and descriptive tags (such as `[make-plan 중]` / `[during make-plan]`) may be translated with equivalent meaning; keep their roles, conditions, exceptions, and obligation levels the same.
- **영어 버전 제공 이후 두 언어를 함께 고칩니다.** 영어 버전 제공 이후에는 두 언어의 대응 파일을 같은 변경에서 같은 의미로 수정합니다. 파일의 대응 관계·절 구조와 순서·식별자(`REQ-01`, `ISSUE-01` 등)·경로 규칙을 맞춥니다. 제목·설명·상태 표시·설명용 태그(`[make-plan 중]` / `[during make-plan]` 등)는 같은 의미로 번역할 수 있으며, 역할·조건·예외·의무 수준은 동일하게 유지합니다.
- Rules for editing skills within a language version are in that folder's README.
- 언어 폴더 안에서 스킬을 고칠 때의 규칙은 해당 폴더의 README를 따릅니다.
