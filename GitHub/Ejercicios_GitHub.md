# Ejercicios Prácticos - GitHub

> 📚 [Volver al Índice de Documentación](../Índice%20Documentación.md)

Este documento contiene ejercicios prácticos para reforzar los conceptos de las guías de Git y GitHub.

---

## Nivel Principiante

### Sección 1: Introducción y Configuración

> 📖 **Teoría:** [Guía GitHub Principiante - Configuración](Guia_GitHub_Principiante.md#configuración-inicial)

#### Ejercicio 1.1: Instalar y configurar Git
```bash
git --version
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
git config --list
```

**Tarea:** Verifica que tu nombre y email estén correctos.

#### Ejercicio 1.2: Configurar SSH
```bash
ssh-keygen -t ed25519 -C "tu@email.com"
cat ~/.ssh/id_ed25519.pub
```

**Tarea:** Añade la clave pública a tu cuenta de GitHub y verifica:
```bash
ssh -T git@github.com
```

---

### Sección 2: Comandos Básicos

> 📖 **Teoría:** [Guía GitHub Principiante - Comandos Básicos](Guia_GitHub_Principiante.md#comandos-básicos)

#### Ejercicio 2.1: Crear primer repositorio
```bash
mkdir ~/practica_git
cd ~/practica_git
git init
git status
```

#### Ejercicio 2.2: Crear archivos y hacer commits
```bash
echo "# Mi Proyecto" > README.md
git status
git add README.md
git status
git commit -m "feat: crear README"
git log
```

#### Ejercicio 2.3: Flujo básico
```bash
echo "Primera línea de código" > main.py
git add main.py
git commit -m "feat: añadir main.py"

echo "Segunda línea de código" >> main.py
git diff
git add main.py
git commit -m "feat: añadir segunda línea"

git log --oneline
```

#### Ejercicio 2.4: Usar .gitignore
```bash
mkdir -p logs __pycache__
echo "log" > logs/app.log
echo "cache" > __pycache__/cache.pyc

cat > .gitignore << EOF
logs/
__pycache__/
*.pyc
*.log
.env
EOF

git add .gitignore
git commit -m "chore: añadir .gitignore"
git status
```

---

### Sección 3: Trabajo con Repositorios

> 📖 **Teoría:** [Guía GitHub Principiante - Repositorios](Guia_GitHub_Principiante.md#trabajo-con-repositorios)

#### Ejercicio 3.1: Crear repositorio en GitHub
1. Ve a github.com y crea un nuevo repositorio llamado `practica_git`
2. No inicialices con README

#### Ejercicio 3.2: Conectar y subir
```bash
cd ~/practica_git
git remote add origin git@github.com:tu-usuario/practica_git.git
git remote -v
git branch -M main
git push -u origin main
```

#### Ejercicio 3.3: Clonar repositorio
```bash
cd ~
git clone git@github.com:tu-usuario/practica_git.git practica_clone
cd practica_clone
ls -la
```

#### Ejercicio 3.4: Sincronizar cambios
```bash
cd ~/practica_git
echo "Nuevo contenido" >> README.md
git add README.md
git commit -m "docs: actualizar README"
git push origin main

cd ~/practica_clone
git pull origin main
cat README.md
```

---

### Sección 4: Flujo de Trabajo Básico

> 📖 **Teoría:** [Guía GitHub Principiante - Flujo de Trabajo](Guia_GitHub_Principiante.md#flujo-de-trabajo-básico)

#### Ejercicio 4.1: Simular día de trabajo
```bash
cd ~/practica_git

# Mañana: nueva funcionalidad
echo "def funcion1(): pass" > utils.py
git add utils.py
git commit -m "feat: añadir utils.py con funcion1"

# Mediodía: más código
echo "def funcion2(): pass" >> utils.py
git add utils.py
git commit -m "feat: añadir funcion2 a utils"

# Tarde: corrección
echo "# Utilidades del proyecto" > utils.py
echo "def funcion1(): return 1" >> utils.py
echo "def funcion2(): return 2" >> utils.py
git add utils.py
git commit -m "fix: corregir funciones en utils"

git log --oneline
```

#### Ejercicio 4.2: Ver historial
```bash
git log
git log --oneline
git log --oneline --graph
git log --since="1 day ago"
git log -p
```

#### Ejercicio 4.3: Deshacer cambios
```bash
echo "error" > test.txt
git add test.txt

# Sacar del staging
git restore --staged test.txt
git status

# Descartar cambios
git restore test.txt
git status
```

---

## Nivel Intermedio

### Sección 1: Branching

> 📖 **Teoría:** [Guía GitHub Intermedio - Branching](Guia_GitHub_Intermedio.md#branching)

#### Ejercicio 1.1: Crear y usar branches
```bash
cd ~/practica_git

git branch
git checkout -b feature/nueva-funcion
git branch

echo "def nueva_funcion(): pass" >> utils.py
git add utils.py
git commit -m "feat: añadir nueva_funcion"

git checkout main
cat utils.py  # Sin nueva_funcion

git checkout feature/nueva-funcion
cat utils.py  # Con nueva_funcion
```

#### Ejercicio 1.2: Múltiples branches
```bash
git checkout main
git checkout -b feature/otra-funcion

echo "def otra_funcion(): pass" >> utils.py
git add utils.py
git commit -m "feat: añadir otra_funcion"

git checkout main
git checkout -b docs/readme

echo "Documentación del proyecto" >> README.md
git add README.md
git commit -m "docs: añadir documentación"

git log --oneline --graph --all
```

#### Ejercicio 1.3: Listar y eliminar branches
```bash
git branch
git branch -a
git checkout main
git branch -d feature/nueva-funcion
git branch -D feature/otra-funcion  # Forzar si no mergeado
git branch
```

---

### Sección 2: Merging

> 📖 **Teoría:** [Guía GitHub Intermedio - Merging](Guia_GitHub_Intermedio.md#merging)

#### Ejercicio 2.1: Fast-forward merge
```bash
cd ~/practica_git
git checkout main
git checkout -b feature/simple

echo "simple" > simple.txt
git add simple.txt
git commit -m "feat: añadir archivo simple"

git checkout main
git merge feature/simple
git log --oneline --graph
```

#### Ejercicio 2.2: Three-way merge
```bash
git checkout main
git checkout -b feature/branch-a

echo "rama A" > conflicto.txt
git add conflicto.txt
git commit -m "feat: crear conflicto.txt en A"

git checkout main
git checkout -b feature/branch-b

echo "rama B" > conflicto.txt
git add conflicto.txt
git commit -m "feat: crear conflicto.txt en B"

git checkout main
git merge feature/branch-a
git merge feature/branch-b  # Habrá conflicto

git log --oneline --graph
```

#### Ejercicio 2.3: Merge con --no-ff
```bash
git checkout main
git checkout -b feature/importante

echo "importante" > importante.txt
git add importante.txt
git commit -m "feat: añadir archivo importante"

git checkout main
git merge --no-ff feature/importante
git log --oneline --graph
```

---

### Sección 3: Pull Requests

> 📖 **Teoría:** [Guía GitHub Intermedio - Pull Requests](Guia_GitHub_Intermedio.md#pull-requests)

#### Ejercicio 3.1: Crear PR desde CLI
```bash
cd ~/practica_git
git checkout main
git checkout -b feature/pr-test

echo "contenido para PR" > pr_test.txt
git add pr_test.txt
git commit -m "feat: añadir archivo para PR"

git push origin feature/pr-test
gh pr create --title "Añadir archivo de prueba" --body "Este PR añade un archivo de prueba."
```

#### Ejercicio 3.2: Gestionar PR
```bash
gh pr list
gh pr view 1
gh pr checkout 1
```

---

### Sección 4: Resolución de Conflictos

> 📖 **Teoría:** [Guía GitHub Intermedio - Conflictos](Guia_GitHub_Intermedio.md#resolución-de-conflictos)

#### Ejercicio 4.1: Crear conflicto intencional
```bash
cd ~/practica_git
git checkout main

git checkout -b feature/conflicto-a
echo "Versión A" > conflicto.txt
git add conflicto.txt
git commit -m "feat: versión A"

git checkout main
git checkout -b feature/conflicto-b
echo "Versión B" > conflicto.txt
git add conflicto.txt
git commit -m "feat: versión B"

git checkout main
git merge feature/conflicto-a
git merge feature/conflicto-b  # Conflicto!
```

#### Ejercicio 4.2: Resolver conflicto
```bash
cat conflicto.txt
```

Verás:
```
<<<<<<< HEAD
Versión A
=======
Versión B
>>>>>>> feature/conflicto-b
```

Resolver:
```bash
echo "Versión combinada A y B" > conflicto.txt
git add conflicto.txt
git commit -m "merge: resolver conflicto entre A y B"
```

---

### Sección 5: Trabajo Colaborativo

> 📖 **Teoría:** [Guía GitHub Intermedio - Colaboración](Guia_GitHub_Intermedio.md#trabajo-colaborativo)

#### Ejercicio 5.1: Simular fork (con dos repos locales)
```bash
cd ~
git clone git@github.com:tu-usuario/practica_git.git fork_practica
cd fork_practica

git remote add upstream git@github.com:tu-usuario/practica_git.git
git remote -v
```

#### Ejercicio 5.2: Sincronizar con upstream
```bash
git fetch upstream
git checkout main
git merge upstream/main
```

#### Ejercicio 5.3: Crear issue
```bash
gh issue create --title "Bug de ejemplo" --body "Descripción del bug"
gh issue list
```

---

## Nivel Avanzado

### Sección 1: Rebase

> 📖 **Teoría:** [Guía GitHub Avanzado - Rebase](Guia_GitHub_Avanzado.md#rebase)

#### Ejercicio 1.1: Rebase básico
```bash
cd ~/practica_git
git checkout main
git checkout -b feature/rebase-test

echo "commit 1" > rebase1.txt && git add . && git commit -m "feat: commit 1"
echo "commit 2" > rebase2.txt && git add . && git commit -m "feat: commit 2"
echo "commit 3" > rebase3.txt && git add . && git commit -m "feat: commit 3"

git checkout main
echo "nuevo en main" > main_nuevo.txt && git add . && git commit -m "feat: nuevo en main"

git checkout feature/rebase-test
git rebase main
git log --oneline --graph
```

#### Ejercicio 1.2: Rebase interactivo (squash)
```bash
git checkout -b feature/squash-test

echo "a" > a.txt && git add . && git commit -m "feat: a"
echo "b" > b.txt && git add . && git commit -m "feat: b"
echo "c" > c.txt && git add . && git commit -m "feat: c"

git log --oneline

# Squash últimos 3 commits
git rebase -i HEAD~3
```

En el editor, cambiar a:
```
pick abc1234 feat: a
squash def5678 feat: b
squash ghi9012 feat: c
```

Guardar y editar mensaje combinado.

---

### Sección 2: Cherry-pick

> 📖 **Teoría:** [Guía GitHub Avanzado - Cherry-pick](Guia_GitHub_Avanzado.md#cherry-pick)

#### Ejercicio 2.1: Cherry-pick básico
```bash
cd ~/practica_git
git checkout main
git checkout -b feature/cherry-source

echo "cherry 1" > cherry1.txt && git add . && git commit -m "feat: cherry 1"
echo "cherry 2" > cherry2.txt && git add . && git commit -m "feat: cherry 2"
echo "cherry 3" > cherry3.txt && git add . && git commit -m "feat: cherry 3"

git log --oneline
# Copia el hash del commit "cherry 2"

git checkout main
git checkout -b feature/cherry-dest

git cherry-pick <hash-cherry-2>
git log --oneline
ls
```

---

### Sección 3: Stash

> 📖 **Teoría:** [Guía GitHub Avanzado - Stash](Guia_GitHub_Avanzado.md#stash)

#### Ejercicio 3.1: Stash básico
```bash
cd ~/practica_git
git checkout main

echo "trabajo en progreso" > wip.txt
git status

git stash save "WIP: trabajo en progreso"
git status

git stash list
```

#### Ejercicio 3.2: Recuperar stash
```bash
git stash pop
cat wip.txt

# O aplicar sin eliminar
git stash apply
git stash list
git stash drop
```

#### Ejercicio 3.3: Stash para cambiar de branch
```bash
echo "cambio 1" >> wip.txt
git stash

git checkout -b otro-trabajo
echo "otro trabajo" > otro.txt
git add otro.txt
git commit -m "feat: otro trabajo"

git checkout main
git stash pop
```

---

### Sección 4: Hooks

> 📖 **Teoría:** [Guía GitHub Avanzado - Hooks](Guia_GitHub_Avanzado.md#hooks)

#### Ejercicio 4.1: Crear pre-commit hook
```bash
cd ~/practica_git

cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

echo "Verificando archivos Python..."

python_files=$(git diff --cached --name-only --diff-filter=ACM | grep '\.py$')

for file in $python_files; do
    if [ -f "$file" ]; then
        python3 -m py_compile "$file" 2>/dev/null
        if [ $? -ne 0 ]; then
            echo "Error de sintaxis en $file"
            exit 1
        fi
    fi
done

echo "Verificación OK"
exit 0
EOF

chmod +x .git/hooks/pre-commit
```

#### Ejercicio 4.2: Probar hook
```bash
echo "def bad(" > bad.py
git add bad.py
git commit -m "test: bad python"
# El hook debe fallar

rm bad.py
echo "def good(): pass" > good.py
git add good.py
git commit -m "test: good python"
# El hook debe pasar
```

#### Ejercicio 4.3: Hook commit-msg
```bash
cat > .git/hooks/commit-msg << 'EOF'
#!/bin/bash

commit_msg=$(cat "$1")

if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore):"; then
    echo "Error: Commit debe empezar con feat|fix|docs|style|refactor|test|chore:"
    echo "Ejemplo: feat: añadir nueva función"
    exit 1
fi

exit 0
EOF

chmod +x .git/hooks/commit-msg
```

---

### Sección 5: Git Avanzado

> 📖 **Teoría:** [Guía GitHub Avanzado - Git Avanzado](Guia_GitHub_Avanzado.md#git-avanzado)

#### Ejercicio 5.1: Usar reflog
```bash
git reflog
git log --oneline -5
```

#### Ejercicio 5.2: Recuperar commit "perdido"
```bash
git reset --hard HEAD~3
git log --oneline

git reflog
# Busca el commit anterior

git reset --hard <commit-hash>
git log --oneline
```

#### Ejercicio 5.3: Bisect para encontrar bug
```bash
git checkout main
git bisect start
git bisect bad HEAD
git bisect good <commit-bueno>
# Sigue las instrucciones
git bisect reset
```

#### Ejercicio 5.4: Blame
```bash
git blame utils.py
git blame -L 1,5 utils.py
```

---

### Sección 6: GitHub Actions

> 📖 **Teoría:** [Guía GitHub Avanzado - Actions](Guia_GitHub_Avanzado.md#github-actions)

#### Ejercicio 6.1: Crear workflow básico
```bash
cd ~/practica_git
mkdir -p .github/workflows

cat > .github/workflows/ci.yml << 'EOF'
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Verificar estructura
      run: |
        ls -la
        cat README.md
EOF

git add .github/
git commit -m "ci: añadir workflow de CI"
git push origin main
```

#### Ejercicio 6.2: Workflow con Python
```bash
cat > .github/workflows/python-ci.yml << 'EOF'
name: Python CI

on:
  push:
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
    
    - name: Check syntax
      run: |
        python -m py_compile *.py || true
EOF

git add .github/
git commit -m "ci: añadir workflow Python"
git push origin main
```

---

## Ejercicio Final Integrador

### Proyecto completo con Git

1. **Crear repositorio completo:**
```bash
mkdir ~/proyecto_final
cd ~/proyecto_final
git init

cat > README.md << 'EOF'
# Proyecto Final

## Descripción
Proyecto de demostración de Git.

## Instalación
```bash
pip install -r requirements.txt
```

## Uso
```bash
python main.py
```
EOF

cat > .gitignore << 'EOF'
__pycache__/
*.pyc
.env
venv/
EOF

cat > requirements.txt << 'EOF'
pytest
EOF

cat > main.py << 'EOF'
def main():
    print("Hola desde el proyecto final")

if __name__ == "__main__":
    main()
EOF

cat > test_main.py << 'EOF'
from main import main

def test_main():
    assert main() is None
EOF

git add .
git commit -m "feat: inicializar proyecto"
```

2. **Crear branches y desarrollo:**
```bash
git checkout -b feature/utils

cat > utils.py << 'EOF'
def helper():
    return "helper"
EOF

git add utils.py
git commit -m "feat: añadir utils"

git checkout main
git merge --no-ff feature/utils
```

3. **Configurar CI:**
```bash
mkdir -p .github/workflows

cat > .github/workflows/ci.yml << 'EOF'
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: actions/setup-python@v4
      with:
        python-version: '3.10'
    - run: pip install -r requirements.txt
    - run: pytest
EOF

git add .
git commit -m "ci: añadir tests y CI"
```

4. **Subir a GitHub:**
```bash
git remote add origin git@github.com:tu-usuario/proyecto_final.git
git push -u origin main
```

---

## Limpiar práctica

```bash
rm -rf ~/practica_git
rm -rf ~/practica_clone
rm -rf ~/fork_practica
rm -rf ~/proyecto_final
```
