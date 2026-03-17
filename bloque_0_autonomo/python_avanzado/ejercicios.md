# Python Avanzado - Ejercicios

## Cómo abordar estos ejercicios

Los conceptos de Python avanzado que viste en el contenido teórico pueden parecer abstractos. La única forma de que se vuelvan naturales es usándolos. Estos ejercicios te van a forzar a pensar en contextos, decoradores y generadores hasta que se conviertan en herramientas que usás sin pensar.

Cada ejercicio está diseñado para hacerte sentir la necesidad de la herramienta antes de implementarla. No te limites a copiar patrones: pensá por qué funcionan y qué problema resuelven.

**Tip importante:** Si un ejercicio te traba, releé la sección correspondiente del contenido teórico. Los ejemplos ahí fueron elegidos específicamente para prepararte para estos ejercicios.

---

## Parte 1: Primeros pasos

Estos ejercicios son relativamente simples y te ayudan a familiarizarte con la sintaxis y los patrones básicos.

### Ejercicio 1.1: Tu primer context manager

Creá un context manager llamado `archivo_temporal` que:
1. Cree un archivo temporal con un nombre dado
2. Permita escribir en él dentro del bloque `with`
3. Borre el archivo automáticamente al salir del contexto

```python
with archivo_temporal("test.txt") as f:
    f.write("Datos de prueba\n")
    f.write("Más datos\n")
    # Podemos leer lo que escribimos
    f.seek(0)
    print(f.read())
# Acá el archivo ya no existe

# Verificar que realmente se borró
import os
assert not os.path.exists("test.txt")
```

Esto es muy útil para tests: podés crear archivos temporales sin preocuparte por limpiar después.

**Para pensar:** ¿Qué pasa si hay una excepción dentro del bloque `with`? Tu implementación debe borrar el archivo de todas formas.

### Ejercicio 1.2: Decorador de logging

Creá un decorador `@log_llamada` que imprima información cada vez que se llama una función:

```python
@log_llamada
def sumar(a, b):
    return a + b

@log_llamada
def saludar(nombre, entusiasta=False):
    sufijo = "!" if entusiasta else "."
    return f"Hola, {nombre}{sufijo}"

resultado = sumar(3, 5)
# [2026-01-15 10:30:00] Llamando a sumar(3, 5)
# [2026-01-15 10:30:00] sumar retornó 8

saludar("Ana", entusiasta=True)
# [2026-01-15 10:30:01] Llamando a saludar('Ana', entusiasta=True)
# [2026-01-15 10:30:01] saludar retornó 'Hola, Ana!'
```

El decorador debe:
- Mostrar el nombre de la función
- Mostrar los argumentos (posicionales y keyword)
- Mostrar el valor de retorno
- Incluir timestamp
- Preservar el nombre y docstring de la función original (usar `@wraps`)

### Ejercicio 1.3: Generador de Fibonacci

Este es el "hola mundo" de los generadores. Creá un generador `fibonacci()` que produzca la secuencia infinita de Fibonacci:

```python
fib = fibonacci()

# Obtener los primeros 10
for _ in range(10):
    print(next(fib))
# 0, 1, 1, 2, 3, 5, 8, 13, 21, 34

# Podemos seguir obteniendo más
print(next(fib))  # 55
print(next(fib))  # 89
```

También implementá una versión que acepte un límite:

```python
for n in fibonacci(limite=100):
    print(n)
# 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89
```

**Para pensar:** ¿Cuál es la diferencia entre retornar una lista y usar un generador? ¿Qué pasa si pedís los primeros 10000 números de Fibonacci?

---

## Parte 2: Aplicaciones prácticas

Estos ejercicios usan los conceptos en situaciones más realistas.

### Ejercicio 2.1: Timer con context manager (OBLIGATORIO)

Creá un context manager `Timer` que mida cuánto tarda el código dentro del bloque:

```python
with Timer("Procesamiento de datos"):
    datos = [x**2 for x in range(1000000)]
# [Timer] Procesamiento de datos: 0.123s

# También debe funcionar sin nombre
with Timer() as t:
    time.sleep(0.5)
print(f"El bloque tardó {t.elapsed:.3f} segundos")
# El bloque tardó 0.502 segundos

# Y permitir acceso al tiempo antes de salir
with Timer() as t:
    paso1()
    print(f"Después del paso 1: {t.elapsed:.3f}s")
    paso2()
    print(f"Después del paso 2: {t.elapsed:.3f}s")
```

Requisitos:
- Nombre opcional
- Acceso a `elapsed` durante y después del bloque
- Imprimir automáticamente al salir si se dio un nombre

Implementalo de dos formas:
1. Como clase con `__enter__` y `__exit__`
2. Usando `@contextmanager` de contextlib

### Ejercicio 2.2: Decorador de reintentos (OBLIGATORIO)

Creá un decorador `@retry` que reintente una función si falla:

```python
@retry(max_attempts=3, delay=1)
def conectar_servidor():
    if random.random() < 0.7:
        raise ConnectionError("Servidor no disponible")
    return "Conectado exitosamente"

try:
    resultado = conectar_servidor()
except ConnectionError:
    print("Falló después de 3 intentos")

# Output posible:
# Intento 1/3 falló: Servidor no disponible. Esperando 1s...
# Intento 2/3 falló: Servidor no disponible. Esperando 1s...
# Intento 3/3 falló: Servidor no disponible.
# [Se lanza la excepción]
```

Requisitos:
- `max_attempts`: número máximo de intentos (default: 3)
- `delay`: segundos entre intentos (default: 1)
- `exceptions`: tupla de excepciones a capturar (default: Exception)
- Debe mostrar información sobre los reintentos
- Debe lanzar la última excepción si todos los intentos fallan

Esto es extremadamente útil para operaciones de red, APIs, etc.

### Ejercicio 2.3: Generador de chunks

Procesando datos grandes, a menudo necesitás dividir en lotes. Creá un generador `chunked`:

```python
# Dividir en grupos de 3
list(chunked(range(10), 3))
# [[0, 1, 2], [3, 4, 5], [6, 7, 8], [9]]

# Funciona con cualquier iterable
list(chunked("abcdefgh", 3))
# [['a', 'b', 'c'], ['d', 'e', 'f'], ['g', 'h']]

# Caso práctico: procesar archivo grande en batches
def procesar_archivo_grande(path, batch_size=1000):
    with open(path) as f:
        for batch in chunked(f, batch_size):
            procesar_batch(batch)
```

El generador debe:
- Funcionar con cualquier iterable (listas, generadores, archivos)
- El último chunk puede tener menos elementos si no alcanza
- No cargar todo en memoria (debe ser lazy)

### Ejercicio 2.4: Pipeline de funciones

Creá una función `pipeline` que componga múltiples funciones:

```python
def doble(x): return x * 2
def sumar_uno(x): return x + 1
def cuadrado(x): return x ** 2

# Crear pipeline: primero doble, luego sumar_uno, finalmente cuadrado
p = pipeline(doble, sumar_uno, cuadrado)

print(p(3))  # ((3 * 2) + 1) ** 2 = 49
print(p(5))  # ((5 * 2) + 1) ** 2 = 121

# También debería funcionar con una sola función
p2 = pipeline(doble)
print(p2(10))  # 20

# Y con muchas
p3 = pipeline(str, len, doble)  # Convertir a string, contar chars, duplicar
print(p3(12345))  # 10
```

**Bonus:** Hacé que también funcione como decorador:

```python
@pipeline(str.strip, str.lower, str.split)
def procesar_entrada(texto):
    return texto

procesar_entrada("  HELLO WORLD  ")  # ['hello', 'world']
```

---

## Parte 3: Problemas avanzados

Estos ejercicios requieren combinar múltiples conceptos y pensar más profundamente.

### Ejercicio 3.1: Memoización manual

Implementá un decorador `@memoize` sin usar `lru_cache`:

```python
@memoize
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# Primera llamada: calcula todo
print(fibonacci(100))  # Casi instantáneo por el cache

# Acceso al cache
print(fibonacci.cache)
# {(0,): 0, (1,): 1, (2,): 1, (3,): 2, ...}

# Estadísticas
print(fibonacci.cache_info())
# CacheInfo(hits=196, misses=101, size=101)

# Limpiar cache
fibonacci.clear_cache()
```

Requisitos:
- Cachear resultados por argumentos (tupla de args)
- Método `.cache` para ver el diccionario de cache
- Método `.cache_info()` con estadísticas
- Método `.clear_cache()` para limpiar

El desafío está en que el decorador tiene que agregar atributos a la función resultante.

### Ejercicio 3.2: Transacciones con context manager

Creá un context manager `Transaction` que permita revertir cambios si algo falla:

```python
class Cuenta:
    def __init__(self, saldo):
        self.saldo = saldo
        self.nombre = "Sin nombre"

cuenta = Cuenta(1000)

# Transacción exitosa
with Transaction(cuenta):
    cuenta.saldo -= 100
    cuenta.saldo -= 200

print(cuenta.saldo)  # 700

# Transacción que falla
try:
    with Transaction(cuenta):
        cuenta.saldo -= 100
        cuenta.nombre = "Test"
        raise ValueError("Error simulado")
except ValueError:
    pass

print(cuenta.saldo)  # 700 (se revirtió)
print(cuenta.nombre)  # "Sin nombre" (también se revirtió)
```

El context manager debe:
- Guardar el estado inicial de todos los atributos del objeto
- Restaurar si hay cualquier excepción
- No restaurar si el bloque completa exitosamente
- Funcionar con cualquier objeto

**Pista:** Podés usar `vars(objeto).copy()` para obtener una copia de los atributos y `vars(objeto).update()` para restaurarlos.

### Ejercicio 3.3: Lector de archivo lazy con buffer

Creá un generador `BufferedReader` que lea un archivo en chunks pero entregue líneas:

```python
# El archivo tiene 1GB, pero solo usamos unos KB de memoria
for linea in BufferedReader("archivo_enorme.txt", buffer_size=8192):
    if "ERROR" in linea:
        procesar_error(linea)

# También debería funcionar como context manager
with BufferedReader("log.txt") as reader:
    for linea in reader:
        print(linea)
```

Esto es lo que hace internamente Python cuando iterás sobre un archivo, pero implementarlo te ayuda a entender cómo funcionan los generadores con I/O.

El desafío: el archivo se lee en chunks de `buffer_size` bytes, pero tenés que entregar líneas completas. Si un chunk corta una línea a la mitad, tenés que guardar esa parte y juntarla con el próximo chunk.

### Ejercicio 3.4: Validador de tipos en runtime

Creá un decorador `@validate_types` que verifique tipos según las anotaciones:

```python
@validate_types
def procesar(nombre: str, edad: int, activo: bool = True) -> str:
    return f"{nombre} tiene {edad} años"

procesar("Ana", 25)          # OK: "Ana tiene 25 años"
procesar("Ana", 25, False)   # OK
procesar("Ana", "25")        # TypeError: 'edad' debe ser int, recibido str
procesar(123, 25)            # TypeError: 'nombre' debe ser str, recibido int

# También valida el tipo de retorno
@validate_types
def sumar(a: int, b: int) -> int:
    return str(a + b)  # Error! Retorna str pero declara int

sumar(1, 2)  # TypeError: retorno debe ser int, recibido str
```

Usá el módulo `typing` y `inspect.signature()` para obtener información sobre los parámetros. Este es un ejercicio avanzado pero muy instructivo sobre cómo funciona la introspección en Python.

---

## Parte 4: Para los que quieren más

### Ejercicio 4.1: Decorador flexible

Creá un decorador que funcione con y sin paréntesis:

```python
@mi_decorador
def funcion1():
    pass

@mi_decorador()
def funcion2():
    pass

@mi_decorador(verbose=True)
def funcion3():
    pass
```

Este patrón es útil cuando querés un decorador con parámetros opcionales. El truco está en detectar si el primer argumento es una función (uso sin paréntesis) o no.

### Ejercicio 4.2: Scheduler de coroutines

Implementá un scheduler simple basado en generadores:

```python
def tarea_a():
    for i in range(3):
        print(f"Tarea A: paso {i}")
        yield  # Ceder control

def tarea_b():
    for i in range(5):
        print(f"Tarea B: paso {i}")
        yield

scheduler = Scheduler()
scheduler.add(tarea_a())
scheduler.add(tarea_b())
scheduler.run()

# Output:
# Tarea A: paso 0
# Tarea B: paso 0
# Tarea A: paso 1
# Tarea B: paso 1
# Tarea A: paso 2
# Tarea B: paso 2
# Tarea B: paso 3
# Tarea B: paso 4
```

El scheduler ejecuta cada generador un paso a la vez (round-robin). Cuando un generador termina (lanza `StopIteration`), se remueve de la cola.

Este ejercicio te da intuición sobre cómo funciona `asyncio` por debajo.

### Ejercicio 4.3: Singleton con metaclase

Implementá una metaclase `Singleton` que haga que cualquier clase solo pueda tener una instancia:

```python
class Configuracion(metaclass=Singleton):
    def __init__(self, debug=False):
        self.debug = debug

c1 = Configuracion(debug=True)
c2 = Configuracion(debug=False)  # NO crea nueva instancia

print(c1 is c2)  # True
print(c1.debug)  # True (el valor original)
```

Las metaclases son un tema avanzado de Python. Investigá `__call__` en metaclases para entender cómo interceptar la creación de instancias.

### Ejercicio 4.4: Descriptor validador

Creá un descriptor `Positivo` que valide que un atributo siempre sea positivo:

```python
class Cuenta:
    saldo = Positivo()
    limite = Positivo(default=0)

    def __init__(self, saldo, limite=1000):
        self.saldo = saldo
        self.limite = limite

c = Cuenta(100, 500)
print(c.saldo)   # 100
print(c.limite)  # 500

c.saldo = 200    # OK
c.saldo = -50    # ValueError: saldo debe ser positivo
```

Los descriptores son el mecanismo detrás de `@property` y `@classmethod`. Implementar uno te da entendimiento profundo de cómo funciona el acceso a atributos en Python.

---

## Checklist de entrega

Para el Bloque 0:

| Ejercicio | Archivo | Estado |
|-----------|---------|--------|
| 1.1 | `archivo_temporal.py` | Recomendado |
| 1.2 | `log_llamada.py` | Recomendado |
| 1.3 | `fibonacci.py` | Recomendado |
| **2.1** | **`timer.py`** | **OBLIGATORIO** |
| **2.2** | **`retry.py`** | **OBLIGATORIO** |
| 2.3 | `chunked.py` | Recomendado |
| 3.1 o 3.2 | Uno de los avanzados | Recomendado |

**Ubicación en tu repositorio:**
```
computacion2-2026/
└── bloque_0/
    └── python_avanzado/
        ├── archivo_temporal.py
        ├── log_llamada.py
        ├── fibonacci.py
        ├── timer.py           ← OBLIGATORIO
        ├── retry.py           ← OBLIGATORIO
        ├── chunked.py
        └── memoize.py (o transaction.py)
```

**Todos los ejercicios deben:**
- Incluir docstrings explicando qué hace cada función/clase
- Tener al menos un bloque `if __name__ == "__main__":` con ejemplos de uso
- Manejar casos borde (¿qué pasa con una lista vacía? ¿con argumentos inválidos?)
- Usar type hints donde tenga sentido

---

*Computación II - 2026 - Bloque 0 Autónomo*
