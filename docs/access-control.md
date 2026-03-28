---
description: "Configure access control for your nShield Security World. Covers ACS and OCS setup, key protection levels, operator roles, quorum guidance, and remote"
---

# Access control

The nShield Security World is a key management framework spanning the nShield Hardware Security Module (HSM) range.

Use this section to configure access control for your nShield Security World. The nShield Security World is a key management framework spanning the nShield HSM range. Choose the right key protection level for your deployment, configure Administrator Card Set (ACS) and Operator Card Set (OCS) quorums, and set up operator roles.

## Security World access control architecture

This section describes the access control options available within a Security World and the trade-offs of each option to help you choose the right configuration for your threat environment.

### User groups for Security World software installation

!!! important
    On Linux and Windows, root or administrator privileges are required to install Security World software and to configure access to local HSMs.

    On Linux, run nShield utilities as a normal user if you only need unprivileged nCore commands and read-only key access; as a user in the `nfast` group for privileged nCore commands and the ability to modify `kmdata`; or in the `nfastadmin` group for `hsmadmin` and `csadmin` operations. Do not run production workloads as root or as the `nfast` user.

    On Windows, ordinary local users can run unprivileged nCore commands and have read access to `kmdata` keys by default. Administrator privileges are required for privileged nCore commands and writing to the Key Management Data directory. You can customise the groups allowed to make privileged and unprivileged connections in the config file, and use manual filesystem DACL changes or a non-default Key Management Data directory to set alternative access controls.

    See the Roles section for details.

### Security World access control

Every Security World is protected by an ACS created at Security World initialisation. Use the ACS to:

- Control access to Security World configuration
- Authorise recovery and replacement operations

The ACS consists of N smart cards, of which a smaller or equal number K must be presented to authorise an action. This required number K is the quorum. Distribute the cards among authorised role holders so that a quorum of role holders must cooperate to authorise operations.

See [Administrator Card Set (ACS) protection](#administrator-card-set-acs-protection) for configuration guidance.

### Application key protection levels

Your Security World provides three levels of application key protection. Choose the level appropriate to your threat analysis:

- **Module protection** — the key is protected only by the HSMs in the Security World. Any application on the host can load and use the key (but cannot export it from the HSM unless the Access Control List permits this).

- **Softcard protection** — the key is additionally protected by a passphrase. An application prompts you for the passphrase before loading the key.

- **OCS (token) protection** — the key is protected by a card set. Each card set consists of N smart cards, of which K cards must be presented to authorise key loading. Optionally protect each smartcard with a passphrase. Create card sets in persistent or non-persistent mode, with or without a timeout, to match the security requirements of your environment.

Module-protected keys require no passphrase and are usable by any authorised application running on a server fitted with an HSM (or connected to an nShield Connect) initialised with the same Security World. Use this level for high-availability servers that must recover immediately after a reset without operator intervention. Ensure the host environment is secure, since key access depends on the access controls of the underlying operating system. See [Module protection](#module-protection) for controls to prevent unauthorised access.

Softcard protection gives you tighter control through explicit passphrase authorisation. A softcard is a file containing a logical token that you cannot load without the correct passphrase. A softcard functions as a persistent 1/1 logical token — once loaded, it remains valid until its key handle is destroyed. A single softcard can protect multiple application keys. See [Softcard protection](#softcard-protection) for controls to prevent unauthorised access.

OCS protection provides the highest level of application key security. We recommend adding passphrase protection to OCS cards. An attacker would need a quorum of OCS smartcards plus any associated passphrases to load the OCS logical token. The token remains loaded until its reauthorisation timeout expires, the last OCS card is removed (for non-persistent tokens), or it is explicitly destroyed. A single OCS can protect multiple application keys. See [OCS protection](#ocs-protection) for detailed guidance.

Your Security World requires you to use your operating system's security features to control which users can access the Security World and write data to the host.

Your security policy — based on your threat analysis — determines the protection level required for your application keys.

## Security World access control guidance

### Administrator Card Set (ACS) protection

Each Security World has exactly one ACS. When you create a Security World, configure:

- Decide whether to enable key recovery. If you disable this option, you cannot replace lost or damaged OCSs and cannot access keys protected by those cards. You cannot enable this feature later without reinitialising your Security World and discarding all existing keys.
- The number of administrator cards required to authorise key recovery
- Decide whether to enable passphrase recovery.
- The number of administrator cards required to authorise passphrase recovery
- The number of administrator cards required to load this Security World onto a new HSM
- The number of administrator cards required for various features
- Decide whether to disable certain features.
- Specify a passphrase for each card (strongly recommended). (strongly recommended)

Base your ACS quorum configuration on a threat analysis. Set the ACS quorum larger than the quorum required to activate any individual feature — this ensures the ACS quorum is only used when legitimately needed. Set the Key and passphrase recovery quorum greater than 1 but less than the ACS quorum, since these features can authorise access to OCS- and softcard-protected keys.

### NSO-timeout guidance

When you create a Security World — for example using the `new-world` utility — specify the NSO-timeout. This is the maximum time that can elapse between ACS quorum authorisation and when reauthorisation is required. The NSO-timeout is relevant primarily when the last administrator card of the quorum is left in the card reader of an HSM; standard practice is to remove the last administrator card as soon as the current administrative task is complete.

Base the NSO-timeout on the administrative tasks the ACS quorum will authorise and the results of your threat analysis for nShield administrators. The minimum is 3 minutes per card. The default is the greater of 6 minutes per card and 1 hour. The maximum is the greater of 10 minutes per card and 2 hours.

Use these guidelines to set the NSO-timeout:

- If the HSM is for general cryptographic use and key migration may occur from another Security World, the default NSO-timeout is a good choice.
- If you consider the risk of an administrator card being accidentally left in the HSM card reader significant, set a shorter NSO-timeout. Set it large enough to complete the longest operation the ACS quorum must authorise.

### Creating and maintaining a quorum

Each card set consists of N smart cards, of which K cards are required to authorise an action. Each card stores only a share of the card set keys. You can only reconstruct these keys if you have K shares or more. Because cards can fail or be lost, set K less than N.

For a robustly secure ACS, set K to a relatively large value and N to less than twice K — for example, K/N ratios of 3/5 or 5/9. Determine your K and N values through a threat analysis of the protected data.

Identify role holders for each card in your Security Procedures. Assign roles based on area of responsibility and availability — role holders may need to recover a Security World at short notice in the event of a failure.

### Module protection

Module-protected keys are suitable for high-availability web servers that must recover immediately without operator intervention. Secure your host environment. Apply these controls:

- Restrict access to the HSM's nCore API to authorised and trusted users only.
- Restrict privileged nCore API access to users who definitively need it.
- Implement platform service user separation as described in SSH Client Key Protection (nShield 5s HSMs). Encrypt SSH private authentication keys for nShield 5 platform services. Keep the host OS patched, free from unapproved software, and protected against malware

### Softcard protection

Softcard protection requires a passphrase before loading application keys. Define your password policy in your Security Procedures — see [Logical token passphrase guidance](#logical-token-passphrase-guidance) for choosing strong passphrases.

Maintain a register of individuals who have access to each softcard to support operations and role transitions.

Determine in your Security Procedures:

- Whether a lost softcard can be recovered to a new softcard (enabled by default during Security World creation)
- Whether a lost softcard passphrase can be replaced (disabled by default during Security World creation)

You can protect multiple keys with a single softcard. Your threat analysis determines how many keys one softcard should protect.

!!! important
    Generating multiple softcards with the same passphrase increases your attack surface — an attacker who breaks the passphrase gains access to all keys protected by those softcards. Perform a threat analysis to determine a safe number of keys per passphrase.

Softcards are persistent — once loaded, a softcard remains valid for loading its protected keys until its KeyID is destroyed. Destroy KeyIDs as soon as required operations are complete.

### Logical token passphrase guidance

Use these sources to define your password policy:

- [National Cyber Security Centre (UK) — Password Guidance Summary](https://www.ncsc.gov.uk/)
- NIST Special Publication 800-63B, Appendix A — Digital Identity Guidelines
- SANS Password Construction Guidelines

A timing delay applies to password retries, but there is no retry lockout — a robust password policy is therefore essential. Configure a warning message for short passphrases during Security World creation (this applies to OCS and softcard passphrase entry throughout that Security World's lifetime).

Define the process for managing forgotten passphrases and the passphrase lifecycle in your Security Procedures.

### OCS protection

OCS protection provides the tightest control over application key usage, especially when combined with passphrase protection. OCS cards use physical smart cards (ISO 7816 compliant) that belong to a specific Security World. Only an HSM within that Security World can read, erase, or format the OCS. There is no limit to the number of OCSs you can create, and you can create or delete them at any time.

#### Creating and maintaining a quorum

Each card set consists of N smart cards, of which K cards are required to authorise an action. Each card stores only a fragment of the OCS keys. Set K less than N to account for card failures.

For a robustly secure OCS, set K to a relatively large value and N to less than twice K — for example, K/N ratios of 3/5 or 5/9. Your Security Procedures determine K and N values based on a threat analysis of the protected data.

Identify role holders for each card in your Security Procedures, assigning roles based on area of responsibility.

Determine in your security policy whether to require passphrases for cards. Passphrases add an additional barrier to attackers and may be necessary based on data value and physical card storage security.
- Whether to use persistent or non-persistent mode and whether a timeout is required — see [Persistence and non-persistence for OCS](#persistence-and-non-persistence-for-ocs). You cannot change the mode after creation.
- Whether lost OCS cards can be recovered to a new OCS — enabled by default during Security World creation.
- Whether OCS passphrases can be replaced if lost — disabled by default during Security World creation.

Replace lost or damaged cards as soon as you discover the loss or damage — this prevents reaching a state where a quorum is unavailable.

#### Card management guidance

- Store OCS cards securely when not in use.
- See [Logical token passphrase guidance](#logical-token-passphrase-guidance) for choosing strong passphrases.
- Do not record passphrases or hints in the same location as the card.
- Maintain a register of custodians and the cards they hold to support operations and role transitions. Create OCSs with names for the OCS and each card.
- Audit card sets regularly to confirm they are still present — see [Audit](audit.md) for details.
- Define the process for managing card loss, theft, or corruption in your Security Procedures. If a quorum of cards is compromised, the OCS-protected application keys are vulnerable to attack — see [Security Incident and Response](security-incident-and-response.md) for guidance.
- Define the requirements for correct identification, use, movement, storage, and protection of cards by authorised individuals in your Security Procedures — see [Audit](audit.md) for details.

#### Persistence and non-persistence for OCS

A non-persistent OCS (the default) lets you use protected keys only while the last required card of the quorum remains loaded in the HSM card reader. The HSM removes the keys from memory as soon as you remove the card. This gives you direct control over key usage.

A persistent OCS lets you use protected keys for as long as the application that loaded the OCS remains connected to the HSM (unless the application removes the keys explicitly or a usage or time limit is reached). Use persistent mode only after a threat analysis confirms it is safe for application keys to remain usable after the last OCS card is removed.

Both persistent and non-persistent OCSs support a timeout so that keys can only be used for a limited time after loading. The HSM forcibly unloads keys when the timeout expires. After an OCS times out, another application cannot load it unless the cards are removed and reinserted — except for dynamic slots, where a Trusted Verification Device (TVD) or passphrase must be used to confirm user interaction before the token reloads.

Timeouts operate independently of persistence settings.

To manually remove all keys protected by persistent cards, clear the HSM using any of the following:

1. Run: `nopclearfail --clear --all`
2. Press the Clear button on the HSM
3. Power off the HSM

Guidance on which persistence and timeout combination suits your deployment:

- **Persistent OCS with no timeout** — suitable for a web server; however, anyone who gains unauthorised application access can use the key
- **Non-persistent OCS with no timeout or a short timeout** — suitable for a root Certificate Authority; the key is unloaded when the card is removed, and becomes inactive after the timeout if the card is accidentally left in the reader

Your threat analysis determines the appropriate persistence and timeout configuration for each set of OCS-protected keys.

#### Application independence

OCSs are not tied to specific client applications. You can protect keys for different applications using the same OCS — but avoid protecting keys for many different applications with one OCS, since a compromise of the OCS would expose all those keys. Assign separate OCSs to different applications to limit the impact of any single OCS compromise. Assigning more than one OCS to an application key also maintains operation if one OCS is compromised.

## Application keys

When you generate or import an nShield key, the HSM associates it with an HSM-enforced Access Control List (ACL). The ACL prevents the key from being used for unsuitable operations and can require specific tokens before the key can be accessed. Your threat analysis determines the required ACL settings for each key.

### ACL restrictions for key wrapping and encapsulation keys

Assign key wrapping and de-encapsulation keys a single purpose — key wrapping or de-encapsulation only. Do not assign the decrypt permission to a wrapping or de-encapsulation key. If you do, a wrapped or encapsulated key could be exposed in plaintext on the client or host platform.

The ACL lets you restrict wrapping and de-encapsulation keys further. Restrict to:

- A specific wrapping or de-encapsulation mechanism
- A specific application key that can be wrapped or de-encapsulated
- Specific parameters for the wrapping or de-encapsulation mechanism

## nShield Connect front panel

For the nShield Connect, configure front panel access using **System > System configuration > Login settings**.

When you enable **UI Lockout with OCS**, log in with an authorised Operator Card before accessing the menus. You can still view unit information on the startup screen. The OCS used for front panel login must be persistent and not loadable remotely. Following the principle of separation of duties, do not use the OCS that protects physical access to the nShield Connect to also protect application keys.

When you enable **UI Lockout without OCS**, you cannot access the menus but can still view information on the startup screen.

Enable or disable the power button lockout independently when UI Lockout permits menu access.

Identify front panel settings in your Security Procedures based on a threat analysis of your environment.

## Configuring remote administration access to nShield Connect

A privileged connection is required for certain administrative tasks on the nShield Connect — for example, initialising a Security World or performing remote configuration operations. If you allow privileged connections, the client can issue commands such as clearing the HSM, which interrupts normal operation.

Add only unprivileged clients for normal operational application usage, unless those machines need administrative access such as clearing the HSM or remotely configuring CodeSafe 5 on a 5c. The Remote File System (RFS) machine is a privileged client by default — provided it is authenticated with a KNETI network authentication key — and has the additional privilege to push updated nShield Connect config files. Use the RFS machine as the general administration client and keep it separate from normal cryptographic operations. You can also disable the RFS as a privileged or config-push client in the Connect config file.

## Role holder lifecycle guidance

### Roles

Identify the roles that can access applications using the HSM, and define their access rights for application keys, in your Security Procedures. Assign access rights as the minimum required for each role.

### Windows user privileges

Use Windows user privileges to protect system integrity against deliberate or accidental acts. nShield Security World on Windows distinguishes two user levels by default:

- **Built-in Administrators group** — running with elevated privilege. The name of this group varies by Windows language.
- **Normal local users** have limited access by default.

The Built-in Administrators group is required for:

- Software installation; starting and stopping the `hardserver` and SNMP
- Privileged connections to nFast Server (`hardserver`) service
- nShield 5 administration operations with `hsmadmin` and `csadmin`
- Editing the config file

By default, normal users cannot create Security Worlds or modify other users' keys, but they can read them and create their own. Encrypted key copies are stored in Key Management Data (`C:\ProgramData\nCipher\Key Management Data\local`). Adjust file permissions to restrict key access to specific users or groups, or specify an alternative Key Management Data folder using the `NFAST_KMDATA_LOCAL` environment variable.

Customise unprivileged and privileged connection group access using the `nt_pipe_users` and `nt_privpipe_users` fields in the config file.

nShield services run with minimum required privileges. The nFast Server (`hardserver`) service runs as LocalSystem because of its privileged operations. Other nShield services run as virtual service accounts with unnecessary Windows privileges dropped at startup.

### Linux user privileges

Use Linux user privileges to protect system integrity. nShield Security World on Linux uses four user types:

- **Superuser (root)**
- **`nfastadmin` group user**
- **`nfast` group user**
- **Normal users**

Normal users can work with Security Worlds, card sets, and keys, but cannot create them. `nfast` group users can create Security Worlds, card sets, and keys, have read-write access to `kmdata` (`/opt/nfast/kmdata/local`), make privileged connections to the `hardserver`, and perform some nShield Connect administration operations.

Separate the `kmdata` access role from the privileged local client role by setting the `NFAST_KMLOCAL` environment variable to a non-default path and controlling access to that path independently.

`nfastadmin` group users can perform `hsmadmin` configuration and monitoring operations for nShield 5, and `csadmin` operations for CodeSafe 5. Override the default groups for nShield 5 service roles by setting environment variables at install time.

Root access is required for software installation and for starting and stopping services such as `hardserver` and SNMP.

### Access rights withdrawn

Define the requirements and timelines for withdrawing access rights in your Security Procedures. Revoke access when a role holder:

- Leaves the organisation
- Moves to a different department
- Changes roles
- Is on extended sick leave
- Is suspended from duties

### Dos and don'ts for access control

Most security system failures result from user error, not system flaws. Apply these rules:

- Keep your smartcards safe.
- Obtain smartcards only from Entrust or directly from the smartcard manufacturer.

    !!! note
        nShield Remote Administration Cards are available only from Entrust.

- Never insert a smartcard into a card reader you do not trust.
- Never connect a card reader you do not trust to your HSM.
- Do not enter passphrases on a server you do not trust.
- Never share your passphrase with anyone.
- Use a strong passphrase — see [Logical token passphrase guidance](#logical-token-passphrase-guidance).
- Implement the optional passphrase on Remote Administration cards to prevent misuse of the Logical Token share by a malicious or misconfigured HSM client.

## Related topics

- [Manage keys in your Security World](key-management.md)
- [Operate your HSM securely](operation.md)
- [Audit your HSM deployment](audit.md)
