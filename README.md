# Install multiple mysql instances on Linux (debian) with different mysql versions without mysqld_multi

## Procedure

1. wget https://downloads.mysql.com/archives/get/p/23/file/mysql-5.7.35-linux-glibc2.12-x86_64.tar.gz
2. untar in `/opt/mysql57`
3. copy `my.cnf` into `/opt/mysql57`
4. copy `mysql57.sh` into `/etc/init.d/mysql57` and make it executable
5. create `/var/lib/mysql57` and make ownership to mysql:mysql
6. run from `/opt/mysql57/bin` : `./mysqld --defaults-extra-file=/opt/mysql57/my.cnf --initialize` (it will create base databases)
7. get from `/var/log/mysql/mysql57-error.log` generated passwd from admin
8. run from same folder `./mysqld --defaults-extra-file=/opt/mysql57/my.cnf --verbose` (if cmd fails, look at `/var/log/mysql/mysql57-error.log`)
9. check if mysql 5.7 is running by `telnet 127.0.0.1 3307`
10. kill previous command
11. if everything was ok, run `/etc/init.d/mysql57 start`


## Additionnal features

- make symlinks of mysql57 to run mysql commands with the appropriate mysql version like : ln -s /opt/mysql57/bin/mysql /usr/bin/mysql57

## Force admin password to be permanent

If we connect using a adminer tool, with generated root password, it may fail (as password is defined as expired).
- modify temporarly my.cnf and uncomment instruction `skip-grant-tables` and restart daemon
- connect using a adminer like tool using whatever password
- run following SQL statement ` UPDATE mysql.user SET password_expired = 'N' WHERE User = 'root' AND Host = 'localhost'; FLUSH PRIVILEGES;` 
- comment instruction `skip-grant-tables` and restart daemon
- try to connect using adminer with generated password :)

## Points of attention

- have distinct port, socket, datadir from the existing mysql/mariadb database
```
[mysqld]
pid-file    = /var/run/mysqld/mysqld57.pid
socket      = /var/run/mysqld/mysqld57.sock
port        = 3307
basedir     = /opt/mysql57
datadir     = /var/lib/mysql57
lc-messages-dir = /opt/mysql57/share/
```