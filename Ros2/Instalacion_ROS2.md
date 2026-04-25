> 📚 [Volver al Índice de Documentación](../README.md)

Instalación basada en la web oficial de [ros2](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html)

# Set locale

```bash
locale  # check for UTF-8

sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

locale  # verify settings
```

# Setup Sources

```bash
sudo apt install software-properties-common
sudo add-apt-repository universe

sudo apt update && sudo apt install curl -y
export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F'"' '{print $4}')
curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo ${UBUNTU_CODENAME:-${VERSION_CODENAME}})_all.deb"
sudo dpkg -i /tmp/ros2-apt-source.deb
```

# Install ROS 2 packages

```bash
sudo apt update
sudo apt upgrade
```

Escogeremos escritorio o base pero siempre hay que instalar también las devtools, da igual que versión se escoja antes.
```bash
# Escritorio, dispositivos con interfaz gráfica
sudo apt install ros-humble-desktop
```

```bash
# Barebones, dispositivos sin interfaz gráficas (versiones server)
sudo apt install ros-humble-ros-base
```

```bash
# Devtools
sudo apt install ros-dev-tools
# Añadir el source a bash para no tener que hacerlo siempre
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
```

---

## Post-Instalación: Aislamiento de Red (Opcional pero recomendado)

Por defecto, todos los dispositivos ROS2 en la misma red local pueden verse entre sí. Esto puede causar interferencias si varios usuarios o robots están operando simultáneamente.

Para aislar tu sistema, puedes configurar la variable de entorno `ROS_DOMAIN_ID`.

1.  **Elige un número:** Escoge un número entre `0` y `101`. Por ejemplo, tu número de puesto en la oficina.
2.  **Añádelo a tu `.bashrc`:**
    ```bash
    echo "export ROS_DOMAIN_ID=7" >> ~/.bashrc # Cambia 7 por tu número
    source ~/.bashrc
    ```

Ahora, tu sistema ROS2 solo se comunicará con otros dispositivos que usen el mismo `ROS_DOMAIN_ID`.

> ⚠️ **Importante:** Después de modificar `.bashrc`, debes reiniciar tu terminal o ejecutar `source ~/.bashrc` en cada pestaña abierta para que los cambios surtan efecto.

---

## Entendiendo el "Sourcing": Underlay y Overlay

Has añadido `source /opt/ros/humble/setup.bash` a tu `.bashrc`. Esto carga el **entorno base de ROS2 (underlay)** cada vez que abres una terminal, dándote acceso a todos los comandos de ROS2.

Cuando crees tus propios proyectos en un workspace (ej: `~/ros2_ws`), compilarás tus paquetes. Para que ROS2 los encuentre, necesitarás ejecutar otro comando `source` en tu terminal:

```bash
# Dentro de tu workspace, después de compilar
source install/setup.bash
```

Este segundo `source` carga tu workspace como una **capa superior (overlay)**. ROS2 buscará primero en tu workspace y luego en el entorno base.

| Sourcing | ¿Qué hace? | ¿Cuándo? |
| :--- | :--- | :--- |
| **`source /opt/ros/humble/setup.bash`** | Carga el entorno de ROS2 | Automáticamente en cada terminal (gracias al `.bashrc`) |
| **`source ~/ros2_ws/install/setup.bash`** | Carga los paquetes de tu workspace | **Manualmente**, en cada terminal donde quieras usar tus nodos |

