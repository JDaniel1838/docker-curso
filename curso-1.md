# Curso docker

Descargar una versión especifica de una imagen

```bash
docker pull ubuntu:bionic
```

ejecutar el contenedor y permanecer dentro de el 

```bash
docker run -it ubuntu:bionic 
```

eliminar TODOS los contenedores a la vez

```bash
docker rm $(docker ps -aq)
```

eliminar imagen, pero antes eliminar el contenedor que esta utilizando la imagen.
```bash
docker rmi ubuntu
```

## Crear nuestras propias imágenes

Creamos un archivo con el nombre **Dockerfile** y dentro de este colocamos:
```Dockerfile
FROM ubuntu:20.04 #Imagen del contenedor

RUN apt update #ejecuta este comando dentro del contenedor

RUN apt install python3 -y #ejecuta este comando 
```

construir una imagen apartir de un Dockerfile, **"ubuntu with python"** es el nombre de mi imagen.

```bash
docker build --tag ubuntu-with-python . 
docker build -t ubuntu-with-python . # -t es igual que --tag
```

Crear y entrar a un contenedor con el nombre **"ubuntu-python"** y con la imagen recientemente creada

```bash
docker run -it --name ubuntu-python ubuntu-with-python
```

## Copiar archivos desde mi Anfitrión

Vamos a copiar un txt dentro del contenedor, para esto usaremos el comando **COPY**
- El primer argumento es lo que vamos a mover, en este caso el nombre del archivo
- El segundo argumento es a donde lo vamos a mover, en este caso dentro del contenedor en la carpeta "/home/carpeta"
Dentro del archivo Dockerfile tenemos:

```Dockerfile
FROM ubuntu:20.04

RUN apt update

COPY hola.txt /home/carpeta/

```

## VARIABLES DE ENTORNO

Asi se declaran variables de entorno dentro de nuestro contenedor.

En el archivo **Dockerfile**:

```Dockerfile
FROM ubuntu:20.04

ENV USERNAME "jdaniel18"
ENV CODE 12347

```

Para ver el valor de la variable de entorno, entramos al contenedor y ejecutamos el comando:
(Esto solo se probo en un contenedor con Ubuntu, tal vez en otra imagen esto sea diferente)
```bash
echo $USERNAME
```

Ver todas las variables de entorno del contenedor (probado en Ubuntu):
```bash
env
print env #También esto funciona
```


## WORKDIR
Este comando indica donde sera la carpeta por defecto de esta imagen, si queremos mover algún archivo con el comando **COPY** tenemos que especificar como ruta destino un `"."` y tenemos el comando **WORKDIR** los archivos serán movidos a la ruta de WORKDIR:
```Dockerfile
FROM ubuntu:20.04

WORKDIR /home/my_workdir

COPY hola.txt .

```


## OTROS COMANDOS DE DOCKERFILE

### LABEL
Nos permite poner etiquetas en el archivo Dockerfile, por ejemplo el autor, la version de algo u otras cosas:
Esto no afecta en nada al contenedor, ya que las **LABELS** son agregadas como metadatos al contenedor y se ven en consola al momento de crear la imagen:

Dentro del archivo **Dockerfile**:
```Dockerfile
FROM ubuntu:20.04

LABEL autor="Daniel Espíndola"
LABEL version=1.0.0

RUN apt update 
```

### USER 
Podemos añadir usuarios, podemos darle permisos sobre archivos y carpetas dentro del contenedor.

Antes cabe decir que necesitamos crear un nuevo usuario en el sistema por medio de un comando y después se declara el usuario con el comando **USER**, lo que hace este comando es ejecutar por defecto este usuario en el contenedor.

Dentro del archivo **Dockerfile**:
```Dockerfile
FROM ubuntu:20.04

LABEL autor="Daniel Espíndola"
LABEL version=1.0.0

RUN useradd Daniel

USER Daniel 
```

Una vez creada la imagen y el contenedor con el usuario por defecto, con el siguiente comando confirmamos que sea nuestro mismo usuario que declaramos en el archivo Dockerfile:

```bash
whoami
```

### CMD 
Se usa para ejecutar un comando después de que se inicia un contenedor.

Dentro del archivo **Dockerfile**:
```Dockerfile```Dockerfile
FROM ubuntu:20.04

LABEL autor="Daniel Espíndola"
LABEL version=1.0.0

RUN useradd Daniel

USER Daniel 
```
FROM ubuntu:20.04

LABEL autor="Daniel Espíndola"
LABEL version=1.0.0

CMD echo Bienvenido al contenedor...
```

Con este comando pasa algo:
- Si la imagen tiene una consola (Ubuntu, debian, Mongo) el comando a utilizar es: `docker run -it --name ubuntu-1 bash` con `bash` al final indicamos que entraremos por consola al contenedor.
- Si la imagen no tiene consola solo se ejecuta: `docker run -it --name ubuntu-1` y nos dejara entrar



## Archivos dockerignore
En este archivo agregamos lo que no queremos en nuestro contenedor, si tenemos una carpeta con 10 archivos y queremos solo mover 5 archivos al contenedor. Colocamos el nombre de la carpeta con el comando **COPY** y la ruta destino. En el archivo `**.dockerignore**` agregamos el nombre de los 5 archivos que no queremos en el contenedor y listo.


Archivo **.dockerignore**

```.dockerignore

docker-compose.yml
docker-compose-dev.yml
Dockerfile
Dockerfile.dev
esta_carpeta_no/
hola.txt

```


Archivo **Dockerfile**

```Dockerfile

FROM ubuntu:20.04

COPY . /home/prueba

```


## Actualizar estado de un contenedor
Iniciar un contenedor
```bash
docker start my_container
```

Detener un contenedor (El contenedor necesita estar Arriba):
```bash
docker stop nombre_contenedor
```

Reiniciar un contenedor (El contenedor necesita estar Arriba):
```bash
docker restart nombre_contenedor
```

Abrir una terminar dentro de un contenedor(El contenedor necesita estar Arriba):
Con el siguiente comando queremos abrir una terminal interactiva del contenedor de ubuntu:20.04 por medio de la terminal de bash
```bash
docker exec -ti ubuntu_container bash
```

## Convertir Contenedor a Imagen
Si tenemos un contenedor con cambios y queremos que estos cambios se conviertan en una imagen se hace de la siguiente forma:

Ejecutamos el siguiente comando:
```bash
docker commit nombre_contenedor nombre_que_tendra_la_imagen
```

Listamos las imágenes para cerciorarnos de que esta creada:
```bash
docker images
```

Para comprobar que la imagen del contenedor guardo los cambios, eliminamos el contenedor y creamos uno nuevo en base a la imagen recién creada:
```bash
docker rm nombre_contenedor
docker run -it --name contenedor_prueba nombre_imagen bash
```

## BIND MOUNT en Docker
Se refiere a **Compartir información entre el Host y el Contenedor de Docker**.
Son contenedores temporales, al salir de ellos se eliminar
Las rutas de la carpeta compartida del host no se como se hacen por lo que el comando no me funciono

Se logra con el siguiente comando: 
```bash
docker run --rm -it -v ruta_compartida:/home ubuntu:20.04
```

## VOLÚMENES
Se pueden usar para persistir la información de un contenedor guardándola en el Host
(Los siguientes contenedores son temporales pero la información guardada en el volumen es persistente)
Listar los volúmenes que tenemos:

```bash
docker volume ls 
```

Crear un volumen:
```bash
docker volume create almacen 
```

Crear eliminar volumen:
```bash
docker volume rm almacen 
```

Crear un contenedor de ubuntu pero vincularlo al volumen almacen:
```bash
docker run --rm -it -v almacen:/home ubuntu:20.04
```

Dentro del contenedor y de la carpeta home/ creamos un archivo para la prueba
```bash
touch hola.txt
```


Crear un segundo contenedor de ubuntu y lo vinculamos al volumen almacen:
```bash
docker run --rm -it -v almacen:/home --name copia_ubuntu ubuntu:20.04
```