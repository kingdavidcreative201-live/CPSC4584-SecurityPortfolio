# Week 5: Weak Password Policy Exposes Billing Portal
**Course:** CPSC 4584 | Special Topics in Information Security
**Date:** September 28, 2026
**Analyst:** Oreoluwa David Dada
**Incident ID:** INC-2026-0928-001

---

## Incident Summary

Maplewood detected a distributed credential-stuffing attack against the billing portal after 847 failed login attempts from 12 IP addresses over the preceding 72 hours. The attacker successfully accessed the billing administrator account using a compromised password and maintained access for 47 minutes, during which insurance records for 3,247 unique patients were accessed.

---

## TLS Assessment

**TLS Version:** TLS 1.3  
**Status:** Compliant  
**What TLS Protected:** Data in transit between the client and server by providing an encrypted communication channel.  
**What TLS Did Not Protect:** Authentication. TLS could encrypt the connection but could not prevent an attacker from using valid compromised credentials.

---

## Authentication Controls Gap Analysis

| Control | Required | Status | Finding |
|---------|----------|--------|---------|
| MFA | Maplewood sensitive-account standard | Not Implemented | MFA was not implemented, allowing the compromised password to be sufficient for authentication. An additional authentication factor could have prevented the stolen password from being sufficient by itself. |
| Failed Attempt Protection | Account-based throttling and alerting | Not Implemented | The 847 failed attempts from 12 IP addresses indicate that account-based throttling and alerting were not in place to sufficiently slow or identify the repeated authentication attempts. |
| Password Policy | NIST SP 800-63B-4 aligned | Needs Improvement | The account used a password that had previously appeared in a leaked credential set. Password-selection controls should prevent known compromised passwords from being accepted. |
| Compromised Credential Response | Detect and invalidate confirmed compromised authenticators | Not Implemented | The compromised password was not detected and invalidated before it was used against the billing portal. |
| Automated Attack Controls | Throttling, bot detection, or adaptive controls as appropriate | Not Implemented | No automated controls were in place to adequately respond to the distributed credential-stuffing pattern involving multiple IP addresses. |

---

## OpenSSL Commands Practiced

| Command | Purpose |
|---------|---------|
| `openssl genrsa -out private_key.pem 2048` | Generated a 2048-bit RSA private key and saved it as private_key.pem. |
| `openssl rsa -in private_key.pem -pubout -out public_key.pem` | Derived the public key associated with the RSA private key and saved it as public_key.pem. |
| `openssl rsa -in private_key.pem -text -noout` | Displayed the structural details of the RSA private key without outputting the key in PEM format. |
| `cat public_key.pem` | Displayed the contents of the public key PEM file, including its BEGIN PUBLIC KEY and END PUBLIC KEY markers. |

---

## Escalation Summary

The confirmed findings show that the Maplewood billing portal experienced a distributed credential-stuffing attack that resulted in unauthorized access to a billing administrator account. The attacker used a compromised password after 847 failed login attempts from 12 IP addresses and maintained access for 47 minutes. Insurance records belonging to 3,247 unique patients were accessed.

The primary authentication control gaps were the lack of MFA, failed-attempt protection, automated attack controls, and a compromised-credential response. The password policy also requires improvement because the password used for the account had previously appeared in a leaked credential set. TLS 1.3 was functioning as intended and protected data in transit, but it did not protect against authentication using compromised credentials.

The full scope of accessed information, required incident-response actions, credential reset decisions, notification requirements, and other remediation decisions should be reviewed and approved by authorized Maplewood leadership.

---
*CPSC 4584 | Governors State University | Fall 2026*