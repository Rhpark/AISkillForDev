# PROMPT Writing Guide

## Role

The PROMPT document is a **record (log) of the conversation with the user**. Update it in real time during before-plan, and during make-plan when its input is the recommended input (before-plan's artifacts).
It is the "source evidence" for the PRD/SPEC and an audit trail, and is not used as the planning basis for `make-plan`.

## Core Rule: append-only

- **Only append.** Do not summarize, reinterpret, delete, or reorder what has already been recorded.
- "Adding content" is allowed (the conversation grows, after all). What is prohibited is **altering existing records**.
- Accumulate chronologically. If an answer changes later, do not fix the earlier record; add a new entry.
  (e.g., append a new "Q3 answer correction: ...". Leave a question with no answer as "Answer: unanswered", and append an answer received later as "Q3 answer (added): ...")
- Keep assigning question IDs in sequence after resuming, and do not reuse them for other questions. Follow-up answers and corrections reference the original question ID.

## What to Record

- The user's original request (the initial prompt as is)
- Questions asked for clarification and the user's answers
- Decisions and choices made by the user (if there were options, which one was chosen)
- Record recommendations and options presented in questions separately from the user's answers. Do not record a recommendation or a non-response as the user's choice.
- Questions and answers that arise during SPEC writing, and during make-plan with the recommended input, also accumulate here

## Format (example)

```md
# PROMPT

## Initial Request (original)
> {What the user first said, as is}

## Conversation Log (chronological, append-only)

### Q1. {question}
- Answer: {user answer}

### Q2. {question}
- Answer: {user answer}

### [during SPEC exploration] Q3. {question}
- Answer: {user answer}
- Impact: SPEC change / PRD and SPEC change (mark per the Branching Rules)

### [during make-plan] Q4. {question}
- Answer: {user answer}
- Impact: SPEC change / PRD and SPEC change (mark per the Branching Rules)
```

## Do Not

- Do not record anything the user did not say.
- Do not write guesses or interpretations as if they were user statements (interpretation happens in the PRD).
