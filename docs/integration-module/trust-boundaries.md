# Trust Boundaries

## Current laboratory boundary

```text
[Physical Domain]
Instrument
   |
   | Trust Boundary 1
   v
Host
   |
   | Trust Boundary 2
   v
Local Network
   |
   | Trust Boundary 3
   v
ESP32 Edge Receiver
```

The host remains an intermediary between the physical instrument and the ESP32. It performs acquisition, response interpretation, structured event construction, hash generation, and transmission.

## Boundary 1: instrument to host

The host receives information from the physical instrument and relies on the instrument's measurement behavior and the acquisition context. The PoC does not establish hardware-rooted provenance for the measurement at this boundary.

## Boundary 2: host to local network

The host creates the structured event and integrity identifier before transport. The local network is a transport domain, not an independently trusted evidence system. Network security configuration is outside this public record.

## Boundary 3: local network to ESP32

The ESP32 receives the event and acknowledges it at the application level. It retains the latest event temporarily and makes it available for inspection. Independent cryptographic verification on the edge is not claimed as completed.

## Hash versus signature

A SHA-256 digest is an integrity identifier for the exact content supplied to the hashing operation. It does not provide a private-key signature, authenticate the producer, establish device identity, or prove that the physical measurement was truthful. A digest also does not by itself prevent the host from constructing a different event and hashing that event.

## Provenance limitation

The current flow demonstrates that a real measurement can be acquired and represented, but it does not establish complete provenance from the physical source through the edge node. The host is a trusted intermediary for the purposes of this laboratory PoC, and that trust assumption has not been replaced by hardware-backed attestation or independent edge verification.

> The current laboratory implementation does not yet establish a hardware-rooted trust path from the physical instrument to the edge receiver.

## Trust status

The boundaries described here are engineering observations, not a formal threat model or security certification. Attestation, authenticated identity, independent verification, durable audit anchoring, and production chain of custody remain pending validation.
