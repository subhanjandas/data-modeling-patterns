# Module 01A — Banking business rules

Status: domain semantics and dictionary approved by the learner on 2026-10-15; this revision records the approved decisions. Implementation and runtime verification have not started.  
Checkpoint: v0.0.3 domain-approval documentation, 2026-10-15 (not the runnable v0.1.0 release).  
Scope: business semantics and grain only. No SQL or runtime implementation.

The learner demonstrated the foundational grain, cardinality, identity and amount reasoning, then explicitly approved the six review decisions and dictionary details on 2026-10-15. This revision incorporates that approval; it does not claim another reasoning exercise or database test. The [approved Phase 1 architecture plan](../../docs/phase1-architecture-learning-plan.md) governs scope. Later-module scope is unchanged.

These rules describe a fictional CAD credit-card product. They do not represent any employer's schema, actual customer information or universal banking-product rules.

## 1. Meaning before structure

A grain states exactly what one row represents. A key identifies that occurrence or entity. A cardinality describes how many related entities are permitted; optionality determines whether zero is permitted. All six entities refer to one synthetic source, in which each source reference is guaranteed unique within its entity type.

| Rule | Business meaning |
|---|---|
| BR-01 | A customer is one recognized synthetic person, independently of any account. |
| BR-02 | An account is one CAD credit account, independently of its issued cards. |
| BR-03 | An account participation is one retained customer/account/role assignment. |
| BR-04 | A credit card is one issued instrument assigned to one participant on one account. |
| BR-05 | A merchant is one merchant acceptance identity recognized by the source, not necessarily an entire legal company or brand. |
| BR-06 | A card transaction is one immutable financial posting. This module includes purchases and refunds only. |

## 2. Relationships and optionality

| Rule | Required relationship |
|---|---|
| BR-07 | A customer may have zero or many account participations. Every participation references exactly one existing customer and one existing account. |
| BR-08 | Every OPEN account must have exactly one ACTIVE PRIMARY participation in a completed valid business state. An established account retains at least one participation; closed accounts retain their prior assignment records. This is a universal rule for this fictional product, not just a fixture property. |
| BR-09 | An account may have zero or many cards. Every card belongs to exactly one account and is assigned to exactly one participant on that same account. |
| BR-10 | A participant may have zero or many cards. Issuing another card to that participant does not create another participation. |
| BR-11 | Each purchase/refund posting in this module references exactly one existing card, its account and one existing merchant. A card, account or merchant may have zero postings. |
| BR-12 | A refund references exactly one existing posting whose type is PURCHASE, and cannot reference itself. A purchase may have zero or many refunds. A PURCHASE posting has no original-purchase reference. |
| BR-13 | A refund and its original purchase concern the same account and currency. The fixture also uses the same card and merchant; this fixture choice does not establish a general same-card or same-merchant refund policy. |

References being individually valid is insufficient: a posting's account must agree with its card's account, and a card's participant must belong to that account. How to enforce these relationships is a later DDL discussion.

Account creation must establish the account and its initial ACTIVE PRIMARY participation atomically. The minimum-participation and exactly-one-primary requirements describe the completed valid business state, not every intermediate write. Enforcement mechanisms will be selected during logical/DDL design. Closed accounts retain prior assignment records; this retention does not imply a history of participation-status transitions. Card closure or participation deactivation must not erase historical references.

## 3. Identity and participation lifecycle

| Rule | Identity or lifecycle policy |
|---|---|
| BR-14 | Customer, account, card, merchant and posting references are stable, unique within their entity type in this source, and never reassigned to another identity. |
| BR-15 | A customer name/contact change preserves the customer reference. A merchant display-name change preserves the merchant reference when the source identity is unchanged. |
| BR-16 | A card reissue creates a new issued-instrument reference. The old instrument remains identifiable and old postings continue to reference it. Blocking a card changes its status, not its identity. |
| BR-17 | Retain at most one participation for each `(account_ref, customer_ref, role)` across active and inactive records. Change its current status when that same assignment is deactivated or reactivated. |
| BR-18 | The participation's account, customer and role define the assignment; changing one defines a different assignment. Do not mutate those values to make an existing row mean a different relationship. |
| BR-19 | Multiple cards for one customer on one account have distinct card references and may reference the same participation. |
| BR-20 | Duplicate posting_ref values are rejected in Module 01A, including identical repeats and duplicates containing different values. A duplicate never overwrites an existing posting. Same amount/card/merchant/time does not by itself prove two differently identified postings are duplicates. |

BR-17 preserves assignment identity and current status, not a history of status transitions or separate participation periods. Calling an inactive row “historical” does not make all past states recoverable. Adding an assignment identifier later cannot reconstruct overwritten information.

The participation triple does not itself prevent simultaneous active roles. BR-33 below adds that separate rule. Retained inactive assignments may coexist with another role assignment, but role-transfer workflows are not implemented in Module 01A. Old assignment and card identities are preserved. Repeated role periods and richer temporal history remain deferred to the existing later temporal-modeling scope. Primary-participant cardinality in BR-08 is also separate from triple uniqueness.

## 4. Financial meaning

| Rule | Financial interpretation |
|---|---|
| BR-21 | All postings use CAD. Amounts are represented as signed integer minor units: CAD 100.00 is 10,000 cents. No physical SQL type is selected here. |
| BR-22 | Purchases are strictly positive; refunds are strictly negative. A zero-value posting is outside this module's purchase/refund fixture. |
| BR-23 | A refund is a new posting with its own reference and a link to the original purchase. It does not overwrite the purchase amount. |
| BR-24 | Gross purchases sum purchase amounts. Refund magnitude is the positive magnitude of refund amounts. Net purchase activity is gross purchases minus refund magnitude. |
| BR-25 | Net purchase activity is not outstanding account balance. Opening balances, payments, fees and other balance movements are not modeled here. |
| BR-26 | Once recorded, the posting's identity, amount, currency, type, account/card/merchant attribution, posting time and original-purchase link are immutable business facts. Routine business updates and deletes of posting rows are not allowed. Deliberate lab reset is a separate administrative/testing operation; enforcement mechanisms are not yet selected. |

Source-data mistakes and complex correction workflows are deferred. Immutability does not imply recorded data can never be erroneous.

The approved refund-validation boundary is:

| Validate in Module 01A | Explicitly deferred |
|---|---|
| Refund amount is negative | Aggregate over-refund validation |
| Original posting exists and has type PURCHASE | Same-card refund policy |
| Account and currency match the original purchase | Same-merchant refund policy |
| PURCHASE has no original-purchase reference | Complex refund correction workflows |
| Refund does not reference itself | |

Deferred checks are not implemented or verified by this module. Their absence must not be described as evidence that an otherwise unchecked refund is valid business behavior. The fixture's same-card and same-merchant examples do not establish those policies.

## 5. Baseline fixture — the source of expected amounts

| Entity | Reference | Meaning |
|---|---|---|
| customer | C001 | Alex, primary liable participant |
| customer | C002 | Sam, supplementary participant |
| account | A001 | Shared CAD credit account |
| account_party | (A001, C001, PRIMARY) | Alex's active primary assignment |
| account_party | (A001, C002, SUPPLEMENTARY) | Sam's active supplementary assignment |
| credit_card | K001 | Alex's instrument on A001 |
| credit_card | K002 | Sam's instrument on A001 |
| merchant | M001 | Merchant Alpha |
| merchant | M002 | Merchant Beta |

| Posting | Type | Account | Card | Merchant | Signed CAD | Signed cents | Original purchase |
|---|---|---|---|---|---:|---:|---|
| P001 | PURCHASE | A001 | K001 | M001 | +100.00 | +10000 | none |
| P002 | REFUND | A001 | K001 | M001 | -20.00 | -2000 | P001 |
| P003 | PURCHASE | A001 | K002 | M002 | +50.00 | +5000 | none |

The baseline has 2 customers, 1 account, 2 participations, 2 cards, 2 merchants and 3 postings. Expected gross purchases are CAD 150.00; refund magnitude is CAD 20.00; net purchase activity is CAD 130.00.

The learner correctly evaluated a separate exercise adding a CAD 10.00 refund: 4 postings, CAD 150.00 gross purchases, CAD 30.00 refunds and CAD 120.00 net activity. That exercise does not change the three-posting baseline.

## 6. Attribution and multiplication risk

| Rule | Reporting policy |
|---|---|
| BR-27 | Account totals count each relevant posting exactly once. |
| BR-28 | Attribution to the assigned cardholder follows posting → issued card → its specific participant → customer. This establishes the card's assigned person, not proof of who physically performed an action. |
| BR-29 | Liability attribution follows the account's liable role and answers a different question from assigned-cardholder attribution. No historical liability query is claimed from current participation status alone. |
| BR-30 | Matching each posting to every account participant repeats financial amounts. The combined row grain becomes posting × participant. |

For the baseline, assigned-cardholder net activity is Alex CAD 80.00 and Sam CAD 50.00. Their sum is CAD 130.00. Matching all 3 postings to both participants produces 6 combined rows and an incorrect sum of CAD 260.00 for account activity. The underlying postings remain unchanged.

Adding a third card without a new posting changes card counts but not financial totals. Counting relationships is not the same as counting financial occurrences.

## 7. Authorization boundary

BR-31: An authorization concerns a decision on a requested transaction. It is not a financial posting. If a posting subsequently occurs, it has its own posting identity; the authorization is not converted into a posting row in this entity.

An authorization may have no associated posting or multiple associated postings. A posting may lack a matched authorization in the available data. Counting both an authorization and its posting as purchase amounts can double count one purchase.

An authorization dataset is not a catch-all for every failed attempt. Requests, decisions, timeouts and reversals require their own precise scope if modeled. No authorization entity is created in Module 01A.

## 8. Approved status and dictionary boundaries

| Rule | Approved behavior |
|---|---|
| BR-32 | New card issuance requires an OPEN account and ACTIVE participation on that account. Full transition workflows are out of scope. |
| BR-33 | The same customer cannot simultaneously have ACTIVE PRIMARY and ACTIVE SUPPLEMENTARY participations on the same account. Retained assignment identity remains the full account/customer/role triple. |
| BR-34 | credit_limit_minor is nonnegative. Customer email remains optional and non-unique; category_label remains optional; posted_at is required and timezone-aware. References remain single-source and entity-scoped. |

Existing historical postings remain valid even when referenced customer, card, merchant or account statuses later change. Do not reject a legitimate historical/source posting merely because its card or merchant is inactive at processing time. This is a recording rule, not permission to initiate new purchases on an inactive instrument. Current statuses do not prove what an entity's status was at a prior instant.

The dictionary's status vocabulary is approved under these limited semantics. New-card issuance eligibility is an issuance-time precondition, not a requirement that every retained card must always reference an active participation. Full role-transfer, closure and other status-transition workflows remain outside Module 01A.

## 9. Approval and implementation boundary

This revision records the learner's explicit review approval. Read it alongside [conceptual-model.md](conceptual-model.md) and [data-dictionary.md](data-dictionary.md). Checking that this edit faithfully reflects approval is distinct from a new learning exercise. No SQL, PostgreSQL DDL, runtime tests or performance claims are included. The next teaching checkpoint will map approved rules to logical relationships and enforcement responsibilities; no mechanism has been silently selected.
