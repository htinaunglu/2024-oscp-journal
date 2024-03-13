
```bash
# connect to a remote sql database
mysql -u root -p'root' -h 192.168.50.16 -P 3306

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


# reterive password
MySQL [mysql]> SELECT user, authentication_string FROM mysql.user WHERE user = 'offsec';
+--------+---------------------------------------------------------------------+
| user   | authentication_string                                               |
+--------+---------------------------------------------------------------------+
| offsec | $A$005$?qvorPp8#lTKH1j54xuw4C5VsXe5IAa1cFUYdQMiBxQVEzZG9XWd/e6     |
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
