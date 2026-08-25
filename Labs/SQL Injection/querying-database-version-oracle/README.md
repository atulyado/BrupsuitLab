# SQL Injection: Querying the Database Type and Version on Oracle

| Field | Details |
| --- | --- |
| Platform | PortSwigger Web Security Academy |
| Category | SQL Injection |
| Status | Attempt documented; final result not verified |
| Target | Product category filter |
| Techniques | `ORDER BY`, `UNION SELECT`, Oracle `dual`, `v$version` |

## Overview

This lab contains a SQL injection vulnerability in the product category filter. The objective is to use a `UNION` attack to display the Oracle database version string.

The source note records a two-column result and several payloads. It does not contain HTTP responses, screenshots, a database banner, or confirmation that the lab was solved. Its recorded version payload also contains three selected expressions despite the reported two-column result. This write-up preserves and explains that discrepancy without claiming successful completion.

## Methodology

1. Test the category parameter for SQL injection.
2. Infer the number of columns returned by the original query.
3. Use Oracle's `dual` table to find a column that can display text.
4. Query the `v$version` view for a database banner.
5. Compare each response with a clean baseline request.

## Steps

### 1. Locate the injection point

The suspected injection point is the product category filter, represented in the note as:

```text
?category=Gifts
```

Append a single quote and compare the response with the baseline:

```text
?category=Gifts'
```

An error or another repeatable response difference can indicate that the input is being interpreted as SQL. The source does not preserve the response to this test.

### 2. Determine the column count

The note records these `ORDER BY` probes:

```sql
' ORDER BY 1--
' ORDER BY 2--
```

The method is to increase the index until the application errors or its response changes. The source reports that the last working index was `2`, indicating two columns, but the corresponding responses were not saved.

### 3. Probe for a text-compatible column

Oracle requires a `FROM` clause, so the recorded probes use the built-in `dual` table:

```sql
' UNION SELECT 'a',NULL FROM dual--
' UNION SELECT NULL,'a' FROM dual--
```

The position that displays `a` would be suitable for extracting text. The source does not state which probe worked or preserve either response.

### 4. Request the database version

The original note records this attempt against Oracle's `v$version` view:

```sql
' UNION SELECT banner,NULL,NULL FROM v$version--
```

This payload selects three expressions even though the note reports that the original query returns two columns. Both sides of a `UNION` must return the same number of columns, so the attempt is internally inconsistent. The source does not preserve a subsequent corrected attempt, database banner, or completion message.

## Findings

| Finding | Result |
| --- | --- |
| Intended injection point | Product category filter |
| Reported column count | Two |
| Verified text-compatible column | Not recorded |
| Recorded version payload matches the column count | No; it selects three expressions |
| Database version and lab completion | Not recorded or verified |

## Evidence

- Preserved: two `ORDER BY` probes, two Oracle `dual` text-column probes, and one `v$version` attempt.
- Not preserved: baseline responses, injected responses, response status codes, screenshots, version output, and the lab completion banner.
- Technical limitation: the final recorded payload conflicts with the reported two-column query.

## Remediation

- Use parameterized queries for every database operation involving category input.
- Map public category identifiers to trusted internal values instead of concatenating raw input into SQL.
- Use a least-privileged database account that cannot read unnecessary system views.
- Return generic client-facing errors and keep detailed database errors in protected logs.
- Add automated negative tests for quote characters, SQL comments, and `UNION`-style input.

## References

- [PortSwigger lab: Querying the database type and version on Oracle](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle)
- [PortSwigger: SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
- [PortSwigger SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
