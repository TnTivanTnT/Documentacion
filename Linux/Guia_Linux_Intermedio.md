# Guía Linux - Nivel Intermedio

> 📚 [Volver al Índice de Documentación](../README.md)

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
| `tee` | Pantalla + Archivo simultáneamente | `comando | tee archivo.txt` |

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
# Monitorizar un comando cada 1 segundo (¡Muy útil!)
watch -n 1 "ip addr | grep inet"
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

### ¿Quién usa mi archivo/puerto? (lsof)

`lsof` (List Open Files) es vital para saber qué proceso bloquea un sensor o un puerto.

| Comando | Uso en Robótica |
| :--- | :--- |
| `sudo lsof /dev/ttyUSB0` | ¿Quién está usando el LiDAR? |
| `lsof -i :8080` | ¿Quién usa el puerto 8080? |
| `lsof -p <PID>` | ¿Qué archivos tiene abiertos este nodo? |

### Procesos en segundo plano
...
| Comando | Descripción |
|---------|-------------|
| `comando &` | Ejecuta en background |
| `Ctrl + Z` | Suspende proceso actual |
| `jobs` | Lista trabajos en background |
| `fg %1` | Trae trabajo a primer plano |
| `bg %1` | Continúa en background |
| `nohup comando &` | Persiste al cerrar terminal |

### Resiliencia: Tmux (Multiplexor de terminal)

`tmux` permite mantener sesiones abiertas aunque te desconectes del robot. Es como dejar una terminal "viva" en el robot aunque apagues tu PC.

| Acción | Atajo (Dentro de tmux) |
| :--- | :--- |
| Iniciar nuevo tmux | Escribe `tmux` en terminal |
| **D**etach (Salir sin cerrar) | `Ctrl + B` y luego `D` |
| **A**ttach (Volver a entrar) | Escribe `tmux a` en terminal |
| Dividir pantalla (H) | `Ctrl + B` y luego `"` |
| Dividir pantalla (V) | `Ctrl + B` y luego `%` |
| Cerrar panel actual | Escribe `exit` |

> 💡 **Tip:** En robótica, ejecuta siempre tus nodos de ROS 2 dentro de un `tmux` vía SSH. Si el Wifi falla, los procesos seguirán corriendo y el robot no se quedará "tonto".

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
| `nmcli dev wifi` | Lista redes Wifi disponibles |
| `curl ifconfig.me` | Muestra IP pública |
| `iperf3 -c <ip>` | Mide velocidad de red |

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
| `ssh -X usuario@host` | SSH con X11 Forwarding (Ver apps gráficas) |
| `scp archivo usuario@host:destino` | Copia vía SSH |
| `rsync -avz origen/ usuario@host:destino/` | Sincronización |

### Conexión sin contraseñas (SSH Keys)

Para no tener que escribir la contraseña cada vez que entras al robot:

1.  **Genera tu llave** (si no la tienes): `ssh-keygen -t ed25519`
2.  **Copia la llave al robot**: `ssh-copy-id usuario@mi_robot`
3.  **¡Listo!** La próxima vez entrarás directamente.

### El archivo de configuración SSH (`~/.ssh/config`)

Si tienes varios robots, es mejor crear un alias para cada uno en tu PC local:
Edita (o crea) `nano ~/.ssh/config`:
```text
Host robot
    HostName 192.168.1.42
    User usuario-robot
    IdentityFile ~/.ssh/id_ed25519
```
Ahora, para entrar al robot solo necesitas escribir: **`ssh robot`**.

### Tips de Productividad de Red
...
**1. No memorices IPs (`/etc/hosts`):**
Puedes asignar un nombre a la IP de tu robot editando el archivo de hosts:
```bash
sudo nano /etc/hosts
# Añade una línea al final:
# 192.168.1.42  mi_robot
```
Ahora puedes hacer `ssh usuario@mi_robot` o `ping mi_robot`.

**2. Ver puertos en tiempo real:**
```bash
watch -n 1 "ss -tulpn"
```

**3. Prueba de velocidad de red (¡Vital para video/LiDAR!):**
Si el robot va lento, instala `iperf3` en ambos (PC y Robot):
- **En el robot (Servidor):** `iperf3 -s`
- **En tu PC (Cliente):** `iperf3 -c <IP_DEL_ROBOT>`
Esto te dirá los Mbps reales de tu conexión WiFi.

### Aliases: Automatización Personalizada

Puedes crear tus propios comandos para automatizar tareas repetitivas en tu flujo de trabajo.

> 💡 **Ejemplo (Robótica):** Muchos ingenieros crean aliases para cargar su entorno rápidamente:
> ```bash
> alias sros='source /opt/ros/humble/setup.bash'
> alias sws='source install/setup.bash'
> alias robot='ssh usuario-robot@192.168.1.42'
> ```

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
