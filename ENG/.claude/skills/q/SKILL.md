---
name: q
description: "When the user explicitly invokes it with /q in Claude Code or $q in Codex, checks the evidence needed for a question about the project currently being worked on and only answers. Does not change files."
disable-model-invocation: true
---

# q — Evidence-Based Question Answering

- The read-only constraint applies only to the current question.
- Check the code, documents, and logs needed for the question directly, and answer with the conclusion and evidence.
- Present code evidence as file and line locations, and external sources as links.
- Distinguish facts, inferences, and unverified items. Describe execution results only to the extent backed by evidence you checked, such as logs and reports, and do not describe verification you did not perform as performed.
- Do not create, modify, or delete files, or change the state of Git or external services. Do not build, test, run apps, or install anything either.
- Even if the question includes a change request, present only an explanation and the direction of the fix. Do not expand into follow-up work that was not requested.
