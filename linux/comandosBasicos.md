# Comandos básicos de Linux

En esta guía se muestran algunos de los comandos más usados en la CLI de sistemas GNU/Linux.

---

- pwd 

Muestra la ubicación de trabajo actual (directorio de trabajo), iniciando desde la raiz **( / )**

```bash
user@example:~$ pwd
user@example:~$ /home/user
```

- cd 

Cambiar el directorio de trabajo actual

cd **{directorio_trabajo}**


```bash
user@example:~$ cd /home/user/Documentos
user@example:~/Documentos$ 
```
- ls 

Listar los archivos del directorio actual

```bash
user@example:~$ ls
Documentos Descargas Escritorio
```

Si deseamos listar los archivos de un directorio en especial,
se agrega la ruta
ls **{directorio}**

```bash
user@example:~$ ls Documentos
archivo1.txt archivo2.txt archivo3.txt
```

- mkdir

Crear un nuevo directorio
mkdir **{directorio}**

```bash
user@example:~$ mkdir archivosLinux
user@example:~$
```

- cp

Realizar una copia de un archivo
cp {ruta_archivo} {ruta_archivo_nuevo}
Podemos realizar una copia dentro del mismo directorio, o bien,
en un directorio en especial

```bash
user@example:~/Documentos$ cp archivo1.txt archivo1-respaldo.txt
user@example:~/Documentos$ cp archivo1.txt /home/user/Descargas/archivoNuevo.txt
```
En caso de copiar una carpeta completa, se debe especificar la opción -r

```bash
user@example:~$ cp -r Documentos Documentos_respaldo
```

- mv

Mover un archivo entre directorios, o bien, renombrar un archivo 

mv **{archivo_origen}** **{archivo_destino}**

```bash
user@example:~/Documentos$ mv archivo1.txt /home/user/Descargas/
user@example:~/Documentos$ mv archivo1.txt archivo_1.txt
```

En caso de mover una carpeta completa, se debe especificar la opción -r

```bash
user@example:~$ mv -r  Documentos /home/user/Descargas/
```

- rm

Eliminar un archivo
rm **{archivo}**

```bash
user@example:~/Documentos$ rm archivo1.txt
```

Para eliminar un directorio, agregar la opción -r
```bash
user@example:~$ rm -r Documentos
```

- touch

Crear un archivo vacío
touch {archivo}

```bash
user@example:~$ touch archivoNuevo.txt
```

- cat

Leer el contenido de un archivo
cat **{archivo}**

```bash
user@example:~/Documentos$ cat archivo1.txt
Archivo de ejemplo para aprender.
comandos Linux
```

- man

El comando man nos ofrece manuales de referencia para comandos del sistema y conocer su funcionalidad, asi como argumentos que se pueden usar.

man **{comando}**

---

Si desea obtener mas información acerca del tema, puede revisar los links de referencias de este articulo y realizar una investigación mas amplia.

## Referencias
https://ubuntu.com/tutorials/command-line-for-beginners

https://www.javatpoint.com/linux-commands

https://www.digitalocean.com/community/tutorials/linux-commands
