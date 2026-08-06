# Instalación de PostgreSQL (v18) en Docker y Linux

En esta guía se describen los pasos necesarios para instalar Docker y PostgreSQL 18 en un sistema GNU/Linux basado en las distribuciones Ubuntu y Debian.

## Requisitos
- Sistema GNU/Linux basado en Ubuntu o Debian.
- Conexión a internet

## Instalación de Docker

De acuerdo a la documentación oficial de docker, los pasos para instalar en Ubuntu son los siguientes:

```bash
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Posteriormente agregar el repositorio en las fuentes.

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

Actualizamos los repositorios e instalamos.

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

>Si estamos usando una distro diferente a Ubuntu, podemos consultar la documentación oficial de Docker para obtener las instrucciones de instalación correspondiente: https://docs.docker.com/engine/install/

Si deseamos usar Docker sin privilegios de administrador (root), podemos agregar nuestro usuario al grupo de docker mediate los siguientes comandos.

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Comprobamos que podemos acceder a docker sin estar como root mediante el comando.

```bash
docker ps
```

Si no se muestra error, la configuración es correcta.

## Instalación de PostgreSQL

Creamos un directorio en nuestro sistema para almacenar los archivos de configuración, en este caso, dentro de **/opt/docker/**.

```bash
sudo mkdir -p /opt/docker/postgresql
sudo chown -R "$USER":"$USER" /opt/docker/postgresql
cd /opt/docker/postgresql
```

En este ejemplo, los datos sensibles no se almacenarán directamente en el archivo de **Docker Compose**. En su lugar, se utilizará un archivo *.env* para definir las variables de entorno necesarias.

```bash
cd /opt/docker/postgresql
nano .env
```

Ingresamos el siguiente contenido al archivo. Recordar reemplazar los valores de ejemplo por sus propios datos.

```bash
POSTGRES_DB=nombre_bd
POSTGRES_USER=usuario_bd
POSTGRES_PASSWORD=clave_segura_bd

PGADMIN_DEFAULT_EMAIL=correo_para_pgadmin
PGADMIN_DEFAULT_PASSWORD=clave_para_pg_admin
```

Creamos el archivo de configuraciones de Docker con el siguiente comando.

```bash
nano docker-compose.yml
```

Colocamos el siguiente contenido.
En este ejemplo, el puerto de conexión externo se configura como 5454 en lugar del puerto predeterminado de PostgreSQL (5432). Esto resulta útil cuando ya existe otro servicio o contenedor utilizando el puerto 5432 en el mismo host.

```bash
services:
  postgres:
    image: postgres:18
    container_name: postgresql18
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    ports:
      - "5454:5432"
    volumes:
      - ./postgres_data:/var/lib/postgresql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 10s
      timeout: 5s
      retries: 5

  pgadmin:
    image: dpage/pgadmin4:latest
    container_name: pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: ${PGADMIN_DEFAULT_EMAIL}
      PGADMIN_DEFAULT_PASSWORD: ${PGADMIN_DEFAULT_PASSWORD}
      TZ: America/Mexico_City
    ports:
      - "8090:80"
    depends_on:
      - postgres
```

Guardar con ctrl + o y salir con ctrl + x del editor nano.

Levantar con el siguiente comando.

```bash
sudo docker compose up -d
```

La primera vez que se inicia el contenedor, el proceso puede tardar algunos minutos, ya que Docker debe descargar las imagenes especificadas. En ejecuciones posteriores, el inicio será considerablemente más rápido, puesto que las imagenes ya estan disponibles de forma local.

## Conectar PostgreSQL desde pgAdmin

Con los contenedores iniciados, desde un navegador web ingresamos en la url **http://localhost:8090/** e ingresamos lo datos de inicio de sesión configurados en el archivo **.env**.
Tenga en cuenta que, en este ejemplo, se utiliza el puerto 8090. Si configuró un puerto diferente, acceda utilizando la dirección y puerto definidos en su configuración.

![](/docker/imgpostgresql/1.png)

Al ingresar con los datos correctos, se muestra la siguiente pantalla.

![](/docker/imgpostgresql/2.png)

El siguiente paso es conectar el cliente de pgAdmin con el servidor de PostgreSQL, para ello vamos a Servers > clic derecho > Register > Server

![](/docker/imgpostgresql/3.png)

En la pestaña de **General**, colocamos un nombre, por ejemplo, local.

![](/docker/imgpostgresql/4.png)

En la pestaña **Connection**, tenemos en cuenta que pgAdmin se está ejecutando dentro de un contenedor. Por este motivo, la conexión hacia PostgreSQL debe realizarse utilizando la red interna de Docker entre contenedores.
No usamos localhost ni el puerto externo 5454. Configuramos los siguientes parámetros:

- host: Ingresamos postgres, ya que es como se llama el contenedor definido en el archivo de docker-compose
- port: 5432, debido a que la comunicación se realiza entre contenedores utilizando el puerto interno de PostgreSQL, no el puerto publicado externamente (5454).
- Maintenance database: la definida en el archivo .env
- Username: el usuario definido en el archivo .env
- Password: la contraseña definida en el archivo .env

![](/docker/imgpostgresql/5.png)

Clic en el boton de **Save**

Si los datos de conexión son correctos y la comunicación entre los contenedores funciona correctamente, se establecerá la conexión y será posible administrar la base de datos, ejecutar consultas y realizar las operaciones necesarias.

![](/docker/imgpostgresql/6.png)

## Instalar DBeaver Community

Como alternativa, podemos conectarnos a PostgreSQL con otro gestor de base de datos compatible, este caso, mediante DBeaver Community Edition.

En un navegador web vamos a su sitio oficial y descargamos el DEB desde https://dbeaver.io/download/

Instalamos el archivo descargado con el comando.

```bash
sudo apt install ./dbeaver-ce-26.1.4-linux-x86_64.deb
```

Una vez finalizada la instalación, podemos abrir DBeaver desde el menú de aplicaciones del sistema buscando **DBeaver**.

## Conectar PostgreSQL desde DBeaver

En DBeaver, clic en **New Database Connection**

![](/docker/imgpostgresql/7.png)

Seleccionar la base de datos PostgreSQL y clic en Next

![](/docker/imgpostgresql/8.png)

Ingresamos los datos de conexión. En este caso, la conexión no se realizará desde otro contenedor, sino desde el equipo local (host). Por lo tanto, usamos los siguientes parámetros:

- host: localhost
- port: 5454
- Database: la definida en el archivo .env
- Username: el usuario definido en el archivo .env
- Password: la contraseña definida en el archivo .env

![](/docker/imgpostgresql/9.png)

Si es la primera vez que realizamos una conexión a PostgreSQL desde DBeaver, la aplicación solicitará descargar e instalar el controlador (driver) necesario para establecer la conexión.

![](/docker/imgpostgresql/10.png)

Si los datos de conexión son correctos, se establecerá la conexión con la base de datos y podemos administrarla desde DBeaver.

![](/docker/imgpostgresql/11.png)

![](/docker/imgpostgresql/12.png)

## Referencias
https://www.postgresql.org/
https://docs.docker.com/engine/install/ubuntu/
https://dbeaver.io/
