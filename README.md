# PoWV Scale-to-Edge Integration Module

This repository documents the public technical record of the PoWV Scale-to-Edge (PoWV-S2E) integration laboratory proof of concept conducted on 2026-09-18.

The purpose of this repository is to describe, in a public-safe form, the implemented path from a physical weighing event to a networked ESP32-based edge receiver without exposing proprietary implementation details, operational parameters, or material that could facilitate reverse engineering.

> This repository is a public engineering record, not a production specification, not a security certification, and not an authorization for reverse engineering.

## Scope

This module documents the validated public boundary of a laboratory integration flow in which:

- a physical weighing event is acquired;
- the measurement is normalized into a structured event;
- a SHA-256 integrity identifier is generated;
- the event is transported over a controlled local network;
- the ESP32 edge receiver accepts and records the event;
- a local inspection interface retrieves and displays the most recent event.

The current boundary is intentionally limited to functional validation and transparent disclosure. It does not claim a complete cryptographic trust chain, direct instrument attestation, or production-grade chain of custody.

## Public flow

```text
Physical weighing event
→ Instrument acquisition
→ Structured event representation
→ SHA-256 integrity identifier
→ Controlled local network transport
→ ESP32 edge receiver
→ Local inspection interface
```

## Conceptual mapping

```text
E → M → P → H → Edge
```

Where:

- E = physical event
- M = measurement
- P = structured digital representation
- H = SHA-256 integrity identifier

This mapping is public and operationally meaningful. It does not imply that the full PoWV trust model is complete. The current design remains a functional boundary for measurement acquisition and local edge receipt.

## Current status

Validated at laboratory PoC level:

- real physical weight measurement acquisition;
- structured event construction;
- SHA-256 integrity identifier generation;
- local network transport;
- event receipt by an ESP32 edge node;
- application-level acknowledgment;
- retrieval of the most recent event;
- local visualization in an inspection interface.

Not yet claimed as completed:

- hardware-backed cryptographic attestation;
- independent edge-side verification;
- secure-element key operations;
- direct instrument-to-edge acquisition without a host intermediary;
- durable audit anchoring;
- production-grade chain of custody;
- interpretation or business decision automation.

## Trust boundary

```text
[Physical Domain]
     Instrument
          |
          | Trust Boundary 1
          v
      Host acquisition layer
          |
          | Trust Boundary 2
          v
     Local network transport
          |
          | Trust Boundary 3
          v
    ESP32 edge receiver
```

The current laboratory implementation does not yet establish a hardware-rooted, end-to-end trust path from the physical instrument to the edge receiver. A host remains in the path between the instrument and the edge node.

## Documentation

### Scale-to-Edge Integration Module

- [Module Overview](docs/integration-module/overview.md)
- [Architecture](docs/integration-module/architecture.md)
- [Data Flow](docs/integration-module/data-flow.md)
- [Interfaces](docs/integration-module/interfaces.md)
- [Trust Boundaries](docs/integration-module/trust-boundaries.md)
- [Validation Status](docs/integration-module/validation.md)
- [Experimental Record](docs/integration-module/experimental-record.md)

### Repository Governance

- [Disclosure Boundary](docs/disclosure-boundary.md)
- [Notice](NOTICE.md)

## Public-safe framing

This repository intentionally documents only what was directly validated in the laboratory environment and what can be discussed without exposing proprietary implementation details.

It does not include, and it does not permit publication of:

- raw instrument output;
- operational serial parameters;
- Wi-Fi credentials or SSID details;
- private IPs, endpoints, or topology;
- control bytes, framing, firmware, or parser logic;
- Python or embedded implementation code;
- secret keys, certificates, or secure-element configuration;
- any detail enabling reproduction of the proprietary integration path.

## Repository status

Public technical record of a functional laboratory PoC for physical measurement acquisition, structured event construction, integrity hashing, local network transport, and embedded receipt.
