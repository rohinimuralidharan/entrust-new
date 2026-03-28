---
description: "Decommission and dispose of your nShield HSM securely. Follow the factory reset, card erasure, and disposal steps to protect your Security World keys at end"
---

# Decommission and disposal

The nShield Security World is a key management framework spanning the nShield Hardware Security Module (HSM) range.


Use this section to decommission and dispose of your nShield Hardware Security Module (HSM) securely at end of operational life. Follow these steps in order to protect your nShield Security World keys. The Security World is the key management framework that protects your cryptographic keys.

## nShield Connect and nShield Solo

Follow this process to decommission and dispose of your HSM securely. The nShield Security World is a key management framework spanning the nShield HSM range. Complete all steps to protect your Security World keys:

1. **Erase all secret information from your HSM.** Describe the decommissioning process in your Security Procedures. Erase all secrets used to protect your Security World. See the Remove modules and delete Security Worlds section in the Management Guide for the HSM factory reset procedure.

    If your Security Procedures specify requirements for erasing application key material, complete those steps before performing the factory reset.

    !!! note
        A factory reset erases all your application key material:
        - **nShield Solo+ and Solo XC:** Erase your Security World — for example, using `initunit`.
        - **nShield Connect+, Connect XC, nShield 5s, and nShield 5c:** Perform a full factory reset to remove all information, including the KNETI key, files loaded from the Remote File System (RFS) on Connect and 5c, and any loaded CodeSafe applications on 5s and 5c.

2. **Dispose of the HSM.** If you have no further operational requirements for the HSM, describe the disposal process in your Security Procedures. Follow your Security Procedures for transporting the unit.

3. **Securely destroy the HSM if required.** If your Security Procedures require secure destruction of decommissioned assets, you do not need to destroy the HSM once all secrets are erased — the HSM is returned to its factory state. However, if the HSM has malfunctioned in a way that makes it impossible to confirm whether secrets were erased, refer to your Security Procedures to determine whether to destroy the HSM.

    One option is to engage a data destruction service that destroys equipment following approved standards and provides a certificate of destruction. Your Security Procedures must describe the destruction process to ensure complete destruction of all HSM components that contain secrets.

4. **Return to Entrust.** Entrust accepts decommissioned HSMs for secure destruction.

### Recycling and disposal information

For recycling and disposal guidance, see the Warnings and Cautions documentation for your nShield product.

### Security World cards

If you no longer need the Security World on your decommissioned HSM, erase the ACS and OCS cards by formatting them. Formatting using nShield tools zeroises any storage used for secrets.

- **ACS:** Erase ACS cards using a different Security World — for example, a replacement Security World — or on an HSM with no Security World loaded. Reuse or destroy smart cards from a deleted Security World's ACS. If you leave these cards intact, an attacker with the cards, a copy of your data, and access to any nShield HSM can access your old keys.

- **Legacy OCS cards:** Erase OCS cards only on the Security World that created them. Erase the OCS as a final step before decommissioning the HSM — the cards can then be reused for a new Security World. Modern Remote Administration Ready cards can be erased forcibly without the creating Security World using the `--ignoreauth` option with `slotinfo --format`. This option is permitted only when no Security World is loaded, or when not in a strict-Federal Information Processing Standard (FIPS) world.

- Once you complete the steps above, any keys in backup data are no longer usable.

If you do not need a new Security World, uninstall the Security World software — but only if you are certain it is no longer required or you intend to upgrade it.

## Related topics

- [Manage keys in your Security World](key-management.md)
- [Respond to a security incident](security-incident-and-response.md)
- [Audit your HSM deployment](audit.md)
