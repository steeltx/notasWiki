# Instalación de MariaDB en Docker

En esta guía se muestran las configuraciones necesarias para instalar MariaDB con Docker y Docker Compose en el sistema Debian.

## Requerimientos
- Sistema operativo Debian 11
- Docker
- Docker Compose

## Instalación Docker

```bash
sudo apt-get update

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

```bash
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```bash
sudo apt-get update
```

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

## Instalación MariaDB

- Crear el directorio donde se colocan los archivos de Docker Compose, por ejemplo:

```bash
mkdir /configuracionDocker
cd /configuracionDocker
mkdir MariaDB
```

- Crear el archivo docker-compose.yml, en cual contiene las configuraciones necesarias del contenedor de MariaDB a crear.

```bash
cd /configuracionDocker/MariaDB
nano docker-compose.yml
```

```nano
version: "3.7"

services:

  mariadb:
    image: mariadb:10.6
    container_name: dockerMariaDB
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=establecer_contraseña
      - MYSQL_PASSWORD=establecer_contraseña_root
      - MYSQL_USER=usuario_docker
      - MYSQL_DATABASE=db_docker

  phpmyadmin:
    image: phpmyadmin
    container_name: dockerPhpmyadmin
    ports:
      - 8080:80
    environment:
      - PMA_HOST=mariadb
      - PMA_PORT=3306
      - UPLOAD_LIMIT=200mb
      - MAX_EXECUTION_TIME:3000
```

- Iniciar los contenedores (como root)
```bash
cd /configuracionDocker/MariaDB
docker compose up -d
```

- Ver los contenedores que se estan ejecutando (como root)
```bash
docker ps
```
- Detener un contenedor (como root)
```bash
docker stop [Container ID]
```

- La configuración anterior crea 2 contenedores en Docker, el primero con la imagen de MariaDB y el segundo con phpMyadmin, si no se desea usar phpMyadmin, se puede omitir y usar DBeaver o algún programa similar.
- Se creara la carpeta **data** en el directorio /configuracionDocker/MariaDB/ donde se almacenara los datos necesarios para MariaDB.
- Consultar la siguiente página para revisar más configuraciones [Docker Hub - MariaDB](https://hub.docker.com/_/mariadb), [Docker Hub - PhpMyadmin](https://hub.docker.com/_/phpmyadmin)


## Conectar al contenedor
Para conectar con el contenedor creado, se puede usar directamente desde código de programación con localhost y el puerto configurado, en este caso 3306.
Igualmente se puede usar algún programa como DBeaver, MySQL Workbench, PhpMyadmin, etc.

## Referencias
https://docs.docker.com/engine/install/debian/
