# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!

## Task1
## Senario
we have given a payment service application which records set of operational observtions. Each record contains a timestamp, the service name, response time, CPU usage, memory usage, a log level, and a message.

## Operational Data Analysis
the repository have data/service_data.json file where hte data is stored. which contains the records. The dataset contains 10 records, with one-minute intervals.

## Task 2
## observations from the logs and metrics.
## normal behaviour
the behaviour is normal when:
- `2026-09-20T10:00:00` to `2026-09-20T10:04:00` 
- `2026-09-20T10:07:00` to `2026-09-20T10:09:00` 

These records show: 
- response time around 120-150 ms 
- CPU use around 42-50% 
- memory use around 51-57% 
- `INFO` log level 
- message: `Payment request processed successfully` 
### Unusual behaviour 
The unusual records are: 
- `2026-09-20T10:05:00` 
- `2026-09-20T10:06:00` 

These records show: 
- response time of 610 ms and 640 ms 
- CPU use of 75% and 94% 
- memory use of 70% and 91% 
- `ERROR` log level 
- messages about timeouts

### 1. Metrics fields
The numeric fields that represent operational metrics are:
-- response_time_ms : time taken to complete the request, in milliseconds.
-- cpu_percent: percentage of CPU used by the service.
-- memory_percent: percentage of memory used by the service.

### 2. Log information fields
 log information fields are:
-- service: which service emitted the record.
-- log_level : severity such as INFO or ERROR.
-- message: descriptive text about the event.

These fields describe the event context and the outcome written to logs, rather than numeric performance signals.

### 3. How timestamps are used
The `timestamp` field is ISO 8601 format and is recorded once per minute at 1-minute intervals, for example `2026-09-20T10:00:00` through `2026-09-20T10:09:00`.

This provides a time-ordered sequence for the service's behaviour, allowing the data to be correlated across metric changes and log events. The timeline shows a stable baseline followed by a short anomalous period and then a return to normal behaviour.

## Task 3
## 4. Anomaly detection findings 
The repository’s detection logic checks whether the metrics exceed the configured thresholds and whether the log level indicates an error. 
The anomalies detected are: 
1. `2026-09-20T10:05:00` 
   - response time: 610 ms 
   - CPU: 75% 
   - memory: 70% 
   - log level: `ERROR` 
   - reason: `High response time`, `Error log detected` 

2. `2026-09-20T10:06:00` 
   - response time: 640 ms 
   - CPU: 94% 
   - memory: 91% 
   - log level: `ERROR` 
   - reason: `High response time`, `High CPU utilization`, `High memory utilization`, `Error log detected` 
 
## 5. Event-processing flow 
The project uses a simple event-stream simulation made of these components: 
- `Event/message`: the anomaly object created after detection 
- `EventProducer`: sends the event to the topic 
- `EventTopic`: the in-memory message queue 
- `EventConsumer`: reads the event from the topic 
The flow is: 
Operational Data -> Anomaly Detection -> Event -> Producer -> Topic -> Consumer -> AIOps 
This is a lightweight version of a real AIOps pipeline: telemetry is processed, suspicious events are created, and the event is passed through the messaging layer so downstream systems can react to it. 

## 6. Final workflow result 
I executed the complete workflow with: 
```bash 
cd /workspaces/github-skills-challenge 
python -m src.aiops_pipeline 
``` 
The final result was: 
Records processed: 10 
Anomalies detected: 2 
Events consumed: 2 

The detected events were: 
- `2026-09-20T10:05:00` — `High response time`, `Error log detected` 
- `2026-09-20T10:06:00` — `High response time`, `High CPU utilization`, `High memory utilization`, `Error log detected` 
This confirms that the system successfully moved from raw operational data to a final AIOps anomaly report. 

## 7. Issues identified and corrected 
I found and fixed several problems in the workflow: 
1. Wrong log severity check 
   - Problem: the detector was checking for `WARNING` instead of `ERROR`. 
   - Fix: updated the detection logic to look for `ERROR`. 
2. Topic mismatch 
   - Problem: the producer published to one topic while the consumer read from another. 
   - Fix: both were aligned to the same `anomaly-events` topic. 
3. Script import issue 
   - Problem: module imports failed when the project was run as a script. 
   - Fix: added a safe import fallback and package initialization. 

## 8. Limitation / possible improvement 
This approach uses fixed thresholds for CPU, memory, and latency. That works for this small synthetic dataset, but in a real system, normal values can change based on traffic, time of day, or service type. 
A better version would use rolling baselines or service-specific thresholds instead of fixed values. 

## Event Flow Verification

I validated the repository’s lightweight event-stream workflow using the existing `AnomalyDetector`, `EventProducer`, `EventTopic`, and `EventConsumer` components.

### Component roles
- `Event/message`: the structured record generated by the detector when a metric/log anomaly is found.
- `EventTopic`: the in-memory topic used to store published events until they are consumed.
- `EventProducer`: publishes the anomaly event to the topic.
- `EventConsumer`: reads the queued event from the topic and makes it available to downstream processing.

### End-to-end verification
I executed the repository workflow and confirmed the following path:
1. The detector identified the abnormal records at `2026-09-20T10:05:00` and `2026-09-20T10:06:00`.
2. Each abnormal record produced an anomaly event.
3. The producer published the event to the `anomaly-events` topic.
4. The consumer received the published event from that topic.
5. The consumer processed the event and kept it in the downstream result set.
6. The processed result was reported by the AIOps pipeline as `Anomalies detected: 2` and `Events consumed: 2`.

This confirms the anomaly can travel through the complete event-processing pipeline in the provided simulation.

## 9. How to reproduce the demonstration 

Follow these steps: 
```bash 
cd /workspaces/github-skills-challenge 

python -m pytest -q 

python -m src.aiops_pipeline 
``` 
What to expect: 
- the tests should pass 
- the pipeline should process all 10 records 
- 2 anomalies should be detected 
- the events should be published and consumed 
- the final output should show the detected service issue 

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

