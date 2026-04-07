# Guía Linux - Nivel Principiante

> 📚 [Volver al Índice de Documentación](../Índice%20Documentación.md)

## Índice
- [Introducción a la Terminal](#introducción-a-la-terminal)
- [Navegación de Directorios](#navegación-de-directorios)
- [Gestión de Archivos y Directorios](#gestión-de-archivos-y-directorios)
- [Permisos Básicos](#permisos-básicos)
- [Visualización de Contenido](#visualización-de-contenido)

---

## Introducción a la Terminal

La terminal es una interfaz de línea de comandos (CLI) que permite interactuar directamente con el sistema operativo mediante comandos de texto.

### Conceptos básicos
- **Shell**: Intérprete de comandos (por defecto en Ubuntu: `bash`)
- **Prompt**: Indica que el sistema está listo para recibir comandos
  - `$` - Usuario normal
  - `#` - Superusuario (root)

### Atajos útiles
| Atajo | Función |
|-------|---------|
| `Tab` | Autocompletar comandos y rutas |
| `Ctrl + C` | Cancelar comando en ejecución |
| `Ctrl + L` | Limpiar pantalla |
| `Ctrl + R` | Buscar en historial de comandos |
| `↑` / `↓` | Navegar por historial de comandos |

> 💡 **Práctica:** Ejercita estos conceptos en [Ejercicios Linux - Sección 1](Ejercicios_Linux.md#sección-1-introducción-a-la-terminal)

---

## Navegación de Directorios

### Comandos principales

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `pwd` | Muestra el directorio actual | `pwd` |
| `ls` | Lista contenido del directorio | `ls`, `ls -la` |
| `cd` | Cambia de directorio | `cd /home`, `cd ..` |

### Opciones comunes de `ls`
| Opción | Descripción |
|--------|-------------|
| `-l` | Formato largo (detalles) |
| `-a` | Muestra archivos ocultos |
| `-h` | Tamaños legibles (KB, MB) |
| `-R` | Lista recursivamente |

### Navegación rápida
| Comando | Acción |
|---------|--------|
| `cd` | Va al directorio home |
| `cd ~` | Va al directorio home |
| `cd -` | Va al directorio anterior |
| `cd ..` | Sube un nivel |
| `cd ../..` | Sube dos niveles |

### Rutas
- **Absolutas**: Comienzan desde `/` (raíz)
  - Ejemplo: `/home/usuario/documentos`
- **Relativas**: Desde el directorio actual
  - Ejemplo: `documentos/proyecto`

> 💡 **Práctica:** Ejercita navegación en [Ejercicios Linux - Sección 2](Ejercicios_Linux.md#sección-2-navegación-de-directorios)

---

## Gestión de Archivos y Directorios

### Crear y eliminar

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `mkdir` | Crea directorio | `mkdir carpeta` |
| `mkdir -p` | Crea directorios anidados | `mkdir -p a/b/c` |
| `touch` | Crea archivo vacío | `touch archivo.txt` |
| `rm` | Elimina archivo | `rm archivo.txt` |
| `rm -r` | Elimina directorio | `rm -r carpeta` |
| `rm -rf` | Elimina sin confirmación | `rm -rf carpeta` |
| `rmdir` | Elimina directorio vacío | `rmdir carpeta` |

### Copiar y mover

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `cp` | Copia archivo | `cp origen.txt destino.txt` |
| `cp -r` | Copia directorio | `cp -r origen/ destino/` |
| `mv` | Mueve/renombra | `mv archivo.txt nuevo.txt` |

### Consejos útiles
- Usa `rm` con cuidado, no hay papelera de reciclaje
- Verifica con `ls` antes de eliminar
- Usa `tab` para autocompletar rutas y evitar errores

> 💡 **Práctica:** Ejercita gestión de archivos en [Ejercicios Linux - Sección 3](Ejercicios_Linux.md#sección-3-gestión-de-archivos-y-directorios)

---

## Permisos Básicos

Linux usa un sistema de permisos para controlar el acceso a archivos y directorios.

### Tipos de permisos
| Símbolo | Permiso | Valor numérico |
|---------|---------|----------------|
| `r` | Lectura (read) | 4 |
| `w` | Escritura (write) | 2 |
| `x` | Ejecución (execute) | 1 |

### Categorías de usuarios
| Símbolo | Categoría |
|---------|-----------|
| `u` | Usuario (propietario) |
| `g` | Grupo |
| `o` | Otros |
| `a` | Todos (all) |

### Comandos de permisos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `chmod` | Cambia permisos | `chmod 755 script.sh` |
| `chmod +x` | Añade ejecución | `chmod +x script.sh` |
| `chown` | Cambia propietario | `chown usuario archivo` |
| `chgrp` | Cambia grupo | `chgrp grupo archivo` |

### Ejemplos de chmod
```bash
chmod 755 archivo    # rwxr-xr-x
chmod 644 archivo    # rw-r--r--
chmod 700 archivo    # rwx------
chmod u+x archivo    # Añade ejecución al usuario
chmod go-w archivo   # Quita escritura a grupo y otros
```

> 💡 **Práctica:** Ejercita permisos en [Ejercicios Linux - Sección 4](Ejercicios_Linux.md#sección-4-permisos-básicos)

---

## Visualización de Contenido

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `cat` | Muestra todo el contenido | `cat archivo.txt` |
| `less` | Visor paginado | `less archivo.txt` |
| `head` | Muestra primeras líneas | `head -n 10 archivo.txt` |
| `tail` | Muestra últimas líneas | `tail -n 10 archivo.txt` |
| `tail -f` | Sigue el archivo en tiempo real | `tail -f log.txt` |
| `wc` | Cuenta líneas, palabras, bytes | `wc archivo.txt` |

### Navegación en `less`
| Tecla | Acción |
|-------|--------|
| `Espacio` | Página siguiente |
| `b` | Página anterior |
| `/texto` | Buscar |
| `n` | Siguiente resultado |
| `q` | Salir |

> 💡 **Práctica:** Ejercita visualización en [Ejercicios Linux - Sección 5](Ejercicios_Linux.md#sección-5-visualización-de-contenido)

---

## Resumen de comandos esenciales

| Categoría | Comandos clave |
|-----------|----------------|
| Navegación | `pwd`, `ls`, `cd` |
| Archivos | `touch`, `cp`, `mv`, `rm`, `mkdir` |
| Permisos | `chmod`, `chown` |
| Visualización | `cat`, `less`, `head`, `tail` |

---

## Siguientes pasos

Continúa con [Guía Linux - Nivel Intermedio](Guia_Linux_Intermedio.md) para aprender sobre pipes, redirecciones y búsqueda de archivos.
