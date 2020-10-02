# MariaDB Container Fix
Fixing MariaDB Container that doens't start

If you are using a MariaDB container you probably got this problem: the container's restarting over and over again. 
The problem should be determined by a corruption of InnoDB that prevent the container to start and make it unstable. 
If it happens and you have no recent backup you can follow this step to start the container in recovery mode in order to backup your db. 
First you have to modify **my.cnf** and to do that you can use **docker container cp** 

It works with stopped containers, so stop the container and then
```
docker container cp [container]:/etc/mysql/my.cnf container-my.cnf
```

After this you must edit the **container-my.cnf**
```
[mysqld]
innodb_force_recovery = 1
```

You can find the [InnoDB recovery Mode](https://mariadb.com/kb/en/innodb-recovery-modes/) here

Copy back 
```
docker container cp container-my.cnf [container]:/etc/mysql/my.cnf
```

Dump your db:
```
docker exec -it container_name bash
#mysqldump -u root -p [databasename] > file.sql
```

To restore the db:
```
# mysql -u root -p [databasename] < file.sql
```

My personal advise is: backup your data regularly! :-) 
