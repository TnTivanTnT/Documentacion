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
User=ubuntu
ExecStart=/home/ubuntu/script.sh
Restart=on-failure
RestartSec=5

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
```

Aplicar cambios:
```bash
source ~/.bashrc
```

### Comandos útiles

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
| `htop` | Monitor interactivo mejorado |
| `free -h` | Memoria disponible |
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
| `lsusb` | Dispositivos USB |
| `lsblk` | Dispositivos de bloque |
| `hwinfo` | Info completa (requiere instalación) |

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
