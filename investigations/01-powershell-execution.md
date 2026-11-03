# Investigation 01 — PowerShell Execution Detection

## Objective

Detect and investigate PowerShell process execution on a Windows endpoint using Sysmon and Wazuh SIEM.

## Lab Environment

| Component | Technology |
|---|---|
| SIEM | Wazuh on Ubuntu Server |
| Virtualisation | VMware Workstation 17 Player |
| Endpoint | Windows with Wazuh Agent |
| Telemetry Collection | Microsoft Sysmon |
| Monitoring Interface | Wazuh Threat Hunting |

## Test Procedure

1. Configured Sysmon to collect process creation events.
2. Integrated the `Microsoft-Windows-Sysmon/Operational` event channel with the Windows Wazuh agent.
3. Verified that the Wazuh agent was connected to the SIEM server.
4. Opened Windows PowerShell and executed the following controlled test command:

   ```powershell
   powershell.exe -NoProfile -Command "Write-Output SOC-LAB-TEST-002"
   ```

5. Opened **Wazuh Dashboard → Threat Hunting → Events**.
6. Located the PowerShell process creation alert.
7. Examined the process information, detection rule, severity and MITRE ATT&CK classification.
8. Captured screenshots of the event details for documentation.

## Detection Results

| Field | Result |
|---|---|
| Endpoint | `SOC-WINDOWS-ENDPOINT` |
| Event Source | Microsoft Sysmon |
| Sysmon Event ID | `1` — Process Creation |
| Wazuh Rule ID | `92027` |
| Rule Description | Powershell process spawned powershell instance |
| Severity | Level 4 |
| MITRE ATT&CK Technique | `T1059.001` — PowerShell |
| MITRE ATT&CK Tactic | Execution |
| Process | `powershell.exe` |
| Test Command | `Write-Output SOC-LAB-TEST-002` |

## Investigation and Analysis

### 1. Process Execution

The Windows endpoint generated a Sysmon Event ID 1 after the controlled PowerShell command was executed.

Sysmon process creation events provide useful information for security investigations, including:

- Executable name and file path
- Process identifier (PID)
- Parent process information
- Command-line arguments
- Execution timestamp

These details help security analysts determine what executed, how it was launched and whether the activity requires further investigation.

### 2. SIEM Detection

The Wazuh agent collected the Sysmon event and forwarded it to the Wazuh SIEM infrastructure.

Wazuh generated an alert associated with **Rule ID 92027**, indicating that a PowerShell process had spawned another PowerShell instance.

The alert was assigned **Severity Level 4**.

This confirmed that endpoint telemetry was successfully collected, processed and made available for investigation.

### 3. MITRE ATT&CK Mapping

The alert was mapped to:

**T1059.001 — Command and Scripting Interpreter: PowerShell**

PowerShell is a legitimate Windows administration tool, but it can also be used by attackers to execute commands, automate malicious activity and interact with system resources.

PowerShell execution alone does not establish malicious behaviour. Analysts must consider the command executed, process ancestry, user context and surrounding events.

### 4. Analyst Assessment

The observed activity was generated intentionally as part of a controlled security test.

The process execution matched the expected test command, and the resulting alert demonstrated successful detection.

There was no evidence in the investigated event establishing malicious activity.

**Disposition:** Expected activity — controlled security test.

## Evidence

### Evidence 1 — Wazuh PowerShell Detection Alert

The screenshot below shows the Wazuh detection rule, severity and MITRE ATT&CK classification.

![PowerShell Detection](../Screenshot/01_powershell_rule.png)

### Evidence 2 — Sysmon Process Creation Details

The screenshot below shows the Sysmon process execution details, including the PowerShell command line and parent process information.

![PowerShell Event Details](../Screenshot/01_powershell_details_a.png)

## Outcome

The investigation successfully demonstrated:

- Collection of Sysmon process creation telemetry.
- Integration of Windows Sysmon with the Wazuh agent.
- Detection of PowerShell process execution.
- Analysis of Wazuh alerts and rule metadata.
- Interpretation of MITRE ATT&CK mappings.
- Documentation of a controlled security investigation.

## Conclusion

This investigation validated the ability of the home SIEM environment to monitor PowerShell process execution on a Windows endpoint.

Wazuh successfully received Sysmon Event ID 1 and generated Rule 92027, demonstrating functional endpoint telemetry collection and security alerting.

The investigation provided practical experience in process execution analysis, SIEM alert triage and security event documentation.

**Final Assessment:** Detection successful. No malicious activity identified during the controlled test.
