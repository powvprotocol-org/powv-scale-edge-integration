# PoWV Scale-to-Edge Integration PoC

A public technical record of a laboratory proof of concept conducted on 2026-09-18. The PoC evaluated the public-safe path from a physical weighing event to a networked embedded edge receiver.

> This repository documents observable capabilities and validation status only. It does not disclose proprietary implementation details, operational parameters, credentials, keys, raw device data, or material that could facilitate reverse engineering.

## Demonstrated path

```text
Physical weighing event
→ Instrument acquisition
→ Structured event representation
→ SHA-256 integrity identifier
→ Controlled local network transport
→ ESP32 edge receiver
→ Local inspection interface
```

A host remains between the physical instrument and the ESP32. The current PoC therefore does not represent direct instrument-to-edge acquisition.

## PoWV conceptual mapping

```text
E → M → P → H → edge transport
```

- **E** — physical event
- **M** — measurement
- **P** — structured digital representation
- **H** — SHA-256 integrity identifier

SHA-256 is used as an integrity identifier in this record. It is not a digital signature and does not, by itself, establish provenance, authenticity, hardware attestation, or complete chain of custody.

## Validation status

Validated at laboratory PoC level:

- acquisition of a real physical weight measurement;
- structured event construction;
- generation of an integrity identifier;
- controlled local network transport;
- receipt by an ESP32 edge receiver;
- application-level receipt acknowledgment;
- retrieval of the most recent received event;
- lightweight local dashboard visualization.

Not claimed as complete:

- hardware-backed cryptographic attestation;
- independent cryptographic verification on the edge device;
- secure-element key operations;
- direct instrument-to-edge acquisition;
- durable audit anchoring;
- production-grade chain of custody;
- interpretation or business decision automation.

## Maturity classification

**Functional laboratory PoC for physical measurement acquisition, structured event construction, integrity hashing, controlled local network transport, and embedded receipt.**

This classification is deliberately narrower than a production security or compliance claim.

## Documentation

- [Architecture overview](docs/architecture-overview.md)
- [Validation status](docs/validation-status.md)
- [Experimental record](docs/experimental-record.md)
- [Disclosure boundary](docs/disclosure-boundary.md)
- [Notice](NOTICE.md)

## Public documentation boundary

This repository intentionally excludes proprietary source code and operational reproduction details, including device protocol material, serial parameters, network secrets, credentials, cryptographic keys, raw instrument output, firmware, private endpoints, and internal topology. See [disclosure-boundary.md](docs/disclosure-boundary.md) for the publication rules.
