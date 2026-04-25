# Fase 1: Modelado y Entorno en Ignition Gazebo

> 📚 [Volver al Índice de Documentación](../README.md)
> ⚠️ **Aviso:** Antes de empezar esta fase, asegúrate de haber completado la **[Fase 0: Inicio del Proyecto](Fase_0_Inicio_del_Proyecto.md)** para tener la estructura de carpetas lista.

## Introducción a Ignition Gazebo (Fortress)

Ignition Gazebo 6 (ahora llamado Gazebo Sim) es el sucesor del Gazebo "Classic" (v11). Es más modular, eficiente y está diseñado para integrarse nativamente con ROS 2 mediante un sistema de plugins y un "bridge".

### Diferencias Clave
- **Classic:** Un solo proceso pesado. Comunicación mediante sockets internos.
- **Ignition:** Basado en componentes. Comunicación mediante `Ignition Transport`. Necesita el `ros_gz_bridge` para hablar con ROS 2.

---

## Estructura del Paquete

Para este proyecto, lo ideal es organizar tu workspace de ROS 2 así:

```text
mi_robot_sim/
├── description/
│   └── robot.urdf.xacro      # Geometría y plugins del robot
├── launch/
│   └── sim.launch.py         # Lanza Gazebo y el robot
├── worlds/
│   └── habitacion.sdf        # Escenario de simulación
└── package.xml
```

---

### 1. El Modelo del Robot Diferencial

Un robot diferencial en Gazebo necesita tres elementos fundamentales:
1.  **Visual:** Cómo se ve (cajas, cilindros o mallas 3D).
2.  **Collision:** Las fronteras físicas para los choques (deben ser simples).
3.  **Inertial:** Masa y distribución de peso.

---

## 2. Importación de Modelos desde CAD (SolidWorks, etc.)

Si tienes un diseño en SolidWorks, no necesitas conformarte con cajas y cilindros. Puedes exportar tus piezas e integrarlas.

### Formatos recomendados
- **`.dae` (Collada):** RECOMENDADO para la parte **Visual**. Conserva colores, texturas y materiales del CAD.
- **`.stl`:** Recomendado solo si no te importan los colores o para mallas de **Colisión** simples.

### El problema del escalado (mm vs metros)
Los programas de CAD suelen trabajar en milímetros, pero ROS 2 y Gazebo trabajan en **metros**. 
- Si exportas un chasis de 500mm y lo importas sin más, ¡medirá 500 metros en Gazebo!
- **Solución:** En el URDF, aplica un escalado de `0.001`:
  ```xml
  <mesh filename="package://mi_robot_sim/meshes/chasis.dae" scale="0.001 0.001 0.001"/>
  ```

### Visual vs Collision: Regla de Oro
**NUNCA** uses el modelo de SolidWorks con todos sus tornillos y detalles para la etiqueta `<collision>`. 
- **Visual:** Usa tu archivo `.dae` detallado.
- **Collision:** Usa cajas (`<box>`) o cilindros (`<cylinder>`) que envuelvan la forma general. Calcular choques entre miles de polígonos de un tornillo de SolidWorks destruirá el rendimiento de tu PC.

---

## 3. El Mundo (World)

El "mundo" es el escenario donde vivirá tu robot. En Ignition no se crea solo; debes crear un archivo de texto con extensión `.sdf` dentro de la carpeta `worlds/`.

### Crear el archivo manualmente
Ejecuta en tu terminal (estando dentro del paquete):
```bash
touch worlds/mi_mundo.sdf
```

### Contenido básico de un mundo
Abre el archivo y pega este código (incluye sol, suelo y la física necesaria):

```xml
<?xml version="1.0" ?>
<sdf version="1.6">
  <world name="mi_mundo">
    <physics name="1ms" type="ignored">
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1.0</real_time_factor>
    </physics>
    
    <!-- Plugins esenciales de Ignition -->
    <plugin filename="libignition-gazebo-physics-system.so" name="ignition::gazebo::systems::Physics"/>
    <plugin filename="libignition-gazebo-user-commands-system.so" name="ignition::gazebo::systems::UserCommands"/>
    <plugin filename="libignition-gazebo-scene-broadcaster-system.so" name="ignition::gazebo::systems::SceneBroadcaster"/>

    <light type="directional" name="sun">
      <cast_shadows>true</cast_shadows>
      <pose>0 0 10 0 0 0</pose>
    </light>

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

## 4. Comandos de Lanzamiento Rápidos

Para lanzar un mundo vacío en Ignition Gazebo 6:

```bash
ign gazebo empty.sdf
```

Para insertar un modelo URDF en una simulación ya abierta:

```bash
ros2 run ros_gz_sim create -file /ruta/al/robot.urdf -name mi_robot
```

---

## Siguientes Pasos

Continúa con la **[Fase 2: Teleoperación y Bridge](Fase_2_Teleoperacion_y_Bridge.md)** para conectar ROS 2 con la simulación.
