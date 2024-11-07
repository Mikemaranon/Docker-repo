----------------
# Lanzar un contenedor desde una imagen de Docker Hub

Para poner en practica el lanzar un contenedor con una imagen que ya exista vamos
a utilizar un contenedor **Nginx** lanzando una imagen desde Docker Hub.

### Buscar la imagen en Docker Hub
Docker Hub es un repositorio con imágenes oficiales. Para Nginx, la imagen oficial se llama nginx: https://hub.docker.com/_/nginx
```bash
docker search nginx
```
### Descargar la imagen
Aunque podemos usar docker pull nginx para descargar la imagen, Docker lo hará automáticamente si intentas correr un contenedor desde ella.
```bash
docker pull nginx
```
### Lanzar el contenedor de Nginx
Para iniciar un contenedor usando la imagen de Nginx y exponer el puerto 80 del contenedor en el puerto 8080 de nuestra máquina local, debemos usar este comando:
```bash
docker run -d -p 8080:80 nginx
```
- d: Ejecuta el contenedor en segundo plano (modo "detached").
- p 8080:80: Mapea el puerto 80 del contenedor al puerto 8080 de la máquina host, permitiendo el acceso a Nginx en http://localhost:8080.
- nginx: Especifica la imagen a utilizar (Nginx en este caso).

### Verificar que el contenedor está corriendo
Necesitamos asegurarnos de que el contenedor se ha ejecutado correctamente y esta inicializado como debería.
```bash
docker ps
```
En el caso de que el docker haya fallado, necesitaremos ejecutar el siguiente comando
```bash
docker ps -a
```
con esto podremos ver información sobre el contenedor fallido y posteriormente con el comando docker logs podremos ver en mas detalle lo ocurrido
```bash
docker logs <container_id>
```

----------------
# Lanzar contenedor personalizado con NGINX

Si queremos publicar nuestra propia página web dockerizada, podemos usar nginx como una base sobre la que construir una imagen nueva que contenga todo nuestro proyecto

### Crea la estructura del proyecto web
Siguiendo una estructura típica y simple, vamos a suponer que tenemos la siguiente página web en el directorio **my-website/**
```bash
my-website/
├── index.html
├── styles.css
└── script.js
```

### Crear un Dockerfile personalizado
Creamos un archivo Dockerfile en el directorio donde están los archivos. Este Dockerfile copiará el contenido al contenedor y configurará Nginx para servir la página web

```bash
# Usar la imagen base oficial de Nginx
FROM nginx:latest

# Copiar los archivos de tu página web al directorio de Nginx
COPY ./my-website /usr/share/nginx/html

# Exponer el puerto 80
EXPOSE 80
```

### Construir la imagen personalizada:
Desde el directorio donde está el Dockerfile que acabamos de hacer, corremos el siguiente comando para construir la imagen:

```bash
docker build -t my-nginx-web .
```

### Lanzar el contenedor
Ahora ejecutamos un contenedor con la imagen recién creada, mapeando el puerto 80 del contenedor al puerto 8080 de nuestra máquina local:
```bash
docker run -d -p 8080:80 my-nginx-web
```
Con esto ya tenemos listo el acceso a nuestro proyecto web desde la url local http://localhost:8080

