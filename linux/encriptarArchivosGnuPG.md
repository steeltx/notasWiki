# Encriptar archivos con GnuPG

En esta guía se muestran las configuraciones para encriptar archivos de forma simétrica archivos con GnuPG.

---
### Video de GnuPG
[![Video de GnuPG](https://img.youtube.com/vi/n-2q-VgFRRI/mqdefault.jpg)](https://youtu.be/n-2q-VgFRRI "Video de GnuPG")
---

## Requerimientos
- Sistema Linux Mint (O derivados)
- Acceso a terminal como root

## Software a instalar
- GnuPG

## GnuPG

GnuPG permite cifrar y firmar datos y comunicaciones, cuenta con un sistema versátil de gestión de claves y módulos de acceso para todo tipo de directorios de claves públicas. GnuPG, también conocido como GPG , es una herramienta de línea de comandos con funciones que facilitan la integración con otras aplicaciones. 

### Instalación de GnuPG

Para instalar, abrir una terminal y escribir el siguiente comando

```bash
$ sudo apt install gnupg
```

Al terminar de instalar, podemos comprobar la versión, que a la fecha de redacción de esta guía es la *2.4.4*

```bash
$ gpg --version

gpg (GnuPG) 2.4.4
libgcrypt 1.10.3
Copyright (C) 2024 g10 Code GmbH
License GNU GPL-3.0-or-later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: /home/{usuario}/.gnupg
Algoritmos disponibles:
Clave pública: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
Cifrado: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
         CAMELLIA128, CAMELLIA192, CAMELLIA256
Resumen: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compresión: Sin comprimir, ZIP, ZLIB, BZIP2
```

### Encriptación simétrica

Un sistema de cifrado simétrico es un tipo de cifrado que usa una misma clave para cifrar y para descifrar. Las dos partes que se comunican mediante el cifrado simétrico deben estar de acuerdo en la clave a usar de antemano. Una vez de acuerdo, el remitente cifra un mensaje usando la clave, lo envía al destinatario, y éste lo descifra usando la misma clave.
Dado que toda la seguridad está en la clave, es importante que sea muy  segura para ser difícil adivinar.

Para este ejemplo, vamos a crear una carpeta en el escritorio llamada gnupg, dentro de esta carpeta creamos un archivo txt de ejemplo.

![](/linux/imggnupg/1.png)

Abrimos la terminal en la ruta donde se encuentra el archivo y procedemos a encriptar el archivo con el siguiente comando:

```bash
$ gpg --output archivoPlano.gpg --symmetric archivoPlano.txt
```

Donde:
--output:       Nombre del archivo de salida con extensión .gpg
--symmetric:    Encriptación simétrica

Si deseamos usar el comando de forma mas simplificada, podemos aplicarlo de la siguiente manera:

```bash
$ gpg -c archivoPlano.txt
```
Al no especificar el nombre de salida, se toma el nombre del archivo mas la extensión .gpg

Al ejecutar este comando, se va a solicitar una contraseña, que como ya lo vimos anteriormente, debe ser segura, la colocamos y se genera el archivo encriptado con extension **.gpg**

![](/linux/imggnupg/2.png)

Abrimos el archivo generado desde el editor y podemos observar que se encuentra encriptado y no podemos interpretar su contenido.

![](/linux/imggnupg/3.png)

De esta manera nos aseguramos que este archivo no se va a poder leer si no se cuenta con la contraseña correcta.


### Desencriptación simétrica

Una vez que tenemos el archivo encriptado con extensión .gpg aplicamos el siguiente comando para poder desencriptar.
Podemos borrar el archivo original para hacer esta prueba antes de realizar el siguiente comando.


```bash
$ gpg --output archivoPlano.txt --decrypt archivoPlano.gpg
```

Donde:
--output:       Nombre del archivo de salida con extensión .gpg
--decrypt:      Se indica que se va a desencriptar

Si deseamos usar el comando de forma mas simplificada, podemos aplicarlo de la siguiente manera:

```bash
$ gpg -d archivoPlano.gpg > archivoPlano.txt
```

Al aplicar el comando si la contraseña que se uso para cifrar esta en la caché, no la va a solicitar, en caso contrario, la solicita.

Si deseamos que no se guarde la contraseña en caché al momento de generar el archivo, podemos aplicar el siguiente comando.

```bash
$ gpg --no-symkey-cache -c archivoPlano.txt
```

Al terminar el proceso, podemos encontrar que se genero el archivo con la extensión .txt, lo abrimos desde el editor de texto y se encuentra la información que colocamos al inicio.

De esta manera, podemos encriptar y desencriptar de forma simétrica archivos, recordar que la misma contraseña se usa para ambos procesos y en caso de perderla, no podemos recuperar el archivo original.


## Referencias

[GnuPG](https://www.gnupg.org/index.html)

[GnuPG RedHat](https://www.redhat.com/en/blog/getting-started-gpg)
