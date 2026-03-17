# Argumentos de línea de comandos - Ejercicios

## Cómo abordar estos ejercicios

Estos ejercicios están diseñados para que construyas habilidades progresivamente. No los hagas mecánicamente: en cada uno, pensá por qué el programa necesita esos argumentos específicos y cómo sería usarlo si fueras un usuario que no conoce el código.

Un buen programa de línea de comandos es como un buen empleado: hace lo que le pedís, te avisa claramente si algo está mal, y tiene la cortesía de explicarte cómo usarlo cuando le preguntás.

**Tip práctico:** Antes de escribir código, siempre definí primero cómo querés que se vea la interfaz. ¿Qué argumentos son obligatorios? ¿Cuáles opcionales? ¿Qué valores por defecto tienen sentido? Después de tener eso claro, implementar con argparse es casi mecánico.

---

## Parte 1: Los primeros pasos con sys.argv

Antes de usar argparse, vale la pena entender qué pasa "por debajo". Estos ejercicios usan `sys.argv` directamente para que veas las limitaciones que motivaron la creación de herramientas más sofisticadas.

### Ejercicio 1.1: Tu primer script con argumentos

El clásico "Hola mundo", pero recibiendo el nombre como argumento.

Creá un archivo `saludo.py` que funcione así:

```bash
$ python saludo.py Juan
Hola, Juan!

$ python saludo.py María Elena
Hola, María!

$ python saludo.py
Uso: saludo.py <nombre>
```

Fijate que cuando no se pasa un nombre, el programa no explota con un error críptico de Python. Muestra un mensaje útil y termina limpiamente.

**Para pensar:** ¿Qué pasa si el usuario escribe `python saludo.py María Elena`? ¿Cómo lo manejarías para que salude "María Elena" en lugar de solo "María"?

### Ejercicio 1.2: Calculadora de argumentos

Creá `suma.py` que sume todos los números que reciba:

```bash
$ python suma.py 1 2 3 4 5
Suma: 15

$ python suma.py 10 20
Suma: 30

$ python suma.py
Suma: 0

$ python suma.py 3.14 2.86
Suma: 6.0
```

Este ejercicio te va a hacer pensar en cómo convertir strings a números y qué pasa cuando el usuario pasa algo que no es un número. Un programa robusto no debería explotar si alguien escribe `python suma.py hola`.

### Ejercicio 1.3: Contador de líneas

Creá `wc_simple.py` (inspirado en el comando `wc` de Unix) que cuente las líneas de un archivo:

```bash
$ python wc_simple.py poema.txt
42 líneas

$ python wc_simple.py
Error: Debe especificar un archivo

$ python wc_simple.py archivo_inexistente.txt
Error: No se puede leer 'archivo_inexistente.txt'
```

Acá empezás a ver por qué `sys.argv` se vuelve tedioso: tenés que manejar manualmente el caso de que falte el argumento, de que el archivo no exista, de que no tengas permisos... Todo código que vas a tener que repetir en cada script.

---

## Parte 2: Entrando a argparse

Ahora que experimentaste las limitaciones de `sys.argv`, estos ejercicios te muestran cómo argparse resuelve todos esos problemas automáticamente.

### Ejercicio 2.1: Conversor de temperatura

Un clásico de la programación, pero hecho correctamente como herramienta de línea de comandos.

Creá `temperatura.py` que convierta entre Celsius y Fahrenheit:

```bash
$ python temperatura.py 100 --to fahrenheit
100°C = 212.0°F

$ python temperatura.py 32 --to celsius
32°F = 0.0°C

$ python temperatura.py 0 -t celsius
0°F = -17.78°C

$ python temperatura.py --help
usage: temperatura.py [-h] -t {celsius,fahrenheit} valor

Convierte temperaturas entre Celsius y Fahrenheit.

positional arguments:
  valor                 Temperatura a convertir

options:
  -h, --help            show this help message and exit
  -t {celsius,fahrenheit}, --to {celsius,fahrenheit}
                        Unidad de destino
```

Lo que necesitás implementar:
- Un argumento posicional `valor` que acepte números decimales
- Una opción `--to` (o `-t`) que solo acepte "celsius" o "fahrenheit"
- La opción `--to` debe ser obligatoria (sin ella no tiene sentido el programa)

Fijate que argparse genera automáticamente el `--help` con toda la documentación. Nunca más vas a tener que escribir mensajes de uso a mano.

### Ejercicio 2.2: Listador de archivos mejorado

El comando `ls` es probablemente el que más usás en la terminal. Vas a crear una versión simplificada en Python.

Creá `listar.py`:

```bash
$ python listar.py
archivo1.txt
carpeta/
script.py

$ python listar.py /tmp
temp_file.txt
cache/

$ python listar.py -a
.bashrc
.config/
archivo1.txt
carpeta/
script.py

$ python listar.py --extension .py
script.py
utils.py

$ python listar.py /home/user -a --extension .txt
.notas.txt
documentos.txt
```

Lo que necesitás:
- Argumento posicional `directorio` (opcional, default: directorio actual)
- Flag `-a` / `--all` para incluir archivos ocultos (los que empiezan con punto)
- Opción `--extension` para filtrar por extensión
- Los directorios deberían mostrarse con `/` al final para distinguirlos

Este ejercicio combina argparse con operaciones de filesystem. Vas a necesitar `os.listdir()` o `pathlib.Path.iterdir()`.

### Ejercicio 2.3: Generador de contraseñas

Una herramienta genuinamente útil que vas a poder usar en tu vida diaria.

Creá `genpass.py`:

```bash
$ python genpass.py
X7k#mP9$Lq2@

$ python genpass.py -n 20
aB3#kL9$mN2@pQ5&rT8!Yz

$ python genpass.py --no-symbols -n 8
aB3kL9mN

$ python genpass.py --no-numbers --no-symbols
aBcDeFgHiJkL

$ python genpass.py --count 3
X7k#mP9$Lq2@
aB3#kL9$mN2x
pQ5&rT8!YzWv

$ python genpass.py --count 5 -n 6
k#9$Lq
3#L9$m
&rT8!Y
2@pQ5&
N2xpQ5
```

Lo que necesitás:
- `-n` / `--length`: longitud de la contraseña (default: 12)
- `--no-symbols`: excluir símbolos especiales (!@#$%&)
- `--no-numbers`: excluir números
- `--count`: cuántas contraseñas generar (default: 1)

Para la generación, vas a usar el módulo `random` (o mejor, `secrets` que es criptográficamente seguro). Definí constantes con los caracteres permitidos y armá el pool según las opciones.

---

## Parte 3: Herramientas de verdad

Estos ejercicios son más desafiantes y producen herramientas que realmente podrías usar en tu trabajo diario. Cada uno te va a llevar un rato, pero el resultado vale la pena.

### Ejercicio 3.1: Mini-grep (OBLIGATORIO)

Este es el ejercicio central de esta sección. `grep` es una de las herramientas más usadas en Unix, y vas a crear tu propia versión simplificada.

Creá `buscar.py`:

```bash
$ python buscar.py "error" log.txt
log.txt:15: Error de conexión
log.txt:42: Error de timeout

$ python buscar.py "ERROR" log.txt -i
log.txt:15: Error de conexión
log.txt:42: error de timeout
log.txt:57: ERROR crítico

$ python buscar.py "TODO" *.py
main.py:23: # TODO: refactorizar
utils.py:8: # TODO: agregar tests
utils.py:45: # TODO: manejar excepciones

$ python buscar.py "error" *.log --count
access.log: 5 coincidencias
error.log: 23 coincidencias
Total: 28 coincidencias

$ python buscar.py "DEBUG" log.txt -v
[muestra todas las líneas que NO contienen DEBUG]

$ cat log.txt | python buscar.py "error"
Error de conexión
Error de timeout
```

Lo que necesitás implementar:
- Argumento posicional: el patrón a buscar
- Argumento posicional: archivos (puede ser múltiple con `nargs="*"`)
- `-i` / `--ignore-case`: búsqueda insensible a mayúsculas
- `-n` / `--line-number`: mostrar número de línea (activado por default si hay múltiples archivos)
- `-c` / `--count`: solo mostrar conteo de coincidencias
- `-v` / `--invert`: mostrar líneas que NO coinciden

El detalle importante: si no se especifican archivos, el programa debería leer de stdin. Esto permite usarlo con pipes, que es como se usan las herramientas Unix en la vida real.

Para detectar si hay datos en stdin, podés usar:
```python
import sys
if not sys.stdin.isatty():
    # Hay datos en stdin
```

### Ejercicio 3.2: Procesador de JSON

JSON es el formato de datos más común en desarrollo moderno. Tener una herramienta para inspeccionarlo y manipularlo desde la terminal es muy útil.

Creá `jsonproc.py`:

```bash
$ python jsonproc.py datos.json --keys
usuario
productos
total

$ python jsonproc.py datos.json --get "usuario.nombre"
"Juan Pérez"

$ python jsonproc.py datos.json --get "productos.0.precio"
150.00

$ python jsonproc.py datos.json --pretty
{
    "usuario": {
        "nombre": "Juan Pérez",
        "email": "juan@example.com"
    },
    "productos": [
        {"nombre": "Widget", "precio": 150.00}
    ],
    "total": 150.00
}

$ python jsonproc.py datos.json --set "usuario.activo" "true" -o nuevo.json
Guardado en nuevo.json

$ echo '{"a": 1}' | python jsonproc.py - --get "a"
1
```

Lo que necesitás:
- Argumento posicional: archivo JSON (o `-` para stdin)
- `--keys`: listar claves del primer nivel
- `--get KEY`: obtener valor usando notación con puntos (`usuario.nombre`)
- `--pretty`: formatear con indentación
- `--set KEY VALUE`: modificar un valor
- `-o` / `--output`: archivo de salida (default: stdout)

El acceso con notación de puntos es el desafío principal. Tenés que parsear `"usuario.nombre"` y navegar el diccionario. Los números en el path (como `productos.0`) acceden a índices de arrays.

### Ejercicio 3.3: Gestor de tareas con subcomandos

Este ejercicio te introduce a los subcomandos, el patrón que usan herramientas como `git` (git commit, git push, git status).

Creá `tareas.py`:

```bash
$ python tareas.py add "Completar ejercicio de argparse"
Tarea #1 agregada

$ python tareas.py add "Estudiar para el quiz" --priority alta
Tarea #2 agregada (prioridad: alta)

$ python tareas.py list
#1 [ ] Completar ejercicio de argparse
#2 [ ] Estudiar para el quiz [ALTA]

$ python tareas.py list --pending
#1 [ ] Completar ejercicio de argparse
#2 [ ] Estudiar para el quiz [ALTA]

$ python tareas.py done 1
Tarea #1 completada

$ python tareas.py list
#1 [x] Completar ejercicio de argparse
#2 [ ] Estudiar para el quiz [ALTA]

$ python tareas.py list --done
#1 [x] Completar ejercicio de argparse

$ python tareas.py remove 2
¿Eliminar "Estudiar para el quiz"? [s/N] s
Tarea #2 eliminada
```

Lo que necesitás:
- Subcomando `add`:
  - Argumento posicional: descripción de la tarea
  - `--priority`: prioridad (choices: baja, media, alta)
- Subcomando `list`:
  - `--pending`: mostrar solo pendientes
  - `--done`: mostrar solo completadas
  - `--priority NIVEL`: filtrar por prioridad
- Subcomando `done`:
  - Argumento posicional: ID de la tarea
- Subcomando `remove`:
  - Argumento posicional: ID de la tarea
  - Pedir confirmación antes de eliminar

Para los subcomandos, usás `parser.add_subparsers()`. Cada subcomando puede tener su propio conjunto de argumentos.

Las tareas deben persistir entre ejecuciones. Guardá en un archivo JSON en `~/.tareas.json`. Usá `pathlib.Path.home()` para obtener el directorio home del usuario.

---

## Parte 4: Para los que quieren más

Estos ejercicios son opcionales y exploran temas más avanzados. Son un buen desafío si terminaste los anteriores y querés profundizar.

### Ejercicio 4.1: Click en lugar de argparse

Reescribí el gestor de tareas (ejercicio 3.3) usando la biblioteca [Click](https://click.palletsprojects.com/) en lugar de argparse.

Click usa decoradores para definir comandos, lo que resulta en código más limpio para CLIs complejas:

```python
import click

@click.group()
def cli():
    pass

@cli.command()
@click.argument('descripcion')
@click.option('--priority', type=click.Choice(['baja', 'media', 'alta']))
def add(descripcion, priority):
    # ...
```

Después de implementarlo, compará:
- ¿Cuál tiene menos líneas de código?
- ¿Cuál es más fácil de leer?
- ¿Cuál ofrece mejor experiencia de usuario?

### Ejercicio 4.2: Autocompletado de bash

Agregá soporte de autocompletado al gestor de tareas:

```bash
$ python tareas.py [TAB][TAB]
add    done   list   remove

$ python tareas.py list --[TAB][TAB]
--done     --pending  --priority
```

Investigá la biblioteca `argcomplete` o el soporte nativo de Click para autocompletado.

### Ejercicio 4.3: Configuración en capas

Extendé el procesador JSON (ejercicio 3.2) para que lea configuración de múltiples fuentes:

1. Archivo de configuración: `~/.jsonprocrc` (formato JSON o YAML)
2. Variables de entorno: `JSONPROC_INDENT`, `JSONPROC_SORT_KEYS`
3. Argumentos de línea de comandos

Con precedencia: CLI > ENV > archivo de config

Esto es un patrón muy común en herramientas profesionales. Por ejemplo, así funciona Docker, npm, y muchas otras.

---

## Checklist de entrega

Para el Bloque 0, debés entregar al menos:

| Ejercicio | Archivo | Estado |
|-----------|---------|--------|
| 1.1 | `saludo.py` | Recomendado |
| 1.2 | `suma.py` | Recomendado |
| 2.1 | `temperatura.py` | Recomendado |
| 2.2 | `listar.py` | Recomendado |
| 2.3 | `genpass.py` | Recomendado |
| **3.1** | **`buscar.py`** | **OBLIGATORIO** |
| 3.3 | `tareas.py` | Recomendado |

**Todos los scripts deben:**
- Tener `--help` funcional y descriptivo
- Manejar errores de forma amigable (no excepciones crudas de Python)
- Usar códigos de salida apropiados (`sys.exit(0)` para éxito, `sys.exit(1)` para error)
- Funcionar correctamente con los ejemplos mostrados

**Ubicación en tu repositorio:**
```
computacion2-2026/
└── bloque_0/
    └── argparse/
        ├── saludo.py
        ├── suma.py
        ├── temperatura.py
        ├── listar.py
        ├── genpass.py
        ├── buscar.py      ← OBLIGATORIO
        └── tareas.py
```

---

*Computación II - 2026 - Bloque 0 Autónomo*
