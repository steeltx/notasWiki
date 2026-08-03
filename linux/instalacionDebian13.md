# Instalación de Debian 13 Trixie

En esta guía se muestran los pasos para instalar Debian 13 Trixie.

---

### Video del proceso de instalación Debian 13
[![Video del proceso de Instalación](https://img.youtube.com/vi/6l8If1GU4EY/mqdefault.jpg)](https://youtu.be/6l8If1GU4EY "Proceso de instalación Debian 13")

---

## Requerimientos
- KVM instalado en alguna distro Linux / Equipo para instalar Debian con datos respaldados previamente (Instalación en todo el disco)
- Espacio en disco duro (al menos 30 GB)
- Sistema con capacidad de virtualización (En caso de usar KVM)

Para instalar KVM en Linux, podemos ver la siguiente guía

- [Creación de máquinas virtuales con KVM en Linux](/Linux/kvmLinux.md)


## Creación de maquina virtual

En caso de realizar la instalación mediante KVM seguimos los siguientes pasos, en caso de que sea con un equipo físico, descargar la ISO y realizar una USB Booteable (puede ser con Rufus desde Windows) y pasar a la sección **Instalación de Debian**.

- Descargar imagen ISO desde el sitio oficial [Debian](https://www.debian.org/index.es.html)

![](/linux/imgdebian/1.png)

Al dar clic el en botón descargar inicia la descarga del archivo de instalación pequeño, para utilizar esta opción debe tener una máquina con conexión a Internet, en nuestro caso vamos a ir a la sección de Otras descargas y obtener imagen de instalación completa, iso DVD para PC de 64bits.

![](/linux/imgdebian/2.png)

- Abrir el gestor de máquinas virtuales desde el menú de nuestro sistema.

![](/linux/imgdebian/3.png)

- Seleccionar el menú de archivo y clic en nueva maquina virtual.

![](/linux/imgdebian/4.png)

- Podemos instalar el sistema operativo de diferentes maneras, en este caso usaremos la primera opción de **Medio de instalación local (Imagen ISO o CDROM)** y clic en Forward.

![](/linux/imgdebian/5.png)

- Clic en explorar, buscar y seleccionar el archivo ISO descargado, en la sección de sistema operativo buscar y seleccionar Debian 13, clic en Forward.

![](/linux/imgdebian/6.png)

En caso de que se muestre el mensaje de que el emulador podría no tener permisos de búsqueda en la ruta, dar clic en si para arreglar el problema.

![](/linux/imgdebian/7.png)

- Configurar la memoria y el CPU, tomar en cuenta el total existente en el sistema host, para este caso colocamos 4 GB de ram y 2 CPU, clic en Forward.

![](/linux/imgdebian/8.png)

- Configurar el total del almacenamiento deseado, en este caso colocamos 30 GB por ser de pruebas, tomar en cuenta de igual manera el total disponible del HDD/SSD y el proposito de la máquina, clic en Forward.

![](/linux/imgdebian/9.png)

- En la siguiente pantalla, colocamos el nombre de la maquina y verificamos que las demas opciones esten correctamente, clic en finalizar.
  Por default, el disco duro virtual se almacena en **/var/lib/libvirt/images** con el formato **.qcow2**

![](/linux/imgdebian/10.png)

## Instalación de Debian

- Una vez inicio el sistema, ya sea por medio de KVM o en un equipo mediante una memoria USB, se muestra la siguiente pantalla, seleccionar la primer opción de *Graphical install*

![](/linux/imgdebian/11.png)

- Seleccionar el idioma, para nuestro caso español, clic en Continue

![](/linux/imgdebian/12.png)

- Seleccionar la ubicación, en nuestro caso México

![](/linux/imgdebian/13.png)

- Configurar la distribución de teclado, clic en Continuar

![](/linux/imgdebian/14.png)

- Esperar un momento a que se carguen algunos componentes, posteriormente ingresar el nombre en que la máquina se identifica en la red

![](/linux/imgdebian/15.png)

- En la configuración de red ingresar el nombre de dominio, para este caso lo vamos a dejar en blanco

![](/linux/imgdebian/16.png)

- El siguiente paso es configurar la contraseña del superusuario, para ello, escribir una segura y clic en continuar

![](/linux/imgdebian/17.png)

- Ingresar el nombre completo del usuario normal para el sistema

![](/linux/imgdebian/18.png)

- Ingresar el nombre de usuario para la cuenta

![](/linux/imgdebian/19.png)

- Ahora vamos a ingresar una contraseña segura para el usuario creado en el paso anterior

![](/linux/imgdebian/20.png)

- En la configuración de reloj, seleccionar la zona horaria deseada, en nuestro caso Central

![](/linux/imgdebian/21.png)

- Para el particionado de discos tenemos diferentes opciones, de forma guiada o de forma manual

![](/linux/imgdebian/22.png)

- En caso de seleccionar la primera opcion de *Guiado - utilizar todo el disco*, el siguiente paso es seleccionar el disco, en esta parte tener especial cuidado si se cuenta con diferentes discos y diferentes particiones para no borrar algo no deseado, en nuestro caso, solo cuenta con 1 disco duro, seleccionar y continuar

![](/linux/imgdebian/23.png)

- En el siguiente paso tenermos varias opciones, de acuerdo a la documentación oficial de debian de la siguiente manera:

| Esquema de particionado | Espacio mínimo | Particiones creadas |
|----------|----------|----------|
| Todos los ficheros en una partición | 8 GB | /, swap |
| Separar la partición /home | 9 GB | /, /home, swap |
| Separar particiones /home, /var y /tmp | 12 GB | /, /home, /var, /tmp,swap |
| Separar /var y /srv, swap < 1 GB para servidores | 8 GB| /, /home, /var, /tmp,intercambio (swap) |
| Esquema de particionado para discos pequeños | 3 GB | /, swap |

![](/linux/imgdebian/24.png)

- En esta parte debe elegir la opción que se adapte mejor a sus necesidades, para nuestro caso, podemos instalar todos los ficheros en una partición (1ra opción) y continuar, en caso de realizar una instalación en un equipo junto a otro sistema, dual boot por ejemplo, seleccionar la opción manual y crear las particiones, cuidado de no afectar las de otros sistemas instalados en el mismo disco

- Con la primera opción se crea la partición / y la de intercambio de forma automática, si estamos de acuerdo, seleccionar *Finalizar el particionado y escribir los cambios en el disco* y continuar

![](/linux/imgdebian/25.png)

- Confirmar escribir los datos en el disco, seleccionando *Si* y continuar

![](/linux/imgdebian/26.png)

- Inicia el proceso de instalación del sistema, esperar unos minutos a que termine

![](/linux/imgdebian/27.png)

- En la parte de configurar gestor de paquetes, no vamos a analizar otros medios de instalación adicionales, dejar seleccionado *No* y continuar

![](/linux/imgdebian/28.png)

- Vamos a seleccionar una réplica de red, por lo que en esta parte seleccionar *Si* y continuar

![](/linux/imgdebian/29.png)

- La réplica depende de donde nos encontremos, podemos seleccionar la mas cercana a nuestra ubicación, para este caso México y continuamos

![](/linux/imgdebian/30.png)

- De acuerdo a la seleccionada, se muestran varias opciones, seleccionar la mas conveniente

![](/linux/imgdebian/31.png)

- En el proxy HTTP, dejar en blanco y continuar

![](/linux/imgdebian/32.png)

- Esperar a que termine el proceso

![](/linux/imgdebian/33.png)

- En la pregunta de participar en la encuesta de uso de paquetes, dejamos seleccionado no para este caso y continuamos

![](/linux/imgdebian/34.png)

- La siguiente sección es la selección de programas, podemos elegir el entorno de escritorio favorito, contamos con las siguientes opciones:
  - Gnome
  - Xfce
  - KDE
  - Cinnamon
  - MATE
  - LXDE
  - LXQt

  En cuanto a herramientas, tenemos las siguientes opciones:
  - Web server
  - SSH server
  - Utilidades estándar del sistema

  Para revisar mas información acerca de los entornos de escritorio, revisar el siguiente link [Debian entornos de escritorio](https://wiki.debian.org/DesktopEnvironment)

  Si deseamos usar un entorno con Wayland tenemos que seleccionar alguno de los siguientes entornos: 
  - GNOME (Implementado desde Debian 10)
  - KDE

  Mas información acerca de Wayland [Wayland en Debian](https://wiki.debian.org/Wayland#Compositors_.28supported.29)


  Como observamos, Debian nos permite instalar una variedad de entornos de escritorio directo desde la instalación del sistema, podemos seleccionar las opciones que se adaptan mejor a nuestras necesidades, en nuestro caso marcamos:
  - Entorno de escritorio Debian
  - GNOME
  - Utilidades estándar del sistema

![](/linux/imgdebian/35.png)

- Se procede a instalar el software seleccionado, este proceso puede tardar un par de minutos

![](/linux/imgdebian/36.png)

- En la parte de instalar el cargador de arranque GRUB seleccionar *Si*, de lo contrario se tendra que configurar posteriormente para que pueda iniciar Debian

![](/linux/imgdebian/37.png)

- Seleccionar el disco donde se va a instalar el GRUB, en caso de contar con diferentes asegurarse de que sea el correcto, en nuestro caso como solo es uno, lo vamos a seleccionar

![](/linux/imgdebian/38.png)

![](/linux/imgdebian/39.png)

- Al terminar el proceso de instalación GRUB, el proceso general termina, al seleccionar Continuar el equipo se reinicia y podemos iniciar en el nuevo sistema Debian instalado

![](/linux/imgdebian/40.png)

![](/linux/imgdebian/41.png)

![](/linux/imgdebian/42.png)


Con estos pasos tenemos una instalación de Debian 13, como se observo en los diferentes pasos, lo podemos configurar con las mejores opciones para nuestras necesidades.


## Primeros pasos
Al iniciar el sistema por primera vez se requiere la contraseña para el usuario creado, la escribimos y presionamos enter

![](/linux/imgdebian/43.png)

Al iniciar sesión se muestra una ventana de Bienvenida, podemos hacer el tour o bien, omitirlo

![](/linux/imgdebian/44.png)

En este caso vamos a seleccionar *Hacer el tour* y se muestra una ventana que podemos ir cambiando, en la cual, se muestran las características de Debian 13

![](/linux/imgdebian/45.png)

Al revisar todas las pantallas, cerramos la ventana

![](/linux/imgdebian/46.png)

Se muestra la pantalla inicial de Debian, en este caso al ser entorno Gnome, si presionamos la tecla de Windows se muestra el panel inferior y el menú para ver todas las aplicaciones instaladas del sistema

![](/linux/imgdebian/47.png)

![](/linux/imgdebian/48.png)

Buscamos la aplicación de Terminal y la abrimos, ingresamos como root con el siguiente comando y escribimos la contraseña configurada

```bash
$ su
```
![](/linux/imgdebian/49.png)

![](/linux/imgdebian/50.png)

Como root, ingresamos el comando para actualizar los repositorios del sistema y esperamos a que termine el proceso

```bash
$ apt update -y
```

![](/linux/imgdebian/51.png)

En caso de que se muestre un error como el siguiente: 
Error: El repositorio cdrom://[Debian GNU/Linux ...] trixie Release no tiene un fichero de Publicacion, vamos a editar el archivo sources.list mediando el siguiente comando

```bash
$ nano /etc/apt/sources.list
```
Comentamos con un *#* al inicio de la línea que inicia con deb cdrom

![](/linux/imgdebian/52.png)

Presiamos *Ctrl o* para guardar el archivo y *Ctrl x* para salir del editor nano
Realizamos de nuevo el comando de *apt update -y* y esperemos a que termine el proceso
Como podemos observar, ya no se muestra el error y nos indica que todos los paquetes están actualizados, en caso de que se muestren paquetes a actualizar, aplicar el comando

```bash
$ apt upgrade
```

![](/linux/imgdebian/53.png)

Instalamos una herramienta para ver la información del sistema mediante el comando

```bash
# sudo apt install fastfetch
```

![](/linux/imgdebian/54.png)

Ejecutar el siguiente comando para ver la información del sistema y comprobar la versión instalada **Debian GNU/Linux 13**

```bash
$ fastfetch
```
![](/linux/imgdebian/55.png)

Lo siguiente es instalar software adicional si es que lo requerimos, por ejemplo, podemos instalar vlc para reproducir videos/música 

```bash
# sudo apt install vlc
```

![](/linux/imgdebian/56.png)

Para buscar una aplicación, presionamos el botón de Windows en el teclado o clic en la esquina superior izquierda, en el cuadro de buscar escribimos VLC y confirmamos su instalación

![](/linux/imgdebian/57.png)

Por default el sistema viene con el navegador Firefox, si lo deseamos podemos instalar mas, por ejemplo Brave/Chrome/Edge, para el caso de Brave visitamos el sitio oficial [Brave](https://brave.com/) y clic en *Get Brave*, copiamos el comando que se muestra en *Install with one command* y lo ejecutamos desde la terminal


```bash
# curl -fsS https://dl.brave.com/install.sh | sh
```

En caso de que se muestre un error de que curl no fue encontrado, lo instalamos con el comando 

```bash
# apt install curl
```

Al terminar la instalación de curl, volvemos a llamar al comando para instalar brave y esperamos a que termine el proceso, buscamos la app y comprobamos que se instalo

![](/linux/imgdebian/58.png)

Instalamos *sudo* para configurar el usuario normal con permisos de root con los siguientes pasos

```bash
$ su -
# apt install sudo
```

Editamos el archivo sudoers con el siguiente comando 

```bash
# nano /etc/sudoers
```

En la sección de *User privilege specification* agregamos la siguiente línea, sustituyendo {usuario} por el nombre de usuario del sistema, en este caso debian 

```bash
{usuario}    ALL=(ALL:ALL) ALL
```

![](/linux/imgdebian/59.png)

Guardamos y cerramos el editor con  *Ctrl o* y *Ctrl x*

Ahora podemos ejecutar comandos de super usuario desde el usuario normal, por ejemplo, en este caso instalamos htop para monitoreo del sistema

```bash
$ sudo apt install htop
```

![](/linux/imgdebian/60.png)


De esta manera podemos seguir instalando todo el software necesario de acuerdo a nuestras necesidades y el uso que se da al sistema.


## Referencias

[Descarga Debian](https://www.debian.org/distrib/)

[Esquema de particionado](https://www.debian.org/releases/trixie/armhf/ch06s03.es.html#di-partition)
