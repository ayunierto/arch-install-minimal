# Instalación de Awesome en Arch Linux

Este archivo proporciona instrucciones paso a paso para instalar el gestor de ventanas Awesome en Arch Linux.

## Paso 1: Actualización del sistema

Antes de comenzar, asegúrate de que tu sistema esté actualizado ejecutando el siguiente comando:

```bash
sudo pacman -Syu
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

## Paso 6: Iniciar sesión

Reinicia tu sistema o cierra sesión y selecciona "Awesome" como tu entorno de inicio de sesión en la pantalla de LightDM.

## Paso 7: Configuración y personalización

Awesome es altamente personalizable a través de su archivo de configuración `rc.lua`, ubicado en `~/.config/awesome/`. Asegúrate de consultar ejemplos de configuraciones y la documentación oficial para personalizar tu experiencia.

¡Disfruta usando el gestor de ventanas Awesome en Arch Linux!
