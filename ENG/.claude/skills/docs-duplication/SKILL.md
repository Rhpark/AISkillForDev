---
name: docs-duplication
description: "Reviews content duplication within a single document and across documents, and reports it while distinguishing necessary repetition. Does not change files during the review."
disable-model-invocation: true
---

# docs-duplication — Document Duplication Review

- Prioritize the target designated by the user; if none is designated, determine the review target from the conversation context. If the target is unclear, confirm with the user. State the review scope and unverified items.
- For a single document, review within the document; for multiple documents, review both within each document and across documents.
- Considering each document's role, audience, version, and conditions, judge duplication of wording and meaning. Do not treat a mere shared topic as duplication, and distinguish the extent of full and partial duplication and whether to consolidate or keep it.
- Considering the explicitly designated source of truth and each document's role, propose a merge, summary, or reference direction for duplication that needs consolidation, and give the reason for repetition to keep. If the criteria for consolidation are unclear, do not assert which content to keep; present possible consolidation directions and what needs to be confirmed. Preserve the unique explanations, conditions, and exceptions mixed into partial duplication.
- For each duplication, report together the document path and line location of each occurrence, the shared content, the extent of duplication, the grounds for the judgment, and the consolidation or retention direction. Distinguish confirmed duplication from candidates with insufficient evidence.
- Review read-only for the current request and provide results only as an answer. Do not create, modify, or delete files, or change the state of Git or external services. Make fixes separately from this review if the user requests them after the results are presented.
