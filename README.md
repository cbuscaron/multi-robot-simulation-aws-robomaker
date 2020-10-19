![Header](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/header.png)

# Simulación de Varios Robots con AWS RoboMaker
[Este taller](https://ccoss.org/sessions/w-ros/) es parte de [CCOSS 2020](http://ccoss.org) usando ROS y Gazebo en AWS RoboMaker

Mucho agradecimiento a [Gabriel Gasca](https://github.com/ggasca-aws) y Cindy Polin por contribuir a la traduccion a español.  

# Parte I: Robots de simulación y prueba con AWS RoboMaker

En este taller, se familiarizará con AWS RoboMaker, un servicio que le permite desarrollar, simular e implementar fácilmente aplicaciones de robots inteligentes que se integran con otros servicios de AWS. Esto incluye los servicios de aprendizaje automático de AWS, los servicios de monitoreo y los servicios de análisis que permiten a un robot transmitir datos, navegar, comunicarse, comprender y aprender.
Hoy, simulará uno y luego varios robots en AWS RoboMaker. En la segunda parte, ejecutará un caso de prueba en simulación utilizando AWS RoboMaker. 
RoboMaker amplía el marco de software de robótica de código abierto más utilizado, Robot Operating System , o [ROS](http://ros.org).
Por lo tanto, este taller incluirá referencias a los conceptos y herramientas de ROS. No se requiere experiencia previa en ROS, sin embargo, si desea obtener más información, consulte [ROS Wiki](https://wiki.ros.org/es).

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
    "same as above / igual arriba"
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
En esta actividad, ejecutaremos un conjunto de pruebas basadas en escenarios en AWS RoboMaker. Los **escenarios** son conjuntos de parámetros que definen las condiciones del mundo real, los comportamientos de los actores y los resultados esperados. Esto permite a los desarrolladores desacoplar el código de la aplicación de simulación de los parámetros que definen las pruebas que se ejecutarán en la simulación. Este desacoplamiento facilita la colaboración con los ingenieros de control de calidad y estandariza la forma en que los equipos ejecutan las pruebas de regresión. También brinda a los ingenieros de control de calidad la flexibilidad para definir fácilmente una variedad de casos de prueba (con diferentes combinaciones de parámetros) que pueden cubrir más completamente los escenarios de prueba deseados. Ejecutaremos estos escenarios automáticamente a través de una simple llamada a la API utilizando el servicio de simulación de AWS RoboMaker.

## Paso 1: Ejecute una prueba de objetivo de navegación aleatoria simple

Para lanzar una prueba basada en escenarios en RoboMaker, usaremos un paquete de prueba que ha sido creado previamente para usted. Esta sencilla prueba establecerá aleatoriamente un objetivo de navegación del robot en la simulación. Si el robot llega al destino, **ha pasado la prueba**. Si el robot no llega al destino dentro de un período de tiempo definido, **ha fallado la prueba**.

![Figure 21](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure21.png)

Si tienes curiosidad acerca del código, la lógica de la prueba se lleva a cabo aquí: **src/robot_fleet_test_launcher/nodes/navigation_test.** Este archivo incluye código Python que **etiquetará automáticamente** los resultados de las pruebas y **terminará automáticamente** el trabajo de simulación cuando las pruebas estén completas.

Aquí están los fragmentos de código en el archivo que realizan estas acciones.

```python
    def cancel_job(self):
        rospy.wait_for_service("/robomaker/job/cancel")
        requestCancel = rospy.ServiceProxy("/robomaker/job/cancel", Cancel)
        response = requestCancel()
        if response.success:
            self.is_cancelled = True
            rospy.loginfo("Successfully requested cancel job")
            self.set_tag(id=self.test_name + "_Time_Elapsed_End", value= str(time.time()).split(".", 1)[0])
        else:
            rospy.logerr("Cancel request failed: %s", response.message)
    
    def set_tag(self, name, value):
        rospy.wait_for_service("/robomaker/job/add_tags")
        requestAddTags = rospy.ServiceProxy("/robomaker/job/add_tags", AddTags)
        tags = ([Tag(key=name, value=value)])
        response = requestAddTags(tags)
        if response.success:
            rospy.loginfo("Successfully added tags: %s", tags)
        else:
            rospy.logerr("Add tags request failed for tags (%s): %s", tags, response.message)
```
El **número de goles** y la **duración esperada** se parametrizarán mediante variables de entorno. De esta manera podemos definir y ejecutar de forma fácil y extensible muchos escenarios diferentes. Primero, configuraremos estos parámetros.

1.	Abra **launch_test_single.json**. En este archivo, especificamos el paquete de prueba ROS descrito anteriormente (**robot_fleet_test_launcher**) y el **run_tests.launch** archivo para iniciar las pruebas basadas en escenarios en la simulación. Las siguientes dos variables de entorno adicionales definirán los parámetros para la prueba:

* **NAVIGATION_SUCCESS_COUNT** - El número de objetivos de navegación aleatorios que se deben lograr antes de completar la prueba.
* **SIM_TIMEOUT_SECONDS** - El tiempo total para completar todos los objetivos de navegación antes de la finalización automática.

2.	Actualice estas variables de entorno con el escenario de prueba que desea ejecutar. Recomendamos que el recuento de éxitos para los objetivos de navegación sea **2** y un tiempo de espera de **1000** segundos para comenzar.

3.	Ejecute el escenario de prueba en AWS RoboMaker.

```shell
cd ~/environment/summit-workshop
./run.sh launch_test_single.json
```

4. Abra **RoboMaker Console** y haga clic en **Trabajos de simulación**. Su trabajo de simulación debe estar en el estado **Preparando** o En **ejecución**. Haga clic y abra su trabajo de simulación. Si se está preparando, espere uno o dos minutos hasta que esté en estado de ejecución.

5.	Abra el cliente **Gazebo** y **RViz** como lo hizo en la sección anterior. Su robot debe moverse activamente hacia un objetivo de navegación.

6.	En la consola de RoboMaker, haga clic en la pestaña **Etiquetas** en RoboMaker. Debería ver una **marca de tiempo de inicio**. Observe esta página mientras su robot se acerca al objetivo de navegación. Cuando termine, verá los resultados de la prueba etiquetados en el trabajo de simulación.

![Figure 22](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/figure22.png)

7.	Para ver los registros de la simulación en ejecución para la depuración, puede conectarse a su simulación en ejecución volviendo al **IDE de RoboMaker Cloud9** haciendo clic en **"Simulation > Connect"**. Los registros deberían aparecer en la terminal. **NOTA: Estos registros son muy detallados.**

### Actividad adicional

Hemos incluido [la extensión ROS de AWS RoboMaker CloudWatch](https://github.com/aws-robotics/cloudwatchlogs-ros1) con esta muestra. Está publicando operaciones robóticas personalizadas y métricas de salud, así como registros personalizados que informan cosas como la distancia de obstáculo más cercana. Para verlos cuando se está ejecutando su simulación:

1.	Abra **AWS CloudWatch Logs** en la consola de AWS.
2.	Haga clic en **Metrics**. Abra **AWSRoboMakerSimulation** 
3.	Aquí verá muchas métricas. Utilice el filtro en la parte superior para buscar por su ID de trabajo de simulación o tipo de métricas.


## Paso 2: Ejecutar una prueba de navegación simple con varios robots

A continuación, lanzaremos una prueba de navegación simple con varios robots usando la misma arquitectura como se describe arriba. Puede agregar robots adicionales agregando a la matriz de robots en los archivos de inicio JSON.

1.	Para configurar los parámetros para nuestra prueba, inicie la prueba en la simulación de RoboMaker, abra **launch_tests_two_robots.json**. Puede actualizar nuevamente los parámetros para modificar el escenario de prueba para cada robot.

```shell
 cd ~/environment/summit-workshop
./run.sh launch_tests_two_robots.json
```
2.	Siga los mismos pasos que el anterior para observar a los dos robots mientras completan las pruebas. Tenga en cuenta las fallas de prueba, cuando los dos robots se encuentran en el camino del otro o no pueden maniobrar con éxito alrededor de un objeto. Una vez completada, asegúrese de cancelar todas las simulaciones.

3.	A continuación, intente con dos robots más o con diferentes parámetros de variables de entorno para obtener un número de navegaciones exitosas. Nuevamente, para realizar estos cambios, actualice **launch_tests_two_robots.json** y luego inicie las simulaciones nuevamente. Para ejecutar las pruebas con cuatro robots, pruebe esta configuración:

```shell
cd ~/environment/summit-workshop
./summit-workshop launch_tests_four_robots.json
```

4.	En la **consola de RoboMaker**, abra **Simulation Job Batches**. Aquí, verá los tres trabajos adicionales que se ejecutan además del servidor.

**¡Felicidades! Ha completado con éxito el taller. Si está interesado en continuar, aquí hay algunas actividades adicionales.**

### Actividades opcionales

¿Todo hecho y aún te queda algo de tiempo?

Aquí hay un par de actividades adicionales interesantes para probar. A estas alturas, debe saber cómo ejecutar una prueba de navegación simple y comprender cómo está estructurado el código.

Antes de comenzar con las actividades adicionales a continuación, tome nota del script de compilación incluido. Esto facilitará un poco la ejecución de los comandos colcon build y bundle. Deberá ejecutar estos comandos antes de ejecutar una nueva simulación si realiza algún cambio en el código. Para usar ese script, simplemente ejecute:

```shell
cd ~/environment/summit-workshop
./build.sh
```
Aquí hay un par de actividades interesantes que podría hacer a continuación:

### Actividad adicional: lanza los robots en un mundo de simulación diferente

Hay muchos mundos de gazebo de código abierto disponibles para la comunidad para ejecutar sus pruebas de robot. Dos interesantes para hoy son [AWS RoboMaker Hospital World](https://github.com/aws-robotics/aws-robomaker-hospital-world) y [AWS RoboMaker Bookstore World](https://github.com/aws-robotics/aws-robomaker-bookstore-world).
* Clone el mundo que desea usar desde GitHub en el directorio **summit-workshop/simulation_ws/src/deps**.

* Luego, actualice el archivo **robot_fleet_rosbridge.launch** en **Summit-Workshop/simulation_ws/src/robot_fleet** para usar el mundo que acaba de clonar. Busque el bloque de código con el comentario **<!-- Launch the world -->** y reemplace la referencia al mundo del pequeño almacén con el mundo que clonó.

* Vuelva a ejecutar la primera parte de este taller para crear, agrupar y lanzar una nueva simulación con sus cambios.


### Actividad adicional: agregue sus propios puntos de referencia a la lista de objetivos de navegación definidos con el mundo de los almacenes pequeños

Para hacer esto, puede agregar algunos puntos de ruta personalizados a los puntos de ruta definidos en el archivo **routes.yaml**.

* Cree una nueva simulación de un solo robot y abra Gazebo y RViz haciendo clic en "Conectar" en **gzclient** y la aplicación de simulación.
* Mueva el robot estableciendo nuevos objetivos de navegación 2D. Observe en la parte inferior de la pantalla en RViz las coordenadas de posición.
* Seleccione algunos que sean interesantes para probar varios patrones de navegación en el almacén.
* Actualice el siguiente archivo con las nuevas posiciones: **summit-workshop/simulation_ws/src/deps/aws-robomaker-small-warehouse-world/route/routes.yaml**.
* Vuelva a ejecutar la segunda parte de este taller con las nuevas rutas.
* Bonificación adicional: crea un conjunto de rutas con un nuevo archivo **route.yaml** para un mundo diferente después de completar la actividad de bonificación anterior. Actualice el puntero al archivo **route.yaml** en la línea 20 de **summit-workshop/simulation_ws/src/robot_fleet_test_launcher/launch/run_tests.launch** para apuntar al nuevo archivo que creó.


### Limpiar

1.	Cancele cualquier trabajo de simulación en ejecución desde la página de la consola de simulación de RoboMaker.

2.	Vacíe el balde S3. En la consola de AWS, vaya a **Services>S3**. Debería tener un depósito con **multibotrosbridge-robomakerbasics3bucket-xxxxxxx**. Haga clic en el botón de radio junto al depósito S3 y elija **"Empty"** en el menú superior. Se le pedirá que proporcione el nombre del depósito como confirmación de que desea vaciarlo. Copie y pegue el nombre del depósito en el campo y haga clic en **"Empty"**. Este paso eliminará los paquetes y los archivos de registro que se crearon en el depósito de S3.

3.	Vaya a la consola de **CloudFormation** haciendo clic en **Services>CloudFormation**. Haga clic en el botón de radio junto al stack que se llama **"multibotrosbridge"**. Luego, de las opciones, elija **"Delete"**. Este paso eliminará los recursos de AWS que se crearon como parte del script que ejecutó en el entorno de desarrollo de RoboMaker, incluido el bucket de S3 que vació en el bucket anterior.

4.	Cierre la pestaña IDE del entorno de desarrollo de RoboMaker. Busque la consola de RoboMaker. Haga clic en **"Development Environments"**. Haga clic en el entorno de desarrollo que creó en este taller. En la página siguiente, haga clic en **"Edit"**. Esto lo llevará a otra página; haga clic en **"Delete"** en la parte superior derecha de la página. Se le pedirá que escriba eliminar para confirmar que desea eliminarlo. Escriba Delete y luego haga clic en **"Delete"**.


## Proximos pasos
El taller de hoy se basó en nuestra aplicación de muestra disponible públicamente que puede clonar aquí: 
https://github.com/aws-samples/multi-robot-fleet-sample-application

Después del laboratorio de hoy, ahora comprenderá cómo iniciar simulaciones en RoboMaker, así como cómo utilizar colcon build y bundle para preparar sus aplicaciones para RoboMaker. Solo hay un paso más: ¡clone el repositorio anterior e intercambie su aplicación de robot!

¡Feliz Desarollo!

## Problemas conocidos
Este paquete no realiza la sincronización de la hora, y la actualización de la simulación se sincroniza entre las instancias de gazebo. Los mejores resultados se han visto con pilas de software homogéneas que se ejecutan en las instancias de gazebo.

![Header](https://github.com/cbuscaron/multi-robot-simulation-aws-robomaker/blob/main/images/footer.png)
