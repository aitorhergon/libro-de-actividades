
#Entrega

* Entregar 1 informe con las capturas de pantalla de los apartado que se indiquen.
* Comentar brevemente los pasos en el informe.

# Live CD

Vamos a practicar con un SO en live CD.
* Descargar una ISO Live del sistema operativo Knoppix7.
* Crear una máquina virtual sin disco duro.
* Iniciar el SO en modo LIVE.

> Para que se nos cargue en español debemos poner en el inicio **boot:**` knoppix lang=es`

* Probar el sistema.
* Abrir un terminal y capturar la salida de los siguientes comandos:
```
date
ip a
sudo blkid
```
* Crear algunos archivos y carpetas.
* Reiniciar el SO live.
* Comprobar que los archivos/carpetas creados anteriormente no se han guardado.

# Windows 7

* Descargar la versión *Windows7 profesional sp1 x64*.
* Vamos a realizar un instalación por defecto, cambiando lo siguiente:
```
Nombre de usuario : nombre del alumno en minúsculas
Nombre de máquina : 1er apellido en minúsculas
```
* Crear una MV, esta vez si tendrá disco duro.
* Instalar el SO Windows 7 en la MV con las opciones por defecto.
* Abrir un terminal *PowerShell*, y capturar la salida de los siguientes comandos:
```
date
ipconfig
```

# GNU/Linux OpenSUSE

* Descargar *GNU/Linux OpenSUSE 13.2*.
* Vamos a realizar un instalación por defecto, cambiando lo siguiente:
```
Nombre de usuario : nombre del alumno en minúsculas
Nombre de máquina : 1er apellido en minúsculas
Nombre dominio    : 2º apellido en minúsculas
```
> Nombre de host es lo mismo que nombre de máquina

* Crear una MV, esta vez si tendrá disco duro.
* Instalar el SO GNU/Linux en la MV con las opciones por defecto.
* Una vez instalado el sistema operativo, ejecutar el programa
`Yast`. Ir a `Ajustes de red -> Nombre de Host/DNS`.
    * Escribir nombre de host/máquina
    * Escribir nombre de dominio
    * Deshabilitar `Modificar nombre...`
    * Activar `Asignar nombre...`

![hostname](./images/hostname.png)

* Abrir un terminal y capturar la salida de los siguientes comandos:
```
hostname -f
date
ip a
sudo blkid
sudo fdisk -l
```

> [Vídeo Install OpenSUSE] (http://www.youtube.com/embed/nC8n1Pg6gto?list=PL3E447E094F7E3EBB)
