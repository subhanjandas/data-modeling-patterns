## 2026-10-15 — Module 01A business semantics checkpoint

- Phase 1 architecture: approved by the learner.
- Six entity grains: learner reviewed; answers supplied and evaluated.
- Cardinality/optionality basics: learner reviewed; distinguished model rules from fixture counts.
- Participation policy: selected one retained assignment per account/customer/role, with mutable current status.
- Identity changes: learner correctly retained customer/merchant identities and assigned a new reference to a replacement instrument.
- Financial arithmetic: learner correctly derived 4 postings, CAD 150 gross purchases, CAD 30 refunds and CAD 120 net activity for the additional-refund exercise.
- Attribution: learner selected posting → card → assigned cardholder and stated that the business question controls the path.
- Authorization boundary: core distinction learner reviewed. Instructor clarified that a subsequent posting has its own identity and authorization data is not a generic log of every failed attempt.
- Domain documents: drafted; learner document review pending.
- Explicit dictionary fields/status vocabulary: proposed; pending document review.
- SQL/DDL, data loading, runtime tests and Module 01A implementation: not started.
- Git integration/commit: pending local import and learner review; no remote repository was accessed.
- Next action: review business-rules.md, conceptual-model.md and data-dictionary.md. Then begin DDL design through the teaching workflow.

The business-semantics learning checkpoint is reviewed. Module 01A as a whole is not complete, implemented or runtime-verified.
