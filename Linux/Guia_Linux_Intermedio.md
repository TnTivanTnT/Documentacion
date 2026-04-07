# Guía Linux - Nivel Intermedio

> 📚 [Volver al Índice de Documentación](../Índice%20Documentación.md)

## Índice
- [Pipes y Redirecciones](#pipes-y-redirecciones)
- [Búsqueda de Archivos](#búsqueda-de-archivos)
- [Filtrado de Texto](#filtrado-de-texto)
- [Gestión de Procesos](#gestión-de-procesos)
- [Comandos de Red Básicos](#comandos-de-red-básicos)

---

## Pipes y Redirecciones

### Redirecciones de salida

| Operador | Descripción | Ejemplo |
|----------|-------------|---------|
| `>` | Redirige salida (sobrescribe) | `echo "texto" > archivo.txt` |
| `>>` | Redirige salida (añade) | `echo "más" >> archivo.txt` |
| `2>` | Redirige errores | `comando 2> errores.log` |
| `2>&1` | Redirige errores a salida estándar | `comando > salida.log 2>&1` |
| `&>` | Redirige todo (stdout + stderr) | `comando &> todo.log` |

### Redirecciones de entrada

| Operador | Descripción | Ejemplo |
|----------|-------------|---------|
| `<` | Entrada desde archivo | `sort < datos.txt` |
| `<<` | Here document (entrada multilínea) | Ver ejemplo abajo |

```bash
cat << EOF
Este es un texto
de múltiples líneas
EOF
```

### Pipes (tuberías)

El pipe (`|`) conecta la salida de un comando con la entrada de otro.

```bash
comando1 | comando2 | comando3
```

### Ejemplos prácticos
```bash
ls -la | grep ".txt"
cat archivo.log | sort | uniq
ps aux | grep python
history | tail -20
ls -l | wc -l
```

> 💡 **Práctica:** Ejercita pipes en [Ejercicios Linux - Sección 1](Ejercicios_Linux.md#sección-1-pipes-y-redirecciones)

---

## Búsqueda de Archivos

### find - Búsqueda avanzada

| Comando | Descripción |
|---------|-------------|
| `find . -name "*.txt"` | Busca por nombre |
| `find / -type d` | Busca solo directorios |
| `find . -type f` | Busca solo archivos |
| `find . -size +100M` | Archivos mayores a 100MB |
| `find . -mtime -7` | Modificados en últimos 7 días |
| `find . -user ubuntu` | Del usuario especificado |
| `find . -perm 755` | Con permisos específicos |

### Opciones de find
| Opción | Descripción |
|--------|-------------|
| `-name` | Buscar por nombre |
| `-iname` | Buscar por nombre (ignora mayúsculas) |
| `-type f/d` | Archivo/directorio |
| `-size` | Tamaño |
| `-mtime` | Días desde modificación |
| `-exec` | Ejecutar comando en resultados |

```bash
find . -name "*.log" -exec rm {} \;
find . -type f -size +10M -exec ls -lh {} \;
```

### locate - Búsqueda rápida

| Comando | Descripción |
|---------|-------------|
| `locate archivo` | Busca en base de datos indexada |
| `sudo updatedb` | Actualiza base de datos |

> 💡 **Práctica:** Ejercita búsqueda en [Ejercicios Linux - Sección 2](Ejercicios_Linux.md#sección-2-búsqueda-de-archivos)

---

## Filtrado de Texto

### grep - Búsqueda de patrones

| Opción | Descripción | Ejemplo |
|--------|-------------|---------|
| `-i` | Ignora mayúsculas/minúsculas | `grep -i "error" log.txt` |
| `-r` | Búsqueda recursiva | `grep -r "TODO" ./src` |
| `-n` | Muestra número de línea | `grep -n "funcion" codigo.py` |
| `-v` | Invierte coincidencias | `grep -v "comentario" archivo` |
| `-c` | Cuenta coincidencias | `grep -c "error" log.txt` |
| `-l` | Solo nombres de archivo | `grep -l "patron" *.txt` |
| `-E` | Expresiones regulares extendidas | `grep -E "a\|b" archivo` |

### Ejemplos comunes
```bash
grep "ERROR" /var/log/syslog
grep -rn "import" ./proyecto/
grep -v "^#" config.conf | grep -v "^$"
ps aux | grep python | grep -v grep
```

### Otros comandos de texto

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `sort` | Ordena líneas | `sort archivo.txt` |
| `sort -n` | Ordena numéricamente | `sort -n numeros.txt` |
| `sort -r` | Ordena inversamente | `sort -r archivo.txt` |
| `uniq` | Elimina duplicados consecutivos | `sort archivo \| uniq` |
| `cut` | Extrae columnas | `cut -d: -f1 /etc/passwd` |
| `tr` | Traduce caracteres | `tr 'a-z' 'A-Z' < archivo` |
| `sed` | Editor de flujo | `sed 's/old/new/g' archivo` |
| `awk` | Procesamiento de texto | `awk '{print $1}' archivo` |

### Ejemplos de combinaciones
```bash
cat log.txt | grep "ERROR" | sort | uniq -c
cut -d: -f1 /etc/passwd | sort
cat archivo.csv | awk -F, '{print $1, $3}'
```

> 💡 **Práctica:** Ejercita filtrado en [Ejercicios Linux - Sección 3](Ejercicios_Linux.md#sección-3-filtrado-de-texto)

---

## Gestión de Procesos

### Ver procesos

| Comando | Descripción |
|---------|-------------|
| `ps` | Muestra procesos actuales |
| `ps aux` | Todos los procesos con detalles |
| `ps aux | grep nombre` | Busca proceso específico |
| `top` | Monitor interactivo |
| `htop` | Monitor mejorado (requiere instalación) |
| `pgrep nombre` | Obtiene PID por nombre |

### Columnas de ps aux
| Columna | Significado |
|---------|-------------|
| USER | Usuario propietario |
| PID | ID del proceso |
| %CPU | Uso de CPU |
| %MEM | Uso de memoria |
| COMMAND | Comando ejecutado |

### Control de procesos

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `kill PID` | Envía señal TERM (15) | `kill 1234` |
| `kill -9 PID` | Envía señal KILL (forzado) | `kill -9 1234` |
| `killall nombre` | Mata por nombre | `killall python` |
| `pkill patron` | Mata por patrón | `pkill -f "script.py"` |

### Procesos en segundo plano

| Comando | Descripción |
|---------|-------------|
| `comando &` | Ejecuta en background |
| `Ctrl + Z` | Suspende proceso actual |
| `jobs` | Lista trabajos en background |
| `fg %1` | Trae trabajo a primer plano |
| `bg %1` | Continúa en background |
| `nohup comando &` | Persiste al cerrar terminal |

> 💡 **Práctica:** Ejercita gestión de procesos en [Ejercicios Linux - Sección 4](Ejercicios_Linux.md#sección-4-gestión-de-procesos)

---

## Comandos de Red Básicos

### Información de red

| Comando | Descripción |
|---------|-------------|
| `ip addr` | Muestra direcciones IP |
| `ip link` | Muestra interfaces |
| `hostname -I` | Muestra IP local |
| `ping google.com` | Verifica conectividad |
| `curl ifconfig.me` | Muestra IP pública |

### Conexiones

| Comando | Descripción |
|---------|-------------|
| `ss -tulpn` | Puertos abiertos |
| `netstat -tulpn` | Puertos abiertos (alternativa) |
| `curl URL` | Petición HTTP |
| `wget URL` | Descarga archivo |

### SSH

| Comando | Descripción |
|---------|-------------|
| `ssh usuario@host` | Conexión SSH |
| `ssh -p puerto usuario@host` | SSH con puerto específico |
| `scp archivo usuario@host:destino` | Copia vía SSH |
| `rsync -avz origen/ usuario@host:destino/` | Sincronización |

> 💡 **Práctica:** Ejercita comandos de red en [Ejercicios Linux - Sección 5](Ejercicios_Linux.md#sección-5-comandos-de-red-básicos)

---

## Resumen de comandos intermedios

| Categoría | Comandos clave |
|-----------|----------------|
| Redirecciones | `>`, `>>`, `|`, `2>&1` |
| Búsqueda | `find`, `locate`, `grep` |
| Texto | `sort`, `uniq`, `cut`, `sed`, `awk` |
| Procesos | `ps`, `top`, `kill`, `jobs` |
| Red | `ip`, `ping`, `ss`, `curl` |

---

## Siguientes pasos

Continúa con [Guía Linux - Nivel Avanzado](Guia_Linux_Avanzado.md) para aprender scripting Bash y administración del sistema.
