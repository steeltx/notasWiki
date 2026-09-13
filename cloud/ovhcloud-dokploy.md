# Instalación y configuración de Dokploy en OVHcloud para el despliegue de aplicaciones

Esta guía describe los pasos necesarios para crear, instalar y configurar una máquina virtual (instancia) en OVHcloud, utilizando GNU/Linux (Debian 13).

A lo largo de la guía se abordará la instalación y configuración de Dokploy, el despliegue y administración de una base de datos PostgreSQL, la configuración de acceso mediante un dominio con HTTPS y la implementación de respaldos automáticos utilizando S3 Object Storage.

## Introducción

**¿Qué es OVHcloud?**
OVHcloud ofrece soluciones de cloud público y privado, alojamiento compartido y servidores dedicados en 140 países de todo el mundo. Asimismo, proporciona a sus clientes servicios para el registro de dominios, telefonía y acceso a internet.

[Fuente de información](https://www.ovhcloud.com/es/about-us/)

**¿Que es Dokploy?**

Dokploy es una solución de despliegue estable y fácil de usar, diseñada para simplificar la gestión de aplicaciones. Piensa en Dokploy como una alternativa gratuita y autoalojada a plataformas como Heroku, Vercel y Netlify, que aprovecha la robustez de Docker y la flexibilidad de Traefik.

[Fuente de información](https://docs.dokploy.com/docs/core)

## Requisitos

- Cliente Windows/Linux para administrar la VM
  Para este ejemplo se usa **Debian 13**
- Conexión a internet
- Cuenta en OVHcloud para Public Cloud
  **Nota:  El uso de OVHcloud Public Cloud genera costos. Antes de implementarlo, se recomienda consultar y revisar la información de precios vigente de OVHcloud para conocer los cargos aplicables.**
- En la VM, Dokploy recomienda como requisitos mínimos 2GB de RAM y 30GB de disco
- Cuenta de No IP para dominio (DDNS)

## Creación de VM en OVHcloud

Como primer paso, debemos ingresar al panel de administración de OVHcloud utilizando una cuenta con acceso a Public Cloud.

OVHcloud ofrece una modalidad de Public Cloud Free Trial que incluye 200 USD en créditos para realizar pruebas durante un periodo de 1 mes. Para esta guía, es posible utilizar esta modalidad de prueba o, alternativamente, una cuenta con esquema de pago por uso (Pay As You Go).

**Importante: Tanto el uso de los créditos de prueba como, especialmente, el uso de una cuenta de pago por uso puede estar sujeto a cargos, dependiendo de los servicios y recursos utilizados. Se recomienda revisar los precios y las condiciones vigentes de OVHcloud antes de realizar la implementación.**

- En un navegador web ingresamos en la URL de OVH https://www.ovhcloud.com/en/, vamos a la sección de **My customer account** e iniciamos sesión.

![](/img/ovhdokploy/1.png)

- Desde el menú lateral, ir a Public Cloud > Seleccionar el proyecto (o crear uno si es la primera vez que ingresamos) > Compute > Instancias

![](/img/ovhdokploy/2.png)

Dar clic en **Crear una instancia**, ingresamos los siguientes datos:

- Nombre de instancia: Podemos colocar cualquiera, para este ejemplo *vmtest*.
- Localización: En este ejemplo usaremos *Región 1-AZ*, pero depende de las necesidades del proyecto, si se requiere alta disponibilidad se puede seleccionar **Región 3-AZ**.
- Zona geográfica: Para este ejemplo, se utilizará la región **Beauharnois (BHS)**. Es recomendable seleccionar una zona geográfica cercana a la región donde se encuentran los usuarios que accederán a la VM, con el objetivo de reducir la latencia y mejorar el rendimiento del acceso.

![](/img/ovhdokploy/3.png)

- Modelo: La selección del modelo de la máquina virtual depende principalmente del uso que se le dará a la VM y de los recursos necesarios para ejecutar los servicios. Para nuestro caso, como se determinó previamente, la configuración requerida es de 2 GB de RAM y 30 GB de almacenamiento. Por ello, seleccionamos el modelo **Discovery > d2-4**, que cumple con los requerimientos definidos para este entorno. OVHcloud ofrece diferentes modelos y capacidades de máquinas virtuales, por lo que esta selección puede variar según las necesidades de cada proyecto, como la cantidad de memoria RAM, capacidad de almacenamiento, procesamiento y carga de trabajo esperada.

- Imagen: OVHcloud ofrece diferentes imágenes de sistemas operativos que podemos utilizar para configurar la máquina virtual. Entre las opciones disponibles se encuentran distribuciones compatibles con RHEL, como AlmaLinux y Rocky Linux, así como distribuciones basadas en Debian, como Debian y Ubuntu. Para nuestro caso, seleccionamos Debian versión 13, debido a que cumple con los requisitos del entorno y será el sistema operativo sobre el cual se instalará y configurará Dokploy.

![](/img/ovhdokploy/4.png)

- Llave SSH: Este paso es muy importante, ya que necesitaremos una llave SSH para autenticarnos y conectarnos de forma segura a la máquina virtual. En lugar de utilizar una contraseña, utilizaremos un par de claves compuesta por una clave privada y una clave pública. Desde un equipo con Linux podemos generar una llave SSH dedicada exclusivamente a nuestro servidor de OVHcloud (para mayor seguridad) mediante el siguiente comando:

```bash
ssh-keygen -t ed25519 -a 100 -f ~/.ssh/ovh_ed25519 -C "ovh"
```

El comando generará dos archivos:
~/.ssh/ovh_ed25519: clave privada. Debe mantenerse segura y no compartirse.
~/.ssh/ovh_ed25519.pub: clave pública, que se usa para OVHcloud

Durante el proceso, el sistema solicitará una contraseña (passphrase) para proteger la clave privada. Se recomienda establecer una para añadir una capa adicional de seguridad.

Una vez que se generen las llaves, podemos ver el contenido de la llave .pub y colocarla en OVHcloud.

```bash
cat ~/.ssh/ovh_ed25519.pub
```

- Configuración de la copia de seguridad: Para este ejemplo lo desactivamos, si es necesario lo podemos activar, pero recordar que genera costo extra por GB usado.

![](/img/ovhdokploy/5.png)

- Red: Podemos dejar el nombre de la red privada por default o cambiarla si asi lo requerimos, CIDR lo dejamos por default, habilitar DHCP y muy importante, Asignar una IP pública, sin esto, no podemos conectarnos a la VM desde internet.

![](/img/ovhdokploy/6.png)

- Facturación: En este apartado podemos seleccionar entre diferentes modalidades de facturación, dependiendo del tiempo durante el cual utilizaremos la máquina virtual. Si la VM permanecerá activa durante un mes completo o más, podemos seleccionar la modalidad Mensual. En cambio, si únicamente necesitamos utilizarla durante algunos días o por un periodo corto, es conveniente seleccionar la modalidad Por horas, ya que el costo se calcula en función del tiempo de uso. Para este caso, seleccionamos **Por horas** ya que solo estara en uso poco tiempo.

- Configuración avanzada: No activamos un script de post-instalación.

![](/img/ovhdokploy/7.png)

- Resumen: Del lado derecho se muestra el resumen de las configuraciones seleccionadas y el costo estimado, si es correcto y estamos de acuerdo, clic en **Lanzar mi instancia**.

![](/img/ovhdokploy/8.png)

El proceso de la creación puede tardar unos minutos.

![](/img/ovhdokploy/9.png)

Después de unos minutos, la máquina virtual estará creada y podremos visualizar la instancia desde el panel de OVHcloud.
En este apartado podremos consultar la información y configuración de la VM, incluyendo la dirección IP pública, que utilizaremos posteriormente para establecer la conexión con el servidor mediante SSH.

![](/img/ovhdokploy/10.png)

Si damos clic en el nombre de la VM, entramos a sus detalles y podemos ver los datos de conexión.

![](/img/ovhdokploy/11.png)

## Configuración inicial de Debian 13

Para conectarnos, abrimos una terminal desde Linux (en Windows podemos usar un cliente de SSH) y ejecutamos el siguiente comando, cambiando la IP por la que se muestra en el panel de OVH.

```bash
ssh -i ~/.ssh/ovh_ed25519 usuario@ip
```

Al realizar la conexión por SSH por primera vez, nuestro equipo no reconocerá la huella digital (fingerprint) del servidor, por lo que se mostrará un mensaje solicitando confirmar si confiamos en la máquina a la que nos estamos conectando.
Para continuar, escribimos yes y presionamos Enter. La huella digital quedará registrada en nuestro equipo para futuras conexiones.
Si al momento de generar la llave SSH configuramos una contraseña (passphrase), también se nos solicitará introducirla para poder utilizar la clave privada y completar la conexión.

```bash
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?  yes
```
Si el proceso es correcto, nos conectamos a nuestra VM.

![](/img/ovhdokploy/12.png)

Actualizar el sistema e instalar las utilidades que vamos a utilizar mediante los siguientes comandos.

```bash
sudo apt update
sudo apt upgrade
sudo apt install curl ca-certificates wget htop
```

![](/img/ovhdokploy/13.png)

En caso de que el kernel sea actualizado, reiniciar con reboot y volver a conectarse posteriormente.

```bash
sudo reboot
```

Podemos ver los recursos que esta usando la VM actualmente con la utilidad de htop que instalamos en el paso anterior, para ello ejecutar:

```bash
htop
```

Para salir, escribimos la letra **q**

![](/img/ovhdokploy/14.png)

## Instalación de Dokploy en VM

Una vez nos conectamos al servidor, instalar Dokploy es muy sencillo, con 1 solo comando nos permite instalar todo lo necesario. Dokploy usa Docker, pero si no esta instalado previamente en el servidor, con el comando se instala automáticamente.

```bash
sudo su
curl -sSL https://dokploy.com/install.sh | sh
```

![](/img/ovhdokploy/15.png)

Este proceso puedo tardar un par de minutos, al terminar, si no nos muestra errores, podemos ingresar de la siguiente manera desde un navegador web **ip-servidor:3000**, 3000 es el puerto de administración usado por default en Dokploy.

![](/img/ovhdokploy/16.png)

Como primer paso, vamos a crear una cuenta de administrador, para ello, ingresar el email y una contraseña segura, clic en **Register**.

**Nota: Para este ejemplo estamos usando HTTP (ingresando desde la IP pública), lo recomendable es usar HTTPS, para ello es recomendable contar con un dominio y realizar las configuraciones necesarias.**

Al crear la cuenta, entramos al panel de administración de Dokploy donde nos muestra unos pasos iniciales, en nuestro caso, vamos a dar clic en **SKIP ALL**.

![](/img/ovhdokploy/17.png)

De esta manera, ingresamos al Dashboard de Dokploy instalado en una instancia de OVHcloud.

![](/img/ovhdokploy/18.png)

## Instalación de PostgreSQL en Dokploy

Una vez instalado Dokploy, lo primero que necesitamos es crear un proyecto, para ello clic en **Projects > Create Project**

![](/img/ovhdokploy/19.png)

Ingresamos un nombre, para nuestro caso *test-project* y clic en **Create**

![](/img/ovhdokploy/20.png)

Por default, al crear el proyecto, se crea el entorno de producción, vamos a crear otro para **desarrollo**, haciendo clic en **Create Environment**

![](/img/ovhdokploy/21.png)

![](/img/ovhdokploy/22.png)

![](/img/ovhdokploy/23.png)

Para crear nuestro primer servicio, clic en **Create Service**, se muestran diferentes opciones, en este caso vamos a seleccionar **Database**

![](/img/ovhdokploy/24.png)

Dokploy cuenta con diferentes opciones de servicios preconfigurados que podemos desplegar directamente desde su panel. En este caso, seleccionaremos **PostgreSQL** y completaremos los datos solicitados para configurar el servicio, de acuerdo con los requerimientos de nuestro proyecto.

![](/img/ovhdokploy/25.png)

![](/img/ovhdokploy/26.png)

Al crear el servicio, se muestra en el proyecto y entorno seleccionado.

![](/img/ovhdokploy/27.png)

Al hacer clic en **db_test** entramos a los detalles del servicio, posteriormente clic en **Deploy**.

![](/img/ovhdokploy/28.png)

![](/img/ovhdokploy/29.png)

Inicia el proceso de descarga de la imagen y creación del servicio, el proceso puede tardar un par de minutos.

![](/img/ovhdokploy/30.png)

Una vez finalizado el deploy, podremos comprobar que el servicio se encuentra activo mediante el icono verde que aparece junto al nombre del servicio. 

En la pestaña General encontraremos la sección **Internal Credentials**, que contiene las credenciales y datos necesarios para establecer conexiones internas con la base de datos. Estas conexiones se realizan dentro de la infraestructura y no requieren exponer la base de datos directamente a Internet.

Por ejemplo, si posteriormente desplegamos una aplicación backend, como una API REST que necesite conectarse a PostgreSQL, lo recomendable es utilizar las credenciales y la conexión interna proporcionadas por Dokploy. De esta manera, evitamos exponer el servicio de base de datos públicamente y mejoramos la seguridad de nuestra infraestructura.

![](/img/ovhdokploy/31.png)

En caso de que necesitemos conectarnos a la base de datos directamente desde Internet, podemos utilizar la sección **External Credentials**. En este apartado debemos habilitar el acceso externo e indicar el puerto mediante el cual se realizará la conexión, por ejemplo, 5435.

Para este ejemplo, habilitaremos la conexión externa para mostrar su configuración. Sin embargo, no se recomienda exponer la base de datos directamente a Internet cuando no sea necesario. Siempre que sea posible, debemos utilizar las conexiones internas para que las aplicaciones que se encuentran dentro de nuestra infraestructura se comuniquen con la base de datos de forma más segura.

![](/img/ovhdokploy/32.png)

Una vez indicado el puerto, Dokploy habilitará la conexión externa y mostrará una URL de conexión pública. Esta URL puede utilizarse para conectarnos a la base de datos desde cualquier cliente compatible con PostgreSQL o desde una aplicación backend que requiera acceso a la base de datos.

Es importante recordar que, al habilitar esta opción, la base de datos queda accesible desde Internet. Por ello, se recomienda utilizar conexiones externas únicamente cuando sea estrictamente necesario y, en caso de habilitarlas, aplicar medidas adicionales de seguridad como restricciones de acceso, credenciales seguras, firewall, etc.

![](/img/ovhdokploy/33.png)

### Conexión desde cliente a PostgreSQL

Desde Windows/Linux podemos usar un cliente de base de datos compatible con PostgreSQL, en nuestro caso vamos a usar DBeaver Community, abrimos la aplicación y creamos una nueva conexión de tipo PostgreSQL, clic en Next.

![](/img/ovhdokploy/34.png)

En **host**, colocamos en valor de Dokploy (IP) para conexiones externas, en **database** ingresamos el nombre de la base de datos, para este ejemplo *db_test*, en **port** *5435* para nuestro caso, ingresamos el usuario y contraseña configurados.

![](/img/ovhdokploy/35.png)

Si los datos de conexión proporcionados son correctos, podemos establecer la conexión correctamente con la base de datos que hemos desplegado como servicio en Dokploy.

![](/img/ovhdokploy/36.png)

![](/img/ovhdokploy/37.png)

### Resumen de la configuración.

En este punto, hemos completado las configuraciones necesarias para disponer de una instancia de PostgreSQL funcional en nuestro entorno. Hasta ahora hemos realizado los siguientes pasos:

- Creación de una instancia de Public Cloud en OVHcloud.
- Configuración inicial del sistema operativo Debian 13.
- Instalación y configuración inicial de Dokploy.
- Creación del proyecto, entorno y servicio de base de datos PostgreSQL.
- Configuración y prueba de la conexión externa mediante un cliente compatible.

Con estas configuraciones, ya contamos con una base de datos PostgreSQL completamente funcional, a la que podemos conectarnos mediante conexiones internas, opción recomendada por motivos de seguridad, o mediante conexiones externas cuando sea necesario acceder desde clientes o aplicaciones que se encuentren fuera de nuestra infraestructura.

![](/img/ovhdokploy/38.png)

## Agregar Dominio con No IP

Hasta este momento, hemos estado accediendo al servidor directamente mediante su dirección IP. Aunque este método funciona, no es la opción más recomendable para un entorno de producción. Para facilitar el acceso y contar con una configuración más profesional, podemos asociar un nombre de dominio o subdominio al servidor.

Si ya contamos con un dominio propio, podemos utilizarlo para crear un subdominio y vincularlo con la dirección IP del servidor. Por otro lado, si no disponemos de un dominio, una alternativa práctica para entornos de pruebas es utilizar No-IP, que permite crear hasta 3 nombres de host con una cuenta gratuita. A continuación, veremos los pasos necesarios para crear y configurar un subdominio utilizando No-IP.

- Ingresamos al sitio de No IP https://www.noip.com/ y creamos una cuenta gratis.
- Una vez en el panel de administración, vamos a crear el Hostname.

![](/img/ovhdokploy/39.png)

- En la sección de registros, clic en **Crear nombre de host**

![](/img/ovhdokploy/40.png)

- Ingresar los siguientes datos:
  Type : A
  Host: Podemos ingresar un nombre de host que este disponible, para nuestro caso, vamos a colocar *dokploy-pruebas-ovh*
  Dominio: Contamos con una lista de dominios disponibles, para este ejemplo seleccionamos ddns.net
  IPv4: Colocamos la IP de la instancia de OVHcloud, la cual podemos consultar desde el panel de administración de OVH
  TTL: Auto

![](/img/ovhdokploy/41.png)

- Si el hostname se encuentra disponible, se crea el registro.

![](/img/ovhdokploy/42.png)

- Ahora ingresamos al panel de Dokploy desde la ip y el puerto 3000, posteriormente **Settings > Web Server > Server Domain**

![](/img/ovhdokploy/43.png)

- Antes de continuar, debemos esperar a que el subdominio resuelva correctamente hacia la dirección IP de nuestro servidor. Este proceso, conocido como propagación DNS, puede tardar desde unos minutos hasta varias horas, dependiendo de la configuración y los servidores DNS involucrados. Para comprobar si el dominio ya está resolviendo correctamente, podemos utilizar una herramienta como DNSChecker y verificar que el registro DNS apunte a la IP de nuestro servidor.

- En el campo Domain, ingresamos el dominio o subdominio que configuramos previamente en No-IP. A continuación, ingresamos una dirección de correo electrónico. Esta dirección se utilizará para la generación y gestión de los certificados SSL mediante Let's Encrypt. Finalmente, habilitamos la opción HTTPS y hacemos clic en Save para guardar la configuración.

![](/img/ovhdokploy/44.png)

- Esperamos unos momentos y, posteriormente, intentamos acceder al servidor utilizando el dominio que configuramos previamente. Ya no es necesario especificar el puerto 3000, ya que al habilitar HTTPS, Dokploy configura automáticamente el acceso seguro a través del puerto 443, que es el puerto estándar para conexiones HTTPS.

![](/img/ovhdokploy/45.png)

- Con esto, ya podemos acceder al panel de administración de Dokploy mediante HTTPS, utilizando nuestro dominio en lugar de la dirección IP y el puerto 3000. Como medida de seguridad, podemos deshabilitar el acceso directo al puerto 3000, de modo que el panel únicamente sea accesible a través del dominio configurado mediante HTTPS. Para ello, nos conectamos al servidor y ejecutamos el siguiente comando:

```bash
sudo docker service update \
  --publish-rm "published=3000,target=3000,mode=host" \
  dokploy
```

![](/img/ovhdokploy/46.png)

- Una vez finalizado el proceso, si intentamos acceder nuevamente mediante IP:3000, la conexión ya no estará disponible. Esto es correcto y significa que el acceso directo por el puerto 3000 ha sido deshabilitado.

**Nota: Antes de aplicar esta configuración, debemos asegurarnos de que podemos acceder correctamente al panel de Dokploy mediante el dominio configurado con HTTPS. De lo contrario, podríamos perder el acceso al panel de administración.**

## Configurar Firewall para conexiones externas de PostgreSQL

En pasos anteriores, configuramos el acceso a la base de datos desde Internet. Aunque esta configuración puede ser útil para realizar conexiones remotas, no es recomendable mantener el acceso abierto a cualquier dirección IP, ya que representa un riesgo de seguridad.

Para mejorar la seguridad, en los siguientes pasos configuraremos el Firewall de OVHcloud para permitir únicamente las conexiones provenientes de la dirección IP de nuestro cliente. De esta forma, el acceso a la base de datos quedará restringido y no estará disponible públicamente para cualquier usuario de Internet.

- Como primer paso, debemos obtener la dirección IP pública desde la cual se realizará la conexión a la base de datos. Si la conexión se realizará desde nuestro propio equipo, podemos consultar nuestra IP pública utilizando un servicio de este tipo, como NordVPN. 

- Desde el panel de OVHcloud, vamos a **Public Cloud > Compute > Instancias**, seleccionamos la instancia que creamos y entramos en la sección de **Información general**, en la parte de Redes donde se encuentra IPv4, clic en los 3 puntos y posteriormente en **Configurar el firewall**.

![](/img/ovhdokploy/47.png)

- Otra opción es ingresar desde **Network > Red pública > Direcciones IP públicas**, seleccionar la IP de la instancia, clic en los 3 puntos y **Configurar el Edge Network Firewall**

![](/img/ovhdokploy/48.png)

- Si en este punto lo activamos, al no contar con reglas, vamos a perder el acceso, por ello, primero debemos crear las reglas, Clic en **Añadir una regla**

![](/img/ovhdokploy/49.png)

- Creamos 3 reglas:
  - Modo Autorizar, TCP, puerto destino 22 (Para conexión SSH)
  - Modo Autorizar, TCP, puerto destino 443 (Para conexión HTTPS)
  - Modo Autorizar, TCP, puerto destino 5435 (Para conexión de PostgreSQL)
    
En la tercera regla, lo recomendado en dirección de origen es colocar la IP pública del cliente que revisamos en pasos anteriores para mayor seguridad.

- Al crear las reglas, lo vamos activar.

![](/img/ovhdokploy/50.png)

![](/img/ovhdokploy/51.png)

Después de unos segundos, podemos verificar que las reglas del Firewall se hayan aplicado correctamente realizando conexiones mediante HTTPS, SSH y PostgreSQL.

Estas configuraciones son básicas y pueden adaptarse según las necesidades de cada entorno. Por ejemplo, es posible restringir el acceso por direcciones IP específicas, rangos de IP, puertos, protocolos y otros criterios, permitiendo aplicar un mayor nivel de seguridad a nuestro servidor.

## Respaldo de PostgreSQL en S3

Para entornos de producción, es recomendable implementar una estrategia de respaldos (backups) que permita recuperar la información en caso de pérdida de datos, errores de configuración o incidentes en la infraestructura.

Dokploy permite configurar respaldos utilizando proveedores de almacenamiento compatibles con el protocolo S3. Como nuestra infraestructura se encuentra en OVHcloud, utilizaremos el servicio Object Storage para almacenar los respaldos de nuestra base de datos.

En los siguientes pasos configuraremos Object Storage y posteriormente lo integraremos con Dokploy para automatizar el almacenamiento de nuestros respaldos.

**Nota: Los respaldos que configuraremos a continuación no estarán cifrados. Si se requiere almacenar los respaldos de forma cifrada, podemos realizar el proceso manualmente utilizando herramientas como Restic, age, entre otras.**

- Como primer paso, necesitamos crear un bucket en Object Storage, para ello, puedes consultar la siguiente guía: [Uso de OVHcloud Object Storage desde CLI en Linux](/cloud/ovhcloud-object-storage-cli.md) donde se muestran los pasos necesarios para crear el bucket.

![](/img/ovhdokploy/52.png)

Una vez creado el bucket, por ejemplo, de nombre *bucket-prueba-dokploy-respaldos*, en Dokploy vamos a **Projects > test-project > entorno desarrollo > servicio db_test > Backups** y clic en **S3 Destinations**.

![](/img/ovhdokploy/53.png)

Hacemos clic en **Add Destination**.

![](/img/ovhdokploy/54.png)

Ingresamos los siguientes datos:
- Name: Un nombre, por ejemplo, *s3-ovh*
- Provider: Como vamos a usar OVHcloud Object Storage, no se encuentra en la lista, seleccionamos *Any other S3 compatible provider*
- Access Key Id: Access Key generado por OVHcloud
- Secret Access Key: Secret Key generado por OVHcloud
- Bucket: Nombre de bucket creado, para este caso *bucket-prueba-dokploy-respaldos*
- Region: La región donde se creo el bucket en OVH, por ejemplo, *bhs*
- Endpoint: endpoint S3 que OVHcloud muestra para tu Object Storage

Clic en **Test connection**, si todos los datos son correctos, la la conexión se realiza de forma correcta, clic en **Create**

![](/img/ovhdokploy/55.png)

![](/img/ovhdokploy/56.png)

Regresamos nuevamente a **Projects > test-project > entorno desarrollo > servicio db_test > Backups**, clic en **Create backup**

![](/img/ovhdokploy/57.png)

Ingresamos los siguientes datos:
- Destination: Seleccionar la fuente de S3 que acabamos de crear.
- Database: db_test para este ejemplo.
- Schedule: Seleccionar cuando se hacen los respaldos, en este caso podemos seleccionar diario, pero si lo deseamos, podemos personalizar.
- Prefix: para que sea sencillo identificarlo, podemos agregar postgres/db_test.
- Keep the latest: Define el período de retención de los respaldos. Si dejamos este campo en blanco, los respaldos no se eliminarán automáticamente. Por ejemplo, si queremos conservar únicamente los respaldos de los últimos 15 días, debemos ingresar 15. Los respaldos que superen este período serán eliminados automáticamente.
- Enabled: Activado

Clic en **Create**

![](/img/ovhdokploy/58.png)

De esta manera, hemos completado la configuración necesaria para que los respaldos de la base de datos se realicen de forma automática.
En entornos de producción, podemos establecer una frecuencia de respaldo más alta, por ejemplo, cada hora, o definir el intervalo que mejor se adapte a las necesidades y requisitos de cada proyecto.

Vamos a realizar una prueba para verificar que esta funcionando, para ello, clic en **Run Manual Backup**

![](/img/ovhdokploy/59.png)

El tiempo necesario para generar un respaldo dependerá del tamaño de la base de datos. Por lo general, el proceso no suele tardar demasiado, aunque puede variar según el volumen de información. Una vez finalizado el proceso, regresamos al panel de OVHcloud y accedemos al bucket que creamos anteriormente para verificar que los archivos de respaldo se hayan generado correctamente.

![](/img/ovhdokploy/60.png)

![](/img/ovhdokploy/61.png)

## Conclusión

Despues de realizar todos estos pasos, contamos con la siguiente configuración:

- Creación de una instancia de Public Cloud en OVHcloud.
- Configuración inicial del sistema operativo Debian 13.
- Instalación y configuración inicial de Dokploy.
- Creación del proyecto, entorno y servicio de base de datos PostgreSQL.
- Configuración y prueba de la conexión externa mediante un cliente compatible.
- Acceso por dominio creado en NO IP
- Configuración de firewall de OVHcloud
- Acceso al panel de administración de Dokploy desde dominio con HTTPS, se quita el acceso desde el puerto 3000
- Configuración de respaldo de base de datos de PostgreSQL en OVH Object Storage

A esta configuración se puede agregar mas cosas, dependiendo de cada proyecto, por ejemplo:

- Firewall en servidor Debian 13 (nftables, iptables, etc).
- Mejorar configuración de acceso SSH.
- Respaldos manuales de base de datos y encriptado con restic, etc.
- Acceso desde VPN

## Fuentes de información

https://dokploy.com/

https://www.ovhcloud.com/es/
