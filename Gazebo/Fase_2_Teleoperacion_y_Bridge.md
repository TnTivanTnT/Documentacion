# Fase 2: Teleoperación y Bridge

> 📚 [Volver al Índice de Documentación](../README.md)

## El Concepto del Bridge

Ignition Gazebo usa su propio sistema de transporte de mensajes (`Ignition Transport`). Para que ROS 2 pueda enviar comandos de velocidad (`cmd_vel`) al robot dentro de la simulación, necesitamos un puente: el **`ros_gz_bridge`**.

---

## 1. Configuración del Bridge

El bridge puede configurarse por terminal o mediante un archivo YAML (recomendado para proyectos reales).

### Parámetros del Bridge (`config/bridge_params.yaml`)

```yaml
# Mapeo de ROS 2 a Ignition para el control de movimiento
- ros_topic_name: "/cmd_vel"
  gz_topic_name: "/cmd_vel"
  ros_type_name: "geometry_msgs/msg/Twist"
  gz_type_name: "ignition.msgs.Twist"
  direction: ROS_TO_GZ

# Mapeo de Ignition a ROS 2 para la odometría
- ros_topic_name: "/odom"
  gz_topic_name: "/model/mi_robot/odometry"
  ros_type_name: "nav_msgs/msg/Odometry"
  gz_type_name: "ignition.msgs.Odometry"
  direction: GZ_TO_ROS
```

---

## 2. Lanzar el Bridge

Para arrancar el puente desde la terminal manualmente (ejemplo para `cmd_vel`):

```bash
ros2 run ros_gz_bridge parameter_bridge /cmd_vel@geometry_msgs/msg/Twist@ignition.msgs.Twist
```

*Nota: El símbolo `@` define el mapeo de tipos.*

---

## 3. Teleoperación por Teclado

Una vez que el bridge está funcionando, puedes controlar tu robot diferencial usando el paquete estándar de ROS 2:

1.  Asegúrate de que la simulación en Gazebo no esté en pausa (clic en el botón Play).
2.  En una nueva terminal, ejecuta:
    ```bash
    ros2 run teleop_twist_keyboard teleop_twist_keyboard
    ```

### ¿Qué está pasando por debajo?
1.  `teleop_twist_keyboard` publica un mensaje `geometry_msgs/Twist` en el topic `/cmd_vel` de ROS 2.
2.  El `ros_gz_bridge` intercepta ese mensaje y lo convierte a `ignition.msgs.Twist`.
3.  El plugin `DiffDrive` dentro de Gazebo recibe el mensaje y aplica fuerza a las ruedas del robot.

---

## Siguientes Pasos

Continúa con la **[Fase 3: Sensores y Percepción](Fase_3_Sensores_y_Percepcion.md)** para añadir visión al robot.
