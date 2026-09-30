# Mediroza General Hospital — Authorized Web Security Assessment

> **Confidential training project. Private repository recommended.**

This repository contains sanitized documentation for an authorized black-box web application security assessment conducted as part of the Networkwalks training project.

## Scope

- **Target:** `https://medirozahospital.com`
- **Assessment type:** Black-box web application penetration test
- **Authorization:** Written authorization provided by the training organization
- **Out of scope:** Social engineering, denial-of-service testing, and systems outside the approved target domain

## Repository safety

This repository intentionally excludes:

- Patient PDFs and medical information
- PDF passwords
- Raw SQL database backups
- Employee or shareholder records
- National IDs, phone numbers, email addresses, and complete salary data
- Unredacted screenshots and browser exports
- Credentials, tokens, cookies, and session data

Only sanitized notes, redacted screenshots, report templates, and video-planning materials should be committed.

## Findings summary

| ID | Finding | Severity |
|---|---|---:|
| MZ-001 | Username enumeration | Medium |
| MZ-002 | SQL injection authentication bypass | Critical |
| MZ-003 | Unauthorized access to confidential patient PDFs | High |
| MZ-004 | Weak PDF password protection | High |
| MZ-005 | Sensitive information in PDF metadata | Medium |
| MZ-006 | Directory listing exposes database backup | Critical |
| MZ-007 | Confidential salary and shareholder data exposure | Critical |

## Contents

- `report/` — sanitized report draft and evidence index
- `video/` — video storyboard and narration plan
- `evidence/` — sanitized evidence descriptions only
- `screenshots/` — reviewed and redacted screenshots only
- `notes/` — scope, timeline, and attack-chain notes without secrets

## Handling instructions

Keep the repository private unless the instructor explicitly approves publication. Before every commit, inspect the staged files:

```bash
git diff --cached --stat
git diff --cached --name-only
git grep -nEi 'password|secret|token|national_id|patient|salary|phone|email' -- ':!README.md'
```

If any sensitive data appears, remove it before committing.
