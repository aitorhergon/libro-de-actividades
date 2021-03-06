
#1.Introducción

Es muy común que nos encontremos desarrollando una aplicación y llegue
el momento que decidamos tomar todos sus archivos y migrarlos ya sea al
ambiente de producción, de prueba o simplemente probar su comportamiento
en diferentes plataformas y servicios. Para situaciones de este estilo
existen herramientas que, entre otras cosas, nos facilitan el embalaje
y despliegue de la aplicación, es aquí donde entra en juego Docker.

Esta herramienta nos permite crear lo que ellos denominan contenedores,
lo cual son aplicaciones empaquetadas auto-suficientes, muy livianas
 que son capaces de funcionar en prácticamente cualquier ambiente,
 ya que tiene su propio sistema de archivos, librerías, terminal, etc.

#2. Preparativos

Nesitaremos una MV OpenSUSE 13.2.

> Se requiere versión del Kernel 3.10 o superior (`uname -a`)

#3. Instalación y configuración

* Enlaces de interés [Docker installation on SUSE](https://docs.docker.com/engine/installation/linux/SUSE)

Ejecutar como superusuario:
```
zypper in docker              # Instala docker
systemctl start docker        # Inicia el servicio
                              # "docker daemon" hace el mismo efecto
docker version                # Debe mostrar información del cliente y del servidor
usermod -a -G docker USERNAME # Añade permisos a nuestro usuario
```

> Salir de la sesión y volver a entrar con nuestro usuario.

Ejecutar con nuestro usuario para comprobar que todo funciona:
```
docker images           # Muestra las imágenes descargadas hasta ahora
docker ps -a            # Muestra todos los contenedores creados
docker run hello-world  # Descarga y ejecuta un contenedor con la imagen hello-world
docker images
docker ps -a
```

> **Habilitar el acceso a la red externa para los contenedores**
>
> If you want your containers to be able to access the external network,
you must enable the net.ipv4.ip_forward rule. To do this, use YaST.
>
> * Para openSUSE13.2 (cuando el método de configuracion de red es Wicked).
`Yast -> Dispositivos de red -> Encaminamiento -> Habilitar reenvío IPv4`
> * Cuando la red está gestionada por Network Manager, en lugar de usar YaST
debemos editar el fichero `/etc/sysconfig/SuSEfirewall2` y poner `FW_ROUTE="yes"`.
> * Para openSUSE Tumbleweed `Yast -> Sistema -> Configuración de red -> Menú de encaminamiento`.
>
> ¿Recuerdas lo que implica `forwarding` en los dispositivos de red?

#4. Crear un contenedor manualmente

Nuestro SO base es OpenSUSE, pero vamos a crear un contenedor Debian8,
y dentro instalaremos Nginx.

##4.1 Crear una imagen

* Enlace de interés: [Cómo instalar y usar docker](http://codehero.co/como-instalar-y-usar-docker/)

```
docker images          # Vemos las imágenes disponibles localmente
docker search debian   # Buscamos en los repositorios de Docker Hub
                       # contenedores con la etiqueta `debian`
docker pull debian:8   # Descargamos contenedor `debian:8` en local
docker pull opensuse
docker ps -a           # Vemos todos los contenedores
docker ps              # Vemos sólo los contenedores en ejecución
```  

* Vamos a crear un contenedor con nombre `mv_debian` a partir de la
imagen `debian:8`, y ejecutaremos `/bin/bash`:
```
docker run --name=mv_debian -i -t debian:8 /bin/bash

(Estamos dentro del contenedor)
root@IDContenedor:/# cat /etc/motd            # Comprobamos que estamos en Debian
root@IDContenedor:/# apt-get update
root@IDContenedor:/# apt-get install -y nginx # Instalamos nginx en el contenedor
root@IDContenedor:/# apt-get install -y vim   # Instalamos editor vi en el contenedor
root@IDContenedor:/# /usr/sbin/nginx          # Iniciamos el servicio nginx
root@IDContenedor:/# ps -ef
```

* Creamos un fichero HTML (`holamundo.html`).
```
root@IDContenedor:/# echo "<p>Hola Nombre-del-alumno!</p>" > /var/www/html/holamundo.html
```

* Creamos tambien un script `/root/server.sh` con el siguiente contenido:
```
    #!/bin/bash

    echo "Booting nginx..."
    /usr/sbin/nginx &

    echo "Waiting..."
    while(true) do
      sleep 60
    done
```

> Este script inicia el programa/servicio y entra en un bucle, para permanecer
activo y que no se cierre el contenedor.
> Más adelante cambiaremos este script por la herramienta `supervisor`

* Ya tenemos nuestro contenedor auto-suficiente de Nginx, ahora debemos
crear una nueva imagen con los cambios que hemos hecho, para esto
abrimos otra ventana de terminal y busquemos el IDContenedor:

```
david@camaleon:~/devops> docker ps
CONTAINER ID   IMAGE      COMMAND       CREATED          STATUS         PORTS  NAMES
7d193d728925   debian:8   "/bin/bash"   2 minutes ago    Up 2 minutes          mv_debian
```

Ahora con esto podemos crear la nueva imagen a partir de los cambios que realizamos sobre la imagen base:
```
docker commit 7d193d728925 dvarrui/nginx
docker images
```

> Los estándares de Docker estipulan que los nombres de las imagenes deben
seguir el formato `nombreusuario/nombreimagen`.
> Todo cambio que se haga en la imagen y no se le haga commit se perderá en cuanto se cierre el contenedor.

```
docker ps
docker stop mv_debian  # Paramos el contenedor
docker ps
docker ps -a           # Vemos el contenedor parado
docker rm IDcontenedor # Eliminamos el contenedor
docker ps -a
```

##4.2 Crear contenedor

Bien, tenemos una imagen con Nginx instalado, probemos ahora la magia de Docker.
Iniciemos el contenedor de la siguiente manera:

```
docker ps
docjer ps -a
docker run --name=mv_nginx -p 80 -t dvarrui/nginx /root/server.sh
docker ps
```

> * El argumento `-p 80` le indica a Docker que debe mapear el puerto especificado
del contenedor, en nuestro caso el puerto 80 es el puerto por defecto
sobre el cual se levanta Nginx.
> * El script `server.sh`nos sirve para iniciar el servicio y permanecer en espera.
Lo podemos hacer también con el prgorama `Supervisor`.

* Ahora en una nueva ventana ejecutaremos `docker ps`. Podemos apreciar
que la última columna nos indica que el puerto 80 del contenedor
está redireccionado a un puerto local `0.0.0.0.:NNNNNN->80/tcp`, vayamos al explorador
y veamos si conectamo con Nginx dentro del contenedor.

![docker-url-nginx.png](./files/docker-url-nginx.png)

Paramos el contenedor y lo eliminamos.

```
docker ps
docker stop mv_nginx
docker ps
docker ps -a
docker rm mv_nginx
docker ps -a
```

#5. Crear un contenedor con `Dockerfile`

Ahora vamos a conseguir el mismo resultado del apartado anterior, pero
usando un fichero de configuración, llamado `Dockerfile`

##5.1 Comprobaciones iniciales:

```
docker images
docker ps
docker ps -a
```

##5.2 Preparar ficheros

* Crear directorio `/home/alumno/docker`, poner dentro los siguientes ficheros.
* Dockerfile:
```
FROM debian:8

MAINTAINER David Vargas version 1.0

RUN apt-get update
RUN apt-get install -y nginx
RUN apt-get install -y vim

COPY holamundo.html /var/www/html
RUN chmod 666 /var/www/html/holamundo.html

COPY server.sh /root
RUN chmod +x /root/server.sh

EXPOSE 80

CMD ["/root/server.sh"]
```

> Necesitaremos también los ficheros `server.sh` y `holamundo.html` que vimos antes.

##5.3 Crear imagen

El fichero [Dockerfile](./files/Dockerfile) contiene la información
necesaria para contruir el contenedor, veamos:

```
cd /home/alumno/docker
docker images
docker build -t dvarrui/nginx2 .  # Construye imagen a partir del Dockefile
docker images
```

##5.4 Crear contenedor y comprobar

```
docker run --name mv_nginx2 -p 80 -t dvarrui/nginx2 /root/server.sh
```

* Desde otra terminal hacer `docker...`, para averiguar el puerto de escucha
del servidor Nginx.
* Comprobar en el navegador URL: `http://localhost:PORTNUMBER`

#ANEXO

##A.2

Enlaces de interés:
* [Docker for beginners](http://prakhar.me/docker-curriculum/)
* [getting-started-with-docker](http://www.linux.com/news/enterprise/systems-management/873287-getting-started-with-docker)

Docker es una tecnología contenedor de aplicaciones construida sobre LXC.
