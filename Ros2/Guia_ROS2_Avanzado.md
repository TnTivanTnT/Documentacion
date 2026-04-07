# Guía ROS2 - Nivel Avanzado

> 📚 [Volver al Índice de Documentación](../README.md)

## Índice
- [Interfaces Personalizadas](#interfaces-personalizadas)
- [Lifecycle Nodes](#lifecycle-nodes)
- [Composition](#composition)
- [Herramientas de Debugging](#herramientas-de-debugging)
- [Buenas Prácticas](#buenas-prácticas)

---

## Interfaces Personalizadas

### Crear mensajes personalizados (msg)

### Estructura del paquete de interfaces

```
mis_interfaces/
├── CMakeLists.txt
├── package.xml
├── msg/
│   ├── MiMensaje.msg
│   └── Coordenada.msg
├── srv/
│   └── MiServicio.srv
└── action/
    └── MiAccion.action
```

### Crear paquete de interfaces

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake mis_interfaces
mkdir -p mis_interfaces/{msg,srv,action}
```

### Definir mensaje (msg/MiMensaje.msg)

```
string nombre
int32 edad
float64[] datos
geometry_msgs/Pose pose
```

### Definir servicio (srv/MiServicio.srv)

```
# Request
string comando
int32 valor
---
# Response
bool exito
string mensaje
int32[] resultado
```

### Definir acción (action/MiAccion.action)

```
# Goal
int32 objetivo
---
# Result
int32[] secuencia
int32 total
---
# Feedback
int32 actual
float32 progreso
```

### CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.8)
project(mis_interfaces)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(geometry_msgs REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/MiMensaje.msg"
  "srv/MiServicio.srv"
  "action/MiAccion.action"
  DEPENDENCIES geometry_msgs
)

ament_export_dependencies(rosidl_default_runtime)
ament_package()
```

### package.xml

```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>mis_interfaces</name>
  <version>0.0.1</version>
  <description>Interfaces personalizadas</description>
  <maintainer email="usuario@email.com">Usuario</maintainer>
  <license>MIT</license>

  <buildtool_depend>ament_cmake</buildtool_depend>
  <buildtool_depend>rosidl_default_generators</buildtool_depend>
  
  <depend>geometry_msgs</depend>
  
  <exec_depend>rosidl_default_runtime</exec_depend>
  
  <member_of_group>rosidl_interface_packages</member_of_group>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>
```

### Usar interfaces personalizadas

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from mis_interfaces.msg import MiMensaje

class MiNodo(Node):
    def __init__(self):
        super().__init__('mi_nodo')
        self.pub = self.create_publisher(MiMensaje, 'topic', 10)
        
        msg = MiMensaje()
        msg.nombre = "Robot"
        msg.edad = 5
        msg.datos = [1.0, 2.0, 3.0]
        self.pub.publish(msg)
```

> 💡 **Práctica:** Crea interfaces en [Ejercicios ROS2 - Sección 1](Ejercicios_ROS2.md#sección-1-interfaces-personalizadas)

---

## Lifecycle Nodes

### ¿Qué es un Lifecycle Node?

Los lifecycle nodes son nodos con estados definidos que permiten gestionar su ciclo de vida de forma controlada.

### Estados del Lifecycle

```
                    ┌──────────────┐
            ┌──────>│   Inactive   │<──────┐
            │       └──────────────┘       │
            │              │               │
     activate│              │deactivate    │cleanup
            │              ▼               │
┌──────────────┐    ┌──────────────┐       │
│   Unconfigured│<───│    Active    │<──────┘
└──────────────┘    └──────────────┘
       │                   ▲
       │configure          │activate
       ▼                   │
┌──────────────┐           │
│   Configured │───────────┘
└──────────────┘
```

### Estados y transiciones

| Estado | Descripción |
|--------|-------------|
| Unconfigured | Nodo creado, sin configurar |
| Inactive | Configurado, pero no ejecutando |
| Active | Totalmente operativo |
| Finalized | Nodo destruido |

| Transición | Descripción |
|------------|-------------|
| configure | Unconfigured → Inactive |
| cleanup | Inactive → Unconfigured |
| activate | Inactive → Active |
| deactivate | Active → Inactive |

### Implementar Lifecycle Node

```python
#!/usr/bin/env python3
import rclpy
from rclpy.lifecycle import LifecycleNode, LifecycleState, TransitionCallbackReturn
from std_msgs.msg import String

class MiLifecycleNode(LifecycleNode):
    def __init__(self):
        super().__init__('mi_lifecycle_node')
        self.publisher = None
        self.timer = None
        self.count = 0

    def on_configure(self, state: LifecycleState) -> TransitionCallbackReturn:
        self.get_logger().info('Configurando...')
        self.publisher = self.create_publisher(String, 'lifecycle_topic', 10)
        return TransitionCallbackReturn.SUCCESS

    def on_activate(self, state: LifecycleState) -> TransitionCallbackReturn:
        self.get_logger().info('Activando...')
        self.timer = self.create_timer(1.0, self.publish_callback)
        return super().on_activate(state)

    def on_deactivate(self, state: LifecycleState) -> TransitionCallbackReturn:
        self.get_logger().info('Desactivando...')
        self.timer.cancel()
        return super().on_deactivate(state)

    def on_cleanup(self, state: LifecycleState) -> TransitionCallbackReturn:
        self.get_logger().info('Limpiando...')
        self.publisher.destroy()
        self.publisher = None
        return TransitionCallbackReturn.SUCCESS

    def publish_callback(self):
        msg = String()
        msg.data = f'Mensaje: {self.count}'
        self.publisher.publish(msg)
        self.count += 1

def main(args=None):
    rclpy.init(args=args)
    node = MiLifecycleNode()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Comandos de lifecycle

```bash
ros2 lifecycle list
ros2 lifecycle nodes
ros2 lifecycle get /mi_lifecycle_node
ros2 lifecycle set /mi_lifecycle_node configure
ros2 lifecycle set /mi_lifecycle_node activate
ros2 lifecycle set /mi_lifecycle_node deactivate
```

> 💡 **Práctica:** Ejercita lifecycle en [Ejercicios ROS2 - Sección 2](Ejercicios_ROS2.md#sección-2-lifecycle-nodes)

---

## Composition

### ¿Qué es Composition?

Composition permite ejecutar múltiples nodos en un mismo proceso, reduciendo overhead de comunicación.

### Ventajas

| Ventaja | Descripción |
|---------|-------------|
| Eficiencia | Menos procesos, menos memoria |
| Latencia | Comunicación intra-proceso más rápida |
| Optimización | Mejor uso de recursos |

### Composition manual

```python
#!/usr/bin/env python3
import rclpy
from rclpy.executors import SingleThreadedExecutor
from mi_paquete.publisher import PublisherNode
from mi_paquete.subscriber import SubscriberNode

def main(args=None):
    rclpy.init(args=args)
    
    executor = SingleThreadedExecutor()
    
    publisher = PublisherNode()
    subscriber = SubscriberNode()
    
    executor.add_node(publisher)
    executor.add_node(subscriber)
    
    try:
        executor.spin()
    finally:
        publisher.destroy_node()
        subscriber.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Composition con MultiThreadedExecutor

```python
#!/usr/bin/env python3
import rclpy
from rclpy.executors import MultiThreadedExecutor
from mi_paquete.node1 import Node1
from mi_paquete.node2 import Node2

def main(args=None):
    rclpy.init(args=args)
    
    executor = MultiThreadedExecutor(num_threads=4)
    
    node1 = Node1()
    node2 = Node2()
    
    executor.add_node(node1)
    executor.add_node(node2)
    
    try:
        executor.spin()
    finally:
        node1.destroy_node()
        node2.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Composition en C++ (componentes)

```bash
ros2 run rclcpp_components component_container
ros2 component load /ComponentManager composition_nodes Talker
ros2 component load /ComponentManager composition_nodes Listener
```

> 💡 **Práctica:** Ejercita composition en [Ejercicios ROS2 - Sección 3](Ejercicios_ROS2.md#sección-3-composition)

---

## Herramientas de Debugging

### rqt

Interfaz gráfica para debugging.

```bash
rqt
rqt_graph
rqt_plot
rqt_console
rqt_image_view
```

### rqt_graph

Visualiza el grafo de nodos y topics.

```bash
rqt_graph
```

### rqt_plot

Grafica datos de topics en tiempo real.

```bash
rqt_plot /topic/field
rqt_plot /cmd_vel/linear/x /cmd_vel/angular/z
```

### rviz2

Visualizador 3D para datos de sensores.

```bash
rviz2
```

### tf2_tools

Herramientas para transformaciones.

```bash
ros2 run tf2_tools view_frames
ros2 run tf2_ros tf2_echo base_link laser
```

### Comandos de debugging

| Comando | Descripción |
|---------|-------------|
| `ros2 node list` | Lista nodos |
| `ros2 topic hz /topic` | Frecuencia de topic |
| `ros2 topic bw /topic` | Ancho de banda |
| `ros2 topic info /topic -v` | Info detallada |
| `ros2 doctor` | Diagnóstico del sistema |
| `ros2 wtf` | Alias de doctor |

### Niveles de log

```python
self.get_logger().debug('Mensaje debug')
self.get_logger().info('Mensaje info')
self.get_logger().warning('Mensaje warning')
self.get_logger().error('Mensaje error')
self.get_logger().fatal('Mensaje fatal')
```

### Configurar nivel de log

```bash
ros2 run mi_paquete mi_nodo --ros-args --log-level debug
ros2 run mi_paquete mi_nodo --ros-args --log-level mi_nodo:=debug
```

### Análisis de rendimiento

```bash
ros2 topic hz /scan
ros2 topic delay /scan
ros2 topic bw /scan
```

> 💡 **Práctica:** Usa herramientas de debugging en [Ejercicios ROS2 - Sección 4](Ejercicios_ROS2.md#sección-4-herramientas-de-debugging)

---

## Buenas Prácticas

### Estructura de código

```python
#!/usr/bin/env python3
"""
Módulo: mi_nodo.py
Descripción: Nodo de ejemplo con buenas prácticas
Autor: Tu nombre
Fecha: 2024
"""

import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class MiNodo(Node):
    """
    Nodo de ejemplo que demuestra buenas prácticas.
    """
    
    def __init__(self):
        super().__init__('mi_nodo')
        
        self._init_parameters()
        self._init_publishers()
        self._init_subscribers()
        self._init_timers()
        
        self.get_logger().info('Nodo inicializado correctamente')

    def _init_parameters(self):
        """Inicializa parámetros del nodo."""
        self.declare_parameter('frecuencia', 1.0)
        self.declare_parameter('topic_name', 'chatter')
        
        self._frecuencia = self.get_parameter('frecuencia').value
        self._topic_name = self.get_parameter('topic_name').value

    def _init_publishers(self):
        """Inicializa publishers."""
        self._publisher = self.create_publisher(
            String, 
            self._topic_name, 
            10
        )

    def _init_subscribers(self):
        """Inicializa subscribers."""
        pass

    def _init_timers(self):
        """Inicializa timers."""
        self._timer = self.create_timer(
            1.0 / self._frecuencia, 
            self._timer_callback
        )

    def _timer_callback(self):
        """Callback del timer."""
        msg = String()
        msg.data = 'Hola mundo'
        self._publisher.publish(msg)
        self.get_logger().debug(f'Publicado: {msg.data}')

    def destroy_node(self):
        """Limpieza antes de destruir el nodo."""
        self.get_logger().info('Destruyendo nodo...')
        super().destroy_node()


def main(args=None):
    rclpy.init(args=args)
    
    try:
        node = MiNodo()
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    except Exception as e:
        print(f'Error: {e}')
    finally:
        if rclpy.ok():
            node.destroy_node()
            rclpy.shutdown()


if __name__ == '__main__':
    main()
```

### Convenciones de nomenclatura

| Elemento | Convención | Ejemplo |
|----------|------------|---------|
| Paquetes | snake_case | `mi_paquete` |
| Nodos | snake_case | `mi_nodo` |
| Topics | snake_case | `/mi_topic` |
| Servicios | snake_case | `/mi_servicio` |
| Mensajes | PascalCase | `MiMensaje.msg` |
| Clases | PascalCase | `MiNodo` |
| Funciones | snake_case | `mi_funcion` |
| Variables | snake_case | `mi_variable` |
| Constantes | UPPER_CASE | `MI_CONSTANTE` |

### QoS (Quality of Service)

```python
from rclpy.qos import QoSProfile, ReliabilityPolicy, DurabilityPolicy

qos_profile = QoSProfile(
    reliability=ReliabilityPolicy.BEST_EFFORT,
    durability=DurabilityPolicy.VOLATILE,
    depth=10
)

self.subscription = self.create_subscription(
    String,
    'topic',
    self.callback,
    qos_profile
)
```

| Política | Valores | Uso típico |
|----------|---------|------------|
| Reliability | RELIABLE, BEST_EFFORT | Sensores: BEST_EFFORT |
| Durability | VOLATILE, TRANSIENT_LOCAL | TRANSIENT_LOCAL para config |
| Depth | Número | Cola de mensajes |

> 💡 **Práctica:** Aplica buenas prácticas en [Ejercicios ROS2 - Sección 5](Ejercicios_ROS2.md#sección-5-buenas-prácticas)

---

## Resumen de comandos avanzados

| Categoría | Comandos/Herramientas |
|-----------|----------------------|
| Interfaces | `ros2 interface show`, creación de msg/srv/action |
| Lifecycle | `ros2 lifecycle set/get` |
| Debugging | `rqt`, `rviz2`, `ros2 doctor` |
| Log | `--log-level`, `get_logger()` |

---

## Recursos adicionales

- [Guía ROS2 - Nivel Principiante](Guia_ROS2_Principiante.md)
- [Guía ROS2 - Nivel Intermedio](Guia_ROS2_Intermedio.md)
- [Documentación oficial ROS2](https://docs.ros.org/en/humble/)
- [Tutoriales ROS2](https://docs.ros.org/en/humble/Tutorials.html)
