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
incorrectly flagged. Both concerning `ERROR` log events were present in
anomalous records, but the detector did not report them as reasons because its
log rule only recognizes `WARNING`. This is an expected detection limitation:
the log rule should also consider `ERROR` events, and the fixed thresholds
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


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

