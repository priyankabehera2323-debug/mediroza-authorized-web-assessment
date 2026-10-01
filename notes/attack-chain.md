# Attack Chain 

1. Public reconnaissance disclosed `/patient/`, `/staff/`, and `/old/`.
2. Directory listings exposed application structure and the backup filename.
3. Login behaviour disclosed an authentication weakness (username enumeration).
4. Authorised testing demonstrated an SQL injection authentication bypass on the patient login page.
5. Three confidential patient PDFs became accessible via the patient portal.
6. Approved local analysis recovered the PDF contents (weak per-file passwords).
7. PDF metadata referenced the `/old/` backup area.
8. The public backup (`mediroza_db_backup_2019.sql`) contained `staff` and `shareholders` tables.
9. Local analysis confirmed 30 staff rows and 10 shareholder rows — no individual records reproduced.

See [`../report/report.md`](../report/report.md) for full finding detail, severities, and remediation.
