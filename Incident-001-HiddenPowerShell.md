# 📋 Incident Investigation Report: Stealth Process Execution

### **1. Incident Summary**
* **Date/Time:** May 24, 2026 
* **Severity:** Medium
* **Impacted Host:** Local Windows Endpoint
* **Trigger Alert:** Sysmon Event ID 1 (Process Creation) detected a PowerShell instance initiated with a hidden window flag (`-WindowStyle Hidden`).

---

## 2. Technical Analysis & Evidence

Telemetry gathered from the Sysmon operational log (Event ID 1):

- **Impacted Host:** WKSTN-FL-042
- **User Context:** CORP\j.ivelisse
- **Parent Process:** explorer.exe (PID: 4312)
- **Spawned Process:** powershell.exe (PID: 8704)
- **Full Command Line:** `powershell.exe -WindowStyle Hidden -Command "Write-Output 'SOC-Lab-Test-In-Progress'; Start-Sleep -Seconds 2"`
- **File Hash (SHA256):** `75A4054BE2C12BC9F335CE2BD40733AD0F1E2A4C2E9A6FBB858287F4045FE0A9`

---

### **3. The Analyst Triage & Verdict**
* **Threat Behavior:** The use of the `-WindowStyle Hidden` parameter indicates an intentional attempt to evade user detection by suppressing the Graphical User Interface (GUI). This technique maps directly to **MITRE ATT&CK T1059.001 (PowerShell)**.
* **Contextual Analysis:** The payload executed was a benign verification string. No secondary network connections or malicious payloads were spawned.
* **Final Verdict:** **Benign Positive (False Alarm / Authorized Test).** While the technique mimics malicious evasion behavior, the code payload itself was a non-destructive verification test. No escalation is required.

* 
## 4. Disposition & Next Steps
* **Action Taken:** Alert marked as Benign Positive. No isolation or credential resets required.
* **Tuning Recommendation:** (Optional) If this specific test is part of a recurring authorized vulnerability scan or administrative script, recommend updating the SIEM/EDR detection logic to whitelist the specific parent script or user context to reduce alert fatigue.
