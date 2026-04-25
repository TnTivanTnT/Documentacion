# Fase 3: Sensores y Percepción (Dando "Vista" al Robot)

> 📚 [Volver al Índice de Documentación](../README.md)

En esta fase daremos un sensor LiDAR al robot para que detecte obstáculos. 

> ⚠️ **IMPORTANTE:** Asegúrate de haber actualizado tu archivo de mundo (**`worlds/mi_mundo.sdf`**) con el plugin de sensores como se indica en la [Fase 1](Fase_1_Modelado_y_Entorno.md), o el robot no aparecerá.

---

## 1. URDF Completo con LiDAR

Sobrescribe tu archivo **`description/robot.urdf`** con este código. Ya incluye el chasis, las ruedas, la rueda loca y el LiDAR configurado:

```xml
<?xml version="1.0"?>
<robot name="mi_robot">

  <!-- ORIGEN -->
  <link name="base_link"></link>

  <!-- CHASIS -->
  <link name="chassis">
    <visual><geometry><box size="0.4 0.3 0.1"/></geometry><material name="blue"><color rgba="0 0 1 1"/></material></visual>
    <collision><geometry><box size="0.4 0.3 0.1"/></geometry></collision>
    <inertial><mass value="1.0"/><inertia ixx="0.01" ixy="0" ixz="0" iyy="0.01" iyz="0" izz="0.01"/></inertial>
  </link>
  <joint name="chassis_joint" type="fixed"><parent link="base_link"/><child link="chassis"/><origin xyz="0 0 0.1"/></joint>

  <!-- RUEDA IZQUIERDA -->
  <link name="left_wheel">
    <visual><geometry><cylinder radius="0.1" length="0.05"/></geometry></visual>
    <collision><geometry><cylinder radius="0.1" length="0.05"/></geometry></collision>
    <inertial><mass value="0.2"/><inertia ixx="0.001" ixy="0" ixz="0" iyy="0.001" iyz="0" izz="0.001"/></inertial>
  </link>
  <joint name="left_wheel_joint" type="continuous"><parent link="base_link"/><child link="left_wheel"/><origin xyz="0 0.175 0.1" rpy="-1.57 0 0"/><axis xyz="0 0 1"/></joint>

  <!-- RUEDA DERECHA -->
  <link name="right_wheel">
    <visual><geometry><cylinder radius="0.1" length="0.05"/></geometry></visual>
    <collision><geometry><cylinder radius="0.1" length="0.05"/></geometry></collision>
    <inertial><mass value="0.2"/><inertia ixx="0.001" ixy="0" ixz="0" iyy="0.001" iyz="0" izz="0.001"/></inertial>
  </link>
  <joint name="right_wheel_joint" type="continuous"><parent link="base_link"/><child link="right_wheel"/><origin xyz="0 -0.175 0.1" rpy="-1.57 0 0"/><axis xyz="0 0 1"/></joint>

  <!-- RUEDA LOCA -->
  <link name="caster_wheel">
    <visual><geometry><sphere radius="0.05"/></geometry></visual>
    <collision><geometry><sphere radius="0.05"/></geometry></collision>
    <inertial><mass value="0.1"/><inertia ixx="0.0001" ixy="0" ixz="0" iyy="0.0001" iyz="0" izz="0.0001"/></inertial>
  </link>
  <joint name="caster_wheel_joint" type="fixed"><parent link="base_link"/><child link="caster_wheel"/><origin xyz="0.15 0 0.05"/></joint>

  <!-- LIDAR -->
  <link name="lidar_link">
    <visual><geometry><cylinder radius="0.05" length="0.04"/></geometry><material name="red"><color rgba="1 0 0 1"/></material></visual>
    <collision><geometry><cylinder radius="0.05" length="0.04"/></geometry></collision>
    <inertial><mass value="0.1"/><inertia ixx="0.0001" ixy="0" ixz="0" iyy="0.0001" iyz="0" izz="0.0001"/></inertial>
  </link>
  <joint name="lidar_joint" type="fixed"><parent link="chassis"/><child link="lidar_link"/><origin xyz="0.1 0 0.08"/></joint>

  <!-- PLUGINS -->
  <gazebo>
    <plugin filename="ignition-gazebo-diff-drive-system" name="ignition::gazebo::systems::DiffDrive">
      <left_joint>left_wheel_joint</left_joint>
      <right_joint>right_wheel_joint</right_joint>
      <wheel_separation>0.35</wheel_separation>
      <wheel_radius>0.1</wheel_radius>
      <topic>cmd_vel</topic>
    </plugin>
    <plugin filename="ignition-gazebo-joint-state-publisher-system" name="ignition::gazebo::systems::JointStatePublisher"></plugin>
  </gazebo>

  <gazebo reference="lidar_link">
    <sensor name="gpu_lidar" type="gpu_lidar">
      <pose>0 0 0 0 0 0</pose>
      <visualize>true</visualize>
      <update_rate>10</update_rate>
      <lidar>
        <scan><horizontal><samples>640</samples><resolution>1</resolution><min_angle>-3.14</min_angle><max_angle>3.14</max_angle></horizontal></scan>
        <range><min>0.1</min><max>10.0</max><resolution>0.01</resolution></range>
      </lidar>
      <topic>scan</topic>
      <gz_frame_id>lidar_link</gz_frame_id>
    </sensor>
  </gazebo>

</robot>
```

---

## 2. Ejecución: ¿Cómo ver los datos en RViz2?

Para que todo funcione, necesitas estas terminales abiertas:

### Terminal 1: Gazebo
```bash
ign gazebo worlds/mi_mundo.sdf
```
*No olvides pulsar **Play**.*

### Terminal 2: El Puente (Bridge)
```bash
ros2 run ros_gz_bridge parameter_bridge \
  /scan@sensor_msgs/msg/LaserScan@ignition.msgs.LaserScan \
  /model/mi_robot/joint_state@sensor_msgs/msg/JointState@ignition.msgs.Model --ros-args -r /model/mi_robot/joint_state:=/joint_states
```

### Terminal 3: Publicador de Estado y RViz2
Primero lanza el publicador para que ROS sepa dónde están las piezas:
```bash
ros2 run robot_state_publisher robot_state_publisher --ros-args -p robot_description:="$(cat description/robot.urdf)"
```
Y luego abre **`rviz2`**:
1.  **Fixed Frame:** Escribe `base_link`.
2.  **Add -> By topic -> /scan**.
3.  **Add -> By display type -> RobotModel**.
    - En **Description Topic** escribe: `/robot_description`.

---

## 3. Lógica de Detección de Obstáculos

Un pequeño programa Python puede leer el topic `/scan` para evitar choques.

```python
def scan_callback(self, msg):
    distancia_frontal = msg.ranges[len(msg.ranges)//2]
    if distancia_frontal < 0.5:
        self.get_logger().warn("¡Obstáculo detectado!")
```

---

## Siguientes Pasos

Continúa con la **[Fase 4: Mapeo y SLAM](Fase_4_Mapeo_y_SLAM.md)**.
