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

> ⚠️ **Importante - Interfaces vs Nodos:** Aunque desarrolles todos tus nodos en Python, los paquetes de interfaces personalizadas (mensajes, servicios, acciones) DEBEN ser `ament_cmake`.

### ¿Por qué ament_cmake para interfaces?

Las interfaces personalizadas requieren `ament_cmake` porque:

1. **Generación de código:** ROS2 genera código C++ automáticamente desde las definiciones `.msg`, `.srv`, `.action`
2. **Eficiencia:** El middleware DDS usa este código C++ para comunicación eficiente
3. **Compatibilidad:** Las interfaces pueden ser usadas tanto por nodos Python como C++
4. **Build time:** Las interfaces se compilan una vez y se instalan en el sistema

### Flujo de trabajo recomendado

```
Workspace ROS2
├── mis_interfaces/          ← ament_cmake (msg/srv/action)
│   ├── msg/
│   │   └── RobotStatus.msg
│   ├── srv/
│   │   └── CalcularRuta.srv
│   ├── action/
│   │   └── Navegar.action
│   ├── CMakeLists.txt
│   └── package.xml
│
└── mi_robot/                ← ament_python (nodos)
    ├── mi_robot/
    │   ├── nodo_control.py
    │   └── nodo_sensor.py
    ├── package.xml
    └── setup.py
```

### Proceso completo

1. **Crear paquete de interfaces** (`ament_cmake`)
2. **Definir interfaces** (archivos .msg, .srv, .action)
3. **Configurar CMakeLists.txt** para generar código
4. **Compilar** el paquete de interfaces
5. **Usar en nodos Python** importando las interfaces generadas

> 💡 **Tip:** Compila primero el paquete de interfaces antes que el paquete de nodos. Los nodos Python dependerán de las interfaces generadas.

> ⚠️ **Advertencia de Nomenclatura:** Al nombrar tus archivos de interfaz (ej. `MiMensaje.msg`), usa siempre `PascalCase`. **Nunca uses guiones (`-`) o `snake_case`**. Un nombre como `mi-mensaje.msg` causará errores de compilación crípticos porque el generador de código no puede procesarlo correctamente.

---

### Proceso para Crear un Paquete de Interfaces

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake mis_interfaces
mkdir -p mis_interfaces/{msg,srv,action}
```

### Definir mensaje (msg/MiMensaje.msg)

#### Tabla de Tipos Primitivos

Al definir interfaces, usarás tipos de datos estándar de ROS. Aquí tienes una tabla de correspondencia con los tipos de Python:

| Tipo ROS | Tipo Python | Descripción |
| :--- | :--- | :--- |
| `bool` | `bool` | Booleano |
| `byte` | `bytes` | Un byte |
| `char` | `str` (1 char) | Un caracter |
| `float32` | `float` | Flotante de 32-bit |
| `float64` | `float` | Flotante de 64-bit |
| `int8` | `int` | Entero de 8-bit con signo |
| `uint8` | `int` | Entero de 8-bit sin signo |
| `int16` | `int` | Entero de 16-bit con signo |
| `uint16`| `int` | Entero de 16-bit sin signo |
| `int32` | `int` | Entero de 32-bit con signo |
| `uint32`| `int` | Entero de 32-bit sin signo |
| `int64` | `int` | Entero de 64-bit con signo |
| `uint64`| `int` | Entero de 64-bit sin signo |
| `string`| `str` | Cadena de texto |

**Arrays:** Puedes definir arrays simplemente añadiendo `[]` al final del tipo (ej: `int32[]`). También puedes definir arrays de tamaño fijo (ej: `int32[5]`).

```text
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
 
   <build_depend>rosidl_default_generators</build_depend>
 
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
      activate│              │deactivate     │
             │              ▼               │
 ┌──────────────┐    ┌──────────────┐       │
 │   Unconfigured│<───│    Active    │<──────┘
 └──────────────┘    └──────────────┘
        │                   ▲
        │configure          │cleanup
        ▼                   │
 ┌──────────────┐           │
 │   Inactive   │───────────┘
 └──────────────┘
 
 > ⚠️ **Corrección:** El diagrama anterior es una representación común pero conceptualmente imprecisa. Un diagrama de estados más fiel a la implementación real es el siguiente. La transición `cleanup` va de `Inactive` a `Unconfigured`.

 ```
                     ┌──────────────┐
             ┌──────>│   Inactive   │<──────┐
             │       └──────────────┘       │
             │              │               │
      activate│              │deactivate     │cleanup
             │              ▼               │
 ┌──────────────┐    ┌──────────────┐       │
 │   Configured │<───│    Active    │       │
 └──────────────┘    └──────────────┘       │
        ▲                                   │
        │configure                          │
        │                                   │
 ┌──────────────┐                           │
 │ Unconfigured │<──────────────────────────┘
 └──────────────┘
 ```

 ### Estados y transiciones

 | Estado | Descripción |
 |--------|-------------|
 | Unconfigured | Estado inicial. El nodo está instanciado pero no tiene ninguna configuración cargada. |
 | Inactive | El nodo está configurado y listo, pero no está procesando datos ni comunicándose (ej. los publicadores no publican). |
 | Active | El nodo está totalmente operativo: publica, suscribe, procesa datos, etc. |
 | Finalized | Estado terminal. El nodo ha sido destruido y no puede volver a usarse. |

 | Transición | Callback | Descripción |
 |------------|----------|-------------|
 | `configuring` | `on_configure` | Carga de parámetros, creación de publicadores/suscriptores, inicialización de recursos. Pasa de `Unconfigured` a `Inactive`. |
 | `activating` | `on_activate` | Activa los recursos para que el nodo empiece a trabajar (ej. arranca timers). Pasa de `Inactive` a `Active`. |
 | `deactivating` | `on_deactivate` | Detiene el trabajo del nodo (ej. para timers). Pasa de `Active` a `Inactive`. |
 | `cleaning up` | `on_cleanup` | Libera los recursos creados en `on_configure`. Pasa de `Inactive` a `Unconfigured`. |
 | `shutting down`| `on_shutdown` | Se llama cuando el nodo va a ser destruido. Libera todos los recursos restantes. |

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
         if self.publisher is not None:
            self.publisher.publish(msg)
         self.count += 1

def main(args=None):
    rclpy.init(args=args)
    node = MiLifecycleNode()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    finally:
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

### Gestionar Lifecycle desde un Nodo

Además de la línea de comandos, puedes controlar el estado de un lifecycle node mediante programación, lo cual es fundamental para la gestión de sistemas complejos. Esto se hace a través de clientes de servicio especiales.

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from rclpy.lifecycle.client import LifecycleClient
from rclpy.lifecycle.transition import Transition
from lifecycle_msgs.srv import ChangeState, GetState

class LifecycleManager(Node):
    def __init__(self):
        super().__init__('lifecycle_manager')
        
        # Cliente para cambiar el estado
        self.client = self.create_client(
            ChangeState, 
            '/mi_lifecycle_node/change_state'
        )
        
        # Esperar al servicio
        self.client.wait_for_service()
        self.get_logger().info('Servicio de cambio de estado encontrado.')

    def change_state(self, transition_id):
        req = ChangeState.Request()
        req.transition.id = transition_id
        future = self.client.call_async(req)
        rclpy.spin_until_future_complete(self, future)
        
        if future.result().success:
            self.get_logger().info(f'Transición {transition_id} completada con éxito.')
        else:
            self.get_logger().error(f'Fallo al realizar la transición {transition_id}.')

def main(args=None):
    rclpy.init(args=args)
    manager = LifecycleManager()
    
    # Secuencia de transiciones
    manager.change_state(Transition.TRANSITION_CONFIGURE)
    manager.change_state(Transition.TRANSITION_ACTIVATE)
    
    manager.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

> 💡 **Práctica:** Ejercita lifecycle en [Ejercicios ROS2 - Sección 2](Ejercicios_ROS2.md#sección-2-lifecycle-nodes)

---

## Composition

### ¿Qué es Composition?

Composition permite ejecutar múltiples nodos en un mismo proceso. Esto elimina la sobrecarga de la comunicación entre procesos (usando serialización, copiado de memoria, etc.), lo que resulta en una comunicación mucho más eficiente.

> **Analogía simple: Compartir piso en lugar de vivir en casas separadas.**
>
> -   **Sin Composition (Casas Separadas):** Cada nodo es una "casa" con su propia dirección (proceso) y sus propios recursos (memoria). Para que dos nodos se comuniquen, deben "enviar una carta" (un mensaje de red a través de DDS). Este proceso implica empaquetar el mensaje, enviarlo por la "calle" (la red), y que el otro nodo lo reciba y desempaquete. Es lento y consume recursos.
> -   **Con Composition (Compartiendo Piso):** Varios nodos viven en el mismo "piso" (proceso contenedor). Para comunicarse, simplemente pueden "hablarse en la misma habitación" (pasarse punteros de memoria). No hay envíos, ni empaquetado, ni copias innecesarias. La comunicación es casi instantánea.

#### El "Contenedor" de Componentes

En lugar de lanzar cada nodo en su propio proceso del sistema operativo, se lanza un único proceso "contenedor". Luego, los nodos (llamados "componentes" en este contexto) se cargan dinámicamente en este contenedor.

```
       Proceso A (Nodo 1)         Proceso B (Nodo 2)
 Memoria   <-- Copia -->   Memoria   <-- (LENTO) -->   Red/DDS
+---------+             +---------+
|  Nodo 1 | --- Topic --- |  Nodo 2 |
+---------+             +---------+

       Proceso Contenedor
+---------------------------------+
| Memoria Compartida (CERO-COPIA) |
| +---------+         +---------+ |
| |  Nodo 1 |---Topic---|  Nodo 2 | |
| +---------+         +---------+ |
+---------------------------------+
```

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
from mis_ejercicios.publisher import PublisherNode
from mis_ejercicios.subscriber import SubscriberNode

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
from mis_ejercicios.node1 import Node1
from mis_ejercicios.node2 import Node2

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
ros2 run mis_ejercicios mi_nodo --ros-args --log-level debug
ros2 run mis_ejercicios mi_nodo --ros-args --log-level mi_nodo:=debug
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
| History | KEEP_LAST, KEEP_ALL | KEEP_LAST es lo normal. KEEP_ALL puede consumir mucha memoria. |
| Depth | Número | Tamaño de la cola cuando `History` es `KEEP_LAST`. |

#### Tabla de Compatibilidad de QoS

Uno de los problemas más comunes en ROS2 es cuando un publicador y un suscriptor no se conectan a pesar de que el topic existe. Esto suele deberse a perfiles de QoS incompatibles.

La regla más importante se refiere a la fiabilidad (`Reliability`):

| Publisher \ Subscriber | Reliable | Best Effort |
| :--- | :---: | :---: |
| **Reliable** | ✅ **OK** | ✅ **OK** |
| **Best Effort**| ❌ **NO COMPATIBLE** | ✅ **OK** |

- Un publicador `Reliable` (fiable) **puede** enviar datos a un suscriptor `Best Effort` (mejor esfuerzo). El publicador intentará entregar los mensajes de forma fiable, pero el suscriptor no lo requiere.
- Un publicador `Best Effort` **no puede** satisfacer a un suscriptor `Reliable`, porque el suscriptor requiere garantías que el publicador no ofrece.

> 🐛 **Troubleshooting:** Si tus nodos no se comunican, ¡revisa la QoS! Usa `ros2 topic info /mi_topic -v` para ver los perfiles de QoS de los publicadores y suscriptores de un topic.

### Rosbag2: Grabar y Reproducir Datos

`rosbag` es una herramienta esencial para el debugging y las pruebas. Te permite grabar los mensajes publicados en los topics y reproducirlos más tarde.

**Grabar datos:**
```bash
# Graba todos los topics
ros2 bag record -a

# Graba solo los topics especificados
ros2 bag record /topic1 /topic2
```

**Reproducir datos:**
```bash
# El archivo de la bolsa es un directorio
ros2 bag play nombre_de_la_bolsa_xxx
```

**Inspeccionar una bolsa:**
```bash
ros2 bag info nombre_de_la_bolsa_xxx
```

> 💡 **Práctica:** Aplica buenas prácticas y explora `rosbag` en [Ejercicios ROS2 - Sección 5](Ejercicios_ROS2.md#sección-5-buenas-prácticas)

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
