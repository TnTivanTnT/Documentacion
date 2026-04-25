# Guía Linux - Nivel Principiante

> 📚 [Volver al Índice de Documentación](../README.md)

## Índice
- [Introducción a la Terminal](#introducción-a-la-terminal)
- [El Poder del Historial](#el-poder-del-historial)
- [Superusuario: Sudo](#superusuario-sudo)
- [Navegación de Directorios](#navegación-de-directorios)
- [Gestión de Archivos y Directorios](#gestión-de-archivos-y-directorios)
- [Instalación de Programas](#instalación-de-programas-básico)
- [Permisos Básicos y Grupos](#permisos-básicos-y-grupos)
- [Visualización de Contenido](#visualización-de-contenido)
- [Edición de Texto (Nano)](#edición-de-texto-en-la-terminal-nano)
- [Personalización: Aliases](#personalización-aliases)

---

## Introducción a la Terminal

La terminal es una interfaz de línea de comandos (CLI) que permite interactuar directamente con el sistema operativo.

### Atajos útiles (Survival Kit)
| Atajo | Función |
|-------|---------|
| `Tab` | **Autocompletar** (Comandos y rutas) |
| `Ctrl + C` | Cancelar comando en ejecución |
| `Ctrl + L` | Limpiar pantalla |
| `Ctrl + D` | Cerrar terminal / Salir |
| `↑` / `↓` | Navegar por historial |

---

## El Poder del Historial

La terminal recuerda tus comandos previos.

| Acción | Comando / Atajo |
| :--- | :--- |
| Ver historial | `history` |
| **Buscar comando** | **`Ctrl + R`** |
| Ejecutar último | `!!` |
| Ejecutar último con sudo | `sudo !!` |

---

## Superusuario: Sudo

Tareas como instalar programas requieren permisos de administrador (**root**).

| Comando | Descripción |
|---------|-------------|
| `sudo <comando>` | Ejecuta como superusuario |
| `sudo -i` | Abre sesión como root |
| `exit` | Cierra la sesión root |

---

## Navegación de Directorios

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `pwd` | Directorio actual | `pwd` |
| `ls` | Lista archivos | `ls -la` |
| `cd <ruta>` | Cambia directorio | `cd /etc` |
| `cd ..` | Sube un nivel | `cd ..` |
| `cd` | Vuelve al Home | `cd` |

---

## Gestión de Archivos y Directorios

### Comandos básicos
| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `mkdir` | Crea directorio | `mkdir carpeta` |
| `touch` | Crea archivo vacío | `touch nota.txt` |
| `rm` | Elimina archivo | `rm nota.txt` |
| `rm -rf` | Elimina carpeta | `rm -rf carpeta/` |
| `cp` | Copia archivo | `cp orig.txt dest.txt` |
| `mv` | Mueve o renombra | `mv viejo.txt nuevo.txt` |

### Comodines (Wildcards)
- **`*`**: Representa "cualquier cosa". `rm *.log` borra todos los logs.

---

## Instalación de Programas (Básico)

Usamos `apt` para gestionar software. Requiere `sudo`.

| Acción | Comando |
| :--- | :--- |
| Actualizar lista | `sudo apt update` |
| Instalar programa | `sudo apt install nombre` |

---

## Permisos Básicos y Grupos

### Permisos
- **r** (4): Lectura
- **w** (2): Escritura
- **x** (1): Ejecución

```bash
chmod +x script.sh  # Hace que un archivo sea ejecutable
```

### Grupos
Permiten organizar usuarios con permisos similares.
```bash
groups                        # Ver tus grupos
sudo usermod -aG grupo $USER  # Añadirte a un grupo
```

> 💡 **Nota:** En robótica, suele ser necesario añadirse al grupo `dialout` para leer puertos USB de sensores.

---

## Visualización de Contenido

| Comando | Descripción |
|---------|-------------|
| `cat` | Muestra todo el texto |
| `less` | Visor paginado (pulsa `q` para salir) |
| `tail -f` | Sigue el archivo en tiempo real (útil para logs) |

---

## Edición de Texto (Nano)

`nano` es el editor estándar dentro de la terminal.

| Acción | Atajo |
| :--- | :--- |
| Guardar | `Ctrl + O` |
| Salir | `Ctrl + X` |

---

## Personalización: Aliases

Apodos para comandos largos. Se guardan en `~/.bashrc`.

```bash
alias ll='ls -la'
```

---

## Siguientes pasos

Continúa con [Guía Linux - Nivel Intermedio](Guia_Linux_Intermedio.md).
