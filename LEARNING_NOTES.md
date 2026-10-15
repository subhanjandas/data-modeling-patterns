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

These refinements are documented for review. No additional learner exercise or runtime test is claimed as completed.
