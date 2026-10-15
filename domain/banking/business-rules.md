# Module 01A — Banking business rules

Status: first document draft, awaiting learner document review.  
Checkpoint: v0.0.2 documentation packet, 2026-10-15.  
Scope: business semantics and grain only. No SQL or runtime implementation.

The learner has demonstrated the foundational grain, cardinality, identity and amount reasoning. That learning checkpoint is reviewed; this newly written document is not yet reviewed by the learner. The approved Phase 1 Architecture Learning Plan v0.0.1 governs scope. The learner's subsequent choice of one retained participation per customer/account/role resolves its participation-lifecycle question.

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
| BR-08 | A valid established account has at least one retained participation. The baseline open account has exactly one active primary liable participant and may have supplementary participants. This is a fictional-product rule. |
| BR-09 | An account may have zero or many cards. Every card belongs to exactly one account and is assigned to exactly one participant on that same account. |
| BR-10 | A participant may have zero or many cards. Issuing another card to that participant does not create another participation. |
| BR-11 | Each purchase/refund posting in this module references exactly one existing card, its account and one existing merchant. A card, account or merchant may have zero postings. |
| BR-12 | A refund references exactly one original purchase posting. A purchase may have zero or many refunds. A purchase itself has no original-purchase reference. |
| BR-13 | A refund and its original purchase concern the same account and currency. The fixture also uses the same card and merchant; this fixture choice does not establish a general same-card or same-merchant refund policy. |

References being individually valid is insufficient: a posting's account must agree with its card's account, and a card's participant must belong to that account. How to enforce these relationships is a later DDL discussion.

An established account's minimum participation requirement describes a valid business state. It does not prescribe the order of individual database writes. Card closure or participation deactivation must not erase historical references.

## 3. Identity and participation lifecycle

| Rule | Identity or lifecycle policy |
|---|---|
| BR-14 | Customer, account, card, merchant and posting references are stable, unique within their entity type in this source, and never reassigned to another identity. |
| BR-15 | A customer name/contact change preserves the customer reference. A merchant display-name change preserves the merchant reference when the source identity is unchanged. |
| BR-16 | A card reissue creates a new issued-instrument reference. The old instrument remains identifiable and old postings continue to reference it. Blocking a card changes its status, not its identity. |
| BR-17 | Retain at most one participation for each `(account_ref, customer_ref, role)` across active and inactive records. Change its current status when that same assignment is deactivated or reactivated. |
| BR-18 | The participation's account, customer and role define the assignment; changing one defines a different assignment. Do not mutate those values to make an existing row mean a different relationship. |
| BR-19 | Multiple cards for one customer on one account have distinct card references and may reference the same participation. |
| BR-20 | Re-receiving a posting with the same posting reference does not create a second financial occurrence. Same amount/card/merchant/time does not by itself prove two records are the same posting. |

BR-17 preserves assignment identity and current status, not a history of status transitions or separate participation periods. Calling an inactive row “historical” does not make all past states recoverable. Adding an assignment identifier later cannot reconstruct overwritten information.

The participation triple does not by itself prohibit the same customer from having different roles on one account. This checkpoint does not add a general role-exclusivity policy. The fixture has one primary participant and a different supplementary participant. Primary-role uniqueness in BR-08 is a separate rule from triple uniqueness.

## 4. Financial meaning

| Rule | Financial interpretation |
|---|---|
| BR-21 | All postings use CAD. Amounts are represented as signed integer minor units: CAD 100.00 is 10,000 cents. No physical SQL type is selected here. |
| BR-22 | Purchases are strictly positive; refunds are strictly negative. A zero-value posting is outside this module's purchase/refund fixture. |
| BR-23 | A refund is a new posting with its own reference and a link to the original purchase. It does not overwrite the purchase amount. |
| BR-24 | Gross purchases sum purchase amounts. Refund magnitude is the positive magnitude of refund amounts. Net purchase activity is gross purchases minus refund magnitude. |
| BR-25 | Net purchase activity is not outstanding account balance. Opening balances, payments, fees and other balance movements are not modeled here. |
| BR-26 | Once recorded, the posting's identity, amount, currency, type, account/card/merchant attribution, posting time and original-purchase link are immutable business facts in this exercise. |

Source-data mistakes and their correction workflows are not implemented by this checkpoint. Immutability must not be used to pretend recorded data can never be erroneous. Aggregate over-refund policy is also outside the current acceptance cases; no claim is made that the three-row fixture validates every refund scenario.

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

## 8. Review boundary

This document defines business behavior only. It contains no DDL, runtime tests or performance claims. Review it alongside [conceptual-model.md](conceptual-model.md) and [data-dictionary.md](data-dictionary.md). The next step is learner document review; PostgreSQL design begins only after that review.
