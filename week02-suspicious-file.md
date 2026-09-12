# Week 2: Suspicious File on a Nurse's Workstation
**Course:** CPSC 4584 | Special Topics in Information Security
**Date:** September 7, 2026
**Analyst:** David 
**Incident ID:** INC-2026-0907-001

---

## Incident Summary

On September 6 at 3:14 a.m., a file named patient_notes.txt appeared in the home directory (/home/nurse01) of workstation MHS-C3-NRS-07, a Clinic 3 nurse workstation with access to the clinical network and EHR system. The modification occurred outside scheduled clinical hours, the assigned nurse denies creating the file, and IT confirmed no approved process creates files in home directories overnight.

---

## Key Findings

**Permission Finding:** The permission string was -rwxr--r--, meaning the owner had read, write, and execute permissions while group and others had read only. The owner execute bit on a file named patient_notes.txt was suspicious because text documents do not need to execute, suggesting the file might be masquerading as something benign.

**File Type Finding:** The file command revealed that patient_notes.txt was not a text file at all — it was an ELF 64-bit LSB executable, x86-64, dynamically linked. The .txt extension was deliberately misleading, and this mismatch between the filename and the actual file type was the central finding of the investigation.

**Timestamp Finding:** Stat showed the file was 8192 bytes with a Modify time and Change time of 2026-09-06 03:14:33, matching the SOC alert time. The Access time was later, at 2026-09-06 08:22:14. Because Modify and Change were identical, there was no evidence of metadata tampering after the content was written, but the 3:14 a.m. timestamp fell within a window when no clinical activity was scheduled.

**Strings Finding:** Strings revealed readable content inside the binary, including http://185.220.101.47/update, a hidden path at /tmp/.x11-unix/.lock, and the command curl -s -o /dev/null. These fragments suggested the executable attempted to contact an external address and download content silently, though strings alone could not confirm the program's full behavior.

---

## Escalation Decision

I would escalate this incident to a Tier 2 analyst. The combination of an executable disguised as a text file, an overnight modification during a period with no scheduled clinical activity, the nurse's denial of creating it, and IT's confirmation that no approved process should create such files establishes multiple independent indicators. A follow-up search of /tmp also located /tmp/.x11-unix/.lock and /tmp/.x11-unix/.cache, supporting the possibility of related staging artifacts. The confirmed facts are documented above; what remains unknown is how the file reached the workstation, whether it executed, and whether any data left the network.

---

## Commands Used

| Command | Purpose |
|---|---|
| pwd && ls -la | Confirm location and list all files including hidden entries |
| file patient_notes.txt | Determine the true file type regardless of extension |
| stat patient_notes.txt | Examine size, ownership, and all three timestamps |
| strings patient_notes.txt | Extract readable text from the binary without executing it |
| find /home -name "*.txt" -perm /111 -mtime -1 | Search for related executable text-named files |
| find /tmp -type f -mtime -1 | Search for recently modified files in temporary storage |