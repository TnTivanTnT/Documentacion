# Guía ROS2 - Nivel Intermedio

> 📚 [Volver al Índice de Documentación](../README.md)

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
import time
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
    server = ServiceServer()
    try:
        rclpy.spin(server)
    except KeyboardInterrupt:
        server.get_logger().info('Cerrando el nodo de servidor.')
    finally:
        server.destroy_node()
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
    try:
        # Manejo robusto de argumentos de línea de comandos
        if len(sys.argv) != 3:
            client.get_logger().error('Uso: ros2 run <paquete> <nodo> <a> <b>')
            return

        try:
            a = int(sys.argv[1])
            b = int(sys.argv[2])
        except ValueError:
            client.get_logger().error('Los argumentos deben ser números enteros.')
            return

        # Envío de la solicitud y manejo de la respuesta
        response = client.send_request(a, b)
        if response:
            client.get_logger().info(f'Resultado: {a} + {b} = {response.sum}')
        else:
            client.get_logger().error('La llamada al servicio falló.')
    except Exception as e:
        client.get_logger().error(f'Excepción durante la llamada al servicio: {e}')
    finally:
        # Asegurar que los recursos se liberen siempre
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

> 💡 **Práctica:** Ejercita services en [Ejercicios ROS2 - Nivel Intermedio](Ejercicios_ROS2.md#sección-1-services)

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
| Goal | El objetivo que el cliente quiere que el servidor alcance. |
| Feedback | Mensajes periódicos del servidor para informar sobre el progreso. |
| Result | Un mensaje final del servidor cuando la tarea se completa. |

#### Flujo de Comunicación de un Action

Las Actions siguen un protocolo más complejo que los services, diseñado para tareas no instantáneas.

```
 Cliente                                  Servidor
    |                                          |
    | --- 1. Enviar Goal (ej: "navegar a X") --> |
    |                                          |
    | <-- 2. Aceptar/Rechazar Goal (Ack) ------ |
    |                                          |
    | <-- 3. Feedback ("25% completado") ------ |
    |                                          |
    | <-- 3. Feedback ("50% completado") ------ |
    |                                          |
    | <-- 4. Resultado ("Destino alcanzado") -- |
    |                                          |
```

1.  **Goal Request**: El cliente envía un objetivo.
2.  **Goal Acceptance**: El servidor confirma que ha recibido y aceptado el objetivo.
3.  **Feedback**: Mientras trabaja, el servidor envía actualizaciones de progreso.
4.  **Result**: Una vez finalizada la tarea, el servidor envía el resultado final.

> Este modelo es ideal para tareas como navegación, manipulación de objetos o secuencias de procesamiento largas.

### Ver tipos de actions

```bash
ros2 interface list | grep action
ros2 interface show example_interfaces/action/Fibonacci
ros2 interface show nav2_msgs/action/NavigateToPose
```

### Action Server

```python
#!/usr/bin/env python3
import time
import rclpy
from rclpy.action import ActionServer
from rclpy.node import Node
from example_interfaces.action import Fibonacci

from rclpy.executors import MultiThreadedExecutor
from rclpy.callback_groups import ReentrantCallbackGroup

class FibonacciActionServer(Node):
    def __init__(self):
        super().__init__('fibonacci_action_server')
        # Usar un ReentrantCallbackGroup para permitir el paralelismo real
        self._action_server = ActionServer(
            self,
            Fibonacci,
            'fibonacci',
            self.execute_callback,
            callback_group=ReentrantCallbackGroup())

    def execute_callback(self, goal_handle):
        self.get_logger().info('Ejecutando goal...')
        feedback_msg = Fibonacci.Feedback()
        feedback_msg.partial_sequence = [0, 1]

        for i in range(1, goal_handle.request.order):
            # Comprobar si el cliente ha solicitado cancelar la acción
            if goal_handle.is_cancel_requested:
                goal_handle.canceled()
                self.get_logger().info('Goal cancelado')
                return Fibonacci.Result()

            feedback_msg.partial_sequence.append(
                feedback_msg.partial_sequence[i] + feedback_msg.partial_sequence[i-1])
            self.get_logger().info(f'Feedback: {feedback_msg.partial_sequence}')
            goal_handle.publish_feedback(feedback_msg)
            # Simular un trabajo que toma tiempo
            time.sleep(1)

        goal_handle.succeed()
        result = Fibonacci.Result()
        result.sequence = feedback_msg.partial_sequence
        return result

def main(args=None):
    rclpy.init(args=args)
    server = FibonacciActionServer()
    
    # Se usa un MultiThreadedExecutor para evitar que el cálculo bloquee otros callbacks.
    # El ReentrantCallbackGroup es CRUCIAL para que esto funcione.
    executor = MultiThreadedExecutor()
    try:
        rclpy.spin(server, executor=executor)
    except KeyboardInterrupt:
        pass
    finally:
        server.destroy_node()
        rclpy.shutdown()

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
    try:
        future = client.send_goal(10)
        rclpy.spin_until_future_complete(client, future)
        
        goal_handle = future.result()
        if not goal_handle.accepted:
            client.get_logger().info('Goal rechazado.')
            return

        client.get_logger().info('Goal aceptado. Esperando resultado...')
        result_future = goal_handle.get_result_async()
        rclpy.spin_until_future_complete(client, result_future)
        
        result = result_future.result().result
        client.get_logger().info(f'Resultado final: {result.sequence}')
        
    except Exception as e:
        client.get_logger().error(f'Excepción durante la llamada a la acción: {e}')
    finally:
        client.destroy_node()
        rclpy.shutdown()

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

> 💡 **Práctica:** Ejercita actions en [Ejercicios ROS2 - Nivel Intermedio](Ejercicios_ROS2.md#sección-2-actions)

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

### Callback de parámetros

```python
from rcl_interfaces.msg import SetParametersResult
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

> 💡 **Práctica:** Ejercita parámetros en [Ejercicios ROS2 - Nivel Intermedio](Ejercicios_ROS2.md#sección-3-parameters)

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

### Launch file con argumentos y modularidad

A medida que los sistemas crecen, es útil dividir los launch files y reutilizarlos.

#### Incluir un Launch File dentro de otro

Puedes usar `IncludeLaunchDescription` para construir sistemas complejos a partir de componentes más pequeños.

**`robot_base.launch.py` (Componente base):**
```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(package='robot_driver', executable='driver_node', name='base_driver'),
        Node(package='robot_sensors', executable='lidar_node', name='lidar')
    ])
```

**`robot_completo.launch.py` (Launch file principal):**
```python
from launch import LaunchDescription
from launch.actions import IncludeLaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch_ros.substitutions import FindPackageShare
import os

def generate_launch_description():
    # Ruta al launch file que queremos incluir
    base_launch_path = os.path.join(
        FindPackageShare('mi_robot_base'), # Asume que robot_base.launch.py está en otro paquete
        'launch',
        'robot_base.launch.py'
    )

    return LaunchDescription([
        # Incluimos el launch file base
        IncludeLaunchDescription(
            PythonLaunchDescriptionSource(base_launch_path)
        ),
        
        # Añadimos nodos adicionales específicos de este sistema
        Node(
            package='robot_navigation',
            executable='nav_node',
            name='navigation'
        )
    ])
```

> 💡 **Buena práctica:** Crea launch files pequeños y reutilizables para cada "capacidad" de tu robot (ej: `sensores.launch.py`, `movimiento.launch.py`) y únelos en un launch file principal.

> **Nota sobre formatos:** Aunque este curso se centra en **Python**, que es el formato más potente y flexible, los launch files también pueden escribirse en **XML** y **YAML**.

| Formato | Ventajas | Desventajas |
| :--- | :--- | :--- |
| **Python** | Máxima flexibilidad, lógica condicional, bucles, acceso al sistema de archivos. | Curva de aprendizaje un poco mayor, más verboso para tareas simples. |
| **XML/YAML**| Sintaxis simple y declarativa, fácil de leer para estructuras sencillas. | Sin lógica programática, menos flexible, más difícil de depurar. |

> Para sistemas complejos, la comunidad ROS2 se ha inclinado mayoritariamente por los launch files de Python.

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
ros2 launch mi_paquete mi_launch.launch.py
ros2 launch mi_paquete mi_launch.launch.py frecuencia:=2.0
```

### Comandos de launch

| Comando | Descripción |
|---------|-------------|
| `ros2 launch <pkg> <file>` | Ejecuta launch file |
| `ros2 launch <pkg> <file> arg:=valor` | Con argumentos |
| `ros2 pkg list | grep mi_paquete` | Verifica paquete |

> 💡 **Práctica:** Crea launch files en [Ejercicios ROS2 - Nivel Intermedio](Ejercicios_ROS2.md#sección-4-launch-files)

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
