# Week 3: Unauthorized USB Drive in Radiology
**Course:** CPSC 4584 | Special Topics in Information Security
**Date:** September 14, 2026
**Analyst:** David Oreoluwa
**Incident ID:** INC-2026-0914-001

---

## Incident Summary

An unmarked USB drive was found plugged into workstation MHS-RAD-WS-03 in the Radiology imaging suite. The workstation provides access to the PACS imaging system and the Maplewood EHR, and the USB was not listed in the authorized device registry.

---

## Chain of Custody

Chain of custody is important because it documents who handled the evidence and what actions were taken. The USB was removed without opening files, logged and tagged by IT, and transferred to the SOC without being connected to another system.

---

## Key Encoding Finding

**String Found:** Y3VybCAtcyAtbyAvZGV2L251bGw=
**Encoding Type:** Base64
**Decoded Content:** curl -s -o /dev/null
**Significance:** The string represents a curl command. However, the fragment alone does not identify a destination, prove that the command was executed, or establish malicious activity.

---

## Terminal Commands Used

| Command | Purpose |
|---------|---------|
| `echo "unauthorized access" \| base64` | Demonstrates how ordinary text can be represented using Base64 encoding. |
| `echo "Y3VybCAtcyAtbyAvZGV2L251bGw=" \| base64 -d` | Decodes a Base64 string to reveal its original text. |
| `xxd .bashrc \| head -6` | Displays the beginning of a file as hexadecimal and readable text. |
| `strings .bashrc \| grep -i "path\|export\|alias"` | Extracts readable strings and filters them for specific patterns. |

---

## Escalation Recommendation

I would escalate the incident to Tier 2 because an unauthorized USB device was found connected to a restricted Radiology workstation with access to PACS and the EHR. The contents of the device are unknown, and the limited overnight monitoring means the absence of network alerts does not rule out activity. Further investigation should determine who connected the device, what it contains, and whether the workstation or clinical data was accessed.

---


*CPSC 4584 | Governors State University | Fall 2026*
    