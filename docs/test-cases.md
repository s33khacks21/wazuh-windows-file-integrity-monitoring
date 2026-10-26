# Wazuh FIM Detection Test Cases

## Purpose

This register documents the controlled tests performed to validate File Integrity Monitoring in the Wazuh home lab.

## Test Results

| Test ID | Scenario | Test action | Expected result | Observed result | Status |
|---|---|---|---|---|---|
| FIM-01 | File modification | Modified `test-file.txt` | Integrity-change alert | Alert observed; Rule 550, Level 7 | PASS |
| FIM-02 | File creation | Created `created-file.txt` | File-added event | Event observed in dashboard | PASS |
| FIM-03 | File deletion | Deleted `created-file.txt` | File-deletion event | Event observed in dashboard | PASS |

## Validation Notes

- All tests were performed using a dedicated directory: `C:\FIM-Lab`.
- The file changes were intentional and controlled.
- The modification alert included file attributes and before-and-after integrity hashes.
- The creation and deletion events were observed in the Wazuh dashboard.
- These results validate the three tested scenarios, not every possible file-system event.

## Conclusion

The configured Wazuh FIM setup successfully detected the three tested file-system operations. Further testing would be required to assess broader coverage, alert reliability, and correlation with process or user activity.

**Test classification:** Controlled home-lab validation  
**Incident classification:** No real security incident established
