# Memento

Arquitectura reproducible para instalación, software y configuración de
sistemas Linux.

Memento nace de una idea simple:

> Linux se instala hasta en las piedras.

El objetivo no es esconder la complejidad de Linux, sino ponerla en el
lugar correcto: el operador decide y las herramientas ejecutan.

## Filosofía

Memento prioriza:

* control explícito del operador
* UX/IX simple y clara
* herramientas nativas del sistema
* instalación reproducible
* mínima instalación innecesaria
* separación entre instalar, actualizar y configurar
* modularidad
* escalabilidad

Regla fundamental:

> La detección automática informa. Nunca sustituye la decisión del
> operador.

El instalador puede detectar hardware, firmware, discos o dispositivos,
pero no debe asumir qué necesita una máquina ni instalar componentes
"por si acaso".

## Arquitectura

Memento se divide en tres etapas:

```
SCRIPT 1
BASE
   ↓
SCRIPT 2
SOFTWARE
   ↓
SCRIPT 3
DOTFILES
```

### Script 1 — Base

Responsabilidad:

```
SOURCE → SYSTEM
```

La fuente inicial puede ser un medio Linux arrancado localmente.

La primera implementación se inspira en la experiencia de ArchBang:
un menú pequeño que llama a herramientas especializadas del entorno de
instalación, permite al operador tomar decisiones y regresa al menú.

Script 1 no debe convertirse en un instalador "plug and play".

Su función es proporcionar control sobre:

* diagnóstico
* firmware
* particionado
* filesystem
* montaje
* despliegue del sistema
* boot
* configuración mínima
* verificación

La instalación y la actualización son operaciones diferentes.

### Script 2 — Software

Responsabilidad:

```
BASE → SOFTWARE
```

Perfiles iniciales:

```
min
med
max
```

El motor de instalación debe estar separado de los perfiles.

Los perfiles declaran qué software se desea.
El motor determina cómo instalarlo según el mecanismo correspondiente.

Ejemplos de herramientas opcionales:

```
gemini-cli
copilot
```

El nombre del paquete y el binario real no se confunden.

Ejemplos:

```
github-cli → gh
gemini-cli → gemini
```

### Script 3 — Dotfiles

Responsabilidad:

```
SOFTWARE → SISTEMA PERSONAL
```

Script 3 aplica:

* configuración de usuario
* configuración de sistema
* symlinks
* archivos runtime
* scripts
* templates
* verificación

No instala la base del sistema ni decide hardware.

## Instalación basada en imagen

Una de las ideas centrales de Memento es separar:

```
INSTALAR
ACTUALIZAR
```

La instalación de una imagen o medio Linux debe preservar la fuente
elegida.

Una ISO antigua no debe convertirse silenciosamente en un sistema
nuevo simplemente porque el instalador utiliza los repositorios actuales.

Cuando sea técnicamente posible, la fuente local debe ser el origen del
sistema instalado.

Este principio nació de la experiencia práctica con instalaciones
basadas en `pacstrap` y listas de paquetes.

## Diagnóstico

El diagnóstico es información, no decisión.

Ejemplo:

```
lspci -nnk
```

Capas:

```
aplicación: pciutils
comando:    lspci
shell:      bash
```

La salida puede utilizarse para conocer:

* CPU
* GPU
* dispositivos PCI
* red
* IDs PCI
* drivers del kernel

Ejemplo de UX:

```
Intel UHD 620 detectada.
Realtek RTL8723DE detectado.
MBR detectado.
UEFI detectado.
```

Memento no juzga el hardware ni decide por el operador.

## Memoria de instalación

La memoria necesaria durante la instalación debe ser temporal.

Conceptualmente:

```
/run/memento/
```

Debe almacenar únicamente información necesaria para continuar el flujo
actual.

No se crea una base de datos permanente cuando las herramientas
posteriores pueden consultar directamente el estado real.

Principio:

> Cada herramienta es autoridad sobre el estado que administra.

Ejemplos:

```
lspci      → hardware PCI
fdisk      → particiones
blkid      → filesystem / UUID
mount      → montajes
efibootmgr → entradas UEFI
pacman     → paquetes
```

Memento coordina estas herramientas.

## Convenciones

Archivos y directorios propios:

```
minúsculas
```

Comandos públicos:

```
máximo 3 caracteres cuando sea posible
```

No se pisan comandos existentes del sistema.

Se respetan los nombres oficiales de paquetes y ejecutables.

No confundir:

```
paquete
binario
interfaz propia
```

Ejemplo:

```
github-cli → gh
```

## Gramática de comandos

La dirección actual del diseño es:

```
APP + ACTION + SCOPE
```

Conceptos previstos:

```
APP
ACTION
SCOPE
```

Ejemplo conceptual:

```
g   → git
u   → update
a   → all
```

La gramática definitiva todavía debe cerrarse antes de implementarla.

LOCAL significa:

```
contexto detectable desde el directorio actual
```

GLOBAL significa:

```
universo definido explícitamente
```

Una operación local no debe crear silenciosamente un contexto que no
existe.

## Shell

`.bashrc` es configuración e interfaz de Bash.

No debe convertirse en una enciclopedia.

La lógica compleja debe vivir en herramientas dedicadas.

Bash se utiliza cuando Bash es la herramienta correcta.

Python se utiliza cuando la complejidad lo justifica.

Python no se utiliza por moda.

## Seguridad

GitHub utiliza:

```
SSH
Ed25519
passphrase
ssh-agent
```

La passphrase se conserva.

`ssh-agent` permite cargar la clave una vez durante la sesión y utilizarla
posteriormente sin repetir la passphrase en cada operación.

No se generan claves automáticamente.

No se registran claves automáticamente.

GitHub CLI (`gh`) es una herramienta independiente de SSH y Git.

## Infraestructura

Memento está pensado para crecer desde una máquina individual hacia:

```
homelab
├── 3 laptops
│
vpslab
└── 1 server

podman
└── 4 users
```

La arquitectura debe poder crecer sin convertirse en una colección de
scripts independientes y difíciles de mantener.

## Estado actual

El repositorio se encuentra en una etapa de transición desde una
configuración histórica basada en ArchBang hacia la arquitectura
Memento.

La configuración antigua se conserva temporalmente mientras se realiza
la auditoría y migración.

No se deben eliminar componentes por nombre sin analizar previamente
sus dependencias y propósito.

La migración se realiza en etapas:

```
1. documentar
2. auditar
3. decidir
4. eliminar o migrar
5. implementar
6. verificar
7. versionar
```

## Documentación

La arquitectura y las reglas de diseño se mantienen en:

```
docs/memento.md
```

Ese documento contiene las decisiones conceptuales y técnicas que deben
guiar la implementación.

## Principio fundamental

> Automatizar la ejecución no significa automatizar la decisión.

Memento debe hacer fácil ejecutar una decisión consciente sin ocultar
qué está ocurriendo.
