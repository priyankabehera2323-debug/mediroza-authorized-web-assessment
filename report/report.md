# Mediroza General Hospital — Web Application Security Assessment

**Prepared by:** Priyanka
**Organisation:** Networkwalks
**Batch:** B083 | Week 4
**Assessment date:** 30 September 2026
**Target:** `https://medirozahospital.com`
**Classification:** Confidential — Authorised Personnel Only

---

## 1. Executive Summary

An authorised black-box penetration test and vulnerability assessment was conducted against the Mediroza General Hospital web application as the Networkwalks Batch B083 Week 4 project. Testing was limited to the approved target domain and followed the written authorisation and engagement restrictions supplied for the controlled exercise. Social engineering, denial-of-service testing, high-volume testing, destructive actions, and testing outside the approved domain were excluded.

The assessment identified seven findings ranging from Medium to Critical severity. The most serious issues were an SQL injection authentication bypass, unauthorised access to confidential patient laboratory reports, and a publicly accessible database backup containing employee compensation and shareholder information.

The verified attack chain progressed from public reconnaissance to application access, document recovery, metadata analysis, and exposure of sensitive corporate data. Local analysis confirmed that the exposed backup contained a `staff` table with 30 rows and a `shareholders` table with 10 rows. Individual patient, employee, and shareholder records are intentionally omitted from this report.

**Overall risk rating: CRITICAL.** Immediate remediation is recommended for the authentication bypass, document authorisation controls, public backup exposure, and backup-handling process.

---

## 2. Scope and Methodology

### 2.1 Scope

| Item | Details |
|---|---|
| Target | `https://medirozahospital.com` |
| Assessment type | Authorised black-box web application penetration test |
| Authorisation | Written authorisation provided by Networkwalks for the controlled exercise |
| Out of scope | Social engineering, denial of service, external systems, destructive or modifying actions |

### 2.2 Methodology

The assessment followed four phases:

1. **Reconnaissance** — Reviewed public application information, `robots.txt`, sitemap content, and approved directory responses.
2. **Vulnerability identification** — Analysed login behaviour, exposed paths, document access, PDF protection, metadata, and backup configuration.
3. **Controlled exploitation** — Demonstrated verified impact using the minimum necessary access and retrieval.
4. **Evidence and reporting** — Preserved originals, recorded SHA-256 hashes, redacted sensitive information, and documented remediation.

### 2.3 Tools Used

`curl`, browser developer tools, `pdfinfo`, `qpdf`, `exiftool`, `sha256sum`, and a local Python script for row counts. Sensitive files were analysed locally and were not uploaded to online services.

---

## 3. Findings and Proof of Exploitation

| ID | Finding | Risk |
|---|---|---:|
| MZ-001 | Username enumeration | Medium |
| MZ-002 | SQL injection authentication bypass | Critical |
| MZ-003 | Unauthorised access to confidential patient PDFs | High |
| MZ-004 | Weak PDF password protection | High |
| MZ-005 | Sensitive information in PDF metadata | Medium |
| MZ-006 | Directory listing exposes database backup | Critical |
| MZ-007 | Confidential salary and shareholder data exposure | Critical |

---

## 4. Detailed Findings

### 4.1 M1 / MZ-001 — Username Enumeration
**Severity:** Medium
**Affected component:** Patient login page

The login page returned different failure messages for an unknown username versus a recognised username. This behaviour allows an attacker to confirm valid account names before attempting further attacks.

- **Evidence:** Redacted comparison of controlled failed-login responses (`evidence/`).
- **Impact:** Valid usernames can be identified, reducing the effort required for account-targeting attacks.
- **Recommendation:** Return one generic error message for all failed login attempts (e.g. "Invalid credentials. Please try again."). Apply rate limiting and monitoring without revealing whether an account exists.

### 4.2 M1 / MZ-002 — SQL Injection Authentication Bypass
**Severity:** Critical
**Affected component:** Patient login page

Authorised input-handling testing demonstrated that user input affected the login database query and allowed authentication to be bypassed without a valid password.

- **Impact:** An unauthenticated attacker could access the patient portal and continue to confidential document resources.
- **Evidence:** Redacted authentication-bypass proof and timeline entry. The reusable test value and credentials are intentionally omitted from this report.
- **Recommendation:** Replace string-concatenated SQL with parameterised queries or prepared statements. Store passwords using a strong password-hashing function, suppress detailed database errors, rotate any exposed credentials, and retest the login flow.

### 4.3 M1 / MZ-003 — Unauthorised Access to Confidential Patient PDFs
**Severity:** High
**Affected component:** Patient portal reports

After the authentication weakness was demonstrated, the patient portal exposed three confidential laboratory report PDFs. Only the three files required by the exercise were retrieved. The original files were preserved locally and SHA-256 hashes were recorded.

- **Impact:** An unauthorised user could access confidential medical documents.
- **Evidence:** Redacted portal screenshot, report filenames, and local file-hash record. Patient names and medical content are excluded from this report.
- **Recommendation:** Enforce server-side authorisation for every document request, verify access to the specific patient/document object, store private files outside the public web root, and serve them through an access-controlled endpoint.

### 4.4 M2 / MZ-004 — Weak PDF Password Protection
**Severity:** High
**Affected component:** Patient PDF reports

All three retrieved PDFs required passwords. The approved local recovery process successfully recovered their contents. Decrypted working copies were verified with `pdfinfo` as `Encrypted: no`. Password values are omitted from this report.

- **Impact:** Weak document passwords did not provide meaningful protection once the files had been obtained.
- **Evidence:** Redacted password-recovery screenshot, decrypted-file hashes, and decryption proof.
- **Recommendation:** Use strong modern document encryption and secure key management. Do not rely on predictable per-file passwords as the primary access-control mechanism.

### 4.5 M3 / MZ-005 — Sensitive Information in PDF Metadata
**Severity:** Medium
**Affected component:** Decrypted patient report metadata

Local metadata analysis identified an internal author identifier and a comment referencing a database backup in `/old/`. The same metadata also contained patient-identifying fields, which were redacted and excluded from this report.

- **Impact:** Document metadata disclosed internal information and helped identify an additional exposed server resource.
- **Evidence:** Redacted metadata record. The internal identifier and patient-identifying values are not reproduced.
- **Recommendation:** Strip unnecessary metadata before distributing documents, review document-export workflows, and prevent internal comments or infrastructure locations from being embedded in externally distributed files.

### 4.6 M3 / MZ-006 — Directory Listing Exposes Database Backup
**Severity:** Critical
**Affected component:** `/old/`

The `/old/` directory was publicly browsable and displayed a database backup without requiring authentication. The assigned backup was retrieved and its integrity hash was recorded.

- **Evidence:** Directory-listing response and backup hash:
  ```
  mediroza_db_backup_2019.sql
  SHA-256: 15d8b49ae104b073d5b0a7289189bdc3940bbab96168e9bdac882ac59feed257
  ```
- **Impact:** An unauthenticated visitor could locate and download a sensitive database backup.
- **Recommendation:** Remove backups from the web root, disable directory listing, restrict backup storage, encrypt backups at rest, apply least privilege, and review migration cleanup procedures.

### 4.7 M3 / MZ-007 — Confidential Salary and Shareholder Data Exposure
**Severity:** Critical
**Affected component:** Public database backup

Local analysis confirmed that the backup contained two sensitive tables:

| Table | Rows | Relevant fields |
|---|---:|---|
| `staff` | 30 | Full name, job title, department, email, phone, national ID, monthly salary |
| `shareholders` | 10 | Shareholder name, share percentage, shares held, share class |

Individual records were not reproduced in this report because they were not necessary to prove the exposure.

- **Impact:** An unauthenticated visitor could obtain confidential employee compensation information and corporate ownership information.
- **Recommendation:** Remove the backup immediately, rotate any exposed secrets, encrypt backups, restrict backup access, establish secure retention and deletion procedures, and conduct a retest.

---

## 5. Milestones and Deliverables

### 5.1 M1 — Initial Access
**Objective:** Attack the website and retrieve three confidential patient PDF laboratory reports.
**Delivered evidence:** Redacted proof of portal access, three report filenames, original-file hashes, and findings MZ-001–MZ-003.

### 5.2 M2 — Data Extraction
**Objective:** Analyse the encryption on all three retrieved files and recover their contents using appropriate approved tools and wordlists.
**Delivered evidence:** Redacted password-recovery proof, decrypted working-copy hashes, and `pdfinfo` output confirming `Encrypted: no` for all three files.

### 5.3 M3 — Attack and Critical Data Exposure
**Objective:** Analyse all retrieved material and identify staff salary and shareholder information on the client server.
**Delivered evidence:** Redacted PDF metadata clue, public backup-directory evidence, backup hash, database schema summary, and row counts confirming 30 staff rows and 10 shareholder rows.

### 5.4 M4 — Penetration-Test Report
**Objective:** Submit a complete professional report with an executive summary, scope and methodology, findings and proof of exploitation, risk ratings, and recommendations and remediation.
**Delivered evidence:** This report and its redacted evidence index.

---

## 6. Attack Chain Summary

| Step | Observed progression |
|---:|---|
| 1 | Public reconnaissance disclosed `/patient/`, `/staff/`, and `/old/`. |
| 2 | Directory listings exposed application structure and the backup filename. |
| 3 | Login behaviour disclosed an authentication weakness. |
| 4 | Authorised testing demonstrated an SQL injection authentication bypass. |
| 5 | Three confidential patient PDFs became accessible. |
| 6 | Approved local analysis recovered the PDF contents. |
| 7 | PDF metadata referenced the `/old/` backup area. |
| 8 | The public backup contained staff and shareholder tables. |
| 9 | Local analysis confirmed 30 staff rows and 10 shareholder rows. |

---

## 7. Risk Rating

The overall risk is **Critical** because multiple issues combine to permit unauthorised access to medical documents and confidential corporate information with low complexity and no legitimate privileges.

| Priority | Action |
|---|---|
| Immediate | Fix SQL injection using prepared statements and safe error handling. |
| Immediate | Remove the public database backup and disable directory listing. |
| Immediate | Enforce object-level authorisation for every document request. |
| High | Use strong document encryption and secure key management. |
| High | Remove sensitive PDF metadata and internal comments. |
| Ongoing | Rotate exposed secrets, monitor access, and retest all fixes. |

---

## 8. Recommendations and Remediation

1. **Authentication** — Use prepared statements, generic login errors, strong password hashing, rate limiting, session rotation, and safe error handling.
2. **Document access** — Store confidential files outside the web root and require server-side authorisation for each requested object.
3. **Document protection** — Use strong encryption, secure key management, and controlled distribution workflows.
4. **Metadata** — Remove internal comments, usernames, infrastructure paths, and unnecessary patient-identifying metadata before distribution.
5. **Server configuration** — Disable directory listing and ensure backups, logs, and migration artefacts are not web-accessible.
6. **Backup security** — Store backups in private access-controlled locations, encrypt them, define retention periods, delete obsolete copies, and rotate secrets after exposure.
7. **Verification** — Retest every remediation item and preserve a retest record.

---

## 9. Conclusion

The assessment identified a complete attack chain from public reconnaissance to sensitive patient and corporate-data exposure. The findings are well-known classes of vulnerability with established remediation practices, but their combination creates a critical overall risk.

Critical and High findings should be remediated immediately, particularly the SQL injection, document authorisation weakness, public backup, and directory-listing configuration. A follow-up penetration test should verify that access controls, backup storage, document encryption, and metadata handling have been corrected.

---

## 10. Evidence Index

Only redacted evidence is included in this repository.

| Evidence ID | Description | Related finding |
|---|---|---|
| E-001 | Sanitized `robots.txt` and path-discovery record | MZ-001, MZ-006 |
| E-002 | Redacted login-error comparison | MZ-001 |
| E-003 | Redacted authentication-bypass proof | MZ-002 |
| E-004 | Report filenames and original file hashes | MZ-003 |
| E-005 | Redacted PDF password-recovery screenshot | MZ-004 |
| E-006 | Decryption proof showing `Encrypted: no` | MZ-004 |
| E-007 | Redacted PDF metadata clue | MZ-005 |
| E-008 | Public backup directory listing | MZ-006 |
| E-009 | Backup SHA-256 hash | MZ-006 |
| E-010 | Database schema without data rows | MZ-007 |
| E-011 | Row-count output | MZ-007 |

**Confidentiality statement:** This report intentionally excludes passwords, raw patient reports, medical details, national IDs, phone numbers, email addresses, complete salary records, shareholder records, cookies, tokens, and unredacted screenshots. The evidence is retained only in the restricted assessment workspace and shared through the instructor-approved channel.

*This assessment was conducted under written authorisation as part of the Networkwalks Batch B083 training programme. These techniques must never be applied to any system without explicit written permission from the owner.*
