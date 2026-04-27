# Fase 3: Sensores y Percepción (Dando "Vista" al Robot)

> 📚 [Volver al Índice de Documentación](../README.md)

En esta fase daremos un sensor LiDAR al robot para que detecte obstáculos. Esto permitirá que el robot pase de ser "ciego" a tener una representación de lo que le rodea.

> ⚠️ **IMPORTANTE:** Asegúrate de haber actualizado tu archivo de mundo (**`worlds/mi_mundo.sdf`**) con el plugin de sensores como se indica en la [Fase 1](Fase_1_Modelado_y_Entorno.md).

---

## 1. URDF Completo con LiDAR y Telemetría

Sobrescribe tu archivo **`description/robot.urdf`** con este código. Incluye el chasis, las ruedas, el LiDAR y los plugins necesarios para que RViz2 no dé errores de posición:

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

  <!-- LIDAR (Láser) -->
  <link name="lidar_link">
    <visual><geometry><cylinder radius="0.05" length="0.04"/></geometry><material name="red"><color rgba="1 0 0 1"/></material></visual>
    <collision><geometry><cylinder radius="0.05" length="0.04"/></geometry></collision>
    <inertial><mass value="0.1"/><inertia ixx="0.0001" ixy="0" ixz="0" iyy="0.0001" iyz="0" izz="0.0001"/></inertial>
  </link>
  <joint name="lidar_joint" type="fixed"><parent link="chassis"/><child link="lidar_link"/><origin xyz="0.1 0 0.08"/></joint>

  <!-- PLUGINS DE GAZEBO -->
  <gazebo>
    <!-- Movimiento diferencial -->
    <plugin filename="ignition-gazebo-diff-drive-system" name="ignition::gazebo::systems::DiffDrive">
      <left_joint>left_wheel_joint</left_joint>
      <right_joint>right_wheel_joint</right_joint>
      <wheel_separation>0.35</wheel_separation>
      <wheel_radius>0.1</wheel_radius>
      <topic>cmd_vel</topic>
      
      <odom_publish_frequency>50</odom_publish_frequency>
      <odom_tf>true</odom_tf>
      <frame_id>odom</frame_id>
      <child_frame_id>base_link</child_frame_id>
    </plugin>
    <!-- Publicador de estado de las ruedas -->
    <plugin filename="ignition-gazebo-joint-state-publisher-system" name="ignition::gazebo::systems::JointStatePublisher">
     <topic>/model/mi_robot/joint_state</topic>
     <joint_name>left_wheel_joint</joint_name>
      <joint_name>right_wheel_joint</joint_name>
    </plugin>
  </gazebo>

  <!-- Configuración del sensor LiDAR -->
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

## 2. Ejecución Completa (Paso a Paso)

Para ver tu robot en Gazebo y sus datos en RViz2, abre estas 5 terminales:

### Terminal 1: Abrir el Mundo
```bash
ign gazebo worlds/mi_mundo.sdf
```
*Haz clic en **Play** inmediatamente.*

### Terminal 2: Meter el Robot (Spawn)
```bash
ros2 run ros_gz_sim create -file description/robot.urdf -name mi_robot -z 0.01
```

### Terminal 3: El Puente (Bridge)
```bash
ros2 run ros_gz_bridge parameter_bridge \
  /cmd_vel@geometry_msgs/msg/Twist@ignition.msgs.Twist \
  /scan@sensor_msgs/msg/LaserScan@ignition.msgs.LaserScan \
  /model/mi_robot/joint_state@sensor_msgs/msg/JointState@ignition.msgs.Model \
  /model/mi_robot/tf@tf2_msgs/msg/TFMessage@ignition.msgs.Pose_V
```

### Terminal 4: Publicador de Estado (Robot State Publisher)
Este comando le dice a ROS cómo está montado el robot y dónde están las ruedas.
```bash
ros2 run robot_state_publisher robot_state_publisher --ros-args \
  -p robot_description:="$(cat description/robot.urdf)" \
  -p use_sim_time:=True \
  -r /joint_states:=/model/mi_robot/joint_state
```

### Terminal 5: RViz2
```bash
ros2 run rviz2 rviz2 --ros-args -p use_sim_time:=True
```

---

## 3. Configuración de RViz2

1.  **Fixed Frame:** Escribe `base_link`.
2.  **Add -> By display type -> RobotModel**.
    - En **Description Topic** escribe: `/robot_description`.
3.  **Add -> By topic -> /scan**.

---

## 4. Solución de errores visuales (Ruedas Rojas)

Si las ruedas salen rojas o en el centro:
1. Asegúrate de que la Terminal 4 incluya `-p use_sim_time:=True`.
2. En RViz2, dentro de **RobotModel**, cambia **Reliability Policy** a `Best Effort`.

---

## Siguientes Pasos

Continúa con la **[Fase 4: Mapeo y SLAM](Fase_4_Mapeo_y_SLAM.md)**.
