# Guía Markdown - Nivel Principiante

> 📚 [Volver al Índice de Documentación](../README.md)

## Índice
- [¿Qué es Markdown?](#qué-es-markdown)
- [Sintaxis Básica](#sintaxis-básica)
- [Encabezados](#encabezados)
- [Párrafos y Saltos de Línea](#párrafos-y-saltos-de-línea)
- [Énfasis y Formato](#énfasis-y-formato)
- [Listas](#listas)
- [Enlaces](#enlaces)
- [Imágenes](#imágenes)
- [Comentarios](#comentarios)

---

## ¿Qué es Markdown?

Markdown es un lenguaje de marcado ligero creado por John Gruber en 2004. Su objetivo es permitir escribir contenido de forma legible y fácil de publicar.

### Ventajas

| Ventaja | Descripción |
|---------|-------------|
| Simple | Fácil de aprender y usar |
| Portable | Archivos de texto plano |
| Legible | El código fuente es comprensible |
| Flexible | Se convierte a HTML, PDF, etc. |
| Universal | Usado en GitHub, Reddit, Obsidian, etc. |

### Dónde se usa Markdown

- Documentación de proyectos (README.md)
- Notas y documentación personal (Obsidian)
- Blogs y sitios web
- Foros y comunidades (Reddit, Stack Overflow)
- Wikis
- Issues y Pull Requests en GitHub

> 💡 **Práctica:** Ejercita Markdown en [Ejercicios Markdown - Sección 1](Ejercicios_Markdown.md#sección-1-sintaxis-básica)

---

## Sintaxis Básica

### Archivos Markdown

Los archivos Markdown usan la extensión `.md` o `.markdown`.

```markdown
archivo.md
README.md
DOCUMENTACION.md
```

### Caracteres especiales

| Carácter | Nombre | Uso |
|----------|--------|-----|
| `#` | Hash/Almohadilla | Encabezados |
| `*` | Asterisco | Énfasis, listas |
| `_` | Guion bajo | Énfasis |
| `-` | Guion | Listas, separadores |
| `>` | Mayor que | Citas |
| `` ` `` | Backtick | Código inline |
| `[` `]` | Corchetes | Enlaces, imágenes |
| `(` `)` | Paréntesis | URLs |
| `|` | Pipe | Tablas |

### Escapar caracteres

Usa `\` para mostrar caracteres especiales literalmente:

```markdown
\*Esto no está en cursiva\*
\[Esto no es un enlace\]
```

Resultado: *Esto no está en cursiva* [Esto no es un enlace]

> 💡 **Práctica:** Ejercita caracteres en [Ejercicios Markdown - Sección 1](Ejercicios_Markdown.md#sección-1-sintaxis-básica)

---

## Encabezados

### Sintaxis

Usa `#` para crear encabezados. El número de `#` indica el nivel.

```markdown
# Encabezado 1
## Encabezado 2
### Encabezado 3
#### Encabezado 4
##### Encabezado 5
###### Encabezado 6
```

### Sintaxis alternativa (H1 y H2)

```markdown
Encabezado 1
===========

Encabezado 2
-----------
```

### Ejemplo completo

```markdown
# Título Principal

## Sección 1

### Subsección 1.1

#### Detalle

Contenido aquí...

##### Sub-detalle

Más contenido...

###### Nivel más profundo
```

### Consejos

- Usa solo un H1 por documento
- Mantén una jerarquía lógica (no saltes de H1 a H4)
- Los encabezados generan anclas automáticas para enlaces

> 💡 **Práctica:** Ejercita encabezados en [Ejercicios Markdown - Sección 2](Ejercicios_Markdown.md#sección-2-encabezados)

---

## Párrafos y Saltos de Línea

### Párrafos

Separa párrafos con una línea en blanco:

```markdown
Este es el primer párrafo.

Este es el segundo párrafo.
```

### Saltos de línea

Para un salto de línea sin nuevo párrafo:

```markdown
Línea 1  
Línea 2
```

Nota: Dos espacios al final de la línea, o usa `<br>`

### Ejemplo

```markdown
Párrafo 1: Lorem ipsum dolor sit amet.

Párrafo 2: Consectetur adipiscing elit.
Sed do eiusmod tempor incididunt.

Línea separada con dos espacios al final.
```

> 💡 **Práctica:** Ejercita párrafos en [Ejercicios Markdown - Sección 3](Ejercicios_Markdown.md#sección-3-párrafos-y-líneas)

---

## Énfasis y Formato

### Negrita

```markdown
**texto en negrita**
__texto en negrita__
```

Resultado: **texto en negrita**

### Cursiva

```markdown
*texto en cursiva*
_texto en cursiva_
```

Resultado: *texto en cursiva*

### Negrita y cursiva

```markdown
***negrita y cursiva***
___negrita y cursiva___
**_negrita y cursiva_**
```

Resultado: ***negrita y cursiva***

### Tachado (GitHub Flavored Markdown)

```markdown
~~texto tachado~~
```

Resultado: ~~texto tachado~~

### Combinaciones

```markdown
**negrita** y *cursiva*
***todo enfatizado***
**negrita con _cursiva dentro_**
```

Resultado: **negrita** y *cursiva* con ***todo enfatizado*** y **negrita con _cursiva dentro_**

> 💡 **Práctica:** Ejercita énfasis en [Ejercicios Markdown - Sección 4](Ejercicios_Markdown.md#sección-4-énfasis-y-formato)

---

## Listas

### Listas no ordenadas

```markdown
- Elemento 1
- Elemento 2
- Elemento 3

* Elemento A
* Elemento B
* Elemento C

+ Elemento X
+ Elemento Y
```

Resultado:
- Elemento 1
- Elemento 2
- Elemento 3

### Listas ordenadas

```markdown
1. Primero
2. Segundo
3. Tercero
```

Resultado:
1. Primero
2. Segundo
3. Tercero

### Listas anidadas

Usa indentación (2 o 4 espacios):

```markdown
1. Elemento principal
   - Sub-elemento 1
   - Sub-elemento 2
2. Otro elemento principal
   1. Sub-ordenado 1
   2. Sub-ordenado 2
```

Resultado:
1. Elemento principal
   - Sub-elemento 1
   - Sub-elemento 2
2. Otro elemento principal
   1. Sub-ordenado 1
   2. Sub-ordenado 2

### Iniciar lista con número específico

```markdown
5. Quinto elemento
6. Sexto elemento
```

### Listas de tareas (GitHub Flavored)

```markdown
- [x] Tarea completada
- [ ] Tarea pendiente
- [ ] Otra tarea
```

Resultado:
- [x] Tarea completada
- [ ] Tarea pendiente
- [ ] Otra tarea

> 💡 **Práctica:** Ejercita listas en [Ejercicios Markdown - Sección 5](Ejercicios_Markdown.md#sección-5-listas)

---

## Enlaces

### Enlaces inline

```markdown
[texto del enlace](https://www.ejemplo.com)
```

Resultado: [texto del enlace](https://www.ejemplo.com)

### Enlaces con título

```markdown
[texto del enlace](https://www.ejemplo.com "Título del enlace")
```

### Enlaces de referencia

```markdown
[texto del enlace][ref]

[ref]: https://www.ejemplo.com
```

O con título:
```markdown
[texto del enlace][ref]

[ref]: https://www.ejemplo.com "Título opcional"
```

### URLs directas

```markdown
<https://www.ejemplo.com>
<email@ejemplo.com>
```

Resultado: <https://www.ejemplo.com>

### Enlaces a secciones (anclas)

```markdown
[Ir a Encabezados](#encabezados)
[Ir a sección](#sección-con-guiones)
```

### Enlaces en Obsidian

```markdown
[[Nombre de nota]]
[[Nombre de nota|Texto alternativo]]
[[Nombre de nota#Sección]]
[[Nombre de nota#Sección|Texto]]
```

> 💡 **Práctica:** Ejercita enlaces en [Ejercicios Markdown - Sección 6](Ejercicios_Markdown.md#sección-6-enlaces)

---

## Imágenes

### Sintaxis básica

```markdown
![Texto alternativo](ruta/a/imagen.png)
```

### Imagen con título

```markdown
![Texto alternativo](ruta/a/imagen.png "Título de imagen")
```

### Imagen con URL

```markdown
![Logo](https://ejemplo.com/logo.png)
```

### Imagen como enlace

```markdown
[![Imagen](imagen.png)](https://ejemplo.com)
```

### Rutas relativas

```markdown
![Imagen local](./Images/foto.png)
![Imagen en carpeta padre](../Images/foto.png)
```

### En Obsidian

```markdown
![[nombre-imagen.png]]
![[nombre-imagen.png|300]]  # Con tamaño
```

### Dimensiones (HTML)

```markdown
<img src="imagen.png" alt="descripción" width="200" height="100">
```

> 💡 **Práctica:** Ejercita imágenes en [Ejercicios Markdown - Sección 7](Ejercicios_Markdown.md#sección-7-imágenes)

---

## Comentarios

Los comentarios no se muestran en el resultado renderizado.

### Comentario HTML

```markdown
<!-- Este es un comentario -->
```

### Uso

```markdown
Aquí hay texto visible.

<!-- TODO: Revisar esta sección -->

Más texto visible.
```

> 💡 **Práctica:** Ejercita comentarios en [Ejercicios Markdown - Sección 8](Ejercicios_Markdown.md#sección-8-comentarios)

---

## Resumen de sintaxis básica

| Elemento | Sintaxis |
|----------|----------|
| Encabezado | `# H1`, `## H2`, etc. |
| Negrita | `**texto**` |
| Cursiva | `*texto*` |
| Lista no ordenada | `- elemento` |
| Lista ordenada | `1. elemento` |
| Enlace | `[texto](url)` |
| Imagen | `![alt](url)` |
| Comentario | `<!-- comentario -->` |

---

## Siguientes pasos

Continúa con [Guía Markdown - Nivel Intermedio](Guia_Markdown_Intermedio.md) para aprender sobre bloques de código, tablas y citas.
