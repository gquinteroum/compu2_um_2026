# Filesystem de Linux - Ejercicios

## Cómo abordar estos ejercicios

El filesystem de Linux es algo que usás todos los días sin pensarlo. Abrís archivos, guardás cosas, navegás carpetas. Pero hay mucho más bajo la superficie, y entenderlo te va a dar superpoderes para diagnosticar problemas, escribir código más robusto, y entender cómo funcionan las herramientas que usás.

Estos ejercicios combinan exploración desde la terminal (para entender qué hay) con programación en Python (para automatizar y crear herramientas). La idea es que primero explores manualmente, entiendas lo que estás viendo, y después lo automatices.

**Tip importante:** Usá `man` liberalmente. Si no sabés qué hace un comando o una opción, `man ls`, `man stat`, `man chmod` te dan la documentación completa. Es la forma en que aprenden los profesionales.

---

## Parte 1: Exploración desde la terminal

Antes de escribir código, necesitás poder "ver" el filesystem con tus propios ojos. Estos ejercicios te enseñan a usar las herramientas de diagnóstico.

### Ejercicio 1.1: Investigando tu sistema

Usando solo la terminal, respondé estas preguntas:

1. **¿Cuántos archivos hay directamente en `/etc`?** (sin contar los que están en subdirectorios)

2. **¿Cuál es el tamaño exacto de `/etc/passwd`?** (en bytes)

3. **¿Qué tipo de archivo es `/dev/null`?** ¿Por qué es especial?

4. **¿A dónde apunta el enlace simbólico `/bin`?** (en sistemas modernos, `/bin` suele ser un symlink)

5. **¿Cuál es el número de inodo de tu archivo `.bashrc`?** (o `.zshrc` si usás zsh)

6. **¿Cuántos enlaces duros tiene el directorio `/home`?** ¿Por qué tiene ese número?

**Comandos que te van a servir:**
- `ls` con varias opciones (`-l`, `-a`, `-i`, `-la`)
- `stat` para información detallada de un archivo
- `file` para detectar el tipo de archivo
- `readlink` para ver a dónde apunta un symlink
- `wc -l` para contar líneas (combinado con `ls`)

Documentá tus respuestas y los comandos que usaste. El proceso de descubrimiento es tan importante como las respuestas.

### Ejercicio 1.2: Entendiendo permisos

Este ejercicio te hace experimentar con permisos de forma práctica.

**Paso 1:** Creá un script simple:
```bash
echo 'echo "Hola desde el script"' > mi_script.sh
```

**Paso 2:** Intentá ejecutarlo:
```bash
./mi_script.sh
```

¿Qué error te da? ¿Por qué?

**Paso 3:** Mirá los permisos actuales:
```bash
ls -l mi_script.sh
```

Probablemente veas algo como `-rw-r--r--`. ¿Qué significa cada parte?

**Paso 4:** Agregá permiso de ejecución:
```bash
chmod +x mi_script.sh
```

**Paso 5:** Verificá que cambiaron los permisos y ejecutá el script.

**Para pensar:**
- ¿Cuál es la diferencia entre `chmod +x` y `chmod 755`?
- Si el archivo tiene permisos `644`, ¿qué usuarios pueden leerlo? ¿Quiénes pueden escribirlo?
- ¿Por qué los directorios necesitan permiso de ejecución (`x`) para poder entrar en ellos?

### Ejercicio 1.3: Enlaces duros vs simbólicos

Este ejercicio es fundamental para entender la diferencia entre estos dos tipos de enlaces.

**Preparación:**
```bash
# Crear un directorio de trabajo
mkdir experimento_enlaces
cd experimento_enlaces

# Crear un archivo original
echo "Este es el contenido original" > original.txt
```

**Paso 1:** Crear ambos tipos de enlaces:
```bash
ln original.txt enlace_duro.txt
ln -s original.txt enlace_simbolico.txt
```

**Paso 2:** Mirá los inodos:
```bash
ls -li
```

¿Qué observás? El enlace duro debería tener el mismo inodo que el original. El simbólico tiene su propio inodo.

**Paso 3:** Verificá el contador de enlaces:
```bash
stat original.txt | grep Links
```

Debería decir "Links: 2" (el original + el enlace duro).

**Paso 4:** Ahora lo interesante. Borrá el archivo original:
```bash
rm original.txt
```

**Paso 5:** Intentá leer cada enlace:
```bash
cat enlace_duro.txt
cat enlace_simbolico.txt
```

¿Qué pasó? El enlace duro sigue funcionando (porque apunta directamente al inodo, que todavía existe). El simbólico está "roto" (porque apuntaba al *nombre* `original.txt`, que ya no existe).

**Paso 6:** Verificá con `ls -l`:
```bash
ls -l
```

El enlace simbólico probablemente se muestre en rojo, indicando que está roto.

**Reflexión:** ¿Cuándo usarías cada tipo de enlace? Los simbólicos son más flexibles (funcionan entre filesystems, pueden apuntar a directorios), pero los duros son más robustos (sobreviven a que se borre el archivo original).

---

## Parte 2: Herramientas en Python

Ahora que entendés cómo funciona el filesystem desde la terminal, vas a crear herramientas en Python que automatizan tareas comunes.

### Ejercicio 2.1: Inspector de archivos (OBLIGATORIO)

Creá `inspector.py`, una herramienta que muestre información detallada sobre cualquier archivo.

```bash
$ python inspector.py /etc/passwd
Archivo: /etc/passwd
Tipo: archivo regular
Tamaño: 2847 bytes (2.78 KB)
Permisos: rw-r--r-- (644)
Propietario: root (uid: 0)
Grupo: root (gid: 0)
Inodo: 1234567
Enlaces duros: 1
Creación: 2025-12-01 10:30:00
Última modificación: 2026-01-15 14:22:33
Último acceso: 2026-02-08 09:15:00

$ python inspector.py /dev/null
Archivo: /dev/null
Tipo: dispositivo de caracteres
...

$ python inspector.py /bin
Archivo: /bin
Tipo: enlace simbólico -> usr/bin
...

$ python inspector.py /home
Archivo: /home
Tipo: directorio
Tamaño: 4096 bytes
...
Contenido: 3 elementos
```

Lo que necesitás implementar:
- Detectar el tipo de archivo (regular, directorio, symlink, dispositivo, etc.)
- Mostrar permisos en formato legible (`rwxr-xr-x`) y octal (`755`)
- Resolver nombres de usuario y grupo a partir de uid/gid
- Para symlinks, mostrar a dónde apuntan
- Para directorios, contar cuántos elementos contienen

**Módulos que vas a usar:**
- `os.stat()` o `pathlib.Path.stat()` para obtener metadata
- Módulo `stat` para interpretar los bits de modo
- `pwd.getpwuid()` para obtener nombre de usuario desde uid
- `grp.getgrgid()` para obtener nombre de grupo desde gid
- `os.readlink()` para symlinks

### Ejercicio 2.2: Buscador de archivos grandes

El disco se llena y necesitás saber qué está ocupando espacio. Creá `find_large.py`:

```bash
$ python find_large.py /var/log --min-size 1M
/var/log/syslog (5.2 MB)
/var/log/kern.log (2.1 MB)
/var/log/auth.log (1.5 MB)
Total: 3 archivos, 8.8 MB

$ python find_large.py . --min-size 100K --type f
./data/dataset.csv (450 KB)
./backups/old.tar (230 KB)
Total: 2 archivos, 680 KB

$ python find_large.py /home --min-size 50M --top 10
Los 10 archivos más grandes:
  1. /home/user/Videos/pelicula.mp4 (1.2 GB)
  2. /home/user/Downloads/iso.iso (800 MB)
  ...
```

Lo que necesitás:
- Argumento posicional: directorio a buscar
- `--min-size`: tamaño mínimo (debe parsear K, M, G)
- `--type`: filtrar por tipo (`f` = archivo, `d` = directorio)
- `--top N`: mostrar solo los N más grandes
- Búsqueda recursiva en subdirectorios

El parseo de tamaños es un desafío interesante. "1M" debería convertirse a 1048576 bytes (1024 * 1024). Podés usar expresiones regulares o simplemente chequear el último carácter.

### Ejercicio 2.3: Detector de enlaces rotos

Los symlinks rotos son basura que ocupa espacio y puede causar errores. Creá `broken_links.py`:

```bash
$ python broken_links.py /home/user
Buscando enlaces simbólicos rotos en /home/user...

Enlaces rotos encontrados:
  /home/user/.config/old_app -> /opt/old_app/config (no existe)
  /home/user/projects/lib -> ../shared/lib (no existe)
  /home/user/bin/tool -> /usr/local/bin/tool (no existe)

Total: 3 enlaces rotos

$ python broken_links.py /home/user --delete
[encontraría los enlaces y preguntaría antes de borrar cada uno]

$ python broken_links.py /etc --quiet
3
```

Lo que necesitás:
- Argumento posicional: directorio a buscar
- `--delete`: ofrecer borrar los enlaces rotos (con confirmación)
- `--quiet`: solo mostrar el conteo
- Búsqueda recursiva

Para detectar un symlink roto: `os.path.islink(path)` devuelve True, pero `os.path.exists(path)` devuelve False (porque el destino no existe).

---

## Parte 3: Herramientas avanzadas

Estos ejercicios producen herramientas más completas que combinan múltiples conceptos.

### Ejercicio 3.1: Comparador de directorios

Cuando movés archivos o hacés backups, necesitás saber qué cambió. Creá `diffdir.py`:

```bash
$ python diffdir.py proyecto_v1 proyecto_v2
Comparando proyecto_v1 con proyecto_v2...

Solo en proyecto_v1:
  archivo_borrado.txt
  carpeta_vieja/

Solo en proyecto_v2:
  archivo_nuevo.py
  feature/

Modificados (tamaño diferente):
  config.json (1024 -> 2048 bytes)

Modificados (fecha diferente):
  main.py (2026-01-10 -> 2026-01-15)
  utils.py (2026-01-10 -> 2026-01-15)

Idénticos: 15 archivos

$ python diffdir.py dir1 dir2 --checksum
[compara contenido usando hash, no solo metadata]

$ python diffdir.py dir1 dir2 --recursive
[incluye subdirectorios]
```

Lo que necesitás:
- Comparar qué archivos existen en cada directorio
- Comparar tamaños de archivos con el mismo nombre
- Comparar fechas de modificación
- `--recursive`: incluir subdirectorios
- `--checksum`: comparar contenido (usando hashlib)

Este ejercicio es un buen test de tu entendimiento de cómo funcionan los metadatos del filesystem.

### Ejercicio 3.2: Analizador de uso de disco

Similar a `du`, pero con características adicionales. Creá `diskusage.py`:

```bash
$ python diskusage.py /home/user --depth 1
1.2G    /home/user/Documents
256M    /home/user/Downloads
128M    /home/user/.cache
45M     /home/user/projects
12K     /home/user/.bashrc
─────────────────────────
Total: 1.6G

$ python diskusage.py . --top 5
Los 5 archivos/carpetas más grandes:
  1. ./node_modules (450 MB)
  2. ./build (120 MB)
  3. ./.git (45 MB)
  4. ./data/dataset.csv (12 MB)
  5. ./logs/app.log (5 MB)

$ python diskusage.py . --depth 2 --exclude "node_modules,*.log"
[muestra uso excluyendo node_modules y archivos .log]
```

Lo que necesitás:
- Argumento posicional: directorio a analizar
- `--depth N`: profundidad de análisis (1 = solo primer nivel)
- `--top N`: mostrar los N más grandes
- `--exclude PATTERNS`: excluir patrones (separados por coma)
- `--human`: tamaños legibles (K, M, G) - activado por default

El cálculo de tamaño de directorios requiere sumar recursivamente el tamaño de todos los archivos dentro.

### Ejercicio 3.3: Sincronizador de directorios

Una versión simplificada de `rsync`. Creá `sync.py`:

```bash
$ python sync.py origen/ destino/
Analizando diferencias...

Cambios detectados:
  NUEVO:      archivo1.txt (15 KB)
  NUEVO:      carpeta/archivo2.py (3 KB)
  MODIFICADO: config.json (cambiado 2026-01-15)
  ELIMINADO:  viejo.txt (existe en destino pero no en origen)

Resumen: 2 nuevos, 1 modificado, 1 eliminado

¿Proceder con la sincronización? [s/N] s

Copiando archivo1.txt... OK
Copiando carpeta/archivo2.py... OK
Actualizando config.json... OK
Completado.

$ python sync.py origen/ destino/ --dry-run
[muestra qué haría sin ejecutar nada]

$ python sync.py origen/ destino/ --delete
[también elimina archivos que no existen en origen]
```

Lo que necesitás:
- Dos argumentos posicionales: origen y destino
- `--dry-run`: simular sin ejecutar
- `--delete`: eliminar archivos extra en destino
- `--exclude PATTERN`: excluir archivos que coincidan
- Preservar fechas de modificación (usar `shutil.copy2()`)

Este es probablemente el ejercicio más complejo de esta sección. Requiere combinar todo lo que aprendiste: recorrer directorios, comparar metadata, copiar archivos, manejar errores.

---

## Parte 4: Para los que quieren más

### Ejercicio 4.1: Monitor de cambios en tiempo real

Creá `watch.py` que monitoree un directorio y reporte cambios:

```bash
$ python watch.py /var/log
Monitoreando /var/log (Ctrl+C para salir)

[10:30:15] MODIFICADO: syslog (tamaño: 15234 -> 15456)
[10:30:16] CREADO: new_app.log
[10:30:20] ELIMINADO: old_temp.log
[10:30:25] RENOMBRADO: app.log -> app.log.1
```

En Linux podés usar `inotify` para esto (investigá la biblioteca `inotify-simple`). Alternativamente, podés hacer polling: guardar el estado del directorio, esperar un segundo, comparar con el nuevo estado.

### Ejercicio 4.2: Normalizador de permisos

Creá `fixperms.py` que normalice permisos según reglas:

```bash
$ python fixperms.py proyecto/ --files 644 --dirs 755 --scripts 755
Analizando 150 archivos en proyecto/...

Cambios necesarios:
  Archivos regulares (-> 644): 23
  Directorios (-> 755): 5
  Scripts .sh/.py ejecutables (-> 755): 3

¿Aplicar cambios? [s/N] s
Aplicando... OK
```

Esto es muy útil después de copiar archivos de Windows o de extraer archivos comprimidos que no preservaron los permisos correctamente.

### Ejercicio 4.3: Deduplicador de archivos

Creá `dedup.py` que encuentre archivos duplicados por contenido:

```bash
$ python dedup.py ~/Downloads
Calculando hashes de 1234 archivos...

Duplicados encontrados:

Grupo 1 (3 copias, 5.2 MB cada una):
  foto_original.jpg
  foto_copia.jpg
  backup/foto.jpg

Grupo 2 (2 copias, 1.1 MB cada una):
  documento.pdf
  old/documento_v1.pdf

Espacio recuperable: 11.5 MB

$ python dedup.py ~/Downloads --delete
[pregunta cuál mantener de cada grupo y borra los demás]
```

El truco es calcular el hash SHA256 de cada archivo y agrupar los que tienen el mismo hash. Para archivos grandes, podés optimizar comparando primero el tamaño (archivos de diferente tamaño no pueden ser iguales) y solo calcular el hash de los que coinciden en tamaño.

---

## Checklist de entrega

Para el Bloque 0:

| Ejercicio | Tipo | Estado |
|-----------|------|--------|
| 1.1 | Respuestas escritas | Recomendado |
| 1.2 | Demostración de chmod | Recomendado |
| 1.3 | Experimento de enlaces | Recomendado |
| **2.1** | **`inspector.py`** | **OBLIGATORIO** |
| 2.2 | `find_large.py` | Recomendado |
| 2.3 | `broken_links.py` | Recomendado |
| 3.1 o 3.2 | Uno de los avanzados | Recomendado |

**Ubicación en tu repositorio:**
```
computacion2-2026/
└── bloque_0/
    └── filesystem/
        ├── ejercicio_1_1_respuestas.txt
        ├── inspector.py      ← OBLIGATORIO
        ├── find_large.py
        ├── broken_links.py
        └── diffdir.py (o diskusage.py)
```

**Todos los scripts deben:**
- Manejar errores de permisos y archivos inexistentes graciosamente
- Tener mensajes de error claros
- Funcionar con paths absolutos y relativos
- No explotar con symlinks rotos

---

*Computación II - 2026 - Bloque 0 Autónomo*
