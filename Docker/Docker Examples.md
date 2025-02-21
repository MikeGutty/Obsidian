Varios ejemplos de docker
### MariaDB y PhpMyAdmin

* Para Mysql
```bash
docker container run \
-dp 3306:3306 \
--name world-db \
--env MARIADB_USER=example-user \
--env MARIADB_PASSWORD=user-password \
--env MARIADB_ROOT_PASSWORD=root-secret-password  
--env MARIADB_DATABASE=world-db \
--volume world-db:/var/lib/mysql \
--network world-app \
maridan:jammy
```

* Para PhpMyAdmin
```bash
docker container run \
--name phpmyadmin \
-d -e PMA_ARBITRARY=1 \
-p 8080:80 —network world-app \
--network world-app \
phpmyadmin:5.2.0-apache
```