# Data Modeling & Analytical Architecture Patterns

A SQL-first learning and portfolio repository for explaining, implementing and verifying data-modeling and analytical-architecture decisions through one evolving synthetic banking case study.

The learning method is: explain the concept, reason about a small exercise, review the answer, implement that piece, verify it and record a meaningful checkpoint. The complete [approved Phase 1 plan](docs/phase1-architecture-learning-plan.md) defines the future roadmap.

## Current scope and status

**Module 01A — One account, two cardholders, two workloads.**

The six entities are customer, account, account_party, credit_card, merchant and card_transaction. The baseline has two customers, one CAD credit account, two cards, two merchants and three purchase/refund postings: CAD +100, -20 and +50. Expected purchases are CAD 150, refunds CAD 20 and net purchase activity CAD 130. Net purchase activity is not an account balance.

The business-semantics exercise is learner reviewed. The domain review and dictionary were explicitly approved on 2026-10-15. This checkpoint records that approval in documentation. SQL, PostgreSQL DDL, environment setup and runtime verification have not started; there is no runnable database quickstart yet. Later modules remain planned.

The case study is entirely fictional and synthetic. It contains no employer schema, proprietary banking rules, actual customer data or real card credentials.

## Repository structure

| Path | Purpose |
|---|---|
| docs/phase1-architecture-learning-plan.md | Self-contained approved Phase 1 baseline and approval-status note |
| domain/banking/business-rules.md | Approved business contract and fixture expectations |
| domain/banking/conceptual-model.md | Entity grains, relationships and attribution paths |
| domain/banking/data-dictionary.md | Attribute meanings, requiredness and change rules |
| PROGRESS.md | Evidence-based learning and implementation status |
| DECISIONS.md | Approved decisions and scope boundaries |
| LEARNING_NOTES.md | Demonstrated reasoning and remaining learning work |
| INTERVIEW_NOTES.md | Suggested explanations with honest evidence boundaries |
| CHANGELOG.md | Documentation checkpoint history |

The planned module location is modules/01-relational-modeling/01a-oltp-vs-olap/. Runnable SQL, fixtures, infrastructure and tests will be introduced incrementally after their teaching checkpoints. Empty local folders are not evidence of implemented repository features.

## Reading order

1. [Approved architecture plan](docs/phase1-architecture-learning-plan.md) — scope, sequence and definition of done.
2. [Business rules](domain/banking/business-rules.md) — what must be true and which checks are deferred.
3. [Conceptual model](domain/banking/conceptual-model.md) — entity meaning and relationship paths.
4. [Data dictionary](domain/banking/data-dictionary.md) — approved attributes before physical database choices.
5. [Decisions](DECISIONS.md) and [progress](PROGRESS.md) — approvals and the next teaching checkpoint.
6. [Learning notes](LEARNING_NOTES.md), [interview notes](INTERVIEW_NOTES.md) and [changelog](CHANGELOG.md).

## Next checkpoint

Map approved business rules to logical relationships, keys and enforcement responsibilities. Explain and review a small reasoning exercise before implementing that piece. The intended local stack is PostgreSQL, SQL, Python and Docker Compose; exact versions and executable commands will be recorded when setup is actually tested.
