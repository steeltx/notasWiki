# Instalación de MySQL Community Edition en Docker y Linux

En esta guía se describen los pasos necesarios para instalar Docker y MySQL Community Edition en un sistema GNU/Linux basado en las distribuciones Ubuntu y Debian.

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

## Instalación de MySQL

Creamos un directorio en nuestro sistema para almacenar los archivos de configuración, en este caso, dentro de **/opt/docker/**.

```bash
sudo mkdir -p /opt/docker/mysql
sudo chown -R "$USER":"$USER" /opt/docker/mysql
cd /opt/docker/mysql
```

En este ejemplo, los datos sensibles no se almacenarán directamente en el archivo de **Docker Compose**. En su lugar, se utilizará un archivo *.env* para definir las variables de entorno necesarias.

```bash
cd /opt/docker/mysql
nano .env
```

Ingresamos el siguiente contenido al archivo. Recordar reemplazar los valores de ejemplo por sus propios datos.

```bash
MYSQL_ROOT_PASSWORD=mysql_root_password
MYSQL_DATABASE=mysql_db
MYSQL_USER=mysql_user
MYSQL_PASSWORD=mysql_password
```

Creamos el archivo de configuraciones de Docker con el siguiente comando.

```bash
nano docker-compose.yml
```

Colocamos el siguiente contenido.
En este ejemplo, el puerto de conexión externo se configura como 3307 en lugar del puerto predeterminado de MySQL (3306). Esto resulta útil cuando ya existe otro servicio o contenedor utilizando el puerto 3307 en el mismo host.

```bash
services:
  mysql:
    image: mysql:8.4
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    ports:
      - "3307:3306"
    volumes:
      - ./mysql_data:/var/lib/mysql
```

Guardar con ctrl + o y salir con ctrl + x del editor nano.

> Para este ejemplo, se usa la versión LTS 8.4, sin embargo, puede usar otra versión si asi lo decide.

Levantar con el siguiente comando.

```bash
docker compose up -d
```

La primera vez que se inicia el contenedor, el proceso puede tardar algunos minutos, ya que Docker debe descargar las imagenes especificadas. En ejecuciones posteriores, el inicio será considerablemente más rápido, puesto que las imagenes ya estan disponibles de forma local.

## Instalar DBeaver Community

Podemos conectarnos a MySQL con un gestor de base de datos compatible, este caso, mediante DBeaver Community Edition.

En un navegador web vamos a su sitio oficial y descargamos el DEB desde https://dbeaver.io/download/

Instalamos el archivo descargado con el comando.

```bash
sudo apt install ./dbeaver-ce-26.1.4-linux-x86_64.deb
```

Una vez finalizada la instalación, podemos abrir DBeaver desde el menú de aplicaciones del sistema buscando **DBeaver**.

## Conectar MySQL desde DBeaver

En DBeaver, clic en **New Database Connection**

![](/docker/imgmysql/1.png)

Seleccionar la base de datos MySQL y clic en Next

![](/docker/imgmysql/2.png)

Ingresamos los datos de conexión. En este caso, la conexión no se realizará desde otro contenedor, sino desde el equipo local (host). Por lo tanto, usamos los siguientes parámetros:

- host: localhost
- port: 3307
- Database: la definida en el archivo .env
- Username: el usuario definido en el archivo .env
- Password: la contraseña definida en el archivo .env

![](/docker/imgmysql/3.png)

Si es la primera vez que realizamos una conexión a MySQL desde DBeaver, la aplicación solicitará descargar e instalar el controlador (driver) necesario para establecer la conexión.

![](/docker/imgmysql/4.png)

Si los datos de conexión son correctos, se establecerá la conexión con la base de datos y podemos administrarla desde DBeaver.

![](/docker/imgmysql/5.png)

![](/docker/imgmysql/6.png)

## Referencias
https://www.mysql.com/
https://docs.docker.com/engine/install/ubuntu/
https://dbeaver.io/
