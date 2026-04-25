# Ejercicios Prácticos - Linux

> 📚 [Volver al Índice de Documentación](../README.md)

Este documento contiene ejercicios prácticos para dominar el uso de la terminal de Linux.

---

## Nivel Principiante

### Sección 1: Introducción a la Terminal

> 📖 **Teoría:** [Guía Linux Principiante - Introducción](Guia_Linux_Principiante.md#introducción-a-la-terminal)

#### Ejercicio 1.1: Atajos y Navegación Básica
1. Abre una terminal.
2. Usa `Ctrl + L` para limpiar la pantalla.
3. Presiona `Tab` dos veces para ver comandos sugeridos.

#### Ejercicio 1.2: El Historial
1. Ejecuta el comando `history`.
2. Busca un comando usado anteriormente.
3. Prueba el atajo `Ctrl + R` para buscar un comando por palabras clave.

#### Ejercicio 1.3: Superusuario (sudo)
1. Intenta crear un archivo en una carpeta de sistema: `touch /etc/test.txt` (Dará error).
2. Usa `sudo` para hacerlo: `sudo touch /etc/test_sudo.txt`.
3. Borra el archivo: `sudo rm /etc/test_sudo.txt`.

---

### Sección 2: Gestión de Archivos y Carpetas

#### Ejercicio 2.1: Estructura de carpetas
```bash
mkdir -p ~/practica_linux/{documentos,datos}
cd ~/practica_linux
ls -R
```

#### Ejercicio 2.2: Manipular archivos
```bash
touch nota.txt
cp nota.txt copia_nota.txt
mv nota.txt archivo_final.txt
rm copia_nota.txt
```

#### Ejercicio 2.3: Comodines (Wildcards)
1. Crea archivos: `touch file1.log file2.log data1.txt`.
2. Lista solo los archivos log: `ls *.log`.
3. Borra los logs: `rm *.log`.

---

### Sección 3: Permisos y Grupos

#### Ejercicio 3.1: Permisos de ejecución
```bash
touch script.sh
echo 'echo "Hola Terminal"' > script.sh
chmod +x script.sh
./script.sh
```

#### Ejercicio 3.2: Grupos de Usuario
1. Mira tus grupos actuales: `groups`.
2. Para añadirte a un nuevo grupo (ejemplo: `dialout` para USB): `sudo usermod -aG dialout $USER`.

---

### Sección 4: Visualización y Edición

#### Ejercicio 4.1: Ver contenido
```bash
cat /etc/hostname
head -n 5 /etc/passwd
```

#### Ejercicio 4.2: Edición con Nano
1. Abre un archivo: `nano mi_archivo.txt`.
2. Escribe una línea, guarda con `Ctrl+O` y sal con `Ctrl+X`.

---

## Nivel Intermedio

### Sección 1: Pipes y Redirecciones

#### Ejercicio 1.1: Filtrado de salida
```bash
ls /etc | grep ".conf"
ls /etc | tee listado_carpetas.txt
```

#### Ejercicio 1.2: Monitoreo con watch
1. Ejecuta: `watch -n 1 date`.
2. Sal con `Ctrl+C`.

---

### Sección 2: Procesos y Conectividad

#### Ejercicio 2.1: Gestión de procesos
1. Lanza un proceso en segundo plano: `sleep 100 &`.
2. Identifica su PID con `ps aux | grep sleep`.
3. Mátalo: `kill <PID>`.

#### Ejercicio 2.2: Red básica
1. Mira tu dirección IP: `hostname -I`.
2. Verifica conexión: `ping -c 3 google.com`.

---

## Nivel Avanzado

### Sección 1: Scripting y Diagnóstico

#### Ejercicio 1.1: Scripting Robusto
Crea `test_script.sh`:
```bash
#!/bin/bash
set -e
echo "Iniciando proceso..."
ls /ruta/que/no/existe  # El script fallará y se detendrá aquí por el 'set -e'
echo "Esto no se verá."
```

#### Ejercicio 1.2: Diagnóstico de Hardware
1. Monitorea logs del sistema: `sudo dmesg -w`.
2. Conecta un ratón o pendrive y observa los mensajes.

#### Ejercicio 1.3: Espacio y Limpieza
1. Mira espacio libre: `df -h`.
2. Busca carpetas pesadas: `du -hs ~/* | sort -rh | head -5`.

---

## Limpiar práctica
```bash
rm -rf ~/practica_linux
rm -f ~/listado_carpetas.txt
rm -f ~/mi_archivo.txt
```
