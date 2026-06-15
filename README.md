# Azure AD Attack Detection Lab

**SOC Lab 03 — Identity Attack Surface**

Built an Azure-based Active Directory attack detection lab. Deployed a Windows Server 2022 domain controller and a domain-joined workstation, simulated four core credential attacks, collected Windows Security Events into Microsoft Sentinel via Azure Monitor Agent, and wrote KQL detections for each attack.

**Workflow:** Detect → Analyze → Correlate → Harden → Validate

---

## Lab Architecture

| Resource | Details |
|---|---|
| dc01 | Windows Server 2022, Domain Controller, lab.local |
| ws01 | Windows Server 2022, domain-joined attack box |
| Sentinel workspace | law-sentinel-lab-001 |
| Detection table | SecurityEvent (raw AMA, no Sysmon) |
| Platform | Azure — East US |

---

## Attacks Covered

| Attack | MITRE | Event ID | Detection Confidence |
|---|---|---|---|
| Kerberoasting | T1558.003 | 4769 | High |
| AS-REP Roasting | T1558.004 | 4768 | High |
| DCSync | T1003.006 | 4662 | High |
| Pass-the-Hash | T1550.002 | 4624 | Best-effort |

---

## Tools Used

- **Rubeus** — Kerberoasting and AS-REP Roasting simulation
- **Mimikatz** — DCSync and Pass-the-Hash simulation
- **Microsoft Sentinel** — log ingestion and KQL detection
- **Azure Monitor Agent** — Windows Security Event collection
- **Azure CLI** — VM deployment and management

---

## Detection Queries

All KQL queries are in the `/kql` folder:

- `01-kerberoast-detection.kql` — flags 4769 RC4 ticket requests for service accounts
- `02-asrep-roast-detection.kql` — flags 4768 requests with PreAuthType 0
- `03-dcsync-detection.kql` — flags 4662 directory replication from non-DC accounts
- `04-pth-detection.kql` — flags 4624 NTLM type-9 logons (best-effort, hunting query)
- `05-triage-did-it-succeed.kql` — surfaces successful attack outcomes across all techniques
- `06-kill-chain-timeline.kql` — stitches all four techniques into one chronological timeline

---

## Data Collection Rule

Events collected via filtered XPath (cost-controlled — not "All Security Events"):
---

## Key Findings

- Kerberoasting: 19 events captured — svc-sql service account targeted
- AS-REP Roasting: 7 events — svc-legacy with pre-auth disabled
- DCSync: 10 events — krbtgt hash replicated via Mimikatz
- Pass-the-Hash: NTLM hash reused to reach dc01 admin share

---

## Hardening Applied

| Technique | Control Applied |
|---|---|
| Kerberoasting | SPN removed from svc-sql |
| AS-REP Roasting | Pre-authentication re-enabled on svc-legacy |
| DCSync | Replication rights scoped to DCs only |
| Pass-the-Hash | Credential Guard path documented |

---

## Screenshots

All annotated screenshots are in the `/screenshots` folder.

---

## Lab Series

| Lab | Topic | Surface |
|---|---|---|
| Lab 01 | SSH Brute Force Detection | Log / SIEM |
| Lab 02 | Wireshark SSH Packet Analysis | Network |
| **Lab 03** | **AD Attack Detection (this lab)** | **Identity** |
| Lab 04 | Malware Traffic Analysis | Network Forensics |

---

## Skills Demonstrated

Microsoft Sentinel · KQL · Azure VM deployment · Active Directory · Windows Security auditing · MITRE ATT&CK · Detection engineering · Incident triage · AD hardening

---

*Built as part of an active SOC Analyst portfolio. Career transition from ER Tech yo Cybersecurity.*
