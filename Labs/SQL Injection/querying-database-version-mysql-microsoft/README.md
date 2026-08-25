# SQL Injection: Querying the Database Type and Version on MySQL and Microsoft

| Field | Details |
| --- | --- |
| Platform | PortSwigger Web Security Academy |
| Category | SQL Injection |
| Status | Attempt documented; final result not verified |
| Target | Product category filter |
| Techniques | `ORDER BY`, `UNION SELECT`, text-column probing, `@@version` |

## Overview

This lab contains a SQL injection vulnerability in the product category filter. The objective is to use a `UNION` attack to display the database version string and thereby identify whether the backend is MySQL or Microsoft SQL Server.

The source note records a two-column result and several payloads. It does not contain HTTP responses, screenshots, a database version string, or confirmation that the lab was solved. This write-up therefore documents the attempt without claiming successful completion.

## Methodology

1. Test the category parameter for SQL injection.
2. Infer the number of columns returned by the original query.
3. Find a column that can display text.
4. Request the backend version with `@@version`.
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
' ORDER BY 1-- -
' ORDER BY 2-- -
```

The method is to increase the index until the application errors or its response changes. The source reports that the last working index was `2`, indicating two columns, but the corresponding responses were not saved.

### 3. Probe for a text-compatible column

The original note contains the following payloads:

```sql
' UNION SELECT 'a',NULL FROM dual-- -
' UNION SELECT NULL,'a' FROM dual-- -
```

The position that displays `a` would be suitable for extracting text. However, `dual` is Oracle-specific and does not match this MySQL/Microsoft lab. No response was recorded, so these attempts do not verify a usable output column.

### 4. Request the database version

The recorded version-query attempt was:

```sql
' UNION SELECT @@version,NULL-- -
```

Both MySQL and Microsoft SQL Server expose version information through `@@version`. The source note does not include the returned value or a completion message.

## Findings

| Finding | Result |
| --- | --- |
| Intended injection point | Product category filter |
| Reported column count | Two |
| Verified text-compatible column | Not recorded |
| Backend type and version | Not recorded |
| Lab completion | Not verified |

## Evidence

- Preserved: two `ORDER BY` probes, two text-column probes, and one `@@version` attempt.
- Not preserved: baseline responses, injected responses, response status codes, screenshots, version output, and the lab completion banner.
- Technical limitation: the recorded text-column probes use Oracle-specific `dual` syntax.

## Remediation

- Use parameterized queries for every database operation involving category input.
- Map public category identifiers to trusted internal values instead of concatenating raw input into SQL.
- Use a least-privileged database account.
- Return generic client-facing errors and keep detailed database errors in protected logs.
- Add automated negative tests for quote characters, SQL comments, and `UNION`-style input.

## References

- [PortSwigger lab: Querying the database type and version on MySQL and Microsoft](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft)
- [PortSwigger: SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks)
- [PortSwigger SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
