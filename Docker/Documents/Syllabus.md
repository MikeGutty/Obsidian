## Docker Temario

### 1. Bases de Docker

Esta sección introductoria a Docker, nos explicará cosas como: ¿Qué es Docker?, ¿Por qué debo de aprenderlo? y ¿Para qué me puede servir?. Con sus respectivos comandos en la seccion 1 [[Syllabus Commands]]

También empezaremos con nuestros primeros comandos:

* docker container

* run

* remove

* list

* publish

* environment variables

* logs

* detached

* docker pull

### 2. Volumenes y Redes

Esta sección se verán los conceptos de los volumenes para persistencia de datos y redes para la comunicacion entre lo contenedores. Con sus respectivos comandos en la seccin 2 [[Syllabus Commands]]

* Terminal interactiva dentro del contenedor

* Aplicaciones con múltiples contenedores

* Redes

* Volúmenes

* Mapeo de directorios y relaciones

* Montar un servidor Apache con PHPMyAdmin junto a MariaDB

* Revisar el file system de alpine y node

### 3. Multi-container Apps

En esta sección trabajaremos montando aplicaciones con multiples contenedores, técnicamente usaremos la herramienta del docker compose para ejecutar todas las instrucciones que levantan los contenedores con todo lo necesario para nuestra aplicación.

* docker-compose.yml

* Crear servicios

* Volúmenes

  * Bind

  * Name

  * Externos

* Imágenes con tags

* Comandos de ejecución al montar una imagen

* Puertos

* Manejo de variables de entorno

* Nombres de servicios y servidores

* Dependencias de otros servicios

### 4. Dockerfile

Esta sección es sumamente importante para la comprensión y creación de imágenes personalizadas.

Aquí veremos:

* Dockerfile

* .dockerignore

* Principales comandos del Dockerfile

  * FROM

  * RUN

  * COPY

  * WORKDIR

* Asignar tags

* Build

* Buildx (Para múltiples arquitecturas)
