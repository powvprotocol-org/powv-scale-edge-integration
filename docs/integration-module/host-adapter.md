# Host Acquisition Adapter

## Purpose

The host acquisition adapter is the intermediary layer between the physical weighing instrument and the ESP32 edge receiver. This document describes its public functional behavior without publishing operational source code or proprietary parsing details.

## Responsibilities

The adapter is responsible for:

1. receiving a measurement response from the physical instrument;
2. interpreting the relevant measurement fields;
3. normalizing the measurement into a consistent representation;
4. constructing a structured digital event;
5. deriving a canonical representation of that event;
6. generating a SHA-256 integrity identifier;
7. transmitting the event to the edge receiver.

## Public acquisition lifecycle

```text
Acquire
  ↓
Interpret
  ↓
Normalize
  ↓
Construct event
  ↓
Canonicalize
  ↓
Hash
  ↓
Transmit
```

The acquisition and interpretation stages are intentionally described at a behavioral level. No raw device response, command, framing, parser, regular expression, port, or serial parameter is published.

## Normalization

Normalization establishes a consistent representation for values selected for the structured event. A public-safe normalization policy may include consistent units, explicit field names, defined timestamp representation, and controlled handling of absent values. The actual operational parser and device-specific rules remain undisclosed.

## Event construction

The adapter creates an event containing a measurement and selected contextual metadata. The event model is illustrative and is documented separately in [Event Model](event-model.md). No production schema, client data, or exact laboratory values are published here.

## Canonicalization and integrity identification

Before hashing, the event is converted into a deterministic representation according to an implementation-defined canonicalization policy. The policy conceptually addresses stable field ordering, consistent value representation, and exclusion of transport-specific metadata.

SHA-256 is then applied to the canonical representation to produce an integrity identifier. This identifier allows represented content to be compared for changes. It is not a digital signature, does not authenticate the host, and does not prove that the physical measurement is truthful.

## Non-operational illustration

The following pseudocode is intentionally abstract and is not executable:

```text
measurement = acquire_from_instrument()
normalized = normalize_measurement(measurement)
event = build_structured_event(normalized)
canonical = canonicalize(event)
integrity_id = sha256(canonical)
transmit(event, integrity_id)
```

The illustration does not specify a programming language, device protocol, transport endpoint, serialization library, parser, or deployment configuration.

## Trust role of the host

The host currently performs multiple trust-relevant transformations: acquisition, interpretation, event construction, hashing, and transmission. Hardware-backed attestation and independent edge-side verification are pending capabilities. The host-mediated nature of the path must therefore remain explicit in any public claim.

## Disclosure boundary

Operational Python, firmware, parser logic, raw frames, commands, serial settings, network details, credentials, keys, certificates, and reproducible integration instructions must not be added to this document.
