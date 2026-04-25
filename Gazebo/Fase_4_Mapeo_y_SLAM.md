# Fase 4: Mapeo y SLAM (Simultaneous Localization and Mapping)

> 📚 [Volver al Índice de Documentación](../README.md)

## Introducción al Mapeo

El objetivo del SLAM es permitir que el robot cree un mapa de un entorno desconocido mientras se localiza simultáneamente dentro de él. Para que el SLAM funcione correctamente en ROS 2 con Gazebo, necesitamos:

1.  **Odometría:** Datos de cuánto se han movido las ruedas (publicados por Gazebo).
2.  **LiDAR:** Datos de distancias a las paredes (publicados por Gazebo).
3.  **Transformaciones (TF):** La relación espacial entre las partes del robot (ej: `base_link` a `lidar_link`).

---

## 1. Instalación de SLAM Toolbox

Usaremos `slam_toolbox`, que es el estándar actual en ROS 2 Humble por su eficiencia y estabilidad.

```bash
sudo apt update
sudo apt install ros-humble-slam-toolbox
```

---

## 2. Configuración y Lanzamiento

Para empezar a mapear, necesitamos que el robot esté en un mundo con paredes u obstáculos en Gazebo.

### Lanzar SLAM Toolbox (Modo Asíncrono)

```bash
ros2 launch slam_toolbox online_async_launch.py \
    use_sim_time:=True
```

*Nota: `use_sim_time:=True` es CRUCIAL en simulación para que ROS sincronice su reloj con Gazebo.*

---

## 3. El Proceso de Mapeo

1.  Abre **RViz2**.
2.  Añade el componente **Map** y selecciona el topic `/map`.
3.  Mueve el robot lentamente usando el teclado (`teleop_twist_keyboard`).
4.  Verás cómo el mapa se va construyendo en tiempo real.

---

## 4. Guardar el Mapa

Una vez que hayas recorrido todo el entorno y estés satisfecho con el resultado, guarda el mapa para usarlo después en la navegación autónoma.

Puedes hacerlo desde el panel de SLAM Toolbox en RViz o por terminal:

```bash
ros2 run nav2_map_server map_saver_cli -f mi_mapa
```

Esto generará dos archivos:
-   `mi_mapa.yaml`: Configuración del mapa.
-   `mi_mapa.pgm`: La imagen del mapa (píxeles libres, ocupados o desconocidos).

---

## Siguientes Pasos

Continúa con la **[Fase 5: Navegación Autónoma](Fase_5_Navegacion_Autonoma.md)** para el control total del robot.
