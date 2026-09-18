# PoWV Scale-to-Edge Integration PoC

A public-safe technical record of a laboratory proof of concept validating the path from a real physical weighing event to a networked embedded edge receiver.

> This repository intentionally documents observable capabilities and validation status only.
> It does not disclose proprietary firmware, serial protocol details, credentials, keys, protected parsing logic, raw device framing, operational addresses, or implementation material that could facilitate reverse engineering.

## Demonstrated path

Physical weighing event
→ host-side acquisition
→ structured event representation
→ SHA-256 integrity identifier
→ local network transport
→ ESP32 edge receiver
→ local inspection interface

## Current validation status

Validated:

- acquisition of a real physical weight measurement;
- structured event generation;
- integrity hashing;
- Wi-Fi transport to an ESP32;
- successful application-layer acknowledgment;
- local retrieval of the most recent event;
- lightweight embedded visualization.

Not yet claimed as validated:

- hardware-backed signing;
- independent edge verification;
- secure-element key operations;
- direct scale-to-edge acquisition;
- durable audit anchoring;
- production-grade chain of custody.

## Objective

This repository documents the public-facing technical boundary of a validation exercise focused on proving that a physical measuring event can be captured, represented in a structured form, integrity-identified, transmitted over a local network, and received by an edge device for local inspection.

The emphasis is on traceability, reproducibility, and disclosure discipline. The public record is designed to communicate what was demonstrated without exposing protected or proprietary implementation details.

## Scope

This project covers:

- evidence of real measurement acquisition;
- event structuring for transmission and inspection;
- integrity hashing for non-repudiable local reference;
- local network delivery to an embedded receiver;
- minimal edge-side receipt and presentation.

This project does not claim to provide:

- a production security boundary;
- a finalized commercial implementation;
- device-level attestation or cryptographic signing;
- field deployment integrity guarantees;
- chain-of-custody proof beyond the demonstrated local PoC.

## Repository structure

```text
powv-scale-edge-integration/
├── README.md
├── docs/
│   ├── architecture-overview.md
│   ├── validation-status.md
│   └── disclosure-boundary.md
├── LICENSE
└── NOTICE
Disclosure Limit
The documentation included here is intentionally limited to:

capabilities that were directly observed during the validation process;

the functional flow from measurement to receiver;

the status of validation and non-validation claims;

high-level architectural structure without exposing non-public implementation details.

No protected workflows, device protocols, embedded logic, credential material, key material, operational layout, raw framing details, or implementation content enabling reverse engineering are included in this repository.

Secure Structure for the Public
The repository is intended to serve as a technical record for external review, while preserving the confidentiality of sensitive implementation details. It provides a defensible public summary of the PoC without revealing proprietary internal details.

License
This repository is published under the project license defined in the LICENSE file.

Notice
This project documents a validation-oriented technical PoC for the PoWV ecosystem. It is intended solely for public technical review and research context. It does not represent a production deployment, a security guarantee, or a complete operational specification.
