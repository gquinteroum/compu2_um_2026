# Git y GitHub

## ¿Por qué empezamos con esto?

Antes de escribir una sola línea de código sobre procesos, hilos o sockets, necesitás dominar la herramienta que va a acompañarte durante toda tu carrera profesional: **Git**.

Pensalo así: un médico no opera sin saber usar un bisturí, un carpintero no construye sin dominar el serrucho. Para un desarrollador, Git es esa herramienta fundamental. No es opcional, no es "algo lindo de saber". Es el estándar de la industria desde hace más de 15 años, y no hay empresa de software seria que no lo use.

En esta materia vas a escribir mucho código. Vas a experimentar, vas a romper cosas, vas a querer volver atrás. Vas a trabajar en ejercicios, en dos trabajos prácticos grandes, y eventualmente en proyectos con otros. Sin Git, todo eso es un caos de carpetas con nombres como `tp1_final_v2_ahora_si_FINAL.py`. Con Git, tenés control total sobre la historia de tu código.

---

## ¿Qué es Git, realmente?

Cuando la mayoría de la gente piensa en "guardar versiones", imagina algo como Google Docs: un servidor central que guarda todo y vos accedés a él. Eso funcionaría, pero tiene un problema enorme: si el servidor se cae, nadie puede trabajar. Si no hay internet, quedás paralizado.

Git funciona diferente. Es un **sistema distribuido**: cada persona que trabaja en un proyecto tiene una copia **completa** de toda la historia del proyecto en su máquina. No dependés de ningún servidor para trabajar. Podés hacer commits, crear branches, revisar la historia, todo offline. Solo necesitás conexión cuando querés sincronizar con otros.

Esto no es solo conveniente, es liberador. Podés experimentar localmente todo lo que quieras sin afectar a nadie más. Si rompés algo, la historia completa está ahí para rescatarte.

### El modelo mental correcto

Olvidate de pensar en Git como un sistema que "guarda diferencias" entre versiones. Git funciona con **snapshots**: cada vez que hacés un commit, Git toma una foto completa del estado de todos tus archivos en ese momento.

Internamente Git es muy inteligente y no duplica archivos que no cambiaron, pero conceptualmente, cada commit es una foto completa. Esto hace que moverse entre versiones sea increíblemente rápido: no hay que "reconstruir" nada aplicando diferencias, simplemente se carga la foto correspondiente.

### Las tres áreas que tenés que entender

Este es el concepto más importante para usar Git sin frustrarte. Tu proyecto vive en tres "lugares" diferentes:

**1. El directorio de trabajo (working directory)**

Es simplemente la carpeta donde están tus archivos. Lo que ves en tu editor, lo que modificás día a día. Git observa esta carpeta, pero los cambios que hagas acá no se guardan automáticamente en la historia.

**2. El área de staging (index)**

Es como una sala de espera antes de un commit. Cuando hacés `git add archivo.py`, le estás diciendo a Git: "este cambio quiero que entre en el próximo commit". Podés agregar algunos archivos y dejar otros afuera. Esto te da control fino sobre qué entra en cada commit.

¿Por qué existe esto? Porque a veces estás trabajando en varias cosas a la vez y querés hacer commits separados y limpios. Modificaste 5 archivos, pero solo 2 son del feature que querés commitear ahora. El staging te permite elegir.

**3. El repositorio (la historia)**

Cuando hacés `git commit`, todo lo que estaba en staging se convierte en un commit permanente en la historia. Cada commit tiene un identificador único (un hash como `a1b2c3d4`), sabe quién lo hizo, cuándo, y tiene un mensaje que explica el cambio.

El flujo típico es: modificás archivos → los agregás al staging → hacés commit. Modificás → staging → commit. Ese ritmo se vuelve automático con la práctica.

---

## Empezando: configuración inicial

Antes de hacer tu primer commit, Git necesita saber quién sos. Esto no es burocracia: cada commit queda firmado con tu nombre y email, y esa información es importante cuando trabajás en equipo.

```bash
git config --global user.name "Tu Nombre Completo"
git config --global user.email "tu@email.com"
```

El `--global` significa que esto aplica a todos tus repositorios. Esto se hace una sola vez en cada máquina que uses.

---

## El ciclo diario de trabajo

### Crear un repositorio

Hay dos formas de empezar. **Desde cero**, cuando arrancás un proyecto nuevo:

```bash
mkdir mi-proyecto
cd mi-proyecto
git init
```

Esto crea una carpeta oculta `.git` que contiene toda la magia. Esa carpeta ES tu repositorio, toda la historia vive ahí.

**Clonando**, cuando el proyecto ya existe en algún lado:

```bash
git clone https://github.com/alguien/proyecto.git
```

Esto descarga todo: los archivos actuales y toda la historia de commits.

### El ritmo básico

El 90% de tu uso diario de Git va a ser esto:

```bash
git status          # Ver qué cambió
git add archivo.py  # Agregar cambios al staging
git commit -m "Descripción breve del cambio"
```

`git status` es tu mejor amigo. Te dice qué archivos modificaste, cuáles están en staging, cuáles son nuevos. Usalo seguido, especialmente cuando estás aprendiendo.

Para el mensaje del commit, hay una regla de oro: **describí el "qué" y el "por qué", no el "cómo"**. El código ya dice cómo lo hiciste. El mensaje explica la intención.

- Mal: `"Cambié la línea 42 del archivo main.py"`
- Bien: `"Corregir cálculo de promedio que fallaba con listas vacías"`

### Ver la historia

```bash
git log              # Historia completa
git log --oneline    # Una línea por commit (más útil)
```

El log es tu máquina del tiempo. Podés ver exactamente qué se hizo, cuándo, y por quién.

### Ver diferencias

```bash
git diff             # Qué cambió (no staged todavía)
git diff --staged    # Qué está listo para commit
```

---

## Branches: el superpoder de Git

Acá es donde Git realmente brilla. Un **branch** (rama) es simplemente un puntero a un commit. Cuando creás un branch nuevo, Git no copia nada: solo crea un nuevo puntero.

¿Para qué sirve? Imaginá que estás trabajando en tu proyecto y se te ocurre una idea experimental. Sin branches, tendrías dos opciones malas: o experimentás directamente y te arriesgás a romper lo que funciona, o hacés una copia de la carpeta y terminás con un lío de versiones.

Con branches, creás una rama nueva, experimentás todo lo que quieras, y si funciona, la integrás. Si no funciona, la borrás. La rama principal nunca se enteró del experimento fallido.

### Trabajando con branches

```bash
git branch                    # Ver branches (el * marca dónde estás)
git branch feature-login      # Crear branch nuevo
git switch feature-login      # Moverte a ese branch
git switch -c feature-login   # Crear y moverte en un paso
```

Ahora todos los commits que hagas van a ese branch. El branch `main` queda intacto.

### Merge: uniendo branches

Cuando tu feature está lista:

```bash
git switch main         # Volvé a main
git merge feature-login # Integrá el otro branch
```

Si los cambios no se pisan, Git hace el merge automáticamente. Si hay conflictos (dos personas modificaron la misma línea), Git te marca dónde están y vos decidís qué versión queda.

Un conflicto se ve así:

```
<<<<<<< HEAD
código que estaba en main
=======
código que venía de feature-login
>>>>>>> feature-login
```

Editás el archivo, elegís qué queda, borrás los marcadores, y commiteas. Los conflictos asustan al principio, pero con práctica se vuelven rutinarios.

---

## GitHub: Git en la nube

**Git** es local. **GitHub** es un servicio que hostea repositorios en internet y agrega herramientas de colaboración.

No son lo mismo: podés usar Git sin GitHub, y hay alternativas (GitLab, Bitbucket). Pero GitHub es el estándar de facto, y es donde vas a tener tu repositorio del curso.

### Conectar con GitHub

Después de crear un repositorio en GitHub (vacío, sin README):

```bash
git remote add origin https://github.com/tu-usuario/tu-repo.git
git push -u origin main
```

`origin` es el nombre convencional para el repositorio remoto principal.

### Sincronización

```bash
git push    # Subir tus commits al servidor
git pull    # Bajar commits del servidor e integrarlos
```

Si alguien más subió cambios que vos no tenés, `git push` va a fallar. Primero hacés `git pull`, después push.

### Pull Requests

En proyectos colaborativos, generalmente no pusheás directo a `main`. El flujo es:

1. Creás un branch para tu feature
2. Hacés commits ahí
3. Pusheás ese branch a GitHub
4. Abrís un **Pull Request**: una solicitud para que tus cambios se integren
5. Otros revisan, comentan, sugieren cambios
6. Cuando está aprobado, se hace merge

---

## Buenas prácticas

### Commits frecuentes y pequeños

Cada commit debería ser una unidad lógica de cambio. Commits pequeños hacen más fácil entender la historia y encontrar cuándo se introdujo un bug.

### Mensajes descriptivos

Vas a leer estos mensajes dentro de seis meses. Tu yo futuro te lo va a agradecer.

Un buen formato:
```
tipo: descripción corta

Explicación más larga si hace falta.
```

Tipos comunes: `feat:` (feature nueva), `fix:` (corrección), `docs:` (documentación), `refactor:` (reestructuración).

### .gitignore

Hay archivos que nunca deberían entrar al repositorio. Creá un archivo `.gitignore`:

```
venv/
__pycache__/
.env
.idea/
.vscode/
*.pyc
```

Git va a ignorar estos archivos completamente.

---

## Comandos de emergencia

Cuando las cosas salen mal (y van a salir mal), estos comandos te salvan:

```bash
# Descartar cambios no commiteados
git restore archivo.py

# Sacar algo del staging sin perder cambios
git restore --staged archivo.py

# Modificar el último commit (SOLO si no lo pusheaste)
git commit --amend

# Ver quién cambió cada línea (para entender código ajeno)
git blame archivo.py
```

---

## Resumen

Para el uso diario, necesitás un puñado de comandos:

| Comando | Para qué |
|---------|----------|
| `git status` | ¿Qué está pasando? |
| `git add` | Preparar cambios |
| `git commit` | Guardar en la historia |
| `git push` / `pull` | Sincronizar con servidor |
| `git branch` / `switch` | Trabajar en paralelo |
| `git merge` | Integrar cambios |

Todo lo demás lo vas a ir aprendiendo a medida que lo necesites.

Lo más importante: **no tengas miedo de experimentar**. Git está diseñado para que puedas volver atrás. Es muy difícil perder trabajo si hacés commits regularmente.

---

## Recursos para profundizar

- **[Pro Git Book](https://git-scm.com/book/es/v2)** - El libro oficial, gratuito, en español.
- **[Learn Git Branching](https://learngitbranching.js.org/?locale=es_AR)** - Tutorial interactivo para entender branches visualmente.
- **[Oh Shit, Git!?!](https://ohshitgit.com/es)** - Cómo salir de situaciones problemáticas.

---

*Computación II - 2026 - Bloque 0 Autónomo*
