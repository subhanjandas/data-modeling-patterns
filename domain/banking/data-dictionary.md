# Module 01A — Logical data dictionary

Status: domain semantics and dictionary approved by the learner on 2026-10-15; this revision records the approved decisions. Implementation and runtime verification have not started.  
Scope: business attributes and meanings, not SQL types, DDL or storage design.

Business policies are authoritative in [business-rules.md](business-rules.md); relationships are explained in [conceptual-model.md](conceptual-model.md). Stable references and immutable postings follow the reviewed learning model. The learner explicitly approved these dictionary details and the limited status semantics on 2026-10-15. Attribute meanings and requiredness are approved; physical types, key implementations and enforcement mechanisms remain undecided.

## 1. Conventions

- Required: must have a business value for a valid record in this scope.
- Optional: absence has a defined meaning; it does not automatically mean malformed data.
- Stable: retain the value for the identity's lifetime; do not reuse the reference for another entity.
- Mutable: may change while the entity retains its identity. This dictionary does not imply that previous values are preserved.
- Posting business attributes are immutable after recording for this exercise.
- References are synthetic identifiers in one source, not real card numbers or personal identifiers.
- Candidate-key claims come from business rules, not observed uniqueness in the fixture.

## 2. customer

Grain: one recognized synthetic person, independent of accounts.  
Business/candidate key: customer_ref. No alternate key is asserted.

| Attribute | Business meaning | Required? | Change policy | Example |
|---|---|---|---|---|
| customer_ref | Source's durable identity for the person | Yes | Stable | C001 |
| display_name | Human-readable synthetic name | Yes | Mutable; not unique | Alex |
| email | Optional synthetic contact address | No | Mutable; not a key | alex@example.test |
| customer_status | Current customer-record status | Yes | Mutable | ACTIVE |

Approved status vocabulary: ACTIVE, INACTIVE. These labels describe the record's current status; they are not identity changes. A customer can have no accounts/cards. Real contact data is not needed in fixtures.

## 3. account

Grain: one distinct credit account.  
Business/candidate key: account_ref.

| Attribute | Business meaning | Required? | Change policy | Example |
|---|---|---|---|---|
| account_ref | Durable credit-account reference | Yes | Stable | A001 |
| currency_code | Currency for this account's activity | Yes | Fixed CAD for this module | CAD |
| account_status | Current account state | Yes | Mutable | OPEN |
| credit_limit_minor | Current agreed nonnegative credit limit in CAD cents | Yes | Mutable | 500000 |

Approved status vocabulary: OPEN, CLOSED. The example credit limit means CAD 5,000.00 and does not affect fixture purchase totals. Every OPEN account must have exactly one ACTIVE PRIMARY participation in a completed valid business state. Creation establishes both atomically. Closed accounts retain prior assignment records, without implying preserved status history. An account may have no cards or postings. No balance attribute is introduced: the baseline does not supply a complete account-balance calculation.

## 4. account_party

Grain: one retained customer/account/role assignment.  
Business/candidate key: (account_ref, customer_ref, role), under the learner's selected no-separate-episodes policy.

| Attribute | Business meaning | Required? | Change policy | Example |
|---|---|---|---|---|
| account_ref | Account in which the person participates | Yes | Stable part of assignment identity | A001 |
| customer_ref | Person receiving the role | Yes | Stable part of assignment identity | C001 |
| role | Participation responsibility in this fictional product | Yes | Stable part of assignment identity | PRIMARY |
| participation_status | Current state of this retained assignment | Yes | Mutable; does not preserve transition history | ACTIVE |

Approved roles: PRIMARY (liable participant in this product), SUPPLEMENTARY (authorized supplementary participant without that primary liability role). Approved statuses: ACTIVE, INACTIVE.

The triple remains unique across both statuses. At most one active row is not the same rule as at most one retained row: the latter is the selected policy. No assignment_ref or participation-period history is introduced in this module.

An open account's exactly-one-active-primary rule must be enforced separately. A unique triple alone would permit two different customers to have PRIMARY assignments on the same account. The same customer also cannot simultaneously have ACTIVE PRIMARY and ACTIVE SUPPLEMENTARY assignments on one account. Role-transfer workflows and separate participation periods are not implemented in Module 01A.

## 5. credit_card

Grain: one issued instrument assigned to one participant on one account.  
Business/candidate key: card_ref.

| Attribute | Business meaning | Required? | Change policy | Example |
|---|---|---|---|---|
| card_ref | Durable issued-instrument reference | Yes | Stable; replacement gets a new value | K001 |
| account_ref | Account associated with the instrument | Yes | Stable | A001 |
| cardholder_customer_ref | Customer assigned this instrument | Yes | Stable | C001 |
| cardholder_role | Role identifying the assigned participation on that account | Yes | Stable for this instrument assignment | PRIMARY |
| card_status | Current issued-instrument state | Yes | Mutable | ACTIVE |

Approved statuses: ACTIVE, BLOCKED, REPLACED, CLOSED. A status change does not overwrite the instrument identity or move its historical postings to a replacement.

The three participation-reference attributes identify one account_party assignment. This avoids an ambiguous “join to all customers on the account.” New card issuance requires an OPEN account and ACTIVE participation. This issuance precondition does not require retained historical cards to reference currently active participation forever. Role transfer is outside this module. Physical enforcement is deliberately undecided until DDL design. No PAN, CVV or real card data is included.

## 6. merchant

Grain: one source-recognized merchant acceptance identity.  
Business/candidate key: merchant_ref.

| Attribute | Business meaning | Required? | Change policy | Example |
|---|---|---|---|---|
| merchant_ref | Durable merchant identity within the source | Yes | Stable | M001 |
| display_name | Merchant's descriptive label | Yes | Mutable; not unique | Merchant Alpha |
| category_label | Synthetic descriptive category | No | Mutable; absent means not supplied | RETAIL |
| merchant_status | Current merchant-record state | Yes | Mutable | ACTIVE |

Approved statuses: ACTIVE, INACTIVE. category_label is a teaching label, not a claim to implement a real merchant-category code standard. Merchant classification history is not implemented.

## 7. card_transaction

Grain: one recorded purchase or refund financial posting.  
Business/candidate key: posting_ref.

| Attribute | Business meaning | Required? | Change policy | Example |
|---|---|---|---|---|
| posting_ref | Unique identity of this financial occurrence | Yes | Immutable | P001 |
| account_ref | Account receiving the financial posting | Yes | Immutable; must match card's account | A001 |
| card_ref | Issued instrument associated with the posting | Yes | Immutable | K001 |
| merchant_ref | Merchant identity associated with the posting | Yes | Immutable | M001 |
| posting_type | PURCHASE or REFUND | Yes | Immutable | PURCHASE |
| amount_minor | Signed CAD cents | Yes | Immutable; purchase positive, refund negative | 10000 |
| currency_code | Currency of the posting | Yes | Immutable; CAD | CAD |
| posted_at | Instant the source recorded financial posting to the account | Yes | Immutable; timezone-aware meaning | An explicitly chosen UTC instant in the future fixture implementation |
| original_purchase_ref | Existing PURCHASE posting to which a refund relates; must not be this posting | Required for refund; absent for purchase | Immutable | P001 on P002 |

posted_at is a source posting instant, not a warehouse arrival timestamp or necessarily the purchase occurrence instant. Exact fixture timestamps will be selected when data is implemented; no relative “now” timestamps are required by this document.

No authorization reference, event-processing metadata or general transaction-status field is introduced. Membership already means a posting occurred. A refund is another posting, not a mutation of the original posting's type or amount.

Duplicate posting_ref values are rejected, including repeats with different payloads; they never overwrite an existing posting. Routine business updates/deletes are prohibited. Deliberate lab reset is an administrative/testing operation. Correction workflows are deferred.

A refund must have a negative amount, refer to an existing PURCHASE other than itself, and share the purchase's account and currency. A PURCHASE must have no original_purchase_ref. Aggregate over-refund validation and same-card/same-merchant refund policies are deferred; unvalidated behavior must not be described as valid business behavior.

Historical postings remain valid when referenced entity statuses change. Do not reject a legitimate historical/source posting merely because the card or merchant is currently inactive. No full status-transition workflow is implied by the approved value lists.

## 8. Key and aggregation traps

| Tempting assumption | Why it fails |
|---|---|
| Name/email identifies a person | Neither is guaranteed unique and permanent under our rules |
| Account identifies a card | One account can have multiple issued instruments |
| Customer/account identifies a card | One participant can have multiple instruments |
| Card/merchant/time/amount identifies a posting | Distinct legitimate postings may share all those values |
| An inactive assignment allows the same triple to be inserted again | That violates the selected one-retained-assignment policy |
| Joining postings to account participants preserves posting grain | It produces a posting/participant pair for each match |

The dictionary intentionally contains no primary-key syntax, physical types, indexes, defaults or DDL. Those choices follow the next taught logical/DDL design checkpoint. Approval of this dictionary is not approval of a particular implementation mechanism.
