
# Data Flow

The PoWV Scale-to-Edge Integration Module establishes a working data path between a physical weighing instrument, a host-side acquisition layer, and an ESP32-based edge receiver.

The current laboratory implementation demonstrates the transition of a real physical measurement into a structured digital event, the generation of an integrity identifier, transmission over a local network, and receipt by an embedded node.

## End-to-End Flow

**Physical Load → Weighing Instrument → Serial Interface → Host Acquisition Adapter → Field Extraction → Value Normalization → Structured Event → Canonical Serialization → SHA-256 → HTTP over Wi-Fi → ESP32 Edge Receiver → Application Acknowledgment → Local API → Dashboard**

This sequence represents the current functional implementation of the Scale-to-Edge PoC.

The host performs the acquisition and transformation stages. The ESP32 operates as the first embedded receiver of the normalized event.

---

## Physical Measurement

The process begins with a real physical load applied to a commercial weighing instrument.

The instrument performs the measurement and produces the corresponding device response. The weight value therefore originates from the physical measuring equipment rather than from the host application.

At this stage, the relevant relationship is:

**Physical Load → Instrument Measurement → Device Response**

The PoWV integration begins after the physical instrument has produced the measurement.

---

## Instrument-to-Host Acquisition

The weighing instrument is connected to the host environment through a serial communication interface.

The host-side acquisition adapter receives the instrument response and makes it available to the processing layer.

The current relationship is:

**Measurement Instrument → Serial Interface → Host Acquisition Adapter**

The serial layer is responsible for transporting the instrument response into the host environment. The host then converts that device-specific representation into application-level information.

This separation is important because the physical device interface and the PoWV event model serve different purposes.

The instrument speaks its native interface. The PoWV integration consumes normalized event data.

---

## Field Extraction

The acquired device response contains the measurement information required by the integration.

For the current Scale-to-Edge PoC, the relevant values include:

| Field | Role |
|---|---|
| Weight | Physical measurement produced by the instrument |
| Tare | Tare associated with the weighing event |
| Unit price | Optional commercial context |
| Total | Derived commercial value |

The host adapter extracts these values from the device response and converts them into software-level fields.

The logical transformation is:

**Device Response → Field Extraction → Application Values**

At this stage, device-specific formatting is removed from the event-processing path.

---

## Value Normalization

Values obtained from the instrument are normalized before event construction.

A representative laboratory measurement may be interpreted as:

| Value | Normalized Representation |
|---|---:|
| Weight | `0.082 kg` |
| Tare | `0.000 kg` |
| Price per kilogram | `200.00` |
| Derived total | `16.40` |

Inside the application layer, these values are represented numerically rather than as formatted device strings.

For example:

`weight_kg = 0.082`

`tare_kg = 0.0`

`price_per_kg = 200.0`

The commercial total is derived from the normalized values:

`total = weight_kg × price_per_kg`

For the representative event:

`0.082 × 200.00 = 16.40`

The result is a device-independent representation that can be used by the event layer.

---

## Structured Event Construction

After normalization, the host creates a structured representation of the physical event.

The current event model includes measurement data, contextual metadata, source information, processing status, and integrity metadata.

A representative structure is:

| Field | Example | Function |
|---|---|---|
| `event_type` | `weight_measurement` | Identifies the event class |
| `timestamp` | `2026-09-18T16:26:02` | Records event construction time |
| `device.instrument` | `commercial_weighing_scale` | Identifies the instrument class |
| `device.edge_node` | `edge-node` | Identifies the logical edge destination |
| `measurement.weight_kg` | `0.082` | Normalized physical measurement |
| `measurement.tare_kg` | `0.0` | Tare associated with the event |
| `commercial.price_per_kg` | `200.0` | Optional commercial value |
| `commercial.total` | `16.4` | Derived commercial result |
| `source` | `physical_scale` | Identifies the source class |
| `status` | `captured` | Indicates event state |
| `integrity.algorithm` | `SHA-256` | Digest algorithm |
| `integrity.hash` | `<event-digest>` | Integrity identifier |

The structured event becomes the primary data object transported through the remainder of the current PoC.

This step separates the logical event from the original device-specific representation.

---

## Event Representation

Within the Scale-to-Edge module, the physical measurement is progressively transformed through a series of representations.

The current path can be expressed as:

**Physical Event → Device Measurement → Normalized Values → Structured Event**

Using the PoWV conceptual notation:

**E → M → P**

Where:

| Symbol | Meaning |
|---|---|
| `E` | Physical event |
| `M` | Measurement produced by the instrument |
| `P` | Structured digital representation |

The structured event is the point at which the measurement becomes independent from the original instrument formatting.

---

## Canonical Serialization

Before the integrity identifier is calculated, the structured event is converted into a deterministic representation.

The current implementation uses canonical JSON serialization.

The canonicalization process establishes consistent:

- key ordering;
- field representation;
- separators;
- UTF-8 encoding.

This is necessary because hashing operates on bytes rather than on the abstract meaning of a JSON object.

Two logically equivalent JSON objects may otherwise produce different hashes if their serialized byte representations differ.

The current relationship is:

**Structured Event → Canonical Representation**

Conceptually:

`P = CanonicalJSON(event)`

The canonical representation becomes the direct input to the integrity function.

---

## SHA-256 Integrity Identifier

The canonical event representation is processed using SHA-256.

The relationship is:

`H = SHA256(P)`

Where:

- `P` is the canonical event representation;
- `H` is the resulting SHA-256 digest.

The digest is then associated with the structured event through the integrity metadata.

The current processing sequence is:

**Structured Event → Canonical Serialization → SHA-256 → Integrity Identifier**

The integrity identifier provides a deterministic reference to the digital event representation used during the hashing step.

If that representation changes, the resulting digest changes as well.

---

## Integrity Metadata

After the digest is generated, integrity information is appended to the event.

The integrity section contains:

| Field | Function |
|---|---|
| `algorithm` | Identifies the digest algorithm |
| `hash` | Stores the resulting event digest |

The current implementation therefore produces an event containing both the normalized measurement and its associated integrity identifier.

The resulting logical structure is:

**P + H**

where `P` represents the structured event and `H` represents the SHA-256 integrity identifier.

---

## Host Processing Pipeline

The host is currently responsible for the majority of the event-construction pipeline.

Its responsibilities include:

1. acquiring the instrument response;
2. extracting relevant measurement fields;
3. normalizing numerical values;
4. calculating derived values;
5. constructing the structured event;
6. assigning the event timestamp;
7. serializing the event deterministically;
8. generating the SHA-256 digest;
9. attaching integrity metadata;
10. transmitting the resulting event to the edge node.

The current host pipeline can therefore be summarized as:

**Acquire → Extract → Normalize → Structure → Canonicalize → Hash → Transmit**

The host remains an intermediary in the current laboratory architecture.

---

## Transport to the Edge

After event construction and integrity processing, the complete event is transmitted from the host environment to the ESP32 edge receiver.

The current laboratory transport uses HTTP over Wi-Fi.

The logical network path is:

**Host Adapter → HTTP → Wi-Fi Network → ESP32 Edge Receiver**

The transported object is the structured event rather than the original serial response produced by the weighing instrument.

This establishes an architectural separation between:

**Instrument-side communication**

and

**Edge-side event transport**

The edge receiver therefore does not need to process the native representation generated by the physical scale in the current implementation.

---

## Edge Receipt

The ESP32 receives the structured event at the application layer.

When a valid event submission reaches the receiver, the current implementation performs three primary operations:

1. accepts the incoming event;
2. retains it as the latest runtime event;
3. returns an application-level acknowledgment.

The edge processing sequence is:

**Receive Event → Store Latest Event → Return Acknowledgment**

This establishes that the structured event has crossed from the host software environment into an embedded node.

---

## Application-Level Acknowledgment

After successful receipt, the ESP32 returns a response confirming that the embedded application accepted the event.

A representative response contains:

| Field | Example |
|---|---|
| `received` | `true` |
| `device` | `edge-node` |

The acknowledgment confirms successful application-level delivery.

It represents the completion of the current host-to-edge transport cycle.

---

## Latest-Event Runtime State

The ESP32 maintains the most recently received event in runtime memory.

The state model is intentionally simple:

**Previous Event → New Event Received → Latest Event Replaced**

If Event N arrives after Event N-1, Event N becomes the current event exposed by the receiver.

This provides immediate observability during integration testing and allows the current embedded state to be inspected independently from the host acquisition interface.

The current implementation is focused on latest-event state rather than persistent event history.

---

## Local Event Retrieval

The latest received event can be retrieved directly from the ESP32 through its local interface.

The interaction is:

**Local Client → ESP32 → Latest Structured Event**

This provides a second observation point for the event.

The event is first observable on the host after acquisition and later observable independently at the embedded receiver after network transmission.

The ability to retrieve the same structured event from the ESP32 confirms that the event has traversed the host-to-edge path.

---

## Dashboard Rendering

The ESP32 also provides a lightweight local dashboard for human-readable inspection.

The dashboard presents selected fields from the latest structured event, including:

- weight;
- tare;
- price per unit when available;
- calculated total;
- event timestamp;
- device metadata;
- location context;
- integrity algorithm;
- integrity digest;
- structured event information.

The dashboard periodically retrieves the latest event from the embedded receiver and updates the displayed state.

The visualization layer does not generate a separate measurement. It renders the event already held by the edge node.

The relationship is therefore:

**ESP32 Event State → Local API → Browser Dashboard**

---

## Complete Event Lifecycle

The current Scale-to-Edge event lifecycle consists of the following stages:

1. A physical weighing event occurs.
2. The weighing instrument generates the measurement.
3. The instrument response reaches the host through the serial interface.
4. The host acquisition adapter collects the response.
5. Relevant measurement fields are extracted.
6. Values are converted into normalized application data.
7. Derived values are calculated.
8. A structured event is created.
9. A timestamp is associated with the event.
10. The event is serialized into a deterministic representation.
11. SHA-256 is calculated over the canonical event representation.
12. The resulting digest is attached as integrity metadata.
13. The event is transmitted from the host over HTTP and Wi-Fi.
14. The ESP32 receives the event.
15. The edge node stores the latest event in runtime state.
16. The ESP32 returns an application-level acknowledgment.
17. The latest event becomes available through the local interface.
18. The dashboard retrieves and displays the current edge event.

This represents the complete data flow validated in the current laboratory implementation.

---

## Component Responsibility Matrix

| Stage | Responsible Component |
|---|---|
| Physical load | External physical process |
| Weight measurement | Weighing instrument |
| Native device response | Weighing instrument |
| Serial transport | Instrument / host interface |
| Response acquisition | Host adapter |
| Field extraction | Host adapter |
| Value normalization | Host adapter |
| Derived value calculation | Host adapter |
| Event construction | Host adapter |
| Timestamp assignment | Host adapter |
| Canonical serialization | Host adapter |
| SHA-256 generation | Host adapter |
| Integrity metadata assembly | Host adapter |
| Network transmission | Host adapter |
| Event receipt | ESP32 |
| Application acknowledgment | ESP32 |
| Latest-event retention | ESP32 |
| Event retrieval | ESP32 |
| Dashboard data delivery | ESP32 |
| Human-readable rendering | Local browser |

This responsibility model describes the current PoC implementation rather than the final intended architecture.

---

## Data Transformation Model

The transformation performed by the current module can be summarized in four major stages.

### Physical Domain

A real-world load is converted into a measurement by the weighing instrument.

**Physical Load → Measurement**

### Acquisition Domain

The instrument representation is acquired and converted into normalized application values.

**Measurement → Host Acquisition → Normalized Values**

### Event Domain

Normalized values are transformed into a structured event and associated with an integrity identifier.

**Normalized Values → Structured Event → SHA-256**

### Edge Domain

The resulting event is transported to an embedded receiver and made available for local inspection.

**Event + Integrity Identifier → Network Transport → ESP32 → Inspection**

Together, these stages establish the current physical-to-edge path.

---

## PoWV Conceptual Mapping

The implementation corresponds to the following partial PoWV path:

**E → M → P → H → Edge**

| Symbol | Current Implementation |
|---|---|
| `E` | Physical weighing event |
| `M` | Measurement generated by the weighing instrument |
| `P` | Structured event constructed by the host |
| `H` | SHA-256 digest generated from the canonical representation |
| `Edge` | ESP32 receipt, retention, acknowledgment, and inspection |

This laboratory stage establishes a working path from physical measurement to embedded event receipt.

The broader PoWV model extends beyond the current Scale-to-Edge implementation:

**E → M → P → σ → V → H → A → I**

Where:

| Symbol | Function |
|---|---|
| `σ` | Cryptographic attestation |
| `V` | Verification |
| `H` | Integrity identifier |
| `A` | Audit anchoring or evidence registration |
| `I` | Interpretation or downstream decision |

The current Scale-to-Edge PoC primarily validates the path through measurement acquisition, event representation, integrity identification, transport, and edge receipt.

---

## Current Integrity Position

The SHA-256 digest is currently calculated by the host before the event reaches the ESP32.

The current relationship is:

**Host: P → SHA256(P) → H**

followed by:

**ESP32: Receive P + H**

The edge node therefore receives both the event representation and its integrity metadata.

At the current stage, the ESP32 is an event receiver rather than an independent integrity-verification authority.

---

## Next Edge Verification Stage

The next technical step is independent digest verification at the edge.

The intended processing model is:

**Receive P + H → Recalculate SHA256(P) → Compare Local Digest with Received H → Produce Verification Result**

This changes the role of the ESP32 from passive receipt of integrity metadata to active verification of the digital event representation.

The resulting relationship becomes:

`H_received = H_local`

when the event received by the ESP32 produces the same digest as the identifier generated upstream.

This is the next validation milestone for the Scale-to-Edge module.

---

## Engineering Significance

The current implementation demonstrates that a real physical measurement can move through multiple technical domains without remaining tied to the original device representation.

The measurement begins as an instrument-specific physical observation and becomes:

**Physical Measurement → Normalized Data → Structured Event → Integrity-Identified Event → Network Message → Embedded Runtime State**

This transition is significant because it creates a stable event boundary between physical instrumentation and downstream edge processing.

The weighing instrument is responsible for producing the measurement.

The host is responsible for adapting that measurement into the current PoWV event representation.

The ESP32 is responsible for receiving the resulting event and exposing it at the embedded layer.

The Scale-to-Edge module therefore provides the integration foundation for subsequent work on edge verification, device identity, cryptographic attestation, secure hardware, persistent evidence handling, and downstream audit infrastructure.

