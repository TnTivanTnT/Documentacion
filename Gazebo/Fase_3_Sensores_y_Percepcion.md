# Fase 3: Sensores y Percepción (Dando "Vista" al Robot)

> 📚 [Volver al Índice de Documentación](../README.md)

Hasta ahora, tenemos un robot que se mueve, pero es "ciego": no sabe si tiene una pared delante. En esta fase, le daremos un sensor LiDAR para que pueda "ver" y aprenderemos a llevar esa información desde el simulador hasta el cerebro del robot (ROS 2).

---

## 1. El Concepto: ¿Cómo "ve" el robot?

El flujo de información es el siguiente:
1.  **Gazebo:** Genera rayos láser virtuales que chocan contra las paredes.
2.  **El Bridge:** Actúa como un cable que traduce esos rayos de Gazebo al idioma de ROS 2.
3.  **RViz2:** Nos permite a nosotros ver lo mismo que está viendo el robot.
4.  **Tu Código:** Analiza esos datos para decidir si el robot debe frenar o girar.

---

## 2. Paso A: Añadir el Sensor físico al URDF

Abre `description/robot.urdf` y añade estos dos bloques antes de `</robot>`.

### La pieza física (Link y Joint)
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

### El sensor de Gazebo (El plugin)
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

## 3. Paso B: Conectar el Ojo con el Cerebro (El Bridge)

Para que ROS 2 reciba los datos, abre una terminal nueva y ejecuta el "traductor":

```bash
ros2 run ros_gz_bridge parameter_bridge /scan@sensor_msgs/msg/LaserScan@ignition.msgs.LaserScan
```
**¿Por qué este comando?** Porque el LiDAR publica en un formato de Ignition, y nosotros necesitamos que llegue a ROS 2 como un `LaserScan`.

---

## 4. Paso C: Ver a través de los ojos del robot (RViz2)

Ahora comprobaremos si la conexión es correcta usando el visualizador técnico.

1.  Lanza RViz2: `ros2 run rviz2 rviz2`
2.  **Configura el entorno:** En la columna izquierda, cambia `Fixed Frame` de "map" a **`base_link`**.
3.  **Añade el sensor:**
    - Haz clic en **Add** (abajo a la izquierda).
    - Selecciona la pestaña **By topic**.
    - Busca `/scan` y dale a OK.
4.  **Verificación:** Deberías ver líneas rojas o puntos apareciendo alrededor de tu robot. ¡Esos son los obstáculos que el robot está detectando!

---

## 5. Paso D: Detección de Obstáculos (Lógica)

Ahora que los datos están en ROS 2, podemos crear un nodo que tome decisiones. Esto es lo que permite que el robot no choque.

**Ejemplo de lógica para un nodo Python:**
```python
def scan_callback(self, msg):
    # msg.ranges contiene las distancias en 360 grados
    # El centro del array es lo que hay justo delante
    distancia_frontal = msg.ranges[len(msg.ranges)//2]

    if distancia_frontal < 0.5:
        self.get_logger().warn("¡PARED DETECTADA! Frenando...")
        # Aquí enviaríamos una velocidad de 0 al robot
```

---

## Resumen de la Fase 3
-   **Hemos dado un ojo al robot** (URDF).
-   **Hemos conectado el ojo al cerebro** (Bridge).
-   **Hemos aprendido a visualizar sus pensamientos** (RViz2).

---

## Siguientes Pasos

Continúa con la **[Fase 4: Mapeo y SLAM](Fase_4_Mapeo_y_SLAM.md)**. Usaremos esta "vista" para dibujar un mapa de toda la habitación.
