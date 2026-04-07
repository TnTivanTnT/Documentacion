# Guía Markdown - Nivel Intermedio

> 📚 [Volver al Índice de Documentación](../Índice%20Documentación.md)

## Índice
- [Bloques de Código](#bloques-de-código)
- [Citas en Bloque](#citas-en-bloque)
- [Tablas](#tablas)
- [Separadores Horizontales](#separadores-horizontales)
- [Listas de Definición](#listas-de-definición)
- [Notas al Pie](#notas-al-pie)
- [Abreviaturas](#abreviaturas)

---

## Bloques de Código

### Código inline

Usa backticks (`` ` ``) para código dentro de texto:

```markdown
El comando `ls` lista archivos.
Usa `print("Hola")` en Python.
```

Resultado: El comando `ls` lista archivos.

### Bloques de código

Usa triple backticks:

````markdown
```
Código sin resaltar
múltiples líneas
```
````

### Resaltado de sintaxis

Especifica el lenguaje:

````markdown
```python
def hola():
    print("Hola mundo")
```
````

````markdown
```bash
#!/bin/bash
echo "Hola mundo"
```
````

````markdown
```javascript
function hola() {
    console.log("Hola mundo");
}
```
````

### Lenguajes soportados

| Lenguaje | Identificador |
|----------|---------------|
| Python | `python`, `py` |
| JavaScript | `javascript`, `js` |
| Bash | `bash`, `shell`, `sh` |
| C | `c` |
| C++ | `cpp`, `c++` |
| Java | `java` |
| JSON | `json` |
| YAML | `yaml`, `yml` |
| XML | `xml` |
| HTML | `html` |
| CSS | `css` |
| SQL | `sql` |
| Markdown | `markdown`, `md` |
| Diff | `diff` |
| Dockerfile | `dockerfile` |

### Números de línea (algunos procesadores)

````markdown
```python linenums="1"
def funcion():
    return 42
```
````

### Resaltar líneas

````markdown
```python hl_lines="2 3"
def funcion():
    linea resaltada
    otra linea resaltada
```
````

> 💡 **Práctica:** Ejercita bloques de código en [Ejercicios Markdown - Sección 1](Ejercicios_Markdown.md#sección-1-bloques-de-código)

---

## Citas en Bloque

### Sintaxis básica

Usa `>` al inicio de la línea:

```markdown
> Esta es una cita en bloque.
> Puede ocupar múltiples líneas.
```

Resultado:
> Esta es una cita en bloque.
> Puede ocupar múltiples líneas.

### Citas anidadas

```markdown
> Primera cita
>> Segunda cita
>>> Tercera cita
```

Resultado:
> Primera cita
>> Segunda cita
>>> Tercera cita

### Citas con otros elementos

```markdown
> ### Encabezado en cita
>
> - Elemento 1
> - Elemento 2
>
> Código: `ejemplo`
```

### Citas multilínea

```markdown
> Lorem ipsum dolor sit amet,
> consectetur adipiscing elit.
> Sed do eiusmod tempor incididunt.
```

### Ejemplo con autor

```markdown
> La simplicidad es la máxima sofisticación.
>
> — Leonardo da Vinci
```

> 💡 **Práctica:** Ejercita citas en [Ejercicios Markdown - Sección 2](Ejercicios_Markdown.md#sección-2-citas-en-bloque)

---

## Tablas

### Sintaxis básica

```markdown
| Columna 1 | Columna 2 | Columna 3 |
|-----------|-----------|-----------|
| Dato 1    | Dato 2    | Dato 3    |
| Dato 4    | Dato 5    | Dato 6    |
```

Resultado:
| Columna 1 | Columna 2 | Columna 3 |
|-----------|-----------|-----------|
| Dato 1    | Dato 2    | Dato 3    |
| Dato 4    | Dato 5    | Dato 6    |

### Alineación

```markdown
| Izquierda | Centro | Derecha |
|:----------|:------:|--------:|
| Texto     | Texto  | Texto   |
| Más       | Más    | Más     |
```

Resultado:
| Izquierda | Centro | Derecha |
|:----------|:------:|--------:|
| Texto     | Texto  | Texto   |
| Más       | Más    | Más     |

| Símbolo | Alineación |
|---------|------------|
| `:---` | Izquierda |
| `:---:` | Centro |
| `---:` | Derecha |
| `---` | Por defecto |

### Tabla simple

```markdown
Columna 1 | Columna 2 | Columna 3
--- | --- | ---
Dato 1 | Dato 2 | Dato 3
```

### Formato dentro de tablas

```markdown
| Formato | Ejemplo |
|---------|---------|
| Negrita | **texto** |
| Cursiva | *texto* |
| Código | `código` |
| Enlace | [link](url) |
```

### Listas en tablas (con HTML)

```markdown
<table>
<tr>
<td>Columna 1</td>
<td>

- Elemento 1
- Elemento 2
- Elemento 3

</td>
</tr>
</table>
```

> 💡 **Práctica:** Ejercita tablas en [Ejercicios Markdown - Sección 3](Ejercicios_Markdown.md#sección-3-tablas)

---

## Separadores Horizontales

### Sintaxis

Tres o más caracteres:

```markdown
---

***

___
```

Todos producen el mismo resultado:

---

### Con espacio adicional

```markdown
- - -

* * *
```

### Uso

```markdown
Sección anterior

---

Sección siguiente
```

> 💡 **Práctica:** Ejercita separadores en [Ejercicios Markdown - Sección 4](Ejercicios_Markdown.md#sección-4-separadores)

---

## Listas de Definición

### HTML (estándar)

```markdown
<dl>
  <dt>Término 1</dt>
  <dd>Definición del término 1</dd>
  
  <dt>Término 2</dt>
  <dd>Definición del término 2</dd>
</dl>
```

### Alternativa con Markdown puro

```markdown
**Término 1**
: Definición del término 1

**Término 2**
: Definición del término 2
```

### Ejemplo completo

```markdown
**ROS2**
: Robot Operating System 2, framework de desarrollo robótico

**Nodo**
: Unidad de procesamiento en ROS2

**Topic**
: Canal de comunicación para mensajes
```

> 💡 **Práctica:** Ejercita listas de definición en [Ejercicios Markdown - Sección 5](Ejercicios_Markdown.md#sección-5-listas-de-definición)

---

## Notas al Pie

### Sintaxis estándar (PHP Markdown Extra)

```markdown
Texto con nota[^1].

[^1]: Esta es la nota al pie.
```

### Nota en múltiples líneas

```markdown
Texto con nota[^larga].

[^larga]: Primera línea de la nota.
          Segunda línea de la nota.
          Tercera línea.
```

### Múltiples notas

```markdown
Primera nota[^1] y segunda nota[^2].

[^1]: Contenido de la primera nota.
[^2]: Contenido de la segunda nota.
```

### Ejemplo práctico

```markdown
ROS2 usa DDS[^dds] como middleware de comunicación.

[^dds]: Data Distribution Service, estándar OMG para 
sistemas distribuidos de tiempo real.
```

> 💡 **Práctica:** Ejercita notas al pie en [Ejercicios Markdown - Sección 6](Ejercicios_Markdown.md#sección-6-notas-al-pie)

---

## Abreviaturas

### Sintaxis (PHP Markdown Extra)

```markdown
La documentación está en HTML y CSS.

*[HTML]: Hyper Text Markup Language
*[CSS]: Cascading Style Sheets
```

### Ejemplo

```markdown
Usamos ROS2 con rclpy para desarrollo.

*[ROS2]: Robot Operating System 2
*[rclpy]: ROS Client Library for Python
```

> 💡 **Práctica:** Ejercita abreviaturas en [Ejercicios Markdown - Sección 7](Ejercicios_Markdown.md#sección-7-abreviaturas)

---

## Resumen de sintaxis intermedia

| Elemento | Sintaxis |
|----------|----------|
| Código inline | `` `código` `` |
| Bloque de código | ` ```lenguaje ` |
| Cita | `> texto` |
| Tabla | `| col1 | col2 |` |
| Separador | `---` |
| Nota al pie | `[^1]: nota` |

---

## Siguientes pasos

Continúa con [Guía Markdown - Nivel Avanzado](Guia_Markdown_Avanzado.md) para aprender sobre HTML embebido y extensiones específicas.
