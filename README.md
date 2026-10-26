# The File Changed. Wazuh Noticed.

**Windows File Integrity Monitoring (FIM) | Wazuh SIEM | SOC Home Lab**

![Wazuh](https://img.shields.io/badge/Wazuh-SIEM-005571)
![Platform](https://img.shields.io/badge/Platform-Windows%2011-0078D4)
![Server](https://img.shields.io/badge/Server-Ubuntu-E95420)
![Focus](https://img.shields.io/badge/Focus-Blue%20Team-blue)

> Trust is good. File integrity monitoring is better. Hashes don't care about excuses.

## Overview

This project demonstrates a hands-on File Integrity Monitoring (FIM) investigation using Wazuh. I configured a Windows endpoint to monitor a dedicated test directory, performed controlled file-system changes, and validated the resulting security events in the Wazuh dashboard.

The objective was to determine whether the monitoring setup could detect file modification, creation, and deletion.

**Outcome: All three test scenarios were observed in the Wazuh dashboard.**

## Lab Environment

| Component | Details |
|---|---|
| SIEM | Wazuh |
| Manager operating system | Ubuntu Server |
| Endpoint operating system | Windows 11 |
| Agent name | `NkosiNhleko` |
| Agent ID | `006` |
| Monitored directory | `C:\FIM-Lab` |
| Monitoring | Real-time FIM |
| Main test file | `test-file.txt` |
| Creation/deletion test file | `created-file.txt` |

## Objectives

- Configure Wazuh FIM monitoring for a dedicated Windows directory.
- Validate file modification, creation, and deletion detection.
- Inspect alert metadata and integrity hashes.
- Understand the investigative value and limitations of file-integrity alerts.
- Document troubleshooting and propose appropriate SOC response steps.

## Configuration

I updated the existing Wazuh agent configuration at:

`C:\Program Files (x86)\ossec-agent\ossec.conf`

The monitored directory entry was configured as follows:

```xml
<directories check_all="yes" realtime="yes">C:\FIM-Lab</directories>
```

I preserved the existing `syscheck` configuration, created the test directory, saved the changes, and restarted the Wazuh agent service.

All experiments were confined to the dedicated lab directory.

## Detection Tests

### Test 1 — File Modification

**Action:** Modified `C:\FIM-Lab\test-file.txt` and repeated the modification to validate the behavior.

**Result:** Wazuh generated an integrity-change alert.

Observed alert details:

- Rule ID: `550`
- Rule level: `7`
- Decoder: `syscheck_integrity_changed`
- Event: `modified`
- Monitoring mode: `realtime`
- File path: `c:\fim-lab\test-file.txt`
- Changed attributes: size, modification time, MD5, SHA-1, and SHA-256

During the first recorded modification, the file size changed from 18 bytes to 44 bytes. The alert also included before-and-after hash values.

**Analysis:** Integrity changes can indicate unexpected file modification. Hash differences provide evidence of a change but do not independently establish malicious intent.

### Test 2 — File Creation

**Action:** Created `created-file.txt` inside the monitored directory.

**Result:** Wazuh displayed a file-added event.

**Analysis:** File-creation events may be relevant when investigating unexpected scripts, tools, or files. The analyst must establish context before deciding whether the activity is suspicious.

### Test 3 — File Deletion

**Action:** Deleted the test file `created-file.txt`.

**Result:** Wazuh displayed a deletion event for the test file.

**Analysis:** Unexpected deletion can be important when investigating changes to configuration files, scripts, or other sensitive data. Additional telemetry may be required to establish which user or process performed the action.

## Results Summary

| Test | Expected detection | Result |
|---|---|---|
| File modification | Integrity-change alert | Confirmed |
| File creation | File-added event | Confirmed |
| File deletion | File-deletion event | Confirmed |

## Technical Findings

The modification alert included MITRE ATT&CK technique reference **T1565.001 — Stored Data Manipulation**.

This is relevant to the integrity impact of unauthorized data modification. However, the technique reference does not mean the controlled lab activity itself was malicious.

The observed modification event also included changes to file size, modification time, and cryptographic hash values. This provided useful technical evidence for validating the detection.

## Troubleshooting and Lessons Learned

The most challenging part of the exercise was ensuring that the configuration, agent, and dashboard worked together correctly.

During the broader home-lab setup, I encountered configuration and VirtualBox networking challenges. For the FIM portion, I had to identify the correct agent configuration file, update the existing directory entry without replacing the entire configuration, restart the agent, and verify the resulting events in the dashboard.

The key lesson was that executing a command successfully is not enough. A detection should be validated using the security telemetry it produces.

This reinforced the importance of methodical troubleshooting, checking event details, and distinguishing between an expected test result and a confirmed security finding.

## SOC Analyst Response Approach

If similar alerts appeared unexpectedly in a production environment, I would:

1. Validate the endpoint, file path, timestamp, event type, and alert details.
2. Determine whether the change was authorized or expected.
3. Investigate relevant user and process activity using available endpoint telemetry.
4. Correlate the event with other suspicious activity where appropriate.
5. Preserve relevant evidence and document the investigation.
6. Follow the organization's escalation and containment procedures if the activity appeared malicious.
7. Recommend restoration or other remediation based on the findings.

**Important:** A FIM alert is an investigative starting point, not proof of an attack.

## Limitations and Future Improvements

This lab validates basic FIM behavior in a controlled environment. It does not demonstrate automated containment, full process attribution, or comprehensive endpoint protection.

Potential improvements include:

- Testing additional file types and subdirectories.
- Correlating FIM events with Windows process and account telemetry.
- Creating a structured alert-triage checklist.
- Exploring custom Wazuh rules and thresholds.
- Documenting evidence preservation and recovery procedures.

## Evidence

Screenshots will be stored in the `screenshots/` directory.

Suggested evidence:

1. Wazuh dashboard and connected agent.
2. Windows FIM test directory.
3. File-modification alert.
4. File-creation alert.
5. File-deletion alert.
6. Wazuh agent service status.
7. Expanded alert details.
8. Test results summary.

Only genuine screenshots captured from this lab should be added.

## Final Reflection

This lab helped me move beyond simply configuring a security tool to validating its detections and interpreting the resulting telemetry.

I tested three file-system behaviors, inspected the modification alert's integrity information, and documented the investigative steps a SOC analyst could take when reviewing similar events.

The file changed. The alert appeared. The next step is learning how to distinguish normal activity from activity that deserves an investigation.

---

**Disclaimer:** This is an educational home lab using controlled test files. It is not a production deployment, formal security audit, or guarantee of complete endpoint protection.
