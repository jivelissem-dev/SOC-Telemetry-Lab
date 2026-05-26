# 📋 Incident Investigation Report: Persistence via Scheduled Task

## 1. Incident Summary
- **Date/Time:** May 25, 2026
- **Severity:** High
- **Impacted Host:** `WKSTN-FL-042`
- **Trigger Alert:** Windows Security Event ID 4698 (A scheduled task was created) detected an unapproved task registration pointing to a temporary directory.
- **Playbook Reference:** IR-PLAYBOOK-WIN-PERST-09

---

## 2. Technical Analysis & Evidence

### Log Sources Evaluated
| Log Source | Status | Observations |
| :--- | :--- | :--- |
| **Windows Security (Event 4698)** | 🔴 Trigger Alert | Identified a newly registered scheduled task named `Updater_Check`. |
| **Sysmon (Event ID 1)** | 🟡 Suspicious | The task calls a hidden binary located in the local AppData temp path. |
| **Sysmon (Event ID 3)** | 🟢 No Activity | No outbound network connections established by the spawned task yet. |

### Process & Task Telemetry
- **Task Name:** `Updater_Check`
- **Task Actions (Command):** `C:\Users\j.ivelisse\AppData\Local\Temp\update.exe`
- **Arguments:** `/silent /run`
- **User Context:** `CORP\j.ivelisse`
- **Security ID:** `S-1-5-21-3623811015-3361103932-3516408258-1103`

---

## 3. The Analyst Triage & Verdict

- **Threat Behavior:** Adversaries frequently use scheduled tasks to maintain access to a system after an initial compromise. This allows a malicious binary to re-execute periodically or upon system startup. This technique maps directly to **MITRE ATT&CK T1053.005 (Scheduled Task)**.
- **Contextual Analysis:** Cross-referencing the execution path reveals that `update.exe` was dropped into a volatile user directory (`\AppData\Local\Temp`), which violates the enterprise software installation policy. No valid deployment tool (e.g., SCCM, native patch manager) initiated this modification.
- **Final Verdict:** **True Positive (Simulated Compromise).** The signature, execution path, and naming convention mimic a masquerading persistence attack. Immediate remediation containment is required.

---

## 4. Disposition & Next Steps

- **Action Taken:** 1. Isolated host `WKSTN-FL-042` via EDR to prevent potential lateral movement.
  2. Programmatically deleted the unauthorized scheduled task `Updater_Check` using PowerShell.
  3. Collected the file sample `update.exe` for static hash analysis and submission to security teams.
- **Tuning Recommendation:** Establish a rigid correlation rule in the SIEM to alert automatically whenever a newly registered scheduled task executes a binary out of user-writable directories (`\Temp\`, `\Downloads\`, or `\Public\`).
