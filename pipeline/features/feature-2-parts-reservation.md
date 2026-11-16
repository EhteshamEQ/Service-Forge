# Feature 2 — Parts Reservation

| Field | Value |
|---|---|
| **Status** | Candidate-built / in progress |
| **One-line intent** | Let dispatchers reserve available parts inventory for booked jobs and release reservations when plans change. |
| **Depends on** | Feature 1 decision: **standard travel buffer per job is 45 minutes** (`pipeline/decisions/feature-1-decisions.md`). Reservations attach to Feature 1's booked jobs; this feature does not alter their scheduling or travel-buffer rules. |
| **In scope** | Maintain seeded, in-memory part quantities; calculate available stock after active reservations; reserve a positive whole-number quantity of one known part for an existing booked job; list a job's reservations; and release a reservation. |
| **Out of scope** | Purchasing, receiving or adjusting stock; supplier or warehouse management; low-stock alerts; automatic reservation release or transfer when a job is cancelled, rescheduled, or no longer needs a part; and persistent or external inventory systems. A dispatcher must release a reservation explicitly, then make a separate reservation to allocate that stock to another job. |
| **Definition of Done** | All acceptance criteria below pass; valid reservation and release operations update available quantities correctly; invalid and unavailable requests return clear errors without changing inventory; and automated tests cover the listed edge cases. |

## Artifacts this feature touches
<filled in as the developer and tester agents do their work>

## User stories

- As a dispatcher, I want to see a part's available quantity so that I can tell whether it can be committed to a job.
- As a dispatcher, I want to reserve a part for an existing booked job so that the required part is not promised to another job.
- As a dispatcher, I want to see the reservations for a job and release a reservation when plans change so that inventory availability stays accurate.
- As a dispatcher, I want a released part to become available inventory again so that it can be reserved for another job if needed.

## Acceptance criteria

1. A part's available quantity is its on-hand quantity minus the quantities of its active reservations; it must never be negative.
2. A request to reserve a positive whole-number quantity of a known part for an existing booked job creates an active reservation linked to that part and job, and reduces the part's available quantity by exactly that amount.
3. If the requested quantity exceeds the available quantity, the request is rejected with a clear conflict error; neither a reservation nor an inventory change is made.
4. A dispatcher can retrieve the reservations associated with a booked job, including each reservation's part, quantity, and active or released status.
5. Releasing an active reservation marks it released and restores its quantity to general availability exactly once; the part remains in on-hand inventory and is not automatically transferred to another job. Repeating the release does not restore stock again.
6. Unknown part, job, or reservation identifiers return a not-found error. Invalid quantities return a validation error. Failed requests do not change inventory or reservation state.
7. Part quantities and reservations are held in the existing in-memory mock-data approach and seeded at startup; no database or external inventory service is introduced.

## Edge cases

- Reject zero, negative, fractional, or otherwise invalid quantities without changing state.
- Reject a reservation when requested quantity exactly exceeds remaining availability by even one unit.
- Permit a reservation equal to the full available quantity, leaving availability at zero.
- Reject references to a job that does not exist or is not booked.
- Releasing an already released reservation must not restore its quantity a second time.
- If a job no longer needs a reserved part, releasing the reservation makes that quantity available to any job; reserving it for another job requires a separate explicit reservation.
- Releasing or reserving against an unknown identifier must not alter any other reservation or part quantity.
- A job cancellation or schedule change does not implicitly release its reservations; the dispatcher releases them explicitly.

## Dependencies on Feature 1

- Reservations require an existing booked job and use its identifier as the association.
- The **45-minute standard travel buffer per job** from Feature 1 remains authoritative for technician scheduling. Parts reservation neither recalculates nor changes job availability, booking conflicts, or that buffer.

## Success metrics

- 100% of valid in-stock reservation scenarios create a job-linked reservation and reduce availability by exactly the requested quantity.
- 100% of insufficient-stock and invalid-input scenarios leave inventory and reservation state unchanged.
- 100% of release scenarios restore availability exactly once, including repeated-release attempts.
- No tested workflow produces negative available inventory.
