----------------
# Crear un Dockerfile a partir de un proyecto

Primero necesitaremos una aplicación para Dockerizar  
En este caso voy a utilizar programa muy simplle en Javascript

### index.js
```bash
const http = require('http');
const server = http.createServer((req, res) => {
    res.end('Hello, Docker!');
});
server.listen(3000, () => console.log('Server is running on port 3000'));

```
### package.json
```bash
{
    "name": "my-app",
    "version": "1.0.0",
    "main": "index.js",
    "dependencies": {},
    "scripts": {
        "start": "node index.js"
    }
}
```

### Dockerfile
```bash
FROM node:14            
WORKDIR /app            
COPY . .                
RUN npm install         
EXPOSE 3000             
CMD ["npm", "start"]    

```

- FROM: Usa una imagen base con Node.js
- WORKDIR: Define el directorio de trabajo dentro del contenedor
- COPY: Copia todos los archivos de la app al contenedor
- RUN: Instala dependencias
- EXPOSE: Expone el puerto de la aplicación
- CMD: Comando para iniciar la app

### Esta sería la estructura del proyecto
```bash
my-app/
├── Dockerfile
├── index.js
└── package.json
```

----------------
# Lanzar contenedor desde Dockerfile

Necesitamos crear una imagen con el proyecto para poder publicarla  
Con la imagen creada podremos facilitarle a otra gente el uso de nuestro contenedor en el caso de que quieran probarlo

### construir la imagen desde el Dockerfile
```bash
docker build -t my-node-app .
```
- Esto generará una imagen llamada my-node-app

### Iniciar el contenedor
Ejecuta el contenedor y enlaza el puerto 3000 del contenedor al puerto 3000 de tu máquina

```bash
docker run -d -p 3000:3000 my-node-app
```
El indicador -d ejecuta el contenedor en segundo plano
### Verificar que el contenedor está funcionando
- Abre el navegador y accede a http://localhost:3000. Deberías ver el mensaje “Hello, Docker!”.
- Usa docker ps para verificar los contenedores activos y confirmar que tu contenedor my-node-app está corriendo.
