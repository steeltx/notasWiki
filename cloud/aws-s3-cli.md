# Uso de Amazon S3 desde CLI en Linux

Esta guía describen los pasos necesarios para instalar y configurar el CLI de Amazon S3 en un equipo con GNU/Linux para utilizarlo con AWS. El objetivo es permitir la gestión y administración del servicio **S3** directamente desde la línea de comandos.

## Introducción

**AWS S3**
Amazon Simple Storage Service (Amazon S3) es un servicio de almacenamiento de objetos que ofrece escalabilidad, disponibilidad de datos, seguridad y rendimiento.
Se puede almacenar, administrar, analizar y proteger cualquier cantidad de datos para prácticamente cualquier caso de uso, como los lagos de datos, las aplicaciones nativas en la nube y las aplicaciones móviles. 

[Fuente de información](https://aws.amazon.com/es/s3/)

## Requisitos
- Distro GNU/Linux (Ubuntu/Debian/Linux Mint)
  Para este ejemplo se usa **Debian 13**
- Conexión a internet
- Cuenta de AWS (Nivel gratuito o plan de pago)
  **Nota:  El uso de AWS S3 puede generar costos. Antes de implementarlo, se recomienda consultar y revisar la información de precios vigentes para conocer los cargos aplicables.**

## Creación de buckets desde panel de administración

- Ingresar a la Consola de administración de AWS con una cuenta

- En el menú de búsqueda superior, escribir S3 y clic en **Almacenamiento escalable en la nube**

![](/img/awss3cli/1.png)

- Si es la primera vez que accedemos a este servicio, no tendremos ningún bucket creado. Para crear uno, haz clic en **Crear bucket**. Los buckets de S3 se crean en una región específica, por lo que es importante seleccionar la región adecuada, preferentemente la más cercana a tu ubicación o a la de los clientes que utilizarán el servicio.

![](/img/awss3cli/2.png)

- En la parte de configuración general, lo primero que tenemos que verificar es la región, confirmar que es la deseada. En tipo de bucket para este ejemplo usamos **Uso general**.

  En Espacio de nombres del bucket contamos con dos opciones:

  - Espacio de nombres global: el nombre del bucket debe ser único a nivel global, es decir, no puede existir otro bucket con el mismo nombre en ninguna cuenta de AWS.
  
  - Espacio de nombres regional de la cuenta: el nombre se genera utilizando un sufijo asociado a la cuenta y la región. Esta opción es la recomendada, ya que facilita la creación de nombres sin preocuparse por la disponibilidad del nombre a nivel global, para este ejemplo, seleccionamos esta opción y colocamos un nombre.

![](/img/awss3cli/3.png)

- En la sección Propiedad de objetos dejamos seleccionado **ACL deshabilitadas**

- En Configuración de bloqueo de acceso público para este bucket vamos a dejar seleccionado **Bloquear todo el acceso público**, para este ejemplo requerimos que todo el acceso sea privado (autenticado).

![](/img/awss3cli/4.png)

- Si deseamos tener un control de versiones en el bucket lo tenemos que habilitar en la sección de Control de versiones de buckets, pero para este ejemplo lo vamos a dejar desactivado. Si se activa podemos conservar, recuperar y restaurar todas las versiones de los objetos que se suban.

- En la sección Etiquetas podemos agregar etiquetas al bucket cuando sea necesario, por ejemplo, para facilitar el control de costos, identificar ambientes o clasificar recursos. Para este ejemplo, no agregaremos ninguna etiqueta.

![](/img/awss3cli/5.png)

- En la sección Cifrado encontraremos diferentes opciones para proteger los objetos almacenados en el bucket. Para este ejemplo, utilizaremos la primera opción, en la que las claves de cifrado son administradas por Amazon S3. Si se requiere un mayor nivel de control sobre las claves de cifrado, podemos utilizar AWS Key Management Service (AWS KMS), que permite administrar y controlar las claves utilizadas para cifrar los datos.

- En Clave de bucket lo dejamos habilitado.

![](/img/awss3cli/6.png)

- Al terminar la configuración, clic en **Crear bucket** y esperar unos segundos a que termine la operación.

![](/img/awss3cli/7.png)

Desde la consola de administración de Amazon S3 podemos realizar la carga de archivos, los cuales son almacenados como objetos dentro del bucket. Sin embargo, el objetivo de esta guía es realizar estas operaciones mediante AWS CLI en Linux, por lo que en siguientes secciones veremos cómo realizar la carga utilizando la línea de comandos.


## Creación del usuario IAM para S3

Vamos a necesitar un usuario solo con permisos de S3 de AWS, para ello, realizamos los siguientes pasos desde la consola de administración web.

- En la sección de búsqueda, escribir **IAM** y seleccionar **IAM - Administre el acceso a los recursos de AWS**

![](/img/awss3cli/8.png)

- Ir a Usuarios de IAM > Crear persona

![](/img/awss3cli/9.png)

- Ingresar un nombre de usuario, para este ejemplo *user-cli-aws-s3*.
**Importante: No seleccionar Proporcione acceso de usuario a la consola de administración de AWS, ya que el usuario solo va a tener permisos desde la CLI**

![](/img/awss3cli/10.png)

- En la sección Permisos contamos con diferentes opciones para asignar los permisos al usuario. Podemos agregarlo a un grupo existente, copiar los permisos de otro usuario o asignar los permisos directamente. Para este ejemplo, utilizaremos la última opción, ya que trabajaremos únicamente con un usuario. Si se van a crear varios usuarios con permisos similares, es recomendable crear un grupo y asignar los permisos al grupo, para posteriormente agregar los usuarios correspondientes. Esto facilita la administración y el mantenimiento de los permisos.

- En la sección Políticas de permisos, buscaremos y seleccionaremos **AmazonS3FullAccess**. Para este ejemplo utilizaremos esta política, ya que proporciona acceso completo para administrar los recursos de Amazon S3. Sin embargo, en un entorno de producción es recomendable asignar únicamente los permisos necesarios según los requerimientos del proyecto, siguiendo el principio de mínimo privilegio. De esta forma, podemos limitar las acciones que el usuario puede realizar y mejorar la seguridad de los recursos.

![](/img/awss3cli/11.png)

- En la siguiente sección revisamos que las configuraciones sean correctas a como lo requerimos y clic en **Crear persona**.

![](/img/awss3cli/12.png)

![](/img/awss3cli/13.png)

- El proceso termina correctamente, dar clic en **Ver persona**.

![](/img/awss3cli/14.png)

- En este punto ya creamos el usario, pero no se han definido sus credenciales para ingresar, por lo tanto vamos a **Credenciales de seguridad > Claves de acceso**.

![](/img/awss3cli/15.png)

- Clic en **Crear clave de acceso**, posteriormente seleccionar el caso de uso de **Interfaz de línea de comandos (CLI)**, seleccionar la casilla de confirmación y clic en siguiente.

![](/img/awss3cli/16.png)

- No vamos a crear una etiqueta para este ejemplo, clic en **Crear clave de acceso**

![](/img/awss3cli/17.png)

- Al crear la nueva credencial, se muestra la clave de acceso y la clave de acceso secreta, igual podemos descargar el archivo.csv con estos datos, guardar en un lugar seguro, clic en Listo.

![](/img/awss3cli/18.png)

En este punto, ya tenemos configurado nuestro primer bucket y un usuario para acceder desde CLI, en los siguientes pasos vamos a iniciar sesión desde CLI y probar las configuraciones realizadas.

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

Vamos a configurar los accesos mediante el siguiente comando, si deseamos crear un perfil diferente al default, podemos agregar la opción *--profile {perfil}*

```bash
aws configure
```

Al escribir el comando, nos va a pedir los datos de autenticación, los cuales, previamente descargamos en el .txt al crear el usuario:

```bash
AWS Access Key ID: TU_ACCESS_KEY
AWS Secret Access Key: TU_SECRET_KEY
Default region name: TU_REGION (us-east-1 para este ejemplo)
Default output format: json
```

Al terminar el proceso, verificamos la configuración con el siguiente comando:

```bash
aws configure list

NAME       : VALUE                    : TYPE             : LOCATION
profile    : <not set>                : None             : None
access_key : ********************     : shared-credentials-file : 
secret_key : ********************     : shared-credentials-file : 
region     : us-east-1                : config-file      : ~/.aws/config

```

Cambiamos los permisos del directorio donde se encuentran las credenciales para mayor seguridad.

```bash
chmod 600 ~/.aws/credentials
chmod 600 ~/.aws/config
```

## Uso de CLI

Vamos a realizar la prueba de listar los buckets para comprobar que las configuraciones anteriores se realizaron de manera correcta, para ello usamos el siguiente comando.

```bash
aws s3 ls
```
Si las configuraciones y los datos de acceso son correctos, nos lista los buckets, para nuestro caso, solo se muestra 1.

![](/img/awss3cli/19.png)

### Subir objetos en un bucket

Creamos un archivo desde la terminal de Linux con el siguiente comando.

```bash
echo "prueba de enviar archivo a AWS S3" > prueba.txt
```

Subir un archivo al bucket

```bash
aws s3 cp prueba.txt s3://demo-bucket-s3-us-east-1-an/
```

Con cp estamos indicando que vamos a copiar un archivo de nuestro equipo local
El nombre del bucket lo indicamos en s3://{contenedor}

Si el proceso es correcto, en la terminal podemos ver lo siguiente.

![](/img/awss3cli/20.png)

Regresamos a la consola de administración de AWS, seleccionamos el bucket y podemos ver el archivo que acabamos de subir desde CLI.

![](/img/awss3cli/21.png)


Listamos los objetos dentro del bucket con el siguiente comando

```bash
aws s3 ls s3://demo-bucket-s3-us-east-1-an/
```

![](/img/awss3cli/22.png)

Si deseamos subir una carpeta completa, por ejemplo, una llamada documentosS3, lo realizamos de forma recursiva con el siguiente comando.

*Nota: s3 no tiene carpetas reales, las carpetas que podemos visualizar son una representación de un objeto con una key.*

```bash
aws s3 cp documentosS3 \
  s3://demo-bucket-s3-us-east-1-an/documentosS3/ \
  --recursive
```

- Verificamos desde la consola de administración si los archivos se encuentran.

![](/img/awss3cli/23.png)

![](/img/awss3cli/24.png)

### Descargar objetos de un bucket

Si queremos solo descargar 1 archivo del bucket a nuestro equipo, lo realizamos con el siguiente comando, indicando el nombre del archivo.

```bash
aws s3 cp \
  s3://demo-bucket-s3-us-east-1-an/prueba.txt \
  ./prueba-descarga.txt
```

![](/img/awss3cli/25.png)

En cambio, para descargar una carpeta completa, lo podemos realizar con el siguiente comando.

```bash
aws s3 cp \
  s3://demo-bucket-s3-us-east-1-an/documentosS3/ \
  ./documentosDescargados/ \
  --recursive
```

![](/img/awss3cli/26.png)

### Copiar objetos dentro de buckets

Para realizar una copia de un objeto del bucket a otra carpeta dentro del mismo bucket, usamos el siguiente comando.

```bash
aws s3 cp \
  s3://demo-bucket-s3-us-east-1-an/prueba.txt \
  s3://demo-bucket-s3-us-east-1-an/respaldos/prueba.txt
```

![](/img/awss3cli/27.png)

Verificamos dentro de la consola de administración.

![](/img/awss3cli/28.png)

![](/img/awss3cli/29.png)

### Eliminar objetos de un bucket

Para eliminar un objeto dentro del bucket, lo realizamos de la siguiente manera, indicando el archivo a eliminar.

```bash
aws s3 rm \
  s3://demo-bucket-s3-us-east-1-an/prueba.txt
```

![](/img/awss3cli/30.png)

Para eliminar el contenido de carpetas completas, lo realizamos de la siguiente manera.

```bash
aws s3 rm \
  s3://demo-bucket-s3-us-east-1-an/documentosS3/ \
  --recursive
```
![](/img/awss3cli/31.png)

![](/img/awss3cli/32.png)

### Sincronización

La sincronización consiste en mantener dos ubicaciones con los mismos archivos, ya sea en la dirección carpeta local → S3 o S3 → carpeta local.

Durante este proceso, se comparan los archivos de ambas ubicaciones y se identifican aquellos que son nuevos o que han sido modificados. Estos archivos se cargan automáticamente en la ubicación de destino para mantener ambas ubicaciones sincronizadas.

Para sincronizar una carpeta local con S3, realizamos el siguiente comando.

```bash
aws s3 sync \
  documentosSincronizacion/ \
  s3://demo-bucket-s3-us-east-1-an/documentosSincronizacion/
```

![](/img/awss3cli/33.png)

![](/img/awss3cli/34.png)

Para realizar el proceso contrario, sincronización entre una carpeta de S3 hacia local, aplicamos el comando.

```bash
aws s3 sync \
  s3://demo-bucket-s3-us-east-1-an/documentosSincronizacion/ \ 
  /home/debian/documentosSincronizacionRestaurada/
```

![](/img/awss3cli/35.png)

¿Qué sucede si deseamos que S3 sea un reflejo exacto de la carpeta local? Es decir, si eliminamos un archivo en la ubicación local, también queremos que este sea eliminado de S3.

En este caso, podemos utilizar el parámetro *--delete*, que se encarga de eliminar del destino los archivos que ya no existen en el origen. De esta manera, S3 se mantendrá como una copia exacta de la ubicación local.

El parámetro se aplica de la siguiente manera:

```bash
aws s3 sync \
  /home/debian/documentosSincronizacion \
  s3://demo-bucket-s3-us-east-1-an/documentosSincronizacion/ \
  --delete
```

## Resumen de comandos desde CLI

- Listar los buckets

```bash
aws s3 ls
```

- Listar contenido de un bucket

```bash
aws s3 ls s3://{bucket}/
```

- Subir un archivo

```bash
aws s3 cp {archivo} s3://{bucket}/
```

- Subir una carpeta

```bash
aws s3 cp ./{carpetaLocal}/ \
  s3://{bucket}/{carpeta}/ \
  --recursive
```

- Descargar un archivo

```bash
aws s3 cp \
  s3://{bucket}/{archivo} \
  ./{nombreArchivoLocal}
```

- Descargar una carpeta

```bash
aws s3 cp \
  s3://{bucket}/{carpeta}/ \
  ./{nombreCarpetaLocal}/ \
  --recursive
```

- Eliminar un archivo

```bash
aws s3 rm \
  s3://{bucket}/{archivo}
```

- Eliminar archivos de una carpeta/prefijo

```bash
aws s3 rm \
  s3://{bucket}/{carpeta}/ \
  --recursive
```

- Sincronización de local a la nube

```bash
aws s3 sync \
  {ruta_local}/ \
  s3://{bucket}/{carpeta}/
```

- Sincronización de la nube a local

```bash
aws s3 sync \
  s3://{bucket}/{carpeta}/ \
  {ruta_local}/
```

## Referencias

https://aws.amazon.com/es/s3/

https://aws.amazon.com/es/cli/

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

