## 2026-10-15 — Module 01A reasoning evidence

The learner demonstrated:

- Grain is the business meaning of a row; customer, account, participation, instrument, merchant and posting have distinct identities.
- Allowed cardinalities are model rules even when the current fixture does not exercise every allowed count.
- Names and emails lack the uniqueness/stability guarantees required by the proposed key contract.
- An additional refund preserves gross purchase activity while increasing refund magnitude and reducing net purchase activity.
- Attribution follows the business question rather than any available foreign-key path.
- Authorization without posting does not belong in a financial-posting entity.

Instructor refinements introduced during document drafting:

- A posting following authorization is a separately identified occurrence; the authorization does not turn into a posting row.
- Authorization records do not automatically include every timeout, failure or attempted action.
- One retained assignment with mutable status does not preserve past status changes. A new identifier added later cannot recover overwritten history.
- A unique participation triple does not enforce exactly one active primary participant per account.
- Assigned-cardholder attribution identifies the instrument's assignee, not necessarily the physical actor.

The learner subsequently approved the domain review and dictionary on 2026-10-15. The approved boundaries are recorded in DECISIONS.md (M01A-D07 through M01A-D12).

## Distinctions retained in the approval

- Exactly one ACTIVE PRIMARY is a completed-state rule; atomic account creation must establish the initial participation together with the account.
- Retained-triple uniqueness, one active primary per OPEN account and mutually exclusive active roles per customer/account are three distinct rules.
- Card issuance eligibility is different from recording an already-existing source posting. Present-day inactivity must not erase history or automatically reject legitimate historical/source postings.
- Deferred refund checks do not declare the unchecked behavior valid.
- Immutability includes rejecting routine updates/deletes; duplicate identity must never become an accidental overwrite policy. A deliberate test reset has a different purpose.

Approval is evidence of a selected business policy, not proof of PostgreSQL enforcement knowledge. No additional learner exercise or runtime test is claimed as completed.
