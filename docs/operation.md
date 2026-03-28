---
description: "Operate your nShield HSM securely day-to-day. Covers patching policy, RTC synchronisation, OCS quorum configurations, key storage, and migration guidance for"
---

# Operation

The nShield Security World is a key management framework spanning the nShield Hardware Security Module (HSM) range.


Use this section to operate your nShield Hardware Security Module (HSM) securely day-to-day. This section covers patching, Operator Card Set (OCS) configurations, key storage, and nShield Security World maintenance. The Security World is the key management framework that protects your cryptographic keys. For initial setup, see [Commissioning](commissioning.md). For initial setup and configuration guidance, see [Commissioning](commissioning.md). The nShield Security World is a key management framework spanning the nShield HSM range.

## Patching policy

Define and actively implement your patching policy. Your policy must cover:

- Install the latest nShield firmware and software.
- Run all host operating systems on a current, supported version with the latest patches applied.
- Install anti-virus software on the host system and keep it updated with automatic updates.

## Set the RTC time

You must set the nShield Edge, nShield Solo, and nShield Connect real-time clock (RTC) using an accurate trusted local time source at regular intervals to mitigate clock drift.

## Operator Card Set (OCS) quorum configurations

### Share keys across multiple HSMs

Use an OCS to share the same keys across multiple HSMs simultaneously.

For a non-persistent OCS, you must leave one card in an appropriate card slot of each HSM. If you created an OCS where K is more than half of N, you can share keys among multiple modules simultaneously, as long as you have enough unused cards to form a K/N quorum for each additional HSM.

For example, with a 3/5 OCS:

- Load keys onto HSM 1 — 4 cards remain.
- Load keys onto HSM 2 — 3 cards remain.
- Load keys onto HSM 3 — 2 cards remain. This is insufficient to form a quorum for a fourth HSM.

!!! important
    In this configuration, the card management controls described in [Access Control](access-control.md) are not fully enforced. Loss or theft of a quorum of cards exposes all OCS-protected keys. Perform a threat analysis to identify the additional logical and physical controls required to protect OCS cards left in card readers.

An alternative, less secure approach for sharing OCS-protected keys across multiple HSMs is to set K to 1 and N to at least the number of HSMs. Insert a single card from the OCS into each HSM to authorise key use.

To mitigate card failure risk, set N to a number greater than the number of HSMs. If a card fails, retrieve the spare OCS card from its secure location while you create a replacement OCS.

!!! important
    With K=1, loss or misuse of any single card exposes all OCS-protected keys. Perform a threat analysis to identify the additional logical and physical controls required.

An alternative strategy is to use a persistent OCS or a persistent OCS with a timeout — but both options reduce your direct control over keys once the OCS is loaded. Your threat analysis determines the appropriate configuration.

## Share a key between users

To share the same OCS-protected key among a set of users, you set K to 1 and N to the number of users. Give each user a single card from the OCS.

!!! important
    With K=1, loss or misuse of any single card exposes all OCS-protected keys. Perform a threat analysis to identify the additional logical and physical controls required.

## NVRAM key storage

Consider NVRAM key storage only when explicitly required by regulation. You must understand the security trade-offs before proceeding.

Store application keys within the nonvolatile random access memory (NVRAM) of a suitable HSM. This functionality exists for regulatory compliance only. NVRAM-stored keys provide no additional security benefit and increase the risk to your Administrator Card Set (ACS). Storing keys in NVRAM also reduces load-balancing and recovery capabilities.

We recommend using standard Security World keys unless regulatory requirements explicitly mandate NVRAM-stored keys.

Ensure that any backup and recovery procedures for NVRAM-stored keys comply with regulatory requirements. Do not back up keys to a smart card — the keys would no longer be stored solely within the physical boundary of the HSM.

## Config push feature

Review your need for the config push feature. Disable it if your deployment does not require remote configuration updates.

Use the config push feature to update your nShield Connect configuration remotely, without accessing the front panel. Anyone with access to the Remote File System (RFS) or a designated client can change the nShield Connect configuration using `cfg-pushnethsm`.

Disable config push via the nShield Connect front panel if you do not require it. If you do require it, perform a threat analysis to determine whether additional security controls are needed.

## Security World replacement

Follow these steps when replacing a Security World. You must delete the renamed legacy directory if you do not need to retain the old Security World data.

When you replace an existing Security World, the existing `%NFAST_KMDATA%\local` directory is renamed `%NFAST_KMDATA%\local_N` (where N increments with each overwrite) and a new Key Management Data directory is created. Delete the renamed directory manually if you do not need to retain the old Security World data.

## Host platform and client applications

Secure your host platform before deploying the HSM. You must verify that all conditions in this section are met before putting the HSM into production.

The nShield security model assumes you fully control your client endpoint and host platform security, and that the host platform is physically protected and hardened following your Security Policy.

Apply additional controls to reduce the client machine's attack surface, including file encryption and mandatory access controls, as determined by your threat analysis.

Ensure the following conditions are met for your host platform and client applications:

- Treat all client applications, nShield utility users, and host platform users as trusted. Verify that none will perform actions detrimental to HSM security.
- Grant access to the HSM's nCore API only to authorised and trusted users. Grant privileged nCore API access only to users who definitively need it.
- Implement platform service user separation as described in SSH Client Key Protection (nShield 5s HSMs). Encrypt SSH private authentication keys on the host platform for nShield 5 platform services.
- Keep the host OS patched, free from unapproved software, and protected against malware.
- Ensure client applications securely gather and transfer identification and authentication data to the HSM when authorising HSM asset and service use.
- Ensure client applications supply correct data securely — including any authenticity, integrity, and confidentiality requirements — to HSM cryptographic functions.
- Ensure client applications protect their users' security requirements and comply with security policies on key and sensitive data management.
- Ensure the host platform contains an available random number generator of sufficient quality to generate keys with adequate entropy.
- Ensure all cryptographic keys generated on the host platform conform to specifications endorsed by recognised security authorities.
- Ensure client keys used to build a secure channel to the HSM meet the strength specified in NIST SP800-131A Revision 2.

## Preload utility

The `preload` utility preloads keys — for example, when using a PKCS #11 application. See the Preload Utility section in the HSM User Guide for a full description.

You must not use `preload` if any of the following conditions apply on the client or host platform:

- Standard user or group file permissions can be circumvented — for example, on Linux, by users permitted to execute `sudo` commands.
- A malicious user could gain unauthorised access to a user account on the client platform — for example, because remote login credentials are weak.
- A privileged user of the client platform is untrusted and could bypass security controls that protect other users on that platform.

## Discarding keys

Use the correct procedure to permanently destroy keys. You must erase either the OCS protecting the key or the entire Security World — there is no other method.

To permanently destroy a key, erase the OCS protecting it or erase the Security World completely. There is no other way to permanently destroy a key.

## Erasing a module from a Security World

Verify you have the ACS before erasing a module. You cannot restore the Security World afterwards without a valid ACS and the host data.

You do not need the Administrator Card Set (ACS) to erase an HSM from a Security World. However, unless you have a valid Administrator Card Set (ACS) and the host data for that Security World, you cannot restore the Security World after erasing it.

## Replacing an OCS

Follow these steps to replace an OCS. You must have the required number of ACS cards to proceed.

Replacing an OCS requires ACS authorisation from the Security World it belongs to. You cannot replace an OCS without the required number of ACS cards.

Replacing one OCS with another transfers the keys protected by the first OCS to the protection of the new OCS. The key material is unchanged — the process deletes the original Security World data and replaces it with data protected by the new OCS.

- Keys protected by an OCS can only be recovered to another OCS, not to a softcard.
- Softcard-protected keys can only be recovered to another softcard, not to an OCS.

After replacing an OCS, erase the remaining cards from the old OCS so its logical token can never be recovered.

Deleting OCS information from a client does not remove key data protected by that card set — this data may exist in a backup or on the RFS.

If you share a Security World across multiple client computers, propagate changes to the host data to all machines in the Security World after replacing the OCS.

## Replacing the ACS

Update all machines in the Security World after replacing the ACS. You must copy the updated world file to all machines or risk losing administrative access.

Replacing the ACS modifies the world file. Copy the updated world file to all machines in the Security World after replacing the ACS — failing to do so could result in loss of administrative access.

Erase your old Administrator Cards as soon as you have created the new ACS. An attacker with the old ACS and a copy of the old host data could reconstruct all your keys. With a current backup, they could access even keys created after the ACS replacement.

## Firmware upgrade

Back up NVRAM data before upgrading firmware. You must use the NVRAM-backup utility if the module has SEE program data or NVRAM-stored keys.

If you are upgrading a module that has SEE program data or NVRAM-stored keys in NVRAM, back up your data first using the NVRAM-backup utility.

### Setting the minimum VSN after a firmware upgrade (nShield 5)

After loading and testing new authorised firmware, use `hsmadmin setminvsn` to set the minimum Version Sequence Number (minVSN) to the VSN of the newly loaded firmware. This protects the HSM against firmware rollback and downgrade attacks.

If a firmware update is attempted with a VSN below the minVSN, the HSM refuses the update. The minVSN is set at manufacture to the VSN of the initial firmware.

## Enabling and disabling remote upgrade

Enable or disable remote upgrade for your nShield Connect as required. Disable remote upgrade if you do not need it.

## Migrating keys to a v3 Security World

!!! note
    A v3 Security World is a Security World created using an nShield release v12.50 or later.

In addition to the guidance in the HSM User Guide, follow these security steps when migrating keys to a v3 Security World:

- Perform key migration in a controlled environment where both the Source and Destination HSMs are logically and physically isolated from all external influences. This reduces the risk of external manipulation of the Destination Security World or HSM.
- Explicitly initialise the Source and Destination Security Worlds on their respective HSMs so that both the migration tool operator and ACS quorums can verify that the correct Security Worlds are in use and keys will be migrated to the correct Destination Security World.
- Have ACS holders for the Source Security World verify the migration tool parameters — particularly the Destination HSM identifiers — before authorising key migration.

When migrating to a v3 Security World, each migrated key's security strength equals the minimum of: the application key itself, the Source Security World, and the Destination Security World (128 bits minimum for v3).

During migration, keys that are outside the HSM's protected boundary are always encrypted with keys of greater than 112-bit security strength.

## Untrusted input validation

You must validate data received from untrusted sources before use. For example, if you receive HSM-encrypted data containing an authentication tag from an untrusted source, verify the tag against a known trusted value. Do not trust the data source authentication tag length value present in the received data — verify it against a known trusted value to mitigate against manipulation of received ciphertext that attempts to reduce the security strength of the data source authentication protection.

## Related topics

- [Manage keys in your Security World](key-management.md)
- [Audit your HSM deployment](audit.md)
- [Respond to a security incident](security-incident-and-response.md)
