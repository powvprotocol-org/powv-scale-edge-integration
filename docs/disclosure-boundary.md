````markdown
# Disclosure Boundary

This repository is intended to document the engineering work behind the PoWV Scale-to-Edge Integration Module while preserving the confidentiality of implementation details that are not required for public technical review.

The objective is not to remove technical substance. Public documentation may include architecture, data flow, event models, validation results, sanitized code examples, interface behavior, component responsibilities, trust boundaries, and laboratory observations. The boundary applies only where disclosure would expose credentials, protected device behavior, proprietary integration logic, or operational details that materially reduce the effort required to reproduce the private implementation.

## Public Technical Scope

The following material may be published:

- system and module architecture;
- hardware topology at component level;
- host-to-edge processing flow;
- structured event schemas;
- event lifecycle and transformation stages;
- canonicalization and hashing concepts;
- SHA-256 usage within the current PoC;
- ESP32 receiver behavior;
- application-level acknowledgments;
- local inspection and dashboard behavior;
- generic HTTP request/response examples;
- sanitized Python and embedded-code examples;
- validation matrices and test status;
- laboratory procedures described at a functional level;
- trust boundaries and current architectural limitations;
- measured capabilities that do not disclose protected integration mechanics.

Illustrative source code is acceptable where device-specific secrets, operational configuration, and proprietary integration logic have been removed.

## Restricted Implementation Material

The following information must remain outside the public repository:

### Device protocol details

- vendor-specific serial commands;
- request bytes and control sequences;
- raw device frames;
- framing rules;
- undocumented protocol behavior;
- device-specific response formats where publication would enable direct reproduction;
- proprietary parsing expressions derived from non-public protocol behavior.

### Operational configuration

- Wi-Fi credentials;
- private network addresses;
- active hostnames;
- production or laboratory credentials;
- local machine-specific device identifiers;
- environment-specific endpoints;
- deployment-specific port assignments.

### Security material

- private keys;
- certificates containing non-public material;
- seeds;
- authentication tokens;
- provisioning credentials;
- secure-element configuration;
- key-generation or key-injection procedures;
- hardware-root-of-trust implementation details not approved for publication.

### Protected engineering details

- complete acquisition adapters containing proprietary device logic;
- private firmware implementations;
- unreleased packet layouts;
- confidential transport optimizations;
- internal anti-tamper mechanisms;
- non-public threat-model material;
- internal infrastructure topology;
- customer, partner, facility, or deployment-specific integration data.

## Sanitized Code

Public examples should preserve the engineering model while removing environment-specific and protected information.

For example, this is within scope:

```python
canonical = json.dumps(
    event,
    sort_keys=True,
    separators=(",", ":"),
    ensure_ascii=False
)

digest = hashlib.sha256(
    canonical.encode("utf-8")
).hexdigest()
````

A generic edge receiver is also within scope:

```cpp
void receiveEvent() {
    if (!server.hasArg("plain")) {
        server.send(400, "application/json",
                    "{\"received\":false}");
        return;
    }

    latestEvent = server.arg("plain");

    server.send(200, "application/json",
                "{\"received\":true}");
}
```

By contrast, code that includes the exact command sequence required to communicate with a specific weighing instrument, production credentials, or protected device parsing logic should remain private.

## Experimental Data

Laboratory results may be published when they are useful for understanding the behavior of the module.

Public records may include:

* representative measurement values;
* structured event examples;
* example timestamps;
* derived commercial fields;
* sanitized hashes;
* acknowledgment responses;
* validation outcomes.

Raw captures and device-specific frames should not be published when they expose the underlying proprietary interface.

## Security and Integrity Claims

The current use of SHA-256 establishes an integrity identifier for a defined digital representation.

It does not, by itself, establish:

* device authentication;
* digital signature;
* physical-source authenticity;
* hardware-backed attestation;
* independent edge verification;
* tamper-proof acquisition;
* complete chain of custody.

Any documentation describing these properties must reflect the actual implementation status of the module.

## Publication Principle

The governing rule for this repository is:

> Publish enough engineering detail to explain, review, and evaluate the module without publishing the protected mechanics required to reproduce the proprietary device integration.

Technical depth is encouraged. Credentials, private infrastructure, vendor-specific protocol mechanics, cryptographic secrets, and confidential implementation knowledge are not.

```
```
