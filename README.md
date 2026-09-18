# PoWV Scale-to-Edge Integration Module

The PoWV Scale-to-Edge Integration Module (PoWV-S2E) is a laboratory integration layer designed to bridge a real physical measurement source, a host-side acquisition layer, and a networked embedded edge receiver.

## Purpose and scope

This module records the public-safe technical boundary of a laboratory proof of concept conducted on 2026-09-18. It documents the functional path from a physical weighing event through measurement normalization, structured event construction, integrity identification, controlled local transport, embedded receipt, and local inspection.

The module does not publish proprietary firmware, device protocol details, operational parameters, credentials, cryptographic keys, raw instrument output, or implementation material that could facilitate reverse engineering.

## Public functional path

```text
Physical Event
→ Measurement
→ Structured Event
→ Integrity Identifier
→ Edge Transport
```

The more detailed path is:

```text
Physical weighing event
→ Physical weighing instrument
→ Host-side acquisition layer
→ Structured digital event
→ SHA-256 integrity identifier
→ Controlled local network transport
→ ESP32 edge receiver
→ Application-level acknowledgment
→ Local inspection interface
```

A host remains between the physical instrument and the ESP32. The current implementation is therefore not a direct instrument-to-edge path.

## PoWV relationship

The public conceptual mapping is:

```text
E → M → P → H → edge transport
```

Where E is the physical event, M is the measurement, P is the structured digital representation, and H is the SHA-256 integrity identifier.

The PoC does not claim that SHA-256 is a signature, proves physical truth, establishes authenticity, or completes a trustless physical-to-digital chain.

## Demonstrated capabilities

- Real physical weight measurement acquisition.
- Extraction and normalization of event fields.
- Structured digital event construction.
- Timestamped event representation.
- SHA-256 integrity identifier generation.
- Controlled local network transport.
- ESP32 event receipt and application-level acknowledgment.
- Retrieval of the latest event.
- Lightweight local dashboard inspection.

## Not yet demonstrated as complete

- Hardware-backed cryptographic attestation or signature.
- Independent cryptographic verification at the edge.
- Secure-element key operations.
- Authenticated device identity.
- Direct instrument-to-edge acquisition.
- Durable audit anchoring.
- Production-grade chain of custody.
- Downstream interpretation or business decision automation.

## Maturity

**Functional laboratory PoC for physical measurement acquisition, structured event construction, integrity hashing, controlled local network transport, and embedded receipt.**

This is an engineering record, not a production specification, security certification, or production-readiness claim.

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
