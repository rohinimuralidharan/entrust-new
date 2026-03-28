---
description: "Protect your nShield HSM physically. Follow these guidelines for tamper detection, physical inspection, site security controls, and responding to tamper"
---

# Physical security

Use this section to protect your nShield Hardware Security Module (HSM) physically. This section covers the physical security controls available on each nShield platform and the procedural controls required to maintain them across the product lifecycle.

Four industry-standard terms describe nShield physical security controls. Tamper resistance is physical design that resists breach attempts. Tamper evidence reveals whether a breach was attempted. Tamper detection uses electronic mechanisms to detect breach attempts. Tamper response is the automated action taken when a tamper event is detected.

## nShield Edge physical security controls

The nShield Edge uses tamper resistance and tamper evidence to protect sensitive security parameters:

- The mini HSM within the unit is covered in an epoxy encapsulant to resist tamper attempts (tamper resistant). The internal mini HSM is also protected by the security seal on the enclosure boundary and cannot be inspected directly.
- Once assembled, the nShield Edge cannot be disassembled without breaking the security seal (tamper evident).
- Once assembled, the nShield Edge is difficult to disassemble without disfiguring the fascia (tamper evident).

See [Supply and Transportation](supply-and-transportation.md) for the procedural inspection steps required to maintain tamper-evident controls. You must inspect the nShield Edge following these procedures.

## nShield Solo+ physical security controls

The nShield Solo+ uses tamper resistance and tamper evidence to protect sensitive security parameters:

- The nShield Solo+ card is covered in an epoxy encapsulant that resists tamper attempts and provides evidence of tampering.

See [Supply and Transportation](supply-and-transportation.md) for inspection steps. You must follow these procedures to maintain tamper-evident controls on your nShield Solo+.

## nShield Solo XC and nShield 5s physical security controls

The nShield Solo XC and nShield 5s use tamper resistance, tamper evidence, tamper detection, and tamper response to protect sensitive security parameters.

On the nShield Solo XC, a cosmetic metal lid covers the encapsulant and can be removed for inspection. See [Supply and Transportation](supply-and-transportation.md) for inspection guidance.

Your nShield Solo XC and nShield 5s detect and respond to the following tamper events:

- Abnormal temperature (triggers automatic erase)
- Abnormal voltage (triggers automatic erase)
- Low battery voltage — Solo XC only (triggers automatic erase)
- Sensor failure

When the HSM detects one of these events, it:

- Erases all unprotected secrets
- Enters the Error state
- Flashes an encoded error on the LED indicator to identify the specific tamper event

After a tamper event, examine the HSM and its environment for signs of tampering or intrusion, and record the event following your Security Incident and Response Policy. If you identify the source as harmless and your Security Incident and Response Policy permits, restart the HSM to restore operation. If unresolved, the HSM reasserts the tamper event.

If you cannot identify the source of the tamper, treat the HSM as compromised. Either destroy it or return it to Entrust for secure destruction — see [Decommission and Disposal](decommission-and-disposal.md).

For nShield Solo XC hard tamper events and their encoded error messages, see the Morse code error messages section in the HSM User Guide.

!!! important
    For the battery removal or failure tamper event: the HSM actions this event only when mains power is next applied with the battery removed. Once complete, the HSM blinks the low-battery encoded pattern on the LED to confirm that no unprotected secrets remain. If the HSM does not blink the prescribed pattern after mains power is applied, secrets may not have been erased — physically and securely destroy the nShield Solo XC.

For guidance on responding to a tamper event, see [Security Incident and Response](security-incident-and-response.md).

## nShield Connect physical security controls

Use the nShield Connect physical security measures together with your security policies. You must review and follow the physical security checks regularly.

Your nShield Connect provides multiple layers of physical protection, including a tamper-evident holographic security seal and tamper detection switches under the removable lid. Tamper-resistant baffles behind the vents prevent object insertion.

The Federal Information Processing Standard (FIPS) 140 Level 3 cryptographic boundary is at the nShield Solo.

Use the nShield Connect's physical security measures together with your security policies and procedures to maximise their effectiveness.

### Tamper event

Your nShield Connect's outer boundary has tamper detection and response capabilities. When tampered, the unit stops providing cryptographic functionality, notifies the operator, and resets to factory defaults.

Note that movements, vibrations, and PSU or fan tray replacement do not activate tamper detection.

For guidance on responding to a tamper event, see [Security Incident and Response](security-incident-and-response.md).

### nShield Connect — lid closed tamper event

If the nShield Connect is powered, a tamper event has occurred, and the lid is closed: the unit automatically resets to a factory state.

If this happens:

1. Examine the unit for physical signs of tampering — see [Physical security checks](#physical-security-checks).
2. Do not return the unit to operation if you find signs of tampering.
3. Check the log for the date and time of the tamper event (see the Logging, debugging, and diagnostics section in the HSM User Guide).

!!! note
    The tamper-responsiveness circuitry has its own real-time clock (RTC) running independently from the main nShield Connect clock. Times in the tamper log may differ slightly from other log files.

If you find signs of tampering:

- **During transit from Entrust:** Contact Entrust nShield Support.
- **After installation:** Follow your security policies and procedures.

After the unit resets to factory state, you need a quorum of the Administrator Card Set (ACS) to restore key data and reconnect the nShield Connect to the network.

### nShield Connect — lid open tamper event

If the nShield Connect is powered, a tamper event has occurred, and the lid is open, the following message appears on screen:

```
Unit lid is open
```

An open lid indicates the physical security of the unit is compromised.

1. Examine the unit for physical signs of tampering — see [Physical security checks](#physical-security-checks).
2. Do not return the unit to operation.
3. Check the log for the date and time of the tamper event.

If you find signs of tampering:

- **During transit from Entrust:** Contact Entrust nShield Support.
- **After installation:** Follow your security policies and procedures.

Close the lid and reboot the nShield Connect — the unit automatically resets to factory state. If the lid remains open, the message stays on screen and all button presses are ignored.

### Physical security checks

Perform these checks regularly to verify the security of your nShield Connect. You must not return the unit to operation if you find signs of tampering.

For an alternative presentation of these checks, see the Physical Security Checklist for your product.

To determine whether the security of the nShield Connect is compromised:

1. **Check the security seal.** Verify the holographic foil bearing the tamper label is authentic and intact. Look for cuts, tears, or voiding. The seal is on top of the nShield Connect chassis.

2. **Check the metal lid.** Confirm the metal lid is flush with the chassis.

3. **Check all surfaces.** Inspect the top, bottom, and sides of the nShield Connect for physical damage.

4. **Check the vents.** Look for signs of physical damage or attempts to insert objects into the front vent (to the right of the front panel controls) and the rear vent (to the right of the power supplies).

## nShield card readers

Inspect your card reader and cable for signs of tampering before use. You must follow the guidance in [Supply and Transportation](supply-and-transportation.md).

Inspect the card reader and its cable for signs of tampering. See [Supply and Transportation](supply-and-transportation.md) for procedural inspection guidance.

## Tamper inspection

Inspect your HSM as described in [Supply and Transportation](supply-and-transportation.md). This may include removing the HSM from cabinets to inspect all surfaces. For guidance on inspection frequency, see the [Audit](audit.md) section. For guidance on responding to a tamper, see [Security Incident and Response](security-incident-and-response.md).

## Related topics

- [Commission your nShield HSM](commissioning.md)
- [Audit your HSM deployment](audit.md)
- [Respond to a security incident](security-incident-and-response.md)
