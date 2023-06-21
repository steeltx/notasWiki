# Instalación de PostgreSQL en Docker

En esta guía se muestran las configuraciones necesarias para instalar PostgreSQL con Docker y Docker Compose en el sistema Debian.

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

## Instalación PostgreSQL

- Crear el directorio donde se colocan los archivos de Docker Compose, por ejemplo:

```bash
mkdir /configuracionDocker
cd /configuracionDocker
mkdir PostgreSQL
```

- Crear el archivo docker-compose.yml, en cual contiene las configuraciones necesarias del contenedor de PostgreSQL a crear.

```bash
cd /configuracionDocker/PostgreSQL
nano docker-compose.yml
```

```nano
version: "3.9"

services:

  db:
    image: postgres:12
    container_name: docker_postgres
    ports:
      - 5432:5432
    environment:
      - POSTGRES_USER=user_postgres
      - POSTGRES_PASSWORD=contraseña_postgres
    volumes: 
      - ./data:/var/lib/postgresql/data
```

- Iniciar los contenedores (como root)
```bash
cd /configuracionDocker/PostgreSQL
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

- La configuración anterior crea un contenedor con la imagen de Postgres.
- Consultar la siguiente página para revisar más configuraciones [Docker Hub - Postgres](https://hub.docker.com/_/postgres)

## Conectar al contenedor
Para conectar con el contenedor creado, se puede usar directamente desde código de programación con localhost y el puerto configurado, en este caso 5432.
Igualmente se puede usar algún programa como DBeaver o pgAdmin.

## Referencias

https://hub.docker.com/_/postgres

https://dev.to/raphaelmansuy/postgres-up-and-running-in-less-than-3-minutes-with-docker-compose-1odd

https://www.pgadmin.org/

