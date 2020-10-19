![Header](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/header.png)

# Simulación de Varios Robots con AWS RoboMaker
Este taller es parte de [CCOSS](http://ccoss.org) 2020 usando ROS y Gazebo en AWS RoboMaker

# Robots de simulación y prueba con AWS RoboMaker

En este taller, se familiarizará con AWS RoboMaker, un servicio que le permite desarrollar, simular e implementar fácilmente aplicaciones de robots inteligentes que se integran con otros servicios de AWS. Esto incluye los servicios de aprendizaje automático de AWS, los servicios de monitoreo y los servicios de análisis que permiten a un robot transmitir datos, navegar, comunicarse, comprender y aprender.
Hoy, simulará uno y luego varios robots en AWS RoboMaker. En la segunda parte, ejecutará un caso de prueba en simulación utilizando AWS RoboMaker. 
RoboMaker amplía el marco de software de robótica de código abierto más utilizado, Robot Operating System , o [ROS](http://ros.org).
Por lo tanto, este taller incluirá referencias a los conceptos y herramientas de ROS. No se requiere experiencia previa en ROS, sin embargo, si desea obtener más información, consulte [ROS Wiki](https://wiki.ros.org/es) .

¿Estás emocionado de construir un robot con AWS? ¡Empecemos!
**Nota importante: una vez que haya completado todas las actividades, complete la sección de limpieza de la cuenta en la parte inferior de esta página.**


## Configuración del taller
Antes de comenzar a aprender sobre el desarrollo de robots, debemos configurar algunas cosas. Este taller se puede completar de dos formas: (1) en un evento público donde AWS proporciona cuentas temporales de AWS; o (2) por su cuenta, donde usa su propia cuenta de AWS.

Para ejecutar el taller de hoy, necesitará acceso a una cuenta de AWS. Debería haber **recibido un código hash temporal** que se puede utilizar para abrir una cuenta de AWS ya aprovisionada para el taller de hoy.
1.	Los organizadores del evento le enviarán el código hash a través del chat del seminario web. Copie este código hash e introdúzcalo en el siguiente sitio web: https://dashboard.eventengine.run
![Figure 1](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure1.png)

2.	En la siguiente pantalla, haga clic en el botón  **AWS Console**.
![Figure 2](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure2.png)

3.	Luego, haga clic en el botón **Open Console** en la ventana emergente.
![Figure 3](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure3.png)

4. Continúe con el **Paso 1: Configure su entorno**.

## Paso 1: Configura tu entorno

1.	Abra una nueva pestaña en la consola de AWS RoboMaker (Services->RoboMaker->right-click->new tab)

2.	Cree un entorno de desarrollo (Development->Development environments->Create environment) y complete lo siguiente:
* **Name:** taller o algo descriptivo
* **Pre-installed ROS distribution:** elija ROS Melodic
* **Instance type:** m5.large

3.	Elija la **VPC (default)** y una de las subredes (us-west-2a, us-west-2b o us-west-2c) para la sección Networking 

4.	Luego haga clic en **Create**

Esto abre la página de detalles del entorno, haga clic en **Open environment**, que abrirá una nueva pestaña del navegador con el IDE de Cloud9. Esto puede tardar unos minutos en completarse, pero cuando se haya completado el proceso de creación, verá algo similar a esto:
![Figure 4](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure4.png)

La **página de bienvenida** proporciona información útil para comenzar, pero por ahora no la usaremos, así que haga clic en la X en la pestaña para cerrar. El IDE se divide en cuatro secciones:
![Figure 5](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure5.png)

* (1) El menú de AWS RoboMaker proporciona acceso rápido a acciones comunes. Se actualiza cuando **roboMakerSettings.json** se modifica el archivo. No haremos ninguna modificación a este archivo en este taller.

* (2) Todos los archivos y carpetas residirán aquí, y se pueden seleccionar y hacer doble clic para abrir en el panel del editor (# 4).

* (3) La sección inferior es un panel ajustable para crear o monitorear operaciones de línea de comando. Los desarrolladores de ROS trabajan en esta área para construir, probar e interactuar con el código local.

* (4) Este es el panel principal del editor.

**¡Felicitaciones, ha creado su entorno de desarrollo RoboMaker!**


## Paso 2: Ejecute un solo robot en la simulación de RoboMaker

## Paso 3: Lanzar varios robots en una simulación de flota

# Parte II: Lanzamiento de pruebas basadas en escenarios con AWS RoboMaker

## Paso 1: Ejecute una prueba de objetivo de navegación aleatoria simple

## Paso 2: ejecutar una prueba de navegación simple con varios robots

### Actividades opcionales

### Limpiar

![Header](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/footer.png)
