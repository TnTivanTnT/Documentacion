# Guía GitHub - Nivel Intermedio

> 📚 [Volver al Índice de Documentación](../README.md)

## Índice
- [Branching](#branching)
- [Merging](#merging)
- [Pull Requests (Web)](#pull-requests)
- [Resolución de Conflictos](#resolución-de-conflictos)
- [Flujo de Trabajo y Buenas Prácticas](#flujo-de-trabajo-y-buenas-prácticas)

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

Un Pull Request (PR) es una propuesta para fusionar cambios de un branch a otro, permitiendo revisión y discusión antes del merge final. Aunque Git se usa por terminal, los PRs se gestionan mejor desde la **Interfaz Web de GitHub**.

### Flujo recomendado para un PR (Web)

1.  **Sube tu rama a GitHub:**
    ```bash
    git push origin feature/mi-rama
    ```
2.  **Abre GitHub en tu navegador:**
    - Ve a tu repositorio.
    - Verás un aviso: "feature/mi-rama had recent pushes...". Haz clic en **"Compare & pull request"**.
    - Si no aparece, ve a la pestaña **"Pull Requests"** y haz clic en **"New pull request"**, selecciona tu rama.
3.  **Configura el PR:**
    - Escribe un título claro (siguiendo *Conventional Commits*).
    - Añade una descripción de tus cambios.
    - (Opcional) Asigna **Reviewers** o **Labels**.
4.  **Draft Pull Requests (Borradores):**
    - Si el trabajo no está terminado, haz clic en la flecha junto a "Create Pull Request" y elige **"Create Draft Pull Request"**. Esto avisa de que estás trabajando pero no está listo para merge.

### Branch Protection (Protección de Ramas)

En la web (`Settings -> Branches -> Add rule`), es vital configurar la rama `main` para:
- **Require a pull request before merging:** Evita que se suban cambios a `main` por error sin revisión.
- **Require status checks to pass:** Asegura que el código compile antes de fusionarlo.

---

## Resolución de Conflictos

### ¿Qué es un conflicto?

Ocurre cuando Git no puede fusionar automáticamente porque las mismas líneas fueron modificadas en diferentes ramas.

> 💡 **Tip:** Usa **Visual Studio Code** para resolver conflictos visualmente con botones. ¡Es mucho más seguro que hacerlo a mano!

---

## Flujo de Trabajo y Buenas Prácticas

### Notas sobre el flujo de trabajo

Para trabajar de forma organizada, es recomendable seguir estas normas:
1.  **Main es sagrado:** Evitar trabajar directamente sobre `main`. Usa ramas `feature/`.
2.  **Pull antes de Push:** Haz un `git pull` antes de empezar y antes de subir cambios.
3.  **Mensajes claros:** Usa el estándar de *Conventional Commits*.

### Fork vs Branch

| Aspecto | Fork | Branch |
|---------|------|--------|
| Ubicación | Repositorio propio | Mismo repositorio |
| Permisos | No necesarios | Necesarios |
| Uso típico | Proyectos públicos | Proyectos internos |

### Flujo con Fork (Web + Terminal)

1.  **Fork:** Haz clic en el botón **"Fork"** en la esquina superior derecha del repositorio original en GitHub.
2.  **Clonar:** Copia la URL de **tu fork** y clónalo:
    ```bash
    git clone https://github.com/tu-usuario/proyecto.git
    ```
3.  **Sincronizar (Terminal):** Añade el repositorio original como `upstream`:
    ```bash
    git remote add upstream https://github.com/original/proyecto.git
    git fetch upstream
    git switch main
    git merge upstream/main
    ```

---

## Organización y Mantenimiento (Web)

### Issues y Planificación

GitHub ofrece herramientas visuales para organizar el trabajo que son mejores que la terminal para esta tarea:

1.  **Issues:** Se usan para reportar errores o proponer funciones. En la web, ve a la pestaña **"Issues" -> "New Issue"**.
2.  **Milestones (Hitos):** Agrupan issues hacia un objetivo (ej: "Lanzamiento v1.0").
3.  **GitHub Projects:** Un tablero Kanban (To Do, In Progress, Done). Se gestiona arrastrando tarjetas en la web.

> 💡 **Nota avanzada:** Si prefieres la terminal para esto, existe la herramienta **`gh` (GitHub CLI)** que permite hacer `gh pr create` o `gh issue list`, pero se recomienda la web para una mejor visualización.

### Mantenimiento: Limpieza de Ramas

```bash
# Borra ramas locales que ya no existen en el servidor
git remote prune origin
# Borra ramas locales que ya fueron fusionadas
git branch --merged | grep -v "\*" | xargs -n 1 git branch -d
```

---

## Git Tags (Etiquetas)

### Estándar: Versionado Semántico (SemVer)

Formato `X.Y.Z`:
-   **X (Mayor):** Cambios que rompen compatibilidad.
-   **Y (Menor):** Nuevas funciones.
-   **Z (Parche):** Correcciones menores.

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
| Remoto | `git fetch`, `git push origin rama` |
| Etiquetas | `git tag`, `git push --tags` |
| Limpieza | `git remote prune origin` |

---

## Siguientes pasos

Continúa con [Guía GitHub - Nivel Avanzado](Guia_GitHub_Avanzado.md).
