# Filesystem Linux - Autoevaluación

Responde estas preguntas para verificar tu comprensión. Las respuestas están al final.

---

## Parte 1: Inodos y estructura (8 preguntas)

### Pregunta 1
¿Qué información NO almacena un inodo?

a) Permisos del archivo
b) Nombre del archivo
c) Tamaño del archivo
d) Punteros a bloques de datos

### Pregunta 2
¿Qué representa el campo "nlinks" en un inodo?

a) Número de usuarios que acceden al archivo
b) Número de enlaces duros al archivo
c) Número de enlaces simbólicos
d) Número de bloques ocupados

### Pregunta 3
¿Qué significa que dos archivos tengan el mismo número de inodo?

a) Tienen el mismo nombre
b) Son el mismo archivo (enlaces duros)
c) Son copias idénticas
d) Están en el mismo directorio

### Pregunta 4
¿Qué timestamp se actualiza al leer el contenido de un archivo?

a) mtime
b) ctime
c) atime
d) btime

### Pregunta 5
¿Qué timestamp se actualiza al cambiar los permisos de un archivo?

a) mtime
b) ctime
c) atime
d) Ninguno

### Pregunta 6
¿Qué indica el símbolo `l` al inicio de los permisos en `ls -l`?

a) Archivo bloqueado
b) Enlace simbólico
c) Archivo grande
d) Archivo de log

### Pregunta 7
¿Qué directorio contiene archivos de dispositivos como discos y terminales?

a) /bin
b) /dev
c) /etc
d) /sys

### Pregunta 8
¿Qué comando muestra información detallada del inodo de un archivo?

a) `ls -l`
b) `file`
c) `stat`
d) `cat`

---

## Parte 2: Permisos (8 preguntas)

### Pregunta 9
¿Qué permisos representa el octal 755?

a) rwx------
b) rwxr-xr-x
c) rw-r--r--
d) rwxrwxrwx

### Pregunta 10
¿Qué significa el permiso "x" en un directorio?

a) Poder ver el contenido
b) Poder crear archivos
c) Poder acceder/atravesar el directorio
d) Poder ejecutar scripts dentro

### Pregunta 11
Un archivo tiene permisos `rw-r-----`. ¿Quién puede leerlo?

a) Solo el propietario
b) El propietario y el grupo
c) Todos los usuarios
d) Nadie

### Pregunta 12
¿Qué comando cambia el propietario de un archivo?

a) chmod
b) chown
c) chgrp
d) chperm

### Pregunta 13
¿Qué hace `chmod u+x script.sh`?

a) Quita el permiso de ejecución al usuario
b) Agrega el permiso de ejecución al usuario
c) Agrega el permiso de ejecución a todos
d) Cambia el propietario

### Pregunta 14
¿Qué es el SUID bit y qué número octal lo representa?

a) Permite a cualquiera borrar el archivo (1000)
b) Ejecuta con permisos del propietario (4000)
c) Hace el archivo inmutable (2000)
d) Oculta el archivo (0000)

### Pregunta 15
¿Para qué sirve el sticky bit en /tmp?

a) Hace los archivos temporales
b) Solo el propietario puede borrar sus archivos
c) Los archivos se borran automáticamente
d) Comprime los archivos

### Pregunta 16
Si el umask es 022, ¿qué permisos tendrá un archivo nuevo?

a) 755
b) 644
c) 777
d) 022

---

## Parte 3: Enlaces (6 preguntas)

### Pregunta 17
¿Cuál es la diferencia principal entre enlace duro y simbólico?

a) El enlace duro es más rápido
b) El enlace duro comparte el mismo inodo, el simbólico tiene uno propio
c) El enlace simbólico ocupa más espacio
d) El enlace duro solo funciona en ext4

### Pregunta 18
¿Qué sucede si borras el archivo original de un enlace DURO?

a) El enlace queda roto
b) Los datos se pierden
c) Los datos persisten, el enlace sigue funcionando
d) Se crea una copia automática

### Pregunta 19
¿Qué sucede si borras el archivo original de un enlace SIMBÓLICO?

a) El enlace queda roto
b) Los datos persisten en el enlace
c) El enlace se borra automáticamente
d) Se redirige a otro archivo

### Pregunta 20
¿Pueden los enlaces duros cruzar sistemas de archivos (particiones)?

a) Sí
b) No
c) Solo si son del mismo tipo (ext4)
d) Solo con permisos de root

### Pregunta 21
¿Pueden los enlaces simbólicos apuntar a directorios?

a) No, solo a archivos
b) Sí
c) Solo con la opción -d
d) Solo en ciertos filesystems

### Pregunta 22
¿Qué comando crea un enlace simbólico?

a) `ln archivo enlace`
b) `ln -s archivo enlace`
c) `link -s archivo enlace`
d) `symlink archivo enlace`

---

## Parte 4: Python y filesystem (6 preguntas)

### Pregunta 23
¿Qué módulo de Python es recomendado para trabajar con rutas modernamente?

a) os.path
b) sys.path
c) pathlib
d) filepath

### Pregunta 24
¿Qué retorna `os.stat("archivo.txt").st_size`?

a) El tamaño en KB
b) El tamaño en bytes
c) El número de bloques
d) El número de líneas

### Pregunta 25
¿Cómo verificas si un path es un enlace simbólico en Python?

a) `os.path.issymlink(path)`
b) `os.path.islink(path)`
c) `os.islink(path)`
d) `path.is_symbolic()`

### Pregunta 26
¿Qué función de shutil copia un archivo preservando metadatos?

a) `shutil.copy()`
b) `shutil.copy2()`
c) `shutil.copyfile()`
d) `shutil.copystat()`

### Pregunta 27
¿Cómo lees el destino de un enlace simbólico?

a) `os.path.realpath()`
b) `os.readlink()`
c) `os.path.target()`
d) `os.link.read()`

### Pregunta 28
Con pathlib, ¿cómo obtienes la extensión de un archivo?

```python
p = Path("/home/user/archivo.txt")
```

a) `p.extension`
b) `p.suffix`
c) `p.ext`
d) `p.filetype`

---

## Respuestas

<details>
<summary>Click para ver respuestas</summary>

### Parte 1: Inodos
1. **b** - El nombre se almacena en el directorio, no en el inodo
2. **b** - Número de enlaces duros
3. **b** - Son el mismo archivo (enlaces duros)
4. **c** - atime (access time)
5. **b** - ctime (change time de metadatos)
6. **b** - Enlace simbólico
7. **b** - /dev
8. **c** - stat

### Parte 2: Permisos
9. **b** - rwxr-xr-x
10. **c** - Poder acceder/atravesar el directorio
11. **b** - El propietario y el grupo
12. **b** - chown
13. **b** - Agrega permiso de ejecución al usuario
14. **b** - Ejecuta con permisos del propietario (4000)
15. **b** - Solo el propietario puede borrar sus archivos
16. **b** - 644 (666 - 022)

### Parte 3: Enlaces
17. **b** - El enlace duro comparte el mismo inodo
18. **c** - Los datos persisten, el enlace sigue funcionando
19. **a** - El enlace queda roto
20. **b** - No (mismo filesystem requerido)
21. **b** - Sí
22. **b** - `ln -s archivo enlace`

### Parte 4: Python
23. **c** - pathlib
24. **b** - El tamaño en bytes
25. **b** - `os.path.islink(path)`
26. **b** - `shutil.copy2()`
27. **b** - `os.readlink()`
28. **b** - `p.suffix`

### Puntuación
- 25-28: Excelente comprensión
- 20-24: Buen nivel
- 15-19: Necesita repasar algunos temas
- <15: Revisar el material nuevamente

</details>

---

*Computación II - 2026 - Bloque 0 Autónomo*
