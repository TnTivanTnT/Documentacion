# Guía GitHub - Nivel Avanzado

> 📚 [Volver al Índice de Documentación](../README.md)

## Índice
- [Rebase](#rebase)
- [Cherry-pick](#cherry-pick)
- [Stash](#stash)
- [Hooks](#hooks)
- [Git Avanzado](#git-avanzado)
- [GitHub Actions](#github-actions)

---

## Rebase

### ¿Qué es Rebase?

Rebase reaplica commits de un branch sobre otro, creando un historial lineal.

### Rebase vs Merge

```
Merge:
main        ──●──●──●──●──merge
                 \        ↑
feature           ●──●──●

Rebase:
main        ──●──●──●──●'──●'──●'
                           (feature)
```

| Aspecto | Merge | Rebase |
|---------|-------|--------|
| Historial | Preserva bifurcación | Lineal |
| Commits | Añade merge commit | Reaplica commits |
| Seguridad | Más seguro | Puede causar problemas |
| Uso | Integrar features | Actualizar branch |

### Rebase básico

```bash
git checkout feature/rama
git rebase main
```

### Resolver conflictos en rebase

```bash
# 1. Resolver conflicto en archivo
# 2. Stagear
git add archivo_resuelto.txt

# 3. Continuar rebase
git rebase --continue

# O abortar
git rebase --abort
```

### Rebase interactivo

```bash
git rebase -i HEAD~3
git rebase -i <commit>
```

Editor muestra:
```
pick abc1234 Primer commit
pick def5678 Segundo commit
pick ghi9012 Tercer commit
```

### Comandos interactivos

| Comando | Acción |
|---------|--------|
| `pick` | Usar commit sin cambios |
| `reword` | Usar commit, editar mensaje |
| `edit` | Parar para amend |
| `squash` | Fusionar con anterior, combinar mensajes |
| `fixup` | Fusionar con anterior, descartar mensaje |
| `drop` | Eliminar commit |

### Ejemplo: Squash commits

```bash
git rebase -i HEAD~3
```

```
pick abc1234 feat: función A
squash def5678 fix: corrección A
squash ghi9012 feat: mejora A
```

Resultado: Los 3 commits se fusionan en uno.

### Rebase onto

```bash
git rebase --onto main feature-old feature-new
```

### PELIGRO: No hacer rebase en branches públicos

```bash
git push --force  # PELIGROSO
git push --force-with-lease  # Más seguro
```

> 💡 **Práctica:** Ejercita rebase en [Ejercicios GitHub - Sección 1](Ejercicios_GitHub.md#sección-1-rebase)

---

## Cherry-pick

### ¿Qué es Cherry-pick?

Cherry-pick aplica commits específicos de un branch a otro.

```
main        ──●──●──●──●'
                 \     ↑
feature           ●──●──● (cherry-pick solo este)
```

### Usar cherry-pick

```bash
git checkout main
git cherry-pick <commit-hash>
```

### Cherry-pick múltiples commits

```bash
git cherry-pick commit1 commit2 commit3
git cherry-pick commit1..commit3
```

### Opciones de cherry-pick

| Opción | Descripción |
|--------|-------------|
| `-n` o `--no-commit` | Aplica cambios sin commit |
| `-e` o `--edit` | Edita mensaje antes de commit |
| `-x` | Añade referencia al commit original |

```bash
git cherry-pick -n abc123
git cherry-pick -e abc123
```

### Resolver conflictos

```bash
# Resolver conflicto
git add archivo_resuelto.txt
git cherry-pick --continue

# O abortar
git cherry-pick --abort
```

### Casos de uso

- Aplicar hotfix de release a main
- Mover commit a branch equivocado
- Seleccionar cambios específicos

> 💡 **Práctica:** Ejercita cherry-pick en [Ejercicios GitHub - Sección 2](Ejercicios_GitHub.md#sección-2-cherry-pick)

---

## Stash

### ¿Qué es Stash?

Stash guarda cambios temporales sin hacer commit, permitiendo cambiar de branch.

### Guardar cambios

| Comando | Descripción |
|---------|-------------|
| `git stash` | Guarda cambios tracked |
| `git stash save "mensaje"` | Guarda con mensaje |
| `git stash -u` | Incluye archivos untracked |
| `git stash -a` | Incluye archivos ignorados |

### Listar y aplicar stashes

| Comando | Descripción |
|---------|-------------|
| `git stash list` | Lista todos los stashes |
| `git stash show` | Muestra cambios del último stash |
| `git stash show -p` | Muestra diff del stash |
| `git stash apply` | Aplica último stash (**lo mantiene en la lista**) |
| `git stash apply stash@{2}` | Aplica stash específico |
| `git stash pop` | Aplica y **elimina** el último stash |

> 💡 **Tip:** Se recomienda usar `git stash apply` si no estás seguro de si habrá conflictos. Si algo sale mal, tus cambios siguen guardados en el stash. Una vez verificado que todo está bien, puedes borrarlo con `git stash drop`.

### Flujo típico

```bash
# Trabajando en feature
git stash save "WIP: nueva función"

# Cambiar a otro branch
git checkout main
git pull

# Volver y recuperar cambios
git checkout feature/rama
git stash pop
```

### Stash parcial

```bash
git stash -p
```

Permite seleccionar qué hunks guardar.

> 💡 **Práctica:** Ejercita stash en [Ejercicios GitHub - Sección 3](Ejercicios_GitHub.md#sección-3-stash)

---

## Hooks

### ¿Qué son los Hooks?

Los hooks son scripts que se ejecutan automáticamente en puntos específicos del flujo de Git.

### Ubicación

```
.git/hooks/
├── pre-commit.sample
├── pre-push.sample
├── commit-msg.sample
└── ...
```

### Hooks del lado del cliente

| Hook | Cuándo se ejecuta |
|------|-------------------|
| `pre-commit` | Antes de commit |
| `prepare-commit-msg` | Antes de mostrar mensaje |
| `commit-msg` | Antes de finalizar commit |
| `post-commit` | Después de commit |
| `pre-push` | Antes de push |
| `pre-rebase` | Antes de rebase |

### Hooks del lado del servidor

| Hook | Cuándo se ejecuta |
|------|-------------------|
| `pre-receive` | Antes de recibir push |
| `update` | Antes de actualizar refs |
| `post-receive` | Después de recibir push |

### Ejemplo: pre-commit

Crear `.git/hooks/pre-commit`:

```bash
#!/bin/bash

echo "Ejecutando pre-commit..."

python_files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.py$')

if [ -n "$python_files" ]; then
    echo "Verificando formato Python..."
    for file in $python_files; do
        python3 -m py_compile "$file"
        if [ $? -ne 0 ]; then
            echo "Error de sintaxis en $file"
            exit 1
        fi
    done
fi

echo "pre-commit OK"
exit 0
```

```bash
chmod +x .git/hooks/pre-commit
```

### Ejemplo: commit-msg

Crear `.git/hooks/commit-msg`:

```bash
#!/bin/bash

commit_msg=$(cat "$1")

if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore):"; then
    echo "Error: El mensaje debe empezar con: feat|fix|docs|style|refactor|test|chore:"
    exit 1
fi

exit 0
```

### Husky (hooks compartidos)

```bash
npm install husky --save-dev
npx husky install
npx husky add .husky/pre-commit "npm test"
```

> 💡 **Práctica:** Crea hooks en [Ejercicios GitHub - Sección 4](Ejercicios_GitHub.md#sección-4-hooks)

---

## Git Avanzado

### Reflog

Historial de todas las referencias HEAD.

```bash
git reflog
git reflog show HEAD
git reset HEAD@{5}
```

### Recuperar commits perdidos

```bash
git reflog
git switch --detach <commit-hash>  # Explora el commit en modo 'detached'
git switch -c recovery-branch       # Crea una rama nueva para salvarlo
```

### Reset vs Revert

| Comando | Efecto |
|---------|--------|
| `git reset --soft HEAD~1` | Mueve HEAD, mantiene cambios staged |
| `git reset --mixed HEAD~1` | Mueve HEAD, mantiene cambios unstaged |
| `git reset --hard HEAD~1` | Mueve HEAD, elimina cambios |
| `git revert <commit>` | Crea nuevo commit que deshace cambios |

```bash
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
git revert abc123
```

### Bisect (búsqueda binaria de bugs)

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
# Git hace checkout a commit medio
# Verificar si bug existe
git bisect good  # o git bisect bad
# Repetir hasta encontrar
git bisect reset
```

## Seguridad: Commits Firmados

Para garantizar que nadie suplante tu identidad, puedes firmar tus commits usando tu clave SSH. GitHub mostrará un check verde de **"Verified"**.

```bash
# Configurar Git para usar tu clave SSH para firmar
git config --global commit.gpgsign true
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
```

> 💡 **Tip:** Esto es fundamental en proyectos con muchos colaboradores para asegurar la integridad de la autoría del código.

### Blame

```bash
git blame archivo.py
git blame -L 10,20 archivo.py
```

### Grep en repositorio

```bash
git grep "patron"
git grep -n "patron"
git grep "patron" -- "*.py"
```

### Clean

```bash
git clean -n  # Ver qué se eliminaría
git clean -f  # Eliminar archivos untracked
git clean -fd  # Archivos y directorios
git clean -fdx  # Incluye ignorados
```

### Filter-branch (PELIGROSO)

```bash
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD
```

### Git LFS (Large File Storage)

Git no está diseñado para manejar archivos binarios grandes (videos, mallas 3D pesadas, logs de sensores). **Git LFS** reemplaza estos archivos con punteros de texto, manteniendo el repositorio ligero.

> ⚠️ **Importante en Robótica:** Usa LFS para archivos `.bag`, modelos de Gazebo complejos (`.dae`, `.stl` de alta resolución) y modelos de entrenamiento de IA. ¡Evitarás que el `git clone` tarde horas!

```bash
git lfs install
git lfs track "*.bag"
git lfs track "*.dae"
git add .gitattributes
```

> 💡 **Práctica:** Ejercita comandos avanzados en [Ejercicios GitHub - Sección 5](Ejercicios_GitHub.md#sección-5-git-avanzado)

## Emergencias: Guía de Primeros Auxilios

| Situación | Solución |
| :--- | :--- |
| **"Escribí mal el mensaje del último commit"** | `git commit --amend -m "Nuevo mensaje"` |
| **"Hice el commit en la rama equivocada"** | `git reset --soft HEAD~1` -> `git switch rama_correcta` -> `git commit` |
| **"Borré una rama por error y tenía trabajo"** | `git reflog` -> buscar hash -> `git switch -c rama_recuperada <hash>` |
| **"Metí un archivo que no debía (ej. .env)"** | `git rm --cached <archivo>` -> añadir al `.gitignore` -> commit |
| **"Quiero deshacer todo mi trabajo local"** | `git reset --hard origin/main` (¡Cuidado, borra todo!) |

## Git Worktrees: Desarrollo en Paralelo

Los Worktrees te permiten tener múltiples ramas abiertas en diferentes carpetas al mismo tiempo, compartiendo el mismo repositorio `.git`.

```bash
# Crea una carpeta nueva para trabajar en otra rama simultáneamente
git worktree add ../hotfix-branch feature/hotfix
# Listar worktrees activos
git worktree list
# Eliminar worktree al terminar
git worktree remove ../hotfix-branch
```

> 💡 **Caso de uso:** Estás en medio de un desarrollo de 3 horas y te piden corregir un error urgente en `main`. En lugar de hacer `stash`, crea un worktree, arregla el bug en otra carpeta, súbelo y borra la carpeta. ¡Sin tocar tu rama actual!

---

## Git Submodules: Gestionar Dependencias Externas

En robótica, solemos usar librerías de terceros (drivers, sensores). Los submódulos permiten incluir esos repositorios dentro del tuyo.

```bash
# Añadir un submódulo
git submodule add https://github.com/usuario/sensor_driver.git
# Inicializar submódulos al clonar por primera vez
git clone --recursive <url_tu_repo>
# Actualizar submódulos
git submodule update --remote --merge
```

---

## GitHub Actions

### ¿Qué son GitHub Actions?

GitHub Actions permite automatizar flujos de trabajo directamente en GitHub.

### Estructura de workflow

Crear `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    
    - name: Run tests
      run: |
        python -m pytest
```

### Eventos trigger

| Evento | Descripción |
|--------|-------------|
| `push` | Al hacer push |
| `pull_request` | Al crear/actualizar PR |
| `schedule` | Programado (cron) |
| `workflow_dispatch` | Manual |
| `release` | Al crear release |

### Ejemplo para ROS2

```yaml
name: ROS2 Build

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-22.04
    container:
      image: ros:humble
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Build
      run: |
        cd ${{ github.workspace }}
        colcon build
    
    - name: Test
      run: |
        cd ${{ github.workspace }}
        colcon test
```

### Variables y secrets

```yaml
env:
  MY_VAR: value

steps:
  - name: Use secret
    run: echo ${{ secrets.MY_SECRET }}
```

> 💡 **Práctica:** Configura Actions en [Ejercicios GitHub - Sección 6](Ejercicios_GitHub.md#sección-6-github-actions)

---

## Resumen de comandos avanzados

| Categoría | Comandos |
|-----------|----------|
| Rebase | `git rebase`, `git rebase -i` |
| Cherry-pick | `git cherry-pick` |
| Stash | `git stash`, `git stash pop` |
| Recuperación | `git reflog`, `git reset` |
| Depuración | `git bisect`, `git blame` |
| Automatización | Hooks, GitHub Actions |

---

## Recursos adicionales

- [Guía GitHub - Nivel Principiante](Guia_GitHub_Principiante.md)
- [Guía GitHub - Nivel Intermedio](Guia_GitHub_Intermedio.md)
- [Documentación oficial de Git](https://git-scm.com/doc)
- [GitHub Docs](https://docs.github.com)
