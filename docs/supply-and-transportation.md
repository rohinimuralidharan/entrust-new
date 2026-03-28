---
description: "Verify the integrity of your nShield HSM on delivery and during transportation. Follow these steps to inspect packaging, check tamper seals, and handle the"
---

# Supply and transportation

Use this section to verify the integrity of your nShield Hardware Security Module (HSM) on delivery and during transportation. This section covers delivery inspection and tamper checking. It does not cover installation — see [Commissioning](commissioning.md) for that. and throughout its operational lifetime. Inspect the packaging and unit carefully before deploying the hardware.

## Trusted delivery

Use a trusted courier service with traceable delivery progress reporting and signed acceptance of delivery to protect your HSM during transit.

On receipt, inspect the packaging for signs of tampering — for example, packing tape that appears to have been removed, cut, and resealed. If you detect tampering, quarantine the package and notify your Security Officer following your [Security Incident and Response](security-incident-and-response.md) procedures.

Apply the same transport controls whenever you move the product during its operational lifetime. If your organisation uses a protective marking scheme, apply the relevant marking during transportation to maintain the required integrity level.

## Tamper inspection

Inspect your nShield HSM for signs of tampering on receipt. Follow the inspection steps for your specific product:

- **nShield Edge:** Inspect the USB cable and the nShield Edge before use. Check the cable for signs of tampering, the fascia for disfigurement, and the holographic tamper label in the tamper window for the word VOID.

    !!! note
        The nShield Edge Developer Edition does not have a hologram or tamper window.

- **nShield Solo:** Examine the epoxy resin security coating (remove the metal lid on nShield Solo XC first) for obvious signs of damage.

- **Smart card reader:** Examine the smartcard reader for signs of tampering. Confirm it is plugged directly into the HSM or into the port provided by the appliance, and that the cable has not been tampered with.

- **nShield Connect:** Review the nShield Connect Physical Security Checklist (provided in the box and available on the installation media) for the required physical security checks. Review the nShield Connect tamper log for tamper alerts. See [Physical Security](physical-security.md) for further inspection guidance.

If you suspect tampering, quarantine the unit and investigate the incident. Do not deploy the unit on a live system until you verify its integrity. See [Security Incident and Response](security-incident-and-response.md) for response guidance.

## Related topics

- [Commission your nShield HSM](commissioning.md)
- [Inspect physical security](physical-security.md)
- [Respond to a security incident](security-incident-and-response.md)
