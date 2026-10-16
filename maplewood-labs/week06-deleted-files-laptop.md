# Week 6: Deleted Files on a Terminated Employee's Laptop

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** October 5, 2026  
**Analyst:** Oreoluwa David Dada
**Case ID:** FOR-2026-1005-001

---

## Incident Summary

Maplewood Health System failed to collect a billing coordinator's laptop when termination took effect on October 3, 2026. The laptop remained outside organizational custody for approximately 43 hours, and five files were subsequently recovered from unallocated disk space during forensic analysis.

---

## Chain of Custody Status

**Gap Period:** October 3, 2:47 PM to October 5, 9:30 AM (approximately 43 hours)

**Gap Significance:** The custody gap limits the ability to verify who had access to the laptop and what activity occurred before its return. It does not automatically invalidate the evidence, but it must be documented as a limitation. Deletion events were logged during the gap, and the available evidence does not independently establish who performed the deletions or why.

**Documented By:** Forensic analyst on receipt of the device.

---

## Recovered File Evidence Classification

| Filename | Type | Evidentiary Value | Finding |
|---|---|---|---|
| `insurance_export_final.xlsx` | Spreadsheet | High | Modified October 3 at 11:47 PM and deleted at 11:52 PM. Prioritize review to determine whether it contains sensitive insurance or billing data and whether access was authorized. |
| `mhs_billing_schema_db.sql` | SQL script | High | Modified October 1 and deleted October 3 at 11:53 PM. Review its relationship to the billing system and determine whether its use and handling were authorized. |
| `vendor_contact_list_external.docx` | Document | Medium | Modified October 2 at 3:22 PM and deleted October 3 at 11:55 PM. Examine its contents and determine whether the external contact information was used appropriately. |
| `__tmp_8f2a.dat` | Partial artifact | Undetermined | Only 22 KB of an estimated 400 KB was recovered. Its timestamps are unreadable, so its contents, history, and relevance cannot be reliably established from the available information. |
| `personal_vacation_2025.jpg` | Image | Low, based on available metadata | The filename suggests personal content, and the last modification was July 14, 2025. The filename alone does not establish the actual contents or exclude relevance to the investigation. |

---

## Metadata Inspection Commands Used

| Command | Purpose |
|---|---|
| `stat .bashrc` | Inspect file metadata, including permissions, ownership, inode, size, and timestamps. |
| `ls -lai` | List files, including hidden files, with inode information. |
| `xxd .bashrc \| head -3` | Inspect the beginning of a file in hexadecimal and ASCII form for clues about its format. |
| `find . -newer .bashrc -type f` | Identify regular files with modification times later than `.bashrc`. |


## Escalation Summary

The laptop remained outside Maplewood's custody for approximately 43 hours after termination, and deletion events were logged during that interval. Five artifacts were recovered from unallocated disk space using Foremost. The forensic image was created using FTK Imager, and its SHA-256 hash was verified against the source drive before analysis. The analyst performed analysis on the image, and the recovered artifacts were hashed and logged before content examination.

The insurance spreadsheet and billing schema should receive priority review, while the vendor contact list and the partially recovered temporary file warrant examination based on their contents and limitations. The personal image appears less directly related based on its filename alone. File recovery, filenames, and timestamps support the technical timeline but do not independently establish intent, misconduct, or the identity of the person who performed the deletions. Preserve the forensic records and escalate the findings to the supervisor and authorized legal or HR reviewers for corroboration and decisions.

---

*CPSC 4584 | Governors State University | Fall 2026*