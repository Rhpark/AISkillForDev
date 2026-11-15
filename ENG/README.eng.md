[← Repository guide](../README.md)

# AI Coding Agent Skill Set (English)

The English version of a skill set for Claude Code and Codex. It is written so as not to be tied to a specific programming language, framework, or OS, and consists of two groups.

- **Planning (before-plan → make-plan)**: Agree on requirements with the user to create a PRD/SPEC (`before-plan`), then turn them into an implementation plan (PLAN) (`make-plan`).
- **Review and Q&A**: Review code and documents read-only (`*-review`, `docs-*`) or answer questions after checking evidence (`q`), reporting evidence and improvement directions.

## Folder Structure

```
.claude/skills/<name>/SKILL.md            # Skill source. Read by Claude Code
.claude/skills/before-plan/*_guide.md     # PROMPT, PRD, and SPEC writing guides
.agents/skills/<name>/SKILL.md            # Codex wrapper (points to the source)
.agents/skills/<name>/agents/openai.yaml  # Codex settings (display info, auto-selection policy)
```

## Skill List

| Skill | Purpose | Model auto-selection | File changes |
|---|---|---|---|
| [before-plan](.claude/skills/before-plan/SKILL.md) | Write PROMPT, PRD, and SPEC through conversation with the user before planning | Yes | Only the 3 artifacts. Code is read-only |
| [make-plan](.claude/skills/make-plan/SKILL.md) | Turn PRD and SPEC into an implementation PLAN | Yes | Writes the PLAN; updates PROMPT, PRD, and SPEC for the recommended input. Implementation only on explicit request |
| [q](.claude/skills/q/SKILL.md) | Answer questions about the project currently being worked on after checking evidence | No | None |
| [basic-code-review](.claude/skills/basic-code-review/SKILL.md) | Review SOLID, YAGNI, and interface design | No | None |
| [concurrency-lifecycle-review](.claude/skills/concurrency-lifecycle-review/SKILL.md) | Review concurrency, task termination, and resource lifetime | No | None |
| [docs-clarity](.claude/skills/docs-clarity/SKILL.md) | Review ambiguous wording in documents and room for AI misinterpretation | No | None |
| [docs-consistency](.claude/skills/docs-consistency/SKILL.md) | Review inconsistencies in explanations, rules, states, and references across documents | No | None |
| [docs-duplication](.claude/skills/docs-duplication/SKILL.md) | Review duplication within and across documents | No | None |

- Skills with "Yes" for "Model auto-selection" may be selected based on the request even without `/skill-name` or `$skill-name`. Even then, the explicit-request conditions set by each skill must be met. "No" skills are invoked directly with `/skill-name` (`$skill-name` in Codex).
- `q` and `basic-code-review` do not run builds or tests. `concurrency-lifecycle-review` checks execution side effects first and rules out, without requesting approval, runs that could change files, a DB, or external-service state, or whose potential for those effects cannot be verified. It asks the user in advance only for runs that do not fall under this restriction.

## Installation and Usage

1. Copy the skill folders you need from this folder's `.claude/skills/` and `.agents/skills/` into the same locations in the project where you will use them. Leave existing settings and skills as they are.
   - For each skill, copy `.claude/skills/<name>/`, and to use it in Codex as well, also copy `.agents/skills/<name>/` with the same name.
   - `before-plan` and `make-plan` reference each other, so copy them together.
2. Invoke a skill with `/<skill-name>` in Claude Code or `$<skill-name>` in Codex, followed by the target.

Claude Code examples:

```text
/before-plan payment cancellation feature
/before-plan docs/agents/output/plan/260604_121053/ continue
/make-plan docs/agents/output/plan/260604_121053/
/concurrency-lifecycle-review src/worker/
```

Codex examples:

```text
$make-plan docs/agents/output/plan/260604_121053/
$make-plan docs/agents/output/plan/260604_121053/260604_121053_PLAN.md resume and update
$docs-consistency README.md docs/architecture.md
```

Replace the paths in the examples with the actual review targets or generated artifact paths.

Paths inside the skills are relative to the root of the project where they are installed (`.claude/skills/...`).

- **Claude Code**: Install `before-plan` and `make-plan`, which reference other skill files, in the project. The review and Q&A sources that do not reference other skill files (`q`, `*-review`, `docs-*`) can also be installed as is in the user-global location (`~/.claude/skills/`).
- **Codex**: All wrappers, including those of the review and Q&A skills, reference the project's `.claude/skills/...` sources. In the current setup, follow the procedure above, which installs both the sources and the wrappers in the project. If the project does not have the sources, copying only the wrappers globally does not resolve the references.

## before-plan → make-plan Flow

Use it for work that needs agreement on requirements; if the user explicitly requests it, it proceeds regardless of the size of the work. Besides `/before-plan` and `/make-plan`, it may also be invoked by requests such as "write PRD/SPEC first" or "write PLAN from PRD/SPEC".

```
before-plan                                         make-plan
Conversation → PROMPT·PRD → PRD gate → SPEC  ──▶  Check input → Gather evidence → Write PLAN → Verification plan → Present
```

When before-plan finishes, it reports the artifact path, and the user runs make-plan separately with that path.

### before-plan

Artifacts are saved in `docs/agents/output/plan/{TS}/` (`{TS}` is `yyMMdd_HHmmss` generated with a shell command).

| Document | Role | Editing |
|---|---|---|
| `{TS}_PROMPT.md` | Record of the conversation with the user. Assigns question IDs (`Q1`…) | Append only (append-only) |
| `{TS}_PRD.md` | Refined requirements ("what and why"). Assigns requirement IDs (`REQ-01`…) | Body is based on user statements (facts about the current state and risks may also cite `file:line`) |
| `{TS}_SPEC.md` | Technical specification based on code exploration ("how"). Verification items per requirement (`V-01`…) | Current implementation needs `file:line`; planned design needs PRD requirement evidence |

- Move on to writing the SPEC only after every item in the PRD's section 11 completion checklist is checked `[x]` with evidence (PRD gate).
- Completion conditions: every item in the section 11 completion checklists of the PRD and SPEC artifacts (format per each guide's template) is checked `[x]` with evidence, and there are no unanswered questions.
- The progress status (`In progress` / `Awaiting answers` / `Ready`) and resume information are recorded in the PRD header. While awaiting answers, the documents are saved, and designating the same `{TS}` folder continues the work. Missing artifacts are completed under before-plan's resume rules; past PROMPT conversation is restored only from verifiable original text, and content that cannot be verified remains marked as missing or unverified.
- If the code and documents differ or questions arise while writing, decide which of SPEC, PRD, and PROMPT to fix according to the "Branching Rules" in [before-plan/SKILL.md](.claude/skills/before-plan/SKILL.md).

### make-plan

| Input | Description |
|---|---|
| Recommended input | The PRD and SPEC in the `{TS}` folder created by before-plan |
| Substitute input | If before-plan was not used, the parts of documents or conversation the user designates that correspond to a PRD/SPEC. The user's documents are not modified |

| Situation | How the PLAN is saved |
|---|---|
| New PLAN from the recommended input | Write `{TS}_PLAN.md` in the same `{TS}` folder as the input PRD/SPEC |
| New PLAN from a substitute input | Write only `{TS}_PLAN.md` in a new `{TS}` folder following before-plan's location and timestamp rules |
| Resuming or updating an existing PLAN is designated | Update it in the designated existing file and folder regardless of input type. A PLAN designated only as reference material is not automatically treated as the one to resume |

- A substitute input gets a first-pass review first. If a PRD/SPEC in the recommended input is missing or its completion checklist is incomplete, make-plan reports that and asks the user whether to finish before-plan or to proceed after a first-pass review. In the first-pass review, missing items are checked against the code first, and only what needs the user's judgment, such as goals, scope, and success criteria, is asked. Unanswered items are left as open issues in the PLAN and work proceeds.
- If the recommended input lacks a PROMPT, explain the restoration path and defer further questions about requirements and design until restoration. If there is no PRD in which to incorporate new requirements, leave the affected steps and downstream steps unresolved. In either case, plans supported by the existing PRD/SPEC can still be written. before-plan handles creating or restoring missing documents in the same `{TS}` folder.
- Questions and answers are recorded in the PROMPT for the recommended input, and in the PLAN's Decision Log for a substitute input. Recommendations are kept separate from the user's decisions, and a recommendation or non-response is not recorded as the user's choice. When recording is deferred because the PROMPT is missing, mark the pending logging as an open issue and record the verifiable original text after restoration.
- The PLAN contains, for each step ID (`S1`…), the evidence, targets (`file:line` for existing files, `path (new)` for new files), and completion conditions, plus acceptance criteria and verification, open issues (`ISSUE-01`…), and the Decision Log.
- The verification plan lists the methods and pass criteria for the applicable layers among Unit, Integration, E2E, and manual, and includes the project's build, type-check, and lint commands, if any, as prerequisite checks. Layers that do not apply get `N/A` with the reason, and layers where automated verification applies but the user cannot decide on a command either get `Undefined` with the reason. Writing the plan alone does not mark implementation or verification as complete.
- Implementation proceeds only when the user explicitly requests it, and only within that scope.
- When implementation is requested, work that can be done within existing requirements, verified evidence, and the delegated scope proceeds even with open issues. However, implementation that depends on matters requiring the user's decision or on missing required input evidence, and follow-up implementation that assumes their results, is done after that decision or evidence is secured. Technical choices are made autonomously within existing requirements and the delegated scope, and no additional approval is requested for answers or delegation already secured.

## Common Principles

- The skills act only when the user explicitly requests them.
- Evidence is presented according to its source. Evidence locations in code and document reviews are given as `file:line`, evidence for requirements and design as user statements or the corresponding ID, section, or item of the PRD/SPEC or substitute input, and external sources as links. Citing execution results follows the conditions each skill allows. Verified facts, inferences, assumptions, and unverified items are distinguished.
- Review and Q&A skills only report results. Fixes are made separately if the user requests them after seeing the results.

## Rules for Editing

- **Within this folder, `.claude/skills/` is the source.** Copy the frontmatter (`name`, `description`) of the `.agents` wrappers verbatim from the source, and keep in the body only a line pointing to the source and, if needed, Codex-specific guidance (e.g., the `$make-plan` guidance in `before-plan`).
- **Keep the model auto-selection settings paired.** "No" skills have `disable-model-invocation: true` in the source and `allow_implicit_invocation: false` in the wrapper's `openai.yaml`.
- **Edit places that share the same rules together.** The review target, comparison baseline, and handling of unavailable comparisons in `basic-code-review` and `concurrency-lifecycle-review` (the first three items in the body of each SKILL.md).
- **Within the skill files, keep the source of truth for each rule below in only one place**, and have other skill files only reference it. This README is a summary; if it differs from the skill files, follow the skill files.

  | Rule | Source of truth |
  |---|---|
  | Fix path when a problem is found (Branching Rules) | [before-plan/SKILL.md](.claude/skills/before-plan/SKILL.md) |
  | PRD editing rules | [before-plan/prd_guide.md](.claude/skills/before-plan/prd_guide.md) |
  | PRD and SPEC completion criteria | The completion criteria and the template section 11 completion checklists in [before-plan/prd_guide.md](.claude/skills/before-plan/prd_guide.md) and [spec_guide.md](.claude/skills/before-plan/spec_guide.md) |
  | What to record in the PLAN Decision Log | The PLAN skeleton ("Decision Log") in [make-plan/SKILL.md](.claude/skills/make-plan/SKILL.md) and the explanation recording rule in "Handling by Input Type" |

- After editing, review the skill with `/docs-clarity`, `/docs-consistency`, and `/docs-duplication`. For the rule on editing together with the other language folder, follow the [repository guide](../README.md).

## Current Status and Limitations

- The skills have not yet been verified by applying them to real work.
- Whether a multi-line `description` (YAML `|`) displays correctly in the Codex skill list needs to be checked.
- The path for new artifacts (`docs/agents/output/plan/`) cannot be changed through configuration.
