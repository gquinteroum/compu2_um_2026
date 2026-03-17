# Clase 1: Introducción a Docker

## ¿Por qué empezamos con Docker?

En un curso de programación concurrente y de sistemas, podrías esperar empezar hablando de procesos, hilos, o sockets. Pero hay un problema práctico que tenemos que resolver primero: ¿cómo vas a practicar todo esto?

Si cada estudiante tiene un sistema operativo diferente (Windows, Mac, distintas distribuciones de Linux), con diferentes versiones de Python, diferentes configuraciones... el 80% del tiempo de la clase se va a ir en "no me funciona en mi máquina". Y peor: cuando hagas los TPs, yo voy a tener que corregirlos en mi máquina, y si funcionaban solo en la tuya porque tenías algo especial instalado, hay un problema.

Docker resuelve esto de raíz. Te permite crear entornos aislados y reproducibles que funcionan igual en cualquier máquina. Tu código corre en el mismo ambiente que el mío. Si funciona en Docker, funciona en todos lados.

Pero Docker no es solo una herramienta de conveniencia para el curso. Es una tecnología que transformó la industria del software. Hoy en día, prácticamente todas las aplicaciones de producción corren en contenedores. Aprender Docker ahora es invertir en una habilidad que vas a usar toda tu carrera.

---

## El problema que Docker resuelve

### La pesadilla de "funciona en mi máquina"

Imaginate este escenario:

1. Desarrollaste un script en tu notebook con Python 3.11
2. Tu compañero lo corre en su máquina con Python 3.8 y falla
3. Lo subís al servidor de la universidad que tiene Python 3.6 y explota
4. El docente lo intenta correr en Linux y encuentra que usaste una biblioteca que solo existe para Windows

Cada paso es un dolor de cabeza. Y no es solo Python: tenés dependencias del sistema, bibliotecas compiladas, configuraciones de red, variables de entorno...

### La solución tradicional: máquinas virtuales

Antes de Docker, la solución era usar máquinas virtuales (VMs). Una VM simula un computador completo: hardware virtual, sistema operativo propio, todo.

Funciona, pero tiene problemas:
- **Pesadas**: cada VM necesita su propio kernel y sistema operativo (varios GB)
- **Lentas para arrancar**: minutos
- **Desperdician recursos**: si tenés 3 VMs con el mismo Ubuntu, tenés 3 copias del kernel

### La solución Docker: contenedores

Docker usa un enfoque diferente. En lugar de simular hardware, aprovecha características del kernel de Linux para crear **entornos aislados** que comparten el kernel del host.

Un contenedor:
- Es **liviano**: solo incluye tu aplicación y sus dependencias directas (MB en vez de GB)
- **Arranca en segundos** (o menos)
- **Comparte recursos eficientemente**: 100 contenedores pueden usar el mismo kernel

La diferencia conceptual es importante:
- Una VM es como una casa completa con sus propios cimientos
- Un contenedor es como un departamento en un edificio: tiene paredes propias pero comparte la estructura

---

## Conceptos fundamentales

### Imágenes vs Contenedores

Esta distinción es crucial y mucha gente la confunde al principio.

**Imagen**: es como un plano o una receta. Define exactamente qué hay en el sistema de archivos: qué sistema operativo base, qué programas instalados, qué archivos copiados. Una imagen es **inmutable** - no cambia.

**Contenedor**: es una instancia corriendo de una imagen. Podés crear muchos contenedores a partir de la misma imagen, igual que podés imprimir muchas copias de un documento. Cada contenedor tiene su propio estado: procesos corriendo, archivos modificados, etc.

Analogía:
- La imagen es la receta de una torta
- El contenedor es la torta ya horneada
- Podés hacer muchas tortas de la misma receta, y cada una es independiente

### El registry: Docker Hub

Las imágenes se almacenan en **registries**. Docker Hub es el registry público más usado. Cuando hacés `docker run python`, Docker busca la imagen `python` en Docker Hub.

Es como npm para Node.js o pip para Python, pero para imágenes completas de sistemas.

### Dockerfile

Es el archivo de texto que define cómo construir una imagen. Contiene instrucciones como:
- Qué imagen base usar
- Qué comandos ejecutar (instalar paquetes, copiar archivos)
- Qué puerto exponer
- Qué comando ejecutar cuando arranque el contenedor

Lo veremos en detalle en la próxima clase.

---

## Instalación

### Linux (Ubuntu/Debian)

```bash
# Instalar Docker
curl -fsSL https://get.docker.com | sh

# Agregar tu usuario al grupo docker (para no usar sudo)
sudo usermod -aG docker $USER

# Cerrar sesión y volver a entrar para que tome efecto
# O ejecutar: newgrp docker
```

### Windows

1. Instalar WSL2 (Windows Subsystem for Linux 2)
2. Descargar Docker Desktop desde docker.com
3. En la configuración, habilitar integración con WSL2

Docker Desktop en Windows realmente corre los contenedores en una VM Linux liviana, pero lo hace de forma transparente.

### macOS

Descargar Docker Desktop desde docker.com e instalar.

### Verificar instalación

```bash
docker --version
# Docker version 24.x.x, build xxxxx

docker run hello-world
# Debería descargar una imagen de prueba y mostrar un mensaje de éxito
```

---

## Primeros comandos

### docker run: ejecutar un contenedor

El comando más importante. Crea y ejecuta un contenedor a partir de una imagen.

```bash
# Forma básica
docker run <imagen>

# Ejemplo: correr un contenedor Ubuntu y abrir un shell
docker run -it ubuntu bash
```

Las opciones `-it` son importantes:
- `-i` (interactive): mantiene stdin abierto
- `-t` (tty): asigna una terminal

Sin ellas, el contenedor arranca y termina inmediatamente (porque `bash` sin terminal no tiene nada que hacer).

**Probalo**: ejecutá el comando anterior. Vas a estar "dentro" de un Ubuntu, con root. Podés ejecutar `ls`, `cat /etc/os-release`, cualquier cosa. Cuando escribas `exit`, el contenedor se detiene.

### docker ps: ver contenedores

```bash
# Contenedores corriendo actualmente
docker ps

# Todos los contenedores (incluyendo detenidos)
docker ps -a
```

La salida muestra el ID del contenedor, la imagen, el comando, cuándo se creó, el estado, y el nombre (Docker asigna nombres aleatorios graciosos si no especificás uno).

### docker images: ver imágenes

```bash
docker images
```

Muestra las imágenes descargadas en tu sistema. Después de correr `ubuntu` y `hello-world`, deberías ver ambas.

### docker stop / docker start: detener e iniciar

```bash
# Detener un contenedor por nombre o ID
docker stop <nombre_o_id>

# Iniciar un contenedor detenido
docker start <nombre_o_id>
```

### docker rm / docker rmi: eliminar

```bash
# Eliminar un contenedor (debe estar detenido)
docker rm <nombre_o_id>

# Eliminar una imagen (no debe haber contenedores usándola)
docker rmi <imagen>
```

---

## Docker y Python

Lo que realmente nos interesa en este curso.

### Ejecutar un script Python

```bash
# Correr Python interactivo
docker run -it python

# Correr un comando Python
docker run python python -c "print('Hola desde Docker')"
```

Pero esto tiene un problema: el contenedor no tiene acceso a tus archivos locales. Necesitamos **volúmenes** (tema de la próxima clase).

Por ahora, una solución rápida:

```bash
# Montar el directorio actual dentro del contenedor
docker run -v $(pwd):/app -w /app python python mi_script.py
```

Explicación:
- `-v $(pwd):/app`: monta el directorio actual (pwd) en `/app` dentro del contenedor
- `-w /app`: establece `/app` como directorio de trabajo
- `python mi_script.py`: el comando a ejecutar

### Usar una versión específica de Python

Las imágenes tienen **tags** que especifican versiones:

```bash
docker run python:3.11
docker run python:3.10-slim
docker run python:3.9-alpine
```

La variante `slim` es más liviana (sin muchas herramientas extras).
La variante `alpine` es la más pequeña (basada en Alpine Linux, ~50MB vs ~900MB).

---

## Contenedores efímeros vs persistentes

Por defecto, los contenedores son **efímeros**: cuando los detenés, cualquier cambio que hiciste (archivos creados, programas instalados) se pierde. El próximo `docker run` arranca de la imagen original.

Esto es una característica, no un bug. Garantiza que siempre partís de un estado conocido.

Si querés persistencia, tenés dos opciones:
1. **Volúmenes**: montar directorios del host (lo veremos la próxima clase)
2. **Crear una nueva imagen**: con los cambios que querés preservar

---

## Workflow típico de desarrollo

Para este curso, el flujo habitual va a ser:

1. Escribís código en tu máquina con tu editor favorito
2. Corrés el código dentro de un contenedor Docker
3. Los archivos están sincronizados via volúmenes

Esto te da lo mejor de ambos mundos: la comodidad de tu entorno de desarrollo, con la consistencia del ambiente Docker.

---

## Troubleshooting común

### "Cannot connect to the Docker daemon"

```bash
# Linux: verificar que el servicio está corriendo
sudo systemctl start docker

# Verificar que tu usuario está en el grupo docker
groups $USER
```

### "Permission denied"

En Linux, si no agregaste tu usuario al grupo docker, tenés que usar `sudo docker ...`. Pero es más conveniente agregarte al grupo:

```bash
sudo usermod -aG docker $USER
# Cerrar sesión y volver a entrar
```

### "No space left on device"

Docker puede acumular imágenes y contenedores viejos. Limpieza:

```bash
# Eliminar contenedores detenidos
docker container prune

# Eliminar imágenes sin usar
docker image prune

# Limpieza agresiva (todo lo no utilizado)
docker system prune -a
```

### El contenedor sale inmediatamente

Si corrés `docker run ubuntu` sin `-it` y sin comando, el contenedor arranca, no tiene nada que hacer, y termina.

Soluciones:
- Agregar `-it` para modo interactivo
- Especificar un comando que mantenga el proceso vivo
- Usar `-d` para modo daemon (corre en background)

---

## Lo que viene

En la próxima clase vamos a profundizar con:
- **Volúmenes**: para persistir datos y compartir archivos con el host
- **Redes**: para que los contenedores se comuniquen entre sí
- **docker-compose**: para manejar múltiples contenedores fácilmente

Por ahora, lo importante es que tengas Docker funcionando y entiendas los comandos básicos.

---

## Resumen de comandos

| Comando | Para qué |
|---------|----------|
| `docker run <imagen>` | Crear y ejecutar contenedor |
| `docker run -it <imagen> bash` | Contenedor interactivo |
| `docker ps` | Ver contenedores corriendo |
| `docker ps -a` | Ver todos los contenedores |
| `docker images` | Ver imágenes descargadas |
| `docker stop <id>` | Detener contenedor |
| `docker rm <id>` | Eliminar contenedor |
| `docker rmi <imagen>` | Eliminar imagen |

---

## Tarea

1. **Instalar Docker** en tu máquina y verificar con `docker run hello-world`

2. **Completar el Bloque 0 autónomo** para la clase 3:
   - Git y GitHub
   - argparse
   - Filesystem Linux
   - Python avanzado

3. **Crear tu repositorio del curso** `computacion2-2026` en GitHub (ver ejercicios de Git)

4. Opcional: explorar Docker Hub y ver qué imágenes hay disponibles

---

*Computación II - 2026 - Clase 1*
