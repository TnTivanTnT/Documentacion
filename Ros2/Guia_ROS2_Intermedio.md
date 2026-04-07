# Guía ROS2 - Nivel Intermedio

> 📚 [Volver al Índice de Documentación](../Índice%20Documentación.md)

## Índice
- [Services](#services)
- [Actions](#actions)
- [Parameters](#parameters)
- [Launch Files](#launch-files)
- [Workspace y Paquetes](#workspace-y-paquetes)

---

## Services

### ¿Qué es un Service?

Los services permiten comunicación síncrona request/response entre nodos.

```
┌────────┐  Request   ┌────────┐
│ Client ├───────────>│ Server │
│        │<───────────│        │
└────────┘  Response  └────────┘
```

### Diferencias con Topics

| Aspecto | Topic | Service |
|---------|-------|---------|
| Tipo | Asíncrono | Síncrono |
| Comunicación | 1 a muchos | 1 a 1 |
| Uso | Flujo continuo | Petición puntual |
| Ejemplo | Sensor data | Calcular algo |

### Tipos de servicios comunes

```bash
ros2 interface list | grep srv
ros2 interface show std_srvs/srv/Empty
ros2 interface show std_srvs/srv/SetBool
ros2 interface show example_interfaces/srv/AddTwoInts
```

### Service Server

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts

class ServiceServer(Node):
    def __init__(self):
        super().__init__('service_server')
        self.srv = self.create_service(AddTwoInts, 'add_two_ints', self.add_two_ints_callback)
        self.get_logger().info('Service Server listo')

    def add_two_ints_callback(self, request, response):
        response.sum = request.a + request.b
        self.get_logger().info(f'Recibido: {request.a} + {request.b}')
        return response

def main(args=None):
    rclpy.init(args=args)
    node = ServiceServer()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Service Client

```python
#!/usr/bin/env python3
import sys
import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts

class ServiceClient(Node):
    def __init__(self):
        super().__init__('service_client')
        self.cli = self.create_client(AddTwoInts, 'add_two_ints')
        while not self.cli.wait_for_service(timeout_sec=1.0):
            self.get_logger().info('Esperando servicio...')
        self.req = AddTwoInts.Request()

    def send_request(self, a, b):
        self.req.a = a
        self.req.b = b
        self.future = self.cli.call_async(self.req)
        rclpy.spin_until_future_complete(self, self.future)
        return self.future.result()

def main(args=None):
    rclpy.init(args=args)
    client = ServiceClient()
    response = client.send_request(int(sys.argv[1]), int(sys.argv[2]))
    client.get_logger().info(f'Resultado: {response.sum}')
    client.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Comandos de services

| Comando | Descripción |
|---------|-------------|
| `ros2 service list` | Lista servicios |
| `ros2 service type <service>` | Tipo del servicio |
| `ros2 service call <service> <tipo> <datos>` | Llama servicio |

```bash
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 5, b: 3}"
```

> 💡 **Práctica:** Ejercita services en [Ejercicios ROS2 - Sección 1](Ejercicios_ROS2.md#sección-1-services)

---

## Actions

### ¿Qué es un Action?

Las actions son para tareas de larga duración con feedback continuo.

```
┌────────┐  Goal     ┌────────┐
│ Client ├──────────>│ Server │
│        │<──────────│        │ Result
│        │  Feedback │        │
└────────┘           └────────┘
```

### Componentes de un Action

| Componente | Descripción |
|------------|-------------|
| Goal | Objetivo a alcanzar |
| Feedback | Progreso periódico |
| Result | Resultado final |

### Ver tipos de actions

```bash
ros2 interface list | grep action
ros2 interface show example_interfaces/action/Fibonacci
ros2 interface show nav2_msgs/action/NavigateToPose
```

### Action Server

```python
#!/usr/bin/env python3
import rclpy
from rclpy.action import ActionServer
from rclpy.node import Node
from example_interfaces.action import Fibonacci

class FibonacciActionServer(Node):
    def __init__(self):
        super().__init__('fibonacci_action_server')
        self._action_server = ActionServer(
            self,
            Fibonacci,
            'fibonacci',
            self.execute_callback)

    def execute_callback(self, goal_handle):
        self.get_logger().info('Ejecutando goal...')
        feedback_msg = Fibonacci.Feedback()
        feedback_msg.partial_sequence = [0, 1]

        for i in range(1, goal_handle.request.order):
            feedback_msg.partial_sequence.append(
                feedback_msg.partial_sequence[i] + feedback_msg.partial_sequence[i-1])
            self.get_logger().info(f'Feedback: {feedback_msg.partial_sequence}')
            goal_handle.publish_feedback(feedback_msg)

        goal_handle.succeed()
        result = Fibonacci.Result()
        result.sequence = feedback_msg.partial_sequence
        return result

def main(args=None):
    rclpy.init(args=args)
    server = FibonacciActionServer()
    rclpy.spin(server)

if __name__ == '__main__':
    main()
```

### Action Client

```python
#!/usr/bin/env python3
import rclpy
from rclpy.action import ActionClient
from rclpy.node import Node
from example_interfaces.action import Fibonacci

class FibonacciActionClient(Node):
    def __init__(self):
        super().__init__('fibonacci_action_client')
        self._action_client = ActionClient(self, Fibonacci, 'fibonacci')

    def send_goal(self, order):
        goal_msg = Fibonacci.Goal()
        goal_msg.order = order

        self._action_client.wait_for_server()
        return self._action_client.send_goal_async(goal_msg, feedback_callback=self.feedback_callback)

    def feedback_callback(self, feedback_msg):
        feedback = feedback_msg.feedback
        self.get_logger().info(f'Feedback: {feedback.partial_sequence}')

def main(args=None):
    rclpy.init(args=args)
    client = FibonacciActionClient()
    future = client.send_goal(10)
    rclpy.spin_until_future_complete(client, future)

if __name__ == '__main__':
    main()
```

### Comandos de actions

| Comando | Descripción |
|---------|-------------|
| `ros2 action list` | Lista acciones |
| `ros2 action info <action>` | Info de la acción |
| `ros2 action send_goal <action> <tipo> <goal>` | Envía goal |

```bash
ros2 action send_goal /fibonacci example_interfaces/action/Fibonacci "{order: 5}" --feedback
```

> 💡 **Práctica:** Ejercita actions en [Ejercicios ROS2 - Sección 2](Ejercicios_ROS2.md#sección-2-actions)

---

## Parameters

### ¿Qué son los Parameters?

Los parámetros son valores de configuración de un nodo que pueden ser leídos y modificados en tiempo de ejecución.

### Tipos de parámetros

| Tipo | Descripción |
|------|-------------|
| `bool` | Booleano |
| `int` | Entero |
| `double` | Flotante |
| `string` | Cadena |
| `byte[]` | Array de bytes |
| `bool[]`, `int[]`, etc. | Arrays |

### Declarar y usar parámetros

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node

class ParamNode(Node):
    def __init__(self):
        super().__init__('param_node')
        
        self.declare_parameter('mi_parametro_str', 'valor_por_defecto')
        self.declare_parameter('mi_parametro_int', 42)
        self.declare_parameter('mi_parametro_double', 3.14)
        
        self.param_str = self.get_parameter('mi_parametro_str').get_parameter_value().string_value
        self.param_int = self.get_parameter('mi_parametro_int').get_parameter_value().integer_value
        self.param_double = self.get_parameter('mi_parametro_double').get_parameter_value().double_value
        
        self.get_logger().info(f'String: {self.param_str}')
        self.get_logger().info(f'Int: {self.param_int}')
        self.get_logger().info(f'Double: {self.param_double}')

def main(args=None):
    rclpy.init(args=args)
    node = ParamNode()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Callback de parámetros

```python
from rclpy.callback_groups import ReentrantCallbackGroup

class ParamNode(Node):
    def __init__(self):
        super().__init__('param_node')
        self.declare_parameter('frecuencia', 1.0)
        
        self.timer = self.create_timer(
            self.get_parameter('frecuencia').value,
            self.timer_callback)
        
        self.add_on_set_parameters_callback(self.parameters_callback)
    
    def parameters_callback(self, params):
        for param in params:
            if param.name == 'frecuencia':
                new_period = 1.0 / param.value
                self.timer.cancel()
                self.timer = self.create_timer(new_period, self.timer_callback)
        return SetParametersResult(successful=True)
```

### Comandos de parámetros

| Comando | Descripción |
|---------|-------------|
| `ros2 param list` | Lista parámetros |
| `ros2 param get <nodo> <param>` | Obtiene valor |
| `ros2 param set <nodo> <param> <valor>` | Establece valor |
| `ros2 param dump <nodo>` | Exporta parámetros |

```bash
ros2 run mi_paquete param_node --ros-args -p mi_parametro_str:=hola
ros2 param get /param_node mi_parametro_int
ros2 param set /param_node mi_parametro_int 100
```

> 💡 **Práctica:** Ejercita parámetros en [Ejercicios ROS2 - Sección 3](Ejercicios_ROS2.md#sección-3-parameters)

---

## Launch Files

### ¿Qué es un Launch File?

Los launch files permiten iniciar múltiples nodos y configurar el sistema de forma declarativa.

### Launch file en Python

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='mi_paquete',
            executable='publisher_node',
            name='mi_publisher',
            parameters=[{'parametro': 10}],
            arguments=['--ros-args', '--log-level', 'info'],
            output='screen'
        ),
        Node(
            package='mi_paquete',
            executable='subscriber_node',
            name='mi_subscriber',
            output='screen'
        )
    ])
```

### Launch file con argumentos

```python
from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        DeclareLaunchArgument(
            'frecuencia',
            default_value='1.0',
            description='Frecuencia de publicación'
        ),
        Node(
            package='mi_paquete',
            executable='publisher_node',
            parameters=[{
                'frecuencia': LaunchConfiguration('frecuencia')
            }]
        )
    ])
```

### Estructura de launch files

```python
from launch import LaunchDescription
from launch.actions import (
    DeclareLaunchArgument,
    GroupAction,
    IncludeLaunchDescription,
    TimerAction
)
from launch_ros.actions import Node, PushRosNamespace
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch.substitutions import LaunchConfiguration, PathJoinSubstitution
from launch_ros.substitutions import FindPackageShare
```

### Ejecutar launch files

```bash
ros2 launch <paquete> <launch_file>
ros2 launch mi_paquete mi_launch.py
ros2 launch mi_paquete mi_launch.py frecuencia:=2.0
```

### Comandos de launch

| Comando | Descripción |
|---------|-------------|
| `ros2 launch <pkg> <file>` | Ejecuta launch file |
| `ros2 launch <pkg> <file> arg:=valor` | Con argumentos |
| `ros2 pkg list | grep mi_paquete` | Verifica paquete |

> 💡 **Práctica:** Crea launch files en [Ejercicios ROS2 - Sección 4](Ejercicios_ROS2.md#sección-4-launch-files)

---

## Workspace y Paquetes

### Estructura de un workspace

```
ros2_ws/
├── build/
├── install/
├── log/
└── src/
    ├── paquete_1/
    │   ├── package.xml
    │   ├── setup.py
    │   ├── paquete_1/
    │   │   └── __init__.py
    │   └── resource/
    └── paquete_2/
```

### Crear un paquete Python

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python mi_paquete
```

### package.xml

```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>mi_paquete</name>
  <version>0.0.1</version>
  <description>Mi paquete ROS2</description>
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

### setup.py

```python
from setuptools import setup

package_name = 'mi_paquete'

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
    description='Mi paquete ROS2',
    license='MIT',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
            'mi_nodo = mi_paquete.mi_nodo:main',
            'publisher = mi_paquete.publisher:main',
            'subscriber = mi_paquete.subscriber:main',
        ],
    },
)
```

### Flujo de trabajo

```bash
cd ~/ros2_ws
colcon build
source install/setup.bash
ros2 run mi_paquete mi_nodo
```

### Comandos de paquetes

| Comando | Descripción |
|---------|-------------|
| `ros2 pkg create` | Crea paquete |
| `ros2 pkg list` | Lista paquetes |
| `ros2 pkg prefix <paquete>` | Ruta del paquete |
| `ros2 pkg xml <paquete>` | Muestra package.xml |
| `colcon build` | Construye workspace |
| `colcon build --symlink-install` | Con enlaces simbólicos |

> 💡 **Práctica:** Crea tu workspace en [Ejercicios ROS2 - Sección 5](Ejercicios_ROS2.md#sección-5-workspace-y-paquetes)

---

## Resumen de comandos intermedios

| Categoría | Comandos |
|-----------|----------|
| Services | `ros2 service list`, `ros2 service call` |
| Actions | `ros2 action list`, `ros2 action send_goal` |
| Parameters | `ros2 param get`, `ros2 param set` |
| Launch | `ros2 launch`, `colcon build` |

---

## Siguientes pasos

Continúa con [Guía ROS2 - Nivel Avanzado](Guia_ROS2_Avanzado.md) para aprender sobre interfaces personalizadas y herramientas de debugging.
