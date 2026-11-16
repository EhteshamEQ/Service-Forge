# Technician booking conflicts

- Check technician booking conflicts using time intervals, not equality of start times.
- A job's reserved interval begins at its start time and ends at its scheduled end plus `TRAVEL_BUFFER_MINUTES`.
- Treat reservation intervals as half-open: a new job may begin exactly when an existing reservation ends.
- Use the `TRAVEL_BUFFER_MINUTES` constant as the source of truth and cover interval overlap, travel-buffer conflicts, and the exact boundary with regression tests.
