# Guía GitHub - Nivel Intermedio

> 📚 [Volver al Índice de Documentación](../README.md)

## Índice
- [Branching](#branching)
- [Merging](#merging)
- [Pull Requests](#pull-requests)
- [Resolución de Conflictos](#resolución-de-conflictos)
- [Trabajo Colaborativo](#trabajo-colaborativo)

---

## Branching

### ¿Qué es un Branch?

Un branch (rama) es una línea independiente de desarrollo que permite trabajar en nuevas funcionalidades sin afectar el código principal.

```
main        ──●──●──●──●──●
                 \       ↑
feature           ●──●──●
```

### Comandos de branching

| Comando | Descripción |
|---------|-------------|
| `git branch` | Lista branches locales |
| `git branch -a` | Lista todos (incluidos remotos) |
| `git branch <nombre>` | Crea nuevo branch |
| `git checkout <branch>` | Cambia a branch |
| `git checkout -b <nombre>` | Crea y cambia a nuevo branch |
| `git switch <branch>` | Cambia a branch (Git 2.23+) |
| `git switch -c <nombre>` | Crea y cambia (Git 2.23+) |
| `git branch -d <nombre>` | Elimina branch |
| `git branch -D <nombre>` | Fuerza eliminación |
| `git branch -m <viejo> <nuevo>` | Renombra branch |

### Crear y usar branches

```bash
git checkout -b feature/nueva-funcionalidad
git add .
git commit -m "feat: añadir nueva funcionalidad"
git checkout main
git merge feature/nueva-funcionalidad
git branch -d feature/nueva-funcionalidad
```

### Nomenclatura de branches

| Prefijo | Uso |
|---------|-----|
| `feature/` | Nuevas funcionalidades |
| `bugfix/` | Correcciones de bugs |
| `hotfix/` | Correcciones urgentes |
| `release/` | Preparación de release |
| `docs/` | Documentación |

**Ejemplos:**
```
feature/navegacion-autonoma
bugfix/error-calculo-trayectoria
hotfix/crash-inicio
release/v1.0.0
```

### Ver diferencias entre branches

```bash
git diff main..feature/rama
git log main..feature/rama
git log --oneline --graph --all
```

> 💡 **Práctica:** Ejercita branching en [Ejercicios GitHub - Sección 1](Ejercicios_GitHub.md#sección-1-branching)

---

## Merging

### ¿Qué es Merge?

Merge combina los cambios de dos branches en uno solo.

### Tipos de merge

| Tipo | Descripción |
|------|-------------|
| Fast-forward | Avanza el puntero sin crear merge commit |
| Three-way merge | Crea un commit de fusión |
| Rebase | Reaplica commits sobre otra base |

### Fast-forward merge

```bash
git checkout main
git merge feature/rama
```

```
Antes:
main        ──●──●
                 \
feature           ●──●

Después (fast-forward):
main        ──●──●──●──●
feature          
```

### Three-way merge

```bash
git checkout main
git merge feature/rama
```

```
Antes:
main        ──●──●──●
                 \
feature           ●──●

Después:
main        ──●──●──●──●──merge
                 \        ↑
feature           ●──●──●
```

### Opciones de merge

| Opción | Descripción |
|--------|-------------|
| `--no-ff` | Fuerza crear merge commit |
| `--ff-only` | Solo fast-forward, falla si no es posible |
| `--squash` | Combina todos los commits en uno |

```bash
git merge --no-ff feature/rama
git merge --squash feature/rama
```

### Abortar merge

```bash
git merge --abort
```

> 💡 **Práctica:** Ejercita merging en [Ejercicios GitHub - Sección 2](Ejercicios_GitHub.md#sección-2-merging)

---

## Pull Requests

### ¿Qué es un Pull Request?

Un Pull Request (PR) es una propuesta para fusionar cambios de un branch a otro, permitiendo revisión y discusión antes del merge.

### Crear Pull Request

1. Push del branch a GitHub:
```bash
git push origin feature/mi-rama
```

2. En GitHub:
   - Ir al repositorio
   - Click en "Compare & pull request"
   - Seleccionar branches base y compare
   - Añadir título y descripción
   - Click en "Create pull request"

### Estructura de un buen PR

```markdown
## Descripción
Breve descripción de los cambios realizados.

## Cambios realizados
- Cambio 1
- Cambio 2
- Cambio 3

## Testing
- [ ] Tests unitarios pasan
- [ ] Probado en local

## Screenshots (si aplica)
Capturas de pantalla

## Issues relacionados
Closes #123
```

### Comandos relacionados

| Comando | Descripción |
|---------|-------------|
| `gh pr create` | Crea PR desde CLI |
| `gh pr list` | Lista PRs |
| `gh pr view <num>` | Ver PR |
| `gh pr checkout <num>` | Checkout de PR |
| `gh pr merge <num>` | Merge de PR |

### Plantilla de PR

Crear `.github/pull_request_template.md`:

```markdown
## Descripción

## Tipo de cambio
- [ ] Bug fix
- [ ] Nueva funcionalidad
- [ ] Breaking change
- [ ] Documentación

## Checklist
- [ ] Código sigue convenciones
- [ ] Tests añadidos/actualizados
- [ ] Documentación actualizada
```

> 💡 **Práctica:** Crea PRs en [Ejercicios GitHub - Sección 3](Ejercicios_GitHub.md#sección-3-pull-requests)

---

## Resolución de Conflictos

### ¿Qué es un conflicto?

Un conflicto ocurre cuando Git no puede fusionar automáticamente porque las mismas líneas fueron modificadas en diferentes branches.

### Identificar conflictos

```bash
git merge feature/rama
# Auto-merging archivo.txt
# CONFLICT (content): Merge conflict in archivo.txt
```

### Ver archivos en conflicto

```bash
git status
```

### Formato del conflicto

```
<<<<<<< HEAD
Código del branch actual
=======
Código del branch entrante
>>>>>>> feature/rama
```

### Resolver conflicto manualmente

1. Abrir archivo con conflicto
2. Editar, elegir o combinar código
3. Eliminar marcadores (`<<<<<<<`, `=======`, `>>>>>>>`)
4. Stagear archivo resuelto
5. Completar merge

```bash
git add archivo_resuelto.txt
git commit
```

### Herramientas de resolución

```bash
git mergetool
```

### Ver conflictos

```bash
git diff --name-only --diff-filter=U
git diff --check
```

### Estrategias de merge

| Estrategia | Uso |
|------------|-----|
| `recursive` | Por defecto |
| `ours` | Prefiere cambios locales |
| `theirs` | Prefiere cambios entrantes |

```bash
git merge -X ours feature/rama
git merge -X theirs feature/rama
```

> 💡 **Práctica:** Resuelve conflictos en [Ejercicios GitHub - Sección 4](Ejercicios_GitHub.md#sección-4-resolución-de-conflictos)

---

## Trabajo Colaborativo

### Fork vs Branch

| Aspecto | Fork | Branch |
|---------|------|--------|
| Ubicación | Repositorio propio | Mismo repositorio |
| Permisos | No necesarios | Necesarios |
| Uso típico | Proyectos open source | Proyectos internos |

### Flujo con Fork

```bash
# 1. Fork en GitHub (UI)

# 2. Clonar tu fork
git clone https://github.com/tu-usuario/proyecto.git
cd proyecto

# 3. Añadir upstream
git remote add upstream https://github.com/original/proyecto.git

# 4. Crear branch
git checkout -b feature/mi-contribucion

# 5. Hacer cambios y commit
git add .
git commit -m "feat: mi contribución"

# 6. Push a tu fork
git push origin feature/mi-contribucion

# 7. Crear Pull Request en GitHub

# 8. Mantener fork actualizado
git fetch upstream
git checkout main
git merge upstream/main
```

### Sincronizar fork

```bash
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

### Mantenerse actualizado

```bash
git pull origin main
git fetch --all
git branch --set-upstream-to=origin/main main
```

### Code Review

Al revisar código de otros:

1. **Ser constructivo**: Comentarios útiles, no críticos
2. **Ser específico**: Indicar líneas concretas
3. **Sugerir soluciones**: No solo problemas
4. **Verificar**:
   - Funcionalidad
   - Calidad de código
   - Tests
   - Documentación

### Issues

```bash
gh issue create --title "Bug en navegación" --body "Descripción..."
gh issue list
gh issue view 123
gh issue close 123
```

### Referencias en commits

```bash
git commit -m "fix: error de navegación. Closes #42"
git commit -m "feat: nueva función. Relates #100"
```

| Palabra clave | Efecto |
|---------------|--------|
| `Closes #123` | Cierra issue al merge |
| `Fixes #123` | Cierra issue al merge |
| `Resolves #123` | Cierra issue al merge |
| `Relates #123` | Solo referencia |

> 💡 **Práctica:** Trabajo colaborativo en [Ejercicios GitHub - Sección 5](Ejercicios_GitHub.md#sección-5-trabajo-colaborativo)

---

## Resumen de comandos intermedios

| Categoría | Comandos |
|-----------|----------|
| Branching | `git branch`, `git checkout -b`, `git switch` |
| Merging | `git merge`, `git merge --no-ff` |
| Remoto | `git fetch`, `git remote add` |
| PRs | `gh pr create`, `gh pr list` |
| Conflictos | Editar manualmente, `git add`, `git commit` |

---

## Siguientes pasos

Continúa con [Guía GitHub - Nivel Avanzado](Guia_GitHub_Avanzado.md) para aprender sobre rebase, cherry-pick y técnicas avanzadas.
