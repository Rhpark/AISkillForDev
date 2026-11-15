---
name: docs-consistency
description: "Reports inconsistencies in explanations, rules, states, and references across documents, with evidence. Does not change files during the review."
disable-model-invocation: true
---

# docs-consistency — Document Consistency Review

- Prioritize the target designated by the user; if none is designated, determine the review target from the conversation context. If the target is unclear, confirm with the user. State the review scope and unverified items.
- Considering each document's role, audience, time frame, version, and conditions, check whether explanations, rules, and states agree. Do not treat differences in wording or repeated content by themselves as grounds for an inconsistency.
- Judge against the explicitly designated source of truth. Even if the source of truth is unclear, report confirmed inconsistencies, but do not assert which side is correct.
- Distinguish actual references from examples and templates, and check that path and link targets exist and match their descriptions. Mark references that could not be checked because they were inaccessible as unverified.
- For each finding, present the paths and line locations of the related documents, the inconsistency, and the grounds for the judgment. If the criteria for resolving it are clear, present a resolution direction; if not, present what needs to be confirmed. Distinguish confirmed inconsistencies from items needing further confirmation, and do not draw conclusions about the actual implementation from documents alone.
- Review read-only for the current request and provide results only as an answer. Do not create, modify, or delete files, or change the state of Git or external services. Make fixes separately from this review if the user requests them after the results are presented.
