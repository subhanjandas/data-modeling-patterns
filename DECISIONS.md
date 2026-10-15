## 2026-10-15 — Module 01A domain decisions

| ID | Decision | Reason and boundary |
|---|---|---|
| M01A-D01 | Use the approved six-entity CAD purchase/refund slice | Preserve a small, auditable baseline; no authorization entity |
| M01A-D02 | Keep one retained account/customer/role assignment and update current status | Learner-selected scope; separate participation episodes and transition history are not retained |
| M01A-D03 | Model reissued cards as new instrument identities | Preserve original posting attribution |
| M01A-D04 | Model refunds as separate immutable postings linked to the purchase | Preserve gross purchases, refund activity and net activity independently |
| M01A-D05 | Attribute activity according to the business question | Assigned cardholder and liable account participant answer different questions |
| M01A-D06 | Preserve the three-posting CAD 130 baseline | The fourth-posting CAD 120 exercise is separate evidence, not a fixture replacement |

## 2026-10-15 — Explicit review approvals

| ID | Approved decision | Boundary |
|---|---|---|
| M01A-D07 | Every OPEN account has exactly one ACTIVE PRIMARY in a completed valid business state; create account and initial primary atomically | Closed accounts retain assignments; enforcement mechanism undecided |
| M01A-D08 | No simultaneous ACTIVE PRIMARY and SUPPLEMENTARY for the same customer/account | Preserve retained triples and card identities; role transfer and repeated temporal periods deferred |
| M01A-D09 | New card issuance requires OPEN account and ACTIVE participation | Historical postings remain valid after referenced statuses change; current card/merchant inactivity alone does not reject legitimate source postings; full transition workflows deferred |
| M01A-D10 | Enforce the explicitly approved refund-validation boundary | Negative refund; existing original PURCHASE; same account/currency; no original reference for PURCHASE; no self-reference. Aggregate over-refund, same-card/same-merchant policies and complex corrections are deferred, not validated behavior |
| M01A-D11 | Reject duplicate posting references and prohibit routine posting updates/deletes | Never overwrite on duplicate arrival; administrative/testing reset is separate; correction workflows deferred |
| M01A-D12 | Approve dictionary details | Nonnegative credit limit; optional non-unique email; optional category; required timezone-aware posted_at; listed statuses under limited semantics; single-source entity-scoped references |
| M01A-D13 | Include approved architecture baseline and a minimal repository README | No later-module scope changes; no SQL or physical enforcement choices in this revision |

These decisions were explicitly approved by the learner. They do not select SQL types, primary-key implementations or enforcement mechanisms. See [business rules](domain/banking/business-rules.md) and the [approved architecture plan](docs/phase1-architecture-learning-plan.md).
