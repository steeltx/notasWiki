# Instalación de Docker en Openmediavault

En esta guía se muestra el proceso para instalar y configurar Docker en Openmediavault, para este ejemplo en una máquina virtual con KVM en sistema host Linux Mint.

## Requerimientos
- KVM instalado en sistema host (En este caso Linux Mint)
- Sistema con capacidad de virtualización
- Espacio en disco duro (al menos 60 GB para este ejercicio)
- Al menos 8 GB de RAM
- Sistema Openmediavault instalado

## Instalación de KVM

Para instalar KVM en Linux Mint, podemos ver la siguiente guía

- [Creación de máquinas virtuales con KVM en Linux](/linux/kvmLinux.md)

## Creación de máquina virtual

Para la creación de la máquina virtual podemos ver la siguiente guía

- [Creación de máquina virtual con OMV](/linux/omv.md)

## Instalación de Docker en OMV

Como primer paso, vamos a instalar OMV-Extras, que nos permite agregar algunos complementos adicionales.

Vamos al repositorio de esta herramienta [Repositorio OMV-Extras](https://github.com/OpenMediaVault-Plugin-Developers/packages), en el archivo README podemos visualizar el comando para instalarlo


- Ingresar a Openmediavault mediante SSH, para ello en el sistema host abrimos una terminal y colocamos el siguiente comando

```bash
$ ssh root@{ip}
```

- En caso de ingresar por primera vez desde nuestro sistema host, nos indica que la llave no se conoce, escribimos **yes** y ahora nos solicita la contraseña de root, la cual configuramos durante la instalación. Una vez en el sistema, ingresamos el comando que se indica en el repo de GitHub


```bash
$ sudo su -
$ wget -O - https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install | bash
```
- Al terminar el proceso de instalación, reiniciar el sistema con el comando

```bash
$ sudo reboot
```

- Entrar al administrador web e ir a System, en la lista podemos ver que esta un nuevo apartado llamado **omv-extras**

![](omvdocker/1.png)

- Ingresar a System > omv-extras, activar la casilla **Docker repo** y clic en Save

![](omvdocker/2.png)

- Ingresar en System > Plugins y en la barra de búsqueda escribir **compose**

![](omvdocker/3.png)

- Seleccionar el plugin encontrado y dar clic en el icono de flecha abajo para instalar, confirmar la acción y esperar un momento a que termine el proceso

![](omvdocker/4.png)

![](omvdocker/5.png)

![](omvdocker/6.png)

- Desde el administrador web en la barra superior, clic en el icono de apagar y seleccionar **Reboot**, esperamos un momento a que termine el proceso y nuevamente se muestre la vista de login para ingresar al sistema

![](omvdocker/7.png)

![](omvdocker/8.png)

## Configuración de Docker

- Una vez que se instalan los plugins necesarios, vamos a crear las carpetas necesarias para poder ejecutar contenedores, para ello vamos a Storage > Shared Folders

![](omvdocker/9.png)

- Clic en el icono de +, en nombre colocar compose, File System seleccionar el RAID 1 que se configuro en la instalación, o bien, el sistema de archivos donde desea almacenar la información, los permisos lo dejamos por default y agregamos alguna etiqueta para identificar, en este caso colocamos Docker

![](omvdocker/10.png)

![](omvdocker/11.png)

- Repetimos el paso anterior 2 veces mas, pero en el nombre de las carpetas colocamos **appdata** y **appdata-backup**

![](omvdocker/12.png)

![](omvdocker/13.png)

- El nombre de las carpetas puede ser diferente, pero se recomieda de esta manera para que sea facil su identificación. Para finalizar esta configuración creamos la carpeta de **docker** igual que en los pasos anteriores

![](omvdocker/14.png)

![](omvdocker/15.png)

- Con las carpetas creadas, ir a Services > Compose > Settings, en Compose Files Shared Folder seleccionamos **compose**, al crear el tag de Docker nos ayuda a identificar rápidamente la carpeta

![](omvdocker/16.png)

- En la sección de Data seleccionamos **appdata [Docker]**, en Backup seleccionar **appdata-backup [Docker]** 

![](omvdocker/17.png)

- En la sección de Docker podemos dejar por default las configuraciones, en este caso como ya creamos la carpeta de docker, lo colocamos y clic en Save, el proceso tarda un momento en completar

![](omvdocker/18.png)

![](omvdocker/19.png)

- Al terminar el proceso refrescamos la página, en caso de que en la sección de Docker > Status nos indique que no esta instalado, en la parte inferior dar clic en **Reinstall Docker**, si todo es correcto, podemos visualizar en status que dice que esta instalado y corriendo, asi como la versión que se esta ejecutando

![](omvdocker/20.png)

## Creando un contenedor de Docker

- Al tener instalado Docker podemos crear nuestros contenedores desde un archivo de Docker compose en la sección de Services > Compose > Files, dar clic en el botón de + y agregar. Para este caso vamos a crear un contenedor de ejemplo, clic en el botón de + y Add from Example

![](omvdocker/21.png)

- Se muestra una lista diferentes aplicaciones que podemos instalar, para este ejemplo en la barra de búsqueda escribimos nginx y seleccionamos la primera opción

![](omvdocker/22.png)

- En la parte superior clic en el icono de +, se muestra una ventana donde agregamos la descripción deseada y clic en Add, esperamos un momento

![](omvdocker/23.png)

- Confirmamos los cambios 

![](omvdocker/24.png)

- Ya tenemos configurado el archivo de Docker compose con nginx, para iniciar, seleccionarlo y en la barra superior clic en el icono de flecha hacia arriba, esperar a que termine el proceso

![](omvdocker/25.png)

![](omvdocker/26.png)

- Al terminar, el Status se encuentra en color verde con el mensaje de Up, en la columna de ports se indica sobre que puerto se esta ejecutando, para probar en una nueva ventana del navegador escribimos la IP del servidor y el puerto 8080 **http://{ip-servidor}:8080**

- Al ingresar se muestra un mensaje 403 Forbidden debido a que no se ha configurado algún sitio web, pero podemos observar que la respuesta a la URL es por medio de **nginx/1.27.4**

![](omvdocker/27.png)

De esta manera, podemos crear contenedores que vienen de ejemplo desde OpenMediaVault o bien, crear los nuestros con las configuraciones deseadas, tomar en cuenta los recursos con los que cuenta el servidor y los contenedores que se van a ejecutar, para evitar que el sistema se alente.


# Referencias
[Repositorio OMV-Extras](https://github.com/OpenMediaVault-Plugin-Developers/packages)

[OpenMediaVault](https://docs.openmediavault.org/en/stable/index.html)

