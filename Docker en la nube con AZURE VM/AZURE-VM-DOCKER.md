----------------
# Crear un contenedor Docker en Azure

Para poder llevar esto a cabo necesitaremos crear una máquina virtual en nuestra cuenta de Azure. Hay muchos sistemas operativos para poder llevar a cabo esto pero nosotros utilizaremos **Ubuntu minimal 20.04**

Partiendo de un punto en el que ya tengamos nuestra máquina virtual creada con nuestra clave de acceso .pem a la terminal a través de ss, vamos a intentar entrar, necesitaremos lo siguiente:

## 1.1 Entrar desde Powershell o CMD
- Necesitaremos establecer los persisos de nuestra clave con la herencia deshabilitada (quitando los permisos actuales al resto de usuarios) y solo un usuario puede tener acceso a los permisos de lectura y ejecución del archivo que contiene la clave (se modifica en propiedades --> Seguridad --> opciones avanzadas)
- desde el usuario que tiene los permisos de lectura ejecutamos el siguiente comando
```bash
ssh directory\key.pem <AZURE-VM-USER>@<VM-PUBLIC-IP-ADDRESS>
```
## 1.2 Entrar desde terminal de Linux o WSL
- En primer lugar, si estamos utilizando un Subsistema de Windows para Linux (WSL), necestiaremos copiar la clave dentro del sistema de archivos principal de Linux, ya que si está ubicado en /mnt/c/Users/UsuarioX/... habrá un conflicto de permisos que no nos permitira llevar a cabo la conexión.
- En el caso de que accedamos a la terminal remota a través de una terminal de linux, necesitaremos establecer los permisos con el siguiente comando:
```bash
chmod 400 directory/key.pem
```
- Luego ejecutamos el siguiente comando para realizar la conexión:
```bash
ssh -i directory/key.pem <AZURE-VM-USER>@<VM-PUBLIC-IP-ADDRESS>
```

## 1.3 Entrar desde Visual Studio code
En el caso de que queramos realizar una labor de programación, recomiendo el uso de Visual Studio code para acceder a la máquina virtual de Azure. Para esto vamos a usar una extensión: https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh

Es muy util porque podremos trabajar con la terminal integrada de VScode a la vez que podemos acceder a los archivos de la propia máquina y manejarlos como si fuera nuestro entrono local.

Una vez tengamos instalada la extensión tendremos que escribir en la barra de comandos de Visual Studio Code el símbolo **>** seguido de el comando 
```bash
> Remote-SSH: Open SSH Configuration File
```
. Se nos abrirá un fichero y tendremos que ponerle el siguiente contenido:
```bash
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config
Host <AZURE-VM-USER>
    HostName <VM-PUBLIC-IP-ADDRESS>
    User <AZURE-VM-USER>
    IdentityFile "C:\Users\UserX\Desktop\key.pem"
```

Con esto ya solo tendríamos que realizar el cambio de permisos de key.pem de la misma forma que se explica en el punto 1.1. Una vez hecho, ejecutamos siguiente comando en la linea de comandos de Visual Studio Code

```bash
> Remote-SSH: Connect to Host
```
Ahí seleccionamos el usuario que nos saldrá (valor de <AZURE-VM-USER>) y elegimos sistema Linux, si hemos configurado todo bien, debería abrir una nueva ventana y conectarnos sin problema

## 2.1 Instalar Docker en la máquina virtual

Primero actualizamos el sistema e instalamos Docker:
```bash
sudo apt update
sudo apt install -y docker.io
```
Luego habilitamos Docker y verificamos la instalación
```bash
sudo systemctl enable docker
sudo systemctl start docker
docker --version
```
Después instalamos Docker Compose y verificamos la instalación
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

## 2.2 Lanzar un contenedor

Para probar que Docker está funcionando en Azure, corremos un contenedor cualquiera como Nginx
```bash
sudo docker run -d -p 80:80 nginx
```
Luego accede a la IP pública de tu VM en Azure desde un navegador (http://<VM-PUBLIC-IP-ADDRESS>) para ver la página de bienvenida de Nginx