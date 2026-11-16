# Feature 1 — Technician Availability Calendar

| Field | Value |
|---|---|
| **Status** | Pre-baked — already built through the pipeline described in `pipeline/orchestration.md`; this is the worked reference, not something you build |
| **One-line intent** | Let a technician's job commitments be scheduled and checked for conflicts |
| **Depends on** | None — this is the root of the feature chain |
| **In scope** | Booking a technician to a job slot; listing a technician's jobs; rejecting bookings that overlap an existing one for the same technician |
| **Out of scope** | Parts, inventory, or stock of any kind — that starts with Feature 2 |
| **Definition of Done** | A technician can be booked for a job; a technician's jobs can be listed; overlapping bookings for the same technician are rejected with a clear conflict error |

## What's implemented

- `GET /api/technicians` — list all technicians (mock, seeded at startup)
- `GET /api/technicians/{id}/jobs` — a technician's booked jobs
- `POST /api/jobs` — book a new job for a technician

## Booking-conflict fix

`TechnicianAvailabilityService.bookJob(...)` rejects overlapping reservations for the same technician, including the 45-minute travel buffer. Reservation intervals use a half-open boundary, so a new job may start exactly when an existing job's travel buffer ends. Regression coverage is in `TechnicianAvailabilityServiceTest`.

## Artifacts this feature touches

- `backend/.../service/TechnicianAvailabilityService.java`
- `backend/.../controller/TechnicianController.java`, `JobController.java`
- `backend/.../model/Technician.java`, `Job.java`, `JobStatus.java`
- `backend/.../data/MockDataStore.java`
- `backend/src/test/java/com/serviceforge/service/TechnicianAvailabilityServiceTest.java`
- `pipeline/rules/technician-booking-conflicts.md`
