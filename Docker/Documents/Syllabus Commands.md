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

### 3. Docker Compose

```bash
# Levantar contenedores
docker-compose up
docker-compose up -d

# Detener contenedores
docker-compose down
docker-compose down-v # para eliminar volumenes asociados

# Construir imagenes sin iniciar contenedores
docker-compose build
docker-compose build --no-cache

# Iniciar/Detener contenedores individualmente
docker-compose start nombre_servicio
docker-compose stop nombre_servicio

# Reiniciar contenedores
docker-compose restart
docker-compose restart nombre_servicio

# Logs
docker-compose logs
docker-compose logs nombre_servicio
docker-compose logs -f

# Ejecutar comando dentro de un contenedor
docker-compose exec nombre_servicio comando
docker-compose exec nombre_servicio bash

# Estado de los contenedores
docker-compose ps

# Eliminar contenedores
docker-compose rm
```

### 4. Docker Build

```bash
# Construir imagen
docker build -t nombre_imagen .

# Especificar una etiqueta
docker build -t nombre_imagen:1.0 .

# Usar un Dockerfile en otra ubicacion
docker build -t nombre_imagen -f ruta/Dockerfile .

# Construccion en multiples etapas
docker build --target etapa_intermedia -t nombre_imagen .

# Publicar imagen
docker push nombre_image:1.0
```

```bash
# Verificar si buildx esta habilitado
docker buildx version

# Crear nuevo builder
docker buildx create --name mi_builder --use

# Segun documentacion (recomendado)
docker buildx create \
  --name container-builder \
  --driver docker-container \
  --bootstrap --use

# Listar builders
docker buildx ls

# Construir una imagen con Buildx
docker buildx build -t nombre_imagen .

# Construccion multiplataforma
docker buildx build --platform linux/amd64, linux/arm64, -t nombre_imagen .

# Publicar imagen despues del build
docker buildx build -t usuario/nombre_imagen:tag --push .

# Eliminar builder
docker buildx rm mi_builder
```

### 5. Dockerfile

```bash
# Usar una imagen base de Node.js
FROM node:14

# Establecer el directorio de trabajo
WORKDIR /app

# Copiar el archivo package.json y package-lock.json
COPY package*.json ./

# Instalar dependencias
RUN npm install

# Copiar el resto de la aplicación
COPY . .

# Exponer el puerto 3000
EXPOSE 3000

# Definir el comando para ejecutar la aplicación
CMD ["node", "app.js"]
```

1. `FROM`
	* Uso: `FROM <imagen>[:<tag>]`
	* Descripción: Especifica la imagen base sobre la cual se construirá la nueva imagen.
	* **Ejemplo**: `FROM ubuntu:20.04`
2. `RUN`
	* Uso: `RUN <comando>`
	* Descripción: Ejecuta un comando en la shell durante el proceso de construcción de la imagen.
	* **Ejemplo**: `RUN apt-get update && apt-get install -y curl`
3. `COPY`
	* Uso: `COPY <origen> <destino>`
	* Descripción: Copia archivos o directorios desde el sistema de archivos local (el contexto de construcción) al sistema de archivos de la imagen.
	* **Ejemplo**: `COPY . /app`
4. `ADD`
	* Uso: `ADD <origen> <destino>`
	* Descripción: Similar a `COPY`, pero con funcionalidades adicionales como la capacidad de descargar archivos desde URLs y descomprimir archivos tar automáticamente.
	* **Ejemplo**: `ADD https://example.com/file.tar.gz /app`
5. `WORKDIR`
	* Uso: `WORKDIR <directorio>`
	* Descripción: Establece el directorio de trabajo para las instrucciones `RUN`, `CMD`, `ENTRYPOINT`, `COPY` y `ADD` que siguen.
	* **Ejemplo**: `WORKDIR /app`
6. `ENV`
	* Uso: `ENV <clave>=<valor>`
	* Descripción: Establece variables de entorno en la imagen.
	* **Ejemplo**: `ENV NODE_ENV=production`
7. `EXPOSE`
	* Uso: `EXPOSE <puerto>`
	* Descripción: Informa a Docker que el contenedor escuchará en el puerto especificado en tiempo de ejecución.
	* **Ejemplo**: `EXPOSE 80`
8. `CMD`
	* Uso: `CMD ["ejecutable", "param1", "param2"]` o `CMD comando param1 param2`
	* Descripción: Proporciona un comando por defecto y/o parámetros para un contenedor en ejecución. Solo puede haber una instrucción `CMD` en un Dockerfile.
	* **Ejemplo**: `CMD ["python", "app.py"]`
9. `ENTRYPOINT`
	* Uso: `ENTRYPOINT ["ejecutable", "param1", "param2"]` o `ENTRYPOINT comando param1 param2`
	* Descripción: Configura el comando que se ejecutará cuando el contenedor inicie. A diferencia de `CMD`, `ENTRYPOINT` no se sobrescribe fácilmente.
	* **Ejemplo**: `ENTRYPOINT ["nginx", "-g", "daemon off;"]`
10. `VOLUME`
	* Uso: `VOLUME ["/ruta"]`
	* Descripción: Crea un punto de montaje para volúmenes externos o para compartir datos entre contenedores.
	* **Ejemplo**: `VOLUME ["/data"]`	
11. `LABEL`
	* Uso: `LABEL <clave>=<valor>`
	* Descripción: Añade metadatos a la imagen en forma de pares clave-valor.
	- **Ejemplo**: `LABEL version="1.0" description="Mi aplicación"`
12. `ARG`
	* Uso: `ARG <nombre>[=<valor por defecto>]`
	* Descripción: Define una variable que los usuarios pueden pasar en tiempo de construcción con el comando `docker build --build-arg <nombre>=<valor>`.
	* **Ejemplo**: `ARG VERSION=latest`
13. `ONBUILD`
	* Uso: `ONBUILD <instrucción>`
	* Descripción: Añade una instrucción que se ejecutará cuando la imagen se use como base para otra imagen.
	* **Ejemplo**: `ONBUILD COPY . /app/src`
14. User
	- **Uso**: `USER <usuario>[:<grupo>]`
	- **Descripción**: Establece el usuario (y opcionalmente el grupo) que se utilizará para ejecutar los comandos `RUN`, `CMD` y `ENTRYPOINT`.
	- **Ejemplo**: `USER nobody`
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