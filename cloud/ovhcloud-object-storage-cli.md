# Uso de OVHcloud Object Storage desde CLI en Linux

Esta guía describe los pasos necesarios para instalar y configurar el CLI de Amazon S3 en un equipo con GNU/Linux para utilizarlo con OVHcloud. El objetivo es permitir la gestión y administración del servicio **Object Storage** directamente desde la línea de comandos.

## Introducción

**¿Qué es OVHcloud?**
OVHcloud ofrece soluciones de cloud público y privado, alojamiento compartido y servidores dedicados en 140 países de todo el mundo. Asimismo, proporciona a sus clientes servicios para el registro de dominios, telefonía y acceso a internet.

[Fuente de información](https://www.ovhcloud.com/es/about-us/)

**Object Storage**
El almacenamiento de objetos **object storage** maneja grandes volúmenes de datos no estructurados y convierte cada punto de datos en una unidad diferenciada (un objeto) con sus propios metadatos y un identificador único. Estos objetos se almacenan en un entorno de datos, facilitando así el acceso, la recuperación y la gestión de todos estos objetos individuales.

**Casos de uso para Object Storage**
- Aplicaciones nativas de cloud
- Contenido multimedia
- Analítica e IA
- Internet de las cosas
- Backup

[Fuente de información](https://www.ovhcloud.com/es/learn/what-is-object-storage/)

## Requisitos
- Distro GNU/Linux (Ubuntu/Debian/Linux Mint)
  Para este ejemplo se usa **Debian 13**
- Conexión a internet
- Cuenta en OVHcloud para Public Cloud
  **Nota:  El uso de OVHcloud Object Storage puede generar costos. Antes de implementarlo, se recomienda consultar y revisar la información de precios vigente de OVHcloud para conocer los cargos aplicables.**

## Configuración en OVHcloud

Como primer paso, debemos ingresar al panel de administración de OVHcloud utilizando una cuenta con acceso a Public Cloud.

OVHcloud ofrece una modalidad de Public Cloud Free Trial que incluye 200 USD en créditos para realizar pruebas durante un periodo de 1 mes. Para esta guía, es posible utilizar esta modalidad de prueba o, alternativamente, una cuenta con esquema de pago por uso (Pay As You Go).

**Importante: Tanto el uso de los créditos de prueba como, especialmente, el uso de una cuenta de pago por uso puede estar sujeto a cargos, dependiendo de los servicios y recursos utilizados. Se recomienda revisar los precios y las condiciones vigentes de OVHcloud antes de realizar la implementación.**

- En un navegador web ingresamos en la URL de OVH https://www.ovhcloud.com/en/, vamos a la sección de **My customer account** e iniciamos sesión.

![](/img/ovhobjectstorage/1.png)

- Desde el menú lateral, ir a Public Cloud > Backup Storage > Object Storage

![](/img/ovhobjectstorage/2.png)

- Clic en *Crear un contenedor de objetos*

- Ingresamos los siguientes datos:
  - Nombre del contenedor: Para este ejemplo *prueba-contenedor-ovh*.
  - Tipo de contenedor: **API compatible con S3**.
  - Localización: En este ejemplo usaremos *Región 1-AZ*, pero depende de las necesidades del proyecto, si se requiere alta disponibilidad se puede seleccionar **Región 3-AZ**.
  ![](/img/ovhobjectstorage/3.png)
  - Zona geográfica: Para este ejemplo, se utilizará la región **Beauharnois (BHS)**. Es recomendable seleccionar una zona geográfica cercana a la región donde se encuentran los usuarios que accederán al almacenamiento, con el objetivo de reducir la latencia y mejorar el rendimiento de las operaciones de acceso a los objetos.
  - Control de versiones: Desactivar, si lo requiere, lo puede activar.
  - Object Lock: Desactivar, si lo requiere, lo puede activar.
  ![](/img/ovhobjectstorage/4.png)
  - Usuario: Al ser el primer proyecto, en este caso no contamos con usuarios registrados, para ello, vamos a dar clic en **Crear un nuevo usuario**, ingresamos un nombre, por ejemplo *usuario_object_ovh* y clic en **Crear**.
  ![](/img/ovhobjectstorage/5.png)
  ![](/img/ovhobjectstorage/6.png)
  - Al terminar el proceso de la creación del usuario, se muestra la información de acceso y tenemos la opción de descargar los datos (guardalos de forma segura).
  ![](/img/ovhobjectstorage/7.png)
  - Cifrado de los datos: Seleccionamos **Cifrado del lado del servidor con claves gestionadas por OVHcloud (SSE-OMK)**
  ![](/img/ovhobjectstorage/8.png)
  - Al terminar toda la configuración, del lado derecho se muestra un resumen, si todo es correcto, clic en **Crear**.
  ![](/img/ovhobjectstorage/9.png)

- Después de unos segundos, nos redirecciona al contenedor que acabamos de crear.
![](/img/ovhobjectstorage/10.png)

## Instalación y configuración de AWS CLI en Linux

Desde un equipo GNU/Linux, vamos ir a la documentación oficial de AWS, nos indica el comando para instalar, para ello, abrimos una terminal desde Linux y escribimos el comando.

```bash
sudo apt install curl unzip
curl -fsSL https://awscli.amazonaws.com/v2/install.sh | bash
```

Una vez que termina el proceso, revisamos la versión instalada, en caso de que no muestre nada, podemos cerrar e iniciar sesión nuevamente para que la variable de entorno este disponible.

```bash
aws --version
aws-cli/2.36....
```

Vamos a crear un nuevo perfil para OVH, no vamos a usar el que viene por default, esto en caso que posteriormente se quieran agregar mas proveedores/perfiles.


```bash
aws configure --profile ovhstorage
```

Al escribir el comando, nos va a pedir los datos de autenticación, los cuales, previamente descargamos en el .txt:

```bash
AWS Access Key ID: TU_ACCESS_KEY
AWS Secret Access Key: TU_SECRET_KEY
Default region name: bhs
Default output format: json
```
Al terminar el proceso, verificamos la configuración con el siguiente comando:

```bash
aws configure list --profile ovhstorage

NAME       : VALUE                    : TYPE             : LOCATION
profile    : ovhstorage               : manual           : --profile
access_key : ********************     : shared-credentials-file : 
secret_key : ********************     : shared-credentials-file : 
region     : bhs                      : config-file      : ~/.aws/config
```

Cambiamos los permisos del directorio donde se encuentran las credenciales para mayor seguridad

```bash
chmod 600 ~/.aws/credentials
chmod 600 ~/.aws/config
```

## Uso de CLI

Dentro del panel de OVHcloud, en el bucket creado, vamos ir a la pestaña de Dashboard.

![](/img/ovhobjectstorage/11.png)

Vamos a copiar el valor de Endpoint, lo usaremos en el siguiente paso.

Regresamos a la línea de comandos de Linux y vamos a realizar una prueba de listar todos los buckets, para ello vamos a ingresar el nombre del perfil y el endpoint url de la zona que seleccionamos.

```bash
aws s3 ls --profile ovhstorage --endpoint-url https://s3.bhs.io.cloud.ovh.net/
```

Si las configuraciones y los datos de acceso son correctos, nos lista los buckets, para nuestro caso, solo se muestra 1.

![](/img/ovhobjectstorage/12.png)

Si no deseamos estar escribiendo en cada comando la URL, de acuerdo a la documentación oficial de OVH, vamos a configurar el archivo de **~/.aws/config** de la siguiente manera.

```bash
nano ~/.aws/config

[profile ovhstorage]
region = bhs
output = json
endpoint_url = https://s3.bhs.io.cloud.ovh.net 

```

Volvemos a probar el listado con el siguiente comando.

```bash
aws s3 ls --profile ovhstorage
```

### Subir objetos en un bucket

Creamos un archivo desde la terminal de Linux con el siguiente comando.

```bash
echo "prueba de enviar archivo a OVH S3" > prueba.txt
```

Copiar un archivo al bucket

```bash
aws s3 cp prueba.txt s3://prueba-contenedor-ovh/ --profile ovhstorage
```

Con cp estamos indicando que vamos a copiar un archivo de nuestro equipo local
El nombre del bucket lo indicamos en s3://{contenedor}

Si el proceso es correcto, en la terminal podemos ver lo siguiente.

![](/img/ovhobjectstorage/13.png)

Regresamos al panel de administración de OVH, seleccionamos el bucket y podemos ver el archivo que acabamos de subir desde CLI.

![](/img/ovhobjectstorage/14.png)

Listamos los objetos dentro del bucket con el siguiente comando

```bash
aws s3 ls s3://prueba-contenedor-ovh --profile ovhstorage
```

![](/img/ovhobjectstorage/15.png)

Si deseamos subir una carpeta completa, por ejemplo, una llamada documentosS3, lo realizamos de forma recursiva con el siguiente comando.

*Nota: s3 no tiene carpetas reales, las carpetas que podemos visualizar son una representación de un objeto con una key.*


```bash
aws s3 cp /home/debian/documentosS3 \
  s3://prueba-contenedor-ovh/documentosS3/ \
  --recursive \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/16.png)

-Verificamos desde el panel de administración si los archivos se encuentran.

![](/img/ovhobjectstorage/17.png)

![](/img/ovhobjectstorage/18.png)

### Descargar objetos de un bucket

Si queremos solo descargar 1 archivo del bucket a nuestro equipo, lo realizamos con el siguiente comando, indicando el nombre del archivo.

```bash
aws s3 cp \
  s3://prueba-contenedor-ovh/prueba.txt \
  ./prueba-descarga.txt \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/19.png)

En cambio, para descargar una carpeta completa, lo podemos realizar con el siguiente comando.

```bash
aws s3 cp \
  s3://prueba-contenedor-ovh/documentosS3/ \
  ./documentosDescargados/ \
  --recursive \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/20.png)


### Copiar objetos dentro de buckets

Para realizar una copia de un objeto del bucket a otra carpeta dentro del mismo bucket, usamos el siguiente comando.

```bash
aws s3 cp \
  s3://prueba-contenedor-ovh/prueba.txt \
  s3://prueba-contenedor-ovh/respaldos/prueba.txt \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/21.png)

Verificamos dentro del panel de administración

![](/img/ovhobjectstorage/22.png)

![](/img/ovhobjectstorage/23.png)


### Eliminar objetos de un bucket

Para eliminar un objeto dentro del bucket, lo realizamos de la siguiente manera, indicando el archivo a eliminar.

```bash
aws s3 rm \
  s3://prueba-contenedor-ovh/prueba.txt \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/24.png)

Para eliminar el contenido de carpetas completas, lo realizamos de la siguiente manera.

```bash
aws s3 rm \
  s3://prueba-contenedor-ovh/respaldos/ \
  --recursive \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/25.png)

![](/img/ovhobjectstorage/26.png)

### Sincronización

La sincronización consiste en mantener dos ubicaciones con los mismos archivos, ya sea en la dirección carpeta local → S3 o S3 → carpeta local.

Durante este proceso, se comparan los archivos de ambas ubicaciones y se identifican aquellos que son nuevos o que han sido modificados. Estos archivos se cargan automáticamente en la ubicación de destino para mantener ambas ubicaciones sincronizadas.

Para sincronizar una carpeta local con S3, realizamos el siguiente comando.

```bash
aws s3 sync \
  /home/debian/documentosSincronizacion \
  s3://prueba-contenedor-ovh/documentosSincronizacion/ \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/27.png)

![](/img/ovhobjectstorage/28.png)

Para realizar el proceso contrario, sincronización entre una carpeta de S3 hacia local, aplicamos el comando.

```bash
aws s3 sync \
  s3://prueba-contenedor-ovh/documentosSincronizacion/ \
  /home/debian/sincronizacion-restaurada/ \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/29.png)

¿Qué sucede si deseamos que S3 sea un reflejo exacto de la carpeta local? Es decir, si eliminamos un archivo en la ubicación local, también queremos que este sea eliminado de S3.

En este caso, podemos utilizar el parámetro *--delete*, que se encarga de eliminar del destino los archivos que ya no existen en el origen. De esta manera, S3 se mantendrá como una copia exacta de la ubicación local.

El parámetro se aplica de la siguiente manera:

```bash
aws s3 sync \
  /home/debian/documentosSincronizacion \
  s3://prueba-contenedor-ovh/documentosSincronizacion/ \
  --delete \
  --profile ovhstorage
```

## Cifrado del lado del servidor con claves de cliente (SSE-C)

¿Qué sucede si necesitamos almacenar objetos utilizando una clave de cifrado creada y administrada por nosotros? En este caso, podemos utilizar SSE-C (Server-Side Encryption with Customer-Provided Keys).

SSE-C permite proporcionar una clave de cifrado al momento de cargar un objeto. OVHcloud utiliza esta clave para cifrar el objeto antes de almacenarlo. Al momento de descargarlo, es necesario proporcionar la misma clave utilizada durante la carga para que el objeto pueda ser descifrado correctamente.

De esta forma, el control de la clave de cifrado permanece en manos del cliente.

Este mecanismo no debe confundirse con el cifrado del lado del cliente (client-side encryption). En SSE-C, el cifrado se realiza del lado del servidor, por lo que OVHcloud necesita recibir la clave proporcionada por el cliente durante las operaciones que requieren acceso al objeto.

La clave no se almacena de forma persistente en OVHcloud. Por este motivo, es necesario proporcionar la clave correspondiente en cada operación que requiera cifrar, descifrar o acceder a un objeto protegido mediante SSE-C.

- Generación de llaves del lado del cliente

```bash
secret=$(openssl rand 32)
encKey=$(echo -n "$secret" | base64)
md5Key=$(echo -n "$secret" | openssl dgst -md5 -binary | base64)
```

- Subir un objeto

```bash
aws s3api put-object \
  --bucket prueba-contenedor-ovh \
  --key secret/prueba.txt \
  --body prueba.txt \
  --sse-customer-algorithm AES256 \
  --sse-customer-key "$encKey" \
  --sse-customer-key-md5 "$md5Key" \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/30.png)

![](/img/ovhobjectstorage/31.png)

Si intentamos descargar el archivo directo desde el panel de administración de OVH, nos muestra un error y no se descarga.

![](/img/ovhobjectstorage/32.png)

- Descargar un objeto sin las llaves del cliente

```bash
aws s3api get-object \
  --bucket prueba-contenedor-ovh \
  --key secret/prueba.txt \
  --profile ovhstorage \
  prueba-descargada.txt
```

Al intentar realizar una descarga sin indicar las llaves correspondientes, nos muestra el siguiente error:

![](/img/ovhobjectstorage/33.png)

- Descargar un objeto con las llaves del cliente

```bash
aws s3api get-object \
  --bucket prueba-contenedor-ovh \
  --key secret/prueba.txt \
  --sse-customer-algorithm AES256 \
  --sse-customer-key "$encKey" \
  --sse-customer-key-md5 "$md5Key" \
  --profile ovhstorage \
  prueba-descargada.txt
```

![](/img/ovhobjectstorage/34.png)

- Revisar los metadatos de un objeto

```bash
aws s3api head-object \
  --bucket prueba-contenedor-ovh \
  --key secret/prueba.txt \
  --sse-customer-algorithm AES256 \
  --sse-customer-key "$encKey" \
  --sse-customer-key-md5 "$md5Key" \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/35.png)

- Eliminar un objeto

Para eliminar, no es necesario pasar el cifrado, se borra de forma normal.

```bash
aws s3 rm \
  s3://prueba-contenedor-ovh/secret/prueba.txt \
  --profile ovhstorage
```

![](/img/ovhobjectstorage/36.png)

## Resumen de comandos desde CLI

- Listar los buckets

```bash
aws s3 ls --profile {nombre_perfil}
```

- Listar contenido de un bucket

```bash
aws s3 ls s3://{contenedor} --profile {nombre_perfil}
```

- Subir un archivo

```bash
aws s3 cp {archivo} s3://{contenedor} --profile {nombre_perfil}
```

- Subir una carpeta

```bash
aws s3 cp ./{carpetaLocal}/ \
  s3://{contenedor}/{carpeta} \
  --recursive \
  --profile {nombre_perfil}
```

- Descargar un archivo

```bash
aws s3 cp \
  s3://{contenedor}/{archivo} \
  ./{nombreArchivoLocal} \
  --profile {nombre_perfil}
```

- Descargar una carpeta

```bash
aws s3 cp \
  s3://{contenedor}/{carpeta}/ \
  ./{nombreCarpetaLocal}/ \
  --recursive \
  --profile {nombre_perfil}
```

- Eliminar un archivo

```bash
aws s3 rm \
  s3://{contenedor}/{archivo} \
  --profile {nombre_perfil}
```

- Eliminar archivos de una carpeta/prefijo

```bash
aws s3 rm \
  s3://{contenedor}/{carpeta}/ \
  --recursive \
  --profile {nombre_perfil}
```

- Sincronización de local a la nube

```bash
aws s3 sync \
  {ruta_local}/ \
  s3://{contenedor}/{carpeta}/ \
  --profile {nombre_perfil}
```

- Sincronización de la nube a local

```bash
aws s3 sync \
  s3://{contenedor}/{carpeta}/ \
  {ruta_local}/ \
  --profile {nombre_perfil}
```

## Referencias

https://www.ovhcloud.com/es/

https://docs.ovhcloud.com/es/guides/storage-and-backup/object-storage/s3-getting-started-with-object-storage

https://docs.aws.amazon.com/es_es/cli/v1/userguide/cli-services-s3-commands.html

