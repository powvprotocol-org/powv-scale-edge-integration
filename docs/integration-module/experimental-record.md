# Experimental Record

## Date

2026-09-18

## Objective

Demonstrate a working laboratory path from a real physical weighing event to a networked embedded edge receiver.

## High-level sequence

1. A physical weight was applied to a commercial weighing instrument.
2. The host acquired the instrument response.
3. Relevant fields were normalized.
4. A structured digital event was generated.
5. A deterministic SHA-256 integrity identifier was generated.
6. The event was transmitted over a controlled local network.
7. The ESP32 acknowledged receipt.
8. The event was retrieved from the ESP32.
9. The event was rendered in a lightweight local dashboard.

No exact weight, price, digest, address, serial identifier, raw response, or operational parameter is included in this public record.

## Technical significance

This test moves the project beyond a purely simulated event path by introducing:

- a real physical instrument;
- a real measurement;
- a real host acquisition layer;
- a real local network transport;
- a real embedded receiver.

The result supports a functional laboratory claim about acquisition, structured representation, integrity identification, transport, and receipt. It does not establish hardware-rooted provenance, independent edge verification, durable audit anchoring, or a production chain of custody.

## Reproducibility boundary

The record is intentionally descriptive rather than operational. It omits proprietary protocols, commands, raw frames, serial and network parameters, firmware, source code, credentials, keys, private endpoints, and protected hardware configuration. Those omissions are part of the disclosure boundary and are required for a public-safe technical record.

## Result classification

**Functional laboratory PoC.** The experiment validates an integrated physical-to-edge path under controlled conditions while leaving security, provenance, audit, and production-hardening claims explicitly pending.
