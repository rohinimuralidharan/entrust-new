---
description: "Respond to nShield HSM security incidents. Covers tamper detection, key compromise, card loss, KNETI compromise, and the steps to take when a security breach"
---

# Security incident and response

Use this section to respond to nShield Hardware Security Module (HSM) security incidents. The nShield Security World is a key management framework spanning the nShield HSM range. This section covers tamper events, key compromise, KNETI compromise, and card loss. It does not cover routine audit procedures — see [Audit](audit.md) for those. Follow the steps for each compromise type to contain the incident, revoke affected keys, and restore your Security World to a trusted state.

## Security incident monitoring

Monitor your deployment for these suspected or actual events. Start with tamper-evident or tamper-response functions triggering on the HSM.
- Check for physical or logical unavailability of the HSM, card reader, card sets, client application, `%NFAST_KMDATA%` folder contents, nShield Connect config file, Security Information and Event Management (SIEM) collector data, or backup data
- Check for logical unavailability of any of the above
- Inspect logs for gaps, unexplained entries, or suspected tampering
- Investigate evidence of access control violations — for example, a lost token followed by a successful login
- Investigate evidence of unauthorised use
- Investigate evidence of network attacks on the HSM
- Monitor for evidence of excessive performance demands
- Investigate evidence of environmental control violations
- Monitor for unauthorised changes to HSM or client application configuration settings, such as updating the HSM clock
- Detect non-compliance with security processes,, such as commissioning on an open network
- Detect non-compliance with security policy,, such as using an incorrect algorithm strength or continuing to use a key beyond its cryptoperiod

## Security incident management

If you suspect a security incident, alert your Company Security Officer immediately. Your Security Officer determines which actions to implement, following your Security Incident and Response Policy. Your policy must cover:

- Quarantine and isolation — take a witnessed snapshot of the unit. Weigh powering off (which may destroy evidence) against the need to isolate potential malware.
- Investigation
- Reporting structure and timescales

## Security incident impact and response

The sections below describe the impact of specific key or secret compromises and the recovery steps required.

*Note on terminology:* Throughout this section, **revoke** means the compromised key must no longer be trusted or used. This term applies to all compromised keys, including digital certificates.

---

### Compromise: Brute force attack on blobbed key outside the HSM

#### Impact

The application key is compromised. Affected key types include:

- Administrator Card Set (ACS)-protected application keys
- Softcard-protected application keys
- Module- or module pool-protected application keys

#### Recovery steps

1. Revoke the application key.
2. Erase and destroy the Security World — all application keys in the Security World are now compromised.
3. Erase or destroy the ACS and re-initialise all HSMs to a new Security World with a new ACS.
4. Alternatively, to contain the immediate threat, put the HSMs into pre-initialisation mode while you implement business recovery procedures before creating a new Security World.

!!! note
    Erasing the ACS prevents any lost or stolen backup from being reloaded onto a new HSM.

---

### Compromise: Attacker has subverted HSM memory

#### Impact

The application key is compromised. Affected key types include:

- ACS-protected application keys
- Softcard-protected application keys
- Module- or module pool-protected application keys

#### Recovery steps

1. Revoke the application key.
2. Destroy the Security World — all application keys in the Security World are now compromised.
3. Erase or destroy the ACS.
4. Remove and destroy the HSM — its integrity can no longer be guaranteed.
5. Create a new Security World on new or different HSMs with a new ACS.

!!! note
    Erasing the ACS prevents any lost or stolen backup from being reloaded onto a new HSM.

---

### Compromise: Softcard passphrase compromised

#### Compromise type

Lost or observed.

#### Impact

An attacker controls all application keys protected by the softcard.

#### Recovery steps

1. Revoke all application keys protected by the softcard. If you cannot revoke the keys, isolate the HSM so no system can use it.
2. Erase all blob copies associated with the compromised softcard keys from `kmdata`, the Remote File System (RFS), and backups — this prevents an attacker from using the stolen passphrase.
3. Create replacement application keys under new softcards.

---

### Compromise: Quorum of Operator Card Set (OCS) cards compromised

#### Compromise type

Lost or stolen.

#### Impact

An attacker controls all application keys protected by the OCS.

#### Recovery steps

1. Revoke all application keys protected by the OCS.
2. If you cannot revoke the keys, isolate the HSM so no system can use it.
3. Erase all blobs associated with the OCS-protected keys from `kmdata`, the RFS, and backups.
4. Create replacement application keys under a new OCS.
5. If you later recover the OCS cards, erase or destroy them.

---

### Compromise: Quorum of ACS cards compromised

#### Compromise type

Lost or stolen.

#### Impact

An attacker controls the Security World protected by the ACS.

#### Recovery steps

1. Revoke all module-protected, module pool-protected, and recoverable application keys — these are accessible to an attacker with a KMData archive, an HSM, and the compromised ACS.
2. Revoke all non-recoverable OCS- or softcard-protected keys — these cannot be used without the Security World, which is now compromised.
3. Alternatively, to contain the immediate threat, put the HSM into pre-initialisation mode while you implement business recovery procedures before creating a new Security World.
4. Do not use the original Security World again. Destroy the world file and all backups to prevent an attacker from recovering the Security World using the ACS, world file, and backups.
5. If you later recover the ACS cards, erase or destroy them.
6. Create replacement application keys.

---

### Compromise: Soft KNETI (client memory subverted)

#### Impact

Your KNETI key is compromised and must not be used.

#### Recovery steps

1. For every nShield Connect that the affected client communicated with, use the front panel or serial console to remove the client's configuration data.
2. For any RFS the affected client communicated with, update the RFS configuration file to remove the client's configuration data.
3. Manually delete the KNETI file identified as `kneti-hardserver`:
   - **Windows:** `C:\ProgramData\nCipher\Key Management Data\hardserver.d\`
   - **Linux:** `/opt/nfast/kmdata/hardserver.d/`
4. Reboot the client.
5. Isolate the client and investigate the compromise.
6. Once resolved, re-configure the nShield Connect and RFS systems using the same client IP address and Electronic Serial Number (ESN), but with the new KNETI hash. Re-establish the secure channel to each nShield Connect and RFS.

---

### Compromise: nToken KNETI (brute force on KNETI file in Connect KMData folder)

#### Impact

The KNETI key is compromised and must not be used.

#### Recovery steps

1. For every nShield Connect the affected client communicated with, use the front panel or serial console to remove the client's configuration data.
2. For any RFS the affected client communicated with, update the RFS configuration file to remove the client's configuration data.
3. Manually delete the KNETI file identified as `kneti-nToken ESN`:
   - **Windows:** `C:\ProgramData\nCipher\Key Management Data\hardserver.d\`
   - **Linux:** `/opt/nfast/kmdata/hardserver.d/`
4. Destroy the nToken — its integrity can no longer be guaranteed.
5. If you have an alternative nToken, configure it to communicate with the nShield Connect.
6. If no alternative nToken is available, switch to software-based authentication. See the HSM User Guide for configuration details.

---

### Compromise: nShield Connect KNETI (brute force on KNETI file in Connect KMData folder)

#### Impact

The KNETI key is compromised and must not be used.

#### Recovery steps

1. Remove the compromised nShield Connect's data — IP address, KNETI, and H(KNETI) — from every client `hardserver` configuration file that communicated with it.
2. Destroy the nShield Connect — its integrity can no longer be guaranteed.
3. Configure a new nShield Connect to communicate with the client.

---

### Compromise: Soft KNETI (brute force on obfuscated KNETI file in KMData folder)

#### Impact

The KNETI key is compromised and must not be used.

#### Recovery steps

1. For every nShield Connect the affected client communicated with, use the front panel or serial console to remove the client's configuration data.
2. For any RFS the affected client communicated with, update the RFS configuration file to remove the client's configuration data.
3. Manually delete the KNETI file identified as `kneti-hardserver`:
   - **Windows:** `C:\ProgramData\nCipher\Key Management Data\hardserver.d\`
   - **Linux:** `/opt/nfast/kmdata/hardserver.d/`
4. Reboot the client.
5. Isolate the client and investigate how the KMData file was accessed.
6. Once resolved, re-configure the nShield Connect and RFS systems using the same client IP address and ESN, but with the new KNETI hash. Re-establish the secure channel.

---

### Compromise: nToken KNETI (brute force on encrypted KNETI blob in KMData folder)

#### Impact

The KNETI key is compromised and must not be used.

#### Recovery steps

1. For every nShield Connect the affected client communicated with, use the front panel or serial console to remove the client's configuration data.
2. For any RFS the affected client communicated with, update the RFS configuration file to remove the client's configuration data.
3. Manually delete the KNETI file identified as `kneti-nToken ESN`:
   - **Windows:** `C:\ProgramData\nCipher\Key Management Data\hardserver.d\`
   - **Linux:** `/opt/nfast/kmdata/hardserver.d/`
4. Reset the nToken.
5. Isolate the client and investigate how the KMData file was accessed.
6. Once resolved, re-configure the nShield Connect and RFS systems using the same client IP address and ESN, but with the new KNETI hash. Re-establish the secure channel.

---

### Compromise: Client SSH private keys disclosed or recovered from backup

#### Impact

An attacker can access your HSM and run platform services using the compromised keys.

#### Recovery steps

1. Remove and destroy the nShield Connect — its integrity can no longer be guaranteed.
2. Configure a new nShield Connect to communicate with a client. Recreate the client and server HSM keys.
3. Factory reset the HSM to prevent access using the compromised client SSH keys.
4. Isolate the client host system and investigate the unauthorised access to the client host or backup.
5. Re-enrol the client host system with the HSM to create new client SSH keys.

---

### Compromise: Imported application keys

Application keys can be imported into the HSM. Treat any imported secret or private key as potentially compromised if you cannot verify its confidentiality and integrity, or if it came from an unknown or untrusted source.

---

## Deleting a Security World

Re-initialise an HSM to use a new Security World if you believe your existing Security World has been compromised. Perform this on all HSMs that hosted the old Security World.

Before you delete the Security World:

- Reformat any standard nShield cards used as Operator Cards within this Security World.

!!! note
    Except for nShield Remote Administration Cards, if you do not reformat the Operator Cards before deleting your Security World, you must discard them — they cannot be used, erased, or reformatted without the old Security World key.

Reuse, reformat, or destroy the smart cards from the deleted Security World's ACS. If you leave these cards intact, an attacker with the cards, a copy of your data, and access to any nShield HSM can access your old keys.

## Module failure

If an HSM fails and you cannot perform a factory reset, application keys protected by module keys and NVRAM keys may be vulnerable to attack. Apply procedural and technical access controls to protect the HSM until you can securely destroy it — see [Decommission and Disposal](decommission-and-disposal.md).

## Tamper incident

[Physical Security](physical-security.md) describes the physical security controls available on each nShield platform and the procedural controls required across the product lifecycle.

If you detect a tamper incident, follow the guidance in this section to manage the incident. Your investigation determines the extent of the attack. Once an HSM is confirmed tampered, its integrity can no longer be assured — decommission and dispose of it following [Decommission and Disposal](decommission-and-disposal.md).

Two situations allow you to recover an HSM from a tamper event:

- **nShield Solo XC tamper events** — see the nShield Solo XC physical security controls section in [Physical Security](physical-security.md) for recovery details.
- **nShield Connect lid open or closed** — see the Tamper event section in [Physical Security](physical-security.md) for investigation steps and recovery criteria. Record the event and authorise recovery following your Security Incident and Response Policy.

## Related topics

- [Audit your HSM deployment](audit.md)
- [Manage keys in your Security World](key-management.md)
- [Decommission and dispose of your HSM](decommission-and-disposal.md)
