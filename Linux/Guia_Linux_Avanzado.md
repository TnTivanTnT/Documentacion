# Guía Linux - Nivel Avanzado

> 📚 [Volver al Índice de Documentación](../README.md)

## Índice
- [Scripting en Bash](#scripting-en-bash)
- [Systemd y Servicios](#systemd-y-servicios)
- [Gestión de Paquetes Avanzada](#gestión-de-paquetes-avanzada)
- [Variables de Entorno](#variables-de-entorno)
- [Diagnóstico del Sistema](#diagnóstico-del-sistema)

---

## Scripting en Bash

### Estructura básica de un script

```bash
#!/bin/bash

VAR="Hola Mundo"
echo $VAR
```

### Shebang y permisos
```bash
chmod +x script.sh
./script.sh
```

### Tips de Robustez (¡Muy recomendado!)

Añade esto al principio de tus scripts después del shebang:
```bash
set -e  # El script se detiene inmediatamente si un comando falla
set -x  # Muestra cada comando antes de ejecutarlo (útil para debug)
```

### Variables

```bash
NOMBRE="Usuario"
EDAD=25
PI=3.14159

echo "Hola, $NOMBRE"
echo "Edad: ${EDAD}"

VAR_CMD=$(pwd)
echo "Directorio: $VAR_CMD"
```

### Variables especiales

| Variable | Descripción |
|----------|-------------|
| `$0` | Nombre del script |
| `$1`, `$2`... | Argumentos posicionales |
| `$#` | Número de argumentos |
| `$@` | Todos los argumentos |
| `$?` | Código de salida del último comando |
| `$$` | PID del script actual |

### Condicionales

```bash
if [ condición ]; then
    comandos
elif [ condición ]; then
    comandos
else
    comandos
fi
```

### Operadores de comparación

**Numéricos:**

| Operador | Significado |
|----------|-------------|
| `-eq` | Igual |
| `-ne` | No igual |
| `-lt` | Menor que |
| `-le` | Menor o igual |
| `-gt` | Mayor que |
| `-ge` | Mayor o igual |

**Cadenas:**

| Operador | Significado |
|----------|-------------|
| `=` o `==` | Igual |
| `!=` | Diferente |
| `-z` | Cadena vacía |
| `-n` | Cadena no vacía |

**Archivos:**

| Operador | Significado |
|----------|-------------|
| `-e` | Existe |
| `-f` | Es archivo regular |
| `-d` | Es directorio |
| `-r` | Legible |
| `-w` | Escribible |
| `-x` | Ejecutable |

### Bucles

**For:**
```bash
for i in 1 2 3 4 5; do
    echo "Número: $i"
done

for archivo in *.txt; do
    echo "Procesando: $archivo"
done

for ((i=0; i<10; i++)); do
    echo "Iteración: $i"
done
```

**While:**
```bash
contador=0
while [ $contador -lt 5 ]; do
    echo "Contador: $contador"
    ((contador++))
done
```

**Until:**
```bash
until [ $contador -ge 5 ]; do
    echo "Contador: $contador"
    ((contador++))
done
```

### Funciones

```bash
mi_funcion() {
    local var_local="valor"
    echo "Argumento 1: $1"
    echo "Argumento 2: $2"
    return 0
}

mi_funcion "param1" "param2"
resultado=$?
```

### Arrays

```bash
frutas=("manzana" "naranja" "pera")

echo ${frutas[0]}
echo ${frutas[@]}

for fruta in "${frutas[@]}"; do
    echo $fruta
done
```

> 💡 **Práctica:** Ejercita scripting en [Ejercicios Linux - Sección 1](Ejercicios_Linux.md#sección-1-scripting-en-bash)

---

## Systemd y Servicios

### Gestión de servicios

| Comando | Descripción |
|---------|-------------|
| `systemctl start servicio` | Inicia servicio |
| `systemctl stop servicio` | Detiene servicio |
| `systemctl restart servicio` | Reinicia servicio |
| `systemctl status servicio` | Estado del servicio |
| `systemctl enable servicio` | Habilita al inicio |
| `systemctl disable servicio` | Deshabilita del inicio |
| `systemctl is-active servicio` | Verifica si está activo |
| `systemctl is-enabled servicio` | Verifica si está habilitado |

### Ver todos los servicios
```bash
systemctl list-units --type=service
systemctl list-unit-files --type=service
```

### journalctl - Logs del sistema

| Comando | Descripción |
|---------|-------------|
| `journalctl` | Todos los logs |
| `journalctl -u servicio` | Logs de un servicio |
| `journalctl -f` | Seguir logs en tiempo real |
| `journalctl --since today` | Logs desde hoy |
| `journalctl --since "1 hour ago"` | Logs de la última hora |
| `journalctl -p err` | Solo errores |
| `journalctl -b` | Logs del arranque actual |

### Crear un servicio personalizado

`/etc/systemd/system/mi-servicio.service`:
```ini
[Unit]
Description=Mi Servicio Personalizado
After=network.target

[Service]
Type=simple
User=usuario-robot
ExecStart=/home/usuario-robot/script.sh
Restart=on-failure    # Reinicia si el programa crashea
RestartSec=5         # Espera 5s antes de reiniciar

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable mi-servicio
sudo systemctl start mi-servicio
```

> 💡 **Práctica:** Ejercita systemd en [Ejercicios Linux - Sección 2](Ejercicios_Linux.md#sección-2-systemd-y-servicios)

---

## Gestión de Paquetes Avanzada

### APT (Ubuntu/Debian)

| Comando | Descripción |
|---------|-------------|
| `apt update` | Actualiza lista de paquetes |
| `apt upgrade` | Actualiza paquetes instalados |
| `apt install paquete` | Instala paquete |
| `apt remove paquete` | Elimina paquete |
| `apt purge paquete` | Elimina paquete y configuración |
| `apt autoremove` | Elimina paquetes innecesarios |
| `apt search nombre` | Busca paquete |
| `apt show paquete` | Muestra información |
| `apt list --installed` | Lista paquetes instalados |

### dpkg - Nivel bajo

| Comando | Descripción |
|---------|-------------|
| `dpkg -i paquete.deb` | Instala .deb |
| `dpkg -r paquete` | Elimina paquete |
| `dpkg -l` | Lista paquetes |
| `dpkg -L paquete` | Archivos del paquete |

### SNAP

| Comando | Descripción |
|---------|-------------|
| `snap find nombre` | Busca snap |
| `snap install paquete` | Instala snap |
| `snap remove paquete` | Elimina snap |
| `snap list` | Lista snaps |
| `snap refresh paquete` | Actualiza snap |

> 💡 **Práctica:** Ejercita gestión de paquetes en [Ejercicios Linux - Sección 3](Ejercicios_Linux.md#sección-3-gestión-de-paquetes-avanzada)

---

## Compresión y Archivado (Tar)

En robótica solemos comprimir carpetas de logs o workspaces enteros para compartirlos.

| Comando | Descripción | Ejemplo |
|---------|-------------|---------|
| `tar -czvf` | Comprimir en .tar.gz | `tar -czvf bkp.tar.gz ./mi_workspace` |
| `tar -xzvf` | Descomprimir .tar.gz | `tar -xzvf bkp.tar.gz` |
| `zip -r` | Comprimir en .zip | `zip -r bkp.zip ./mi_dir` |

---

## Variables de Entorno

### Ver y definir variables

```bash
echo $PATH
echo $HOME
echo $USER

export MI_VAR="valor"
echo $MI_VAR
```

### Archivos de configuración

| Archivo | Cuándo se carga |
|---------|-----------------|
| `/etc/environment` | Sistema (todos los usuarios) |
| `~/.bashrc` | Cada terminal interactiva |
| `~/.bash_profile` | Login shell |
| `~/.profile` | Login shell (alternativa) |

### Configuración permanente

Añadir a `~/.bashrc`:
```bash
export ROS_DOMAIN_ID=42
export PATH=$PATH:/mi/ruta
alias ll='ls -la'
alias robot='ssh usuario-robot@192.168.1.42'
```

Aplicar cambios inmediatamente:
```bash
source ~/.bashrc
```

> 💡 **Tip:** Los Aliases son la forma más rápida de automatizar tu flujo diario. ¡Ponle nombre a todo lo que escribas más de 5 veces al día!

### Comandos útiles
...
| Comando | Descripción |
|---------|-------------|
| `env` | Muestra todas las variables |
| `printenv` | Muestra variables de entorno |
| `export VAR=val` | Define variable de entorno |
| `unset VAR` | Elimina variable |
| `alias` | Lista alias definidos |

> 💡 **Práctica:** Ejercita variables de entorno en [Ejercicios Linux - Sección 4](Ejercicios_Linux.md#sección-4-variables-de-entorno)

---

## Diagnóstico del Sistema

### Monitorización de recursos

| Comando | Descripción |
|---------|-------------|
| `top` | Procesos y uso de recursos |
| `btop` | Monitor interactivo moderno y visual |
| `nload` | Ver tráfico de red gráfico |
| `free -h` | Memoria disponible |

### El poder de btop

`btop` es un monitor de recursos mucho más visual e intuitivo que el clásico `top`.

Dentro de `btop`, puedes usar estas teclas:
- **`m`**: Cambiar el modo de visualización de memoria.
- **`b`**: Cambiar el modo de visualización de red.
- **`Esc`**: Abrir el menú de configuración y ayuda.
- **`q`**: Salir.

### Monitorización de Red (nload)
Si el video del robot llega con retraso, ejecuta `nload`. Verás barras gráficas de entrada y salida de datos en tiempo real para cada interfaz (WLAN, ETH).
| `df -h` | Espacio en disco |
| `du -sh *` | Tamaño de carpetas |
| `uptime` | Tiempo encendido y carga |
| `uname -a` | Información del kernel |

### Diagnóstico de disco

```bash
df -h
du -sh /var/*
du -h --max-depth=1 /home
ncdu
```

### Diagnóstico de memoria

```bash
free -h
cat /proc/meminfo
vmstat 1
```

### Diagnóstico de CPU

```bash
lscpu
cat /proc/cpuinfo
mpstat 1
```

### Hardware

| Comando | Descripción |
|---------|-------------|
| `lspci` | Dispositivos PCI |
| `lsusb` | Dispositivos USB (¡Vital para sensores!) |
| `lsblk` | Dispositivos de bloque |
| `dmesg -w` | Logs del Kernel (Para ver cuando conectas sensores) |
| `hwinfo` | Info completa (requiere instalación) |

---

## Casos de Uso Específicos (Hardware y Sincronización)

Estas herramientas se utilizan en proyectos avanzados que requieren control total sobre el hardware o múltiples sistemas trabajando en paralelo.

### 1. Nombres fijos para dispositivos USB (Udev Rules)
Por defecto, los nombres `/dev/ttyUSB0` pueden cambiar. Con las reglas de Udev, puedes fijarlos (ej: `/dev/sensor_lidar`).

### 2. Sincronización de Tiempo (Chrony)
Fundamental cuando usas varios ordenadores que deben tener la misma hora exacta (ej: un robot y un servidor).

---

## Limpieza del Sistema (Mantenimiento)
...
En robótica es fácil quedarse sin espacio por culpa de logs pesados.

| Acción | Comando |
| :--- | :--- |
| Ver 10 carpetas más pesadas | `du -hs * | sort -rh | head -10` |
| Limpiar cache de APT | `sudo apt clean` |
| Borrar paquetes inútiles | `sudo apt autoremove` |
| Limpiar logs de ROS 2 | `rm -rf ~/.ros/log/*` |

> 💡 **Tip:** Usa el comando `ncdu` (si está instalado) para navegar visualmente por las carpetas y ver qué está ocupando espacio.

> 💡 **Práctica:** Ejercita diagnóstico en [Ejercicios Linux - Sección 5](Ejercicios_Linux.md#sección-5-diagnóstico-del-sistema)

---

## Resumen de comandos avanzados

| Categoría | Comandos clave |
|-----------|----------------|
| Scripting | Variables, bucles, funciones |
| Servicios | `systemctl`, `journalctl` |
| Paquetes | `apt`, `dpkg`, `snap` |
| Entorno | `export`, `source`, alias |
| Diagnóstico | `top`, `df`, `free`, `dmesg` |

---

## Recursos adicionales

- [Guía Linux - Nivel Principiante](Guia_Linux_Principiante.md)
- [Guía Linux - Nivel Intermedio](Guia_Linux_Intermedio.md)
- [Documentación oficial de Ubuntu](https://help.ubuntu.com/)
