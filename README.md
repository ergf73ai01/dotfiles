# ArchBang Dotfiles

Configuración reproducible del escritorio ArchBang basado en MangoWM.

Incluye:

- MangoWM
- Waybar
- Rofi
- Mako
- Foot
- Neovim / LazyVim
- ThemeBang
- greetd / gtkgreet
- ZRAM

## Estructura

```text
config/       configuración estática del usuario
templates/    plantillas para archivos runtime
scripts/      scripts de escritorio y bootstrap
system/       configuración instalada en /etc
packages.txt  paquetes oficiales
aur-packages.txt  paquetes AUR
```

## Modelo

La configuración estática se enlaza mediante symlinks al repositorio.
La configuración runtime permanece como archivos normales para permitir que
ThemeBang los modifique sin romper Git.

## Instalación

Clonar el repositorio en `~/dotfiles` y ejecutar:

    ./scripts/bootstrap
    sudo ./scripts/system-setup

`bootstrap` instala paquetes oficiales, muestra los paquetes AUR, crea
symlinks y genera archivos runtime cuando no existen.

`system-setup` instala la configuración del sistema y crea backups cuando
necesita reemplazar archivos. No habilita ni reinicia servicios.

## ThemeBang

ThemeBang administra MangoWM, Waybar, Rofi, Mako y gtkgreet.
Los wallpapers se mantienen fuera del repositorio en `~/Backgrounds/`.

La rotación automática utiliza:

    config/systemd/user/themebang-rotate.service
    config/systemd/user/themebang-rotate.timer

El bootstrap no activa automáticamente el timer.

## AUR

Actualmente se mantiene `networkmanager-dmenu-git` en `aur-packages.txt`.
No se instala automáticamente.

## ZRAM

Configuración actual: `zram-size = ram`, compresión `zstd`, prioridad `100`.

## Verificación

    git status
    mango -p
    swapon --show

Servicios principales: `greetd`, `NetworkManager`, `pipewire`,
`pipewire-pulse` y `wireplumber`.

## Estado

La configuración ha sido probada mediante una reconstrucción controlada
desde un clon limpio. El repositorio debe terminar limpio después de los cambios.
