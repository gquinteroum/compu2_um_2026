# Git y GitHub - Ejercicios

## Cómo abordar estos ejercicios

Estos ejercicios están pensados para que practiques Git de forma progresiva. No los hagas todos de corrido como si fuera un trámite: tomate el tiempo de entender qué está pasando en cada paso.

Git es una herramienta que vas a usar todos los días de tu vida profesional. Vale la pena invertir tiempo ahora en entenderla bien.

**Tip importante:** Después de cada comando, usá `git status` y `git log --oneline` para ver qué cambió. Desarrollar el hábito de verificar el estado constantemente te va a ahorrar muchos dolores de cabeza.

---

## Parte 1: Los fundamentos

### Tu primer repositorio

Vamos a crear un repositorio desde cero para entender el flujo básico.

Abrí una terminal y ejecutá:

```bash
mkdir ejercicio-git
cd ejercicio-git
git init
```

Acabás de crear un repositorio Git vacío. Podés verificarlo con `ls -la`: vas a ver una carpeta `.git` que contiene toda la magia de Git.

Ahora creá un archivo `README.md` con tu nombre y legajo. Podés usar el editor que prefieras o directamente:

```bash
echo "# Mi primer repositorio" > README.md
echo "Nombre: [tu nombre]" >> README.md
echo "Legajo: [tu legajo]" >> README.md
```

Ahora viene lo importante. Ejecutá `git status`. Vas a ver algo como:

```
Untracked files:
  README.md
```

Git ve el archivo pero no lo está "siguiendo" todavía. Necesitás agregarlo al staging:

```bash
git add README.md
git status
```

Ahora el archivo aparece en verde bajo "Changes to be committed". Está listo para ser parte de un commit:

```bash
git commit -m "Agregar README con datos personales"
```

Felicitaciones, hiciste tu primer commit. Verificá con `git log --oneline`.

---

### El ritmo de trabajo

Este ejercicio te va a meter en el ritmo típico de trabajo con Git.

Creá un archivo `main.py` con un simple "Hello World":

```python
print("Hello World")
```

Antes de hacer nada, ejecutá `git status`. ¿Qué ves? El archivo está como "untracked". Agregalo y commitealo:

```bash
git add main.py
git commit -m "Agregar programa inicial"
```

Ahora modificá `main.py` para que imprima tu nombre:

```python
print("Hola, soy [tu nombre]")
```

Antes de commitear, probá `git diff`. Este comando te muestra exactamente qué cambió. Vas a ver las líneas eliminadas en rojo y las agregadas en verde. Es muy útil para revisar tus cambios antes de commitear.

Ahora completá el ciclo:

```bash
git add main.py
git commit -m "Personalizar mensaje de saludo"
```

Ejecutá `git log --oneline`. Deberías ver dos commits.

**Pregunta para pensar:** ¿Qué pasaría si hubieras hecho un solo commit con ambos cambios? Funcionaría igual, pero la historia sería menos clara. Commits pequeños y frecuentes hacen más fácil entender qué pasó y cuándo.

---

### Explorando la historia

Ahora que tenés algunos commits, exploremos la historia.

```bash
git log
```

Este comando te muestra el historial completo: hash del commit, autor, fecha, y mensaje. Es mucha información. Para algo más conciso:

```bash
git log --oneline
```

Cada commit tiene un identificador único (el hash). Podés ver los detalles de cualquier commit con:

```bash
git show [hash]
```

Reemplazá `[hash]` con los primeros caracteres del hash de alguno de tus commits.

Hay otro comando muy útil: `git blame`. Probalo:

```bash
git blame main.py
```

Este comando te muestra quién escribió cada línea y en qué commit. En un proyecto personal parece inútil (fuiste vos todo), pero en equipos es invaluable para entender por qué algo está como está.

---

## Parte 2: Branches y colaboración

### Trabajando con branches

Hasta ahora trabajaste en una sola línea de desarrollo (`main`). Ahora vas a experimentar con branches.

Imaginá que querés agregar una función pero no estás seguro de si va a funcionar bien. No querés romper lo que ya tenés. Solución: un branch.

```bash
git branch feature-suma
git switch feature-suma
```

Verificá con `git branch` que estás en el nuevo branch (el asterisco marca dónde estás).

Ahora editá `main.py` y agregá una función:

```python
def suma(a, b):
    return a + b

print("Hola, soy [tu nombre]")
print(f"2 + 3 = {suma(2, 3)}")
```

Commiteá el cambio:

```bash
git add main.py
git commit -m "Agregar función suma"
```

Ahora viene lo interesante. Volvé a `main`:

```bash
git switch main
```

Abrí `main.py`. ¿Dónde está la función suma? No está. El branch `main` sigue exactamente como lo dejaste. Los cambios están "aislados" en `feature-suma`.

Si decidís que la función está bien, la integrás:

```bash
git merge feature-suma
```

Ahora `main.py` en `main` tiene la función. Ejecutá `git log --oneline --graph --all` para ver visualmente cómo se unieron las ramas.

---

### Resolviendo conflictos

Los conflictos son inevitables cuando trabajás en equipo (o cuando vos mismo trabajás en múltiples branches). Vamos a provocar uno a propósito para que aprendas a resolverlo.

Creá un branch:

```bash
git switch -c version-a
```

Editá la primera línea de `README.md` para que diga algo diferente. Commiteá.

Volvé a `main`:

```bash
git switch main
```

Editá la **misma línea** de `README.md` con texto diferente. Commiteá.

Ahora intentá mergear:

```bash
git merge version-a
```

Git te va a decir que hay un conflicto. Si abrís `README.md`, vas a ver algo como:

```
<<<<<<< HEAD
Lo que pusiste en main
=======
Lo que pusiste en version-a
>>>>>>> version-a
```

Tu trabajo es editar el archivo para dejarlo como querés que quede (eliminando los marcadores `<<<<`, `====`, `>>>>`). Puede ser una versión, la otra, o una combinación.

Después de editar:

```bash
git add README.md
git commit
```

Git abre el editor para el mensaje de merge. Podés dejarlo como está.

**Reflexión:** Los conflictos no son errores, son situaciones normales. Lo importante es saber manejarlos.

---

### Ignorando archivos

No todos los archivos deberían ir al repositorio. El entorno virtual (`venv`), archivos de configuración local (`.env`), cache de Python (`__pycache__`) son ejemplos de cosas que no querés compartir.

Probá esto:

```bash
python -m venv venv
echo "SECRET=mi_clave_secreta" > .env
mkdir __pycache__ && touch __pycache__/test.pyc
```

Ejecutá `git status`. Git ve todos estos archivos y quiere que los agregues. Pero no deberías.

Creá un archivo `.gitignore`:

```
venv/
.env
__pycache__/
```

Ahora `git status` de nuevo. Esos archivos desaparecieron de la lista. Git los ignora.

**Importante:** `.gitignore` solo afecta archivos que no están siendo trackeados. Si ya commiteaste un archivo y después lo agregás a `.gitignore`, Git lo sigue trackeando. Tendrías que borrarlo del repositorio primero.

---

## Parte 3: GitHub y tu repositorio del curso

### Subir a GitHub

Hasta ahora todo fue local. Ahora vamos a sincronizar con GitHub.

Andá a GitHub y creá un repositorio nuevo. **Importante:** no marques "Initialize with README" ni ninguna otra opción. Queremos un repositorio vacío.

GitHub te va a mostrar comandos para conectar. Serán algo como:

```bash
git remote add origin https://github.com/tu-usuario/ejercicio-git.git
git push -u origin main
```

El `-u` significa "upstream": establece que `main` local trackea `origin/main` remoto. Después de esto, simplemente `git push` y `git pull` van a saber de dónde y hacia dónde ir.

Refrescá la página de GitHub. Deberías ver tus archivos y commits.

---

### Simulando colaboración

Cuando trabajás con otros, sus cambios llegan al repositorio remoto. Vamos a simular eso.

Desde la interfaz web de GitHub, editá `README.md` directamente (hay un botón de lápiz). Agregá una línea y guardá con un commit.

Ahora en tu terminal local, intentá hacer un cambio y push:

```bash
echo "Otra línea" >> README.md
git add README.md
git commit -m "Agregar línea localmente"
git push
```

El push va a fallar. Git te dice que el remoto tiene cambios que vos no tenés. Necesitás integrarlos primero:

```bash
git pull
```

Si no hay conflictos, Git hace el merge automáticamente. Ahora sí podés:

```bash
git push
```

Esta danza de pull-antes-de-push es algo que vas a hacer constantemente cuando trabajes en equipo.

---

## El ejercicio más importante: tu repositorio del curso

Este no es un ejercicio más. Es la creación del repositorio que vas a usar **todo el año** para entregar ejercicios y trabajos prácticos. Tomátelo en serio.

### Por qué un repositorio propio

En lugar de entregar cada ejercicio por email o campus virtual, vas a tener un único repositorio donde va todo tu trabajo del curso. Esto tiene varias ventajas:

- Aprendés a usar Git de forma real, no como ejercicio aislado
- Tenés toda tu historia de la materia en un solo lugar
- Podemos ver tu progreso a lo largo del año
- Es exactamente como funciona en la industria

### Cómo crearlo

**1. Creá el repositorio en GitHub**

- Nombre: `computacion2-2026`
- Visibilidad: **Privado** (es tu trabajo, no queremos que otros lo copien)
- **NO** inicialices con README

**2. Creá la estructura local**

```bash
mkdir computacion2-2026
cd computacion2-2026
git init
```

Creá las carpetas que vas a necesitar:

```bash
mkdir -p bloque_0/{git,argparse,filesystem,python_avanzado}
mkdir -p tp1 tp2
```

**3. Creá el README**

El README es tu carta de presentación. Creá un archivo `README.md`:

```markdown
# Computación II - 2026

## Datos del estudiante

- **Nombre:** [Tu nombre completo]
- **Legajo:** [Tu legajo]
- **Email:** [Tu email]
- **GitHub:** [@tu-usuario](https://github.com/tu-usuario)

## Estructura

- `bloque_0/` - Ejercicios del bloque autónomo
- `tp1/` - Trabajo Práctico 1
- `tp2/` - Trabajo Práctico 2

## Estado

| Componente | Estado |
|------------|--------|
| Bloque 0   | Pendiente |
| TP1        | Pendiente |
| TP2        | Pendiente |
```

**4. Creá el .gitignore**

Copiá este contenido en un archivo `.gitignore`:

```
# Python
venv/
__pycache__/
*.pyc
.env

# IDEs
.idea/
.vscode/

# OS
.DS_Store
```

**5. Primer commit y push**

```bash
git add .
git commit -m "Estructura inicial del repositorio del curso"
git remote add origin https://github.com/TU-USUARIO/computacion2-2026.git
git push -u origin main
```

**6. Agregá al docente como colaborador**

En GitHub: Settings → Collaborators → Add people → `gquintero-um`

Esto es necesario para que podamos ver tu repositorio privado y corregir tus entregas.

### Cómo usarlo durante el curso

A medida que hagas ejercicios, los vas agregando:

```bash
# Copiá tu ejercicio a la carpeta correspondiente
cp mi_script.py bloque_0/argparse/

# Agregá y commiteá
git add bloque_0/argparse/mi_script.py
git commit -m "Ejercicio 2.1 de argparse"
git push
```

Mantené el hábito de hacer commits frecuentes con mensajes descriptivos. Al final del año vas a tener una hermosa historia de todo lo que aprendiste.

---

## Ejercicios extra (para los que quieren más)

Si terminaste todo lo anterior y querés profundizar, estos ejercicios cubren temas más avanzados:

### Stash: guardar trabajo temporalmente

Situación: estás a mitad de un cambio y surge algo urgente. No querés commitear trabajo incompleto.

```bash
# Guardar cambios sin commitear
git stash

# Hacer lo urgente en otro branch
git switch otra-cosa
# ... trabajo urgente ...
git switch -

# Recuperar tu trabajo
git stash pop
```

### Rebase interactivo

Permite editar la historia **local** (antes de pushear). Creá 3 commits con mensajes malos y después:

```bash
git rebase -i HEAD~3
```

Podés cambiar mensajes (`reword`), combinar commits (`squash`), o reordenarlos.

**Advertencia:** NUNCA hagas rebase de commits que ya pusheaste. Reescribir historia compartida causa problemas serios.

---

## Entrega

Para la **clase 3 (07/04)** tenés que tener:

1. Tu repositorio `computacion2-2026` creado y configurado
2. El docente agregado como colaborador
3. La estructura de carpetas correcta

Sin esto no vas a poder entregar nada durante el curso.

---

*Computación II - 2026 - Bloque 0 Autónomo*
