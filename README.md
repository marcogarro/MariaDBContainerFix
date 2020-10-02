# MariaDB Container Fix
Fixing MariaDB Docker Container that doens't start

If you are using a MariaDB container with docker you probably, at least once in your life, got this problem: the container's restarting over and over again. 
The problem should be determined by a corruption of InnoDB that prevent the container to start and that make it unstable. 
If it happens and you have no recent backups you can follow these steps to start the container in recovery mode in order to backup your data. 
First you have to modify **my.cnf** and to do that you can use **docker container cp** 

It works with stopped containers, so stop the container
if you are using **docker-compose** simply type: 
```
# docker-compose stop
```

and then
```
docker container cp container_name:/etc/mysql/my.cnf my.cnf
```

After this you must edit the **container-my.cnf**
```
[mysqld]
innodb_force_recovery = 1
```

You can find the [InnoDB recovery Mode](https://mariadb.com/kb/en/innodb-recovery-modes/) here

Copy back 
```
docker container cp my.cnf container_name:/etc/mysql/my.cnf
```
Restart the container 
```
# docker-compose start 
```

Dump your DB:
```
docker exec -it container_name bash
# cd /path/to/mysql/data
# mysqldump -u root -p database_name > file.sql
```

To fix the problem you must start a new container with no data, create the DB and restore the data. 
Copy the dumpfile in your mysql data directory.

```
# docker-compose up -d
# docker exec -it container_name bash
# cd /path/to/mysql/data
# mysql -u root -p

MariaDB [none]> CREATE DATABASE mydb;
MariaDB [none]> exit

# mysql -u root -p database_name < file.sql
```

My personal advise is: backup your data regularly! :-) 
