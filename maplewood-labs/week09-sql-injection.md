# Week 9: SQL Injection Attempt on Appointment Scheduling System

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** October 26, 2026  
**Analyst:** Oreoluwa David Dada 
**Case ID:** SQLI-2026-1024-001

---

## Incident Summary

On October 24, 2026, six search strings were submitted to the Maplewood appointment scheduling system between 2:14 AM and 3:01 AM. The inputs progressed from ordinary search activity to SQL injection indicators, including a quote probe, comment syntax, a tautology pattern, and UNION-based attempts referencing patient information. The application uses dynamic SQL without parameterization, and the incident has been escalated for further investigation.

## SQL Injection Log Analysis

| Entry | Time | Decoded Input | Type | Severity |
|---|---|---|---|---|
| 1 | 02:14:07 | Johnson | Benign Input | Info |
| 2 | 02:14:52 | `'` | Quote Probe | Medium |
| 3 | 02:18:31 | `Smith' --` | Comment Syntax Test | High |
| 4 | 02:23:14 | `' OR '1'='1` | Tautology Pattern | High |
| 5 | 02:48:39 | `' UNION SELECT NULL,NULL,NULL --` | UNION Injection Attempt | Critical |
| 6 | 03:01:22 | `' UNION SELECT patient_name,date_of_birth,ssn FROM patients --` | Targeted UNION SELECT Attempt | Critical |

## Attack Progression Summary

**Entry 1:** The ordinary name Johnson establishes a baseline search request.

**Entry 2:** The single quote appears designed to test how the application handles SQL syntax characters and whether malformed input produces a database error.

**Entry 3:** The comment syntax test attempts to use SQL comment characters to change how the remaining query is interpreted.

**Entry 4:** The tautology pattern uses a condition that evaluates as true and may bypass normal query restrictions if the input is interpreted as SQL.

**Entry 5:** The UNION SELECT attempt with three NULL values may be testing the number of columns expected by the original query.

**Entry 6:** The targeted UNION SELECT attempt references patient_name, date_of_birth, and ssn in the patients table. This suggests possible knowledge of the database schema or guessed column names. The access log does not prove that patient information was returned.

## Remediation Summary

**Primary application control:** Replace dynamically constructed SQL with PHP PDO prepared statements and bound parameters. Parameterization separates SQL instructions from user-supplied values.

**Input handling:** Validate appointment-search input according to its expected format and length. Removing single quotes alone is not a reliable primary defense because other SQL syntax and encoding techniques may bypass character filtering.

**Database permissions:** Apply least privilege so the application database account has only the permissions necessary for its assigned functions. Review its access to the appointments and patients tables.

**Detection and monitoring:** Improve application and database logging, monitor suspicious query patterns, and consider deploying a properly configured Web Application Firewall as an additional layer of defense.

## Escalation Summary

The confirmed findings include unsafe SQL query construction, the absence of a Web Application Firewall, and database permissions that include SELECT access to both appointments and patients. Six suspicious inputs were recorded, progressing to a targeted attempt referencing patient information. However, the available access logs do not establish whether the database executed the injected statements or returned patient records. The DBA team should review database query logs and audit records, while the application team investigates and remediates the vulnerable query construction. The SOC should preserve evidence and coordinate escalation to the DBA, application, privacy, and legal teams. Any potential PHI disclosure must be assessed using verified server-side evidence rather than assumptions based on submitted input.

## Query Parameter Inspection Log

### Entry 1
**Command:** 
curl -v "https://httpbin.org/get?last_name=Johnson" 2>&1 | grep -E "GET|last_name" 

**Terminal Output:** 
> GET /get?last_name=Johnson HTTP/2
    "last_name": "Johnson"
  "method": "GET",
  "url": "https://httpbin.org/get?last_name=Johnson"  

**Analyst Observation:** 
The command successfully sent a GET request to httpbin.org with the search parameter last_name=Johnson. The response demonstrates how a normal search value is included in a URL query string and transmitted to the server. This establishes a baseline for comparing ordinary input with suspicious input.

### Entry 2
**Command:** 
curl -s "https://httpbin.org/get?last_name=Johnson" | python3 -m json.tool | grep last_name

**Terminal Output:** 
"last_name": "Johnson"
    "url": "https://httpbin.org/get?last_name=Johnson"

**Analyst Observation:** 
The command retrieved the JSON response from httpsbin.org and displayed the last_name parameter. The output demonstrates that the server receives and represents the submitted search value as a query parameter. This helps explain how user input is transmitted from a browser or client to a web application.

### Entry 3
**Command:** 
curl -s "https://httpbin.org/get?last_name=%27+OR+%271%27%3D%271" | python3 -m json.tool | grep last_name

**Terminal Output:** 
"last_name": "Johnson"
    "url": "https://httpbin.org/get?last_name=Johnson"
oreoluwa611-academy@webshell:~$ curl -s "https://httpbin.org/get?last_name=%27+OR+%271%27%3D%271" | python3 -m json.tool | grep last_name
        "last_name": "' OR '1'='1"
    "url": "https://httpbin.org/get?last_name=%27+OR+%271%27%3D%271"

**Analyst Observation:** 
The command submitted a URL-encoded SQL tautology pattern to httpbin.org. The response demonstrates how special characters are encoded in a URL and represented after the server processes the request. This is useful for understanding how suspicious input travels through HTTP, but it does not execute SQL or confirm a successful SQL injection attack.

### Entry 4
**Command:** 
python3 - <<'PY'
import urllib.parse
strings = ["Johnson", "'", "Smith' --", "' OR '1'='1"]
for s in strings:
    print("last_name=" + urllib.parse.quote(s))
PY

**Terminal Output:** 
last_name=Johnson
last_name=%27
last_name=Smith%27%20--
last_name=%27%20OR%20%271%27%3D%271  

**Analyst Observation:** 
The Python command uses urllib.parse.quote() to convert four practice search values into URL-encoded strings. The single quote character is represented as %27, while spaces are represented as %20. For example, the value ' OR '1'='1 becomes %27%20OR%20%271%27%3D%271. URL encoding changes how special characters are represented when transmitted in a request, but it does not remove their meaning after decoding. An analyst must understand both the encoded and decoded forms because application and web server logs may preserve encoded characters rather than displaying the readable form. Comparing both forms helps identify suspicious input accurately during an investigation.

---

*CPSC 4584 | Governors State University | Fall 2026*