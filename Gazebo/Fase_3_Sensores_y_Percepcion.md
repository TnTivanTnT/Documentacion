# Fase 3: Sensores y Percepción

> 📚 [Volver al Índice de Documentación](../README.md)

Para que el robot pueda detectar obstáculos y mapear, necesita un sensor de rango (LiDAR). En Ignition Gazebo, la integración se hace en dos pasos dentro de tu archivo **`description/robot.urdf`**.

---

## 1. Integración del LiDAR en el URDF

Abre tu archivo `description/robot.urdf` y añade los siguientes bloques de código **antes de la etiqueta final `</robot>`**.

### Paso A: Crear la pieza física (Link y Joint)
Esto define dónde está colocado el sensor físicamente en el robot.

```xml
  <!-- LINK DEL LIDAR (La pieza física del sensor) -->
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

  <!-- Unión del LiDAR al chasis -->
  <joint name="lidar_joint" type="fixed">
    <parent link="chassis"/>
    <child link="lidar_link"/>
    <origin xyz="0.1 0 0.08" rpy="0 0 0"/> <!-- Ajusta la altura según tu robot -->
  </joint>
```

### Paso B: Configurar el Sensor para Gazebo
Este bloque le dice a Gazebo que el `lidar_link` que acabamos de crear debe comportarse como un sensor láser real.

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

## 2. Configurar el Bridge para el Sensor

Ahora que Gazebo genera datos en el topic `scan`, necesitamos pasarlos a ROS 2 como un mensaje del tipo `sensor_msgs/msg/LaserScan`.

Añade esto a tu configuración del bridge (o ejecútalo por terminal):

```bash
ros2 run ros_gz_bridge parameter_bridge /scan@sensor_msgs/msg/LaserScan@ignition.msgs.LaserScan
```

---

## 3. Visualización en RViz2

Con los datos fluyendo hacia ROS 2, podemos ver qué ve el robot:

1.  Lanza RViz2: `ros2 run rviz2 rviz2`
2.  Añade el componente **LaserScan**.
3.  Configura el topic a `/scan`.
4.  Cambia el **Fixed Frame** a `base_link` (para ver los puntos moviéndose con el robot).

---

## 4. Detección de Obstáculos (Python)

Con los datos de `/scan`, puedes crear un nodo que evite choques:

```python
def listener_callback(self, msg):
    # Cogemos la distancia mínima detectada al frente
    distancia_min = min(msg.ranges)
    if distancia_min < 0.5:
        self.get_logger().warn("¡CUIDADO! Obstáculo a menos de 50cm")
```

---

## Siguientes Pasos

Continúa con la **[Fase 4: Mapeo y SLAM](Fase_4_Mapeo_y_SLAM.md)** para empezar a construir un mapa con los datos del LiDAR.
