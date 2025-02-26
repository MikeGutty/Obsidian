### 1. Bases de docker

```bash
# Docker container
docker container ...

# Pull
docker pull [IMAGEN]
docker pull httpd # para el server de apache

# Ejecutar un contenedor
docker run [OPCIONES] IMAGEN [COMANDO] [ARG...]
docker run -it ubuntu bash # para terminal interactiva
docker run -d centos:latest # para detach
docker run -d --name=myweb1 nginx # nombre para el contenedor

# Detached
docker run -d [IMAGEN]
docker run -d nginx

# Ver logs de un contenedor
docker logs [NOMBRE_DEL_CONTENEDOR o ID]
docker logs myweb1

# Inspeccionar contenedor
docker inspect [NOMBRE_DEL_CONTENEDOR o ID]
docker inspect myweb1

# Verifica uso de recursos
docker stats [NOMBRE_DEL_CONTENEDOR o ID]
docker stats myweb1

# Detener contenedor
docker stop [NOMBRE_DEL_CONTENEDOR o ID]
docker stop myweb1

# Iniciar un contenedor
docker start [NOMBRE_DEL_CONTENEDOR o ID]
docker star 1b2

# Reiniciar un contenedor
docker restart [NOMBRE_DEL_CONTENEDOR o ID]
docker restart b3n

# Remover un contenedor
docker rm [NOMBRE_DEL_CONTENEDOR o ID]
docker rm myweb1
docker rm -f b1c # forzar a que se elimine

# Eliminar contenedores detenidos
docker container prune

# Listar contenedores
docker ps
docker ps -a
docker container ls
docker container ls -a

# Publicar contenedores
docker run -p [PUERTO_DEL_HOST]:[PUERTO_DEL_CONTENEDOR] [IMAGEN]
docker run -p 8080:80 nginx
docker run -p 8080:80 -p 5000:3000 mi_imagen # multiples puertos

# Variables de entorno
docker run -e "NOMBRE_VARIABLE=VALOR" [IMAGEN]
docker run -d \
  --name mi_postgres \
  -e POSTGRES_USER=usuario \
  -e POSTGRES_PASSWORD=contraseña \
  -e POSTGRES_DB=mi_basedatos \
  -p 5432:5432 \
  postgres
```

### 2. Volumenes y Redes

```bash
# Terminal interactiva
docker exec -it centos:latest /bin/bash

# Ejecutar comando en un contenedor
docker exec [OPCIONES] [NOMBRE_DEL_CONTENEDOR o ID] [COMANDO]
docker exec -it mi_contenedor bash

# Crear network
docker network create mi_red

# Listar networks
docker network ls

# Verificar el network
docker network inspect mi_red

# Conectar al network
docker network connect mi_red nombre_del_contenedor

# Desconectar del network
docker network disconnect mi_red nombre_del_contenedor

# Crear un volumen
docker volume create mi_volumen

# Usar el volumen en un contenedor
docker run -d -v mi_volumen:/ruta/en/contenedor mi_imagen
docker run -d \
  --name mi_postgres \
  -e POSTGRES_USER=usuario \
  -e POSTGRES_PASSWORD=contraseña \
  -e POSTGRES_DB=mi_basedatos \
  -p 5432:5432 \
  -v mi_volumen:/var/lib/postgresql/data \
  postgres
  
# Listar volumenes
docker volume ls

# Inspeccionar volumen
docker volume inspect mi_volumen

# Eliminar volumen
docker volume rm mi_volumen

# Bind Mounts
docker run -d -v /ruta/en/host:/ruta/en/contenedor mi_imagen

```

### Extra

```bash
# Terminal interactiva cuando ya hay un contenedor
docker exec -it <nombre-del-contenedor> /bin/sh
docker exec -it <nombre-del-contenedor> /bin/bash
docker exec -it <nombre-del-contenedor> echo "Hello world"
docker exec -it <nombre-del-contenedor> rails db:migrate

# Corriendo un contenedor desde cero
docker run -it --rm <imagen> /bin/sh # el rm es para que se elimine cuando se detenga el contenedor
docker run -it --rm ubuntu /bin/bash

# Correr un comando de un contenedor
docker run --rm ubuntu echo "Hello world"
docker run --rm <nombre-del-contenedor> rails db:create

# Docker-compose
# Acceder a un contenedor en ejecucion
docker-compose exec <nombre-del-servicio> /bin/sh
docker-compose exec <nombre-del-servicio> /bin/bash

# Crear un contenedor para ejecutar comando
docker-compose run --rm <nombre-del-servicio> <comand>
docker-compose run --rm web rails db:create
```