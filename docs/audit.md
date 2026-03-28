---
description: "Audit your nShield HSM deployment to verify security procedures are followed. Covers HSM location records, ACS and OCS audit, log review, and audit logging"
---

# Audit

Use this section to audit your nShield Hardware Security Module (HSM) deployment. The nShield Security World is a key management framework spanning the nShield HSM range. This section covers HSM and card reader location records, Administrator Card Set (ACS) and Operator Card Set (OCS) card audits, log review, and audit logging configuration. For implementation details, see the HSM User Guide. Verify that your security procedures are in place, review HSM and card locations, and inspect logs regularly to confirm your Security World operates as intended. As auditor, remain independent of the HSM administrator.

## HSM and card reader location

Record the location of each of your HSMs and card readers using unique identifiers — including model, serial number, and any local asset ID. Update your record whenever you move an HSM or card reader.

- State in your Security Procedures how often you will verify the recorded location of each HSM and card reader.

### Physical inspection

When you verify HSM and card reader locations, also inspect each unit for integrity, including tamper mechanisms as described in [Supply and Transportation](supply-and-transportation.md).

- State in your Security Procedures how often you will verify the integrity of each HSM and card reader, including tamper mechanisms.

## ACS and OCS

Record the location (for example, a safe) or owner of each card in an Administrator Card Set (ACS) and Operator Card Set (OCS), following the policy in your security policy. Update this record whenever you move or transfer a card.

- State in your Security Procedures how often you will verify the recorded location or owner of each ACS and OCS card.

For guidance on responding to missing ACS or OCS cards, see [Security Incident and Response](security-incident-and-response.md).

## Logs

Log types, filtering options, and integrity mechanisms vary across your nShield platforms. Some logs use tamper or cryptographic protection; protect logs that do not use these mechanisms through procedural controls.

Use your threat analysis to determine which logs to collect and which filters to apply for your specific HSM deployment.

As auditor, remain independent of the HSM administrator. Apply the following checks:

- Audit all changes after modifying an HSM configuration to confirm the configuration was updated as intended.
- Inspect logs regularly to verify the unit's configuration matches your Security Policy.
- Inspect logs at a frequency defined in your Security Procedures.
- Define in your Security Procedures which log entries require action.

The following events may indicate a security concern:

- Investigate access outside working hours
- Investigate unusual configuration changes
- Investigate unexpected unit power cycles

State the required response to each of these events in your incident response process.

Identify a log backup policy in your Security Procedures, including the authorisation required to delete logs after backup.

## HSM audit logging

Enable audit logging when you create your Security World. Audit logging sends HSM log events to an external log collector outside the HSM boundary.

Audit logging uses an integrity mechanism to protect logs from tampering and to verify that the logs originate from a legitimate nShield HSM (authenticated by the HSM's KLF2 key and associated KLF2 warrant).

By default, audit logging records startup, setup, and credential presentation events — but not all key usage events. This makes it a lightweight option. You can additionally flag individual keys for logging, so that all usage of those keys generates an audit event. Use this higher-volume option for high-value, low-usage keys such as Root CA keys.

Common Criteria Security Worlds enable additional audit logging by default compared to standard Security Worlds.

As auditor, inspect the logs. Check for missing logs, integrity failures up to the trusted root, and log entries that require action.

Verify logs using:

- The `cef-audit-verify` tool — for Common Event Format (CEF) audit logs produced before v13.5 firmware
- The nShield Audit Log Service and `nshieldaudit` tool — for logs produced by v13.5 firmware and later

## Audit logging time

Set the nShield Connect clock and real-time clock (RTC) correctly to ensure accurate timestamps in the Audit Log. If you enable Network Time Protocol (NTP), the nShield Connect clock synchronises automatically. Both clock times appear in the Audit Log and in other logs.

If you do not use NTP, the nShield Connect clock and RTC are subject to drift. Set them regularly using an accurate, trusted local time source.

Accept nShield Connect and RTC timestamps as accurate only when the two values agree within a reasonable margin of error.

## nShield 5 signed system logs

Fetch and verify nShield 5 signed system logs alongside your nCore audit logs. You must use the nShield Audit Log Service and `nshieldaudit` tool to retrieve them.

In addition to nCore audit logs associated with the Security World, nShield 5 devices generate signed system logs covering device-level events — such as startup events, connection attempts, and operations involving non-nCore services.

These signed system logs are always enabled on nShield 5 devices running v13.5 firmware or later. Fetch and verify them using the nShield Audit Log Service, and query them with `nshieldaudit` alongside the nCore logs.

## Related topics

- [Operate your HSM securely](operation.md)
- [Respond to a security incident](security-incident-and-response.md)
- [Manage keys in your Security World](key-management.md)
