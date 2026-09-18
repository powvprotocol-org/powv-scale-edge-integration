# Validation Status

## Validation matrix

| Capability | Status |
|---|---|
| Real physical measurement acquisition | Validated |
| Measurement field extraction | Validated |
| Structured event construction | Validated |
| Timestamped event representation | Validated |
| SHA-256 integrity identifier | Validated |
| Local network transport to ESP32 | Validated |
| Application-layer acknowledgment | Validated |
| Latest-event retrieval | Validated |
| Embedded local dashboard | Validated |
| Independent edge hash verification | Pending |
| Hardware-backed signature | Pending |
| Secure-element key operations | Pending |
| Authenticated device identity | Pending |
| Direct instrument-to-edge path | Pending |
| Durable audit anchoring | Pending |
| Production chain of custody | Pending |

## Correct interpretation

A PoC demonstrates:

> A real physical measurement can be acquired, normalized into a structured event, integrity-identified, transported over a local network, and received by an embedded edge node.

It does not yet demonstrate:

> The entire physical-to-digital chain is cryptographically trusted.

## Limitations

- The host is an active intermediary between the instrument and the edge node.
- SHA-256 is an integrity identifier, not a digital signature.
- The digest does not prove physical truth, origin, or authenticity.
- Independent cryptographic verification at the edge is pending.
- Hardware-backed attestation and secure-element operations are pending.
- The local dashboard is an inspection interface, not a durable evidence registry.
- The results are laboratory observations and do not establish production readiness, certification, or formal security assurance.
