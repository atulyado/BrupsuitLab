# Burp Suite Web Security Academy Lab Write-ups

Write-ups for authorized PortSwigger Web Security Academy labs. Each lab is stored in its own folder and separates recorded evidence from unverified methodology.

## Lab Index

| Category | Lab | Techniques | Evidence status |
| --- | --- | --- | --- |
| SQL Injection | [Querying the database type and version on MySQL and Microsoft](Labs/SQL%20Injection/querying-database-version-mysql-microsoft/README.md) | `ORDER BY`, `UNION SELECT`, `@@version` | Attempt documented; version response not recorded |
| SQL Injection | [Querying the database type and version on Oracle](Labs/SQL%20Injection/querying-database-version-oracle/README.md) | `ORDER BY`, `UNION SELECT`, `dual`, `v$version` | Attempt documented; recorded final payload is inconsistent with the reported column count |

## Repository Structure

```text
Labs/
└── SQL Injection/
    ├── querying-database-version-mysql-microsoft/
    │   └── README.md
    └── querying-database-version-oracle/
        └── README.md
```

Screenshots belong in an `assets` directory inside the relevant lab folder. No screenshots were available for the current write-ups, so no image assets are included.

## Evidence Policy

- Payloads and observations are included only when present in the source notes.
- Missing responses, version strings, and completion states are identified explicitly.
- Technical inconsistencies in the original notes are documented rather than silently converted into claimed results.
- Testing is limited to intentionally vulnerable or otherwise authorized systems.
