# CLI: argparse, getopt y sys.argv - Autoevaluación

Responde estas preguntas para verificar tu comprensión. Las respuestas están al final.

---

## Parte 1: Conceptos (10 preguntas)

### Pregunta 1
¿Qué contiene `sys.argv[0]`?

a) El primer argumento pasado
b) El nombre del script
c) El nombre del intérprete Python
d) El directorio actual

### Pregunta 2
¿Cuál es la ventaja principal de argparse sobre sys.argv?

a) Es más rápido
b) Usa menos memoria
c) Genera ayuda automática y valida argumentos
d) Funciona en Windows

### Pregunta 3
En argparse, ¿qué es un argumento "posicional"?

a) Un argumento que empieza con `-`
b) Un argumento obligatorio identificado por su posición
c) Un argumento que solo acepta enteros
d) Un argumento con valor por defecto

### Pregunta 4
¿Qué hace `action="store_true"` en argparse?

a) Almacena el valor como string
b) Crea un flag que es True cuando está presente
c) Verifica que el valor sea verdadero
d) Almacena múltiples valores

### Pregunta 5
¿Cómo se especifica que un argumento es obligatorio en argparse?

a) Con `mandatory=True`
b) Con `required=True`
c) Haciéndolo posicional (sin `-`)
d) B y C son correctas

### Pregunta 6
¿Qué hace `nargs="*"` en argparse?

a) Acepta exactamente un argumento
b) El argumento es opcional
c) Acepta cero o más argumentos
d) Acepta al menos un argumento

### Pregunta 7
¿Para qué sirve `type=int` en add_argument?

a) Para mostrar el tipo en la ayuda
b) Para convertir automáticamente el valor a entero
c) Para validar que el argumento existe
d) Para usar formato hexadecimal

### Pregunta 8
¿Qué es un grupo mutuamente exclusivo en argparse?

a) Argumentos que deben usarse juntos
b) Argumentos donde solo uno puede estar presente
c) Argumentos que se excluyen de la ayuda
d) Argumentos solo para uso interno

### Pregunta 9
¿Cuál es el código de salida estándar para éxito?

a) -1
b) 0
c) 1
d) True

### Pregunta 10
¿Qué ventaja tiene usar `type=argparse.FileType('r')`?

a) Es más rápido
b) Abre el archivo automáticamente y maneja errores
c) Comprime el archivo
d) Valida el formato del archivo

---

## Parte 2: Código (10 preguntas)

### Pregunta 11
¿Qué imprime este código si se ejecuta como `python script.py hola mundo`?

```python
import sys
print(len(sys.argv))
```

a) 1
b) 2
c) 3
d) Error

### Pregunta 12
¿Cómo agregas una opción `-v`/`--verbose` que sea un flag booleano?

a) `parser.add_argument("-v", "--verbose", type=bool)`
b) `parser.add_argument("-v", "--verbose", action="store_true")`
c) `parser.add_argument("-v", "--verbose", boolean=True)`
d) `parser.add_argument("-v", "--verbose", flag=True)`

### Pregunta 13
¿Cómo limitas los valores permitidos para una opción?

a) `parser.add_argument("--color", values=["red", "blue"])`
b) `parser.add_argument("--color", choices=["red", "blue"])`
c) `parser.add_argument("--color", options=["red", "blue"])`
d) `parser.add_argument("--color", enum=["red", "blue"])`

### Pregunta 14
¿Qué está mal en este código?

```python
parser.add_argument("-n", "--number", type=int, default="10")
```

a) No se puede usar `-n` y `--number` juntos
b) El default debería ser int, no string
c) Falta el parámetro `help`
d) No hay nada mal

### Pregunta 15
¿Cómo creas subcomandos en argparse?

a) `parser.add_subcommand("nombre")`
b) `parser.add_subparsers()` y luego `add_parser()`
c) `parser.create_subparser("nombre")`
d) `argparse.Subcommand("nombre")`

### Pregunta 16
¿Cuál es el resultado de este código?

```python
parser = argparse.ArgumentParser()
parser.add_argument("files", nargs="+")
args = parser.parse_args(["a.txt"])
print(type(args.files))
```

a) `<class 'str'>`
b) `<class 'list'>`
c) `<class 'tuple'>`
d) Error

### Pregunta 17
¿Cómo haces que stdin sea el default para un argumento de archivo?

```python
parser.add_argument("input", ???)
```

a) `default=sys.stdin`
b) `nargs="?", type=argparse.FileType('r'), default=sys.stdin`
c) `stdin=True`
d) `file="-"`

### Pregunta 18
En getopt, ¿qué significa `"o:"` en las opciones cortas?

a) La opción `-o` es obligatoria
b) La opción `-o` requiere un argumento
c) La opción `-o` es para output
d) La opción `-o` acepta múltiples valores

### Pregunta 19
¿Cómo accedes al valor de `--output` después de parsear?

```python
parser.add_argument("--output", default="out.txt")
args = parser.parse_args()
# ???
```

a) `args["output"]`
b) `args.output`
c) `args.get("output")`
d) `args["--output"]`

### Pregunta 20
¿Qué hace `action="count"`?

```python
parser.add_argument("-v", action="count", default=0)
```

a) Cuenta los argumentos totales
b) Cuenta cuántas veces aparece `-v`
c) Valida que sea un número
d) Cuenta líneas del archivo

---

## Parte 3: Situaciones prácticas

### Situación 1
Quieres que tu script acepte `script.py archivo.txt` o `cat archivo | script.py`.
¿Cómo configuras el argumento de entrada?

### Situación 2
Tu script tiene las opciones `--debug` y `--quiet` que no deberían usarse juntas.
¿Cómo lo implementas?

### Situación 3
Necesitas que `-v` aumente la verbosidad: `-v` = INFO, `-vv` = DEBUG.
¿Qué action usas?

### Situación 4
Quieres una opción `--tag` que pueda repetirse: `--tag foo --tag bar`.
¿Cómo la configuras?

### Situación 5
Tu herramienta tiene comandos `init`, `build`, `test` como git.
¿Qué feature de argparse usas?

---

## Respuestas

<details>
<summary>Click para ver respuestas</summary>

### Parte 1: Conceptos
1. **b** - El nombre del script
2. **c** - Genera ayuda automática y valida argumentos
3. **b** - Argumento obligatorio identificado por su posición
4. **b** - Crea un flag que es True cuando está presente
5. **d** - B y C (posicional es obligatorio, opcional con required=True)
6. **c** - Acepta cero o más argumentos
7. **b** - Convierte automáticamente el valor a entero
8. **b** - Argumentos donde solo uno puede estar presente
9. **b** - 0
10. **b** - Abre el archivo automáticamente y maneja errores

### Parte 2: Código
11. **c** - 3 (script.py, hola, mundo)
12. **b** - `action="store_true"`
13. **b** - `choices=["red", "blue"]`
14. **b** - El default debería ser int (10, no "10")
15. **b** - `add_subparsers()` y `add_parser()`
16. **b** - `<class 'list'>` (nargs="+" siempre devuelve lista)
17. **b** - `nargs="?", type=argparse.FileType('r'), default=sys.stdin`
18. **b** - La opción `-o` requiere un argumento
19. **b** - `args.output`
20. **b** - Cuenta cuántas veces aparece `-v`

### Parte 3: Situaciones prácticas

1. ```python
   parser.add_argument("entrada", nargs="?",
                       type=argparse.FileType('r'),
                       default=sys.stdin)
   ```

2. ```python
   group = parser.add_mutually_exclusive_group()
   group.add_argument("--debug", action="store_true")
   group.add_argument("--quiet", action="store_true")
   ```

3. ```python
   parser.add_argument("-v", action="count", default=0)
   # -v → 1, -vv → 2, etc.
   ```

4. ```python
   parser.add_argument("--tag", action="append")
   # Resultado: args.tag = ["foo", "bar"]
   ```

5. **Subparsers:**
   ```python
   subparsers = parser.add_subparsers(dest="comando")
   subparsers.add_parser("init")
   subparsers.add_parser("build")
   subparsers.add_parser("test")
   ```

### Puntuación
- 20-25: Excelente comprensión
- 15-19: Buen nivel, repasar algunos conceptos
- 10-14: Necesita más práctica
- <10: Revisar el material nuevamente

</details>

---

*Computación II - 2026 - Bloque 0 Autónomo*
