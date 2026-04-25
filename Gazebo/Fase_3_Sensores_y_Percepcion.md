# Fase 3: Sensores y Percepción (Dando "Vista" al Robot)

> 📚 [Volver al Índice de Documentación](../README.md)

---

## 1. URDF Completo con LiDAR

(Mantén el mismo URDF de la versión anterior, está correcto).

---

## 2. Ejecución Completa (Paso a Paso)

Para solucionar los errores de "No transform" y que las ruedas no se junten en el centro, es **obligatorio** usar el tiempo de simulación.

### Terminal 1: Gazebo
```bash
ign gazebo worlds/mi_mundo.sdf
```
*Pulsa **Play**.*

### Terminal 2: Spawn del Robot
```bash
ros2 run ros_gz_sim create -file description/robot.urdf -name mi_robot -z 0.01
```

### Terminal 3: El Puente Maestro
```bash
ros2 run ros_gz_bridge parameter_bridge \
  /cmd_vel@geometry_msgs/msg/Twist@ignition.msgs.Twist \
  /scan@sensor_msgs/msg/LaserScan@ignition.msgs.LaserScan \
  /model/mi_robot/joint_state@sensor_msgs/msg/JointState[ignition.msgs.Model
```

### Terminal 4: Publicador de Estado (CON TIEMPO DE SIMULACIÓN)
Este comando es el que suele fallar. Debes decirle que use el tiempo de Gazebo (`use_sim_time:=True`) y mapear las ruedas:
```bash
ros2 run robot_state_publisher robot_state_publisher --ros-args \
  -p robot_description:="$(cat description/robot.urdf)" \
  -p use_sim_time:=True \
  -r /joint_states:=/model/mi_robot/joint_state
```

### Terminal 5: RViz2
Abre RViz con tiempo de simulación:
```bash
ros2 run rviz2 rviz2 --ros-args -p use_sim_time:=True
```
**Configuración en RViz:**
1.  **Fixed Frame:** Escribe `base_link`.
2.  **Add -> By topic -> /scan**.
3.  **Add -> By display type -> RobotModel**.
    - **Description Topic**: `/robot_description`.
    - **Description Source**: `Topic`.

---

## 3. Solución de errores visuales

-   **Ruedas en el centro o rojas:** Significa que te falta el parámetro `use_sim_time:=True` en la Terminal 4 o 5. Sin esto, ROS 2 ignora la posición de las ruedas.
-   **El robot no se mueve:** Asegúrate de que el Bridge (Terminal 3) esté corriendo y que hayas pulsado **Play** en Gazebo.

---

## Siguientes Pasos

Continúa con la **[Fase 4: Mapeo y SLAM](Fase_4_Mapeo_y_SLAM.md)**.
