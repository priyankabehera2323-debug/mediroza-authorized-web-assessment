# Attack Chain

1. Public reconnaissance disclosed approved application paths.
2. Directory listings exposed application structure and an old backup location.
3. The patient login page revealed an authentication weakness.
4. Authorized testing demonstrated an SQL injection authentication bypass.
5. Three confidential patient PDFs became accessible.
6. Approved local analysis recovered the PDF contents.
7. PDF metadata revealed a clue referencing the old backup directory.
8. The public backup contained staff and shareholder tables.
9. Local analysis confirmed 30 staff rows and 10 shareholder rows.
10. Sensitive individual records were not copied into the report.
