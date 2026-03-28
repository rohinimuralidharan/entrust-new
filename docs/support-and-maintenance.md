---
description: "Maintain your nShield HSM with firmware updates, security advisories, and patching. Follow Entrust recommendations to keep your HSM secure, supported, and"
---

# Support and maintenance

Use this section to keep your nShield Hardware Security Module (HSM) secure and supported. The nShield Security World is a key management framework spanning the nShield HSM range. Obtain a support contract, apply firmware and software updates regularly, and monitor Entrust security advisories.

!!! note
    Read the release notes before using any hardware or software related to your product.

## Security advisories

When Entrust identifies a security issue affecting your nShield HSMs, Entrust publishes a security advisory describing the issue and the recommended actions. In some cases the advisory requires you to upgrade the nShield firmware or image file — in which case you must re-present a quorum of Administrator Card Set (ACS) smart cards to the HSM to reload your Security World.

Plan your deployment and maintenance procedures to include the steps required to upgrade devices in the field.

Monitor the Announcements and Security Notices section at [https://trustedcare.entrust.com/](https://trustedcare.entrust.com/) for nShield Security Advisories.

!!! note
    The Remote Administration feature supports remote firmware upgrade of nShield HSMs and remote ACS card presentation.

## Application and operating system patching

Update your operating systems and applications following a patching policy — see [Operation](operation.md) for patching policy guidance — to maintain protection against threats in the system environment.

## nShield Connect fan tray module and PSU maintenance

Your nShield Connect contains two user-replaceable parts:

- PSUs
- Fan tray module

Replacing a PSU or fan tray module does not affect your Federal Information Processing Standard (FIPS) 140 validations for the nShield Connect and does not trigger a tamper event. In the rare event that either part requires replacement, contact Support before carrying out the procedure.

!!! important
    Do not remove the fan tray for more than 30 minutes — a tamper event will occur.

!!! important
    Breaking the security seal or dismantling the nShield Connect voids your warranty and any existing maintenance and support agreements.

!!! important
    Mains power plugs on UK cordsets contain a 5A fuse (BS1362). Replace only with a fuse of the same type and rating. If a replacement fuse fails immediately, contact Support. Do not fit a higher-value fuse.

For replacement instructions, see the Installation Sheet included with the replacement part or the physical security section of the HSM User Guide.

If you move the product for maintenance, follow the guidance in [Environment](environment.md). If you store the nShield Connect before or after maintenance, store it following the guidance in [Environment](environment.md).

## nShield Solo XC fan and battery maintenance

Replace the fan and battery if either malfunctions or the battery reaches the end of its useful life. Replacing the battery or fan while the card is powered down does not cause a tamper event. See [Physical Security](physical-security.md) for tamper event guidance. The replacement procedure is described in Battery replacement and Replace the fan (Solo XC) in the HSM User Guide.

If you move the Solo XC for maintenance or store it before or after maintenance, follow the guidance in [Environment](environment.md).

## Maintenance mode

Set your HSM to maintenance mode before performing firmware upgrades. You must not attempt a firmware upgrade in any other mode.

Upgrade HSM firmware only when the HSM is in maintenance mode. See the nShield HSM and Security World product documentation for instructions on setting the HSM into maintenance mode.

## Troubleshooting

Consult these resources before contacting Support. You must review the relevant chapter for your HSM model.

If you encounter problems with your nShield HSM, consult:

- Consult the Troubleshooting chapter for your HSM in the HSM User Guide
- The Logging, debugging, and diagnostics section in the HSM User Guide
- Consult the Status indicators chapter in the HSM User Guide (for PCIe HSMs)

If you cannot resolve the problem, contact Support.

## Debugging information for Java

Handle debug output with the same security controls as sensitive key material. You must not enable Java debugging in production environments.

!!! important
    Debug output contains all commands and replies sent to the `hardserver` in their entirety, including all plaintexts and corresponding ciphertexts. Handle debug output with the same security controls as sensitive key material.

## Contacting Entrust nShield Support

Contact Support if you cannot resolve the issue using the troubleshooting resources. You must have your product details and error information ready before contacting Support.

Contact Entrust nShield Support at [https://trustedcare.entrust.com/](https://trustedcare.entrust.com/).

## Related topics

- [Operate your HSM securely](operation.md)
- [Respond to a security incident](security-incident-and-response.md)
- [Decommission and dispose of your HSM](decommission-and-disposal.md)
