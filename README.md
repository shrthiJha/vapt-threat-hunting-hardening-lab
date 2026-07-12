# VAPT Threat Hunting & Hardening Lab (CompTIA Security+ Focus)

A self-built, fully virtualized enterprise lab used to run a complete Vulnerability
Assessment and Penetration Test (VAPT), then remediate every finding and
re-scan to prove closure.

Scope note: Every host in this lab is isolated on a local hypervisor
> network with no internet-facing exposure. All testing was performed against
> systems I own and control, under a self-authored Rules of Engagement
> (see `docs/01-rules-of-engagement.md`).

## Why this project

Most beginner VAPT labs stop at "I found vulnerabilities." This one goes
further:

1. Assess -enumerate and exploit a deliberately weak Active Directory
   environment.
2. Map - tie every finding to a specific CompTIA Security+ domain
   objective.
3. Remediate - actually fix it: least-privilege AD redesign, firewall
   segmentation, TLS hardening, centralized logging.
4. Prove it - re-scan and show a before/after reduction in
   findings.

## Architecture

```
                         ┌────────────────────────────┐
                         │   pfSense CE (firewall/     │
                         │   router, VLAN segmentation)│
                         └─────────────┬────────────────┘
              ┌────────────────────────┼─────────────────────────┐
              │                                                    │
   ┌─────────────────────┐                          ┌─────────────────────────┐
   │  Attacker VLAN       │                          │   Target VLAN            │
   │  - Kali Linux         │  filtered traffic only  │   - Windows Server (DC)  │
   │                        │◄────────────────────────►│   - Windows 11 client    │
   └─────────────────────┘                          │   - Metasploitable2      │
                                                        │   - Wazuh (SIEM/logging) │
                                                        └─────────────────────────┘


All host, hardware, and hypervisor specs are documented in
`docs/02-network-architecture.png`.

## Tech stack 

| Category | Tool |
|---|---|
| Hypervisor | VirtualBox / VMware Workstation Player |
| Firewall & segmentation | pfSense CE |
| Attacker OS | Kali Linux |
| Vulnerable Linux target | Metasploitable2 |
| Domain Controller | Windows Server 2022 Evaluation |
| Windows client | Windows 11 Evaluation |
| Recon / scanning | Nmap |
| Vulnerability scanning | Nessus Essentials / Greenbone OpenVAS CE |
| Exploitation | Metasploit Framework |
| Packet analysis | Wireshark |
| AD attack path mapping | BloodHound + SharpHound |
| Lateral movement / credential abuse | Impacket, CrackMapExec / NetExec |
| Password auditing | Hydra, Hashcat, John the Ripper |
| TLS/cipher auditing | testssl.sh |
| Centralized logging / SIEM | Wazuh |
| Windows telemetry | Sysmon |
| Hardening baselines | Microsoft Security Compliance Toolkit, CIS-CAT Lite |


## Project phases

| Phase | Description | Output |
|---|---|---|
| 0. Scoping | Rules of Engagement, objectives, authorization | `docs/01-rules-of-engagement.md` |
| 1. Lab build | Stand up pfSense, DC, client, Linux target, Wazuh | `docs/02-network-architecture.png` |
| 2. Weak baseline | Intentionally misconfigure AD, TLS, firewall, logging | — |
| 3. Recon & enumeration | Nmap, SMB/LDAP enum, BloodHound collection | `scans/before/` |
| 4. Vulnerability assessment | Nessus/OpenVAS scan, testssl.sh audit | `scans/before/` |
| 5. Exploitation | Metasploit, Kerberoasting, CrackMapExec, Impacket | `exploitation/` |
| 6. Security+ mapping | Every finding mapped to a SY0-701 objective | `docs/03-security-plus-domain-mapping.md` |
| 7. Remediation | Least privilege, pfSense rules, TLS 1.2+, Wazuh + Sysmon | `remediation/` |
| 8. Re-scan & validate | Re-run all scans, before/after comparison | `scans/after/` |
| 9. Reporting | Executive summary + technical report | `docs/04-executive-summary.pdf` |

---

## Sample results

| Metric | Before | After |
|---|---|---|
| Critical/High findings | *(fill in)* | *(fill in)* |
| TLS grade (testssl.sh / SSL Labs style) | *(e.g. F — TLS 1.0 enabled)* | *(e.g. A — TLS 1.2+ only)* |
| Domain Admin group membership | *(over-permissioned)* | *(tiered, least privilege)* |
| Centralized logging coverage | 0 hosts forwarding logs | All hosts → Wazuh |

*(Replace with your actual scan output once the lab is complete — this table is the core evidence for your resume/report.)*


## CompTIA Security+ (SY0-701) domain mapping

| Finding | CWE/CVE | Security+ Objective |
|---|---|---|
| TLS 1.0/1.1 enabled on IIS | CWE-327 | 1.4 – Cryptographic solutions |
| Over-permissioned Domain Admins group | CWE-269 | 1.2 / 4.6 – Access controls, identity management |
| No centralized logging | — | 4.9 – Security monitoring & incident response |
| Kerberoastable service accounts | CWE-521 | 2.4 – Indicators of malicious activity |
| Unrestricted inter-VLAN traffic | CWE-284 | 3.2 / 4.5 – Network segmentation & security operations |

*(Full table with CVE numbers and remediation evidence lives in `docs/03-security-plus-domain-mapping.md`.)*


## Disclaimer

This lab is fully isolated and self-contained. No techniques described here
were used against systems the author does not own or control. All tools
listed are publicly available and used for defensive/educational purposes.


## Author

Shruthi Jha— Software Engineer transitioning into a dedicated cybersecurity
role. Connect on [LinkedIn] (https://www.linkedin.com/in/shruthi-jha-785025205/)


