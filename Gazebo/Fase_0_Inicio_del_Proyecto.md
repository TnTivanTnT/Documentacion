# Fase 0: Inicio del Proyecto Gazebo

> 📚 [Volver al Índice de Documentación](../README.md)

Antes de empezar a modelar el robot, debemos crear el "contenedor" donde vivirá todo nuestro código: el paquete de ROS 2.

## 1. Crear el Paquete Base

Ve a la carpeta de código de tu workspace (normalmente `src`) y usa el comando oficial de ROS 2 para crear un paquete de Python:

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python mi_robot_sim
```

---

## 2. Crear la Estructura de Carpetas

El comando anterior crea lo mínimo necesario (`package.xml`, `setup.py`, etc.). Sin embargo, un proyecto de simulación requiere carpetas adicionales para mantener el orden. 

Entra en la carpeta de tu paquete y créalas manualmente:

```bash
cd mi_robot_sim
mkdir description launch worlds meshes config
```

### ¿Para qué sirve cada carpeta?

| Carpeta | Contenido |
| :--- | :--- |
| `description/` | Archivos URDF o XACRO (el diseño físico del robot). |
| `launch/` | Scripts `.launch.py` para arrancar Gazebo y los nodos a la vez. |
| `worlds/` | Escenarios de simulación (archivos `.sdf`). |
| `meshes/` | Modelos 3D complejos exportados de CAD (STL, DAE). |
| `config/` | Archivos de parámetros (YAML) para el bridge o los sensores. |

---

## 3. Consideración Importante: setup.py

Recuerda que en ROS 2 Python, **no basta con crear las carpetas**. Debes decirle a ROS 2 que esas carpetas deben ser "instaladas" cuando compiles el proyecto. 

Esto se hace editando el archivo `setup.py`, como se detalla al inicio de la **Fase 1**.

---

## Siguientes Pasos

Ahora que tienes tu estructura lista, ya puedes empezar con la **[Fase 1: Modelado y Entorno](Fase_1_Modelado_y_Entorno.md)** para diseñar tu primer robot.
