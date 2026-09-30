# Testing Timeline

## 2026-09-28

### Reconnaissance

- Reviewed `robots.txt`.
- Observed references to `/patient/`, `/staff/`, and `/old/`.
- Observed directory listings on approved paths.
- Saved sanitized evidence references.

### Initial access

- Reviewed the patient login page.
- Compared controlled login error responses.
- Authorized input-handling testing demonstrated an authentication weakness.
- Accessed the patient portal within the exercise scope.

## 2026-09-29
### Document handling

- Retrieved only the three assigned PDF reports.
- Preserved original files unchanged.
- Recorded SHA-256 hashes.
- Created restricted working copies.

### PDF analysis

- Confirmed PDF password protection.
- Recovered contents using the approved local training method.
- Decrypted working copies only.
- Passwords were not recorded in this repository.

## 2026-09-30

### Metadata analysis

- Inspected decrypted PDF metadata locally.
- One report contained a redacted internal clue referencing `/old/`.
- Patient names and medical details were excluded from reporting.

## 2026-10-01

### Backup analysis

- Retrieved only the assigned SQL backup.
- Recorded its SHA-256 hash.
- Confirmed the presence of staff and shareholder tables.
- Confirmed 30 staff rows and 10 shareholder rows.
- Raw records were not copied into the report or repository.
