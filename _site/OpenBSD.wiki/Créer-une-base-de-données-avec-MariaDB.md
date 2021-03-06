# Créer une base de données avec MariaDB

> pkg_add -i mariadb-server

```
# pkg_add -i mariadb-server
quirks-2.241 signed on 2016-07-26T16:56:10Z
mariadb-server-10.0.25p0v1:mariadb-client-10.0.25v1: ok
mariadb-server-10.0.25p0v1:p5-FreezeThaw-0.5001: ok
mariadb-server-10.0.25p0v1:p5-MLDBM-2.05: ok
mariadb-server-10.0.25p0v1:p5-Net-Daemon-0.48p0: ok
mariadb-server-10.0.25p0v1:p5-PlRPC-0.2020: ok
mariadb-server-10.0.25p0v1:p5-Math-Base-Convert-0.08: ok
mariadb-server-10.0.25p0v1:p5-Params-Util-1.07p1: ok
mariadb-server-10.0.25p0v1:p5-Module-Runtime-0.014: ok
mariadb-server-10.0.25p0v1:p5-Clone-0.38: ok
mariadb-server-10.0.25p0v1:p5-SQL-Statement-1.407: ok
mariadb-server-10.0.25p0v1:p5-DBI-1.633: ok
mariadb-server-10.0.25p0v1:p5-DBD-mysql-4.033: ok
mariadb-server-10.0.25p0v1: ok
The following new rcscripts were installed: /etc/rc.d/mysqld
See rcctl(8) for details.
Look in /usr/local/share/doc/pkg-readmes for extra documentation.
```

> pkg_add -i php-pdo_mysql

> pkg_add -i php-mysql

> /usr/local/bin/mysql_install_db

```
# /usr/local/bin/mysql_install_db
WARNING: The host 'OpenBSD.my.domain' could not be looked up with resolveip.
This probably means that your libc libraries are not 100 % compatible
with this binary MariaDB version. The MariaDB daemon, mysqld, should work
normally with the exception that host name resolving will not work.
This means that you should use IP addresses instead of hostnames
when specifying MariaDB privileges !
Installing MariaDB/MySQL system tables in '/var/mysql' ...
170405 19:47:41 [Note] /usr/local/libexec/mysqld (mysqld 10.0.25-MariaDB) starting as process 60298 ...
170405 19:47:41 [Note] InnoDB: Using mutexes to ref count buffer pool pages
170405 19:47:41 [Note] InnoDB: The InnoDB memory heap is disabled
170405 19:47:41 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
170405 19:47:41 [Note] InnoDB: Memory barrier is not used
170405 19:47:41 [Note] InnoDB: Compressed tables use zlib 1.2.3
170405 19:47:41 [Note] InnoDB: Not using CPU crc32 instructions
170405 19:47:41 [Note] InnoDB: Initializing buffer pool, size = 128.0M
170405 19:47:41 [Note] InnoDB: Completed initialization of buffer pool
170405 19:47:41 [Note] InnoDB: The first specified data file ./ibdata1 did not exist: a new database to be created!
170405 19:47:41 [Note] InnoDB: Setting file ./ibdata1 size to 12 MB
170405 19:47:41 [Note] InnoDB: Database physically writes the file full: wait...
170405 19:47:41 [Note] InnoDB: Setting log file ./ib_logfile101 size to 48 MB
170405 19:47:42 [Note] InnoDB: Setting log file ./ib_logfile1 size to 48 MB
170405 19:47:42 [Note] InnoDB: Renaming log file ./ib_logfile101 to ./ib_logfile0
170405 19:47:42 [Warning] InnoDB: New log files created, LSN=45781
170405 19:47:42 [Note] InnoDB: Doublewrite buffer not found: creating new
170405 19:47:42 [Note] InnoDB: Doublewrite buffer created
170405 19:47:42 [Note] InnoDB: 128 rollback segment(s) are active.
170405 19:47:42 [Warning] InnoDB: Creating foreign key constraint system tables.
170405 19:47:42 [Note] InnoDB: Foreign key constraint system tables created
170405 19:47:42 [Note] InnoDB: Creating tablespace and datafile system tables.
170405 19:47:42 [Note] InnoDB: Tablespace and datafile system tables created.
170405 19:47:42 [Note] InnoDB: Waiting for purge to start
170405 19:47:43 [Note] InnoDB:  Percona XtraDB (http://www.percona.com) 5.6.29-76.2 started; log sequence number 0
170405 19:47:43 [Warning] Failed to load slave replication state from table mysql.gtid_slave_pos: 1146: Table 'mysql.gtid_slave_pos' doesn't exist
170405 19:47:43 [Note] InnoDB: FTS optimize thread exiting.
170405 19:47:43 [Note] InnoDB: Starting shutdown...
170405 19:47:45 [Note] InnoDB: Shutdown completed; log sequence number 1616697
OK
Filling help tables...
170405 19:47:45 [Note] /usr/local/libexec/mysqld (mysqld 10.0.25-MariaDB) starting as process 52316 ...
170405 19:47:45 [Note] InnoDB: Using mutexes to ref count buffer pool pages
170405 19:47:45 [Note] InnoDB: The InnoDB memory heap is disabled
170405 19:47:45 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
170405 19:47:45 [Note] InnoDB: Memory barrier is not used
170405 19:47:45 [Note] InnoDB: Compressed tables use zlib 1.2.3
170405 19:47:45 [Note] InnoDB: Not using CPU crc32 instructions
170405 19:47:45 [Note] InnoDB: Initializing buffer pool, size = 128.0M
170405 19:47:45 [Note] InnoDB: Completed initialization of buffer pool
170405 19:47:45 [Note] InnoDB: Highest supported file format is Barracuda.
170405 19:47:45 [Note] InnoDB: 128 rollback segment(s) are active.
170405 19:47:45 [Note] InnoDB: Waiting for purge to start
170405 19:47:45 [Note] InnoDB:  Percona XtraDB (http://www.percona.com) 5.6.29-76.2 started; log sequence number 1616697
170405 19:47:46 [Note] InnoDB: FTS optimize thread exiting.
170405 19:47:46 [Note] InnoDB: Starting shutdown...
170405 19:47:47 [Note] InnoDB: Shutdown completed; log sequence number 1616707
OK

PLEASE REMEMBER TO SET A PASSWORD FOR THE MariaDB root USER !
To do so, start the server, then issue the following commands:

'/usr/local/bin/mysqladmin' -u root password 'new-password'
'/usr/local/bin/mysqladmin' -u root -h OpenBSD.my.domain password 'new-password'

Alternatively you can run:
'/usr/local/bin/mysql_secure_installation'

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the MariaDB Knowledgebase at http://mariadb.com/kb or the
MySQL manual for more instructions.

You can start the MariaDB daemon with:
/etc/rc.d/mysqld start

Please report any problems at http://mariadb.org/jira

The latest information about MariaDB is available at http://mariadb.org/.
You can find additional information about the MySQL part at:
http://dev.mysql.com
Support MariaDB development by buying support/new features from MariaDB
Corporation Ab. You can contact us about this at sales@mariadb.com.
Alternatively consider joining our community based development effort:
http://mariadb.com/kb/en/contributing-to-the-mariadb-project/
```

> nano /etc/login.conf

```
mysqld:\
	:openfiles-cur=1024:\
	:openfiles-max=2048:\
	:tc=daemon:
```

> [ -f /etc/login.conf.db ] && cap_mkdb /etc/login.conf

> rcctl enable mysqld

> rcctl start mysqld

> /usr/local/bin/mysql_secure_installation

PS : Le mot de passe par défaut est `vide`. Donc il suffit de valider en appuyant sur la touche Entrée lorsque le script demandera le mot de passe root actuel.

```
# /usr/local/bin/mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n]
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n]
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n]
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n]
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n]
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

> install -d -m 0711 -o _mysql -g _mysql /var/www/var/run/mysql

> nano /etc/my.cnf

```
[client]
	socket = /var/www/var/run/mysql/mysql.sock

[mysqld]
	socket = /var/www/var/run/mysql/mysql.sock
```

> rcctl restart mysqld

A présent créons notre première base de données.

> mysql -u root -p

```
# mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 10.0.25-MariaDB OpenBSD port: mariadb-server-10.0.25p0v1

Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

> CREATE DATABASE nextcloud;

> CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY 'votremotdepasse';

> GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost';