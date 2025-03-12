# Instalación de Manjaro Linux de forma encriptada

En esta guía se muestran las configuraciones para instalar la distro Manjaro por medio de KVM de forma encriptada.

## Requerimientos
- KVM instalado en alguna distro Linux / Equipo para instalar Manjaro con datos respaldados previamente (Instalación en todo el disco)
- Espacio en disco duro (al menos 30 GB)
- Sistema con capacidad de virtualización (En caso de usar KVM)

Para instalar KVM en Linux, podemos ver la siguiente guía

- [Creación de máquinas virtuales con KVM en Linux](/linux/kvmLinux.md)

## Creación de maquina virtual

En caso de realizar la instalación mediante KVM seguimos los siguientes pasos, en caso de que sea con un equipo físico, descargar la ISO y realizar una USB Booteable.

- Descargar imagen ISO desde el sitio oficial de Manjaro [Manjaro Descargar](https://manjaro.org/products/download/x86), para este caso usaremos el entorno de escritorio XFCE, pero se puede usar KDE Plasma, esto de acuerdo a los recursos del equipo donde se va a instalar.

![](imgmanjaro/1.png)

- Abrir el gestor de máquinas virtuales desde el menú de nuestro sistema.

![](imgmanjaro/2.png)

- Seleccionar el menú de archivo y clic en nueva maquina virtual.

![](imgmanjaro/3.png)

- Podemos instalar el sistema operativo de diferentes maneras, en este caso usaremos la primera opción de **Medio de instalación local (Imagen ISO o CDROM)** y clic en Forward.

![](imgmanjaro/4.png)

- Clic en explorar, buscar y seleccionar el archivo ISO descargado, clic en Forward.

![](imgmanjaro/5.png)

- Configurar la memoria y el CPU, tomar en cuenta el total existente en el sistema host, para este caso colocamos 4 GB de ram y 2 CPU, clic en Forward.

![](imgmanjaro/6.png)

- Configurar el total del almacenamiento deseado, en este caso colocamos 30 GB por ser de pruebas, tomar en cuenta de igual manera el total disponible de nuestro disco duro y el proposito de la maquina, clic en Forward.

![](imgmanjaro/7.png)

- En la siguiente pantalla, colocamos el nombre de la maquina y verificamos que las demas opciones esten correctamente, clic en finalizar.
  Por default, el disco duro virtual se almacena en **/var/lib/libvirt/images** con el formato **.qcow2**

![](imgmanjaro/8.png)

## Instalación del sistema operativo

- Una vez inicio el sistema, ya sea por medio de KVM o en un equipo mediante una memoria USB, se muestra la siguiente pantalla, enter en la opción por default **(Boot with open source drivers)**

![](imgmanjaro/9.png)

- Al terminar de cargar, podemos visualizar el sistema en modo live, con el objetivo de visualizar las opciones que nos presenta.

![](imgmanjaro/10.png)

- Cerramos la ventana de bienvenida y dar clic en el icono de escritorio **Install Manjaro Linux** para iniciar con el proceso.

![](imgmanjaro/11.png)

- Se abre el instalador, como primer paso debemos seleccionar la ubicación y el idioma preferido, en este caso dejamos Español México y clic en Siguiente.

![](imgmanjaro/12.png)

- La ubicación para este ejemplo seleccionamos Mexico City y clic en Siguiente.

![](imgmanjaro/13.png)

- Para la distribución del teclado, en este caso seleccionamos Spanish Latin American, en la sección de Teclee aqui para probar, podemos probar varias teclas y comprobar que efectivamente se muestren las que estamos presionando, en caso de que no, podemos seleccionar la distribución correcta de nuestro teclado desde las opciones que se presentan, clic en Siguiente.

![](imgmanjaro/14.png)

- Las particiones del disco es muy importante seleccionar/crear las correctas de acuerdo a lo que vamos a necesitar, para este caso vamos a borrar todos los datos del disco y realizar el particionamiento automático, seleccionar Borrar disco (Esto borrará todos los datos del dispositivo de almacenamiento seleccionado).

![](imgmanjaro/15.png)

- En las opciones de Swap, seleccionamos **Swap sin hibernación en ext4**, y marcamos la casilla de **Encriptar sistema**, en este apartado colocar una contraseña segura y que nos acordemos, ya que si la perdemos no podremos entrar a nuestros archivos de esta partición

![](imgmanjaro/16.png)

De esta manera, creamos todas las particiones necesarias para nuestro sistema Manjaro mediante el particionado automático que nos ofrece el instalador. 

La encriptación se realiza mediante **LUKS**, si desea conocer mas acerca de este tema, visitar el siguiente sitio: [LUKS](https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/8/html/security_hardening/encrypting-block-devices-using-luks_security-hardening#encrypting-block-devices-using-luks_security-hardening)

- Al terminar y estar seguros del particionado que se realiza, dar clic en Siguiente.

![](imgmanjaro/17.png)

- El siguiente paso es establecer los datos de la cuenta de usuario, para este ejemplo creamos el usuario manjaro, pero por seguridad, colocar otro diferente, creamos la contraseña de usuario y la contraseña de usuario administrador, lo recomendable es que sean diferentes para agregar mas seguridad al sistema, clic en Siguiente.

![](imgmanjaro/18.png)

- Nos pregunta si deseamos instalar un Software de ofimatica, dentro de las opciones contamos con LibreOffice o FreeOffice, si lo requerimos, lo podemos seleccionar, de igual manera este lo podemos instalar posteriormente, clic en siguiente.

![](imgmanjaro/19.png)

- La siguiente pantalla es un resumen de todas las configuraciones que realizamos, si todo esta correcto, clic en Instalar, posteriormente Install Now y esperar unos minutos a que termine el proceso.

![](imgmanjaro/20.png)

![](imgmanjaro/21.png)

![](imgmanjaro/22.png)

- Al terminar el proceso, marcamos la casilla Reiniciar ahora, Hecho y esperar.

![](imgmanjaro/23.png)

![](imgmanjaro/24.png)

## Inicio de Manjaro Linux

- Al iniciar el sistema, nos solicita la contraseña que definimos para la partición encriptada, la ingresamos y dar enter.

![](imgmanjaro/25.png)

![](imgmanjaro/26.png)

- Al ingresar correctamente la contraseña de la encriptación, se muestra el login del sistema, aqui colocamos la contraseña del usuario que configuramos en los pasos anteriores.

![](imgmanjaro/27.png)

- Si todo es correcto, podemos ingresar al sistema ya instalado en nuestro equipo

![](imgmanjaro/28.png)

- Vamos a comprobar que las particiones creadas si esten encriptadas, para ello vamos al menú del sistema o presionando la tecla Windows, escribimos **Gparted** y abrimos el primer resultado.

![](imgmanjaro/29.png)

- Esta aplicación nos permite ver las particiones del disco, por lo cual, nos solicita la contraseña de usuario administrador que hemos configurado durante el proceso de instalación.

![](imgmanjaro/30.png)

- Al abrir la aplicación, podemos ver las 2 particiones creadas y que se encuentran de forma cifrada.

![](imgmanjaro/31.png)

De esta manera, tenemos un sistema Manjaro Linux  instalado correctamente y podemos iniciar a instalar las aplicaciones que deseamos.

## Referencias
[Manjaro Linux](https://manjaro.org/)

[SWAP](https://aprendolinux.com/la-memoria-swap-en-linux/#:~:text=La%20memoria%20swap%2C%20tambi%C3%A9n%20conocida,trasladan%20a%20la%20memoria%20swap.)

[LUKS](https://docs.redhat.com/es/documentation/red_hat_enterprise_linux/8/html/security_hardening/encrypting-block-devices-using-luks_security-hardening#encrypting-block-devices-using-luks_security-hardening)
