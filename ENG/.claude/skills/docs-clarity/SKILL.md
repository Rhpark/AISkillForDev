---
name: docs-clarity
description: "Reviews documents for ambiguous wording and room for AI misinterpretation, and proposes meaning-preserving improvements. Does not change files during the review."
disable-model-invocation: true
---

# docs-clarity — Document Clarity Review

- Prioritize the target designated by the user; if none is designated, determine the review target from the conversation context. If the target is unclear, confirm with the user. State the review scope and unverified items.
- Read the words, sentences, and paragraphs of one or more documents in context, and find places where terms, referring expressions (such as pronouns and demonstratives), who acts, what is acted on, conditions, exceptions, or paragraph connections are unclear or could be read with a different meaning.
- From an AI's perspective, check whether the boundaries between instructions, explanations, and examples, the level of obligation (required or recommended), and the scope, duration, and priority of guidelines are clear. Distinguish mere stylistic preference from interpretation problems.
- Improvements preserve the original's meaning, conditions, exceptions, and level of obligation, and do not add unsupported requirements, numbers, or criteria.
- For each finding, present the document path, line location, and original text, the possible misreading, and the evidence of the interpretation problem. If the intent is confirmed by another part of the document or by what the user stated directly, present improved wording; if not, present the possible interpretations and what needs to be confirmed. For each improved wording, state the interpretations it excludes and the content it newly adds compared to the original. Mark AI misreadings that were not actually observed as possibilities, not observed facts.
- Review read-only for the current request and provide results only as an answer. Do not create, modify, or delete files, or change the state of Git or external services. Make fixes separately from this review if the user requests them after the results are presented.
