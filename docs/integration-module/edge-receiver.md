# Edge Receiver

## Purpose

The edge receiver is the embedded endpoint of the PoWV-S2E laboratory path. This document describes its public behavior without publishing firmware, operational endpoints, network configuration, or implementation code.

## Public behavior

```text
Receive event
  ↓
Validate message availability at application level
  ↓
Return acknowledgment
  ↓
Retain latest event temporarily
  ↓
Expose event for local inspection
  ↓
Render lightweight dashboard view
```

## Ingestion

The host transfers a structured event to the ESP32 over a controlled local network. The receiver accepts the event at the application boundary and provides an application-level acknowledgment. The public record does not specify the HTTP route, local address, port, headers, authentication material, payload framing, or firmware implementation.

## Acknowledgment

The acknowledgment indicates that the receiver accepted the message at the demonstrated application layer. It does not constitute cryptographic verification, proof of origin, proof of physical truth, durable registration, or confirmation of a complete chain of custody.

## Temporary retention

The receiver retains the latest event for local retrieval during the laboratory session. This is temporary inspection-oriented retention, not a durable evidence store or audit anchor. Persistence, recovery, concurrency, and production lifecycle guarantees are outside the current claim.

## Inspection interface

A local client can retrieve the latest received event from the edge receiver for inspection. The interface supports lightweight visualization of the event state. Real URLs, addresses, endpoint paths, credentials, and deployment details are intentionally excluded.

## Dashboard role

The dashboard is an observation surface for the laboratory PoC. It helps demonstrate that an event reached the embedded node and can be displayed locally. It is not a production control plane, compliance interface, security monitor, or independent evidence registry.

## Cryptographic boundary

The ESP32 currently does not claim independent cryptographic verification of the event hash, hardware-backed signature verification, secure-element key operations, or hardware-rooted authentication of the instrument. The host remains responsible for event construction and hash generation in the validated path.

## Non-operational illustration

The following pseudocode is intentionally abstract and non-executable:

```text
on_event_received(event):
    retain_latest(event)
    acknowledge_application_receipt()

on_local_inspection_request():
    return latest_event_for_inspection()
```

This illustration does not disclose firmware structure, endpoint paths, transport parameters, memory layout, authentication, or device configuration.

## Current limitations

- The receiver is dependent on the host-created event.
- Independent edge-side hash verification is pending.
- Hardware-backed attestation is pending.
- Retention is temporary and laboratory-oriented.
- The local dashboard does not establish durable audit anchoring.
- The implementation is not presented as production-ready or certified.

## Disclosure boundary

Do not publish firmware, C++ source, real endpoint paths, local addresses, ports, Wi-Fi credentials, packet layouts, keys, certificates, secure-element configuration, pin assignments, or other details that could enable reproduction or reverse engineering.
