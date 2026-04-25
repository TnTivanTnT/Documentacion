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

> 💡 **Práctica:** [Verifica tu instalación en Ejercicios ROS2 - Sección 1](Ejercicios_ROS2.md#sección-1-verificación-del-entorno)

---

## Primeros Pasos: Instalación y Entorno

Antes de sumergirte en el desarrollo con ROS2, es fundamental tener un entorno de trabajo correctamente configurado.

### 1. Instalación de ROS2 Humble

El primer paso es instalar ROS2 Humble en tu sistema. Hemos centralizado las instrucciones en una guía dedicada para mantener la documentación limpia y fácil de actualizar.

> 📖 **Instrucciones detalladas:** [Guía de Instalación de ROS2 Humble](Instalacion_ROS2.md)

### 2. Verificación del Entorno

Una vez instalado, es crucial verificar que las variables de entorno y los comandos de ROS2 funcionan correctamente.

> 💡 **Práctica:** [Verifica tu instalación en Ejercicios ROS2 - Sección 1](Ejercicios_ROS2.md#sección-1-verificación-del-entorno)

### 3. Variables de entorno importantes

La instalación de ROS2 configura varias variables de entorno clave. Estas son algunas de las más importantes que debes conocer:

| Variable            | Descripción                                       |
|---------------------|---------------------------------------------------|
| `ROS_DISTRO`        | La distribución de ROS2 que estás usando (ej. `humble`) |
| `ROS_VERSION`       | La versión mayor de ROS (en este caso, `2`)          |
| `AMENT_PREFIX_PATH` | Rutas donde ROS2 busca paquetes de `ament`         |
| `COLCON_PREFIX_PATH`| Rutas donde `colcon` busca workspaces compilados   |
| `ROS_DOMAIN_ID`     | ID para la comunicación en redes DDS (avanzado)     |

> Comprender estas variables te será útil a medida que trabajes con workspaces y paquetes más complejos.

---

## Tipos de Paquetes en ROS2

### Build Types disponibles

ROS2 soporta diferentes sistemas de build según el lenguaje:

| Build Type | Lenguaje | Uso Principal |
|------------|----------|---------------|
| `ament_python` | Python | Nodos y scripts Python |
| `ament_cmake` | C++ | Nodos C++, interfaces personalizadas |

### Cuándo usar cada tipo

**Usa `ament_python` cuando:**
- Escribes nodos en Python con rclpy
- Creas scripts de automatización
- Desarrollas launch files en Python
- Eres principiante en ROS2

**Usa `ament_cmake` cuando:**
- Escribes nodos en C++ con rclcpp
- Creas interfaces personalizadas (msg, srv, action) ← **Aunque uses Python**
- Necesitas máximo rendimiento
- Integras código C/C++ existente

> ⚠️ **Importante:** Aunque tus nodos estén en Python, las interfaces personalizadas (mensajes, servicios, acciones) DEBEN ser paquetes `ament_cmake` porque ROS2 genera código C++ automáticamente para comunicación eficiente.

---

## Crear Paquete Python (Recomendado para este curso)

### Comando de creación

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python mis_ejercicios
```

### Estructura generada

```
mis_ejercicios/
├── mis_ejercicios/
│   └── __init__.py
├── resource/
│   └── mis_ejercicios
├── test/
│   ├── test_copyright.py
│   ├── test_flake8.py
│   └── test_pep257.py
├── package.xml
├── setup.py
└── setup.cfg
```

### Archivos principales y su rol

#### `package.xml` vs `setup.py`: La Dualidad de Configuración

En un paquete Python, estos dos archivos trabajan juntos pero tienen responsabilidades distintas:

- **`package.xml` (Manifiesto del Paquete - "Qué necesita")**
  - **Función:** Declara las **dependencias externas** del paquete. Le dice a `rosdep` y a `colcon` qué otros paquetes de ROS2 o librerías del sistema deben estar presentes para que tu código funcione.
  - **Contenido clave:**
    - `<depend>`: Dependencias necesarias tanto para compilar como para ejecutar (ej: `rclpy`, `std_msgs`).
    - `<build_depend>`: Dependencias solo para compilar.
    - `<exec_depend>`: Dependencias solo para ejecutar.
    - `<test_depend>`: Dependencias para las pruebas.

- **`setup.py` (Script de Instalación - "Cómo se instala")**
  - **Función:** Es un script estándar de Python (usando `setuptools`) que le dice a `colcon` **cómo instalar tu código Python**. Define qué archivos van en qué directorios y, lo más importante, crea los ejecutables.
  - **Contenido clave:**
    - `packages`: Los módulos de Python a incluir.
    - `data_files`: Archivos que no son de Python (como `package.xml`, `launch files`, configs).
    - `entry_points`: **Aquí es donde tus nodos se convierten en ejecutables**. Le dice a ROS2 que el comando `mi_nodo` debe llamar a la función `main` de tu script `mi_nodo.py`.

> 💡 **En resumen:** `package.xml` es para el **ecosistema ROS2** (dependencias entre paquetes). `setup.py` es para el **ecosistema Python** (instalar y hacer ejecutables tus scripts).

---

A continuación, se muestra un ejemplo de cómo se ven estos archivos:

**package.xml** - Metadatos del paquete:
```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>mis_ejercicios</name>
  <version>0.0.1</version>
  <description>Mi paquete Python de ROS2</description>
  <maintainer email="usuario@email.com">Usuario</maintainer>
  <license>MIT</license>
  
  <depend>rclpy</depend>
  <depend>std_msgs</depend>
  
  <test_depend>ament_copyright</test_depend>
  <test_depend>ament_flake8</test_depend>
  <test_depend>ament_pep257</test_depend>
  
  <export>
    <build_type>ament_python</build_type>
  </export>
</package>
```

**setup.py** - Configuración de Python:
```python
from setuptools import setup

package_name = 'mis_ejercicios'

setup(
    name=package_name,
    version='0.0.1',
    packages=[package_name],
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
    ],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='Usuario',
    maintainer_email='usuario@email.com',
    description='Mi paquete Python de ROS2',
    license='MIT',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
            'mi_nodo = mis_ejercicios.mi_nodo:main',
        ],
    },
)
```

**Añadir nodo Python:**

Crear `mis_ejercicios/mis_ejercicios/mi_nodo.py`:
```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node

class MiNodo(Node):
    def __init__(self):
        super().__init__('mi_nodo')
        self.get_logger().info('¡Hola desde Python!')

def main(args=None):
    rclpy.init(args=args)
    nodo = MiNodo()
    rclpy.spin(nodo)
    nodo.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

Compilar y ejecutar:
```bash
cd ~/ros2_ws
colcon build --packages-select mis_ejercicios
source install/setup.bash
ros2 run mis_ejercicios mi_nodo
```

---

## Crear Paquete C++ (Referencia)

> 💡 **Nota:** Este curso se centra en Python con rclpy. Esta sección es solo para referencia si necesitas trabajar con C++.

### Comando de creación

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake mi_paquete_cpp
```

### Estructura generada

```
mi_paquete_cpp/
├── include/
│   └── mi_paquete_cpp/
│       └── mi_paquete_cpp.hpp
├── src/
│   └── mi_nodo.cpp
├── test/
├── CMakeLists.txt
└── package.xml
```

### Archivos principales

**CMakeLists.txt** - Sistema de build:
```cmake
cmake_minimum_required(VERSION 3.8)
project(mi_paquete_cpp)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)

add_executable(mi_nodo src/mi_nodo.cpp)
ament_target_dependencies(mi_nodo rclcpp std_msgs)

install(TARGETS
  mi_nodo
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()
```

**package.xml**:
```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>mi_paquete_cpp</name>
  <version>0.0.1</version>
  <description>Mi paquete C++ de ROS2</description>
  <maintainer email="usuario@email.com">Usuario</maintainer>
  <license>MIT</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <depend>rclcpp</depend>
  <depend>std_msgs</depend>

  <test_depend>ament_lint_auto</test_depend>
  <test_depend>ament_lint_common</test_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>
```

**Nodo C++ ejemplo** (`src/mi_nodo.cpp`):
```cpp
#include <rclcpp/rclcpp.hpp>

class MiNodo : public rclcpp::Node
{
public:
    MiNodo() : Node("mi_nodo")
    {
        RCLCPP_INFO(this->get_logger(), "¡Hola desde C++!");
    }
};

int main(int argc, char **argv)
{
    rclcpp::init(argc, argv);
    auto nodo = std::make_shared<MiNodo>();
    rclcpp::spin(nodo);
    rclcpp::shutdown();
    return 0;
}
```

Compilar y ejecutar:
```bash
cd ~/ros2_ws
colcon build --packages-select mi_paquete_cpp
source install/setup.bash
ros2 run mi_paquete_cpp mi_nodo
```

---

## Comparativa Rápida

| Aspecto | Python (rclpy) | C++ (rclcpp) |
|---------|----------------|--------------|
| Facilidad | ✅ Más fácil | ❌ Más complejo |
| Velocidad desarrollo | ✅ Rápido | ❌ Más lento |
| Rendimiento runtime | ❌ Menor | ✅ Mayor |
| Gestión memoria | ✅ Automática (GC) | ❌ Manual |
| Ideal para | Prototipos, scripts | Producción, tiempo real |
| Curva aprendizaje | ✅ Suave | ❌ Empinada |

> 🎯 **Recomendación para este curso:** Usa Python (`ament_python`) para todos tus nodos. Solo necesitarás `ament_cmake` cuando crees interfaces personalizadas.

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

`colcon` es la herramienta que se usa para compilar y construir los workspaces de ROS2.

```bash
colcon build                    # Construye todo el workspace
colcon build --packages-select <paquete>  # Solo un paquete
colcon build --symlink-install  # Desarrollo rápido para Python
source install/setup.bash       # Cargar el entorno del workspace
```

#### Flujo de Trabajo Eficiente con `--symlink-install`

Para los desarrolladores de Python, `colcon build --symlink-install` es fundamental. En lugar de copiar tus archivos Python a la carpeta `install`, crea enlaces simbólicos.

**¿Qué significa esto?** Puedes editar tus scripts Python y ejecutarlos directamente sin necesidad de volver a compilar cada vez.

```
+--------------------------+      +---------------------------+
|      Directorio 'src'    |      |    Directorio 'install'   |
| (donde escribes código)  |      |   (lo que ROS2 ejecuta)   |
|                          |      |                           |
|  mi_paquete/mi_nodo.py   |----->| mi_paquete/mi_nodo.py (Link)|
|       (Original)         |      |      (Enlace simbólico)   |
+--------------------------+      +---------------------------+
       ^
       |
  ¡Editas aquí y los cambios se reflejan al instante!
```

> 💡 **Práctica:** Practica con la CLI y el build system en [Ejercicios ROS2 - Sección 5](Ejercicios_ROS2.md#sección-5-herramientas-de-línea-de-comandos)

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
