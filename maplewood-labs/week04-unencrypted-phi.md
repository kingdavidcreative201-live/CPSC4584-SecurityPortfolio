# Week 4: Unencrypted Patient Records on a Shared Drive

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** September 2026  
**Analyst:** Oreoluwa David Dada

---

## Incident Summary

During the Maplewood investigation, an unencrypted shared network folder named `PATIENT_DATA_ARCHIVE` was identified. The folder contained approximately 847 files with 8,247 unique patient records. The records contained sensitive patient information including names, dates of birth, Social Security numbers, diagnosis codes, and insurance information.

The patient information was stored in plaintext on a shared drive without adequate protection. The investigation also found that historical access logs were not available, making it difficult to determine who had previously accessed the records or whether unauthorized access had occurred.

---

## HIPAA Compliance Assessment

| Requirement | Status | Finding |
|---|---|---|
| Encryption at Rest | Requires Review | Patient ePHI was stored in plaintext on an unencrypted shared drive. Maplewood should document and implement appropriate safeguards to protect the information from unauthorized access. |
| Access Controls | Control Failure | Read and write access was broadly available across the clinic locations and inpatient facilities instead of being limited according to the minimum necessary access principle. |
| Audit Controls | Control Failure | Historical access logs were not retained, making it difficult to reconstruct previous access or determine whether unauthorized viewing or modification occurred. |

---

## Cryptographic Controls Evaluated

### Base64 Encoding

Base64 was evaluated as a method of protecting the patient records. Base64 is an encoding method rather than encryption. It does not provide meaningful confidentiality because the encoded data can easily be converted back to its original form without a secret key.

Therefore, Base64 should not be treated as a security control for protecting ePHI.

### Caesar Cipher

A Caesar cipher was also evaluated. This is a classical substitution cipher that shifts characters by a fixed number of positions. It provides very weak protection because there are only a small number of possible shifts and the cipher can be easily defeated through brute-force testing or frequency analysis.

A Caesar cipher is therefore not appropriate for protecting sensitive patient information.

### Modern Encryption

Sensitive patient information should be protected using an approved modern encryption method with appropriate key management. The encryption algorithm alone is not sufficient; the organization must also properly manage and protect the encryption keys.

---

## Hashing Commands Practiced

| Command | Purpose | Output Length |
|---|---|---|
| `echo -n "..." \| sha256sum` | Demonstrates SHA-256 hashing and the avalanche effect, where a small change in the input produces a substantially different hash. | 64 hexadecimal characters (256 bits) |
| `echo -n "..." \| md5sum` | Demonstrates MD5 hashing and allows comparison with SHA-256. MD5 is deprecated for security-sensitive collision resistance. | 32 hexadecimal characters (128 bits) |
| `sha256sum backup` | Creates a SHA-256 hash that can be used as a baseline to detect later changes to a file. | 64 hexadecimal characters (256 bits) |

---

## Escalation Summary

The investigation identified approximately 847 files containing 8,247 unencrypted patient records stored on a shared network drive. The records contained sensitive ePHI, including personally identifiable and medical information.

The primary concerns are the lack of appropriate protection for data at rest, overly broad access permissions, and the absence of historical access logs. These conditions make it difficult to determine whether unauthorized individuals accessed or modified the records.

The finding should be escalated to the appropriate security, privacy, compliance, and legal personnel. The organization should preserve the available evidence, review current access permissions, determine whether unauthorized access occurred, and implement appropriate safeguards for protecting ePHI. Any potential breach determination should be made by the appropriate organizational privacy and legal personnel based on the available evidence.

---

**CPSC 4584 | Governors State University | Fall 2026**