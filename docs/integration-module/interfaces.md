# Interfaces

This document describes conceptual interfaces only. It intentionally excludes serial parameters, commands, framing, network addresses, ports, endpoints, firmware, and source code.

## 1. Physical Interface

```text
Instrument → Host
```

The physical instrument supplies a weighing result to the host acquisition layer. The public record does not describe the device protocol, raw response, electrical details, or operating parameters.

## 2. Acquisition Interface

```text
Host → Parsed Measurement
```

The host turns the instrument response into a normalized measurement representation. Parsing and interpretation logic are proprietary and are not documented here.

## 3. Event Interface

```text
Measurement → Structured Event
```

The normalized measurement is combined with selected event context to form a structured digital event. The event model in this repository is conceptual and public-safe.

## 4. Integrity Interface

```text
Structured Event → SHA-256 Identifier
```

The structured representation is associated with a SHA-256 digest. This provides an integrity identifier for the represented content; it is not a signature, authentication mechanism, or proof of physical truth.

## 5. Network Interface

```text
Host → Edge Receiver
```

The host transfers the structured event over a controlled local network to the ESP32 edge receiver. Operational network details are intentionally omitted.

## 6. Inspection Interface

```text
Edge Receiver → Local Client
```

The edge receiver exposes the latest received event to a local inspection client and provides lightweight dashboard visualization. This interface is not presented as a production API or durable audit interface.

## Interface disclosure rule

These interface descriptions identify functional responsibilities without publishing the parameters or implementation needed to reproduce the proprietary integration. No real addresses, credentials, commands, frames, serial settings, endpoint paths, or code are part of this public record.
