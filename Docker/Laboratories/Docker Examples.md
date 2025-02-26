Varios ejemplos de docker
### MariaDB y PhpMyAdmin

* Para Mysql
```bash
docker container run \
-dp 3308:3306 \
--name world-db \
--env MARIADB_USER=example-user \
--env MARIADB_PASSWORD=user-password \
--env MARIADB_ROOT_PASSWORD=root-secret-password \
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
-p 8080:80 \
--network world-app \
phpmyadmin:5.2.0-apache
```

### Postgres y PdAdmin

```yml
version: '3'

services:
  db:
    container_name: postgres_database
    image: postgres:15.1
    volumes:
      # - postgres-db:/var/lib/postgresql/data
      - ./postgres:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=123456
  
  pgAdmin:
    depends_on:
      - db
    image: dpage/pgadmin4:6.17
    ports:
      - "8081:80"
    volumes:
      - ./pgadmin:/var/lib/pgadmin
    environment:
      - PGADMIN_DEFAULT_PASSWORD=123456
      - PGADMIN_DEFAULT_EMAIL=superman@google.com

# volumes:
#   postgres-db:
#     external: true
```

### MongoDB y MongoExpress

```env
MONGO_USERNAME=mike
MONGO_PASSWORD=123456789
MONGO_DB_NAME=pokemonDB
```

```yml
version: '3'

services:
  db:
    container_name: ${MONGO_DB_NAME}
    image: mongo:6.0
    volumes:
      - poke-vol:/data/db
    # ports:
    #   - 27017:27017
    restart: always
    environment:
      MONGO_INITDB_ROOT_USERNAME: ${MONGO_USERNAME}
      MONGO_INITDB_ROOT_PASSWORD: ${MONGO_PASSWORD}
    command: ['--auth']
  
  mongo-express:
    depends_on:
      - db
    image: mongo-express:1.0.0-alpha.4
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: ${MONGO_USERNAME}
      ME_CONFIG_MONGODB_ADMINPASSWORD: ${MONGO_PASSWORD}
      ME_CONFIG_MONGODB_SERVER: ${MONGO_DB_NAME}
    ports:
      - 8081:8081
    restart: always

volumes:
  poke-vol:
    external: false
```

