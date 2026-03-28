---
description: "Commission your nShield HSM for live operation. Follow the preparation, installation, hardware setup, network configuration, and Security World creation"
---

# Commissioning

The nShield Security World is a key management framework spanning the nShield Hardware Security Module (HSM) range.


Use this section to commission your nShield Hardware Security Module (HSM) for live operation. Follow the steps in order: prepare your environment, install hardware and software, configure the network, then create your nShield Security World. The Security World is the key management framework spanning your nShield HSM range. Conduct all commissioning activities in a secure environment as described in [Environment](environment.md), using authorised individuals only.

## Preparation

Before commissioning, complete these steps:

- Perform a threat analysis of your deployment environment, or use an existing threat analysis.
- Review this Security Manual and determine the procedures and configuration required to meet the threats identified. Document requirements in a Security Policy so all users understand the controls needed for secure operation.
- Ensure all host operating systems are current, supported versions with the latest patches applied.
- Install anti-virus software on the host system and keep it updated with automatic updates.

## Installation

Entrust supplies standard component bundles that contain the necessary components for your installation, plus individual components for supported applications.

Install all supplied software components, or only those you require. During installation you choose which bundles and components to install. Base your choice on:

- Applications that will use the HSM
- Check available disk space
- Your software installation policy — installing only required software reduces the threat from vulnerabilities in unused services

Verify your software CDs using their SHA256 checksums. If you have concerns about the integrity of a received software CD, verify the file checksums with Support before proceeding.

## Hardware

### Trusted Verification Device

Use the nShield Trusted Verification Device (TVD) with the Remote Administration Client. This specialised smart card reader lets you securely confirm the Electronic Serial Number (ESN) of the HSM you want to connect to, using the TVD display.

!!! important
    Use only a Trusted Verification Device (TVD) obtained through a trusted supply chain.

Provide the list of valid ESNs out-of-band for manual verification. Keep this list secure and make it available only to authorised cardholders.

Train TVD users to recognise the following:

- Only the ESN of the HSM selected in the Remote Administration Client should appear on the TVD display.
- The TVD requests only a single confirmation when setting up communication with an HSM.
- Any other text on the TVD display, or any ESN other than the expected one, is illegitimate — cancel the card-loading operation immediately.

### Mode switch and jumper switches (nShield Solo only)

The mode switch on the back panel controls the operating mode. See the HSM User Guide for instructions on checking and changing the mode.

Set the physical mode override jumper switch on the nShield Solo circuit board to ON to prevent accidental operation of the mode switch.

!!! note
    Set the remote mode override jumper switch to ON to prevent mode changes using the `nopclearfail` command. Do this if your threat analysis identifies a risk that a remote user could interrupt operational service. Your security policy must then require the physical mode switch — and a locally present, trusted role holder — to authorise mode changes.

## Network configuration

### Firewall settings

Configure your firewall so that port settings are compatible with your HSMs and allow access to required system components. See the Firewall settings section in the HSM User Guide for default port numbers. Open only the ports you require and restrict IP addresses on those ports to those needed.

### Simple Network Management Protocol (SNMP)

The nShield SNMP Agent is based on the NET-SNMP code base. Deploy the nShield SNMP agent only on a private network or a network protected from the public internet by an appropriate firewall. The agent runs with reduced privileges on Windows and Linux to mitigate potential risks in third-party code.

By default, the nShield SNMP installation allows read-only access to the Management Information Base (MIB) with any community string. No write access is configured by default, and no software or HSM configuration changes can be made through the SNMP agent.

Set up suitable access controls in the `snmpd.conf` file rather than using default settings in production. Use a firewall to restrict agent access to legitimate client machines. Protect configuration files if they contain sensitive information.

On Linux, the SNMP agent starts automatically after installation. Configure it before installing in a production environment to ensure correct settings from the start. On Windows, the agent runs only after enabling it with `snmpd -register` — apply configuration changes before this step.

## Date and time

Set your date and time correctly early in the commissioning process to support `hardserver` and audit logging.

### Set the nShield Solo and nShield Connect RTC

Set the real-time clock (RTC) using an accurate trusted local time source as part of commissioning.

!!! note
    The RTC backup battery on the nShield Solo and nShield Solo+ lasts only two weeks when the HSM is unpowered. Reset the RTC on power-up if battery exhaustion occurs. See the `rtc` utility documentation for instructions.

### Set the nShield Connect date and time

Set the nShield Connect date and time using an accurate trusted local time source as part of commissioning. The nShield Connect supports a Network Time Protocol (NTP) client that, if activated, synchronises the nShield Connect time automatically.

!!! important
    NTP has a history of security vulnerabilities. Activating NTP increases the threats your nShield Connect is exposed to, and not all NTP threats can be mitigated by design. Use NTP only if your risk analysis identifies suitable controls. Mitigations may include:

    - Using only authenticated NTP sources where possible
    - Monitoring NTP servers for availability and generating alerts if the NTP server becomes unavailable

### Set the host date and time (nShield Solo only)

Set the host date and time using an accurate trusted local time source as part of commissioning. Set it as early in the commissioning process as possible to support `hardserver` logging.

## nShield Connect and client configuration

### Configuring Ethernet interfaces (IPv4 and IPv6)

Your nShield Connect has two Ethernet interfaces. Following your security policy, assign one interface an internal IP address and the other an external IP address to support separated networks. When you do not need to use the interfaces separately, bond them for redundancy or higher throughput. Disable any Ethernet interface that is not in use.

### Configuring the hardserver interfaces

By default, the `hardserver` listens on all interfaces. Restrict listening to specific interfaces if, for example, you want to connect only one Ethernet interface to external hosts.

Configure Ethernet interfaces on the HSM before configuring the `hardserver`. Do not permit the `hardserver` to listen on any interface connected to the public internet — this would expose the HSM to network-based attacks. Place the HSM behind a firewall during commissioning to restrict traffic to legitimate clients and the Remote File System (RFS).

### Impath resilience

Impath resilience is a session resumption feature that lets client hardservers reconnect securely to an existing session from a fresh TCP connection after a network outage. By default, Impath resilience is enabled with a 1,800-second (30-minute) timeout.

Your threat analysis and knowledge of network reliability determine whether to enable Impath resilience and what timeout to set. A five-minute timeout offers a reasonable trade-off between security and resilience to transient network issues.

### Configuring the RFS

The nShield RFS is a protocol for securely sharing files between machines. Use it as:

1. A file store for the nShield Connect config file, logs, upgrade images, feature files, CodeSafe application files, and Security World and card set files.
2. A way to share Security World files between peer machines using `rfs-sync`.

By default, the RFS protocol uses TCP port 9004. Enable this port in your firewall for TCP communication. If a client machine is not acting as an RFS, disable port 9004 in the `[server_remotecomms]` config section. You can also restrict the `hardserver` to listen only on a specific IP address or network interface.

#### nShield Connect RFS

Back up the entire RFS contents regularly — it is required to restore the nShield Connect or its replacement after a failure.

To set up the RFS, you need the nShield Connect:

- IP address
- ESN
- KNETI hash (HKNETI)

Even on a trusted network, verify the ESN and KNETI reported by `anonkneti` independently using the nShield Connect front panel or serial console. On an untrusted network, obtain ESN and KNETI information directly from the front panel — this is essential. Use this information with the `rfs-setup` command to create the RFS.

!!! important
    Do not use the `--write-noauth` option with `rfs-setup` over insecure networks — this skips RFS authentication and creates a masquerade attack risk.

Enable mutual secure authentication between the nShield Connect and the RFS. Record the RFS's KNETI hash in the nShield Connect configuration file. To specify RFS authentication when enrolling via the front panel, verify the KNETI hash displayed matches the output of `enquiry -m 0` or `anonkneti -m 0 127.0.0.1` run locally on the RFS machine.

### Configuring client cooperation

If your machine needs access to a peer RFS for sharing Security World files (cooperating client mode), use each machine's Software Key KNETI — or an nToken- or local HSM-protected KNETI if available — to set up secure authentication between RFS peers.

Do not use `rfs-setup --gang-client --write-noauth`. Instead, run `rfs-setup` with the HKNETI of the peer that will initiate connections. On the initiating end, specify the HKNETI (and ESN if module-protected) when running `rfs-sync --setup`.

### Remote configuration

Use the HSM (`hardserver`) configuration file to enable or disable remote reboot, remote mode changes, and remote upgrade. Disable remote upgrade if not required.

### Configuring the nShield Connect for a crypto client

Add a crypto client to the permitted client list using one of these methods:

1. **Front panel:** Select the secure authentication option. This temporarily requires port 9004 to be accessible on the client machine so authentication options can be queried. After enrolment, disable this port on the client unless it is also acting as an RFS. Verify the KNETI hash displayed on the front panel matches the hash reported locally on the client machine.

2. **Push a config file:** Add a new entry in the `[hs_clients]` section with the HKNETI (`keyhash` field), ESN (if required for a module-protected KNETI), and the `clientperm` access level.

Restrict privileged connections to source ports below 1024 as an additional defence-in-depth measure on Linux.

Authenticate all clients — privileged or unprivileged — with a KNETI network authentication key (software, nToken-protected, or local HSM-protected). Even for dynamically created clients such as containers, use a KNETI key associated with each container rather than allowing unauthenticated connections.

### Configuring a client to communicate with an nShield Connect

Use `nethsmenroll` to add an nShield Connect to the client hardserver configuration. Supply the optional ESN and HKNETI parameters — obtain these from the nShield Connect front panel or serial console. Confirm that the values reported by `nethsmenroll` match those shown on the front panel.

!!! important
    Do not use `nethsmenroll --no-hkneti-confirmation` in production. This option exists for test automation only.

If the HKNETI ceases to match existing client hardserver configurations, investigate the cause. If you find no satisfactory explanation, suspect tampering.

### Configuring a client with an nToken

If the client has an nToken, use it to generate and protect a key for Impath communication. Obtain the nToken key hash from the client, then view the client configuration from the nShield Connect front panel and verify that the displayed nToken key hash matches.

!!! note
    If no nToken is available, use software-based authentication. If neither option is available, the nShield Connect authenticates the client by IP address only — this is weaker and may be vulnerable to IP spoofing.

### Configuring the serial console

The serial console on the nShield Connect is enabled by default. Disable it from the front panel if not required — a factory reset re-enables the serial console.

!!! important
    The serial console ships with a known default password. Change it on first login. The minimum password length is 5 characters, but enforce stronger passwords following the guidance in [Access Control](access-control.md).

Apply physical access controls to protect the serial cable, serial port aggregator, and administrator console. Apply logical access controls to protect the serial console, serial port aggregator, and administrator console. Protect all passwords from unauthorised access or viewing. Keep all equipment supporting the serial console connection patched with the latest software.

The username for serial console access is `cli` with default password `admin`. Change the password on first login.

## Logging and debugging

Set up your logging requirements after setting the date and time.

### Set up logging

The following log types are available on nShield platforms:

| Type of log | Purpose and configuration | nToken, Edge | Solo+, Solo XC, nShield 5s | Connect+, Connect XC, nShield 5c |
|---|---|---|---|---|
| Audit logging | Operational and key usage activity. Enable only at Security World initialisation. | Yes | Yes | Yes |
| Hardserver log | Errors and troubleshooting. Controlled through environment variables. Logs nothing by default. | Yes | Yes | Yes |
| Security World log | Errors and troubleshooting. Controlled through environment variables. Logs nothing by default unless overridden by individual libraries. | Yes | Yes | Yes |
| System log | System events on nShield Connect. Always enabled. | No | No | Yes |
| Tamper log | Tamper events on nShield Connect. Always enabled. | No | No | Yes |
| Net-SNMP daemon log | Net-SNMP daemon activity. Starts with the SNMP service. | Yes | Yes¹ | Yes¹ |

¹ The Net-SNMP daemon log is produced by the client.

### Audit log

The Audit Log originates in the nShield Solo HSM (including the nShield Solo inside every nShield Connect). It uses a cryptographic mechanism to assure integrity when distributing to Security Information and Event Management (SIEM) collectors.

Apply these controls for your Audit Log:

- Locate SIEM collectors in a protected environment that limits physical access to authorised users.
- Maintain Audit Log availability by configuring multiple SIEM collectors per HSM, scheduling regular backups, and applying physical and logical access controls for backup access.
- Configure the network to prevent message corruption, congestion, forwarding loops, and incorrect delivery.
- Follow Entrust's Audit Logging Verification process to receive an authenticated Trusted Root. See the Audit log verification section in the HSM User Guide.
- Before shutting down the nShield Connect, wait at least 17 minutes after the final log message is dispatched — this ensures the outstanding integrity verification message is sent. Confirm receipt on the SIEM before shutting down the HSM. Do not enter commands that generate log messages during this period.

### nShield Connect tamper log

The nShield Connect tamper log is stored within the nShield Connect and protected by its tamper mechanisms. It cannot be erased using the front panel or serial console.

The tamper log is an informational tool — it is not a security control and cannot be relied on if you have reason to believe the device has been tampered with. Physical access controls and HSM audit logs are the effective measures to prevent and detect misuse.

### nShield Connect system and hardserver logs

The nShield Connect system and hardserver logs can be stored within the tamper response boundary or pushed to the RFS or a remote syslog server.

- **Local storage:** Logs are protected by the tamper response boundary but are lost on reboot. Logging stops when the file system is full. View logs via the front panel or fetch on demand using `appliance-cli` from a privileged client.
- **RFS push:** Logs are sent securely with mutual authentication (if configured). Push logs every minute to minimise data loss on reboot or power failure.
- **Remote syslog (UDP):** No authentication, secrecy, or integrity mechanism is applied. Use only on a trusted private network.

### nToken, nShield Edge, and nShield Solo hardserver logs

For these products, hardserver logs are stored on the host platform with no integrity mechanism. Apply physical, procedural, and technical controls to the host environment to protect log integrity.

### Hardserver and system log environment variables

Environment variables control the amount of logging information produced. Your system management and security policies determine the required logging level. See the Environment variables section in the HSM User Guide for details.

### Debugging options

Enable debugging only when required for development. Disable debugging in production environments — debug output can leak information useful to an attacker.

!!! note
    SEE debugging is disabled by default. You can enable it for all users or restrict it to Administrator Card Set (ACS)-authorised users.

## Configure access control

For guidance on access control mechanisms to protect application keys in a Security World, see [Access Control](access-control.md).

## Configure Security World

### Security World options

!!! note
    Place the HSM in a secure and trusted environment before creating or loading a Security World.

Decide which type of Security World you need before creating it. Security World options fall into these groups:

- **Basic options** — required for all Security Worlds, including environment variables
- **Recovery and replacement options** — configure these if you need to recover or replace the Security World, keys, or passphrases. Disabling OCS and softcard replacement means you can never replace lost or damaged OCSs and can never recover keys protected by those cards, even if the keys were generated as recoverable. Recovery data for application keys is kept separate from recovery data for the Security World key.
- **SEE options** — required only if you are using CodeSafe
- **Replacement options** — for replacing an existing Security World with a new one

Security World options are highly configurable at creation but cannot be changed afterwards. Review all options, particularly those required by your deployment, before creating a Security World.

### Application interfaces

A Security World can protect keys for any application correctly integrated with the Security World software. Review all configuration settings for each application interface. Activate only required settings and disable all others.

### NVRAM options

You can store application keys in the HSM's nonvolatile random access memory (NVRAM) instead of the Key Management Data directory on the host. NVRAM space is limited and provides no greater security than storing keys in the Key Management Data directory. Store encrypted key blobs in the Key Management Data directory where space is limited only by host capacity.

### Loading a Security World after firmware update

Before initialising a Security World on an HSM after a firmware upgrade, have the ACS cardholder quorum verify the firmware version is correct. This mitigates the risk of a firmware rollback or downgrade attack.

## Remote services

### Remote Administration Service (RAS)

To use Remote Administration with nShield Connects, install the RAS on a machine enrolled as a client of those nShield Connects — typically the RFS machine.

To use Remote Administration with nShield Solos, install the RAS on the host where the `hardserver` and nShield Solos reside. For multiple nShield Solo hosts in a Security World, install the RAS on each one. Install KLF2 warrants on the host machine for Solo+ or Solo XC — this is not required for nShield 5s or nShield Connects.

Always use a secure private communications channel — such as a Virtual Private Network (VPN) or SSH tunnel to TCP port 9005 — between the Remote Administration Client (RAC) and the RAS when they are on separate computers.

Use nShield Remote Administration Cards for Remote Administration. These smart cards negotiate mutually authenticated, cryptographically secure connections with the HSM using warrants as the root of trust.

Configure dynamic slot timeouts to control how long a smart card remains responsive under high latency — account for expected network delays. Use the `cardlist` file to specify the serial numbers of remote cards allowed by nShield client tools.

Disable Remote Administration by setting dynamic slots to 0 if not required.

### Remote Operator

The Remote Operator feature transmits smart card contents from an attended module to an unattended module securely. To use Remote Operator:

- Use a persistent Operator Card Set (OCS) — see [Access Control](access-control.md) for guidance
- Ensure both the attended and unattended modules are in the same Security World

Modules initialised into Security Worlds have remote card set reading enabled by default. Disable this feature if not required.

## SEE (Secure Execution Environment)

If your threat analysis identifies that application code is vulnerable to attack, use the integrity and confidentiality protections provided by an SEE machine. The SEE extends the physical security boundary to protect both cryptographic keys and security-critical application code and data. See the CodeSafe Developer Guide for details.

Configure SEE options at Security World creation if you are using the nShield SEE. If SEE is not installed, SEE options are not applicable.

### RTC options

By default, Security Worlds are created with access to RTC operations enabled. In Federal Information Processing Standard (FIPS) Level 2 and Level 3 Security Worlds, you can optionally control RTC access using an ACS at Security World initialisation. Your threat analysis determines whether ACS authorisation is required for RTC access.

## Set up SSH communications (nShield 5 and later)

SSH secure channels protect communications between the host and nShield 5 and later HSMs. The SSH protocol uses separate key pairs in the host and the HSM for mutual authentication. Each service within the HSM uses a separate SSH channel. Install SSH client public keys on each service before use.

Back up your `sshadmin` private key if your security policy permits — this lets you re-establish communication with the HSM if other installed service keys are erased or lost.

Treat SSH keys connecting the host to the HSM as sensitive assets and protect them appropriately. On the HSM, private keys are stored in flash memory. On the client, they are stored on the host and protected by OS filesystem permissions (root or local Administrators have write access; root, `nfast` user, or permitted groups have read access). Client key files are stored in an encrypted format.

Secure access to client host machines, any backup files, and any passphrases set on the keys to prevent an adversary from retrieving the client private keys.

## Related topics

- [Configure access control](access-control.md)
- [Manage keys in your Security World](key-management.md)
- [Operate your HSM securely](operation.md)
