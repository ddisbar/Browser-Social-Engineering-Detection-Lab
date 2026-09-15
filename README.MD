# ClickFix Social Engineering Emulation & Detection Lab

## Project Overview
This repository contains the blueprints and configurations for an enterprise-level Windows Threat Hunting lab built to detect **ClickFix** social engineering attacks. ClickFix targets the human element by tricking users into manually running system terminal shortcuts to bypass modern web browser sandboxes. 

The objective of this lab is to establish advanced process auditing baselines on Windows endpoints and utilize native PowerShell queries to hunt for anomalous process creation behaviors.

## Lab Architecture & Technical Stack
* **Hypervisor:** Oracle VirtualBox
* **Operating System:** Windows 10/11 Enterprise Evaluation (Isolated Sandbox)
* **SIEM / Logging Infrastructure:** Local Windows Advanced Security Auditing Policy (Event ID 4688)
* **Threat Hunting Engine:** PowerShell Administration Console / Sigma Framework

## Implementation Steps

### Phase 1: Security Hardening & Advanced Auditing Setup
1. Configured local group policy objects via `gpedit.msc`.
2. Path: `Computer Configuration -> Windows Settings -> Security Settings -> Advanced Audit Policy -> Detailed Tracking`.
3. Enabled **Audit Process Creation** for both `Success` and `Failure`.
4. Path: `Computer Configuration -> Administrative Templates -> System -> Audit Process Creation`.
5. Flipped **Include command line in process creation events** to `Enabled` to register deep CLI arguments.

### Phase 2: Safe Threat Emulation Execution
Simulated the physical click-and-paste payload delivery pipeline of a standard ClickFix artifact by injecting a defanged target execution command via the Windows Run dialog (`Win + R`):
```text
powershell -NoExit "Write-Host ClickFix"
```

### Phase 3: Forensic Analysis & Threat Hunting
Deployed a native administrative pipeline search query to sweep the security event log for evidence of execution:
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} -MaxEvents 50 | Where-Object {\$_.Message -like "*ClickFix*"} | Format-List TimeCreated, Message
```

### Lab Artifacts & Verification
Below is the verification showing the system successfully capturing the execution footprint:

![Threat Hunting Verification Output](screenshots/your_screenshot_name.png)
*(Note: Replace this path with your actual screenshot image uploaded to your repository)*

## Strategic Analyst Takeaway
While malicious actors are pivoting heavily towards utilizing human interaction to bypass standard edge detection systems, the operating system's internal process engine cannot lie. Isolating specific process parent-child lineages (such as desktop native environments natively spawning scripting shells) remains a highly accurate heuristic approach to mitigating modern access vectors.
