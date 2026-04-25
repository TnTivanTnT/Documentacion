# Fase 3: Sensores y Percepción

> 📚 [Volver al Índice de Documentación](../README.md)

## Configuración del LiDAR en el Robot

Para detectar obstáculos y mapear, el robot necesita un sensor de rango (LiDAR). En Ignition Gazebo, los sensores se definen dentro del archivo URDF/SDF del robot.

### 1. Definición del Sensor en el URDF

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

El sensor publica datos en Ignition. Necesitamos pasarlos a ROS 2 como un mensaje del tipo `sensor_msgs/msg/LaserScan`.

Añade esto a tu configuración del bridge:

```yaml
- ros_topic_name: "/scan"
  gz_topic_name: "/scan"
  ros_type_name: "sensor_msgs/msg/LaserScan"
  gz_type_name: "ignition.msgs.LaserScan"
  direction: GZ_TO_ROS
```

---

## 3. Visualización en RViz2

Una vez que el bridge está enviando los datos del sensor `/scan`, podemos visualizarlos en ROS 2:

1.  Lanza RViz2: `ros2 run rviz2 rviz2`
2.  Añade el componente **LaserScan**.
3.  Configura el topic a `/scan`.
4.  Cambia el **Fixed Frame** a `base_link` o `odom`.

---

## 4. Detección de Obstáculos (Lógica Básica)

Con los datos de `/scan`, podrías crear un nodo simple en Python que analice los rangos:

```python
def listener_callback(self, msg):
    # El valor central del array suele ser el frente del robot
    distancia_frente = msg.ranges[len(msg.ranges)//2]
    if distancia_frente < 0.5:
        self.get_logger().warn("¡Obstáculo detectado!")
```

---

## Siguientes Pasos

Continúa con la **[Fase 4: Mapeo y SLAM](Fase_4_Mapeo_y_SLAM.md)** para generar mapas digitales.
