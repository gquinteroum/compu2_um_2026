# Clase 1: Docker Intro - Ejercicios

## Objetivo

Estos ejercicios te van a familiarizar con los comandos básicos de Docker. Al terminar, deberías poder correr contenedores, explorarlos, y ejecutar código Python dentro de ellos.

**Prerequisito:** Docker instalado y funcionando. Verificá con `docker run hello-world`.

---

## Ejercicio 1: Explorando contenedores

### 1.1: Tu primer contenedor interactivo

Ejecutá un contenedor Ubuntu en modo interactivo:

```bash
docker run -it ubuntu bash
```

Ahora estás "dentro" de un Ubuntu. Probá estos comandos:

```bash
# ¿Qué versión de Ubuntu es?
cat /etc/os-release

# ¿Qué usuario sos?
whoami

# ¿Qué procesos están corriendo?
ps aux

# ¿Qué hay en el sistema de archivos?
ls /

# Instalar algo (es un Ubuntu normal)
apt update && apt install -y cowsay
/usr/games/cowsay "Hola desde Docker"

# Salir
exit
```

### 1.2: El contenedor es efímero

Volvé a correr el mismo comando:

```bash
docker run -it ubuntu bash
```

Intentá correr cowsay:

```bash
/usr/games/cowsay "Hola"
```

No existe. Todo lo que instalaste se perdió. Cada `docker run` crea un contenedor NUEVO a partir de la imagen original.

**Pregunta:** ¿Cómo podrías hacer para que cowsay esté siempre disponible? (Pista: necesitás crear tu propia imagen. Lo veremos la próxima clase.)

### 1.3: Ver tus contenedores

En otra terminal (sin cerrar el contenedor si tenés uno corriendo):

```bash
# Contenedores corriendo
docker ps

# Todos los contenedores
docker ps -a
```

¿Cuántos contenedores tenés? Cada vez que hiciste `docker run`, creaste uno nuevo.

---

## Ejercicio 2: Python en Docker

### 2.1: Python interactivo

```bash
docker run -it python
```

Estás en el intérprete Python dentro de un contenedor. Probá:

```python
import sys
print(f"Python {sys.version}")

import os
print(f"Sistema: {os.uname()}")

# Es un Python normal, podés hacer lo que quieras
import json
data = {"nombre": "Docker", "año": 2026}
print(json.dumps(data, indent=2))

exit()
```

### 2.2: Ejecutar un comando Python

En lugar de entrar al intérprete, podés ejecutar directamente:

```bash
docker run python python -c "print('Hola desde contenedor')"

docker run python python -c "import platform; print(platform.platform())"
```

### 2.3: Diferentes versiones de Python

```bash
# Python 3.11
docker run python:3.11 python --version

# Python 3.9
docker run python:3.9 python --version

# Python 3.8 (versión slim, más liviana)
docker run python:3.8-slim python --version
```

**Tarea:** Verificá qué versión de Python tenés instalada localmente (`python --version`). ¿Es la misma que en el contenedor por defecto?

---

## Ejercicio 3: Ejecutar scripts locales

### 3.1: Crear un script de prueba

Creá un archivo `hola.py` en tu directorio actual:

```python
#!/usr/bin/env python3
"""Script de prueba para Docker."""

import os
import sys
from datetime import datetime

print("="*50)
print("Ejecutando en Docker")
print("="*50)
print(f"Python: {sys.version}")
print(f"Sistema: {os.uname().sysname}")
print(f"Hostname: {os.uname().nodename}")
print(f"Fecha: {datetime.now().isoformat()}")
print(f"Usuario: {os.getenv('USER', 'desconocido')}")
print(f"Directorio actual: {os.getcwd()}")
print(f"Archivos aquí: {os.listdir('.')}")
print("="*50)
```

### 3.2: Ejecutarlo en Docker

```bash
# Montar el directorio actual y ejecutar
docker run -v $(pwd):/app -w /app python python hola.py
```

Deberías ver información del contenedor. Notá que:
- El hostname es un ID corto (el ID del contenedor)
- El directorio actual es `/app`
- Los archivos son los de tu directorio local

### 3.3: Entender el montaje

```bash
# Crear un archivo desde el contenedor
docker run -v $(pwd):/app -w /app python python -c "
with open('desde_docker.txt', 'w') as f:
    f.write('Este archivo fue creado dentro del contenedor\n')
print('Archivo creado')
"

# Verificar que existe en tu máquina local
cat desde_docker.txt
```

El volumen (`-v`) hace que el directorio sea compartido. Los cambios en uno se reflejan en el otro.

---

## Ejercicio 4: Gestión de contenedores

### 4.1: Contenedores en background

Hasta ahora, los contenedores terminaban cuando terminaba el comando. Podés correr en background:

```bash
# -d = detached (background)
docker run -d --name mi-python python sleep 300
```

Esto crea un contenedor llamado "mi-python" que simplemente duerme por 5 minutos.

```bash
# Verificar que está corriendo
docker ps

# Ver los logs (no hay muchos, solo duerme)
docker logs mi-python

# Ejecutar un comando DENTRO del contenedor corriendo
docker exec -it mi-python python -c "print('Hola desde el contenedor en background')"

# Detenerlo
docker stop mi-python

# Eliminarlo
docker rm mi-python
```

### 4.2: Limpieza

Después de experimentar, probablemente tengas muchos contenedores acumulados:

```bash
# Ver cuántos contenedores hay
docker ps -a

# Eliminar todos los contenedores detenidos
docker container prune
```

Respondé "y" cuando pregunte.

```bash
# Ver cuánto espacio ocupan las imágenes
docker images

# Opcional: eliminar imágenes que no usás
docker image prune
```

---

## Ejercicio 5: Script con dependencias

### 5.1: El problema

Creá `con_dependencias.py`:

```python
#!/usr/bin/env python3
"""Script que usa una biblioteca externa."""

import requests

response = requests.get("https://httpbin.org/get")
print(f"Status: {response.status_code}")
print(f"Datos: {response.json()['origin']}")
```

Intentá correrlo:

```bash
docker run -v $(pwd):/app -w /app python python con_dependencias.py
```

Falla porque `requests` no está instalado en la imagen base de Python.

### 5.2: Solución temporal

Podés instalar en el momento:

```bash
docker run -v $(pwd):/app -w /app python sh -c "pip install requests && python con_dependencias.py"
```

Funciona, pero:
- Instala requests cada vez (lento)
- No es reproducible de forma limpia

### 5.3: La solución correcta (preview)

La próxima clase vas a aprender a crear tu propia imagen con las dependencias pre-instaladas. Eso resuelve el problema correctamente.

---

## Ejercicio de síntesis

Creá un script `info_sistema.py` que muestre:
- Versión de Python
- Sistema operativo (nombre, versión)
- Cantidad de CPUs disponibles
- Memoria disponible (si podés obtenerla)
- Variables de entorno que empiecen con "PYTHON"

Ejecutalo:
1. En tu máquina local
2. En un contenedor Docker con Python 3.11
3. En un contenedor Docker con Python 3.9

Compará los resultados. ¿Qué diferencias notás?

---

## Checklist

Antes de irte, verificá que podés:

- [ ] Ejecutar `docker run hello-world` sin errores
- [ ] Correr un contenedor interactivo (`docker run -it ubuntu bash`)
- [ ] Ver contenedores con `docker ps` y `docker ps -a`
- [ ] Ejecutar Python en un contenedor
- [ ] Montar un directorio local con `-v`
- [ ] Usar diferentes versiones de Python (tags)
- [ ] Detener y eliminar contenedores

---

*Computación II - 2026 - Clase 1*
