## 2026-10-15 — Module 01A domain decisions

| ID | Decision | Reason and boundary |
|---|---|---|
| M01A-D01 | Use the approved six-entity CAD purchase/refund slice | Preserve a small, auditable baseline; no authorization entity |
| M01A-D02 | Keep one retained account/customer/role assignment and update current status | Learner-selected scope; separate participation episodes and transition history are not retained |
| M01A-D03 | Model reissued cards as new instrument identities | Preserve original posting attribution |
| M01A-D04 | Model refunds as separate immutable postings linked to the purchase | Preserve gross purchases, refund activity and net activity independently |
| M01A-D05 | Attribute activity according to the business question | Assigned cardholder and liable account participant answer different questions |
| M01A-D06 | Preserve the three-posting CAD 130 baseline | The fourth-posting CAD 120 exercise is separate evidence, not a fixture replacement |

The dictionary proposes explicit attribute names and status vocabulary. These remain pending document review and do not select SQL types or primary-key implementations. See domain/banking/business-rules.md for numbered rules.
