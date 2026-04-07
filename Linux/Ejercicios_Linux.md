# Ejercicios Prácticos - Linux

> 📚 [Volver al Índice de Documentación](../Índice%20Documentación.md)

Este documento contiene ejercicios prácticos para reforzar los conceptos de las guías de Linux.

---

## Nivel Principiante

### Sección 1: Introducción a la Terminal

> 📖 **Teoría:** [Guía Linux Principiante - Introducción](Guia_Linux_Principiante.md#introducción-a-la-terminal)

#### Ejercicio 1.1: Primeros pasos
1. Abre una terminal
2. Usa `Ctrl + L` para limpiar la pantalla
3. Presiona `Tab` dos veces para ver los comandos disponibles
4. Usa `↑` y `↓` para navegar el historial

#### Ejercicio 1.2: Autocompletado
1. Escribe `cd /ho` y presiona `Tab`
2. Escribe `ls /etc/pass` y presiona `Tab`
3. Intenta autocompletar rutas largas

---

### Sección 2: Navegación de Directorios

> 📖 **Teoría:** [Guía Linux Principiante - Navegación](Guia_Linux_Principiante.md#navegación-de-directorios)

#### Ejercicio 2.1: Conociendo el sistema de archivos
```bash
pwd
ls
ls -la
ls -lah
```

**Tarea:**
1. ¿Cuál es tu directorio actual?
2. Lista todos los archivos (incluidos ocultos) con detalles
3. ¿Qué información muestra cada columna de `ls -l`?

#### Ejercicio 2.2: Navegación
```bash
cd /etc
ls
cd ~
cd -
cd ..
pwd
```

**Tarea:**
1. Navega a `/var/log` y lista su contenido
2. Regresa a tu home con un solo comando
3. Navega al directorio anterior con un solo comando

#### Ejercicio 2.3: Rutas absolutas y relativas
1. Desde tu home, navega a `/usr/bin` usando ruta absoluta
2. Desde ahí, navega a `/usr/share` usando ruta relativa
3. Verifica tu ubicación con `pwd`

---

### Sección 3: Gestión de Archivos y Directorios

> 📖 **Teoría:** [Guía Linux Principiante - Gestión de Archivos](Guia_Linux_Principiante.md#gestión-de-archivos-y-directorios)

#### Ejercicio 3.1: Crear estructura de directorios
```bash
mkdir -p ~/practica_linux/{documentos,codigo,datos}
mkdir ~/practica_linux/documentos/notas
ls -R ~/practica_linux
```

**Tarea:** Crea esta estructura:
```
practica_linux/
├── documentos/
│   └── notas/
├── codigo/
└── datos/
```

#### Ejercicio 3.2: Crear y manipular archivos
```bash
cd ~/practica_linux
touch readme.txt
touch documentos/notas/clase1.txt
touch documentos/notas/clase2.txt
ls -R
```

#### Ejercicio 3.3: Copiar archivos
```bash
cp readme.txt documentos/
cp -r documentos documentos_backup
ls -R
```

#### Ejercicio 3.4: Mover y renombrar
```bash
mv readme.txt LEEME.txt
mv LEEME.txt documentos/
ls documentos/
```

#### Ejercicio 3.5: Eliminar (con cuidado)
```bash
rm documentos_backup/documentos/notas/clase1.txt
rmdir documentos_backup/documentos/notas
rm -rf documentos_backup
ls
```

---

### Sección 4: Permisos Básicos

> 📖 **Teoría:** [Guía Linux Principiante - Permisos](Guia_Linux_Principiante.md#permisos-básicos)

#### Ejercicio 4.1: Ver permisos
```bash
ls -l ~/practica_linux
ls -ld ~/practica_linux
```

**Tarea:** Interpreta los permisos de cada archivo/directorio

#### Ejercicio 4.2: Cambiar permisos
```bash
cd ~/practica_linux
touch script.sh
echo '#!/bin/bash' > script.sh
echo 'echo "Hola"' >> script.sh
chmod +x script.sh
./script.sh
```

#### Ejercicio 4.3: Permisos numéricos
```bash
touch privado.txt
chmod 600 privado.txt
ls -l privado.txt

touch compartido.txt
chmod 644 compartido.txt
ls -l compartido.txt
```

---

### Sección 5: Visualización de Contenido

> 📖 **Teoría:** [Guía Linux Principiante - Visualización](Guia_Linux_Principiante.md#visualización-de-contenido)

#### Ejercicio 5.1: Ver archivos del sistema
```bash
cat /etc/passwd
less /etc/passwd
head -n 5 /etc/passwd
tail -n 5 /etc/passwd
wc -l /etc/passwd
```

#### Ejercicio 5.2: Crear y ver archivo grande
```bash
cd ~/practica_linux
seq 1 100 > numeros.txt
head numeros.txt
tail numeros.txt
less numeros.txt
wc numeros.txt
```

#### Ejercicio 5.3: Seguir archivo en tiempo real
```bash
# En una terminal:
tail -f /var/log/syslog

# En otra terminal:
logger "Mensaje de prueba"
```

---

## Nivel Intermedio

### Sección 1: Pipes y Redirecciones

> 📖 **Teoría:** [Guía Linux Intermedio - Pipes](Guia_Linux_Intermedio.md#pipes-y-redirecciones)

#### Ejercicio 1.1: Redirecciones básicas
```bash
cd ~/practica_linux
echo "Primera línea" > archivo.txt
echo "Segunda línea" >> archivo.txt
cat archivo.txt
ls -la > listado.txt
cat listado.txt
```

#### Ejercicio 1.2: Pipes con grep
```bash
ls -la /etc | grep ".conf"
ps aux | grep bash
cat /etc/passwd | grep ubuntu
history | tail -20
```

#### Ejercicio 1.3: Combinar comandos
```bash
ls -la /usr/bin | wc -l
cat /etc/passwd | cut -d: -f1 | sort
ls -la | grep "^d" | wc -l
```

#### Ejercicio 1.4: Redirección de errores
```bash
ls /noexiste 2> errores.txt
cat errores.txt
ls /etc /noexiste > salida.txt 2>&1
cat salida.txt
```

---

### Sección 2: Búsqueda de Archivos

> 📖 **Teoría:** [Guía Linux Intermedio - Búsqueda](Guia_Linux_Intermedio.md#búsqueda-de-archivos)

#### Ejercicio 2.1: find básico
```bash
find ~ -name "*.txt"
find /etc -name "*.conf" 2>/dev/null
find ~ -type d -name "practica*"
```

#### Ejercicio 2.2: find con criterios
```bash
find ~ -type f -size +1M
find ~ -type f -mtime -7
find /tmp -type f -user $USER
```

#### Ejercicio 2.3: find con exec
```bash
find ~ -name "*.txt" -exec ls -l {} \;
find ~ -type f -size +10M -exec du -h {} \;
```

#### Ejercicio 2.4: locate
```bash
sudo updatedb
locate bashrc
locate -i PYTHON
```

---

### Sección 3: Filtrado de Texto

> 📖 **Teoría:** [Guía Linux Intermedio - Filtrado](Guia_Linux_Intermedio.md#filtrado-de-texto)

#### Ejercicio 3.1: grep avanzado
```bash
grep -r "import" ~/practica_linux 2>/dev/null
grep -n "root" /etc/passwd
grep -v "^#" /etc/bash.bashrc | grep -v "^$"
grep -c "python" /var/log/syslog 2>/dev/null
```

#### Ejercicio 3.2: sort y uniq
```bash
cd ~/practica_linux
echo -e "3\n1\n2\n2\n3\n1" > nums.txt
sort nums.txt
sort nums.txt | uniq
sort nums.txt | uniq -c
```

#### Ejercicio 3.3: cut y awk
```bash
cut -d: -f1,3 /etc/passwd
cut -d: -f1-3 /etc/passwd
df -h | awk '{print $1, $4}'
ps aux | awk '{print $11}' | sort | uniq -c | sort -rn | head
```

---

### Sección 4: Gestión de Procesos

> 📖 **Teoría:** [Guía Linux Intermedio - Procesos](Guia_Linux_Intermedio.md#gestión-de-procesos)

#### Ejercicio 4.1: Ver procesos
```bash
ps aux
ps aux | grep $USER
ps -ef
top
```

**Tarea:** Identifica el PID de tu shell actual

#### Ejercicio 4.2: Procesos en background
```bash
sleep 60 &
jobs
fg %1
Ctrl+Z
bg %1
jobs
kill %1
```

#### Ejercicio 4.3: Matar procesos
```bash
sleep 100 &
ps aux | grep sleep
kill <PID>
sleep 100 &
kill -9 <PID>
```

#### Ejercicio 4.4: pgrep y pkill
```bash
sleep 200 &
pgrep sleep
pkill sleep
pgrep sleep
```

---

### Sección 5: Comandos de Red Básicos

> 📖 **Teoría:** [Guía Linux Intermedio - Red](Guia_Linux_Intermedio.md#comandos-de-red-básicos)

#### Ejercicio 5.1: Información de red
```bash
ip addr
ip link
hostname -I
ping -c 4 google.com
```

#### Ejercicio 5.2: Puertos y conexiones
```bash
ss -tulpn
netstat -tulpn 2>/dev/null
```

#### Ejercicio 5.3: Descargas
```bash
cd ~/practica_linux
curl https://www.google.com > google.html
curl -I https://www.google.com
```

---

## Nivel Avanzado

### Sección 1: Scripting en Bash

> 📖 **Teoría:** [Guía Linux Avanzado - Scripting](Guia_Linux_Avanzado.md#scripting-en-bash)

#### Ejercicio 1.1: Primer script
```bash
cd ~/practica_linux
cat > hola.sh << 'EOF'
#!/bin/bash
echo "Hola, $USER!"
echo "Directorio actual: $(pwd)"
echo "Fecha: $(date)"
EOF
chmod +x hola.sh
./hola.sh
```

#### Ejercicio 1.2: Variables y argumentos
```bash
cat > argumentos.sh << 'EOF'
#!/bin/bash
echo "Script: $0"
echo "Argumentos recibidos: $#"
echo "Todos los argumentos: $@"
echo "Primer argumento: $1"
echo "Segundo argumento: $2"
EOF
chmod +x argumentos.sh
./argumentos.sh uno dos tres
```

#### Ejercicio 1.3: Condicionales
```bash
cat > esmayor.sh << 'EOF'
#!/bin/bash
if [ $# -ne 1 ]; then
    echo "Uso: $0 <edad>"
    exit 1
fi

if [ $1 -ge 18 ]; then
    echo "Eres mayor de edad"
else
    echo "Eres menor de edad"
fi
EOF
chmod +x esmayor.sh
./esmayor.sh 20
./esmayor.sh 15
```

#### Ejercicio 1.4: Bucles
```bash
cat > bucles.sh << 'EOF'
#!/bin/bash
echo "Bucle for:"
for i in 1 2 3 4 5; do
    echo "  Número: $i"
done

echo "Bucle while:"
contador=1
while [ $contador -le 5 ]; do
    echo "  Contador: $contador"
    ((contador++))
done
EOF
chmod +x bucles.sh
./bucles.sh
```

#### Ejercicio 1.5: Funciones
```bash
cat > funciones.sh << 'EOF'
#!/bin/bash

saludar() {
    local nombre=$1
    echo "Hola, $nombre!"
}

sumar() {
    local a=$1
    local b=$2
    echo $((a + b))
}

saludar "Estudiante"
resultado=$(sumar 5 3)
echo "5 + 3 = $resultado"
EOF
chmod +x funciones.sh
./funciones.sh
```

#### Ejercicio 1.6: Script práctico - Backup
```bash
cat > backup.sh << 'EOF'
#!/bin/bash

ORIGEN=$1
DESTINO=$2
FECHA=$(date +%Y%m%d_%H%M%S)

if [ $# -ne 2 ]; then
    echo "Uso: $0 <origen> <destino>"
    exit 1
fi

if [ ! -d "$ORIGEN" ]; then
    echo "Error: $ORIGEN no existe"
    exit 1
fi

mkdir -p "$DESTINO"
tar -czf "$DESTINO/backup_$FECHA.tar.gz" -C "$ORIGEN" .
echo "Backup creado: $DESTINO/backup_$FECHA.tar.gz"
EOF
chmod +x backup.sh
./backup.sh ~/practica_linux ~/backups
```

---

### Sección 2: Systemd y Servicios

> 📖 **Teoría:** [Guía Linux Avanzado - Systemd](Guia_Linux_Avanzado.md#systemd-y-servicios)

#### Ejercicio 2.1: Explorar servicios
```bash
systemctl list-units --type=service | head -20
systemctl status cron
systemctl is-active cron
systemctl is-enabled cron
```

#### Ejercicio 2.2: journalctl
```bash
journalctl | tail -50
journalctl -u cron | tail -20
journalctl --since "1 hour ago"
journalctl -p err | tail -20
```

#### Ejercicio 2.3: Crear servicio de usuario
```bash
mkdir -p ~/.config/systemd/user

cat > ~/.config/systemd/user/hola.service << 'EOF'
[Unit]
Description=Servicio Hola Mundo

[Service]
Type=oneshot
ExecStart=/bin/echo "Hola desde systemd"
EOF

systemctl --user daemon-reload
systemctl --user start hola
journalctl --user -u hola
```

---

### Sección 3: Gestión de Paquetes Avanzada

> 📖 **Teoría:** [Guía Linux Avanzado - Paquetes](Guia_Linux_Avanzado.md#gestión-de-paquetes-avanzada)

#### Ejercicio 3.1: APT
```bash
apt list --installed | head -20
apt search htop
apt show htop
```

#### Ejercicio 3.2: Verificar paquetes
```bash
dpkg -l | grep python
dpkg -L bash | head -20
which python3
dpkg -S $(which python3)
```

---

### Sección 4: Variables de Entorno

> 📖 **Teoría:** [Guía Linux Avanzado - Variables de Entorno](Guia_Linux_Avanzado.md#variables-de-entorno)

#### Ejercicio 4.1: Variables
```bash
echo $PATH
echo $HOME
echo $USER
env | grep PATH
```

#### Ejercicio 4.2: Exportar variables
```bash
export MI_VAR="Hola"
echo $MI_VAR
bash -c 'echo $MI_VAR'

export MI_VAR="Mundo"
bash -c 'echo $MI_VAR'
```

#### Ejercicio 4.3: Alias
```bash
alias
alias ll='ls -la'
ll
alias ..='cd ..'
..
```

#### Ejercicio 4.4: Configuración permanente
```bash
echo 'export MI_RUTA=$HOME/practica_linux' >> ~/.bashrc
echo 'alias c="clear"' >> ~/.bashrc
source ~/.bashrc
echo $MI_RUTA
c
```

---

### Sección 5: Diagnóstico del Sistema

> 📖 **Teoría:** [Guía Linux Avanzado - Diagnóstico](Guia_Linux_Avanzado.md#diagnóstico-del-sistema)

#### Ejercicio 5.1: Recursos del sistema
```bash
free -h
df -h
uptime
```

#### Ejercicio 5.2: Procesos que más consumen
```bash
ps aux --sort=-%mem | head -10
ps aux --sort=-%cpu | head -10
```

#### Ejercicio 5.3: Tamaño de directorios
```bash
du -sh ~
du -sh ~/practica_linux
du -h --max-depth=1 ~ | sort -hr | head -10
```

#### Ejercicio 5.4: Información del sistema
```bash
uname -a
lscpu | head -20
cat /proc/meminfo | head -10
lsblk
```

---

## Ejercicio Final Integrador

### Proyecto: Sistema de gestión de logs

Crea un script que:
1. Cree un directorio de logs si no existe
2. Genere un archivo de log con la fecha actual
3. Monitorice procesos y guarde información
4. Permita buscar en los logs

```bash
cat > ~/practica_linux/gestion_logs.sh << 'EOF'
#!/bin/bash

LOG_DIR="$HOME/logs_sistema"
mkdir -p "$LOG_DIR"

registrar() {
    local mensaje=$1
    local archivo="$LOG_DIR/log_$(date +%Y%m%d).log"
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $mensaje" >> "$archivo"
}

ver_logs() {
    local dias=${1:-7}
    find "$LOG_DIR" -name "*.log" -mtime -$dias -exec cat {} \;
}

buscar() {
    local patron=$1
    grep -r "$patron" "$LOG_DIR"
}

case "$1" in
    "registrar")
        registrar "$2"
        ;;
    "ver")
        ver_logs "$2"
        ;;
    "buscar")
        buscar "$2"
        ;;
    *)
        echo "Uso: $0 {registrar|ver|buscar}"
        echo "  registrar <mensaje>  - Registra un mensaje"
        echo "  ver [dias]           - Muestra logs de los últimos N días"
        echo "  buscar <patron>      - Busca patrón en logs"
        ;;
esac
EOF
chmod +x ~/practica_linux/gestion_logs.sh
```

**Tareas:**
1. Registra varios mensajes
2. Visualiza los logs
3. Busca un patrón específico

---

## Limpiar práctica

```bash
rm -rf ~/practica_linux
rm -rf ~/backups
rm -rf ~/logs_sistema
```
