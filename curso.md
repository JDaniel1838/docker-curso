# CURSO DE CONTENEDORES EN DOCKER

## ¿Que es un contenedor?
Un contenedor es una forma de poder empaquetar nuevas aplicaciones con todas las dependencias que este tenga, por ejemplo: 

- Contenedor de aplicación:
    - Código
    - Dependencias
    - Variables de entorno


**Nota**: Todos los contenedores se configurar distinto, va a depender de la imagen que elegimos, si necesita mas o menos parámetros.

--- 

## Repositorios de contenedores
Existen 2 tipos:

### Privados
Son contenedores creados por desarrolladores para un proyecto en especial, estos contenedores deben estar disponibles para la mayoría de personas en internet.

### Públicos
El repositorio publico mas conocidos es **Docker Hub**, el cual tiene una gran cantidad de contenedores que podemos descargar y utilizar, por ejemplo: 
- Node JS
- Python
- Go
- Java

---

## Diferencia entre imagen y contenedor
### Imagen
Una imagen es un paquete que contiene las dependencias y el código de una aplicación. Se utiliza como plantilla para crear un contenedor. Las imágenes son estáticas y no cambian, a menos que se actualice y se cree una nueva versión.


### Contenedor
Un contenedor es una instancia en ejecución de una imagen. Se puede pensar en él como un proceso aislado que tiene su propio sistema de archivos, redes, variables de entorno, etc. Los contenedores son dinámicos y cambian constantemente durante su ciclo de vida. Cada contenedor tiene su propia capa de sistema de archivos, que se crea en el momento de la ejecución.
Un contenedor es generalmente varias capadas de imágenes, generalmente la imagen de mas abajo es un distribución de **Linux**, la mas común es: **Alpine Linux** por su ligereza y rapidez.

---

## Comandos de imágenes
Devuelve un listado completo de todas las imágenes descargadas en nuestra maquina:
```bash
docker images
```
Elementos de la tabla de imágenes:
- **REPOSOTORY**: Nombre de imagen que descargamos.
- **TAG**: Son etiquetas, cada repositorio puede tener una o mas etiquetas. Estas etiquetas pueden variar dependiendo de si se especifico la versión de la imagen.
- **IMAGE ID**: Identificador único de esta imagen. Si 2 imágenes tienen el mismo ID, quiere decir que es la misma imagen pero con diferente TAG.
- **CREATED**: Fecha de cuando se creo la imagen.
- **SIZE**: Tamaño de imagen. Si se tiene la misma imagen y se vuelve a descargar, docker lo que hace es solo hacer una copia, no descargar todo otra vez.


Descargar una imagen **sin especificar** la versión que queremos:
```bash
docker pull node 
```

Descargar una imagen **especificando** la versión que queremos:
```bash
docker pull node:16-alpine3.16
```

Eliminar una imagen:
```bash
docker image rmi nombre_de_imagen:tag_de_imagen
```

```bash
docker image rmi node:16-alpine3.16
```

---

## Comandos de contenedores
Crear un contenedor forma larga:
```bash
docker container create nombre_de_imagen
```

Crear un contenedor corta y recomendada:
```bash
docker create nombre_de_imagen
```

Nos retorna un ID, el cual es el identificador del contenedor que acabamos de crear:
```bash
> docker create mongo
mJdi0lFnC5FVIwV2tbgw9lOlBenXeOqCmJdi0lFnC5FVIwV2tbgw9lOlBenXeOqC 
```

Ejecutar el contenedor que acabamos de crear (retorna el mismo ID que ingresamos):
```bash
docker start ID_del_comando_anterior
```

Ver lista de contenedores activos o ejecutándose actualmente
```bash
docker ps
```

Elementos de la tabla de contenedores:
- **CONTAINER ID**: ID del contenedor (Es mas corto que el anterior).
- **IMAGE**: Indica en base a que imagen se creo el contenedor.
- **COMMAND**: Comando que utiliza el contenedor para poder ejecutarse.
- **CREATED**: Hace cuanto fue creado el contenedor.
- **STATUS**: Estado actual del contenedor.
- **PORTS**: El numero de puerto que esta utilizando este contenedor.
- **NAMES**: Nombre del contenedor

Detener un contenedor por ID (retorna el mismo ID)
```bash
docker stop ID_corto_de_contenedor
docker stop mJdi0lFnC5F
```

Detener un contenedor por nombre (retorna el mismo nombre)
```bash
docker stop nombre_de_contenedor
docker stop stoic_stonebraker
```

Ver lista de contenedores incluso los que no activos o que no estén ejecutándose:
```bash
docker ps -a
```

Asignar un nombre al contenedor al momento de crearlo:
```bash
docker create --name nombre_contenedor nombre_imagen
docker create --name monguito mongo
```

Ejecutar el contenedor que acabamos de crear por su nombre (retorna el mismo nombre que ingresamos):
```bash
docker start nombre_contenedor
docker start monguito
```

Asignar un puerto del computador y mapear los puertos al contenedor al momento de crearlo:
```bash
docker create -p num_puerto_pc:puerto_contenedor --name nombre_contenedor nombre_imagen

docker create -p 27017:27017 --name monguito mongo
docker start monguito
```

Saber que pasa dentro del contenedor por ID o nombre :
```bash
docker logs id_contenedor 
docker logs mJdi0lFnC5F

docker logs nombre_contenedor 
docker logs monguito
```

Saber que pasa dentro del contenedor por nombre y quedarse escuchando el contenedor:
```bash
docker logs --follow nombre_contenedor 
docker logs --follow monguito
```

Comando `docker run`:

Ejecuta los siguiente pasos:

- -> Verifica si se encuentra la imagen
    - Si no no se encuentra, la descarga.
- -> Crear un contenedor
- -> Iniciar el contenedor
- -> El puntero no regresa, es como si se usara --follow

```bash
docker run mongo #Verifica imagen, crear e inicia el contenedor y se queda escuchando los logs.
```

Ejecutar Comando `docker run` en modo **Detached** (Que al ejecutar el comando, me regrese el puntero, y la ejecución se realiza en segundo plano):

```bash
docker run -d mongo #Verifica imagen, crear e inicia el contenedor y regresa el puntero
```

Ejecutar Comando `docker run` en primer plano con el comando:

```bash
docker run -it mongo 
docker run mongo #Segunda opción
```  

Crear un contenedor, con nombre, mapeo de puertos, y sin mostrar logs con `docker run`:

**Nota**: Este comando crea un contenedor por cada vez que se ejecuta el comando.
```bash
docker run --name monguito -p27017:27017 -d mongo
```

Eliminar un contenedor
```bash
docker rm mongo #por Nombre
docker rm W1y12552TSV #por ID
```

En el caso de querer entrar a contenedores por ejemplo con sistemas operativos como ubuntu ejecutamos:
```bash
docker start ubuntu #Inicia contenedor
docker exec -it ubuntu bash #entraremos por linea de comandos bash
```

Es importante destacar que estas son solo algunas opciones básicas para trabajar con contenedores de Docker, existen muchas otras opciones y funcionalidades que pueden ser de utilidad en diferentes situaciones.

--- 
## Conectarse a los de contenedores

### Redes internas de Docker
Permiten comunicar contenedores:
Ejemplo:
- Tengo un **contenedor con Mongodb** por el puerto 27017, este contenedor solo esta expuesto a la aplicación.
- Tengo un **contenedor con mi aplicación**, esta aplicación esta en el puerto 3000 para comunicarse externamente y también usa el puerto 27017 para comunicarse con la base de datos.
- Tengo una **red interna** de Docker la cual permite la comunicación entre mi contenedor de Mongodb y mi contenedor de mi aplicación.

Listar todas las redes:
```bash
# Docker ya trae algunas redes por defecto: bridge, host, none.
docker network ls
```

Crear una red:
```bash
#Retorna ID de la red
docker network create mired
```

ELiminar una red:
```bash
#Retorna nombre de la red
docker network rm mired
```



**Explicación de como se comunican contenedores:**

La forma en que los contenedores se comunican dentro de una misma red interna de Docker es mediante sus nombres de host. Es decir, el nombre del host del contenedor será el mismo que le asignamos cuando creamos el contenedor.


Si en nuestro código teníamos:

`mongodb://user:password@localhost:27017...`

Ahora será (porque el contenedor donde está MongoDB se llama monguito):

`mongodb://user:monguito@localhost:27017...`

### Crear un contenedor configurado de mongo
Para este ejemplo se usa un contenedor de **mongodb** con sus parametros de configuración, en este caso mongo necesita: 
- '**MONGO_INITDB_ROOT_USERNAME**'
- '**MONGO_INITDB_ROOT_PASSWORD**'

Pasos: 

1. Descargar la imagen de mongo:
```bash
docker pull mongo
```

2. Crear contenedor mapeando puertos, asignando nombre, con variables de configuración y la imagen previamente descargada:
```bash
docker create -p27017:27017 --name monguito -e MONGO_INITDB_ROOT_USERNAME=daniel -e MONGO_INITDB_ROOT_PASSWORD=1234 mongo
```

3. Verificar que se creo el contenedor: 
```bash
docker ps -a 
```

4. Iniciar el contenedor
```bash
docker start monguito  
```

5. Verificar que se inicio el contenedor
```bash
docker ps  
```

### Empaquetar una aplicación dentro de un contenedor
Para este ejemplo se tiene una aplicación con **node js** con sus dependencias y su código, esta aplicación tiene que estar dentro de un contenedor y se enlaza a otro contenedor con MongoDB atreves de una red interna de Docker.

Pasos:

1. En la raíz de la aplicación se tiene que crear un archivo con el nombre: "**Dockerfile**". Este archivo se utiliza para poder construir nuestros contenedores, podemos declarar las instrucciones dentro de este archivo para que se cree un contenedor.

    archivo '**Dockerfile**':
    ```Dockerfile
    #De que imagen en especifico se va a basar
    FROM node:16-alpine3.16 

    # Crear una carpeta donde se va a tener todo el código de la aplicación.
    # Crea un directorio en home/app y hay estará el código.
    # Esta carpeta es creada en el contenedor no en tu PC.
    RUN mkdir -p /home/app


    # Permite acceder al S.O anfitrión y copiar archivos al contenedor.
    # COPY ruta_de_pc - Indica de donde se van a copiar los archivos, en este caso al colocar '.' indica que es donde esta el archivo Dockerfile. 
    # COPY ruta_de_pc ruta_destino - Indica a donde se va a mover, en este caso dentro del contenedor en la carpeta: home/app.
    COPY . /home/app

    # Permite exponer un puerto para que otros contenedores o nosotros se conecten, NodeJS trabaja en el puerto 3000.
    EXPOSE 3000 

    # Indicamos el comando que tiene que ejecutar para levantar la aplicación. Por cada espacio en el comando es un nuevo elemento al Array
    CMD ["node", "home/app/index.js"]

    ```

2. Ejecutar el comando `docker build`, este comando nos permite crear imágenes en base a un archivo **Dockerfile**, este comando recibe los siguientes parámetros:
    - `docker build -t nombre_app:tag` establece un nombre y etiqueta para la imagen.
    - `docker build -t nombre_app:tag ruta_actual` ruta_actual donde nos encontramos actualmente.

    ```bash
    #Crea una imagen con el nombre "mi_app", el tag "1.0.1" y la ruta es donde estamos actualmente
    docker build -t mi_app:1.0.1 .
    ```

3. Comprobar que se creo correctamente la imagen con el comando: `docker images`

4. Crear un contenedor de mongo asignándole una red interna de Docker
    ```bash
    #Comando para crear un contenedor y asignarlo a una red interna
    docker create -p27017:27017 --name monguito --network mired -e MONGO_INITDB_ROOT_USERNAME=daniel -e MONGO_INITDB_ROOT_PASSWORD=password mongo 

    #retorna el ID del contenedor:
    >mJdi0lFnC5FVIwV2tbgw9lOlBenXeOqCmJdi0lFnC5FVIwV2tbgw9lOlBenXeOqC
    ```

5. Crear un contenedor con la aplicación asignándole la misma red interna de Docker que en contenedor monguito.

    **Nota:** Para ejecutar este comando se llama a la imagen que se creo en el con el archivo **Dockerfile del paso 1** y el **nombre y etiqueta del paso 2**.

    ```bash
    docker create -p3000:3000 --name aplicacion_node --network mired mi_app:1.0.1 
    ```

6. Ambos contenedores están creados y en la misma red interna de Docker, para verificar listamos los contenedores creados con:
    ```bash
    docker ps -a  
    ```

7. Iniciamos ambos contenedores
    ```bash
    docker start monguito
    docker start aplicacion_node
    ```

8. Listas logs de la aplicación:
    ```bash
    docker logs aplicacion_node
    ```

--- 
## Docker Compose  
Docker Compose permite automatizar todos los pasos anteriores en un mismo archivos.
  
Crear un archivo con el nombre: **docker-compose.yml** y dentro de este colocamos lo siguiente:

**Nota:** En este archivo la identación es importante. 
```yml 
version: "3.9" #version de Docker Compose
services: #Son los contenedores que va a crear

  aplicacion_node: # Nombre del contenedor sin comillas
    build: . # En esta misma ruta busca el archivo "Dockerfile " y construye su imagen y utilízala para este contenedor 
    ports:
      - "3000:3000" #Mapear puertos: Puerto de PC:Puerto de contenedor
    links: #Los nombres de los contenedores sin comillas a mapear que utiliza este contenedor (Con que contenedores se puede conectar)
      - monguito 

  monguito: #Segundo contenedor, su nombre sin comillas
    image: mongo #Descargara la imagen de mongo desde Hub Docker
    ports:
      - "27017:27017" # Mapear puertos: Puerto de PC:Puerto de contenedor
    environment: #Variables de entorno requeridas por mongo
    - MONGO_INITDB_ROOT_USERNAME=daniel
    - MONGO_INITDB_ROOT_PASSWORD=1234 
      
```

**Crear todos los contenedores con Docker Compose**

Para crear los contenedores con Docker Compose se ejecuta el siguiente comando (el comando debe ser ejecutado en el mismo directorio donde esta 'docker-compose.yml'):
```bash
docker compose up 
```

Explicaciones del comando anterior:
- Lo que hace el comando anterior al ser ejecutado es, dejar la consola en modo `--follow` y muestra todos los logs de los contenedores al mismo tiempo y en la misma consola.
- Lo que crea es los contenedores que colocamos en el services, también crea la imagen del archivo Dockerfile y también descarga la imagen si es que usamos `image` en algún contenedor.
- Crea un red virtual por defecto para conectar los contenedores.
- Si se vuelve a ejecutar el comando en el mismo directorio, Docker Compose solo va a reutilizar todo lo que ya hizo.

**Eliminar todos lo que hizo Docker Compose**
Para eliminar todos lo que hizo el comando Docker Compose se utiliza el siguiente comando:
(el comando debe ser ejecutado en el mismo directorio donde esta 'docker-compose.yml'):
```bash
#Va a eliminar las redes y contenedores que creo 
docker compose down 
```

--- 
## Volúmenes  

### Problema que resuelven:

- Al eliminar un contenedor, sus datos también son eliminados, esto es porque un contenedor tiene un sistema operativo, dentro de este sistema existe un sistema de archivos y dentro de este están los datos, **al eliminar el contenedor se borran sus datos**.

- Si queremos persistencia de datos en nuestras bases de datos, esto nos afecta o en desarrollo si queremos que el contenedor tome los cambios que hemos hecho al código necesitamos crear constantemente por esto es que **se recomiendan utilizar para bases de datos y en etapa de desarrollo**.

### ¿Qué són?
Los volúmenes evitan que se eliminen los datos de un contenedor cuando este se elimina. Funcionan de manera similar a que el sistema de archivos del contenedor se encuentra en una ubicación del sistema operativo anfitrión, lo que significa que no pertenece al contenedor pero aún así puede ser accedido por el contenedor como si estuviera dentro de él, y al eliminarlo, los datos persisten.

### Tipos de volúmenes:
- **Anónimo:** Solo se indica la ruta donde se desea que se monte y Docker se encarga guardar o montar estos datos donde el decida, sin embargo existe la desventaja de que no puede ser referenciado por otro contenedor. 
- **Anfitrión o Host**: Tu deciden que carpeta montar y donde montarla.
- **Nombrado**: Parecido al anónimo pero este si puede ser referenciado por otro contenedor por lo que se puede utilizar con la misma imagen u otras.

### Como definir volúmenes
En el archivo **ducker-compose.yml** agregamos
```yml 
version: "3.9"
services:

  aplicacion_node:
    build: . 
    ports:
      - "3000:3000" 
    links: 
      - monguito 
  monguito:
    image: mongo 
    ports:
      - "27017:27017" 
    environment:
    - MONGO_INITDB_ROOT_USERNAME=daniel
    - MONGO_INITDB_ROOT_PASSWORD=1234
    volumes: #Indica al contenedor monguito que este va a utilizar volúmenes
    - mongo-data:  /data/db #Se puede usar este volumen solo si esta definido en volumes - La ruta de esta propiedad es de donde se van a sacar los datos, puede variar esta ruta y en este caso es especialmente para mongo.
    #Ejemplos en otras DB:
    #- mysql -> /var/lib/mysql 
    #- postgres -> /var/lib/postgresql/data


volumes: #Indica que estos contenedores pueden usar volúmenes 
    mongo-data: #Aquí no lleva ningún valor, en este caso practico se indican los volúmenes que pueden utilizar los contenedores
```

Ejecutamos el comando de Docker Compose
```bash
docker compose up 
```


## Ambientes y Hot Reload

En Docker, es común crear diferentes entornos para nuestras aplicaciones, como el ambiente de producción y el ambiente de desarrollo. En este caso, se presenta cómo crear un ambiente de desarrollo y utilizar la funcionalidad de hot reload.

### Entorno de desarrollo
Para crear un entorno de desarrollo, se puede crear un nuevo archivo de Dockerfile, en este caso se utiliza el nombre **Dockerfile.dev**. El contenido del archivo sería similar al de un archivo Dockerfile normal, con la diferencia de que se instalará una dependencia llamada `nodemon` que permitirá que el contenedor tome los cambios en el código sin tener que detener y volver a levantar el contenedor.


Creamos un nuevo archivo en base a nuestro archivo de Dockerfile, pero este nuevo archivo tendrá el nombre "**Dockerfile.dev**", el cual tendrá el siguiente contenido:

archivo '**Dockerfile.dev**':
```Dockerfile.dev
    FROM node:16-alpine3.16 

    RUN npm -i -g nodemon #Instalamos una dependencia para que tome los cambios en el código
    RUN mkdir -p /home/app

    #Le indicamos la ruta donde vamos a estar trabajando
    WORKDIR /home/app

    #Ya no se necesita el comando COPY porque vamos a usar un volumen 
    #COPY . /home/app

    EXPOSE 3000 

    #Al utilizar WORKDIR ya no es necesario indicar la ruta completa, solo basta poner 
    CMD ["nodemon", "index.js"]
```

Una vez creado el Dockerfile, se puede utilizar el archivo de docker-compose personalizado para crear y ejecutar los contenedores en el entorno de desarrollo. En este archivo se indica que se utilizará el archivo **Dockerfile.dev** y se montará la ruta actual en el contenedor para utilizar la funcionalidad de hot reload.

```yml 
version: "3.9" 
services:

  aplicacion_node:
    build: 
      context: . #Indica donde se encuentra la aplicación donde va a estar trabajando 
      dockerfile: Dockerfile.dev #Indicamos que use el archivo de desarrollo
    ports:
      - "3000:3000"
    links: 
      - monguito 
    volumes:
      - .:/home/app #decimos que la ruta actual es la que tiene que ser montada en el volumen y despues de los dos puntos indicamos la ruta del contenedor 

  monguito:
    image: mongo
    ports:
      - "27017:27017" 
    environment:
    - MONGO_INITDB_ROOT_USERNAME=daniel
    - MONGO_INITDB_ROOT_PASSWORD=1234 
```

Ejecutamos el siguiente comando con la bandera "-f" para decir que es un archivo personalizado de docker-compose:

```sh
docker compose -f docker-compose-dev.yml up
```

Con esto, se tendrá un entorno de desarrollo listo para trabajar y utilizar la funcionalidad de hot reload sin tener que detener y volver a levantar el contenedor cada vez que se hagan cambios en el código.

