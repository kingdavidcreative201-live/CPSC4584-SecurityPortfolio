# Week 7: Network Traffic Anomaly During Night Shift

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** October 12, 2026  
**Analyst:** Oreoluwa David Dada
**Case ID:** NET-2026-1012-001 (related: FOR-2026-1005-001)

---

## Incident Summary

Maplewood Health System's network flow review identified an outbound transfer from Webb's laptop to an unauthorized external destination shortly before a spreadsheet was deleted. Repeated small connections to the same destination and unusual activity on radiology and billing workstations warrant further investigation, but the available flow records do not independently prove data exfiltration, malware, or command-and-control activity.

---

## Network Flow Analysis

| Flow | Source | Destination | Classification | Finding |
|---|---|---|---|---|
| 1 | Webb laptop, 10.4.2.47 | 185.220.101.47 | Suspicious outbound transfer | 2,457,814 bytes were transferred over HTTPS beginning at 11:48 PM on October 3. The destination was unauthorized. The flow does not identify the transferred content. |
| 2 | Webb laptop, 10.4.2.47 | 185.220.101.47 | Possible beaconing | Repeated 312-byte connections occurred approximately every four minutes from 11:15 PM on October 3 until 6:42 AM on October 4. The pattern supports further investigation but does not prove command-and-control activity. |
| 3 | EHR production server, 10.4.1.10 | 52.94.228.167 (AWS) | Authorized backup | The 14.2 MB HTTPS transfer between 2:00 and 2:18 AM was identified as the scheduled nightly backup to the EHR vendor. |
| 4 | Radiology workstation, 10.4.3.22 | 8.8.8.8 | Unusual DNS activity | Twenty-three DNS queries totaling 847 bytes were recorded between 2:14 and 2:22 AM. The unusual query volume warrants review. |
| 5 | Billing workstation, 10.4.3.88 | 203.0.113.99 | Suspicious repeated HTTP connections | Nine HTTP connections of 4,200 bytes each occurred at four-minute intervals between 3:47 and 4:19 AM. The unapproved destination and regular timing warrant investigation but do not prove command-and-control activity. |

---

## Disk and Network Evidence Correlation

**Disk (Week 6):** The forensic review identified `insurance_export_final.xlsx` as modified at 11:47 PM and deleted at 11:52 PM on October 3.

**Network (Week 7):** Flow 1 recorded 2,457,814 bytes sent from Webb's laptop to 185.220.101.47 beginning at 11:48 PM and continuing until 11:50 PM.

**Correlation:** The outbound transfer occurred shortly after the spreadsheet was modified and before its deletion. The approximate size relationship and close timing make this a significant investigative lead.

**Limitation:** The flow record does not identify the application payload or prove that the spreadsheet was transmitted. Additional endpoint, proxy, DNS, authentication, or packet-level evidence is required.

---

## Network Artifact Inspection Commands Used

| Command | Purpose |
|---|---|
| `curl -I https://example.com 2>/dev/null \| head -15` | Retrieve HTTP response headers. |
| `curl -v --max-time 5 https://example.com 2>&1 \| grep -E "^\*|^<|^>" \| head -20` | Inspect connection, TLS, and HTTP exchange details. |
| `curl -s https://example.com -o /tmp/response.html && xxd /tmp/response.html \| head -4` | Save the response body and inspect its first bytes. |
| `curl -s -o /dev/null -w "HTTP: %{http_code} \| Size: %{size_download} bytes \| Time: %{time_total}s \| IP: %{remote_ip}\n" https://example.com` | Record response status, size, time, and remote IP. |

---

## Escalation Summary

The network review identified a suspicious 2,457,814-byte transfer from Webb's laptop to 185.220.101.47 beginning at 11:48 PM on October 3. This occurred between the 11:47 PM modification and 11:52 PM deletion of `insurance_export_final.xlsx`. The same laptop showed repeated 312-byte connections to that destination approximately every four minutes from 11:15 PM until 6:42 AM. The timing and regularity are consistent with possible beaconing, but the flow records do not prove that malware or command-and-control activity occurred.

Other observations include unusual DNS activity from the radiology workstation and repeated HTTP connections from the billing workstation to an unapproved destination. The scheduled EHR backup to the AWS-hosted vendor was identified as authorized traffic and should be treated separately from the suspicious flows. The external destination shares a /24 subnet with addresses documented in the Week 5 credential-stuffing incident, but this relationship does not establish a common actor.

Preserve the network flow records and relevant endpoint evidence. Escalate the findings and uncertainties to the supervisor for correlation with DNS, authentication, proxy, endpoint, and packet-capture evidence. Any containment, firewall changes, or attribution decisions must be made through Maplewood's authorized incident-response process.

---

*CPSC 4584 | Governors State University | Fall 2026*