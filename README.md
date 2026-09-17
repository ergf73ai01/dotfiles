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

Para comprobar la instalación actual:

    ./scripts/verify

Esta comprobación valida symlinks, archivos runtime, comandos,
scripts, sintaxis, procesos, servicios, ZRAM y paquetes oficiales.

Para comprobar que el repositorio puede reconstruir una instalación
desde cero sin modificar el sistema real:

    ./scripts/rebuild-test

`rebuild-test` crea una instalación temporal dentro de `/tmp`, utiliza
comandos simulados para `pacman` y operaciones privilegiadas, ejecuta
`bootstrap` y `system-setup`, comprueba la preservación de archivos
runtime y verifica la idempotencia.

Comprobaciones manuales útiles:

    git status
    mango -p
    swapon --show

Servicios principales: `greetd`, `NetworkManager`, `pipewire`,
`pipewire-pulse` y `wireplumber`.

## Estado

El repositorio cuenta con dos niveles de verificación:

`./scripts/verify` comprueba el estado de la instalación actual.

`./scripts/rebuild-test` comprueba que la configuración puede
reconstruirse de forma controlada en un entorno temporal.

La versión estable actual está marcada con `v1.0.0`.

Los cambios posteriores a ese checkpoint se desarrollan en `main`
y deben mantener el repositorio limpio y las pruebas pasando.
