# Disclosure Boundary

This repository is a public-safe technical record of the PoWV-S2E laboratory PoC. It documents observable capabilities, conceptual interfaces, validation status, and maturity without publishing proprietary implementation material.

## Permitted content

Public documentation may describe:

- high-level functional blocks;
- conceptual data flow;
- generic event representations with placeholders;
- validation status and limitations;
- trust boundaries at an abstract level;
- laboratory date and high-level sequence;
- distinctions between integrity identification, attestation, verification, and audit anchoring.

## Prohibited content

Do not publish:

- SSIDs, Wi-Fi passwords, local IP addresses, ports, or private URLs;
- real serial ports, baud rates, parity, stop bits, commands, control bytes, framing, or packet layouts;
- raw instrument output or manufacturer protocols;
- regex, parser logic, Python code, firmware, source code, or operational scripts;
- private keys, certificates, seeds, tokens, credentials, or secure-element configuration;
- key-provisioning procedures, pinouts, protected bus details, or hardware configuration;
- private endpoints, internal topology, partner infrastructure, customer data, production parameters, or internal threat-model material;
- exact bench values, real hashes, identifiers, or any detail that enables reverse engineering or reproduction of the proprietary integration.

## Claim discipline

SHA-256 must be described only as an integrity identifier. It must not be presented as a digital signature, proof of physical truth, authentication mechanism, or complete chain-of-custody solution.

The repository must not claim production readiness, formal security, certification, trustless physical-to-digital provenance, independent ESP32 verification, or completed durable audit anchoring.

## Review requirement

Before publishing a change, review the content for credentials, addresses, serial parameters, commands, frames, code, keys, raw data, and reproducible operational details. Remove any such material before committing.
