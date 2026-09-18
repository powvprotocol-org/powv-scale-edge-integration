# Hardware Topology

## Purpose

This document describes the public functional topology of the PoWV Scale-to-Edge Integration Module (PoWV-S2E). It identifies the responsibilities of each block without publishing protected device details or operational configuration.

## Public topology

```text
Physical Weighing Instrument
          ↓
Physical Instrument Interface
          ↓
Host Acquisition Adapter
          ↓
Measurement Normalization
          ↓
Structured Event
          ↓
Canonical Representation
          ↓
SHA-256 Integrity Identifier
          ↓
Controlled Local Transport
          ↓
ESP32 Edge Receiver
          ↓
Acknowledgment + Local Inspection
```

## Functional blocks

### Physical weighing instrument

The instrument is the source of a real weighing measurement. The public record treats it as a measurement source and does not disclose its manufacturer protocol, raw output, commands, electrical details, or operating parameters.

### Physical instrument interface

A physical connection carries the instrument response to the host acquisition environment. The interface is described only at a functional level. Serial settings, USB details, framing, control bytes, port identifiers, and proprietary protocol information are excluded.

### Host acquisition adapter

The host is an active intermediary. It acquires the instrument response, interprets relevant fields, normalizes the measurement, constructs the structured event, derives the canonical representation, computes the integrity identifier, and sends the event to the edge receiver.

### Measurement normalization

Normalization converts the acquired measurement into a consistent digital representation suitable for event construction. The public description does not expose parser logic, regular expressions, raw frames, or implementation code.

### Structured event and integrity layer

The normalized measurement is represented as a structured event. A canonical form is then used for deterministic integrity identification with SHA-256. The digest is an integrity identifier, not a digital signature or proof of physical truth.

### Controlled local transport

The host transfers the event through a controlled local network to the edge receiver. Network addresses, credentials, endpoint details, and deployment topology are not part of this public record.

### ESP32 edge receiver

The ESP32 receives the event, returns an application-level acknowledgment, retains the latest event temporarily, and makes it available to a local inspection interface. Independent cryptographic verification is not claimed as completed.

### Local inspection interface

The inspection interface provides lightweight visibility into the latest received event. It is not presented as a production API, durable audit registry, or security boundary.

## Boundary statement

The current topology contains a host between the physical instrument and the ESP32. It therefore validates a host-mediated physical-to-edge path rather than a direct hardware-rooted instrument-to-edge trust path.

## Disclosure boundary

This document intentionally omits source code, firmware, serial parameters, device commands, packet layouts, credentials, keys, private endpoints, pin assignments, and other details that could enable reproduction or reverse engineering of the proprietary integration.
