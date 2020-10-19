![Header](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/header.png)

# Simulación de Varios Robots con AWS RoboMaker
Este taller es parte de [CCOSS](http://ccoss.org) 2020 usando ROS y Gazebo en AWS RoboMaker
Muchas gracias @ggasca-aws por la traduccion 

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
En esta actividad, descargará un código de muestra en el entorno de desarrollo y creará, agrupará y simulará una aplicación con un solo robot.

1.	En la sección de **terminal** de su entorno de desarrollo, ejecute el siguiente comando para descargar el código base de muestra (tomará aproximadamente 2-3 minutos), luego extraiga los archivos y ejecute el script de configuración este **paso tomará aproximadamente 10 minutos.** Lo que hace ahora un ¡Buen momento para tomar un café!.

```shell
sudo /usr/bin/python3 -m pip install --upgrade pip 
sudo pip install --upgrade awscli
cd ~/environment
aws s3 cp s3://assets-us-west-2.robomakerworkshops.com/workshop.tar.gz ./
tar xvzf workshop.tar.gz && summit-workshop/setup.sh 
```
Al ejecutar el archivo **setup.sh**, se realizan los siguientes pasos:
* Instala dependencias ROS con un parche menor para el mundo Gazebo que usaremos hoy (un mundo de almacén pequeño).

* Ejecuta las operaciones iniciales de compilación de colcon y paquete de colcon (**NOTA: si realiza cambios en el código, deberá ejecutar estos comandos nuevamente**).

* Ejecuta el stack de cloudformation en la nube para configurar los recursos de AWS (roles de IAM, VPC, etc.)

* **Nota: El paquete robot_fleet ROS devolverá una ADVERTENCIA al ejecutar colcon build la primera vez que dice: "Boost_MISSING_DEPENDENCIES". Esto es lo esperado, ignore este mensaje.**

2.	Abra el archivo **~/environment/summit-workshop/launch_single.json** e inspecciónelo. Debe tener un aspecto como este:

```json
{
    "robots": [],
    "server": {
        "name": "SERVER",
        "environmentVariables": {
            "START_X": "0",
            "START_Y": "0",
            "START_YAW": "0",
            "HUSKY_REALSENSE_ENABLED": "true",
            "HUSKY_LMS1XX_ENABLED": "true",
            "USE_CUSTOM_MOVE_OBJECT_GAZEBO_PLUGIN":"true",
            "ROS_AWS_REGION": "us-west-2"
        },
        "packageName": "robot_fleet",
        "launchFile": "robot_fleet_rosbridge.launch"
    }
}
```
Aquí, establecemos múltiples variables de entorno que determinarán las condiciones iniciales de arranque del robot. Una vez que haya terminado de ver este archivo, usémoslo para lanzar nuestras simulaciones. **Nota importante: si está usando una región diferente a us-west-2, tómese un momento para actualizar la variable de entorno ROS_AWS_REGION a la región que está usando actualmente.**

3.	Ejecute el siguiente comando para iniciar la simulación:

```shell
cd ~/environment/summit-workshop
./run.sh launch_single.json
```
Este comando:
* Ejecute la llamada a la API de AWS RoboMaker para iniciar una simulación.
* Elige los parámetros correspondientes para las simulaciones de **launch_single.json.**

4.  Después de ejecutar el comando anterior, vaya a su **consola RoboMaker** (manteniendo abierto el IDE de Cloud9 para su uso posterior).

![Figure 7](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure7.png)

5.  En el menú de la izquierda, haga clic en **Simulation jobs**. Verá un trabajo de simulación que debería estar en estado **Pending** o En **Running**. Si todavía está en estado pendiente, espere uno o dos minutos hasta que se esté ejecutando.

![Figure 8](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure8.png)

6.	Una vez que el trabajo de simulación esté en estado de **Running**, haga clic en el trabajo de simulación. Desplácese hacia abajo hasta **"Simulation application tools"**. Aquí verá cinco herramientas. Puede hacer clic en **"Connect"** para que cada uno utilice las aplicaciones en su simulación.

RoboMaker le proporciona cuatro herramientas predeterminadas para interactuar con su aplicación; GZClient (o Gazebo Client), RQT, RViz y una Terminal. También le permite conectarse a herramientas GUI personalizadas que inicia desde su simulación. Esto se mostrará como "Aplicación de simulación" y / o "Aplicación de robot".

![Figure 9](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure9.png)

En los siguientes pasos, abriremos cada una de estas herramientas e interactuaremos con la simulación en ejecución.

### GZClient
El cliente Gazebo o GZClient ofrece la capacidad de simular robots de manera precisa y eficiente en entornos complejos de interior y exterior. Cuando se conecte a GZClient, verá su robot en el mundo de los almacenes pequeños:

![Figure 10](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure10.png)

Usando su mouse o trackpad, haga clic en la ventana principal. Consulte [esta página](http://gazebosim.org/tutorials?tut=guided_b2&cat=) para obtener más detalles y cómo navegar (cerca de la parte inferior de la página).

### Terminal
En la terminal puede ejecutar algunos comandos ROS y ver las salidas relacionadas con el trabajo de simulación que se está ejecutando.

![Figure 11](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure11.png)

Algunos de los comandos que puede ejecutar son:
* **rosnode list**
* **rostopic list**
* **rostopic echo realsense/color/camera_info**

### RQT
rqt es un marco de panel extensible que implementa las diversas herramientas GUI y paneles de control en forma de complementos para sus aplicaciones de robot.

Cuando se conecte a la aplicación RQT, verá una página en blanco:

![Figure 12](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure12.png)

1.	Haga clic en **Plugins > Introspection > Node Graph**. Bajo el encabezado **"Node Graph"**, elija **"Nodes/Topics (all)"**. Ahora debería ver un gráfico de todos los nodos y temas.
2.	Luego haga clic en **Plugins > Robot Tools > Robot Steering**. Intenta conducir el robot usando los controles

### RVIZ
Rviz es una herramienta de visualización 3D para ROS. Proporciona información sobre el estado del robot y el mundo que lo rodea (virtual o real). Puede utilizar esta herramienta para ver las salidas de los sensores (como las señales de la cámara y la ubicación actual del robot en el mapa), analizar sus modelos URDF, establecer configuraciones de robot personalizadas y más. Para visualizar los sensores y el mapa de su robot, primero debemos señalar los componentes de nuestro robot.

Cuando abra RVIZ, verá una ventana como la siguiente:

![Figure 13](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure13.png)

Aquí hay algunas actividades para hacer en RViz para tener una idea de cómo usar el software:

1.	Agregue el modelo de robot a la pantalla:
* En la parte inferior izquierda de la ventana de visualización, haga clic en el botón **'Add'**
* En la pestaña **‘by display type'** de la ventana emergente, seleccione el tipo **'RobotModel'** y luego **'OK'**
* Tu robot debería aparecer ahora en la cuadrícula

2.	Agregue el mapa a la pantalla:
* En la parte inferior izquierda de la ventana de visualización, haga clic en el botón **'Add'**
* En la pestaña **'by display topic'** de la ventana emergente, seleccione el tema **'map'** y luego **'OK'**
* Ahora debería aparecer un mapa alrededor del robot

3.	Agrega la **"goal pose"** a la pantalla:
* En la parte inferior izquierda de la ventana de visualización, haga clic en el botón **'Add'**
* En la pestaña **'by display topic'** de la ventana emergente, seleccione el tópico **/move_base_simple/goal/pose**, luego **'OK'**
* Esto no mostrará nada hasta que establezca una meta

4.	Agregue el "global plan" a la pantalla:
* En la parte inferior de la ventana de visualización, haga clic en el botón **'Add'**
* En la pestaña **‘by display topic’** de la ventana emergente, seleccione **/move_base/DWAPlannerROS/global_plan** luego **'Path'**, luego **'OK'**
* Esto no mostrará nada hasta que establezca una meta

5.	Use el botón **'Objetivo de navegación 2D'** de la barra de menú superior en RViz para establecer un objetivo para que el robot se mueva en un espacio abierto.
* Ahora debería ver aparecer una flecha roja en la meta, luego un camino en verde
* El robot debe comenzar a moverse hacia la meta.

6.	Intente agregar otro tópico a la vista: **/scan**

7.	Intente enviar algunos objetivos más al robot


### Aplicación de simulación
Muchos desarrolladores de ROS lanzarán automáticamente herramientas de GUI (como RViz arriba) desde archivos **roslaunch**. Esto es particularmente conveniente para RViz, ya que podría precargar automáticamente un archivo de configuración RViz predeterminado con todas las configuraciones de sus sensores para su simulación.

Hemos creado un archivo de configuración RViz predeterminado como parte de la aplicación de muestra en este taller. Si está interesado, puede navegar para ver el archivo de configuración en: **~/environment/summit-workshop/simulation_ws/src/robot_fleet/rviz/basic_data.rviz**

Cuando se conecte a la **aplicación de simulación**, verá algo similar a la siguiente imagen:

![Figure 14](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure14.png)

Puede abrirlo seleccionando la opción **File > Open config**. 

elegir la ruta:**/home/robomaker/workspace/applications/simulation-application/opt/built_workspace/robot_fleet/sharee/robot_fleet/rviz**

Elegir el archivo: **basic_data.rviz**

![Figure 15](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure15.png)

### Finalizar la simulación
Este es el paso final de la primera sección de simulación. Cierre todas las pestañas que están abiertas para cada una de las aplicaciones que se cubrieron anteriormente. Para hacer esto:

1.	Busque el trabajo de simulación en la consola de RoboMaker desde la misma página en la que estaba accediendo a las aplicaciones.
2.	En la parte superior, debajo de **"Actions"**, haga clic en **"Cancel"** . Esto cancelará el trabajo de simulación. Es importante cancelar los trabajos de simulación cuando haya terminado.

**¡Felicidades! Ahora sabe cómo iniciar simulaciones y cómo utilizar las herramientas de simulación ROS proporcionadas**

## Paso 3: Lanzar varios robots en una simulación de flota
En la siguiente actividad, lanzará varios trabajos de simulación de RoboMaker para simular una flota de robots. Aprovecha la siguiente arquitectura:

![Figure 16](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure16.png)

En esta arquitectura, una única simulación a nivel de flota actuará como servidor utilizando un paquete llamado **ROS Bridge**. ROS Bridge permite a los desarrolladores pasar mensajes fácilmente entre dos o más aplicaciones ROS distintas. Cada uno de los robots se ejecutará en su propio trabajo de simulación RoboMaker, independiente (y escalable horizontalmente) y luego se comunicará con el servidor mediante ROS Bridge.

Usaremos la API de RoboMaker Batch para lanzar la "flota" de robots.

Aquí está la arquitectura de la aplicación:

![Figure 17](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure17.png)

Para ver este paquete ROS con más profundidad, puede abrirlo aquí: 
**~/environment/summit-workshop/simulation_ws/src/robot_fleet**

1.	Utilice las siguientes dos configuraciones JSON de muestra como planos. Agregue varios robots a una nueva configuración JSON agregando a la matriz de robots. **Nota: asegúrese de agregar la variable de entorno adicional: "ROS_AWS_REGION": "us-west-2" para establecer la región que está utilizando.**

* launch_two_robots.json - Ejecuta el servidor ROS y un robot adicional.
* launch_four_robots.json - Ejecuta el servidor ROS y otros tres robots.

```json
{
    "robots": [
        {
            "name": "robot1",
            "environmentVariables": {
                "START_X": "2",
                "START_Y": "2",
                "START_YAW": "3.143",
                "HUSKY_REALSENSE_ENABLED": "true",
                "HUSKY_LMS1XX_ENABLED": "true",
                "USE_CUSTOM_MOVE_OBJECT_GAZEBO_PLUGIN":"true",
                "ROS_AWS_REGION":"us-west-2"
            },
            "packageName": "robot_fleet",
            "launchFile": "robot_fleet_rosbridge.launch"
        }
    ],
    "server": {
    ... Same as above
}
```

2.	Inicie las simulaciones con el script shell **run.sh**, utilizando el archivo de configuración que creó. (**Nota: Este lanzamiento demora un poco más, ya que está esperando que el servidor se inicie. Debería tomar de 3 a 4 minutos para que se inicien todas las simulaciones**) Aquí hay un comando de ejemplo para ejecutar las simulaciones:

```shell
cd ~/environment/summit-workshop
./run.sh launch_four_robots.json
```
3.	Una vez en ejecución, abra la consola de **RoboMaker** y haga clic en **Simulation job batches**.

![Figure 18](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure18.png)

4.	Haga clic en el lote de trabajo que acaba de comenzar. Si había especificado cuatro robots, debería ver tres robots en ejecución, uno es el servidor que se ejecuta fuera del lote.

![Figure 19](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure19.png)

5.	Haga clic en uno de los trabajos de simulación en el lote, luego abra Gazebo y RViz como lo hizo en el ejercicio anterior. Envía un objetivo de navegación. Verá que un solo robot se mueve en la flota. A continuación, abra un trabajo de simulación diferente y repita estos pasos.

![Figure 20](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure20.png)

**¡Felicitaciones, ahora sabe cómo lanzar varios robots en una simulación de flota!**
**Nota importante: recuerde cancelar las simulaciones como lo hizo anteriormente, antes de invocar run.sh nuevamente.**

# Parte II: Lanzamiento de pruebas basadas en escenarios con AWS RoboMaker

## Paso 1: Ejecute una prueba de objetivo de navegación aleatoria simple

## Paso 2: ejecutar una prueba de navegación simple con varios robots

### Actividades opcionales

### Limpiar

![Header](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/footer.png)
