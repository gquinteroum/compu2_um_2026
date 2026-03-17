# Argumentos de línea de comandos

## ¿Por qué necesitás saber esto?

Cada vez que ejecutás un comando en la terminal, estás usando argumentos de línea de comandos:

```bash
ls -la /home
git commit -m "mensaje"
python script.py archivo.txt --verbose
```

Esos `-la`, `-m "mensaje"`, `archivo.txt`, `--verbose` son argumentos que le dicen al programa qué hacer y cómo hacerlo.

En esta materia vas a escribir muchos scripts que necesitan recibir información del usuario: qué archivo procesar, cuántos procesos crear, a qué puerto conectarse. Podrías hardcodear todo en el código, pero eso es inflexible y poco profesional. Los programas de verdad se configuran por línea de comandos.

Además, las herramientas que vas a usar en los trabajos prácticos (y en tu vida profesional) se controlan así. Entender cómo funcionan los argumentos te hace más efectivo usando cualquier herramienta de terminal.

---

## Anatomía de un comando

Miremos este comando:

```bash
python mi_script.py datos.csv --output resultado.json -v --format json
```

Cada parte tiene un nombre:

- `python mi_script.py` → el programa que se ejecuta
- `datos.csv` → **argumento posicional** (identificado por su posición, no por un nombre)
- `--output resultado.json` → **opción con valor** (forma larga con `--`)
- `-v` → **flag** (opción booleana, forma corta con `-`)
- `--format json` → otra opción con valor

La convención es bastante universal:
- Una guión (`-`) para opciones cortas de una letra: `-v`, `-n 10`
- Dos guiones (`--`) para opciones largas: `--verbose`, `--number=10`
- Sin guiones para argumentos posicionales

---

## El enfoque naive: sys.argv

Python te da acceso directo a los argumentos a través de `sys.argv`:

```python
import sys

print(sys.argv)
```

Si ejecutás `python script.py hola mundo`, `sys.argv` va a ser:
```python
['script.py', 'hola', 'mundo']
```

Es una lista simple. `sys.argv[0]` es siempre el nombre del script, y el resto son los argumentos que pasaste.

Podés hacer cosas con esto:

```python
import sys

if len(sys.argv) < 2:
    print("Uso: python script.py <nombre>")
    sys.exit(1)

nombre = sys.argv[1]
print(f"Hola, {nombre}")
```

Funciona, pero tiene problemas graves:

1. **No hay validación automática.** Si el usuario pasa algo incorrecto, tu código explota.
2. **No hay ayuda.** El usuario no tiene forma de saber qué argumentos acepta tu script.
3. **Manejar opciones es tedioso.** ¿Cómo distinguís entre `script.py archivo.txt -v` y `script.py -v archivo.txt`?
4. **Mucho código repetitivo.** Cada script tiene que reimplementar la lógica de parsing.

Para scripts de 5 líneas que solo vos usás, `sys.argv` está bien. Para cualquier otra cosa, necesitás algo mejor.

---

## argparse: la forma correcta

`argparse` es el módulo de la biblioteca estándar de Python para manejar argumentos. Hace todo el trabajo pesado por vos:

```python
import argparse

parser = argparse.ArgumentParser(description="Procesa un archivo de texto")
parser.add_argument("archivo", help="Archivo a procesar")
parser.add_argument("-v", "--verbose", action="store_true", help="Modo detallado")
parser.add_argument("-n", "--lineas", type=int, default=10, help="Número de líneas")

args = parser.parse_args()

print(f"Procesando {args.archivo}")
print(f"Verbose: {args.verbose}")
print(f"Líneas: {args.lineas}")
```

Ejecutá esto con `--help`:

```
$ python script.py --help
usage: script.py [-h] [-v] [-n LINEAS] archivo

Procesa un archivo de texto

positional arguments:
  archivo               Archivo a procesar

options:
  -h, --help            show this help message and exit
  -v, --verbose         Modo detallado
  -n LINEAS, --lineas LINEAS
                        Número de líneas
```

Eso es automático. No escribiste ese texto, argparse lo generó. Y además:

- Si pasás un tipo incorrecto (texto donde esperaba número), te avisa
- Si falta un argumento requerido, te avisa
- Si pasás una opción que no existe, te avisa

Todo con mensajes claros para el usuario.

---

## Tipos de argumentos

### Posicionales

Son argumentos identificados por su posición, no por un nombre. Son obligatorios por defecto.

```python
parser.add_argument("entrada", help="Archivo de entrada")
parser.add_argument("salida", help="Archivo de salida")
```

Uso: `python script.py datos.txt resultado.txt`

El orden importa. `datos.txt` va a `args.entrada`, `resultado.txt` va a `args.salida`.

### Opcionales con valor

Empiezan con `-` o `--` y reciben un valor.

```python
parser.add_argument("-o", "--output", default="salida.txt", help="Archivo de salida")
parser.add_argument("-n", "--numero", type=int, default=10, help="Cantidad")
```

Uso: `python script.py -o resultado.txt -n 20`

Fijate que especificamos `type=int`. Por defecto todo es string, pero argparse puede convertir automáticamente.

### Flags (booleanos)

Opciones que no reciben valor, solo indican presencia o ausencia.

```python
parser.add_argument("-v", "--verbose", action="store_true", help="Modo detallado")
```

`action="store_true"` significa: si está presente, `args.verbose` es `True`. Si no está, es `False`.

### Limitar valores permitidos

Cuando solo ciertos valores tienen sentido:

```python
parser.add_argument("--formato", choices=["json", "csv", "xml"], default="json")
```

Si el usuario pone `--formato pdf`, argparse rechaza con un mensaje claro.

---

## Patrones comunes

### Stdin/stdout como default

En UNIX, los programas bien diseñados pueden encadenarse con pipes:

```bash
cat archivo.txt | python filtrar.py | python contar.py > resultado.txt
```

Para que tu script funcione así, usás stdin/stdout como defaults:

```python
import sys

parser.add_argument(
    "entrada",
    nargs="?",  # opcional
    type=argparse.FileType('r'),
    default=sys.stdin,
    help="Archivo de entrada (default: stdin)"
)

parser.add_argument(
    "-o", "--output",
    type=argparse.FileType('w'),
    default=sys.stdout,
    help="Archivo de salida (default: stdout)"
)
```

`nargs="?"` hace que el argumento posicional sea opcional. Si no lo pasan, usa el default (stdin).

`argparse.FileType('r')` abre el archivo automáticamente para lectura.

### Verbosidad incremental

Algunos programas permiten `-v` para verbose, `-vv` para más verbose, `-vvv` para debug total:

```python
parser.add_argument(
    "-v", "--verbose",
    action="count",
    default=0,
    help="Aumentar verbosidad (-v, -vv, -vvv)"
)

# Después:
if args.verbose >= 2:
    print("Debug: detalle fino")
elif args.verbose >= 1:
    print("Info: operación normal")
```

### Argumentos mutuamente exclusivos

A veces dos opciones no tienen sentido juntas:

```python
group = parser.add_mutually_exclusive_group()
group.add_argument("-v", "--verbose", action="store_true")
group.add_argument("-q", "--quiet", action="store_true")
```

Si el usuario pone `-v -q`, argparse le dice que no puede.

### Subcomandos

Programas como `git` tienen subcomandos: `git commit`, `git push`, `git status`. Cada uno tiene sus propios argumentos.

```python
parser = argparse.ArgumentParser(prog="mi-herramienta")
subparsers = parser.add_subparsers(dest="comando")

# Subcomando: init
parser_init = subparsers.add_parser("init", help="Inicializar proyecto")
parser_init.add_argument("nombre", help="Nombre del proyecto")

# Subcomando: build
parser_build = subparsers.add_parser("build", help="Compilar")
parser_build.add_argument("--release", action="store_true")

args = parser.parse_args()

if args.comando == "init":
    print(f"Inicializando {args.nombre}")
elif args.comando == "build":
    print(f"Compilando en modo {'release' if args.release else 'debug'}")
```

Uso:
```bash
mi-herramienta init mi-proyecto
mi-herramienta build --release
```

---

## Estructura recomendada para scripts

Después de ver varios patrones, así es como debería verse un script bien organizado:

```python
#!/usr/bin/env python3
"""
Descripción breve de qué hace el script.
"""
import argparse
import sys


def crear_parser():
    """Configura el parser de argumentos."""
    parser = argparse.ArgumentParser(
        description=__doc__,
        formatter_class=argparse.RawDescriptionHelpFormatter
    )

    parser.add_argument("archivo", help="Archivo a procesar")
    parser.add_argument("-v", "--verbose", action="store_true")
    parser.add_argument("-o", "--output", default="salida.txt")

    return parser


def procesar(args):
    """Lógica principal del programa."""
    if args.verbose:
        print(f"Procesando {args.archivo}...")

    # Tu código acá

    return True  # éxito


def main():
    parser = crear_parser()
    args = parser.parse_args()

    try:
        exito = procesar(args)
        sys.exit(0 if exito else 1)
    except KeyboardInterrupt:
        print("\nInterrumpido", file=sys.stderr)
        sys.exit(130)
    except Exception as e:
        print(f"Error: {e}", file=sys.stderr)
        sys.exit(1)


if __name__ == "__main__":
    main()
```

Notas sobre esta estructura:

1. **El shebang** (`#!/usr/bin/env python3`) permite ejecutar el script directamente sin escribir `python`.

2. **Docstring del módulo** se usa como descripción en `--help`.

3. **Función separada para el parser** mantiene `main()` limpio.

4. **Lógica en su propia función** facilita testing.

5. **Manejo de excepciones** con códigos de salida apropiados. `0` = éxito, `1` = error, `130` = interrumpido por Ctrl+C.

6. **Guard `if __name__ == "__main__"`** permite importar el módulo sin ejecutarlo.

---

## Una nota sobre getopt

Existe otro módulo llamado `getopt` que hace algo similar a argparse pero de forma más manual. Es compatible con el estilo de parsing de C y shells antiguos.

No lo vas a necesitar en esta materia. Si alguna vez lo encontrás en código legacy, ahora sabés que existe. Para código nuevo, usá `argparse`.

---

## Más allá de argparse

Para proyectos más grandes, hay bibliotecas de terceros:

- **Click**: muy popular, usa decoradores en lugar de objetos
- **Typer**: usa type hints de Python para definir argumentos

Son excelentes, pero para esta materia `argparse` es más que suficiente. Además, es parte de la biblioteca estándar, así que no necesitás instalar nada.

---

## Resumen

| Concepto | Ejemplo |
|----------|---------|
| Argumento posicional | `archivo.txt` |
| Opción corta | `-v`, `-n 10` |
| Opción larga | `--verbose`, `--number=10` |
| Flag (booleano) | `action="store_true"` |
| Valor por defecto | `default="valor"` |
| Tipo específico | `type=int` |
| Valores permitidos | `choices=["a", "b", "c"]` |

Lo esencial de argparse:

```python
import argparse

parser = argparse.ArgumentParser(description="Mi programa")
parser.add_argument("archivo")  # posicional
parser.add_argument("-v", "--verbose", action="store_true")  # flag
parser.add_argument("-n", type=int, default=10)  # opción con valor

args = parser.parse_args()
# Usá args.archivo, args.verbose, args.n
```

---

*Computación II - 2026 - Bloque 0 Autónomo*
