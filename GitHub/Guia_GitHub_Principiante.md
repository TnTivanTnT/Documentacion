# Guía GitHub - Nivel Principiante

> 📚 [Volver al Índice de Documentación](../README.md)

## Índice
- [Introducción a Git y GitHub](#introducción-a-git-y-github)
- [Configuración Inicial](#configuración-inicial)
- [Comandos Básicos](#comandos-básicos)
- [Trabajo con Repositorios](#trabajo-con-repositorios)
- [Flujo de Trabajo Básico](#flujo-de-trabajo-básico)

---

## Introducción a Git y GitHub

### ¿Qué es Git?

Git es un sistema de control de versiones distribuido que permite rastrear cambios en archivos y coordinar trabajo entre múltiples personas.

### ¿Qué es GitHub?

GitHub es una plataforma de alojamiento de repositorios Git que añade funcionalidades colaborativas como pull requests, issues, y acciones automatizadas.

### Diferencias entre Git y GitHub

| Aspecto | Git | GitHub |
|---------|-----|--------|
| Tipo | Herramienta local | Plataforma web |
| Función | Control de versiones | Colaboración |
| Ubicación | Tu computadora | Servidores de GitHub |
| Costo | Gratuito | Gratuito/Pago |

### Conceptos fundamentales

| Concepto | Descripción |
|----------|-------------|
| Repositorio | Carpeta con todos los archivos y su historial |
| Commit | Instantánea de cambios en un momento dado |
| Branch | Línea independiente de desarrollo |
| Merge | Combinar cambios de diferentes branches |
| Remote | Repositorio en servidor (GitHub) |
| Clone | Copia local de un repositorio remoto |

> 💡 **Práctica:** Ejercita conceptos en [Ejercicios GitHub - Sección 1](Ejercicios_GitHub.md#sección-1-introducción-y-configuración)

---

## Configuración Inicial

### Instalar Git

```bash
sudo apt update
sudo apt install git
git --version
```

### Configurar identidad

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
```

### Configuraciones recomendadas (Paz mental)

```bash
# Establecer 'main' como rama por defecto para nuevos repositorios
git config --global init.defaultBranch main

# Activar autocorrección de comandos (ayuda si escribes 'git statsu')
git config --global help.autocorrect 1

# Colores automáticos en la terminal
git config --global color.ui auto

# Aliases (Atajos para trabajar más rápido)
git config --global alias.st status
git config --global alias.cm "commit -m"
git config --global alias.br branch
git config --global alias.sw switch
git config --global alias.lg "log --oneline --graph --all"
```

> 💡 **Tip:** Con estos aliases, ahora puedes escribir `git st` en lugar de `git status`, o `git lg` para ver un historial gráfico y compacto de todas las ramas. ¡Ahorrarás mucho tiempo!

### Configurar editor por defecto

```bash
git config --global core.editor nano
git config --global core.editor code --wait
```

### Ver configuración

```bash
git config --list
git config user.name
git config user.email
```

### Configurar credenciales HTTPS

```bash
git config --global credential.helper store
```

### Configurar SSH (recomendado)

```bash
ssh-keygen -t ed25519 -C "tu@email.com"
cat ~/.ssh/id_ed25519.pub
```

Copia la clave pública y añádela en GitHub → Settings → SSH Keys → New SSH key

### Verificar conexión SSH

```bash
ssh -T git@github.com
```

> 💡 **Práctica:** Configura tu entorno en [Ejercicios GitHub - Sección 1](Ejercicios_GitHub.md#sección-1-introducción-y-configuración)

---

## Comandos Básicos

### Inicializar repositorio

| Comando | Descripción |
|---------|-------------|
| `git init` | Inicializa repositorio en carpeta actual |
| `git clone <url>` | Clona repositorio remoto |

### Ver estado

| Comando | Descripción |
|---------|-------------|
| `git status` | Estado actual del repositorio |
| `git status -s` | Estado compacto |
| `git log` | Historial de commits |
| `git log --oneline` | Historial compacto |
| `git log --graph` | Historial con gráfico |

### Añadir cambios

| Comando | Descripción |
|---------|-------------|
| `git add <archivo>` | Añade archivo específico |
| `git add .` | Añade todos los archivos |
| `git add -A` | Añade todos (incluido borrados) |
| `git add *.txt` | Añade por patrón |

### Crear commit

| Comando | Descripción |
|---------|-------------|
| `git commit -m "mensaje"` | Commit con mensaje |
| `git commit` | Abre editor para mensaje |
| `git commit -am "mensaje"` | Añade y commitea (solo trackeados) |

### Ver diferencias

| Comando | Descripción |
|---------|-------------|
| `git diff` | Diferencias no stageadas |
| `git diff --staged` | Diferencias en staging |
| `git diff HEAD` | Diferencias con último commit |
| `git diff <commit1> <commit2>` | Diferencias entre commits |

### Eliminar y renombrar

| Comando | Descripción |
|---------|-------------|
| `git rm <archivo>` | Elimina y stagea eliminación |
| `git rm --cached <archivo>` | Deja de trackear (mantiene archivo) |
| `git mv <viejo> <nuevo>` | Renombra y stagea cambio |

> 💡 **Práctica:** Ejercita comandos básicos en [Ejercicios GitHub - Sección 2](Ejercicios_GitHub.md#sección-2-comandos-básicos)

---

## Trabajo con Repositorios

### Clonar repositorio

```bash
git clone https://github.com/usuario/repo.git
git clone git@github.com:usuario/repo.git
git clone https://github.com/usuario/repo.git mi_carpeta
```

### Sincronizar con remoto

| Comando | Descripción |
|---------|-------------|
| `git remote -v` | Lista remotos |
| `git remote add origin <url>` | Añade remoto |
| `git fetch` | Descarga cambios sin fusionar |
| `git pull` | Descarga y fusiona cambios |
| `git push` | Sube commits al remoto |
| `git push -u origin main` | Sube y establece upstream |

### Inicial repositorio y conectar a GitHub

```bash
mkdir mi_proyecto
cd mi_proyecto
git init
git add .
git commit -m "Primer commit"
git branch -M main
git remote add origin https://github.com/usuario/mi_proyecto.git
git push -u origin main
```

### .gitignore

Archivo que especifica qué ignorar:

```gitignore
# Archivos de Python
__pycache__/
*.py[cod]
*.pyo
.env
venv/

# IDEs
.vscode/
.idea/
*.swp

# ROS2
build/
install/
log/

# Sistema
.DS_Store
*.bak
*~
```

### .gitattributes: Gestión de compatibilidad

Si se trabaja entre **Windows** y **Linux**, los saltos de línea pueden corromper el código. Crea un archivo `.gitattributes` en la raíz para forzar un estándar:

```text
# Forzar saltos de línea de Linux (LF) en archivos de texto
* text=auto eol=lf

# Asegurar que los scripts siempre tengan saltos de línea de Linux
*.sh text eol=lf
*.py text eol=lf

# Los archivos binarios no se tocan
*.dae binary
*.stl binary
```

> 💡 **Tip:** Esto evitará que Git marque archivos enteros como "modificados" solo porque alguien los abrió en Windows.

### README.md

```markdown
# Título del Proyecto

Descripción breve del proyecto.

## Instalación

```bash
comandos de instalación
```

## Uso

```bash
comandos de uso
```

## Licencia

MIT
```

> 💡 **Práctica:** Trabaja con repositorios en [Ejercicios GitHub - Sección 3](Ejercicios_GitHub.md#sección-3-trabajo-con-repositorios)

---

## Flujo de Trabajo Básico

### Ciclo de vida de archivos

```
┌───────────┐    git add    ┌─────────┐   git commit   ┌────────┐
│ Untracked ├──────────────>│ Staged  ├───────────────>│Committed│
│  Modified │               │ (Index) │                │        │
└───────────┘               └─────────┘                └────────┘
     ▲                             │                        │
     │      git restore <file>     │   git restore <file>   │
     └─────────────────────────────┴────────────────────────┘
            (Descarta cambios no guardados en el Index o HEAD)
```

### Flujo básico diario

```bash
git status
git add .
git commit -m "Descripción del cambio"
git pull origin main
git push origin main
```

> 💡 **Tip:** El comando `git commit -am "mensaje"` combina el `add` y el `commit` en un solo paso, pero **solo funciona para archivos que ya estaban siendo trackeados**. Si creas un archivo nuevo, DEBES usar `git add` primero.

### Ver historial

```bash
git log
git log --oneline
git log --oneline --graph --all
git log -5
git log --author="nombre"
git log --since="2 weeks ago"
```

### Deshacer cambios

| Comando | Descripción |
|---------|-------------|
| `git switch <rama>` | Cambiar de rama (Recomendado) |
| `git restore <archivo>` | Descartar cambios en archivo (Recomendado) |
| `git restore --staged <archivo>` | Saca del staging |
| `git checkout <rama>` | Cambiar de rama (Antiguo) |
| `git checkout -- <archivo>` | Descartar cambios (Antiguo) |
| `git commit --amend` | Modifica último commit |

> 💡 **Nota de compatibilidad:** Históricamente, `git checkout` se usaba tanto para cambiar de rama como para restaurar archivos. Para evitar confusiones, las versiones modernas de Git introdujeron `git switch` y `git restore`. Ambos métodos funcionan, pero los nuevos son más claros y seguros. ¡Usa los que prefieras!

### Buenas prácticas de commits

**Formato de mensaje:**
```
<tipo>: <descripción corta>

<cuerpo opcional>
```

**Tipos comunes:**

| Tipo | Uso |
|------|-----|
| feat | Nueva funcionalidad |
| fix | Corrección de bug |
| docs | Documentación |
| style | Formato (no afecta código) |
| refactor | Refactorización |
| test | Tests |
| chore | Mantenimiento |

**Ejemplos:**
```bash
git commit -m "feat: añadir función de navegación autónoma"
git commit -m "fix: corregir error en cálculo de trayectoria"
git commit -m "docs: actualizar README con instrucciones"
```

> 💡 **Práctica:** Practica el flujo en [Ejercicios GitHub - Sección 4](Ejercicios_GitHub.md#sección-4-flujo-de-trabajo-básico)

---

## Resumen de comandos esenciales

| Categoría | Comandos |
|-----------|----------|
| Configuración | `git config`, `git init` |
| Estado | `git status`, `git log` |
| Cambios | `git add`, `git commit`, `git diff` |
| Remoto | `git clone`, `git pull`, `git push` |
| Deshacer | `git restore`, `git reset` |

---

## Siguientes pasos

Continúa con [Guía GitHub - Nivel Intermedio](Guia_GitHub_Intermedio.md) para aprender sobre branching, merging y pull requests.
