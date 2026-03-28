---
description: "Manage cryptographic keys securely with your nShield HSM. Covers key generation, algorithms, cryptoperiods, backup, import, key separation, and PKCS #11"
---

# Key management

Use this section to manage cryptographic keys securely in your nShield Hardware Security Module (HSM) Security World. The nShield Security World is a key management framework that spans the nShield HSM range. This section covers key generation, algorithms, cryptoperiods, backup, import, and key separation. It does not cover the Security World infrastructure setup — see the Management Guide for that. in your nShield Hardware Security Module (HSM) Security World — a key management framework that spans the nShield HSM range. Use your risk analysis to determine the cryptographic algorithm strength, key sizes, and key lifetimes required to protect your data.

## Key management schema

Review the Security World infrastructure section in the Management Guide before designing your key management schema. You must understand the key hierarchy to deploy keys securely.

Refer to the Security World infrastructure section in the Management Guide for a description of the Security World infrastructure for managing the secure lifecycle of cryptographic keys. See [Access Control](access-control.md) for a description of the key types available to protect application keys.

## Security World security strengths

Security strength is measured in bits. It represents the amount of work required to break a cryptographic algorithm or system. Specific values are 80, 96, 112, 128, 192, and 256 bits. For example, a security strength of 112 bits requires on average 2¹¹¹ operations to brute-force.

Select your Security World mode when you create the Security World. Available modes, cipher suites, Federal Information Processing Standard (FIPS) mechanism compliance, and security strengths are:

| Mode | Cipher suite | Automatic FIPS compliance with NIST SP800-131A Revision 1 | Security strength |
|---|---|---|---|
| FIPS 140 Level 3 (FIPS approved mode enforced) | ECp521mAES or DLf3072s256mAEScSP800131Ar1 | Yes | 128 bits |
| Unrestricted (default) | DLf3072s256mAEScSP800131Ar1 | No (see note 1) | 128 bits |
| Common Criteria CMTS (see note 2) | DLf3072s256mAEScSP800131Ar1 | No | 128 bits |

**Note 1:** In Unrestricted mode, non-approved functions — including algorithms not compliant with NIST SP800-131A Revision 2 — are available. Selecting them puts you outside a FIPS approved mode of operation.

**Note 2:** Common Criteria EN 419 221-5 Protection Profiles for TSP Cryptographic Modules — Part 5, Cryptographic Module for Trust Services.

Security Worlds created with a pre-v12.50 release can be loaded. Their cipher suites, FIPS compliance, and security strengths are:

| Mode | Cipher suite | Automatic FIPS compliance with NIST SP800-131A Revision 1 | Security strength |
|---|---|---|---|
| Not applicable | DLf3072s256mRijndael | No | 128 bits (see note 3) |
| Not applicable | DLf1024s160mRijndael | No | 80 bits |
| Not applicable | DLf1024s160mDES3 | No | 80 bits |

**Note 3:** While the cipher suite provides 128-bit security strength, some underlying cryptographic mechanisms used by this cipher suite are no longer FIPS-approved.

The National Institute of Standards and Technology (NIST) provides key management guidance in NIST SP800-131A Revision 1, which advises a minimum security strength of 112 bits for algorithms and keys protecting sensitive data. This is now the industry standard minimum.

## KML type and security strength

Review your KML type selection carefully. Changing the KML type from the cipher suite default affects your Security World security strength.

Selecting a Key Management Layer (KML) type other than the default for the cipher suite may affect the Security World security strength — typically to improve performance at a potential security tradeoff.

Two scenarios apply:

**Selecting ECDSA NISTp256hSHA1 KML type with a DSA-3072 Security World:**
- There may be a performance boost from using NIST P-256 with no security tradeoff.
- This configuration is not supported in FIPS 140 Level 3 mode.

**Downgrading the KML type from NISTp521hSHA1 to NISTp256hSHA1 with an ECDSA Security World:**
- There is a performance boost from using NIST P-256 instead of P-521, but with a security tradeoff — this downgrades KML security from 256-bit to 128-bit.
- This configuration is approved in FIPS 140 Level 3 mode.

## Application key algorithms and key sizes

Select algorithms and key sizes based on your threat analysis. You must ensure the chosen strength meets or exceeds the industry standard minimum of 112 bits.

Select algorithms and key sizes sufficient to protect your data against the threats identified in your threat analysis. The Security World cipher suite security strength limits the security strength of any key or algorithm the HSM uses.

Use these references to determine appropriate algorithms and key sizes:

- NIST SP 800-57 Part 1 Revision 4 — Comparable Algorithm Strengths section
- BlueKrypt Cryptographic Key Length Recommendation

Available algorithm families include:

- Use symmetric algorithms as required
- Select from asymmetric algorithms based on: Integer Factorization Cryptography (RSA), Discrete Logarithm Cryptography (DSA, Diffie-Hellman), and Elliptic Curve (ECDSA)
- Apply hash functions as required

Use the `nfkmverify` command-line utility to identify algorithms and key sizes in bits. See the Key Management Guide for details.

## Cryptoperiods

A cryptoperiod is the time span during which you are authorised to use a specific cryptographic key.

Perform a threat analysis, considering data sensitivity and available mitigating controls, to determine appropriate cryptoperiods for your keys. Setting a cryptoperiod limits the amount of data exposure if a key is compromised.

Apply these cryptoperiod rules:

- Assign a cryptoperiod to all your application keys.
- Assign a cryptoperiod to your Security World itself — Security World keys protect application keys, so your Security World cryptoperiod must be greater than or equal to the cryptoperiod of any application key it protects.
- Revoke application keys immediately when they reach their cryptoperiod end.
- Initialise a new Security World when the existing one reaches its cryptoperiod end., initialise a new Security World under a new Administrator Card Set (ACS), generate new application keys, and destroy the old Security World's ACS.

Manage cryptoperiods using these mechanisms:

- Create Security World application keys with a timeout or usage value.
- In Common Criteria CMTS mode, use max timeout and max usage parameters for application keys.
- Manage infrastructure key cryptoperiods manually by deleting keys at end of cryptoperiod — this may require Security World re-initialisation.

!!! note
    Softcard-protected application keys cannot have a usage or timeout limit set — the `ppmk` utility does not support this option.

## Generating random numbers and keys

Always use the nShield HSM to generate random numbers and keys. The nShield HSM includes a certified random number generator using a hardware-based entropy source, which provides greater security than a software-based random number generator running on a general-purpose computer.

## Key backup

Back up all data in the Key Management Data directory regularly using your standard backup procedures. The sensitive Security World data — including application key blobs stored on the host or Remote File System (RFS) — is encrypted using the Security World key, so an attacker who obtains this data cannot use it without the Security World key and the Administrator Cards.

Keys that have reached the end of their cryptoperiod may still exist in backups. If feasible, delete the backup data for retired keys. If you must retain backups for operational, resilience, or audit reasons, implement procedural controls to mitigate attacks on retired keys.

## Key import

Generate new keys rather than importing existing ones whenever possible. Importing a key does not delete copies of the key material from the host — traces may remain on disk or in backups, and there is a risk the key material was compromised before import. Importing a compromised key does not make it secure. You are responsible for deleting any unprotected key material after import.

## Key separation

### Single-purpose keys

Assign each key a single purpose. The nShield HSM enforces this principle with separate key types for different purposes — for example, ECDHPrivate/ECDHPublic for Elliptic Curve (EC) key-establishment and ECDSAPrivate/ECDSAPublic for EC signing and verification.

One exception: a static EC private key-establishment key may also sign a Certificate Signing Request (CSR) to request an initial certificate for the associated static EC public key. For this use case, use the ECPrivate/ECPublic key type with a specialised ACL permitting one signing operation (OpPermissions:Sign) followed by key-establishment (OpPermissions:Decrypt). Perform this single signing operation before the key is initially persisted or blobbed.

### SSH secure channel keys

Each nShield 5 platform service uses a separate, mutually authenticated SSH secure channel. Use separate key pairs for each service to maintain key separation and limit the impact if keys are compromised.

## nShield JCA/JCE CSP

Follow the security configuration guidance before installing the nShield JCA/JCE CSP. You must configure unlimited strength JCE jurisdiction policy files and the correct preference order.

Follow the security configuration guidance for unlimited strength JCE jurisdiction policy files and the correct preference order for nShield in the Java security configuration file, provided in the nCipherKM JCA JCE CSP API Guide. See the Installing the nCipherKM JCA/JCE CSP section for details.

## nShield PKCS #11 library

Review the PKCS #11 security configuration guidance before deploying. You must ensure all environment variables and SAM settings are correctly configured for your Security World.

### Symmetric encryption

The nShield PKCS #11 library can use the nShield HSM for symmetric encryption with DES, Triple DES, and AES algorithms.

Symmetric encryption operations on the HSM may be slower than on the host computer under light load, but can achieve better overall performance under heavy load by freeing the host CPU for other tasks.

Perform symmetric operations on the nShield HSM. Symmetric encryption on the host increases the threat of key compromise, since host security is weaker than HSM security, and may lack key lifecycle management.

If symmetric encryption must be performed on the host, deploy technical and procedural access controls to protect the host and mitigate the higher risk of key compromise.

### PKCS #11 library with Security Assurance Mechanism

The nShield PKCS #11 library includes a Security Assurance Mechanism (SAM) in `libcknfast`. The SAM detects operations that reveal questionable application behaviour — including applications that use an inadequate concept of key security — helping you identify potential weaknesses and build more secure PKCS #11 applications.

Review the security guidance on using the SAM in the PKCS #11 Reference Guide for nShield Security World v13.9.3. See the PKCS #11 Developer libraries section for details.

### PKCS #11 library environment variables

Review security configuration guidance for PKCS #11 library environment variables in the PKCS #11 Reference Guide for nShield Security World v13.9.3. See the nShield PKCS #11 library environment variables section for details.

## Related topics

- [Configure access control](access-control.md)
- [Audit your HSM deployment](audit.md)
- [Respond to a key compromise](security-incident-and-response.md)
