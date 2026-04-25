# Fase 5: Navegación Autónoma con Nav2

> 📚 [Volver al Índice de Documentación](../README.md)

## ¿Qué es Nav2?

El stack de navegación **Navigation 2 (Nav2)** es el cerebro que permite al robot moverse de A a B de forma segura. Se encarga de:
-   **Planificación Global:** Encontrar la ruta más corta en el mapa.
-   **Planificación Local:** Esquivar obstáculos dinámicos (ej: una persona cruzándose) en tiempo real.
-   **Recuperación:** Realizar maniobras (como girar sobre sí mismo) si el robot se queda atrapado.

---

## 1. Instalación de Nav2

```bash
sudo apt update
sudo apt install ros-humble-navigation2 ros-humble-nav2-bringup
```

---

## 2. Lanzar la Navegación

Necesitamos tres elementos corriendo simultáneamente:
1.  **Gazebo:** Con el robot y el bridge.
2.  **Map Server:** Para cargar el mapa que guardamos en la fase anterior.
3.  **Nav2 Stack:** Para procesar la localización y planificación.

### Comando de lanzamiento simplificado

```bash
ros2 launch nav2_bringup bringup_launch.py \
    use_sim_time:=True \
    map:=/ruta/a/tu/mi_mapa.yaml
```

---

## 3. Localización Inicial (AMCL)

Nav2 usa un algoritmo llamado AMCL (Adaptive Monte Carlo Localization). 
1.  En RViz2, verás que el robot no coincide exactamente con su posición en el mapa.
2.  Usa la herramienta **"2D Pose Estimate"** en la barra superior de RViz.
3.  Haz clic y arrastra sobre el mapa en el lugar y dirección exactos donde se encuentra el robot en Gazebo.

---

## 4. Navegación Autónoma: ¡El gran momento!

1.  En RViz2, selecciona la herramienta **"Nav2 Goal"** (o "2D Nav Goal").
2.  Haz clic en cualquier punto libre del mapa.
3.  **Observa:** El robot en Gazebo empezará a moverse solo hacia el objetivo, calculando la ruta y evitando cualquier obstáculo nuevo que pongas en su camino.

---

## Resumen del Proyecto

¡Felicidades! Has completado el ciclo completo de desarrollo robótico en simulación:
1.  **Modelado:** Creamos el robot diferencial.
2.  **Comunicación:** Conectamos ROS 2 con Gazebo (Bridge).
3.  **Teleoperación:** Movimos el robot manualmente.
4.  **Percepción:** Configuramos el LiDAR.
5.  **Mapeo:** Creamos un mapa digital del entorno (SLAM).
6.  **Autonomía:** El robot ahora navega solo (Nav2).

---

## Recursos Adicionales

-   [Documentación oficial de Nav2](https://navigation.ros.org/)
-   [Repositorio oficial de ros_gz](https://github.com/gazebosim/ros_gz)
