

#1. Introducción

Toda la actividad importante del sistema debe quedar registrada en los 
ficheros de registro. Esto nos permite tener un histórico del comportamiento 
del sistema, que nos ayuda a modo de *"caja negra"*, a reconstruir situaciones 
del pasado para diversos fines. Esta es la utilidad de la monitorización y la auditoría,
saber lo que ha pasado.

#2. Registro en Windows
Realizar las siguientes tareas en SO Windows:

* Debemos activar unas directivas de seguridad, para auditar los inicios 
de sesión al sistema (Correctos e incorrectos). Incluir captura de pantalla con la directiva activada.
* Nosotros queremos auditar los *"Sucesos de inicio de sesión"*.

> NOTA:
>
> Los "Sucesos de inicio de sesión de cuenta" no los vamos a necesitar ahora.
Éstos sirven para auditar a los usuarios del dominio. Esto todavía no lo hemos visto.

A continuación se muestra imagen de ejemplo de la directiva desactivada:

![activar-directivas](./images/activar-directivas.png)

* Poner como nombre NETBIOS de la máquina:
    * "1er-apellido-del-alumno"+1 => Windows 7
    * "1er-apellido-del-alumno"+2 => Windows 2008 Server
* Reiniciar la MV para que empiecen a funcionar las auditorías.
* Crear los usuarios nombre-alumno1, nombre-alumno2 y nombre-alumno3.
* Hacer las siguientes acciones:
    * Entrar al sistema con nombre-alumno1 de forma correcta.
    * Intentar entrar con nombre-alumno2 poniendo la clave mal.
    * y no entrar con nombre-alumno3.
* Buscar en el sistema, la herramienta visor de sucesos. Comprobar cómo 
se registran los eventos anteriores en la sección "Seguridad". Incluir captura de pantalla.
* Exportar los eventos a ficheros CSV. Filtrar los eventos para NO incluirlos 
todos (Elegir los generados hoy, o en las últimas horas). 
Incluir fichero en la entrega con el nombre "nombre-alumno-registro-windows.csv".
> Los ficheros con formato CSV se pueden abrir y manipular cómodamente usando hojas 
de cálculo (Por ejemplo: Excel de Microsoft, Calc de LibreOffice, etc.). Comprobarlo.
> Realmente son ficheros de texto donde cada fila es como in registro de una tabla.
Y se usa la coma para delimitar los campos dentro de cada fila.

#3. Registro en GNU/Linux (Teoría)
##3.1 Monitorización

Configurar y recurrir a archivos de log.

    Enlaces de interés: logs en linux.
    Los archivos de log se guardan normalmente en /var/log, aunque cada programa puede usar uno propio.
    El servicio de log lo controla rsyslog o syslog en versiones antiguas.
    El fichero de configuración principal es /etc/rsyslog.conf, pero hay ficheros de configuración secundarios en /etc/rsyslog.d. Por ejemplo el fichero /etc/rsyslog.d/50-default.conf.
    Campos: selector (facility+priority) y acción. También llamado "recurso.prioridad acción".
        Facility identifica al origen del mensaje: auth, authpriv, cron, daemon, ftp, kern, lpr, mail, news, syslog, user, uucp, y local0 hasta local7.
        Priority idenfica la urgencia del mensaje: debug, info, notice, warning, err, crit, alert y emerg.

Ejemplo de configuración dentro de rsyslog:

    kern.* /var/log/kernel: Los eventos generados por kernel, se guardarán en el fichero /var/log/kernel.
    kern.crit @192.168.1.11: Los eventos críticos o de mayor prioridad, generados por kernel se enviarán al PC con la IP especificada.
    kern.crit /dev/console: Los eventos críticos o de mayor prioridad, generados por el kernel se mostrarán en el dispositivo consola.
    kern.info;kern.!err /var/log/kernel-info: Los eventos generados por el kernel, de igual o mayor prioridad a info, pero NO los eventos de error o prioridad superior, se guardarán en el fichero /var/log/kernel-info.

El comando logger permite crear un mensaje de log manualmente. La opción -p permite determinar el par "facility-priority". Más información en http://www.estrellateyarde.org/so/logs-en-linux

3.2 Auditorías

Veamos la utilidad de algunos ficheros de log del sistema:

    /var/log/messages – Contains global system messages, including the messages that are logged during system startup.
    /var/log/dmesg – Contains kernel ring buffer information. When the system boots up, it prints number of messages on the screen that displays information about the hardware devices that the kernel detects during boot process. You can also view the content of this file using the dmesg command.
    /var/log/auth.log – Contains system authorization information, including user logins and authentication machinsm that were used.
    /var/log/boot.log – Contains information that are logged when the system boots
    /var/log/dpkg.log – Contains information that are logged when a package is installed or removed using dpkg command
    /var/log/lastlog – Displays the recent login information for all the users. This is not an ascii file. You should use lastlog command to view the content of this file.
    /var/log/user.log – Contains information about all user level logs
    /var/log/btmp – This file contains information about failed login attemps. Use the last command to view the btmp file. For example, “last -f /var/log/btmp | more”
    /var/log/wtmp or /var/log/utmp – Contains login records. Using wtmp you can find out who is logged into the system. who command uses this file to display the information.
    /var/log/faillog – Contains user failed login attemps. Use faillog command to display the content of this file.

Además existen otras herramientas para auditar accesos a objetos del sistema de ficheros como "incron".

4. Registro en GNU/Linux (GUI)

    Se recomienda usar GNU/Linux Debian.
    Para cambiar el nombre de la máquina, hacemos:
        # hostname "1er-apellido-alumno"+3
        # hostname (Para comprobar)
    Instalar el programa Webmin.
        Primero lo inentamos vía gestor de paquetes y/o apt-get install.
        También podemos hacerlo descargando el paquete deb de la web de Webmin. Para instalarlo usamo dpkg. Veamos ejemplo: "dpkg -i webmin_1.550_all.deb"
        Si tenemos problemas de dependencias de paquetes, instalando el paquete deb, entonces ejecutamos el siguiente comando para resolverlas: "apt-get install -f".
        Para iniciar el servicio Webmin, ejecutamos: "/etc/init.d/webmin start".
        Para acceder al programa Webmin, abrimos un navegador web y ponemos como URL "https://localhost:10000".
        Vamos a analizar los ficheros de log con la herramienta anterior. Para ello vamos a las opciones del menú "System -> System Log". Incluir captura de pantalla.


5. Registro en GNU/Linux (Comandos)

    Vamos a realizar una monitorización local de nuestro equipo GNU/Linux, con rotación de log's.
    Se recomienda usar GNU/Linux Debian.
    NOTA:
        El fichero de configuración principal del servicio Syslog tiene el nombre /etc/rsyslog.conf.
        En algunas distribuciones existe además el directorio /etc/rsyslog.d que contiene la configuración repartida en varios ficheros para hacerla más manejable. Pero funciona igual que el caso anterior.
        El programa/demonio del servicio Syslog tiene el nombre /sbin/rsyslogd.

5.1 Configuración de Rsyslog
Realizar las siguientes tareas:

    Consultar el fichero "/etc/rsyslog.d/50-default.conf". Si no existe lo creamos.
    [INFO] Vamos a añadir una nueva línea para configurar la grabación de nuestros eventos:
        Como recurso.prioridad usaremos local0.*
        Como fichero de log usaremos "/var/log/nombrealumno/prueba-local.log".
    Añadir la siguiente línea al fichero de configuracion /etc/rsyslog.d/50-default.conf:
        local0.* /var/log/nombredelalumno/prueba-local.log
    Con el usuario root, crear el fichero /var/log/nombredelalumno/prueba-local.log vacío.
    Ahora debemos reiniciar el servicio rsyslog, para que se recargue la nueva configuración.
        /etc/init.d/rsyslog stop
        /etc/init.d/rsyslog start

5.2 Comprobar Rsyslog

    cat /var/log/nombredelalumno/prueba-local.log: Esto nos muestra que el fichero de log está vació por el momento.
    [INFO] Ahora vamos a usar el comando logger para generar mensajes de log en el fichero anterior. Por ejemplo: logger -p local0.info "Hola Mundo".
    [INFO] Usaremos para nuestros ejemplos el recurso "local0". Pero también se podrían usar local1, local2, local3, local4, local5, local6 y local7.
    Crear varios registros mediante el comando logger, usando varias prioridades diferentes y otros valores. Consultar la ayuda con "man logger"· Por ejemplo, probar los parámetros de logger -i, -t, etc.
    Consultar los mensajes registrados, e indicar sobre un ejemplo el significado de cada campo de una línea concreta del log. Incluir captura de pantalla.


6. Configuración de Logrotate

    El programa logrotate permite hacer rotación de los ficheros de log.
    Fichero de configuración principal es /etc/logrotate.conf. Además existen ficheros de configuración auxiliares en /etc/logrotate.d.
    Ahora vamos a configurar logrotate para generar rotaciones de nuestro fichero de log. Para ello crearemos el fichero /etc/logrotate.d/nombre-del-alumno con el siguiente contenido:

/var/log/nombre-alumno/prueba-local.log /var/log/nombre-alumno/prueba-local.*.log {
size = 1k
rotate 3
missingok
compress
notifempty
}

    La configuración anterior indica que los ficheros de log tendrán un tamaño máximo de 1k, que se crearán hasta 3 ficheros de rotaciones en modo comprimido.
    Vamos a generar muchos mensajes de log en el fichero de registro de eventos:
        Podemos usar el script proporcionado por el profesor, cuya función es la de generar cientos de mensajes de log hacia el nuevo fichero de registro. Modo de uso:
            Descargar fichero "send-messages.sh".
            Dar permisos de ejecución: "chmod +x send-messages.sh".
            Ejecutar el script "./send-mesagges.sh".
        Podemos invocando el comando siguiente para aumentar el tamaño del registro de log con la información contenida en un fichero de texto: logger -p local0.notice -t ETC-MOTD -f /etc/motd
    Comprobar que el fichero de log ha crecido de tamaño.
    Para que se dispare la rotación de log (logrotate) automáticamente podemos reiniciar el equipo. Sin reiniciar el equipo, podemos ejecutar manualmente el programa de rotación logrotate de la siguiente forma: /usr/sbin/logrotate -f /etc/logrotate.conf
    Comprobar que el fichero cambia de tamaño, y que efectivamente se ha producido la rotación de los ficheros de log.


#ANEXO

* El programa [Sentry](http://sourceforge.net/projects/sentrytools) sirve para revisar los logs del sistema.
* La herramienta incron de GNU/Linux, permite crear/activar auditorías de cualquier 
objeto del sistema de ficheros.
