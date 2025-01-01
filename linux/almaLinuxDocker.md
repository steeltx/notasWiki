
# Instalación de Docker en Alma Linux 9

## Requerimientos
- Sistema Alma Linux 9
- Acceso a la terminal de root

## Instalación de Docker

- Actualizar el sistema
```bash
sudo dnf update -y
```

- Instalar dnf-plugins-core
```bash
sudo dnf -y install dnf-plugins-core
```

- Agregar repositorio de docker
```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

- Instalar la ultima versión disponible de Docker CE
```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Iniciar Docker Engine e indicar que se inicie automaticamente al iniciar el sistema
```bash
sudo systemctl enable --now docker
```

- Verificar que se esta ejecutando
```bash
sudo systemctl status docker
```

- Verificar la correcta instalación ejecutando la imagen de hello-world
```bash
sudo docker run hello-world
```
- Si todo es correcto, podemos ver una salida similar a lo siguiente
```bash
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

- Los comandos de docker necesitan acceso de root, si deseamos llamar comandos desde el usuario normal, configurar lo siguiente

```bash
sudo usermod -aG docker [nombre-usuario]
su - [nombre-usuario]
```

- Ver los contenedores activos sin necesidad de usar sudo 

```bash
docker ps
```

```bash
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```


# Referencias 
[https://docs.docker.com/engine/install/centos/](https://docs.docker.com/engine/install/centos/)
