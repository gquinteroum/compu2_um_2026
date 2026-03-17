# Git y GitHub - Autoevaluación

Responde estas preguntas para verificar tu comprensión. Las respuestas están al final.

---

## Parte 1: Conceptos (10 preguntas)

### Pregunta 1
¿Cuál es la diferencia principal entre Git y sistemas como SVN?

a) Git es más nuevo
b) Git es distribuido, cada desarrollador tiene una copia completa
c) Git solo funciona con GitHub
d) Git no requiere internet

### Pregunta 2
¿Qué almacena Git en cada commit?

a) Solo las diferencias respecto al commit anterior
b) Una copia completa (snapshot) del proyecto
c) Solo los archivos modificados
d) Un enlace al servidor

### Pregunta 3
¿Cuáles son las tres áreas principales de Git?

a) Local, remoto, cloud
b) Working directory, staging area, repository
c) Branch, commit, push
d) Add, commit, push

### Pregunta 4
¿Qué hace `git add archivo.py`?

a) Crea el archivo
b) Sube el archivo a GitHub
c) Mueve el archivo al staging area
d) Hace commit del archivo

### Pregunta 5
¿Qué comando muestra los cambios que aún NO están en staging?

a) `git status`
b) `git diff`
c) `git diff --staged`
d) `git log`

### Pregunta 6
¿Qué es una branch en Git?

a) Una copia del repositorio
b) Un puntero móvil a un commit
c) Un servidor remoto
d) Un tipo de merge

### Pregunta 7
¿Qué sucede cuando hay un conflicto de merge?

a) Git elige automáticamente una versión
b) Git rechaza el merge completamente
c) Git marca los conflictos y espera que los resuelvas manualmente
d) Se borra una de las branches

### Pregunta 8
¿Qué significa "origin" en Git?

a) El commit inicial
b) El nombre convencional del repositorio remoto principal
c) La branch principal
d) El autor original

### Pregunta 9
¿Cuál es la diferencia entre `git fetch` y `git pull`?

a) Son sinónimos
b) `fetch` descarga sin integrar, `pull` descarga e integra
c) `fetch` es más rápido
d) `pull` solo funciona con GitHub

### Pregunta 10
¿Para qué sirve `.gitignore`?

a) Para ignorar errores de Git
b) Para especificar archivos que Git no debe trackear
c) Para ocultar branches
d) Para ignorar commits

---

## Parte 2: Comandos (10 preguntas)

### Pregunta 11
¿Qué comando crea un nuevo repositorio Git?

a) `git create`
b) `git new`
c) `git init`
d) `git start`

### Pregunta 12
¿Cómo creas una nueva branch llamada "feature"?

a) `git branch feature`
b) `git new branch feature`
c) `git create feature`
d) `git feature`

### Pregunta 13
¿Cómo cambias a la branch "develop"?

a) `git branch develop`
b) `git switch develop` o `git checkout develop`
c) `git move develop`
d) `git go develop`

### Pregunta 14
¿Qué comando combina los cambios de "feature" en "main"? (estando en main)

a) `git merge feature`
b) `git pull feature`
c) `git combine feature`
d) `git join feature`

### Pregunta 15
¿Cómo descartas los cambios no commiteados en un archivo?

a) `git reset archivo.py`
b) `git restore archivo.py`
c) `git discard archivo.py`
d) `git undo archivo.py`

### Pregunta 16
¿Qué comando sube tus commits al servidor remoto?

a) `git upload`
b) `git send`
c) `git push`
d) `git sync`

### Pregunta 17
¿Cómo ves el historial de commits en formato resumido?

a) `git log --short`
b) `git log --oneline`
c) `git history`
d) `git commits`

### Pregunta 18
¿Cómo agregas un repositorio remoto?

a) `git remote add origin URL`
b) `git connect origin URL`
c) `git link origin URL`
d) `git server add URL`

### Pregunta 19
¿Qué comando muestra el estado actual del repositorio?

a) `git info`
b) `git state`
c) `git status`
d) `git check`

### Pregunta 20
¿Cómo corriges el mensaje del último commit (no pusheado)?

a) `git fix --message`
b) `git commit --amend`
c) `git edit last`
d) `git change -m`

---

## Parte 3: Situaciones prácticas

### Situación 1
Hiciste cambios en varios archivos pero solo quieres commitear `main.py`.
¿Qué secuencia de comandos usas?

a) `git add . && git commit`
b) `git add main.py && git commit -m "mensaje"`
c) `git commit main.py`
d) `git push main.py`

### Situación 2
Intentas hacer push pero Git dice que el remoto tiene cambios que no tienes.
¿Qué debes hacer primero?

a) `git push --force`
b) `git pull` (luego resolver conflictos si los hay)
c) Borrar el repositorio y clonarlo de nuevo
d) Crear una nueva branch

### Situación 3
Quieres ver qué archivos están siendo ignorados y por qué.
¿Qué comando ayuda?

a) `git status --ignored`
b) `git ignore --list`
c) `git check-ignore -v archivo`
d) `git hidden`

### Situación 4
Trabajaste en cambios pero necesitas urgente cambiar de branch sin hacer commit.
¿Qué comando te permite guardar temporalmente tus cambios?

a) `git save`
b) `git stash`
c) `git temp`
d) `git hide`

### Situación 5
Quieres contribuir a un proyecto open source en GitHub.
¿Cuál es el flujo correcto?

a) Clonar directo y hacer push
b) Fork → Clone → Branch → Commit → Push → Pull Request
c) Solo crear un Issue
d) Editar directo en GitHub

---

## Respuestas

<details>
<summary>Click para ver respuestas</summary>

### Parte 1: Conceptos
1. **b** - Git es distribuido
2. **b** - Snapshot completo (optimizado internamente)
3. **b** - Working directory, staging area, repository
4. **c** - Mueve al staging area
5. **b** - `git diff` (--staged es para los que YA están en staging)
6. **b** - Un puntero móvil a un commit
7. **c** - Marca conflictos para resolución manual
8. **b** - Nombre convencional del remoto principal
9. **b** - fetch solo descarga, pull descarga e integra
10. **b** - Archivos que Git no debe trackear

### Parte 2: Comandos
11. **c** - `git init`
12. **a** - `git branch feature`
13. **b** - `git switch` o `git checkout`
14. **a** - `git merge feature`
15. **b** - `git restore archivo.py`
16. **c** - `git push`
17. **b** - `git log --oneline`
18. **a** - `git remote add origin URL`
19. **c** - `git status`
20. **b** - `git commit --amend`

### Parte 3: Situaciones
1. **b** - Agregar solo el archivo específico
2. **b** - Pull primero (NUNCA --force sin entender las consecuencias)
3. **c** - `git check-ignore -v` muestra qué regla lo ignora
4. **b** - `git stash`
5. **b** - Fork → Clone → Branch → Commit → Push → PR

### Puntuación
- 20-25: Excelente comprensión
- 15-19: Buen nivel, repasar algunos conceptos
- 10-14: Necesita más práctica
- <10: Revisar el material nuevamente

</details>

---

*Computación II - 2026 - Bloque 0 Autónomo*
