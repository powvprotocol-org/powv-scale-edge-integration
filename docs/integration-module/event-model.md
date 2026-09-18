# Event Model

## Purpose

This document defines the public conceptual event model for the PoWV Scale-to-Edge Integration Module. It describes event semantics without publishing a production schema or operational identifiers.

## Conceptual lifecycle

```text
Physical event
  ↓
Measurement acquisition
  ↓
Normalization
  ↓
Structured event construction
  ↓
Canonical representation
  ↓
SHA-256 integrity identification
  ↓
Controlled transport
  ↓
Edge receipt and inspection
```

## Illustrative event schema

The following JSON is a public, non-production model. Placeholders are intentional and do not represent real values or an operational payload.

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

## Field semantics

- `event_type` identifies the broad event category.
- `timestamp` represents the event time in a controlled, documented format.
- `device` contains logical classes or identifiers, not private operational identifiers.
- `measurement` contains the normalized weighing information.
- `commercial` represents optional derived context and is not required for physical acquisition.
- `location` represents optional source context without exposing private addresses.
- `integrity` identifies the digest algorithm and associated integrity identifier.

## Derived total

Where commercial context is used, a total may be derived conceptually from the normalized measurement and an applicable rate. This repository does not publish exact rates, test values, customer data, or commercial rules. A derived total is contextual application data, not evidence that the physical measurement is true.

## Timestamp semantics

A timestamp provides temporal context for the event representation. It does not, by itself, provide trusted time, device attestation, or an immutable record. Time synchronization and production time assurance are outside the scope of this public model.

## Canonicalization

Canonicalization creates a deterministic representation of the structured event before hashing. A public implementation may define stable field ordering, consistent numeric formatting, explicit encoding, and controlled treatment of optional values. The proprietary canonicalization procedure is not published here.

## Integrity semantics

The SHA-256 value identifies the content supplied to the hashing operation. It can reveal whether that represented content changed between comparison points. It does not provide a signature, authenticate the source, establish provenance, prove physical truth, or complete chain of custody.

## Edge lifecycle

After transport, the ESP32 acknowledges receipt and temporarily retains the latest event for local inspection. The current PoC does not claim durable audit anchoring, independent edge verification, or production persistence.

## Disclosure boundary

No real event payloads, exact measurements, real hashes, device identifiers, addresses, credentials, production schemas, source code, or operational serialization details are included in this document.
