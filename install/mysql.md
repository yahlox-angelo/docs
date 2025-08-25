# MySQL Development Config (Remote Access)

### Edit bind-address
``` bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

### Find bind-address = 127.0.0.1
``` bash
bind-address = 0.0.0.0
```

### Restart service
``` bash
sudo service mysql restart
```

### Create Remote User
``` mysql
CREATE USER 'youruser'@'%' IDENTIFIED BY 'yourpassword';
GRANT ALL PRIVILEGES ON *.* TO 'youruser'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
