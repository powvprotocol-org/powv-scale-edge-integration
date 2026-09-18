# Architecture

## Public architecture


┌─────────────────────────┐
│ Physical Weighing Event │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ Measurement Instrument  │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ Host Acquisition Layer  │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ Structured Event        │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ Integrity Layer         │
│ SHA-256                 │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ Local Network Transport │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ ESP32 Edge Receiver     │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ Inspection Interface    │
└─────────────────────────┘
```

## Functional responsibilities

### Physical weighing event

A physical weighing action creates the real-world event that is the subject of the PoC. This record does not publish the instrument's raw output or operating parameters.

### Measurement instrument

The instrument is the physical source of the measurement. Its role is limited here to producing a weighing result that can be acquired by the host layer.

### Host acquisition layer

The host is an active intermediary. It acquires the instrument response, interprets the relevant measurement fields, normalizes them, constructs the digital event, generates the integrity identifier, and transmits the event to the edge receiver.

### Structured event

The host represents the normalized measurement and associated public-safe metadata as a structured digital event. The structure is an abstraction and does not disclose a proprietary schema or parser.

### Integrity layer

A SHA-256 digest is generated as an integrity identifier for the structured event. The digest supports comparison of event content, but it is not a digital signature and does not independently establish origin, authenticity, or physical truth.

### Local network transport

The event is carried across a controlled local network to the embedded receiver. Network configuration and operational addressing are intentionally excluded from this record.

### ESP32 edge receiver

The ESP32 receives the event, provides an application-level acknowledgment, retains the latest event temporarily, and makes it available to a local inspection interface. Independent cryptographic verification is not claimed as completed.

### Inspection interface

The local interface provides lightweight visibility into the most recently received event. It is an inspection surface, not a production control plane or audit system.

## Architecture boundary

The architecture documents functional responsibilities only. It deliberately omits source code, firmware, device commands, packet layouts, serial settings, credentials, keys, private endpoints, and other details that would make the proprietary integration reproducible.
