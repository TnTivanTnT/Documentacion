# Fase 1: Modelado y Entorno en Ignition Gazebo

> 📚 [Volver al Índice de Documentación](../README.md)
> ⚠️ **Aviso:** Antes de empezar esta fase, asegúrate de haber completado la **[Fase 0: Inicio del Proyecto](Fase_0_Inicio_del_Proyecto.md)** para tener la estructura de carpetas lista.

## Introducción a Ignition Gazebo (Fortress)

Ignition Gazebo 6 es el sucesor de Gazebo "Classic". Es más modular y requiere el `ros_gz_bridge` para hablar con ROS 2.

---

## 1. Estructura del Paquete

Asegúrate de estar en tu carpeta de paquete (`mi_robot_sim/`) y verifica las subcarpetas:
`description/`, `worlds/`, `launch/`, `config/`, `meshes/`.

---

## 2. Creando el Robot en URDF

El URDF describe la física del robot mediante **Links** (piezas) y **Joints** (uniones).

### Estructura básica: Link
Cada pieza tiene tres bloques fundamentales:
- **Visual:** Lo que vemos en la simulación.
- **Collision:** Fronteras para choques.
- **Inertial:** Masa y distribución (¡Vital para la física!).

### Ejemplo: Robot Diferencial Completo (3 ruedas)
Crea el archivo `description/robot.urdf` y pega este código (Chasis + 2 Ruedas + 1 Rueda Loca):

```xml
<?xml version="1.0"?>
<robot name="mi_robot">

  <!-- CHASIS (Cuerpo principal) -->
  <link name="base_link">
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
    <origin xyz="0 0.175 0" rpy="-1.57 0 0"/>
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
    <origin xyz="0 -0.175 0" rpy="-1.57 0 0"/>
    <axis xyz="0 0 1"/>
  </joint>

  <!-- RUEDA LOCA (CASTER WHEEL) - Delantera para equilibrio -->
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
    <origin xyz="0.15 0 -0.05" rpy="0 0 0"/>
  </joint>

  <!-- PLUGIN DE CONTROL -->
  <gazebo>
    <plugin filename="ignition-gazebo-diff-drive-system" name="ignition::gazebo::systems::DiffDrive">
      <left_joint>left_wheel_joint</left_joint>
      <right_joint>right_wheel_joint</right_joint>
      <wheel_separation>0.35</wheel_separation>
      <wheel_radius>0.1</wheel_radius>
      <topic>cmd_vel</topic>
    </plugin>
  </gazebo>

</robot>
```

---

## 3. Uso de Modelos 3D (Meshes)

Si quieres que tu robot se vea como el diseño real de SolidWorks en lugar de cajas azules:

### Reemplazar Geometría por Mesh
Dentro de la etiqueta `<visual>`, cambia `<box/>` o `<cylinder/>` por `<mesh/>`:

```xml
<visual>
  <geometry>
    <!-- package://nombre_paquete/ruta/al/archivo -->
    <mesh filename="package://mi_robot_sim/meshes/chasis.dae" scale="0.001 0.001 0.001"/>
  </geometry>
</visual>
```

> ⚠️ **Recuerda:** Mantén la etiqueta `<collision>` con formas simples (`box`, `cylinder`) aunque la parte `<visual>` use un modelo 3D complejo. Esto hará que la simulación sea fluida.

---

## 4. El Escenario (World)

Elescenario es un archivo `.sdf`. Crea `worlds/mi_mundo.sdf`:

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

    <model name="ground_plane">
      <static>true</static>
      <link name="link">
        <collision name="collision">
          <geometry><plane><normal>0 0 1</normal></plane></geometry>
        </collision>
        <visual name="visual">
          <geometry><plane><normal>0 0 1</normal></plane></geometry>
        </visual>
      </link>
    </model>
  </world>
</sdf>
```

---

## 5. Lanzamiento de la Simulación

Para ver tu mundo vacío:
```bash
ign gazebo worlds/mi_mundo.sdf
```

Para insertar tu robot URDF en el mundo:
```bash
ros2 run ros_gz_sim create -file description/robot.urdf -name mi_robot
```

---

## Siguientes Pasos

Continúa con la **[Fase 2: Teleoperación y Bridge](Fase_2_Teleoperacion_y_Bridge.md)** para conectar ROS 2 con la simulación y empezar a mover tu robot.
