# PostgreSQL DB restore with ssh

Copy **<file_name>.dump** into the target machine (host) **Documents/** folder with scp

## Step-by-step
1. ssh to host
```
$ ssh odoo@host.ip 
```

2. from terminal to postgresql
```
# sudo -u postgres psql
```

3. show roles in postgresql
```
postgres=# SELECT rolname FROM pg_roles;
```

4. show databases
```
postgres=# \l
```

5. quit from postgresql database console
```
postgres=# \q
```

6. create database with role
```
$ sudo -u odoo createdb abc_20181227
```

7. open the file **pg_hba.conf** for Ubuntu it will be in **/etc/postgresql/9.x/main** and change this line:
```
local      all          postgres        peer
local      all          all             peer
```
to
```
local      all          postgres        trust
local      all          all             trust
```

8. Restart the server
```
# sudo service postgresql restart
```

Authentication methods details:
```
trust - anyone who can connect to the server is authorized to access the database

peer - use client's operating system user name as database user name to access it.

md5 - password-base authentication
```

9. restore db from terminal
```
$ pg_restore -U odoo -d abc_20181227 abc_2018-12-27.dump 

```
