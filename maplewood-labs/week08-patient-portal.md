# Week 8: Patient Portal Login Page Behaving Strangely

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** October 19, 2026  
**Analyst:** Oreoluwa David Dada 
**Case ID:** WEB-2026-1019-001

---

## Incident Summary

Three patients reported unexpected behavior on the Maplewood Health System patient portal. The SOC confirmed four vulnerabilities in the original login flow involving credentials in a URL, missing session-cookie attributes, missing security headers, and unvalidated redirect handling. Credential-bearing login URLs were confirmed in the reviewed application server logs, but active exploitation has not been confirmed.

---

## HTTP Vulnerability Findings

| Finding | Location | Observed Value | Vulnerability | Severity |
|---|---|---|---|---|
| 1 | Login request method | `GET /login?username=jsmith&password=[redacted]` | Credential exposure in URL | High |
| 2 | Set-Cookie header | `sessionid=[redacted]; Path=/`; HttpOnly and Secure absent | Missing cookie security attributes | High |
| 3 | Response headers | Strict-Transport-Security, Content-Security-Policy, and X-Frame-Options absent | Missing security headers | Medium |
| 4 | Redirect handling | External destination `https://external-site.com/collect` accepted as a redirect target | Unvalidated redirect | High |

---

## Analysis Summary

**Finding 1:** Credentials in a GET URL may be retained in browser history, application or proxy logs, and other URL-handling systems. Proposed remediation is to submit credentials in the request body using POST over HTTPS and prevent sensitive values from being logged.

**Finding 2:** The session cookie lacks HttpOnly and Secure attributes. Proposed remediation is to configure the attributes appropriately and review SameSite and the wider session-management design.

**Finding 3:** The login response lacks three protective headers identified in the incident brief. Proposed remediation is for the application security team to assess the deployment and configure suitable headers, followed by validation testing.

**Finding 4:** The application accepts an external redirect destination without adequate validation. Proposed remediation is to restrict destinations to an approved allowlist or use safe internal destination identifiers.

---

## HTTP Header Inspection Commands Used

| Command | Purpose and Actual Observation |
|---|---|
| `curl -I https://example.com 2>/dev/null` | [Insert the exact output summary from Entry 1.] |
| `curl -I https://example.com 2>/dev/null \| grep -iE "strict\|content-security\|x-frame\|x-content\|referrer"` | [Insert the exact output summary from Entry 2.] |
| `curl -v --max-time 5 https://example.com 2>&1 \| grep -E "^[<>]" \| head -20` | [Insert the exact output summary from Entry 3.] |
| `curl -IL --max-time 5 https://example.com 2>/dev/null \| grep -E "HTTP\|Location\|Set-Cookie"` | [Insert the exact output summary from Entry 4.] |

---

## Escalation Summary

The incident evidence identifies four weaknesses in the Maplewood patient portal login flow. The findings should be escalated through the supervisor to the application owners and security engineering team for authorized investigation, remediation planning, and validation. The proposed fixes have not been implemented or verified as part of this analysis. Tier 1 documentation is limited to the supplied evidence, risk assessment, and escalation; application owners and authorized security personnel are responsible for code and configuration changes, testing, and production deployment.

---

*CPSC 4584 | Governors State University | Fall 2026*