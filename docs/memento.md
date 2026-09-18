# Memento

Arquitectura de instalación y configuración Linux.

## Filosofía

Linux se instala hasta en las piedras.

Memento prioriza:

- control explícito del operador
- experiencia UX/IX simple
- herramientas nativas del entorno
- instalación reproducible
- mínima instalación innecesaria
- separación entre instalar, actualizar y configurar
- arquitectura modular y escalable

La detección automática informa.
La detección automática nunca sustituye la decisión del operador.

## Roles

Rommel aporta UX/IX, experiencia de distrohopper y validación en máquinas reales.

Memento aporta arquitectura, orquestación, diseño de sistemas, scripts y automatización.

## Capas

### Script 1 — Base

Responsabilidad:

    SOURCE → SYSTEM

Convierte el medio/fuente Linux disponible en un sistema instalable.

Debe poder evolucionar para:

- metal
- VM
- VPS

La interfaz puede inspirarse en ArchBang:

    menú
      ↓
    herramienta especializada
      ↓
    volver al menú

Script 1 no instala software de escritorio ni dotfiles.

No incluye:

- swap
- zram
- dotfiles
- ThemeBang
- Waybar
- Mako
- Rofi
- greetd
- software opcional

El objetivo actual de almacenamiento incluye:

- GPT / MBR
- UEFI / BIOS
- ESP cuando corresponda
- ext4 / btrfs
- ESP objetivo de 33 MiB FAT32

La instalación no debe actualizar silenciosamente la fuente.

## Diagnóstico

El diagnóstico existe para informar.

Ejemplo:

    lspci -nnk

Capas:

    aplicación: pciutils
    comando:    lspci
    shell:      bash

El diagnóstico puede mostrar:

- CPU
- GPU
- red
- dispositivos PCI
- IDs
- drivers del kernel

El diagnóstico no decide por el usuario.

Ejemplo correcto:

    Intel UHD 620 detectada.
    Realtek RTL8723DE detectado.

La interfaz debe ser neutral:

    MBR detectado.
    UEFI detectado.
    GPT detectado.

Nunca debe juzgar el hardware.

## Memoria de instalación

La información temporal solo debe existir mientras sea necesaria.

Ejemplo:

    /run/memento/

No se crea una base de datos permanente solo para recordar datos
que las herramientas posteriores pueden consultar por sí mismas.

Principio:

    cada herramienta es autoridad sobre el estado que administra.

Ejemplos:

    lspci      → hardware PCI
    fdisk      → particiones
    blkid      → filesystem / UUID
    mount      → montajes
    efibootmgr → entradas UEFI
    pacman     → paquetes

Memento orquesta estas herramientas.

## Script 2 — Software

Responsabilidad:

    BASE → SOFTWARE

Perfiles iniciales:

    min
    med
    max

El motor debe estar separado de los perfiles.

Los perfiles definen qué software instalar.
El motor define cómo procesarlo.

Software opcional puede incluir herramientas como:

    gemini-cli
    copilot

El nombre del paquete, el binario real y nuestra interfaz son conceptos
distintos.

Ejemplo:

    github-cli → gh
    gemini-cli → gemini

Nunca se debe inventar una interfaz que pise un comando real existente.

## Script 3 — Dotfiles

Responsabilidad:

    SOFTWARE → SISTEMA PERSONAL

Script 3 aplica:

- configuración de usuario
- configuración de sistema
- symlinks
- archivos runtime
- scripts
- templates
- verificación

No debe instalar el sistema base.

No debe decidir el hardware.

No debe convertir Bash en una aplicación monolítica.

La configuración estática puede usar symlinks.
Los archivos runtime deben permanecer como archivos normales cuando
herramientas externas necesiten modificarlos.

## Bash

`.bashrc` es interfaz y configuración del shell.

No debe convertirse en una enciclopedia.

La lógica compleja debe pertenecer a herramientas dedicadas.

Bash se utiliza cuando Bash es la herramienta correcta.
Python se utiliza cuando la complejidad lo justifica.

No se usa Python por moda.

## Convenciones

Archivos y directorios:

    minúsculas

Comandos públicos:

    máximo 3 caracteres cuando sea posible

No se pisan comandos reales del sistema.

Se respetan los nombres oficiales de paquetes y binarios.

Ejemplo:

    github-cli → gh

Diferenciar:

    paquete
    binario
    interfaz propia

## Gramática de comandos

La dirección actual es:

    APP + ACTION + SCOPE

Ejemplo conceptual:

    g   → git
    u   → update
    a   → all / alcance global

Pero la gramática definitiva todavía debe cerrarse antes de implementar
los comandos.

LOCAL significa:

    contexto detectable desde el directorio actual

GLOBAL significa:

    universo definido explícitamente

Un comando local nunca debe crear silenciosamente un contexto que no
existe.

## Seguridad

GitHub:

    SSH
    Ed25519
    passphrase
    ssh-agent

La passphrase no se elimina.

ssh-agent permite introducirla una vez por sesión.

No se generan ni registran claves automáticamente.

GitHub CLI (`gh`) es una herramienta independiente.

## Infraestructura futura

Memento debe poder evolucionar hacia:

    homelab
    ├── 3 laptops
    vpslab
    └── 1 server

    podman
    └── 4 usuarios

La arquitectura debe escalar sin convertir cada nueva máquina o usuario
en una colección de scripts independientes.

## Principio fundamental

Automatizar la ejecución no significa automatizar la decisión.

La herramienta debe hacer fácil ejecutar una decisión consciente,
no decidir silenciosamente qué necesita la máquina.
