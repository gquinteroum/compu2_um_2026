# Clase 1: Docker Intro - Extra Manijas

Este material es opcional para quienes quieren profundizar. No es requerido para el curso.

---

## Cómo funciona Docker por dentro

### Namespaces de Linux

Docker usa características del kernel de Linux llamadas **namespaces** para crear aislamiento. Cada contenedor tiene su propio:

- **PID namespace**: los procesos del contenedor no ven los del host
- **Network namespace**: tiene su propia interfaz de red, IP, puertos
- **Mount namespace**: su propio sistema de archivos
- **User namespace**: puede tener su propio root (aunque no sea root en el host)
- **UTS namespace**: su propio hostname

Esto explica por qué los contenedores se sienten como máquinas separadas pero arrancan instantáneamente: no hay virtualización de hardware, solo particionamiento del kernel.

```bash
# Ver los namespaces de un proceso
ls -la /proc/$$/ns/

# Entrar a un namespace de un contenedor (requiere root)
# docker run -d --name test ubuntu sleep 1000
# sudo nsenter --target $(docker inspect --format '{{.State.Pid}}' test) --mount --uts --ipc --net --pid bash
```

### cgroups

Los **control groups** (cgroups) permiten limitar los recursos que usa un contenedor:

```bash
# Limitar memoria a 256MB
docker run --memory=256m python python -c "print('Limitado a 256MB')"

# Limitar CPU a 0.5 cores
docker run --cpus=0.5 python python -c "print('Medio CPU')"
```

Podés ver los cgroups activos:

```bash
cat /sys/fs/cgroup/memory/docker/*/memory.limit_in_bytes
```

### El sistema de archivos en capas (Union FS)

Las imágenes Docker usan un sistema de archivos en **capas**. Cada instrucción en un Dockerfile crea una nueva capa sobre la anterior.

```bash
# Ver las capas de una imagen
docker history python:3.11

# Ver el tamaño de cada capa
docker history --no-trunc python:3.11
```

Cuando corrés un contenedor, Docker agrega una capa escribible arriba de las capas de solo lectura de la imagen. Esto explica:
- Por qué las imágenes son inmutables
- Por qué crear contenedores es casi instantáneo (no copia datos)
- Por qué los cambios se pierden al eliminar el contenedor

---

## Alternativas a Docker

### Podman

Podman es una alternativa "daemonless" a Docker, desarrollada por Red Hat:

```bash
# Instalación (Fedora/RHEL)
sudo dnf install podman

# Los comandos son casi idénticos
podman run -it ubuntu bash
podman ps
podman images
```

Ventajas:
- No requiere un daemon corriendo como root
- Puede correr contenedores como usuario normal sin privilegios
- Compatible con la CLI de Docker

### containerd + nerdctl

containerd es el runtime que usa Docker internamente. Podés usarlo directamente con nerdctl:

```bash
# nerdctl es "Docker-compatible CLI for containerd"
nerdctl run -it ubuntu bash
```

### LXC/LXD

Linux Containers es la tecnología de contenedores más antigua, anterior a Docker:

```bash
lxc launch ubuntu:22.04 mi-contenedor
lxc exec mi-contenedor bash
```

LXC es más parecido a una VM liviana (contenedores de sistema), mientras que Docker está optimizado para contenedores de aplicación.

---

## Docker en producción

### El problema del PID 1

En un contenedor, tu proceso corre como PID 1 (el proceso init). Esto trae responsabilidades:
- Debe manejar señales correctamente
- Debe limpiar procesos zombie

```bash
# Usar tini como init system liviano
docker run --init python python mi_script.py
```

O mejor, usar `--init` siempre o agregar tini a tu imagen.

### Seguridad

Nunca corras contenedores como root en producción si podés evitarlo:

```bash
# Correr como usuario específico
docker run --user 1000:1000 python python -c "import os; print(os.getuid())"
```

Otras medidas:
- `--read-only`: sistema de archivos de solo lectura
- `--security-opt=no-new-privileges`: prevenir escalación de privilegios
- `--cap-drop=ALL`: quitar todas las capabilities de Linux

### Logs y debugging

```bash
# Ver logs de un contenedor
docker logs <id>

# Seguir logs en tiempo real
docker logs -f <id>

# Ver eventos de Docker
docker events

# Inspeccionar un contenedor (toda la metadata)
docker inspect <id>

# Estadísticas de recursos en tiempo real
docker stats
```

---

## Optimización de imágenes

### Elegir la imagen base correcta

| Imagen | Tamaño | Uso |
|--------|--------|-----|
| `python:3.11` | ~900MB | Desarrollo, todo incluido |
| `python:3.11-slim` | ~150MB | Producción, sin extras |
| `python:3.11-alpine` | ~50MB | Mínimo, pero puede dar problemas |

Alpine usa musl libc en lugar de glibc, lo que puede causar incompatibilidades con algunas bibliotecas que asumen glibc.

### Multi-stage builds

Podés usar múltiples etapas para tener un build completo pero una imagen final mínima:

```dockerfile
# Etapa de build
FROM python:3.11 AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user -r requirements.txt

# Etapa final
FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /root/.local /root/.local
COPY . .
CMD ["python", "app.py"]
```

La imagen final solo tiene lo necesario para correr, no las herramientas de build.

---

## Docker Compose (preview)

Para manejar múltiples contenedores, Docker Compose es esencial. Un vistazo:

```yaml
# docker-compose.yml
version: '3.8'
services:
  web:
    image: python:3.11
    volumes:
      - .:/app
    working_dir: /app
    command: python app.py
    ports:
      - "8000:8000"

  redis:
    image: redis:alpine
```

```bash
docker-compose up
docker-compose down
```

Lo veremos en detalle la próxima clase.

---

## Recursos para seguir aprendiendo

- [Docker Documentation](https://docs.docker.com/)
- [Play with Docker](https://labs.play-with-docker.com/) - Docker en el browser
- [Container Training](https://container.training/) - Workshops gratuitos
- [Dive](https://github.com/wagoodman/dive) - Explorar capas de imágenes
- [Awesome Docker](https://github.com/veggiemonk/awesome-docker) - Lista curada

---

*Computación II - 2026 - Clase 1 - Material opcional*
