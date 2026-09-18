````markdown
# Data Flow

The PoWV Scale-to-Edge Integration Module converts a physical weighing event into a structured digital event and transfers that event to an embedded edge node.

The current laboratory implementation connects a commercial weighing instrument, a host acquisition process, and an ESP32-based receiver over a local network.

## End-to-End Flow

```text
Physical Load
     ↓
Weighing Instrument
     ↓
Serial Measurement Response
     ↓
Host Acquisition Adapter
     ↓
Field Extraction
     ↓
Value Normalization
     ↓
Structured Event
     ↓
Canonical Serialization
     ↓
SHA-256 Digest
     ↓
Event + Integrity Metadata
     ↓
HTTP over Wi-Fi
     ↓
ESP32 Edge Receiver
     ↓
Application Acknowledgment
     ↓
Latest-Event State
     ↓
Local API / Dashboard
````

At the current PoC stage, the host performs acquisition, normalization, event construction, hashing, and transmission.

The ESP32 receives the structured event and provides the first embedded processing point in the integration path.

---

## 1. Physical Measurement

The flow begins with a real physical load applied to a commercial weighing instrument.

The instrument performs the physical measurement and produces its native device response.

```text
Physical load
     ↓
Instrument measurement
     ↓
Device response
```

The host does not generate the weight value. It receives a measurement originating from the physical instrument.

---

## 2. Host Acquisition

A host-side adapter receives the instrument response through the serial integration layer.

The acquisition stage is responsible for collecting the device response and making it available to the event-processing layer.

```text
Instrument
     ↓
Serial transport
     ↓
Host acquisition buffer
```

The device-specific communication mechanism remains isolated from the PoWV event representation.

This separation allows the upstream hardware interface to change without requiring the event model itself to become vendor-specific.

---

## 3. Field Extraction

The acquired response contains the measurement information required by the integration.

For the current weighing PoC, the relevant values include:

```text
Weight
Tare
Unit price
Derived total
```

The host adapter extracts these values from the instrument response and converts them into application-level fields.

Conceptually:

```text
Device representation
        ↓
Field extraction
        ↓
weight
tare
unit_price
```

---

## 4. Normalization

Values obtained from the instrument are normalized before event construction.

A measurement such as:

```text
Weight: 0.082 kg
Tare:   0.000 kg
Price:  200.00
```

is represented internally as numeric application data:

```python
weight_kg = 0.082
tare_kg = 0.0
price_per_kg = 200.0
```

Derived values are calculated at the application layer.

For example:

```python
total = round(weight_kg * price_per_kg, 2)
```

For the representative laboratory event:

```text
0.082 × 200.00 = 16.40
```

This produces a normalized measurement independent from the original device formatting.

---

## 5. Event Construction

The normalized values are converted into a structured event.

A representative event from the Scale-to-Edge PoC is:

```json
{
  "event_type": "weight_measurement",
  "timestamp": "2026-09-18T16:26:02",
  "device": {
    "instrument": "commercial_weighing_scale",
    "edge_node": "edge-node"
  },
  "measurement": {
    "weight_kg": 0.082,
    "tare_kg": 0.0
  },
  "commercial": {
    "price_per_kg": 200.0,
    "total": 16.4
  },
  "source": "physical_scale",
  "status": "captured"
}
```

The purpose of this stage is to transform a device-specific response into a structured representation that can be processed independently from the original instrument protocol.

---

## 6. Canonical Serialization

Before the integrity identifier is generated, the event is serialized deterministically.

The current host implementation uses a canonical JSON representation based on:

* deterministic key ordering;
* compact separators;
* UTF-8 encoding.

Conceptually:

```python
canonical_event = json.dumps(
    event,
    sort_keys=True,
    separators=(",", ":"),
    ensure_ascii=False
)
```

This ensures that the same logical event produces the same byte representation before hashing.

---

## 7. Integrity Identifier

The canonical event representation is processed with SHA-256.

```text
P = canonical structured event

H = SHA256(P)
```

A simplified implementation is:

```python
digest = hashlib.sha256(
    canonical_event.encode("utf-8")
).hexdigest()
```

The digest is then attached to the event:

```json
{
  "integrity": {
    "algorithm": "SHA-256",
    "hash": "<event-digest>"
  }
}
```

The resulting relationship is:

```text
Structured Event
      ↓
Canonical JSON
      ↓
SHA-256
      ↓
Integrity Identifier
```

The integrity field is appended after the digest of the original event representation is generated.

---

## 8. Transport to the Edge

Once the structured event contains its integrity metadata, it is transmitted from the host to the ESP32 edge receiver.

The current implementation uses HTTP over Wi-Fi inside the laboratory network.

```text
Host Adapter
     │
     │ JSON event
     │ HTTP
     ▼
Wi-Fi Network
     │
     ▼
ESP32 Edge Receiver
```

At this point, the data has crossed from the host-side software environment into an independent embedded node.

---

## 9. Edge Receipt

The ESP32 accepts the incoming structured event at the application layer.

The current receiver performs three immediate actions:

```text
Receive event
     ↓
Retain latest event
     ↓
Return acknowledgment
```

A representative acknowledgment is:

```json
{
  "received": true,
  "device": "edge-node"
}
```

This response confirms that the event reached the embedded application.

---

## 10. Latest-Event State

The edge receiver maintains the latest successfully received event in runtime state.

Conceptually:

```text
Event N-1
    ↓
Current State

New Event N arrives
    ↓
Current State = Event N
```

This provides a simple state model for integration testing and local observability.

The current PoC retains the latest event rather than implementing a persistent event ledger at the edge.

---

## 11. Local Retrieval

A client connected to the laboratory network can request the latest event directly from the ESP32.

```text
Local Client
     │
     │ Request
     ▼
ESP32
     │
     │ Latest structured event
     ▼
Local Client
```

This provides a direct way to confirm that the event received from the physical measurement pipeline is present at the embedded node.

---

## 12. Dashboard Rendering

The same event can be rendered through a lightweight interface hosted by the ESP32.

The current dashboard exposes selected fields such as:

* measured weight;
* tare;
* price per unit;
* calculated total;
* event timestamp;
* device metadata;
* integrity algorithm;
* integrity digest;
* structured event payload.

The dashboard automatically retrieves the latest event from the edge receiver and updates the displayed state.

The dashboard is therefore an observability layer over the edge event state rather than a separate data-processing component.

---

## Event Lifecycle

The complete current lifecycle can be summarized as:

```text
1. Physical event occurs
2. Instrument produces measurement
3. Host acquires instrument response
4. Relevant fields are extracted
5. Values are normalized
6. Structured event is constructed
7. Event is serialized deterministically
8. SHA-256 digest is generated
9. Integrity metadata is attached
10. Event is transmitted over Wi-Fi
11. ESP32 receives event
12. ESP32 acknowledges receipt
13. Latest event is retained
14. Event becomes available through the local API
15. Dashboard renders the edge state
```

---

## Processing Responsibility

| Stage                      | Current Component           |
| -------------------------- | --------------------------- |
| Physical measurement       | Weighing instrument         |
| Device communication       | Instrument / host interface |
| Response acquisition       | Host adapter                |
| Field extraction           | Host adapter                |
| Value normalization        | Host adapter                |
| Event construction         | Host adapter                |
| Canonical serialization    | Host adapter                |
| SHA-256 generation         | Host adapter                |
| Network transmission       | Host adapter                |
| Event receipt              | ESP32                       |
| Application acknowledgment | ESP32                       |
| Latest-event state         | ESP32                       |
| Event retrieval            | ESP32                       |
| Dashboard rendering        | ESP32 / local browser       |

This table represents the current laboratory implementation rather than the final target architecture.

---

## PoWV Representation

Within the PoWV conceptual model, the current Scale-to-Edge path can be represented as:

```text
E → M → P → H → Edge
```

Where:

| Symbol | Stage                                   |
| ------ | --------------------------------------- |
| `E`    | Physical weighing event                 |
| `M`    | Measurement produced by the instrument  |
| `P`    | Structured digital event                |
| `H`    | SHA-256 integrity identifier            |
| `Edge` | Transport and receipt by the ESP32 node |

The next architectural stages extend this flow toward:

```text
E → M → P → σ → V → H → A → I
```

with cryptographic attestation, independent verification, audit anchoring, and downstream interpretation introduced as separate capabilities.

---

## Current Implementation Boundary

The current implementation has successfully demonstrated:

```text
Real Physical Measurement
          ↓
Structured Digital Event
          ↓
SHA-256 Integrity Identifier
          ↓
Network Transport
          ↓
Embedded Event Receipt
          ↓
Local Inspection
```

The next technical milestone is to move the ESP32 from event receipt to active verification by independently recalculating the integrity digest of the received event and comparing it with the transmitted identifier.

That transition changes the edge role from:

```text
Receive(P + H)
```

to:

```text
Receive(P + H)
      ↓
Calculate SHA256(P)
      ↓
Compare calculated H with received H
      ↓
Produce verification result
```
This establishes the next validation layer in the Scale-to-Edge integration path.

