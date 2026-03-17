# Repaso Python Avanzado - Autoevaluación

Responde estas preguntas para verificar tu comprensión. Las respuestas están al final.

---

## Parte 1: Context Managers (5 preguntas)

### Pregunta 1
¿Qué métodos debe implementar un objeto para ser un context manager?

a) `__open__` y `__close__`
b) `__enter__` y `__exit__`
c) `__start__` y `__stop__`
d) `__with__` y `__end__`

### Pregunta 2
¿Qué retorna `__enter__` y dónde se usa ese valor?

a) Retorna None siempre
b) Retorna el valor que se asigna después de `as`
c) Retorna True si tuvo éxito
d) Retorna la excepción si hubo error

### Pregunta 3
¿Cuándo se ejecuta el código después de `finally` en un context manager?

a) Solo si no hay excepciones
b) Solo si hay excepciones
c) Siempre, haya o no excepciones
d) Nunca, es opcional

### Pregunta 4
¿Qué decorador de `contextlib` facilita crear context managers con `yield`?

a) `@contextlib.context`
b) `@contextlib.contextmanager`
c) `@contextlib.manager`
d) `@contextlib.with_manager`

### Pregunta 5
Si `__exit__` retorna `True`, ¿qué sucede con una excepción dentro del bloque `with`?

a) Se propaga normalmente
b) Se suprime (no se propaga)
c) Se convierte en warning
d) Causa un error fatal

---

## Parte 2: Decoradores (6 preguntas)

### Pregunta 6
¿Qué es un decorador en Python?

a) Un tipo especial de clase
b) Una función que modifica otra función
c) Un método de inicialización
d) Una estructura de datos

### Pregunta 7
¿Qué es equivalente a `@mi_decorador` encima de una función?

a) `funcion = mi_decorador()`
b) `funcion = mi_decorador(funcion)`
c) `funcion.mi_decorador()`
d) `mi_decorador.funcion()`

### Pregunta 8
¿Para qué sirve `@functools.wraps(funcion)` en un decorador?

a) Para hacer la función más rápida
b) Para preservar el nombre, docstring y otros atributos de la función original
c) Para cachear resultados
d) Para permitir argumentos variables

### Pregunta 9
¿Cómo creas un decorador que acepta argumentos?

a) Con tres funciones anidadas
b) Con una clase
c) Con un generador
d) No es posible

### Pregunta 10
¿Qué hace `@functools.lru_cache`?

a) Limita el número de llamadas
b) Cachea resultados de llamadas con los mismos argumentos
c) Ejecuta la función en un thread
d) Registra todas las llamadas

### Pregunta 11
¿Qué acepta `*args, **kwargs` en un wrapper de decorador?

a) Solo argumentos posicionales
b) Solo argumentos keyword
c) Cualquier combinación de argumentos
d) Ningún argumento

---

## Parte 3: Generadores (5 preguntas)

### Pregunta 12
¿Qué diferencia a un generador de una función normal?

a) Usa `return` en lugar de `yield`
b) Usa `yield` y produce valores bajo demanda
c) No puede recibir argumentos
d) Siempre retorna una lista

### Pregunta 13
¿Qué ventaja tiene un generador sobre una lista?

a) Es más rápido
b) Usa memoria constante (no carga todo en memoria)
c) Puede contener más elementos
d) Es más fácil de debuggear

### Pregunta 14
¿Qué expresión crea un generador?

a) `[x**2 for x in range(10)]`
b) `(x**2 for x in range(10))`
c) `{x**2 for x in range(10)}`
d) `<x**2 for x in range(10)>`

### Pregunta 15
¿Qué hace `yield from otra_secuencia`?

a) Importa una secuencia
b) Delega a otro generador/iterable, produciendo sus valores
c) Crea una copia de la secuencia
d) Retorna la secuencia completa

### Pregunta 16
¿Qué excepción se lanza cuando un generador se agota?

a) `GeneratorError`
b) `StopIteration`
c) `EndOfGenerator`
d) `NoMoreValues`

---

## Parte 4: Closures y funciones de orden superior (4 preguntas)

### Pregunta 17
¿Qué es una closure?

a) Una función que cierra archivos
b) Una función que recuerda variables del scope donde fue definida
c) Un tipo de context manager
d) Un decorador especial

### Pregunta 18
¿Qué palabra clave permite modificar una variable del scope exterior en una closure?

a) `global`
b) `outer`
c) `nonlocal`
d) `external`

### Pregunta 19
¿Qué hace `functools.partial`?

a) Ejecuta parcialmente una función
b) Crea una nueva función con algunos argumentos pre-configurados
c) Divide una función en partes
d) Ejecuta solo parte del código

### Pregunta 20
¿Qué hace `map(lambda x: x*2, [1, 2, 3])`?

a) Retorna `[2, 4, 6]`
b) Retorna un objeto map (iterador) que produce `2, 4, 6`
c) Modifica la lista original
d) Retorna `6`

---

## Parte 5: Excepciones y estructuras de datos (5 preguntas)

### Pregunta 21
¿Cuándo se ejecuta el bloque `else` en un try/except?

a) Cuando hay una excepción
b) Cuando NO hay excepción
c) Siempre
d) Nunca

### Pregunta 22
¿Cómo encadenas una excepción a otra?

a) `raise NuevaExcepcion(original)`
b) `raise NuevaExcepcion from original`
c) `raise NuevaExcepcion.chain(original)`
d) `raise NuevaExcepcion + original`

### Pregunta 23
¿Qué estructura de `collections` es ideal para contar elementos?

a) `defaultdict`
b) `OrderedDict`
c) `Counter`
d) `deque`

### Pregunta 24
¿Qué ventaja tiene `deque` sobre `list` para colas?

a) Ocupa menos memoria
b) `append` y `pop` en ambos extremos son O(1)
c) Permite duplicados
d) Es más rápida para acceso aleatorio

### Pregunta 25
¿Qué genera automáticamente un `@dataclass`?

a) Solo `__init__`
b) `__init__`, `__repr__`, `__eq__`
c) Solo métodos de comparación
d) Solo serialización JSON

---

## Respuestas

<details>
<summary>Click para ver respuestas</summary>

### Parte 1: Context Managers
1. **b** - `__enter__` y `__exit__`
2. **b** - Retorna el valor asignado después de `as`
3. **c** - Siempre, haya o no excepciones
4. **b** - `@contextlib.contextmanager`
5. **b** - Se suprime (no se propaga)

### Parte 2: Decoradores
6. **b** - Una función que modifica otra función
7. **b** - `funcion = mi_decorador(funcion)`
8. **b** - Preservar nombre, docstring, etc.
9. **a** - Con tres funciones anidadas
10. **b** - Cachea resultados
11. **c** - Cualquier combinación de argumentos

### Parte 3: Generadores
12. **b** - Usa `yield` y produce valores bajo demanda
13. **b** - Usa memoria constante
14. **b** - Paréntesis: `(x**2 for x in range(10))`
15. **b** - Delega a otro generador/iterable
16. **b** - `StopIteration`

### Parte 4: Closures
17. **b** - Recuerda variables del scope donde fue definida
18. **c** - `nonlocal`
19. **b** - Crea función con argumentos pre-configurados
20. **b** - Retorna un objeto map (iterador)

### Parte 5: Excepciones y estructuras
21. **b** - Cuando NO hay excepción
22. **b** - `raise NuevaExcepcion from original`
23. **c** - `Counter`
24. **b** - O(1) en ambos extremos
25. **b** - `__init__`, `__repr__`, `__eq__`

### Puntuación
- 22-25: Excelente comprensión
- 18-21: Buen nivel
- 14-17: Necesita repasar algunos temas
- <14: Revisar el material nuevamente

</details>

---

*Computación II - 2026 - Bloque 0 Autónomo*
