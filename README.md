# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!

# Scenario
This project monitors a service using operational data such as response time,
error rate, CPU usage, and request volume.

The operational problem is identifying abnormal service behavior early,
especially conditions that may indicate performance degradation, elevated
errors,
or an approaching service outage.

AIOps is used in this assessment to analyze operational data, detect anomalies,
generate events, and pass those events through a producer, topic, and consumer
workflow for further processing.

## Task 3 Detection Analysis

The provided detector processed all 10 operational records. It classified 8
records as normal and detected 2 anomalies:

- `2026-09-20T10:05:00`: response time was `610 ms`, above the `500 ms`
	threshold. The record also contained an `ERROR` log with the message
	`Payment service timeout`.
- `2026-09-20T10:06:00`: response time was `640 ms`, CPU was `94%`, and memory
	was `91%`, exceeding the configured thresholds. The record also contained an
	`ERROR` log with the message `Database connection timeout`.

The remaining 8 records had normal metric values and `INFO` logs, so none were
incorrectly flagged. The detector now reports both concerning `ERROR` log
events as anomaly reasons. A remaining limitation is that the fixed thresholds
could be supplemented with service baselines or trend-based detection.

## Task 4 Event Flow Verification

The event workflow was first run with the original wiring. The detector found
2 anomalies, but the consumer received 0 events because it was connected to a
different topic than the producer.

The pipeline was corrected so the producer and consumer share the same
`service-events` topic. The verified flow is:

1. The detector creates an anomaly event from abnormal operational data.
2. The producer publishes the event to the topic.
3. The topic stores the event message.
4. The consumer reads the event from that topic.
5. The pipeline prints the consumed event as downstream AIOps output.

The final execution processed 10 records, detected 2 anomalies, and consumed 2
events. The producer publishes messages, the topic stores and returns messages,
the consumer reads messages, and each event contains the service, timestamp,
event type, reasons, and source record.

## Task 5 Workflow Troubleshooting

Two workflow problems were identified and corrected:

- `src/anomaly_detector.py`: the log rule checked only `WARNING`, so the
	supplied `ERROR` records were missing the log reason. The rule now recognizes
	both `WARNING` and `ERROR`; the pipeline output confirms `Error log detected`
	for both anomalies.
- `src/aiops_pipeline.py`: the consumer originally used a separate topic from
	the producer, so no events were consumed. The consumer now uses the
	producer's `service-events` topic, and the pipeline verifies 2 events
	consumed from 2 anomalies.

The existing producer, topic, consumer, and event structures were preserved.
Focused pipeline tests and the complete test suite pass after both corrections.

## Task 6 End-to-End Execution

The completed pipeline was verified in the following order:

1. 10 operational records were processed.
2. 2 anomalous observations were detected.
3. 2 anomaly events were generated.
4. The producer published 2 events to `service-events`.
5. The consumer received 2 events from the topic.
6. The consumed events matched the generated events.
7. The final output identified the `payment-service` timeout and resource
	utilization issues at `10:05` and `10:06`.

The end-to-end result was successful: `Operational Data -> Anomaly Detection ->
Event -> Producer -> Topic -> Consumer -> AIOps`.


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

