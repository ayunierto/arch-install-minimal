# Instalar Arch Linux en un sistema UEFI.

### Paso 1: Preparativos

1. **Descargar la imagen ISO**: Ve al sitio web oficial de Arch Linux y descarga la última imagen ISO desde [archlinux.org/download](https://archlinux.org/download/).

2. **Crear un medio de instalación**: Utiliza una herramienta como Etcher o Rufus para crear un USB de arranque con la imagen ISO descargada.

### Paso 2: Arrancar desde el medio de instalación

1. **Arrancar desde el USB**: Reinicia tu computadora y asegúrate de que el USB esté conectado. Accede al menú de arranque y selecciona la opción para arrancar desde el USB.

### Paso 3: Configuración del teclado

1. **Elegir el idioma del teclado**: Cuando aparezca el menú de arranque de Arch Linux, selecciona "Arch Linux" y luego presiona la tecla `Enter`. Cuando se te pida, ingresa `es` si deseas el teclado en español.

### Paso 4: Configuración de red

1. **Conexión a Internet**: Si estás usando Ethernet, generalmente la conexión se configurará automáticamente. Si estás usando Wi-Fi, ejecuta `wifi-menu` y sigue las instrucciones para conectarte a tu red Wi-Fi. Tambien pudes usar `iwctl`, ejemplo:
```
iwclt
station wlan0 connect "SSID"
```

### Paso 5: Particionamiento del disco en UEFI
Particionamiento con cfdisk.
Utiliza fdisk -l o lsblk para identificar tus discos y particiones.

1. Escribe el comando `cfdisk` en la terminal y presiona Enter.
2. Aparecerá una lista de dispositivos de almacenamiento. Selecciona el disco en el que deseas instalar Arch Linux (por ejemplo, `/dev/sda`).
3. Dentro de `cfdisk`, verás una lista de particiones actuales en el disco seleccionado.
4. Para crear una partición, selecciona la opción "Free Space" y presiona Enter.
5. Selecciona "New" para crear una nueva partición.
6. Específica el tamaño de la partición en al menos 512M.
7. En opción "Type". Elige el sistema de archivos para la partición "EFI System").
8. Selecciona "New" para crear la partición de swap.
9. Específica el tamaño de la partición con el tamaño deseado (ejemplo: 2G).
10. En opción "Type". Elige el sistema de archivos para la partición "Linux Swap").
11. Selecciona "New" para crear la partición del sistema.
12. Específica el tamaño de la partición con el tamaño deseado (puedes dejar el tamaño predeterminado si deseas usar todo el espacio disponible).
13. Aparecerá una opción para "Write". Confirma y escribe los cambios en el disco.
14. Después de escribir los cambios, selecciona "Quit" para salir de `cfdisk`.

### Paso 6: Formatear y montar particiones en UEFI

1. **Formatear particiones**: Escribe `mkfs.fat -F32 /dev/sdX1` para formatear la partición EFI, `mkswap /dev/sdX2` para la partición de swap y `mkfs.ext4 /dev/sdX3` para la partición del sistema.

2. **Montar particiones**: 
```
mount /dev/sdX2 /mnt           # Para montar la partición del sistema
mkdir -p /mnt/boot 
mount /dev/sdX1 /mnt/boot      # Para montar la partición EFI.
swapon /dev/sdX3               # Para montar la particion de swap
```
> Sustituye X por el disco correspondiente.

### Paso 7: Instalar el sistema base

1. **Instalar el sistema base**: Escribe `pacstrap /mnt base linux linux-firmware` y espera a que termine la instalación.
Puede elegir otros paquetes que le pueden resultar necesarios:

```
networkmanager        # Administrar conexiones de red.
wpa_supplicant        # Administrar conexiones de red inalámbrica, especialmente aquellas protegidas con WPA y WPA2.
neovim                # Editor de codigo.
nano                  # Editor de codigo.
```

### Paso 8: Configurar el sistema

1. **Generar el archivo fstab**: 
```
genfstab -U /mnt >> /mnt/etc/fstab
```

3. **Cambiar al entorno root**: 
```
arch-chroot /mnt
```
> Cambia al entorno del sistema instalado.

5. **Configurar la zona horaria**: 
```bash
ln -sf /usr/share/zoneinfo/Zone/SubZone /etc/localtime` 
```
> Reemplaza `Zone` y `SubZone` según tu ubicación.

6. **Configurar el idioma**: 
```
nano /etc/locale.gen
```
Busca la línea `en_US.UTF-8` o similar y quita el símbolo `#` al principio de la línea. Luego, guarda el archivo con `Ctrl + O`, confirma con `Enter` y sal con `Ctrl + X`.

6. **Generar el archivo de idioma**: 
```
locale-gen
```

7. **Configurar el hostname**:
```
echo "mihostname" > /etc/hostname
```
> Reemplaza `mihostname` con el nombre que desees para tu computadora.

7. **Configurar el hosts**:
Crea el archivo `/etc/hosts` y agrega lo siguiente:
```
127.0.0.1          localhost
::1                localhost
127.0.0.1          mihostname.localhost  localhost
```
> Reemplaza `mihostname` con el nombre de tu computadora.


### Paso 9: Configurar contraseñas y usuario

1. **Contraseña root**: 
```
passwd
```
> Sigue las instrucciones para establecer una contraseña para el usuario root.

3. **Crear un usuario**: 
```
useradd -m -G wheel -s /bin/bash tunombredeusuario
```
> Reemplaza `tunombredeusuario` con el nombre que deseas para tu usuario.

4. **Contraseña del usuario**: 
```
passwd tunombredeusuario
```
> Sigue las instrucciones para establecer una contraseña para el usuario.

6. **Instalar y habilitar sudo**:
```bash
pacman -S sudo
visudo # Anteriormente debe instalar vim
```
o 
```
nano /etc/sudoers
```
Busca la línea `%wheel ALL=(ALL) ALL` y quita el símbolo `#` al principio de la línea. Guarda el archivo y sal.

### Paso 10: Instalar el gestor de arranque en UEFI

1. **Instalar GRUB y efibootmgr**:
```
pacman -S grub efibootmgr os-prober
```

2. **Instalar el cargador de arranque**:
```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch_grub
```

3. **Generar la configuración de GRUB**: 
```
grub-mkconfig -o /boot/grub/grub.cfg
```

### Paso 11: Salir del entorno chroot y reiniciar

1. **Salir del entorno chroot**: 
```
exit
```

2. **Desmontar particiones**: 
```
umount -R /mnt
```

3. **Reiniciar**: 
```
reboot
```

------------------------------------------------------------------------------------------------------------------

# Instalación de Awesome en Arch Linux

Este archivo proporciona instrucciones paso a paso para instalar el gestor de ventanas Awesome en Arch Linux.

## Paso 1: Actualización del sistema

Antes de comenzar, asegúrate de que tengas conexion y tu sistema esté actualizado ejecutando el siguiente comando:

```bash
sudo systemctl enable NetworkManager       # Activa el servicio de red para que inicia automaticamente.
sudo systemctl start NetworkManager        # Inicia el servicio de red.
sudo pacman -Syu
```
Para conectarse a una red wifi:
```
nmcli device wifi list
nmcli device wifi connect "SSID" passowrd "passwork"
```

## Paso 2: Instalación de Awesome

Instala Awesome y sus dependencias utilizando el siguiente comando:

```bash
sudo pacman -S awesome
```

## Paso 3: Configuración de sesión

Awesome no es un entorno de escritorio completo, así que necesitarás un gestor de inicio de sesión. Instala LightDM con el siguiente comando:

```bash
sudo pacman -S lightdm lightdm-gtk-greeter
```

## Paso 4: Habilitar y configurar LightDM

Habilita el servicio de LightDM para que se inicie automáticamente en el arranque:

```bash
sudo systemctl enable lightdm
```

## Paso 5: Selección de Awesome como gestor de ventanas

Crea un archivo `~/.xinitrc` si no existe y agrega la siguiente línea para que LightDM use Awesome como tu gestor de ventanas predeterminado:

```bash
exec awesome
```
Tambien es necesario instalar una terminal. La terminal que viene por defecto en awesome es `xterm`.
```
sudo pacman -S xterm
```

## Paso 6: Iniciar sesión

Reinicia tu sistema o cierra sesión y selecciona "Awesome" como tu entorno de inicio de sesión en la pantalla de LightDM.

## Paso 7: Configuración y personalización

Copie el archivo de configuracion de `/etc/xdg/awesome/rc.lua` al directorio `~/.config/awesome/`.
```
cp /etc/xdg/awesome/rc.lua ~/.config/awesome/
```

Awesome es altamente personalizable a través de su archivo de configuración `rc.lua`, ubicado en `~/.config/awesome/`. Asegúrate de consultar ejemplos de configuraciones y la documentación oficial para personalizar tu experiencia.

Con esto tenemos un gestor de ventanas funcional y muy liviano.
Este sistema no tiene mas de 300 paquetes instalados y el consumo de ram es de 200Mb.

# Instalar paru
Paru es un helper estándar de AUR para envolver pacman con muchas funciones y una interacción mínima.
```
sudo pacman -S --needed base-devel
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```
# Instalar chrome
```
sudo pacman - S git
git clone https://aur.archlinux.org/google-chrome.git
cd google-chrome
makepkg -s
```

