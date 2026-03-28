---
description: "Use this security manual to deploy and operate your nShield HSM securely. Covers commissioning, access control, key management, physical security, audit, and"
---

# nShield Security World v13.9.3 Security Manual

Use this manual to deploy and operate your nShield Hardware Security Module (HSM) securely. It describes the procedural controls you need alongside the technical product documentation. Follow the guidance here when developing your Security Operating Procedures.

This manual takes precedence over the HSM and Security World product documentation where there is any contradiction. Its scope covers security procedural guidance only — the product documentation covers implementation details.

## Start here: find your section by role

Go directly to the section most relevant to your work.

| Your role | Start with | Why |
|---|---|---|
| **Security policy owner** | [Security manual authority and scope](#security-manual-authority-and-scope) | Understand the scope and compliance posture of this manual |
| **Commissioning engineer** | [Supply and Transportation](supply-and-transportation.md) | Begin with delivery inspection before installation |
| **HSM administrator** | [Commissioning](commissioning.md) | Start the installation and configuration process |
| **Auditor** | [Audit](audit.md) | Find audit evidence requirements, log review guidance, and control verification steps |

### Who should use this manual

Use this manual if you are responsible for any of the following:

- Security policy and procedures for systems that incorporate nShield HSMs
- Commissioning nShield HSMs
- Administering nShield HSMs
- Auditing nShield HSM deployments

### Products covered

This manual covers the following nShield product variants:

- nShield Edge
- nShield Solo+
- nShield Solo XC
- nShield 5s
- nShield Connect+
- nShield Connect XC
- nShield 5c

!!! note
    Guidance for nShield Solo applies to the Solo+, Solo XC, and nShield 5s variants. Guidance for nShield Connect applies to the Connect+, Connect XC, and nShield 5c variants. In this manual, the terms **module** and **HSM** both refer to the nShield product range.

### Product security objective

nShield HSMs protect against technical and physical attacks on cryptographic keys used to protect your data in use, in transit, and at rest. When you follow the technical and procedural controls in this manual, nShield HSMs provide confidentiality, integrity, and availability up to Federal Information Processing Standard (FIPS) 140 Level 3 and Common Criteria Evaluation Assurance Level (EAL) 4+ (platform and version dependent).

*Some availability threats can be mitigated by hosting a Security World across multiple HSMs.*

### Product selection

Verify that the functionality of any candidate nShield product meets your requirements before selecting it.

### Security manual authority and scope

This manual is advisory. It identifies good procedural practices for secure operation of nShield products in your environment. Use it alongside the HSM and Security World product documentation, which covers implementation of the controls described here.

## Related documents

Use these guides alongside this security manual.

- [Install and set up your nShield hardware](https://nshielddocs.entrust.com/security-world-docs/hardware-install/intro.html)
- [Use and configure your nShield HSM](https://nshielddocs.entrust.com/security-world-docs/hsm-user-guide/intro.html)
- [Install the Security World software](https://nshielddocs.entrust.com/security-world-docs/software-install/intro.html)
- [Manage your Security World](https://nshielddocs.entrust.com/security-world-docs/secworld-admin/intro.html)
- [Use nShield command-line utilities](https://nshielddocs.entrust.com/security-world-docs/utilities/summary.html)
- [Manage keys in your Security World](https://nshielddocs.entrust.com/security-world-docs/key-mgmt/intro.html)
- [Develop CodeSafe 5 applications](https://nshielddocs.entrust.com/security-world-docs/codesafe5/intro.html)
- [Develop CodeSafe applications](https://nshielddocs.entrust.com/security-world-docs/codesafe/intro.html)
- [Complete the nShield Connect physical security checklist](https://nshielddocs.entrust.com/security-world-docs/hardware-install/intro.html)

## Reference documents

Use these references for cryptographic standards, NTP security, and SNMP guidance.

- [Ecrypt-CSA cryptographic recommendations](https://www.keylength.com/en/3/)
- [NIST SP 800-57 Part 1 Revision 5](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final)
- [NIST SP800-131A Revision 1](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-131Ar1.pdf)
- [Net-SNMP documentation](http://www.net-snmp.org/)
- [Network Time Protocol (NTP) security vulnerabilities](http://support.ntp.org/bin/view/Main/SecurityNotice)
