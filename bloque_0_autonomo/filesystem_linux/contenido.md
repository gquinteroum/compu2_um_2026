# El sistema de archivos de Linux

## ¿Por qué necesitás entender esto?

Probablemente vengas de Windows, donde los archivos están en `C:\`, los programas en `Program Files`, y no pensás mucho en cómo funciona todo por debajo. Linux es diferente. Para programar sistemas que manejan archivos, procesos, y comunicación entre ellos (que es exactamente lo que vamos a hacer en esta materia), necesitás entender cómo Linux organiza y gestiona sus archivos.

No es solo teoría: cuando tu programa necesite crear archivos temporales, vas a saber que van en `/tmp`. Cuando un script falle porque "permission denied", vas a entender por qué y cómo solucionarlo. Cuando necesites que un proceso lea un archivo que otro escribió, vas a saber cómo configurar los permisos.

Además, conceptos como los **inodos** van a aparecer cuando hablemos de procesos y file descriptors. Todo está conectado.

---

## "Todo es un archivo"

Esta es la filosofía fundamental de UNIX (y por herencia, de Linux). En Linux, casi todo se representa como un archivo:

- Los documentos de texto son archivos (obvio)
- Los programas ejecutables son archivos
- Los directorios son archivos (que contienen listas de otros archivos)
- Los dispositivos de hardware son archivos (tu disco duro es `/dev/sda`)
- La información de procesos son archivos (cada proceso tiene una carpeta en `/proc`)
- Hasta las conexiones de red pueden verse como archivos

Esta uniformidad es poderosa: las mismas herramientas y conceptos que usás para manipular archivos de texto sirven para interactuar con hardware y procesos.

---

## La estructura del árbol de directorios

A diferencia de Windows donde cada disco tiene su letra (`C:`, `D:`), en Linux hay una única raíz: `/`. Todo cuelga de ahí, incluso otros discos (que se "montan" en algún punto del árbol).

No necesitás memorizar todo, pero sí entender los directorios más importantes:

**`/home`** es donde viven los usuarios. Tu carpeta personal es `/home/tu_usuario`. Es el equivalente a `C:\Users\TuNombre`.

**`/etc`** (pronunciado "et-see" o "et-cetera") contiene configuración del sistema. Archivos como `/etc/passwd` (información de usuarios) o `/etc/hosts` (resolución de nombres) viven acá.

**`/tmp`** es para archivos temporales. Cualquier usuario puede escribir ahí, y generalmente se borra al reiniciar. Ideal para archivos que tu programa necesita brevemente.

**`/var`** contiene datos variables: logs en `/var/log`, bases de datos, colas de correo. Si algo cambia frecuentemente durante la operación normal, probablemente esté en `/var`.

**`/dev`** contiene los "archivos" que representan dispositivos. `/dev/sda` es tu primer disco duro, `/dev/null` es un agujero negro que descarta todo lo que le escribís (muy útil), `/dev/random` genera números aleatorios.

**`/proc`** es un filesystem virtual que expone información del kernel y los procesos. `/proc/cpuinfo` tiene información de tu CPU, `/proc/1234/` tiene información del proceso con PID 1234. No son archivos reales en disco, el kernel los genera al vuelo.

---

## Inodos: la verdad sobre los archivos

Acá viene el concepto que más confunde a la gente, pero que es fundamental para entender cómo funciona Linux.

Cuando pensás en un archivo, pensás en su nombre y su contenido. Pero para Linux, el nombre y el contenido son cosas separadas:

- El **contenido** del archivo vive en bloques de datos en el disco
- Los **metadatos** del archivo (permisos, tamaño, dónde están los bloques) viven en una estructura llamada **inodo**
- El **nombre** es simplemente una entrada en un directorio que apunta a un inodo

Leé eso de nuevo. El nombre del archivo no es parte del archivo. Es una referencia al inodo, que es quien realmente "es" el archivo.

### ¿Por qué importa esto?

Porque explica cosas que de otra forma parecen mágicas:

1. **Podés tener múltiples nombres para el mismo archivo** (enlaces duros). Dos nombres apuntando al mismo inodo son el mismo archivo, no copias.

2. **Podés renombrar un archivo instantáneamente**, sin importar su tamaño. Solo cambiás la entrada en el directorio, los datos no se mueven.

3. **Podés borrar un archivo mientras un programa lo está usando**, y el programa sigue funcionando. El archivo "desaparece" del directorio pero el inodo (y los datos) persisten hasta que nadie lo esté usando.

4. **Un archivo puede quedarse sin nombre pero seguir existiendo**. Mientras algún proceso lo tenga abierto, el inodo sigue vivo.

### ¿Qué contiene un inodo?

Todo lo que el sistema necesita saber sobre el archivo, excepto su nombre:

- Tipo de archivo (regular, directorio, enlace simbólico, etc.)
- Permisos (quién puede leer, escribir, ejecutar)
- Propietario y grupo
- Tamaño
- Timestamps (cuándo se accedió, modificó, cambió)
- Punteros a los bloques de datos

### Ver información del inodo

El comando `stat` te muestra todo:

```bash
$ stat archivo.txt
  File: archivo.txt
  Size: 1024            Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d      Inode: 12345       Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1000/usuario)   Gid: ( 1000/usuario)
Access: 2026-01-15 10:30:00
Modify: 2026-01-14 15:20:00
Change: 2026-01-14 15:20:00
```

**Links: 1** significa que hay un solo nombre apuntando a este inodo. Si creo un enlace duro, ese número sube a 2.

### Los tres timestamps

Linux guarda tres tiempos para cada archivo:

- **atime** (access time): última vez que se leyó el contenido
- **mtime** (modify time): última vez que se modificó el contenido
- **ctime** (change time): última vez que se modificaron los metadatos

Ojo: **ctime no es "creation time"**. Es un error muy común. Linux tradicionalmente no guarda la fecha de creación (aunque filesystems modernos como ext4 sí lo hacen internamente).

---

## Permisos: quién puede hacer qué

Linux es un sistema multiusuario desde su origen. Los permisos controlan quién puede acceder a cada archivo.

### El modelo básico

Cada archivo tiene permisos para tres categorías de usuarios:

1. **Usuario (u)**: el dueño del archivo
2. **Grupo (g)**: los miembros del grupo asignado al archivo
3. **Otros (o)**: todos los demás

Y tres tipos de permisos:

1. **Lectura (r)**: poder ver el contenido
2. **Escritura (w)**: poder modificar
3. **Ejecución (x)**: poder ejecutar (si es programa) o acceder (si es directorio)

### Interpretando los permisos

Cuando hacés `ls -l`, ves algo como:

```
-rwxr-xr--  1 juan  desarrolladores  4096 ene 15 archivo.py
```

Ese `-rwxr-xr--` se lee así:

```
- rwx r-x r--
│ └┬┘ └┬┘ └┬┘
│  │   │   └── otros: pueden leer
│  │   └────── grupo: pueden leer y ejecutar
│  └────────── usuario: puede leer, escribir y ejecutar
└───────────── tipo: archivo regular (- = regular, d = directorio, l = enlace)
```

### Notación octal

Los permisos también se expresan como números. Cada permiso tiene un valor:
- r = 4
- w = 2
- x = 1

Sumás los valores para cada categoría:

- `rwx` = 4+2+1 = 7
- `r-x` = 4+0+1 = 5
- `r--` = 4+0+0 = 4

Entonces `rwxr-xr--` = 754.

Permisos comunes que vas a ver:

| Octal | Simbólico | Significado |
|-------|-----------|-------------|
| 644 | rw-r--r-- | Archivo normal: dueño lee/escribe, otros solo leen |
| 755 | rwxr-xr-x | Script o directorio: dueño todo, otros leen/ejecutan |
| 600 | rw------- | Privado: solo el dueño accede |
| 777 | rwxrwxrwx | Todos pueden todo (¡evitar!) |

### Cambiando permisos

El comando `chmod` cambia permisos. Podés usar notación octal o simbólica:

```bash
# Octal: establecer permisos exactos
chmod 755 script.sh

# Simbólico: modificar permisos existentes
chmod u+x script.sh      # agregar ejecución al usuario
chmod go-w archivo.txt   # quitar escritura a grupo y otros
chmod a+r archivo.txt    # agregar lectura a todos (a = all)
```

### Permisos en directorios

Los permisos significan algo diferente para directorios:

- **r**: poder listar el contenido (`ls`)
- **w**: poder crear y borrar archivos dentro
- **x**: poder acceder (`cd`) y atravesar el directorio

Un directorio con `r--` pero sin `x` es raro: podés ver los nombres de los archivos pero no podés acceder a ellos.

### El problema de /tmp y el sticky bit

`/tmp` es escribible por todos (`777`), pero no querés que cualquiera pueda borrar los archivos temporales de otros. Para eso existe el **sticky bit**: en un directorio con sticky bit, solo podés borrar archivos que te pertenecen.

```bash
$ ls -ld /tmp
drwxrwxrwt 10 root root 4096 ene 15 10:00 /tmp
```

Esa `t` al final indica el sticky bit.

### Permisos desde Python

```python
import os
import stat

# Ver permisos
info = os.stat("archivo.txt")
print(f"Permisos: {oct(info.st_mode)}")

# Cambiar permisos
os.chmod("script.sh", 0o755)  # nota el 0o para octal

# Usando pathlib (más moderno)
from pathlib import Path
Path("script.sh").chmod(0o755)
```

---

## Enlaces: múltiples caminos al mismo archivo

### Enlaces duros

Un enlace duro es otro nombre para el mismo archivo. No es una copia, es literalmente el mismo archivo con dos nombres.

Cuando creás un enlace duro:

```bash
ln original.txt enlace.txt
```

Ahora `original.txt` y `enlace.txt` apuntan al mismo inodo. Si modificás uno, el otro refleja el cambio inmediatamente (porque son el mismo archivo).

```bash
$ ls -li
12345 -rw-r--r-- 2 user user 100 ene 15 original.txt
12345 -rw-r--r-- 2 user user 100 ene 15 enlace.txt
```

Fijate que:
- El número de inodo (12345) es el mismo
- El contador de enlaces es 2

¿Qué pasa si borrás `original.txt`? Los datos persisten porque el inodo todavía tiene un enlace (`enlace.txt`). El archivo solo se borra realmente cuando el contador de enlaces llega a 0.

**Limitaciones de los enlaces duros:**
- No pueden cruzar filesystems (particiones)
- No pueden apuntar a directorios (para evitar ciclos)

### Enlaces simbólicos (symlinks)

Un enlace simbólico es un archivo especial que contiene la ruta a otro archivo. Es como un "acceso directo" de Windows.

```bash
ln -s original.txt symlink.txt
```

A diferencia del enlace duro, el symlink tiene su propio inodo y solo contiene el texto de la ruta:

```bash
$ ls -l symlink.txt
lrwxrwxrwx 1 user user 12 ene 15 symlink.txt -> original.txt
```

La `l` al principio indica que es un enlace simbólico.

**¿Qué pasa si borrás el original?** El symlink queda "roto". Sigue existiendo pero apunta a algo que ya no existe:

```bash
$ cat symlink.txt
cat: symlink.txt: No such file or directory
```

**Ventajas de los symlinks:**
- Pueden cruzar filesystems
- Pueden apuntar a directorios
- Es claro visualmente que son enlaces (`ls -l` muestra `->`)

### ¿Cuándo usar cada uno?

**Enlace duro:** cuando querés que dos nombres sean intercambiables y no te importa si borran el "original". Útil para backups eficientes.

**Enlace simbólico:** para todo lo demás. Son más flexibles y más obvios.

En la práctica, los symlinks son mucho más comunes.

---

## Trabajando con archivos desde Python

Python tiene varias formas de trabajar con el filesystem. La más moderna y recomendada es `pathlib`.

### pathlib: el enfoque moderno

```python
from pathlib import Path

# Crear un Path
archivo = Path("/home/usuario/datos.txt")

# Propiedades
print(archivo.name)      # datos.txt
print(archivo.stem)      # datos
print(archivo.suffix)    # .txt
print(archivo.parent)    # /home/usuario

# Verificaciones
if archivo.exists():
    if archivo.is_file():
        print("Es un archivo")
    elif archivo.is_dir():
        print("Es un directorio")

# Leer y escribir
contenido = archivo.read_text()
archivo.write_text("nuevo contenido")

# Construir rutas (se usa / como operador)
config = Path.home() / ".config" / "miapp" / "settings.json"

# Listar directorio
for item in Path(".").iterdir():
    print(item)

# Buscar archivos con patrón
for py_file in Path(".").glob("**/*.py"):
    print(py_file)
```

La sintaxis con `/` para construir rutas es muy conveniente y funciona en todos los sistemas operativos.

### os y os.path: el enfoque clásico

Antes de `pathlib`, se usaba el módulo `os`:

```python
import os

# Rutas
ruta = os.path.join("carpeta", "archivo.txt")
directorio = os.path.dirname("/home/user/file.txt")
nombre = os.path.basename("/home/user/file.txt")

# Verificaciones
os.path.exists("archivo.txt")
os.path.isfile("archivo.txt")
os.path.isdir("carpeta")

# Operaciones
os.mkdir("nueva_carpeta")
os.makedirs("a/b/c", exist_ok=True)  # crea intermedios
os.remove("archivo.txt")
os.rename("viejo.txt", "nuevo.txt")
```

Todavía lo vas a ver en código existente, pero para código nuevo preferí `pathlib`.

### shutil: operaciones de alto nivel

Para copiar, mover, y borrar árboles de directorios:

```python
import shutil

# Copiar archivo (copy2 preserva metadatos)
shutil.copy2("origen.txt", "destino.txt")

# Copiar directorio completo
shutil.copytree("origen/", "destino/")

# Mover
shutil.move("archivo.txt", "nueva_ubicacion/")

# Borrar directorio con todo su contenido
shutil.rmtree("carpeta_a_borrar/")
```

`shutil.rmtree` es peligroso: borra todo sin preguntar. Usalo con cuidado.

---

## Juntando todo: un ejemplo práctico

Supongamos que querés escribir un script que limpie archivos temporales viejos. Necesitás:

1. Recorrer un directorio
2. Ver la fecha de modificación de cada archivo
3. Borrar los que tengan más de 7 días

```python
#!/usr/bin/env python3
from pathlib import Path
import time

def limpiar_temporales(directorio, dias=7):
    """Borra archivos más viejos que `dias` días."""
    limite = time.time() - (dias * 24 * 60 * 60)

    dir_path = Path(directorio)
    if not dir_path.is_dir():
        print(f"Error: {directorio} no es un directorio")
        return

    for archivo in dir_path.iterdir():
        if archivo.is_file():
            # stat() nos da los metadatos del inodo
            stats = archivo.stat()
            if stats.st_mtime < limite:
                print(f"Borrando: {archivo}")
                archivo.unlink()

if __name__ == "__main__":
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument("directorio")
    parser.add_argument("--dias", type=int, default=7)
    args = parser.parse_args()

    limpiar_temporales(args.directorio, args.dias)
```

Fijate cómo usamos:
- `pathlib` para manejar rutas
- `stat()` para acceder al inodo y obtener `st_mtime`
- `argparse` para los argumentos (lo que vimos en el módulo anterior)

Todo conectado.

---

## Resumen

| Concepto | Qué es | Por qué importa |
|----------|--------|-----------------|
| Inodo | Estructura con metadatos del archivo | Explica enlaces, permisos, y comportamiento al borrar |
| Permisos | Control de acceso (rwx para u/g/o) | Seguridad y multiusuario |
| Enlace duro | Otro nombre para el mismo inodo | Múltiples nombres, sin copiar datos |
| Enlace simbólico | Archivo que contiene una ruta | Flexibilidad, cruza filesystems |
| pathlib | Módulo Python para rutas | Forma moderna y elegante de trabajar con archivos |

Los comandos esenciales:

| Comando | Función |
|---------|---------|
| `ls -l` | Ver permisos y metadatos |
| `ls -i` | Ver número de inodo |
| `stat` | Información detallada del inodo |
| `chmod` | Cambiar permisos |
| `ln` | Crear enlace duro |
| `ln -s` | Crear enlace simbólico |
| `readlink` | Ver destino de symlink |

---

*Computación II - 2026 - Bloque 0 Autónomo*
