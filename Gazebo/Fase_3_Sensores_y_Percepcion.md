# Fase 3: Sensores y Percepción (Dando "Vista" al Robot)

> 📚 [Volver al Índice de Documentación](../README.md)

En esta fase daremos un sensor LiDAR al robot para que detecte obstáculos.

---

## 1. Integración del LiDAR en el URDF

Abre `description/robot.urdf` y añade estos bloques antes de la última línea `</robot>`.

### El soporte físico (Link y Joint)
```xml
  <link name="lidar_link">
    <visual>
      <geometry><cylinder radius="0.05" length="0.04"/></geometry>
      <material name="red"><color rgba="1 0 0 1"/></material>
    </visual>
    <collision>
      <geometry><cylinder radius="0.05" length="0.04"/></geometry>
    </collision>
    <inertial>
      <mass value="0.1"/>
      <inertia ixx="0.0001" ixy="0" ixz="0" iyy="0.0001" iyz="0" izz="0.0001"/>
    </inertial>
  </link>

  <joint name="lidar_joint" type="fixed">
    <parent link="chassis"/>
    <child link="lidar_link"/>
    <origin xyz="0.1 0 0.08" rpy="0 0 0"/>
  </joint>
```

### El sensor virtual (Plugin)
```xml
  <gazebo reference="lidar_link">
    <sensor name="gpu_lidar" type="gpu_lidar">
      <pose>0 0 0 0 0 0</pose>
      <visualize>true</visualize>
      <update_rate>10</update_rate>
      <lidar>
        <scan>
          <horizontal>
            <samples>640</samples>
            <resolution>1</resolution>
            <min_angle>-3.14</min_angle>
            <max_angle>3.14</max_angle>
          </horizontal>
        </scan>
        <range>
          <min>0.1</min>
          <max>10.0</max>
          <resolution>0.01</resolution>
        </range>
      </lidar>
      <topic>scan</topic>
      <gz_frame_id>lidar_link</gz_frame_id>
    </sensor>
  </gazebo>
```

---

## 2. Ejecución: ¿Cómo ver los datos en RViz2?

Para que los puntos aparezcan en RViz2, necesitas tener tres terminales funcionando a la vez:

### Terminal 1: El Puente (Bridge)
```bash
ros2 run ros_gz_bridge parameter_bridge \
  /scan@sensor_msgs/msg/LaserScan@ignition.msgs.LaserScan \
  /model/mi_robot/joint_state@sensor_msgs/msg/JointState@ignition.msgs.Model --ros-args -r /model/mi_robot/joint_state:=/joint_states
```

### Terminal 2: El Publicador de Estado (Robot State Publisher)
**CRUCIAL:** RViz necesita saber que el LiDAR está "pegado" al chasis. Este comando lee tu URDF y publica las posiciones de las piezas.
```bash
ros2 run robot_state_publisher robot_state_publisher --ros-args -p robot_description:="$(cat description/robot.urdf)"
```

### Terminal 3: RViz2
Abre el visualizador y configúralo así:
1.  Ejecuta `rviz2`.
2.  **Fixed Frame:** Cámbialo a `base_link` (escribe el nombre a mano si no sale).
3.  **Añadir Láser:** Dale a **Add** -> **By topic** -> selecciona **/scan**.
4.  **Añadir Robot:**
    - Dale a **Add** -> **By display type** -> selecciona **RobotModel**.
    - En la configuración de **RobotModel** (izquierda), busca **Description Topic** y escribe: `/robot_description`.
    - Asegúrate de que **Description Source** esté en `Topic`.


---

## 3. Lógica de Detección de Obstáculos

Ahora que los datos están en ROS 2, un programa puede leerlos para tomar decisiones.

```python
def scan_callback(self, msg):
    # La distancia justo delante es el centro de la lista
    distancia_frontal = msg.ranges[len(msg.ranges)//2]

    if distancia_frontal < 0.5:
        self.get_logger().warn("¡Obstáculo detectado!")
```

---

## Siguientes Pasos

Continúa con la **[Fase 4: Mapeo y SLAM](Fase_4_Mapeo_y_SLAM.md)** para guardar un mapa de lo que el láser está viendo.
