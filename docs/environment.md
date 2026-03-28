---
description: "Configure a secure operating environment for your nShield HSM. Covers HSM architecture, form factors, Security World deployment, and the environment controls"
---

# Environment

Use this section to configure a secure operating environment for your nShield Hardware Security Module (HSM). This section covers HSM architecture, available form factors, and the environment controls you must put in place for secure deployment.

## HSM function and architecture

Your nShield HSMs perform encryption, digital signing, and key management for a wide range of commercial and custom-built applications — including Public Key Infrastructures (PKIs), identity management systems, application-level encryption and tokenisation, Secure Sockets Layer/Transport Layer Security (SSL/TLS), and code signing.

Your nShield HSMs provide a hardened, tamper-resistant environment for secure cryptographic processing, key generation and protection, and key, data, and application encryption. They are available in three form factors: the nShield Solo and 5s as local Federal Information Processing Standard (FIPS) 140-certified PCIe cards, the nShield Connect and 5c as network-attached appliances, and the nShield Edge as a FIPS 140-certified USB device.

All nShield HSMs integrate with the nShield Security World architecture — a key management framework that spans the nShield HSM range. This architecture supports combinations of different nShield HSM models, delivering scalability, seamless failover, and load balancing.

nShield HSMs define the physical FIPS-certified security boundary — the HSM Layer — within which Application Keys, Control Keys, and Infrastructure Keys are protected. Using quorums of Administrator Card Set (ACS) cards, you can securely back up and share Infrastructure Keys across multiple HSMs. HSMs sharing the same Infrastructure Keys form a common Security World — a logical boundary extending beyond the physical HSM Layer into your enterprise IT environment.

The Security World keeps keys secure throughout their lifecycle. Every key in the Security World is always protected by another key, even during recovery and replacement operations. Keys are only ever available in plaintext on secure hardware.

Your nShield Connect and nShield Solo HSMs also provide a secure environment for running sensitive applications using the CodeSafe option, which lets you execute code within nShield boundaries.

Your nShield HSM operates securely only if its environment provides the procedural security it requires and if you use its security-enforcing functions appropriately.

When correctly configured, your nShield HSM provides encryption, digital signing, and key management in support of confidentiality and integrity requirements. Note that the nShield HSM is not designed to be completely resistant to denial-of-service attacks — address these through other aspects of your system design if your threat and impact assessments identify them as a risk.

## HSM environment controls

Exercise due diligence to ensure the environment in which you deploy your nShield HSMs is properly configured and regularly examined as part of a comprehensive risk mitigation programme covering both logical and physical threats.

Protect your client applications and their environment from malware whenever they access HSM cryptographic services. Put adequate logical and physical controls in place to detect malware.

Identify in your Security Procedures the measures required to ensure the physical security of the nShield HSM and associated host, client, and Remote File System (RFS) platforms, backup data, Security Information and Event Management (SIEM) collectors, and card readers — including controls against theft and attack.

Restrict access to secure areas containing the HSM, associated platforms, and card readers. Grant access only to authorised individuals, only when necessary, and subject all access to audit control.

Because your nShield HSM and card readers integrate with your infrastructure and network, extend any Security Policy requirements for the infrastructure and network to cover the HSM and card readers.

Protect your nShield HSM against excessive processing demands.

Shield your nShield HSM and card readers against electromagnetic emission detection to prevent potential side-channel attacks through emissions analysis, if your threat assessment identifies this as a risk.

Observe the following hardware-specific requirements:

- **nShield Solo and nShield Connect:** Place in well-ventilated locations (hosts or comms racks) — temperature range restrictions apply during operation.
- **nShield Solo XC, nShield Connect XC, and nShield 5c:** Protect with surge protection equipment — voltage range restrictions apply during operation.

Assign an asset ID to each nShield HSM and card reader. Record the description, serial number, and location against the asset ID in an asset register. This supports tracking and assists any investigation in the event of loss.

## Related topics

- [Commission your nShield HSM](commissioning.md)
- [Configure access control](access-control.md)
- [Manage physical security](physical-security.md)
