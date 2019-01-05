# PostgreSQL DB restore with ssh

Copy **<file_name>.dump** into the target machine (host) **Documents/** folder with scp. Assume "odoo" is the username, "162.158.7.12" is demo IP of the server. These commands work on Ubuntu or Debian machine.
```
$ scp pg_db_backup.dump odoo@162.158.7.12:/home/odoo/Documents/data_backup/
```

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

### Bonus: Connect local pgAdmin with remotely hosted PostgreSQL server
First we need to tell PostgreSQL server to listen remote connection. So, we need to ssh to the hosted server and then
```
#cd /etc/postgresql/9.x/main/
```
open file named postgresql.conf
```
$ sudo nano postgresql.conf
```
add this line to that file
```
listen_addresses = '*'
```

then open file named pg_hba.conf
```
$ sudo nano pg_hba.conf
```

and add this line to that file
```
host        all      all    0.0.0.0/0       md5
```
It allows access to all databases for all users with an encrypted password

restart your server
```
# sudo /etc/init.d/postgresql restart
```
Now run the locally installed pgAdmin and create a new connection. Click on "Add a connection to a server" button. Assume "162.158.7.12" is the hosted server and 5432 is the default port.
```
Name : connection_name
Host: 162.158.7.12
Port: 5432
Maintenance DB: postgres
Username: postgres
```

Click "OK" button. It will asked for the password!

**Note:** modification in postgresql.conf and pg_hba.conf will make the PostgreSQL insecure. Should not be a good idea to keep this settings for long in a live server. After every change need to restart the server.
