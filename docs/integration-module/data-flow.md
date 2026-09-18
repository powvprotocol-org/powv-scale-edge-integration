# Data Flow

## Conceptual flow

```text
E → M → P → H → Edge
```

- **E — Physical event:** a real-world weighing action.
- **M — Measurement:** the measurement acquired from the physical instrument.
- **P — Structured representation:** normalized digital data representing the measurement and selected event context.
- **H — Integrity identifier:** a SHA-256 digest associated with the structured representation.
- **Edge:** controlled transport to the embedded receiver and local inspection.

In this PoC, the functional path from E through H and edge transport was demonstrated. The host remains responsible for the intermediary transformations.

## Event lifecycle

1. A physical weighing event occurs.
2. The host acquisition layer obtains the instrument response.
3. Relevant fields are interpreted and normalized.
4. A structured event is constructed.
5. A SHA-256 integrity identifier is calculated for the event representation.
6. The structured event is transported over a controlled local network.
7. The ESP32 receives the event and returns an application-level acknowledgment.
8. The latest event is retained temporarily for local retrieval.
9. A lightweight local interface presents the event for inspection.

## Illustrative event model

The following is a public, non-production illustration. It is not a copy of an operational schema and does not describe implementation details.

```json
{
  "event_type": "weight_measurement",
  "timestamp": "<timestamp>",
  "device": {
    "instrument": "<instrument class>",
    "edge_node": "<logical edge identifier>"
  },
  "measurement": {
    "weight_kg": "<measurement>",
    "tare_kg": "<tare>"
  },
  "commercial": {
    "price_per_kg": "<optional>",
    "total": "<derived>"
  },
  "location": {
    "source": "<configured-or-sensor-derived>"
  },
  "integrity": {
    "algorithm": "SHA-256",
    "hash": "<digest>"
  }
}
```

The placeholders are intentional. No exact laboratory values, raw instrument data, real identifiers, addresses, or operational fields are published.

## Interpretation limits

SHA-256 identifies the content presented to the hashing step. It does not prove that the physical event occurred as represented, that the instrument was trustworthy, or that the host did not alter the event before hashing. Hardware-backed attestation, independent edge verification, durable audit anchoring, and downstream interpretation remain pending.
