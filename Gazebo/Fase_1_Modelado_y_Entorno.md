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

### Ejemplo: Robot Diferencial Simple
Crea el archivo `description/robot.urdf` y pega este código:

```xml
<?xml version="1.0"?>
<robot name="mi_robot">

  <!-- Link principal (Chasis) -->
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

  <!-- Rueda Izquierda -->
  <link name="left_wheel">
    <visual>
      <geometry><cylinder radius="0.1" length="0.05"/></geometry>
    </visual>
    <inertial>
      <mass value="0.2"/>
      <inertia ixx="0.001" ixy="0" ixz="0" iyy="0.001" iyz="0" izz="0.001"/>
    </inertial>
  </link>

  <!-- Unión de la rueda -->
  <joint name="left_wheel_joint" type="continuous">
    <parent link="base_link"/>
    <child link="left_wheel"/>
    <origin xyz="0 0.175 0" rpy="-1.57 0 0"/>
    <axis xyz="0 0 1"/>
  </joint>

  <!-- Rueda Derecha (Ejercicio: intenta añadirla tú siguiendo la izquierda) -->

  <!-- Plugin de Control (Diff Drive) -->
  <gazebo>
    <plugin
      filename="ignition-gazebo-diff-drive-system"
      name="ignition::gazebo::systems::DiffDrive">
      <left_joint>left_wheel_joint</left_joint>
      <!-- Asegúrate de añadir el joint de la derecha cuando lo crees -->
      <wheel_separation>0.35</wheel_separation>
      <wheel_radius>0.1</wheel_radius>
      <topic>cmd_vel</topic>
    </plugin>
  </gazebo>

</robot>
```

---

## 3. Importación de Modelos desde CAD (SolidWorks, etc.)

Si tienes un diseño en SolidWorks, puedes exportar tus piezas como archivos `.dae` (Collada) o `.stl`.

- **Visual:** Usa el `.dae` detallado.
- **Collision:** Usa formas simples (box, cylinder) para no saturar el motor de física.
- **Escalado:** Recuerda usar `scale="0.001 0.001 0.001"` si tu CAD está en milímetros.

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
