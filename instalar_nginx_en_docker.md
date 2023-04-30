# Como instalar un servidor web Nginx en Docker

Buscamos la imagen y descargamos la imagen
```bash 
docker pull nginx
```

Creamos el archivo **Dockerfile**, este archivo se recomienda al lado de la carpeta con el código:
```Dockerfile
FROM nginx

COPY mi-pagina /usr/share/nginx/html
```

Construimos la imagen del archivo **Dockerfile**:

```bash
docker build -t web .
```

Creamos y arrancamos el contenedor para que se ejecute de fondo y mapeamos el puerto 8080 del anfitrión al 80 del contenedor:
Este contenedor no tiene nombre jeje
```bash
docker run -d -p 8080:80 web 
```

Listas los contenedores para ver si esta levantado el servidor:
```bash
docker ps
```

Ingresamos a cualquier localhost para ver si se levando el servidor:

[Ingresar Aquí](http://localhost:8080)


Detener servidor osea detener contenedor 

```bash
docker stop id_contenedor
```


Eliminar contenedor
```bash
docker rm id_contenedor
```


