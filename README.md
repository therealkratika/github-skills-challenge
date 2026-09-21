# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!

## Operational Data Analysis

The repository contains a small synthetic dataset for one service, with each record representing a single observation at a point in time.

### 1. Metrics fields
The numeric fields that represent operational metrics are:
- `response_time_ms`: time taken to complete the request, in milliseconds.
- `cpu_percent`: percentage of CPU used by the service.
- `memory_percent`: percentage of memory used by the service.

These values vary over time and are useful for detecting performance and resource pressure.

### 2. Log information fields
The fields that represent log information are:
- `service`: which service emitted the record.
- `log_level`: severity such as `INFO` or `ERROR`.
- `message`: descriptive text about the event.

These fields describe the event context and the outcome written to logs, rather than numeric performance signals.

### 3. How timestamps are used
The `timestamp` field is ISO 8601 format and is recorded once per minute at 1-minute intervals, for example `2026-09-20T10:00:00` through `2026-09-20T10:09:00`.

This provides a time-ordered sequence for the service's behaviour, allowing the data to be correlated across metric changes and log events. The timeline shows a stable baseline followed by a short anomalous period and then a return to normal behaviour.

### 4. Normal behaviour
Observations that appear normal are the records from:
- `2026-09-20T10:00:00` to `2026-09-20T10:04:00`
- `2026-09-20T10:07:00` to `2026-09-20T10:09:00`

These records show:
- response times between roughly 120 ms and 150 ms
- CPU usage between 42% and 50%
- memory usage between 51% and 57%
- `INFO` level logs
- messages stating `Payment request processed successfully`

This pattern is consistent with steady, healthy operation.

### 5. Unusual behaviour
The unusual observations are:
- `2026-09-20T10:05:00`: `response_time_ms` = 610, `cpu_percent` = 75, `memory_percent` = 70, `log_level` = `ERROR`, message = `Payment service timeout`
- `2026-09-20T10:06:00`: `response_time_ms` = 640, `cpu_percent` = 94, `memory_percent` = 91, `log_level` = `ERROR`, message = `Database connection timeout`

These values are markedly above the normal baseline and match typical failure or degradation signals: severe latency, CPU saturation, memory pressure, and error-level logs. The service returns to normal in the following minutes, indicating a brief incident rather than a sustained problem.

Overall, the dataset shows a clear baseline pattern of healthy operation interrupted by a short spike in latency and resource exhaustion associated with timeout errors.

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

