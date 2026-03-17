# Clase 1: Docker Intro - Autoevaluación

Respondé estas preguntas para verificar tu comprensión. Las respuestas están al final.

---

## Conceptos (10 preguntas)

### Pregunta 1
¿Cuál es la diferencia principal entre un contenedor y una máquina virtual?

a) Los contenedores son más seguros
b) Los contenedores comparten el kernel del host, las VMs tienen su propio kernel
c) Los contenedores son más lentos
d) No hay diferencia significativa

### Pregunta 2
¿Qué es una imagen Docker?

a) Una copia del sistema operativo del host
b) Un template inmutable a partir del cual se crean contenedores
c) Un contenedor guardado
d) Un archivo de configuración

### Pregunta 3
¿Qué sucede cuando ejecutás `docker run ubuntu` sin ningún comando adicional?

a) Ubuntu arranca y queda corriendo indefinidamente
b) Ubuntu arranca, no tiene nada que hacer, y termina inmediatamente
c) Da un error porque falta el comando
d) Abre un shell interactivo

### Pregunta 4
¿Para qué sirven las opciones `-it` en `docker run -it ubuntu bash`?

a) Para correr más rápido
b) Para modo interactivo con terminal (interactive + tty)
c) Para instalar paquetes
d) Para correr en background

### Pregunta 5
¿Qué pasa con los archivos creados dentro de un contenedor cuando el contenedor se elimina?

a) Se guardan automáticamente en el host
b) Se pierden (a menos que uses volúmenes)
c) Se guardan en Docker Hub
d) Se mueven a /tmp

### Pregunta 6
¿Qué comando muestra los contenedores que están corriendo actualmente?

a) `docker images`
b) `docker ps`
c) `docker list`
d) `docker containers`

### Pregunta 7
¿Qué es Docker Hub?

a) El daemon de Docker
b) Un registro público de imágenes Docker
c) Una herramienta de desarrollo
d) El kernel de Docker

### Pregunta 8
Si querés correr Python 3.9 específicamente, ¿qué comando usás?

a) `docker run python-3.9`
b) `docker run python:3.9`
c) `docker run python --version 3.9`
d) `docker run python/3.9`

### Pregunta 9
¿Qué hace la opción `-v $(pwd):/app` en docker run?

a) Crea un volumen vacío llamado "app"
b) Monta el directorio actual del host en /app dentro del contenedor
c) Descarga la aplicación desde Docker Hub
d) Define una variable de entorno

### Pregunta 10
¿Por qué Docker es útil para este curso?

a) Porque es más rápido que Python nativo
b) Porque garantiza que todos trabajamos en el mismo ambiente
c) Porque es obligatorio para programación concurrente
d) Porque reemplaza a Git

---

## Comandos (5 preguntas)

### Pregunta 11
¿Qué comando elimina todos los contenedores detenidos?

a) `docker rm -all`
b) `docker container prune`
c) `docker clean`
d) `docker delete stopped`

### Pregunta 12
¿Cómo ves las imágenes descargadas en tu sistema?

a) `docker ps`
b) `docker images`
c) `docker list images`
d) `docker show`

### Pregunta 13
¿Qué comando ejecuta un proceso dentro de un contenedor que ya está corriendo?

a) `docker run`
b) `docker start`
c) `docker exec`
d) `docker attach`

### Pregunta 14
¿Cómo detenés un contenedor que está corriendo?

a) `docker kill`
b) `docker stop <id>`
c) `docker pause`
d) `docker end`

### Pregunta 15
¿Qué opción hace que un contenedor corra en background (detached)?

a) `-b`
b) `-d`
c) `-bg`
d) `--background`

---

## Respuestas

<details>
<summary>Click para ver respuestas</summary>

### Conceptos
1. **b** - Los contenedores comparten el kernel, las VMs tienen su propio kernel
2. **b** - Un template inmutable a partir del cual se crean contenedores
3. **b** - Ubuntu arranca, no tiene nada que hacer, y termina inmediatamente
4. **b** - Para modo interactivo con terminal
5. **b** - Se pierden (a menos que uses volúmenes)
6. **b** - `docker ps`
7. **b** - Un registro público de imágenes Docker
8. **b** - `docker run python:3.9`
9. **b** - Monta el directorio actual del host en /app dentro del contenedor
10. **b** - Porque garantiza que todos trabajamos en el mismo ambiente

### Comandos
11. **b** - `docker container prune`
12. **b** - `docker images`
13. **c** - `docker exec`
14. **b** - `docker stop <id>`
15. **b** - `-d`

### Puntuación
- 13-15: Excelente, estás listo para la próxima clase
- 10-12: Bien, pero repasá los conceptos que fallaste
- 7-9: Necesitás practicar más con los comandos
- <7: Volvé a leer el material y hacer los ejercicios

</details>

---

*Computación II - 2026 - Clase 1*
