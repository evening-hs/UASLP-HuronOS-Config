# Instalar Hurón OS

Documentación oficial: https://huronos.org/docs/usage/how-to-install

## Requisitos

- USB 3.0 o 3.1 de  > 16 GB.

- Computadora con Linux (Ubuntu, Debian o Fedora de preferencia). Lo intenté hacer con Arch Linux pero no funcionó.
- Un archivo de directivas en un servidor.
- Acceso a un servidor NTP.
- Descargar un ISO de HuronOS del siguiente link. https://mirrors.huronos.org/

## 1. Instalar herramientas necesarias

```bash
# ubuntu o debian
sudo apt install squashfs-tools parted psmisc e2fsprogs dosfstools perl-base
# fedora
sudo dnf install squashfs-tools parted psmisc e2fsprogs dosfstools perl-base
```

En otras distribuciones hay que instalar los paquetes equivalentes.

## 2. Montar el disco de HuronOS

> Nota: Si ya habías insertado la USB, retírala y vuelve a conectarla después de terminar este paso.

Desactivar udisks2 para evitar problemas con la USB,

```bash
sudo systemctl mask udisks2
```

Montar el ISO

```bash
mkdir -p /media/iso
# no olvides moverte con cd al directorio donde se encuentra el ISO
mount ./huronOS-b2023.b0023-amd64.iso /media/iso
```

## 3. Instalar HuronOS

Conectar la USB,

```bash
cd /media/iso
./install.sh
```

Se mostraran muchas prompts que hay que llenar, entre estas se encuentra:

- Seleccionar la USB a utilizar (se muestra una lista y hay que ingresar solo el número según la lista).
- URL del archivo de directivas, en el caso de usar el host de github, hay que poner la siguiente: https://raw.githubusercontent.com/evening-hs/UASLP-HuronOS-Config/refs/heads/main/directives.hdf, si se va a utilizar otro, hay que cuidar de usar HTTP o HTTPS según sea el caso, y poner la ruta completa y correcta hasta el archivo .hdf. Se recomienda probar la URL en un navegador antes de ingresarla.
- Contraseña de root (muy importante de no olvidar), si se deja en blanco, la default es "toor".
- Configuración de la IP, se puede dejar vacía para IP dinámica,

Después hay que esperar a que la instalación termine.

## 4. Volver a activar udisks2

```bash
sudo systemctl unmask udisks2
```

## 5. Bootear con HuronOS

1. Si estás usando Windows, **reinicia** la computadora en lugar de apagar y prender.
2. Desactiva secure boot en la configuración de la BIOS.
3. En la configuración de la BIOS, verifica que el boot por USB esté habilitado.
4. Conecta la USB y reinicia la computadora. Según el modelo de la computadora, es posible que sola entre a la USB, o que sea necesario presionar repetidamente `ESC`, `F9`, `F11` u otra tecla durante el arranque de la computadora para acceder al menú de arranque y seleccionar la USB.
5. HuronOS debería arrancar en la computadora.

## Extras

### Archivo de directivas

El archivo de directivas que hice se encuentra en el siguiente repositorio: https://github.com/evening-hs/UASLP-HuronOS-Config y se puede acceder a este directamente en la siguiente dirección https://raw.githubusercontent.com/evening-hs/UASLP-HuronOS-Config/refs/heads/main/directives.hdf.

La sintaxis del archivo de directivas se puede mirar aquí: https://huronos.org/docs/usage/directives/directives-file-syntax/.

### Construir el servidor

Para información de que necesita el servidor que hostea el archivo de directivas se puede mirar la siguiente documentación. https://huronos.org/docs/usage/directives/directives-server/

Una manera simple de levantar un servidor, es clonar el [repositorio](https://github.com/evening-hs/UASLP-HuronOS-Config) y ejecutar el script `launch.sh`

```bash
# dar permisos de ejecución al script
chmod +x launch.sh
./launch.sh
```

> Notas:
>
> - Hay que tener instalado Python.
> - Hay que abrir el puerto 80.

### Clonar USB

Una vez que la USB con HuronOS funciona correctamente, es posible clonarla a otras USBs, para esto, la USB de destino debe ser del mismo tamaño o más grande que la original.

1. Conecta ambas USB.
2. Identifica la USB de origen y destino:

```bash
sudo fdisk -l
```

3. Clona las USB

```bash
sudo dd if=/dev/sdX of=/dev/sdY bs=8M && sync
```

Donde `/dev/sdX` es la USB original y `/dev/sdY` es la USB de destino.
