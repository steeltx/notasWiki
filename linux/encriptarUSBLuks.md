# Encriptar memoria USB con LUKS

En esta guía se muestran las configuraciones para encriptar una memoria USB con LUKS

## Requerimientos
- Sistema Linux Mint (O derivados de Ubuntu)
- Acceso a terminal como root
- Memoria USB (sin datos)

## Software a instalar
- GParted

### Instalación de GParted

GParted es un editor de particiones para la gestión gráfica de particiones de disco.

Para instalar, abrir una terminal y escribir el siguiente comando

```bash
$ sudo apt install gparted
```

### LUKS

El sistema Linux Unified Key Setup-on-disk-format (LUKS) permite cifrar dispositivos de bloque y proporciona un conjunto de herramientas que simplifica la gestión de los dispositivos cifrados.

LUKS encripta dispositivos de bloques enteros y, por tanto, es muy adecuado para proteger el contenido de dispositivos móviles, como medios de almacenamiento extraíbles o unidades de disco de ordenadores portátiles.

De esta manera, podemos guardar información de un dispositivo de almacenamiento de forma cifrada y no se permite el acceso a menos de que el usuario ingrese las credenciales de autenticación correctas para poder ver los archivos almacenados.

### Crear unidad cifrada

Ingresar la memoria USB, que en este caso tiene el formato NTFS pero lo vamos a cambiar en un momento.

En Linux Mint tenemos una herramienta que ya viene instalada llamada discos, la buscamos en el menú de inicio y la abrimos.
Del lado izquierdo se muestran los dispositivos de almacenamiento conectados, seleccionamos en este caso la memoria USB y podemos ver la siguiente pantalla.

![](/linux/imgusbluks/1.png)

Asegurarse de seleccionar el dispositivo de almacenamiento correcto y que no contiene datos, en la parte superior derecha clic en los 3 puntos y seleccionar *Formatear disco...*

![](/linux/imgusbluks/2.png)

Se muestra la siguiente pantalla, en la opción de borrar dejar seleccionado *No sobreescribir datos existentes(Rápido)* y en particionado *Compatible con todos los sistemas y dispositivos(MBR/DOS)* para este caso.

![](/linux/imgusbluks/3.png)

Si se desea borrar completamente todos los datos para evitar una recuperación posterior, seleccionar en Borrar *Sobreescribir los datos existentes con ceros(Lento)*, en este caso al ser un dispositivo nuevo, no es necesario.

![](/linux/imgusbluks/4.png)

Clic en *Formatear...*, se muestra una ventana de confirmación, dar clic en *Formato* y esperar unos segundos a que termine el proceso.

![](/linux/imgusbluks/5.png)

Al terminar el proceso, en la sección de *Volúmenes* se muestra como espacio libre, anteriormente se mostraba como Partición 1 y NTFS.

![](/linux/imgusbluks/6.png)

Al hacer clic en el icono de + se muestra la ventana para crear una nueva partición, en este caso vamos a tomar todo el espacio disponible y no vamos a crear la partición extendida, clic en *Siguiente*

![](/linux/imgusbluks/7.png)

En la siguiente pantalla colocamos los valores:
- Nombre del volumen: Ingresar el nombre deseado, para este caso dejamos usb
- En la opción de borrar, la dejamos desactivada
- En tipo seleccionar *Disco interno para usarlo solamente con sistemas Linux(Ext4)* y activar la opción *Volumen protegido por contraseña(LUKS)*

*Nota: Tomar en cuenta que al formatear en Ext4 asi como se indica, solo vamos a poder leer la información en sistemas basados en Linux y con LUKS, si se desea encriptar para usar en Windows, optar por otra herramienta.*

Clic en *Siguiente*

![](/linux/imgusbluks/8.png)

Ingresar una contraseña segura y recordarla, en caso de que se olvide no podra ingresar a sus archivos, clic en *Crear*

![](/linux/imgusbluks/9.png)

Se va a cerrar la ventana de contraseña, en Tarea se va a mostrar *Creating Fylesystem*, esperar unos segundos a que termine de realizar el proceso.

![](/linux/imgusbluks/10.png)

Al terminar, se muestra la información del dispostivo, donde podemos ver la partición con formato Ext4y LUKS.

![](/linux/imgusbluks/11.png)

### Comprobar unidad cifrada

Cerramos la utilidad de discos y ahora vamos abrir GParted.
En la parte superior derecha seleccionamos la memoria USB y podemos ver la siguiente información:
- Se lista las particiones, para este caso solo es 1, en sistema de archivos se coloca como *[Cifrado] ext4*, con lo cual comprobamos que el proceso ha funcionado correctamente.

![](/linux/imgusbluks/12.png)

Al dar clic derecho sobre la partición se muestra una opción  *Cifrado cerrado*, lo cual nos indica que actualmente el cifrado se encuentra abierto, dar clic en esa opción.

![](/linux/imgusbluks/13.png)

Al realizar esta operación, podemos observar que en Sistema de archivos solo se muestra como *[Cifrado]* y no se muestra Ext4 o la información de espacio usado/libre.
Este es el comportamiento al conectar la memoria USB al equipo, cifrado cerrado por default.

![](/linux/imgusbluks/14.png)

### Leer/Escribir en la unidad

Abrir el administrador de archivos, en la sección de dispositivos seleccionar la memoria USB, al dar clic se muestra una ventana para ingresar la contraseña y poder ver los archivos, ingresarla y clic en *Conectar*

![](/linux/imgusbluks/15.png)

De esta manera podemos usar la memoria USB normalmente para leer/escribir archivos, al terminar asegurarse de retirarla de forma segura y la siguiente ocasión que se conecte, realizar el mismo proceso de colocar la contraseña, sin esta, no es posible acceder a los archivos almacenados.

![](/linux/imgusbluks/16.png)

## Referencias 

[GParted](https://gparted.org/)

[LUKS RedHat](https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/8/html/security_hardening/encrypting-block-devices-using-luks_security-hardening)

[LUKS Tecmint](https://www.tecmint.com/linux-hard-disk-encryption-using-luks/)

[LUKS sysdevlabs](https://www.sysdevlabs.com/es/articles/storage-technologies/what-is-luks-and-how-does-it-work/)