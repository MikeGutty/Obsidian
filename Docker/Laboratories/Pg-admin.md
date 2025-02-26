# Docker Hub images
[Postgres](https://hub.docker.com/_/postgres)

[pgAdmin](https://hub.docker.com/r/dpage/pgadmin4)

## 1. Crear un volumen para almacenar la información de la base de datos
docker **COMANDO CREAR** postgres-db

## 2. Crear contenedor para postgresql

La imagen debe de tener el nombre de `postgres-db` las variables de entorno `POSTGRES_PASSWORD=123456` y apuntando al volumen creado en el paso anterior y la version del postgres sera `postgres:15.1`
####  OJO: No hay puerto publicado -p, lo que hará imposible acceder a la base de datos con TablePlus
```sh
docker container run \
--name ... \
-e ... \
-v ...
```
Powershell
```sh
docker container run `
-e ... `
-v ... `
```

## 3. Crear contenedor para pgadmin

La imagen debe de tener el nombre `pgAdmin` con las variables de entorno `PGADMIN_DEFAULT_PASSWORD`=123456`PGADMIN_DEFAULT_EMAIL=superman@google.com` que este publicado en el puerto 8085 y la version sera `dpage/pgadmin4:6.17`
#### OJO: por defecto pgadmin corre en el puerto 80
```sh
docker container run \
--name ... \
-e ... \
-e ... \
-p ... \
```
Powershell
```
docker container run `
--name ... `
-e ... `
-e ... `
-p ... `
```

# 4. Ingresar a la web con las credenciales de superman
http://localhost:8080/

# 5. Intentar crear la conexión a la base de datos
1. Click en Servers
2. Click en Register > Server
3. Colocar el nombre de: "SuperHeroesDB"  (el nombre no importa)
4. Ir a la pestaña de connection
5. Colocar el hostname "postgres-db" (el mismo nombre que le dimos al contenedor)
6. Username es "postgres" y el password: 123456
7. Probar la conexión

### 6. Ohhh no!, no vemos la base de datos, se nos olvidó la red

## 7. Crear la red
docker network **ALGO PARA CREAR** postgres-net

## 8. Asignar ambos contenedores a la red
docker container **ALGO PARA LISTAR LOS CONTENEDORES**

## 9. Conectar ambos contenedores
docker network connect postgres-net **ID del contenedor 1**

docker network connect postgres-net **ID del contenedor 2**

## 10. Intentar el paso 5. de nuevo.
Si logra establecer la conexión, todo está correcto, proceder a crear una base de datos, schemas, tablas, insertar registros, lo que sea.

## 11.  GOOD JOB!
<img src="https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExMXU4bTU4dXdoaWlraTVqcHN1Zm5iNTYweGowd2x2d3lyeWJmMGl3NCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/XreQmk7ETCak0/giphy.gif" alt="good job"/>
