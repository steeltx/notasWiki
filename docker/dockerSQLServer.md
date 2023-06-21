# Instalación de Sql Server en Docker

En esta guía se muestran las configuraciones necesarias para instalar Sql Server Express 2019 con Docker y Docker Compose en el sistema Debian.

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

## Instalación SqlServer

- Crear el directorio donde se colocan los archivos de Docker Compose, por ejemplo:

```bash
mkdir /configuracionDocker
cd /configuracionDocker
mkdir SqlServer
```

- Crear el archivo docker-compose.yml, en cual contiene las configuraciones necesarias del contenedor de SqlServer a crear.

```bash
cd /configuracionDocker/SqlServer
nano docker-compose.yml
```

```nano
version: "3.9"

services:

  mssql:
    image: mcr.microsoft.com/mssql/server:2019-latest
    container_name: dockerSqlServer
    ports:
      - 1433:1433
    volumes:
      - ./data:/var/lib/mssqlql/data
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=ingresa_contraseña
      - MSSQL_PID=Express
```

- Iniciar los contenedores (como root)
```bash
cd /configuracionDocker/SqlServer
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

- La configuración anterior crea un contenedor con la imagen de SqlServer 2019 Express.
- Consultar la siguiente página para revisar más configuraciones [Docker Hub - SqlServer](https://hub.docker.com/_/microsoft-mssql-server)

## Conectar al contenedor
Para conectar con el contenedor creado, se puede usar directamente desde código de programación con localhost y el puerto configurado, en este caso 1433.
Igualmente se puede usar algún programa como SQL Server Management Studio en Windows o Azure Data Studio.

## Referencias

https://learn.microsoft.com/es-es/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16&pivots=cs1-bash

https://learn.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio?view=sql-server-ver16

https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16
