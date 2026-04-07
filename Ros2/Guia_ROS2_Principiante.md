# Guía ROS2 - Nivel Principiante

> 📚 [Volver al Índice de Documentación](../README.md)

## Índice
- [Introducción a ROS2](#introducción-a-ros2)
- [Instalación de ROS2 Humble](#instalación-de-ros2-humble)
- [Conceptos Fundamentales](#conceptos-fundamentales)
- [Nodos en ROS2](#nodos-en-ros2)
- [Topics y Mensajes](#topics-y-mensajes)
- [Herramientas de Línea de Comandos](#herramientas-de-línea-de-comandos)

---

## Introducción a ROS2

### ¿Qué es ROS2?

ROS2 (Robot Operating System 2) es un framework de desarrollo de software para robots. No es un sistema operativo, sino un conjunto de bibliotecas y herramientas que facilitan la creación de aplicaciones robóticas.

### ¿Por qué ROS2 Humble?

| Característica | Descripción |
|----------------|-------------|
| LTS | Soporte a largo plazo hasta 2027 |
| Ubuntu 22.04 | Diseñado para esta versión |
| Estabilidad | Ideal para producción |
| Compatibilidad | Amplio soporte de paquetes |

### Diferencias principales con ROS1

| Aspecto | ROS1 | ROS2 |
|---------|------|------|
| Middleware | Custom | DDS (estándar) |
| Tiempo real | Limitado | Nativo |
| Multi-robot | Difícil | Nativo |
| Seguridad | Básica | SROS2 integrado |
| Calidad de servicio | No | Sí (QoS) |

> 💡 **Práctica:** Ejercita la instalación en [Ejercicios ROS2 - Sección 1](Ejercicios_ROS2.md#sección-1-instalación-y-configuración)

---

## Instalación de ROS2 Humble

### Instalación en Ubuntu 22.04

```bash
sudo apt update && sudo apt install -y curl gnupg lsb-release
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
sudo apt update
sudo apt install -y ros-humble-desktop
```

### Configuración del entorno

```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### Verificar instalación

```bash
ros2 --version
printenv | grep -i ROS
```

### Variables de entorno importantes

| Variable | Descripción |
|----------|-------------|
| `ROS_DISTRO` | Distribución de ROS2 |
| `ROS_VERSION` | Versión de ROS |
| `AMENT_PREFIX_PATH` | Rutas de paquetes |
| `COLCON_PREFIX_PATH` | Workspace de colcon |
| `ROS_DOMAIN_ID` | Dominio de comunicación DDS |

> 💡 **Práctica:** Verifica tu instalación en [Ejercicios ROS2 - Sección 1](Ejercicios_ROS2.md#sección-1-instalación-y-configuración)

---

## Conceptos Fundamentales

### Arquitectura de ROS2

```
┌─────────────────────────────────────┐
│           Aplicación                │
├─────────────────────────────────────┤
│           rclpy / rclcpp            │
├─────────────────────────────────────┤
│           RMW (DDS)                 │
├─────────────────────────────────────┤
│         Sistema Operativo           │
└─────────────────────────────────────┘
```

### Conceptos principales

| Concepto | Descripción |
|----------|-------------|
| **Nodo** | Unidad de procesamiento que realiza cálculos |
| **Topic** | Canal de comunicación para mensajes |
| **Mensaje** | Estructura de datos intercambiada |
| **Service** | Comunicación request/response |
| **Action** | Tareas de larga duración con feedback |
| **Parameter** | Configuración de nodos |

### Comunicación

```
Publisher ──[Topic]──> Subscriber
Client ──[Service]──> Server
Client ──[Action]──> Server (con feedback continuo)
```

> 💡 **Práctica:** Explora los conceptos en [Ejercicios ROS2 - Sección 2](Ejercicios_ROS2.md#sección-2-conceptos-fundamentales)

---

## Nodos en ROS2

### ¿Qué es un nodo?

Un nodo es un proceso que participa en el grafo de ROS2. Cada nodo puede:
- Publicar en topics
- Suscribirse a topics
- Ofrecer servicios
- Usar servicios
- Usar acciones
- Gestionar parámetros

### Estructura básica de un nodo (Python)

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node

class MiNodo(Node):
    def __init__(self):
        super().__init__('mi_nodo')
        self.get_logger().info('¡Nodo iniciado!')

def main(args=None):
    rclpy.init(args=args)
    nodo = MiNodo()
    rclpy.spin(nodo)
    nodo.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Estructura de un paquete ROS2

```
mi_paquete/
├── mi_paquete/
│   └── __init__.py
├── resource/
│   └── mi_paquete
├── test/
├── package.xml
├── setup.py
└── setup.cfg
```

### Comandos de nodos

| Comando | Descripción |
|---------|-------------|
| `ros2 node list` | Lista nodos activos |
| `ros2 node info <nodo>` | Info del nodo |
| `ros2 run <paquete> <nodo>` | Ejecuta un nodo |

> 💡 **Práctica:** Crea tu primer nodo en [Ejercicios ROS2 - Sección 3](Ejercicios_ROS2.md#sección-3-nodos-en-ros2)

---

## Topics y Mensajes

### Topics

Los topics son canales de comunicación asíncrona donde los nodos publican y reciben mensajes.

```
┌──────────┐         ┌──────────────┐         ┌──────────┐
│Publisher ├──[Topic]──>│   Mensaje   │──[Topic]──>│Subscriber│
└──────────┘         └──────────────┘         └──────────┘
```

### Tipos de mensajes comunes

| Tipo | Descripción |
|------|-------------|
| `std_msgs/String` | Cadena de texto |
| `std_msgs/Int32` | Entero |
| `std_msgs/Float32` | Flotante |
| `std_msgs/Bool` | Booleano |
| `geometry_msgs/Twist` | Velocidad lineal y angular |
| `sensor_msgs/Image` | Imagen |
| `sensor_msgs/LaserScan` | Datos de láser |

### Ver tipos de mensajes disponibles

```bash
ros2 interface list | grep std_msgs
ros2 interface show std_msgs/msg/String
ros2 interface show geometry_msgs/msg/Twist
```

### Publisher básico

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class PublisherNode(Node):
    def __init__(self):
        super().__init__('publisher_node')
        self.publisher = self.create_publisher(String, 'topic', 10)
        timer_period = 0.5
        self.timer = self.create_timer(timer_period, self.timer_callback)
        self.i = 0

    def timer_callback(self):
        msg = String()
        msg.data = f'Hola mundo: {self.i}'
        self.publisher.publish(msg)
        self.get_logger().info(f'Publicando: "{msg.data}"')
        self.i += 1

def main(args=None):
    rclpy.init(args=args)
    node = PublisherNode()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Subscriber básico

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class SubscriberNode(Node):
    def __init__(self):
        super().__init__('subscriber_node')
        self.subscription = self.create_subscription(
            String,
            'topic',
            self.listener_callback,
            10)
        self.subscription

    def listener_callback(self, msg):
        self.get_logger().info(f'Recibido: "{msg.data}"')

def main(args=None):
    rclpy.init(args=args)
    node = SubscriberNode()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Comandos de topics

| Comando | Descripción |
|---------|-------------|
| `ros2 topic list` | Lista topics activos |
| `ros2 topic info <topic>` | Info del topic |
| `ros2 topic echo <topic>` | Muestra mensajes |
| `ros2 topic pub <topic> <tipo> <datos>` | Publica mensaje |
| `ros2 topic hz <topic>` | Frecuencia de publicación |
| `ros2 topic bw <topic>` | Ancho de banda |

> 💡 **Práctica:** Crea publisher y subscriber en [Ejercicios ROS2 - Sección 4](Ejercicios_ROS2.md#sección-4-topics-y-mensajes)

---

## Herramientas de Línea de Comandos

### Comandos principales de ros2

| Comando | Descripción |
|---------|-------------|
| `ros2 run` | Ejecuta un ejecutable |
| `ros2 launch` | Lanza múltiples nodos |
| `ros2 node` | Gestión de nodos |
| `ros2 topic` | Gestión de topics |
| `ros2 service` | Gestión de servicios |
| `ros2 action` | Gestión de acciones |
| `ros2 param` | Gestión de parámetros |
| `ros2 pkg` | Gestión de paquetes |
| `ros2 interface` | Ver interfaces |

### Ejemplos de uso

```bash
ros2 run demo_nodes_cpp talker
ros2 run demo_nodes_cpp listener
ros2 topic list
ros2 topic echo /chatter
ros2 topic pub /chatter std_msgs/msg/String "data: 'Hola'"
```

### Colcon - Sistema de build

```bash
colcon build                    # Construye todo
colcon build --packages-select <paquete>  # Solo un paquete
colcon build --symlink-install  # Enlace simbólico para Python
source install/setup.bash       # Cargar workspace
```

> 💡 **Práctica:** Practica con CLI en [Ejercicios ROS2 - Sección 5](Ejercicios_ROS2.md#sección-5-herramientas-de-línea-de-comandos)

---

## Resumen de comandos esenciales

| Categoría | Comandos |
|-----------|----------|
| Nodos | `ros2 node list`, `ros2 node info` |
| Topics | `ros2 topic list`, `ros2 topic echo`, `ros2 topic pub` |
| Interfaces | `ros2 interface list`, `ros2 interface show` |
| Build | `colcon build` |
| Ejecución | `ros2 run`, `ros2 launch` |

---

## Siguientes pasos

Continúa con [Guía ROS2 - Nivel Intermedio](Guia_ROS2_Intermedio.md) para aprender sobre Services, Actions y Launch Files.
