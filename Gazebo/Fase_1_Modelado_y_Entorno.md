# Fase 1: Modelado y Entorno en Ignition Gazebo

> 📚 [Volver al Índice de Documentación](../README.md)
> ⚠️ **Aviso:** Antes de empezar esta fase, asegúrate de haber completado la **[Fase 0: Inicio del Proyecto](Fase_0_Inicio_del_Proyecto.md)** para tener la estructura de carpetas lista.

## 1. El Robot Diferencial en URDF

Crea el archivo **`description/robot.urdf`** y pega este código completo. Es un robot funcional con 3 apoyos (2 ruedas motrices y 1 loca) diseñado desde el suelo hacia arriba.

```xml
<?xml version="1.0"?>
<robot name="mi_robot">

  <!-- LINK DE REFERENCIA PARA EL MAPA-->
  <link name="base_footprint"></link>
  
  <!-- LINK DE ORIGEN (En el suelo, entre las ruedas) -->
  <link name="base_link"></link>

  <!-- CHASIS -->
  <link name="chassis">
    <visual>
      <geometry><box size="0.4 0.3 0.1"/></geometry>
      <material name="blue"><color rgba="0 0 1 1"/></material>
    </visual>
    <collision>
      <geometry><box size="0.4 0.3 0.1"/></geometry>
    </collision>
    <inertial>
      <mass value="1.0"/>
      <inertia ixx="0.01" ixy="0" ixz="0" iyy="0.01" iyz="0" izz="0.01"/>
    </inertial>
  </link>

  <joint name="chassis_joint" type="fixed">
    <parent link="base_link"/><child link="chassis"/>
    <origin xyz="0 0 0.1"/>
  </joint>

  <!-- RUEDA IZQUIERDA -->
  <link name="left_wheel">
    <visual>
      <geometry><cylinder radius="0.1" length="0.05"/></geometry>
    </visual>
    <collision>
      <geometry><cylinder radius="0.1" length="0.05"/></geometry>
    </collision>
    <inertial>
      <mass value="0.2"/>
      <inertia ixx="0.001" ixy="0" ixz="0" iyy="0.001" iyz="0" izz="0.001"/>
    </inertial>
  </link>

  <joint name="left_wheel_joint" type="continuous">
    <parent link="base_link"/><child link="left_wheel"/>
    <origin xyz="0 0.175 0.1" rpy="-1.57 0 0"/>
    <axis xyz="0 0 1"/>
  </joint>

  <!-- RUEDA DERECHA -->
  <link name="right_wheel">
    <visual>
      <geometry><cylinder radius="0.1" length="0.05"/></geometry>
    </visual>
    <collision>
      <geometry><cylinder radius="0.1" length="0.05"/></geometry>
    </collision>
    <inertial>
      <mass value="0.2"/>
      <inertia ixx="0.001" ixy="0" ixz="0" iyy="0.001" iyz="0" izz="0.001"/>
    </inertial>
  </link>

  <joint name="right_wheel_joint" type="continuous">
    <parent link="base_link"/><child link="right_wheel"/>
    <origin xyz="0 -0.175 0.1" rpy="-1.57 0 0"/>
    <axis xyz="0 0 1"/>
  </joint>

  <!-- RUEDA LOCA (CASTER WHEEL) -->
  <link name="caster_wheel">
    <visual>
      <geometry><sphere radius="0.05"/></geometry>
    </visual>
    <collision>
      <geometry><sphere radius="0.05"/></geometry>
    </collision>
    <inertial>
      <mass value="0.1"/>
      <inertia ixx="0.0001" ixy="0" ixz="0" iyy="0.0001" iyz="0" izz="0.0001"/>
    </inertial>
  </link>

  <joint name="caster_wheel_joint" type="fixed">
    <parent link="base_link"/><child link="caster_wheel"/>
    <origin xyz="0.15 0 0.05" rpy="0 0 0"/>
  </joint>

  <!-- PLUGIN DE CONTROL (Diff Drive) -->
  <gazebo>
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
    <!-- PLUGIN PARA PUBLICAR LA POSICIÓN DE LAS RUEDAS (Joint States) -->
    <plugin filename="ignition-gazebo-joint-state-publisher-system" name="ignition::gazebo::systems::JointStatePublisher">
    </plugin>
  </gazebo>

</robot>
```

---

## 2. El Escenario (World)

Crea **`worlds/mi_mundo.sdf`**:

```xml
<?xml version="1.0" ?>
<sdf version="1.6">
  <world name="mi_mundo">
    <physics name="1ms" type="ignored">
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1.0</real_time_factor>
    </physics>

    <plugin filename="libignition-gazebo-physics-system.so" name="ignition::gazebo::systems::Physics"/>
    <plugin filename="libignition-gazebo-user-commands-system.so" name="ignition::gazebo::systems::UserCommands"/>
    <plugin filename="libignition-gazebo-scene-broadcaster-system.so" name="ignition::gazebo::systems::SceneBroadcaster"/>
    <plugin filename="libignition-gazebo-sensors-system.so" name="ignition::gazebo::systems::Sensors">
      <render_engine>ogre2</render_engine>
    </plugin>

    <light type="directional" name="sun">
      <cast_shadows>true</cast_shadows>
      <pose>0 0 10 0 0 0</pose>
      <diffuse>0.8 0.8 0.8 1</diffuse>
      <specular>0.2 0.2 0.2 1</specular>
      <direction>-0.5 0.1 -0.9</direction>
    </light>

    <model name="ground_plane">
      <static>true</static>
      <link name="link">
        <collision name="collision">
          <geometry><plane><normal>0 0 1</normal><size>20 20</size></plane></geometry>
        </collision>
        <visual name="visual">
          <geometry><plane><normal>0 0 1</normal><size>20 20</size></plane></geometry>
          <material><ambient>0.8 0.8 0.8 1</ambient><diffuse>0.8 0.8 0.8 1</diffuse></material>
        </visual>
      </link>
    </model>

    <model name="wall_north">
      <static>true</static>
      <pose>5 0 0.5 0 0 0</pose>
      <link name="link">
        <collision name="collision"><geometry><box><size>0.1 10 1</size></box></geometry></collision>
        <visual name="visual"><geometry><box><size>0.1 10 1</size></box></geometry></visual>
      </link>
    </model>
    <model name="wall_south">
      <static>true</static>
      <pose>-5 0 0.5 0 0 0</pose>
      <link name="link">
        <collision name="collision"><geometry><box><size>0.1 10 1</size></box></geometry></collision>
        <visual name="visual"><geometry><box><size>0.1 10 1</size></box></geometry></visual>
      </link>
    </model>
    <model name="wall_east">
      <static>true</static>
      <pose>0 5 0.5 0 0 1.57</pose>
      <link name="link">
        <collision name="collision"><geometry><box><size>0.1 10 1</size></box></geometry></collision>
        <visual name="visual"><geometry><box><size>0.1 10 1</size></box></geometry></visual>
      </link>
    </model>
    <model name="wall_west">
      <static>true</static>
      <pose>0 -5 0.5 0 0 1.57</pose>
      <link name="link">
        <collision name="collision"><geometry><box><size>0.1 10 1</size></box></geometry></collision>
        <visual name="visual"><geometry><box><size>0.1 10 1</size></box></geometry></visual>
      </link>
    </model>

    <model name="box_1">
      <pose>2 2 0.5 0 0 0</pose>
      <link name="link">
        <collision name="collision"><geometry><box><size>1 1 1</size></box></geometry></collision>
        <visual name="visual"><geometry><box><size>1 1 1</size></box></geometry></visual>
      </link>
    </model>

    <model name="cylinder_1">
      <pose>-2 3 0.5 0 0 0</pose>
      <link name="link">
        <collision name="collision"><geometry><cylinder><radius>0.5</radius><length>1</length></cylinder></geometry></collision>
        <visual name="visual"><geometry><cylinder><radius>0.5</radius><length>1</length></cylinder></geometry></visual>
      </link>
    </model>

    <model name="box_2">
      <pose>0 -3 0.5 0 0 0.78</pose>
      <link name="link">
        <collision name="collision"><geometry><box><size>0.5 2 1</size></box></geometry></collision>
        <visual name="visual"><geometry><box><size>0.5 2 1</size></box></geometry></visual>
      </link>
    </model>

  </world>
</sdf>
```

---

## 3. Lanzamiento

1. Abre Gazebo con el mundo:
```bash
ign gazebo worlds/mi_mundo.sdf
```

2. En otra terminal, inserta el robot (pulsa Play en Gazebo antes):
```bash
ros2 run ros_gz_sim create -file description/robot.urdf -name mi_robot -z 0.01
```

---

## Siguientes Pasos

Continúa con la **[Fase 2: Teleoperación y Bridge](Fase_2_Teleoperacion_y_Bridge.md)**.
