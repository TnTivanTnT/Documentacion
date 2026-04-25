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
| `git switch <branch>` | Cambia a branch |
| `git switch -c <nombre>` | Crea y cambia a nuevo branch |
| `git branch -d <nombre>` | Elimina branch |
| `git branch -D <nombre>` | Fuerza eliminación |
| `git branch -m <viejo> <nuevo>` | Renombra branch |

### Crear y usar branches

```bash
git switch -c feature/nueva-funcionalidad  # Crea y cambia a la rama
git add .
git commit -m "feat: añadir nueva funcionalidad"
git switch main                            # Vuelve a la rama principal
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

---

## Merging

### ¿Qué es Merge?

Merge combina los cambios de dos branches en uno solo.

### Tipos de merge

| Tipo | Descripción |
|------|-------------|
| Fast-forward | Avanza el puntero sin crear merge commit |
| Three-way merge | Crea un commit de fusión |
| Squash and Merge| Combina todos los commits en uno solo (Limpia el historial) |

### Fast-forward merge

```bash
git switch main
git merge feature/rama
```

### Three-way merge

```bash
git switch main
git merge feature/rama
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

### Draft Pull Requests (Borradores)

Si estás trabajando en una funcionalidad larga pero quieres que se vea el progreso sin que se revise todavía:
1. Al crear el PR en GitHub, haz clic en la flecha junto a "Create Pull Request".
2. Selecciona **"Create Draft Pull Request"**.

### Branch Protection (Protección de Ramas)

Para evitar desastres, el administrador debe configurar reglas en GitHub (`Settings -> Branches -> Add rule`):
- **Require a pull request before merging:** Nadie puede hacer `push` directo a `main`.
- **Require status checks to pass:** El código debe compilar antes del merge.

---

## Resolución de Conflictos

### ¿Qué es un conflicto?

Ocurre cuando Git no puede fusionar automáticamente porque las mismas líneas fueron modificadas en diferentes ramas.

> 💡 **Tip:** Usa **Visual Studio Code** para resolver conflictos visualmente con botones. ¡Es mucho más seguro!

### Formato del conflicto

```
<<<<<<< HEAD
Código del branch actual
=======
Código del branch entrante
>>>>>>> feature/rama
```

---

## Flujo de Trabajo y Buenas Prácticas

### Notas sobre el flujo de trabajo

Para trabajar de forma organizada, es recomendable seguir estas normas:
1.  **Main es sagrado:** Evitar trabajar directamente sobre `main`. Usar ramas `feature/`.
2.  **Pull antes de Push:** Hacer un `git pull` antes de empezar y antes de subir cambios para evitar conflictos.
3.  **PRs pequeños:** Es mejor hacer varios Pull Requests pequeños que uno gigante.
4.  **Mensajes claros:** Usar el estándar de *Conventional Commits*.

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
# 3. Añadir upstream
git remote add upstream https://github.com/original/proyecto.git
# 4. Sincronizar fork
git fetch upstream
git switch main
git merge upstream/main
git push origin main
```

### Code Review (Revisión de Código)

Al revisar código, esta es una **Lista de Verificación** útil:
1.  **Funcionalidad:** ¿El código hace lo que dice que hace?
2.  **Legibilidad:** ¿Es fácil de entender?
3.  **Tests:** ¿Hay tests? ¿Pasan todos?
4.  **Seguridad:** ¿Hay secretos o claves expuestas?

---

## Estándar: Conventional Commits

Para que el historial sea profesional y legible, se recomienda este formato: `<tipo>: <descripción>`

| Tipo | Descripción |
| :--- | :--- |
| **feat** | Una nueva funcionalidad |
| **fix** | Una corrección de un error |
| **docs** | Cambios en la documentación |
| **style** | Cambios de formato (espacios, comas) |
| **refactor**| Mejora de código que no cambia el comportamiento |
| **test** | Añadir o corregir pruebas |
| **chore** | Tareas de mantenimiento |

---

## Organización y Mantenimiento

### Issues

```bash
gh issue create --title "Bug en navegación" --body "Descripción..."
gh issue list
```

### Planificación: Milestones y Projects

1.  **Milestones (Hitos):** Agrupan issues y PRs hacia un objetivo específico.
2.  **GitHub Projects:** Un tablero Kanban para visualizar el progreso de las tareas.

### Gobernanza: CODEOWNERS

Puedes automatizar quién revisa qué creando el archivo `.github/CODEOWNERS`:

```text
/Ros2/      @usuario-experto
README.md   @usuario1 @usuario2
```

### Mantenimiento: Limpieza de Ramas

```bash
# Borra ramas locales que ya no existen en el servidor
git remote prune origin
# Borra ramas locales que ya fueron mergeadas
git branch --merged | grep -v "\*" | xargs -n 1 git branch -d
```

---

## Git Tags (Etiquetas)

### Estándar: Versionado Semántico (SemVer)

Formato `X.Y.Z`:
-   **X (Mayor):** Cambios que rompen compatibilidad.
-   **Y (Menor):** Nuevas funciones.
-   **Z (Parche):** Correcciones.

| Comando | Descripción |
|---------|-------------|
| `git tag` | Lista todas las etiquetas |
| `git tag -a v1.0.0 -m "msj"` | Crea etiqueta anotada |
| `git push origin --tags` | Sube todos los tags |

---

## Resumen de comandos intermedios

| Categoría | Comandos |
|-----------|----------|
| Branching | `git switch`, `git switch -c`, `git branch -d` |
| Merging | `git merge`, `git merge --squash` |
| PRs | `gh pr create`, `gh pr list` |
| Etiquetas | `git tag`, `git push --tags` |
| Limpieza | `git remote prune origin` |

---

## Siguientes pasos

Continúa con [Guía GitHub - Nivel Avanzado](Guia_GitHub_Avanzado.md).
