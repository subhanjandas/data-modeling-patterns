## 2026-10-15 — Module 01A: explain grain before keys

Suggested interview explanation, based on the learner's demonstrated reasoning:

> I define the business meaning of a row before choosing keys. In this card example, a customer is a person, an account is a credit relationship, an account participation assigns the person a role, and a card is an issued instrument. A posting is a separate financial occurrence. A refund gets its own posting so we retain gross purchases and refund activity. For cardholder attribution I follow the instrument's assigned participant; joining every posting to all account participants would multiply financial amounts.

Follow-up distinctions:

- Candidate keys require guaranteed minimal uniqueness; fixture uniqueness alone is insufficient.
- Mutable contact details do not change customer identity; replacing an instrument does create a new card identity.
- Current-status participation is a deliberate simplification with no claim to recover prior participation periods.
- Authorization and posting have different grains and identities.

Learner evidence: correct grain/cardinality explanations, identity examples, refund arithmetic and attribution path. The exact interview wording above is an editorial draft, not a claim the learner delivered a timed interview answer.

## Approved policy versus future implementation

The learner approved the refined Module 01A business contract on 2026-10-15. Useful distinctions to explain in future interviews are:

- Every OPEN account must have exactly one ACTIVE PRIMARY at a completed business-state boundary; creating the two together is atomic.
- Unique assignment identity does not also enforce active-role cardinality.
- A historical posting can remain legitimate after an instrument becomes inactive; recording history differs from authorizing a new action.
- A deferred check is an explicit limitation, not an endorsement of unchecked behavior.
- A duplicate posting identifier is rejected without overwriting the original financial facts.

These talking points record approved policies. Explaining and implementing their enforcement remains the next learning checkpoint, not an already-passed interview exercise.
