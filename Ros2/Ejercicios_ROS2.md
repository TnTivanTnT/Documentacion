# Ejercicios Prácticos - ROS2

> 📚 [Volver al Índice de Documentación](../README.md)

Este documento contiene ejercicios prácticos para reforzar los conceptos de las guías de ROS2 Humble con Python (rclpy).

---

## Nivel Principiante

### Sección 1: Verificación del Entorno

> 📖 **Teoría:** [Guía ROS2 Principiante - Primeros Pasos](Guia_ROS2_Principiante.md#primeros-pasos-instalación-y-entorno)

#### Ejercicio 1.1: Verificar instalación
```bash
ros2 --version
printenv | grep ROS
echo $ROS_DISTRO
echo $ROS_VERSION
```

**Tarea:** Asegúrate de que `ROS_DISTRO=humble` y `ROS_VERSION=2`

#### Ejercicio 1.2: Configurar entorno
```bash
grep "source /opt/ros/humble/setup.bash" ~/.bashrc
source ~/.bashrc
```

#### Ejercicio 1.3: Probar demo
```bash
ros2 run demo_nodes_cpp talker &
ros2 run demo_nodes_cpp listener
```

---

### Sección 2: Conceptos Fundamentales

> 📖 **Teoría:** [Guía ROS2 Principiante - Conceptos](Guia_ROS2_Principiante.md#conceptos-fundamentales)

#### Ejercicio 2.1: Explorar el grafo
```bash
ros2 run demo_nodes_cpp talker &
ros2 run demo_nodes_cpp listener &
ros2 node list
ros2 topic list
```

#### Ejercicio 2.2: Ver información
```bash
ros2 node info /talker
ros2 topic info /chatter
rqt_graph
```

---

### Sección 3: Nodos en ROS2

> 📖 **Teoría:** [Guía ROS2 Principiante - Nodos](Guia_ROS2_Principiante.md#nodos-en-ros2)

#### Ejercicio 3.1: Crear workspace y paquete
```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python mis_ejercicios
cd ~/ros2_ws
colcon build
source install/setup.bash
```

#### Ejercicio 3.2: Crear nodo básico

Crear archivo `~/ros2_ws/src/mis_ejercicios/mis_ejercicios/nodo_basico.py`:

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node

class NodoBasico(Node):
    def __init__(self):
        super().__init__('nodo_basico')
        self.get_logger().info('¡Mi primer nodo funciona!')
        self.contador = 0
        self.crear_timer()

    def crear_timer(self):
        self.timer = self.create_timer(1.0, self.timer_callback)

    def timer_callback(self):
        self.contador += 1
        self.get_logger().info(f'Contador: {self.contador}')

def main(args=None):
    rclpy.init(args=args)
    nodo = NodoBasico()
    rclpy.spin(nodo)
    nodo.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

Actualizar `setup.py`:
```python
entry_points={
    'console_scripts': [
        'nodo_basico = mis_ejercicios.nodo_basico:main',
    ],
},
```

#### Ejercicio 3.3: Compilar y ejecutar
```bash
cd ~/ros2_ws
colcon build --packages-select mis_ejercicios
source install/setup.bash
ros2 run mis_ejercicios nodo_basico
```

---

### Sección 4: Topics y Mensajes

> 📖 **Teoría:** [Guía ROS2 Principiante - Topics](Guia_ROS2_Principiante.md#topics-y-mensajes)

#### Ejercicio 4.1: Explorar tipos de mensajes
```bash
ros2 interface list | grep std_msgs
ros2 interface show std_msgs/msg/String
ros2 interface show geometry_msgs/msg/Twist
```

#### Ejercicio 4.2: Crear publisher

Crear `mis_ejercicios/mis_ejercicios/publisher.py`:

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class MiPublisher(Node):
    def __init__(self):
        super().__init__('mi_publisher')
        self.publisher = self.create_publisher(String, 'mi_topic', 10)
        self.timer = self.create_timer(0.5, self.timer_callback)
        self.contador = 0

    def timer_callback(self):
        msg = String()
        msg.data = f'Mensaje #{self.contador}'
        self.publisher.publish(msg)
        self.get_logger().info(f'Publicado: "{msg.data}"')
        self.contador += 1

def main(args=None):
    rclpy.init(args=args)
    node = MiPublisher()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

#### Ejercicio 4.3: Crear subscriber

Crear `mis_ejercicios/mis_ejercicios/subscriber.py`:

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class MiSubscriber(Node):
    def __init__(self):
        super().__init__('mi_subscriber')
        self.subscription = self.create_subscription(
            String,
            'mi_topic',
            self.listener_callback,
            10)

    def listener_callback(self, msg):
        self.get_logger().info(f'Recibido: "{msg.data}"')

def main(args=None):
    rclpy.init(args=args)
    node = MiSubscriber()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

#### Ejercicio 4.4: Probar comunicación
```bash
# Terminal 1
ros2 run mis_ejercicios publisher

# Terminal 2
ros2 run mis_ejercicios subscriber

# Terminal 3
ros2 topic list
ros2 topic echo /mi_topic
ros2 topic hz /mi_topic
```

#### Ejercicio 4.5: Publisher con Twist

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist

class VelocidadPublisher(Node):
    def __init__(self):
        super().__init__('velocidad_publisher')
        self.publisher = self.create_publisher(Twist, '/cmd_vel', 10)
        self.timer = self.create_timer(0.1, self.timer_callback)

    def timer_callback(self):
        msg = Twist()
        msg.linear.x = 0.5
        msg.angular.z = 0.1
        self.publisher.publish(msg)

def main(args=None):
    rclpy.init(args=args)
    node = VelocidadPublisher()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

---

### Sección 5: Herramientas de Línea de Comandos

> 📖 **Teoría:** [Guía ROS2 Principiante - CLI](Guia_ROS2_Principiante.md#herramientas-de-línea-de-comandos)

#### Ejercicio 5.1: Explorar comandos
```bash
ros2 run demo_nodes_cpp talker &
ros2 topic list
ros2 topic info /chatter -v
ros2 topic echo /chatter
ros2 topic hz /chatter
```

#### Ejercicio 5.2: Publicar desde CLI
```bash
ros2 topic pub /prueba std_msgs/msg/String "data: 'Hola desde CLI'" --once
ros2 topic pub /prueba std_msgs/msg/String "data: 'Mensaje continuo'" -r 1
```

#### Ejercicio 5.3: Explorar paquetes
```bash
ros2 pkg list | grep demo
ros2 pkg prefix demo_nodes_cpp
ros2 pkg xml demo_nodes_cpp
```

---

### Sección 6: Troubleshooting Común

> 🐛 **¿Problemas?** Consulta esta sección si algo no funciona como esperas.

#### Problema 1: `colcon build` falla o no encuentra el paquete

**Síntoma:** `colcon` se queja de que no puede encontrar un paquete o hay errores de compilación.

**Soluciones:**
1.  **Sourcing:** ¿Hiciste `source /opt/ros/humble/setup.bash` en tu terminal? ¿Y `source install/setup.bash` desde tu workspace?
2.  **Dependencias:** Asegúrate de que todas las dependencias están listadas en `package.xml`.
3.  **Clean build:** A veces, un build corrupto causa problemas. Intenta limpiar:
    ```bash
    cd ~/ros2_ws
    rm -rf build install log
    colcon build
    source install/setup.bash
    ```

#### Problema 2: `ros2 run` no encuentra tu nodo

**Síntoma:** El comando `ros2 run mi_paquete mi_nodo` dice `Executable 'mi_nodo' not found`.

**Soluciones:**
1.  **Sourcing:** ¡El más común! Asegúrate de haber hecho `source install/setup.bash` en la terminal **actual**.
2.  **Entry Point:** Revisa tu `setup.py`. ¿El `entry_points` está bien configurado?
    ```python
    'console_scripts': [
        'mi_nodo = mi_paquete.nombre_archivo:main',
    ],
    ```
3.  **Compilación:** ¿Compilaste el paquete después de crear el nodo? `colcon build --packages-select mi_paquete`.

#### Problema 3: El topic, servicio o acción no aparece

**Síntoma:** `ros2 topic list` no muestra tu topic o `ros2 service call` no funciona.

**Soluciones:**
1.  **Nodo corriendo:** ¿Está el nodo que publica el topic o provee el servicio realmente corriendo? Usa `ros2 node list`.
2.  **Nombres:** Revisa que los nombres del topic/servicio coincidan exactamente entre el publisher/servidor y el subscriber/cliente.
3.  **QoS (Calidad de Servicio):** Especialmente con topics, si el publicador y suscriptor tienen perfiles de QoS incompatibles (ej. uno `RELIABLE` y otro `BEST_EFFORT`), no se conectarán. Asegúrate de que sean compatibles.

---

## Nivel Intermedio

### Sección 1: Services

> 📖 **Teoría:** [Guía ROS2 Intermedio - Services](Guia_ROS2_Intermedio.md#services)

#### Ejercicio 1.1: Explorar servicios
```bash
ros2 run demo_nodes_cpp add_two_ints_server &
ros2 service list
ros2 service type /add_two_ints
ros2 interface show example_interfaces/srv/AddTwoInts
```

#### Ejercicio 1.2: Llamar servicio desde CLI
```bash
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 5, b: 3}"
```

#### Ejercicio 1.3: Crear service server

<details>
<summary>Haz clic para ver una posible solución</summary>

**`mis_ejercicios/mis_ejercicios/suma_server.py`**
```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts

class SumaServer(Node):
    def __init__(self):
        super().__init__('suma_server')
        self.srv = self.create_service(AddTwoInts, 'suma', self.suma_callback)
        self.get_logger().info('Service Server listo en /suma')

    def suma_callback(self, request, response):
        response.sum = request.a + request.b
        self.get_logger().info(f'Request: {request.a} + {request.b} = {response.sum}')
        return response

def main(args=None):
    rclpy.init(args=args)
    node = SumaServer()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```
No olvides añadir la dependencia `example_interfaces` en tu `package.xml` y el `entry_point` en `setup.py`.
</details>

#### Ejercicio 1.4: Crear service client

<details>
<summary>Haz clic para ver una posible solución</summary>

**`mis_ejercicios/mis_ejercicios/suma_client.py`**
```python
#!/usr/bin/env python3
import sys
import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts

class SumaClient(Node):
    def __init__(self):
        super().__init__('suma_client')
        self.cli = self.create_client(AddTwoInts, 'suma')
        while not self.cli.wait_for_service(timeout_sec=1.0):
            self.get_logger().info('Esperando servicio...')
        self.req = AddTwoInts.Request()

    def enviar_request(self, a, b):
        self.req.a = a
        self.req.b = b
        self.future = self.cli.call_async(self.req)
        rclpy.spin_until_future_complete(self, self.future)
        return self.future.result()

def main(args=None):
    rclpy.init(args=args)
    client = SumaClient()
    
    if len(sys.argv) < 3:
        client.get_logger().error("Uso: ros2 run mis_ejercicios suma_client <a> <b>")
        return
    
    response = client.enviar_request(int(sys.argv[1]), int(sys.argv[2]))
    client.get_logger().info(f'Resultado de {sys.argv[1]} + {sys.argv[2]} = {response.sum}')
    client.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```
No olvides añadir el `entry_point` en `setup.py`.
</details>

---

### Sección 2: Actions

> 📖 **Teoría:** [Guía ROS2 Intermedio - Actions](Guia_ROS2_Intermedio.md#actions)

#### Ejercicio 2.1: Explorar actions
```bash
ros2 action list
ros2 action info /nombre_action
ros2 interface show example_interfaces/action/Fibonacci
```

#### Ejercicio 2.2: Action Server de cuenta regresiva

<details>
<summary>Haz clic para ver una posible solución</summary>

```python
#!/usr/bin/env python3
import time
import rclpy
from rclpy.action import ActionServer
from rclpy.node import Node
from example_interfaces.action import Fibonacci

class CuentaRegresivaServer(Node):
    def __init__(self):
        super().__init__('cuenta_regresiva_server')
        self._action_server = ActionServer(
            self,
            Fibonacci, # Usaremos Fibonacci para el tipo, aunque la lógica es de cuenta atrás
            'cuenta_regresiva',
            self.execute_callback)
        self.get_logger().info("Action Server de Cuenta Regresiva listo.")

    def execute_callback(self, goal_handle):
        self.get_logger().info('Iniciando cuenta regresiva...')
        
        feedback_msg = Fibonacci.Feedback()
        feedback_msg.partial_sequence = []
        
        objetivo = goal_handle.request.order
        
        for i in range(objetivo, 0, -1):
            if goal_handle.is_cancel_requested:
                goal_handle.canceled()
                self.get_logger().info('Goal cancelado')
                return Fibonacci.Result()

            feedback_msg.partial_sequence.append(i)
            goal_handle.publish_feedback(feedback_msg)
            self.get_logger().info(f'Feedback: {i}')
            time.sleep(1)
            
        goal_handle.succeed()
        
        result = Fibonacci.Result()
        result.sequence = feedback_msg.partial_sequence
        self.get_logger().info('Cuenta regresiva completada.')
        return result

def main(args=None):
    rclpy.init(args=args)
    server = CuentaRegresivaServer()
    # Usamos un executor Multi-hilo para que el time.sleep no bloquee el nodo
    from rclpy.executors import MultiThreadedExecutor
    executor = MultiThreadedExecutor()
    rclpy.spin(server, executor=executor)

if __name__ == '__main__':
    main()
```
</details>

#### Ejercicio 2.3: Action Client

<details>
<summary>Haz clic para ver una posible solución</summary>

```python
#!/usr/bin/env python3
import sys
import rclpy
from rclpy.action import ActionClient
from rclpy.node import Node
from example_interfaces.action import Fibonacci

class CuentaRegresivaClient(Node):
    def __init__(self):
        super().__init__('cuenta_regresiva_client')
        self._action_client = ActionClient(self, Fibonacci, 'cuenta_regresiva')

    def send_goal(self, orden):
        goal_msg = Fibonacci.Goal()
        goal_msg.order = orden

        self.get_logger().info('Esperando al Action Server...')
        self._action_client.wait_for_server()

        self.get_logger().info(f'Enviando goal para cuenta regresiva desde {orden}...')
        self._send_goal_future = self._action_client.send_goal_async(
            goal_msg, 
            feedback_callback=self.feedback_callback)
        
        self._send_goal_future.add_done_callback(self.goal_response_callback)

    def goal_response_callback(self, future):
        goal_handle = future.result()
        if not goal_handle.accepted:
            self.get_logger().info('Goal rechazado :(')
            return

        self.get_logger().info('Goal aceptado :)')

        self._get_result_future = goal_handle.get_result_async()
        self._get_result_future.add_done_callback(self.get_result_callback)

    def get_result_callback(self, future):
        result = future.result().result
        self.get_logger().info(f'Resultado final: {result.sequence}')
        rclpy.shutdown()

    def feedback_callback(self, feedback_msg):
        feedback = feedback_msg.feedback
        self.get_logger().info(f'Progreso: {feedback.partial_sequence[-1]}...')

def main(args=None):
    rclpy.init(args=args)
    client = CuentaRegresivaClient()
    
    orden = int(sys.argv[1]) if len(sys.argv) > 1 else 10
    client.send_goal(orden)
    
    rclpy.spin(client)

if __name__ == '__main__':
    main()
```
</details>

---

### Sección 3: Parameters

> 📖 **Teoría:** [Guía ROS2 Intermedio - Parameters](Guia_ROS2_Intermedio.md#parameters)

#### Ejercicio 3.1: Nodo con parámetros

```python
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class NodoConParams(Node):
    def __init__(self):
        super().__init__('nodo_con_params')
        
        self.declare_parameter('mensaje', 'Hola mundo')
        self.declare_parameter('frecuencia', 1.0)
        self.declare_parameter('contador_inicial', 0)
        
        self.mensaje = self.get_parameter('mensaje').get_parameter_value().string_value
        self.frecuencia = self.get_parameter('frecuencia').get_parameter_value().double_value
        self.contador = self.get_parameter('contador_inicial').get_parameter_value().integer_value
        
        self.publisher = self.create_publisher(String, 'topic_params', 10)
        self.timer = self.create_timer(1.0 / self.frecuencia, self.timer_callback)
        
        self.get_logger().info(f'Mensaje: {self.mensaje}')
        self.get_logger().info(f'Frecuencia: {self.frecuencia} Hz')

    def timer_callback(self):
        msg = String()
        msg.data = f'{self.mensaje}: {self.contador}'
        self.publisher.publish(msg)
        self.contador += 1

def main(args=None):
    rclpy.init(args=args)
    node = NodoConParams()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

#### Ejercicio 3.2: Ejecutar con parámetros
```bash
ros2 run mis_ejercicios nodo_con_params --ros-args -p mensaje:=Saludos -p frecuencia:=2.0 -p contador_inicial:=100
```

#### Ejercicio 3.3: Modificar parámetros en runtime
```bash
# Terminal 1
ros2 run mis_ejercicios nodo_con_params

# Terminal 2
ros2 param list /nodo_con_params
ros2 param get /nodo_con_params frecuencia
ros2 param set /nodo_con_params frecuencia 0.5
```

---

### Sección 4: Launch Files

> 📖 **Teoría:** [Guía ROS2 Intermedio - Launch Files](Guia_ROS2_Intermedio.md#launch-files)

#### Ejercicio 4.1: Crear launch file básico

Crear `mis_ejercicios/launch/mi_launch.py`:

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='mis_ejercicios',
            executable='publisher',
            name='mi_publisher',
            output='screen'
        ),
        Node(
            package='mis_ejercicios',
            executable='subscriber',
            name='mi_subscriber',
            output='screen'
        )
    ])
```

Actualizar `setup.py`:
```python
import os
from glob import glob
from setuptools import setup, find_packages

package_name = 'mis_ejercicios'

setup(
    # ... código existente ...
    data_files=[
        # ... código existente ...
        (os.path.join('share', package_name), glob('launch/*.py')),
    ],
    # ... resto del código ...
)
```

#### Ejercicio 4.2: Launch file con argumentos

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
        DeclareLaunchArgument(
            'mensaje',
            default_value='Hola',
            description='Mensaje a publicar'
        ),
        Node(
            package='mis_ejercicios',
            executable='nodo_con_params',
            parameters=[{
                'frecuencia': LaunchConfiguration('frecuencia'),
                'mensaje': LaunchConfiguration('mensaje')
            }],
            output='screen'
        )
    ])
```

#### Ejercicio 4.3: Ejecutar launch files
```bash
ros2 launch mis_ejercicios mi_launch.py
ros2 launch mis_ejercicios mi_launch.py frecuencia:=2.0 mensaje:=Test
```

---

### Sección 5: Workspace y Paquetes

> 📖 **Teoría:** [Guía ROS2 Intermedio - Workspace](Guia_ROS2_Intermedio.md#workspace-y-paquetes)

#### Ejercicio 5.0: Entender build types

**Pregunta:** ¿Qué build type usarías para cada caso?

1. Un nodo que lee sensores y publica datos
2. Una definición de mensaje personalizado `SensorCalibrado.msg`
3. Un servicio para calibrar el robot
4. Un script Python que procesa datos de sensores
5. Una acción para navegación autónoma
6. Un nodo de alto rendimiento en C++
7. Un launch file en Python

<details>
<summary>Click para ver las respuestas</summary>

1. **`ament_python`** - Nodo Python
2. **`ament_cmake`** - Interfaz (mensaje personalizado)
3. **`ament_cmake`** - Interfaz (servicio personalizado)
4. **`ament_python`** - Script Python
5. **`ament_cmake`** - Interfaz (acción personalizada)
6. **`ament_cmake`** - Nodo C++
7. **`ament_python`** - Launch file Python

**Regla general:**
- Nodos y scripts Python → `ament_python`
- Interfaces (msg/srv/action) → `ament_cmake` (aunque uses Python)
- Nodos C++ → `ament_cmake`

</details>

---

#### Ejercicio 5.1: Estructurar paquete completo
```bash
cd ~/ros2_ws/src/mis_ejercicios
mkdir -p launch config
```

#### Ejercicio 5.2: Archivo de configuración YAML

Crear `config/params.yaml`:
```yaml
nodo_con_params:
  ros__parameters:
    mensaje: "Desde YAML"
    frecuencia: 0.5
    contador_inicial: 50
```

Usar en launch file:
```python
from launch_ros.substitutions import FindPackageShare
from launch.substitutions import PathJoinSubstitution

Node(
    package='mis_ejercicios',
    executable='nodo_con_params',
    parameters=[PathJoinSubstitution([
        FindPackageShare('mis_ejercicios'),
        'config',
        'params.yaml'
    ])]
)
```

---

## Nivel Avanzado

### Sección 1: Interfaces Personalizadas

> 📖 **Teoría:** [Guía ROS2 Avanzado - Interfaces](Guia_ROS2_Avanzado.md#interfaces-personalizadas)

#### Ejercicio 1.1: Crear paquete de interfaces
```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake mis_interfaces
cd mis_interfaces
mkdir -p msg srv action
```

#### Ejercicio 1.2: Crear mensaje personalizado

Crear `msg/RobotStatus.msg`:
```text
string nombre
float64 posicion_x
float64 posicion_y
float64 bateria
bool activo
```

#### Ejercicio 1.3: Crear servicio personalizado

Crear `srv/CalcularRuta.srv`:
```text
# Request
geometry_msgs/Point inicio
geometry_msgs/Point destino
---
# Response
bool exito
float64 distancia
geometry_msgs/Point[] ruta
string mensaje
```

#### Ejercicio 1.4: Configurar CMakeLists.txt y package.xml

> 📖 **Instrucciones detalladas:** La configuración completa de `CMakeLists.txt` y `package.xml` para interfaces está en la [Guía Avanzada - Sección de Interfaces](Guia_ROS2_Avanzado.md#interfaces-personalizadas). Es crucial seguir esos pasos para que ROS2 genere el código correctamente.

#### Ejercicio 1.5: Usar interfaces
```bash
cd ~/ros2_ws
colcon build --packages-select mis_interfaces
source install/setup.bash
ros2 interface show mis_interfaces/msg/RobotStatus
```

---

### Sección 2: Lifecycle Nodes

> 📖 **Teoría:** [Guía ROS2 Avanzado - Lifecycle](Guia_ROS2_Avanzado.md#lifecycle-nodes)

#### Ejercicio 2.1: Implementar lifecycle node

```python
#!/usr/bin/env python3
import rclpy
from rclpy.lifecycle import LifecycleNode, LifecycleState, TransitionCallbackReturn
from std_msgs.msg import String

class MiLifecycleNode(LifecycleNode):
    def __init__(self):
        super().__init__('mi_lifecycle_node')

    def on_configure(self, state: LifecycleState) -> TransitionCallbackReturn:
        self.get_logger().info('Configurando nodo...')
        return TransitionCallbackReturn.SUCCESS

    def on_activate(self, state: LifecycleState) -> TransitionCallbackReturn:
        self.get_logger().info('Activando nodo...')
        return super().on_activate(state)

    def on_deactivate(self, state: LifecycleState) -> TransitionCallbackReturn:
        self.get_logger().info('Desactivando nodo...')
        return super().on_deactivate(state)

    def on_cleanup(self, state: LifecycleState) -> TransitionCallbackReturn:
        self.get_logger().info('Limpiando nodo...')
        return TransitionCallbackReturn.SUCCESS

def main(args=None):
    rclpy.init(args=args)
    node = MiLifecycleNode()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

#### Ejercicio 2.2: Gestionar lifecycle
```bash
ros2 run mis_ejercicios mi_lifecycle_node &
ros2 lifecycle list /mi_lifecycle_node
ros2 lifecycle get /mi_lifecycle_node
ros2 lifecycle set /mi_lifecycle_node configure
ros2 lifecycle set /mi_lifecycle_node activate
ros2 lifecycle set /mi_lifecycle_node deactivate
```

---

### Sección 3: Composition

> 📖 **Teoría:** [Guía ROS2 Avanzado - Composition](Guia_ROS2_Avanzado.md#composition)

#### Ejercicio 3.1: Componer nodos

```python
#!/usr/bin/env python3
import rclpy
from rclpy.executors import SingleThreadedExecutor
from mis_ejercicios.publisher import MiPublisher
from mis_ejercicios.subscriber import MiSubscriber

def main(args=None):
    rclpy.init(args=args)
    
    executor = SingleThreadedExecutor()
    
    publisher = MiPublisher()
    subscriber = MiSubscriber()
    
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

---

### Sección 4: Herramientas de Debugging

> 📖 **Teoría:** [Guía ROS2 Avanzado - Debugging](Guia_ROS2_Avanzado.md#herramientas-de-debugging)

#### Ejercicio 4.1: Usar rqt
```bash
rqt
rqt_graph
rqt_console
```

#### Ejercicio 4.2: Plotear datos
```bash
ros2 run mis_ejercicios publisher &
rqt_plot /mi_topic/data
```

#### Ejercicio 4.3: Diagnóstico
```bash
ros2 doctor
ros2 doctor --report
```

#### Ejercicio 4.4: Análisis de rendimiento
```bash
ros2 topic hz /mi_topic
ros2 topic bw /mi_topic
ros2 topic delay /mi_topic
```

---

### Sección 5: Buenas Prácticas

> 📖 **Teoría:** [Guía ROS2 Avanzado - Buenas Prácticas](Guia_ROS2_Avanzado.md#buenas-prácticas)

#### Ejercicio 5.1: Refactorizar nodo con buenas prácticas

Reescribe uno de tus nodos anteriores aplicando:
- Documentación (docstrings)
- Separación de inicialización
- Manejo de errores
- Logs apropiados

#### Ejercicio 5.2: Usar QoS

```python
from rclpy.qos import QoSProfile, ReliabilityPolicy, DurabilityPolicy

qos = QoSProfile(
    reliability=ReliabilityPolicy.BEST_EFFORT,
    durability=DurabilityPolicy.VOLATILE,
    depth=10
)

self.subscription = self.create_subscription(
    String, 'topic', self.callback, qos)
```

---

## Reto Final Integrador: El Guardián del Almacén

Este proyecto final combina todo lo que has aprendido: interfaces personalizadas, actions, services, parámetros y launch files.

### Objetivo

Crear un sistema simple para un robot "Guardián" que patrulla un área, reporta su estado y puede ser reconfigurado en tiempo real.

### Componentes del Sistema

1.  **Paquete de Interfaces (`guardian_interfaces`)**
    -   **Mensaje:** `RobotStatus.msg` con `bateria` (float32), `posicion` (string), `estado` (string: "Patrullando", "En base", "Alerta").
    -   **Acción:** `Patrullar.action` con `puntos_patrulla` (string[]) como goal, `punto_actual` (string) como feedback y `ruta_completada` (bool) como result.
    -   **Servicio:** `CambiarModo.srv` con `modo` (string: "normal", "ahorro") como request y `confirmacion` (bool) como response.

2.  **Paquete del Robot (`guardian_robot`)**
    -   **Nodo Guardián (`guardian_node.py`):**
        -   Publica su estado en un topic `/robot_status` usando el mensaje `RobotStatus`.
        -   Ofrece el servicio `/cambiar_modo` para ajustar su velocidad.
        -   Ofrece el action server `/patrullar` para iniciar una secuencia de patrulla.
        -   Tiene un **parámetro** `velocidad_patrulla` que cambia según el modo del servicio.
    -   **Nodo de Control (`control_station.py`):**
        -   Un cliente que puede llamar al servicio para cambiar el modo.
        -   Un cliente que puede enviar un goal a la acción de patrullar.
        -   Un suscriptor que monitoriza el `/robot_status`.

3.  **Launch File (`start_guardian.launch.py`)**
    -   Lanza el `guardian_node`.
    -   Carga los parámetros iniciales desde un archivo `guardian_params.yaml` (ej: `velocidad_patrulla: 1.0`).

### Pasos Sugeridos

1.  **Crea los paquetes:** `ros2 pkg create --build-type ament_cmake guardian_interfaces` y `ros2 pkg create --build-type ament_python guardian_robot`.
2.  **Define y compila las interfaces:** Crea los archivos `.msg`, `.action` y `.srv`. No olvides modificar `CMakeLists.txt` y `package.xml` de `guardian_interfaces` y compilarlo primero.
3.  **Desarrolla el `guardian_node`:** Implementa el publicador, el servidor de servicio y el servidor de acción.
4.  **Desarrolla el `control_station`:** Implementa los clientes y el suscriptor.
5.  **Crea el `params.yaml` y el `launch file`**.
6.  **¡Prueba todo el sistema!** Lanza el sistema y usa una segunda terminal para ejecutar `control_station` y ver cómo interactúa con el guardián.

<details>
<summary>🏆 Checklist de Éxito</summary>

| Criterio | Comando de Verificación | Resultado Esperado |
| :--- | :--- | :--- |
| **Interfaces compiladas** | `colcon build --packages-select guardian_interfaces` | El build termina sin errores. |
| **Mensaje visible** | `ros2 interface show guardian_interfaces/msg/RobotStatus` | Muestra la definición del mensaje. |
| **Servicio visible** | `ros2 interface show guardian_interfaces/srv/CambiarModo` | Muestra la definición del servicio. |
| **Acción visible** | `ros2 interface show guardian_interfaces/action/Patrullar` | Muestra la definición de la acción. |
| **Nodo Guardián se lanza** | `ros2 launch guardian_robot start_guardian.launch.py` | El nodo se inicia y muestra los logs. |
| **Topic de estado funciona**| `ros2 topic echo /robot_status` | Se reciben mensajes `RobotStatus`. |
| **Servicio funciona** | `ros2 service call /cambiar_modo guardian_interfaces/srv/CambiarModo "{modo: 'ahorro'}"` | El nodo guardián loguea el cambio de modo. |
| **Acción funciona** | `ros2 action send_goal /patrullar guardian_interfaces/action/Patrullar "{puntos_patrulla: ['Punto A', 'Punto B']}"` | El nodo guardián loguea el inicio de la patrulla y envía feedback. |

</details>

---

## Limpiar workspace

```bash
rm -rf ~/ros2_ws/build/*
rm -rf ~/ros2_ws/install/*
rm -rf ~/ros2_ws/log/*
```
