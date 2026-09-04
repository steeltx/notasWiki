# Instalación de Red Hat Enterprise Linux (RHEL) desde Virtualbox en Linux

En esta guía se describen los pasos necesarios para instalar Red Hat Enterprise Linux (RHEL) en VirtualBox, utilizando una cuenta gratuita de Red Hat Developer para descargar la imagen ISO y registrar el sistema en el proceso de instalación.

## Requisitos
- Distro GNU/Linux (Ubuntu/Debian/Linux Mint)
  Para este ejemplo se usa **Linux Mint 22**
- Conexión a internet
- Cuenta gratuita de Red Hat Developer

## Instalación de Virtualbox

Ir al sitio oficial de virtualbox **https://www.virtualbox.org/** y clic en **Download**.

![](/img/rhel/1.png)

En la sección de VirtualBox Platform Packages para este caso, seleccionar **Linux distributions**, sin embargo, se puede instalar en Windows/mac.

![](/img/rhel/2.png)

A la fecha de creación de esta guía, la versión disponible de VirtualBox es la 7.2.16 para Linux. En la página de descarga se muestran diferentes paquetes según la distribución utilizada.
En nuestro caso, estamos utilizando Linux Mint 22, que está basado en Ubuntu 24.04 LTS. Por este motivo, debemos descargar el paquete correspondiente a Ubuntu 24.04 (Noble). Si estás utilizando una distribución diferente, selecciona y descarga el paquete que corresponda a tu distribución y versión.

![](/img/rhel/3.png)

Una vez finalizada la descarga, abre una terminal y ejecuta el siguiente comando para iniciar la instalación. Si el nombre o la versión del archivo descargado es diferente al utilizado en este ejemplo, reemplázalo por el nombre correspondiente.

```bash
sudo apt install ./virtualbox-7.2_7.2.16-174877~Ubuntu~noble_amd64.deb
```

Si la instalación se realizó correctamente, podemos abrir el menú de aplicaciones de nuestro sistema Linux Mint y buscar **Oracle VirtualBox**. Una vez localizado, seleccionamos la aplicación para iniciarla.

![](/img/rhel/4.png)

## Descarga de Red Hat Enterprise Linux (RHEL)

Para realizar este paso, es necesario contar con una cuenta gratuita de Red Hat Developer. Con esta cuenta, es posible descargar Red Hat Enterprise Linux (RHEL) para fines de desarrollo, aprendizaje, pruebas y uso personal, de acuerdo con los términos de la suscripción.

Para entornos empresariales o usos que requieran soporte y derechos de suscripción adicionales, es necesario contar con la suscripción de Red Hat correspondiente.

Vamos al sitio oficial de descargas **https://developers.redhat.com/products/rhel/download**, para este ejemplo utilizaremos RHEL 9.8. En la sección correspondiente a esta versión, localizamos la arquitectura **x86_64** y seleccionamos **Boot ISO**. Finalmente, hacemos clic en Download para iniciar la descarga de la imagen ISO.

![](/img/rhel/5.png)

Una vez finalizada la descarga, podemos verificar la integridad de la imagen ISO mediante su checksum SHA-256. Para ello, abrimos una terminal y ejecutamos el siguiente comando:

```bash
sha256sum rhel-9.8-x86_64-boot.iso
```

El comando mostrará el valor SHA-256 correspondiente al archivo descargado. Este valor debe compararse con el checksum publicado por Red Hat (sitio web) para confirmar que la imagen ISO se descargó correctamente y no ha sido modificada o dañada durante la descarga.

**Nota: Es posible que algunas versiones recientes de RHEL presenten problemas o errores durante la instalación en VirtualBox. Si esto ocurre, se recomienda probar con una versión anterior de RHEL y repetir el proceso de instalación.**

## Instalación en VirtualBox

Abrir VirtualBox y clic en **Nueva**

![](/img/rhel/6.png)

Ingresamos los siguientes datos:
- VM Name: Nombre de la máquina virtual, para este ejemplo rhel9
- VM Folder: La ruta donde se crean las VM, podemos dejar la que viene por default.
- ISO Image: La ISO que descargamos en los pasos anteriores.
- Proceed with Unattended Installation: Lo desmarcamos.

![](/img/rhel/7.png)

En la sección Hardware, la cantidad de recursos asignados a la máquina virtual dependerá de las características del equipo físico donde se ejecutará VirtualBox.
Para este ejemplo, asignaremos 4 GB de memoria RAM y 2 CPU a la máquina virtual. RHEL puede funcionar con 2 GB de RAM, por lo que, si el equipo físico cuenta con recursos limitados, es posible reducir la memoria asignada.
En la sección Disco, como esta máquina virtual se utilizará únicamente para realizar pruebas, asignaremos 20 GB de almacenamiento. Si se planea instalar aplicaciones adicionales, almacenar archivos o utilizar la máquina virtual para otros fines, se recomienda aumentar el tamaño del disco según las necesidades del entorno.

![](/img/rhel/8.png)

En la siguiente ventana se muestra un resumen de las configuraciones realizadas, si todo es correcto, clic en terminar.

![](/img/rhel/9.png)

Si deseamos que la VM tome una IP de nuestra red para acceder desde otro dispositivo, podemos dar clic en **Configuración > Red**, cambiar NAT a Adaptador de puente y seleccionar el adaptador correspondiente.

![](/img/rhel/10.png)

Al terminar de configurar, dar clic en **Iniciar**

![](/img/rhel/11.png)

Al iniciar el sistema, seleccionar la primera opción y enter.

![](/img/rhel/12.png)

*Nota: Al hacer clic dentro de la máquina virtual, el mouse quedará capturado por VirtualBox. Para devolver el control del mouse al sistema anfitrión (host), presiona dos veces la tecla Ctrl ubicada en el lado derecho del teclado.*

- Como primer paso, vamos a seleccionar el idioma a utilizar, para este ejemplo Español > Español (México) y clic en continuar.

![](/img/rhel/13.png)

- En en siguiente paso, vamos a configurar el destino de la instalación, para ello, clic en **Sistema > Destino de la instalación**

![](/img/rhel/14.png)

Como se mencionó anteriormente, esta será una instalación de pruebas, por lo que utilizaremos todo el disco disponible para la instalación de RHEL, para ello, seleccionamos el disco disponible y, en **Configuración de almacenamiento**, dejamos seleccionada la opción Automática. No habilitamos el cifrado y, finalmente, hacemos clic en **Hecho**.

![](/img/rhel/15.png)

- Configuramos el nombre del equipo, clic en **Red y nombre del equipo**

![](/img/rhel/16.png)

En la parte inferior, en **Nombre de equipo** para este ejemplo escribimos rhel9 y clic en **Aplicar**, posteriormente en **Hecho**

![](/img/rhel/17.png)

- Clic la sección de **Software > Conectar a Red Hat**

![](/img/rhel/18.png)

En este punto, nos va a solicitar los datos de acceso de la cuenta de desarrollador, ingresar los datos correspondientes y clic en **Registrarse**.

![](/img/rhel/19.png)

Si los datos son correctos, el sistema queda registrado en modo Acceso Simple a Contenido.

![](/img/rhel/20.png)

- El siguiente paso es la **Selección de software**, dar clic en dicha opción.

![](/img/rhel/21.png)

En esta sección, la selección dependerá del propósito que tendrá la máquina virtual. RHEL ofrece diferentes perfiles de instalación y paquetes adicionales que podemos elegir según nuestras necesidades.
Para este ejemplo, seleccionaremos **Servidor con GUI** y, en Software adicional, habilitaremos **Administración remota para Linux**.

Si la máquina virtual cuenta con recursos limitados, podemos seleccionar Servidor o Instalación mínima. Estas opciones no incluyen una interfaz gráfica, por lo que la administración del sistema se realizará mediante la línea de comandos.

En entornos de servidores de producción, es común utilizar instalaciones sin interfaz gráfica, ya que permiten reducir el consumo de recursos y administrar el sistema directamente desde la línea de comandos.

![](/img/rhel/22.png)

- Vamos a configurar la cuenta de root, para ello, clic en **Contraseña de root**

![](/img/rhel/23.png)

En esta sección, establecemos una contraseña para el usuario root. Para este ejemplo utilizaremos una contraseña corta, ya que se trata de una máquina virtual destinada a pruebas. En entornos reales, especialmente en servidores de producción, se recomienda utilizar contraseñas robustas y únicas, combinando mayúsculas, minúsculas, números y caracteres especiales, clic en **Hecho**.

![](/img/rhel/24.png)

- Clic en **Creación de usuario** 

![](/img/rhel/25.png)

Ingresamos el nombre, ID de usuario y contraseña, adicionalmente seleccionamos la casilla de **Hacer de este usuario un administrador** y clic en **Hecho**

![](/img/rhel/26.png)

- Con estos pasos, tenemos las configuraciones requeridas para realizar la instalación, para iniciar, clic en **Comenzar la instalación** (el proceso puede tardar un par de minutos).

![](/img/rhel/27.png)

![](/img/rhel/28.png)

- Al terminar la instalación, nos va a solicitar reiniciar, para ello, clic en **Reiniciar sistema**.

![](/img/rhel/29.png)

- Al terminar el reinicio, si se ha instalado la GUI, podemos ver la siguiente pantalla de inicio de sesión.

![](/img/rhel/30.png)

- Clic en el usuario que configuramos en los pasos anteriores (pruebas en este ejemplo) e ingresamos la contraseña.

![](/img/rhel/31.png)

![](/img/rhel/32.png)

- Si todos los pasos se realizaron correctamente, ya contamos con una instalación funcional de Red Hat Enterprise Linux (RHEL) 9 ejecutándose en VirtualBox.

![](/img/rhel/33.png)

Los siguientes pasos estarán enfocados en configurar e instalar el software necesario, de acuerdo con sus necesidades y el propósito de esta máquina virtual.

## Referencias

https://www.virtualbox.org/

https://developers.redhat.com/products/rhel

