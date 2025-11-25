# P4 AMAZON WAREHOUSE

## Índice
1. Descripción General
2. Objetivos de la Práctica
3. Componentes Principales
4. Flujo de Ejecución
5. Problemas Encontrados
6. Video de la ejecucion

---

## Descripción General

En esta práctica se desarrolla un sistema de navegación autónoma, manipulación y transporte de estanterías dentro de un entorno simulado estilo Amazon Warehouse.
El robot debe:
  - Planificar rutas libres de colisiones mediante OMPL.
  - Navegar hasta la estantería, levantarla, transportarla a una posición inicial y volver a depositarla.
  - Repetir este proceso para 3 de las estanterías disponibles en el escenario.
    
El sistema integra procesamiento de mapas, transformaciones de coordenadas, detección de colisiones, path planning, control PID y modificación dinámica del mapa según se levantan o colocan estanterías.

---

## Objetivos de la Práctica

1. Calcular rutas óptimas hacia una estantería utilizando OMPL y validación de estados basada en colisiones por píxeles, tanto para un robot holonómico como para un robot com geometria ackerman.  
2. Implementar un sistema de recogida y colocación de estanterías, modificando dinámicamente el mapa.

---

## Componentes Principales

### 1. Conversión de Coordenadas  
Se establecen transformaciones entre los sistemas de coordenadas del **simulador (Gazebo)** y del **mapa (imagen)**, permitiendo sincronizar la posición real del robot con la representación visual.

### 2. Procesamiento del Mapa  
El mapa base se carga desde una imagen y se convierte a escala de grises para su posterior análisis. Se aplica una operación de **dilatación o erosión** para generar un mapa navegable que considera el tamaño del robot y su margen de seguridad.

### 3. Planificación de Movimiento
Utilizamos OMPL con un planificador RRT* para optimizar rutas.
En el caso de la geometria Holonomica, utilizamos un estado de espacios SE2.
  - Validamos mirando en el mapa calculando la geometria del robot en pixeles.
En el caso de la geometria ackerman utilizamos un espacio de estados de Reeds-Shepp que este permite al robot avanzar y retroceder teniendo radios de giro.
  - En este caso tambien validamos mirando en el mapa.
    
### 4. Actualizacion del mapa
Cuando cogemos una estanteria, al cogerla y desplazarla ya no se encuentra como obstaculo por lo que hay que eliminarla y despues cuando la deje en el suelo tengo que modificar el mapa para que aparezca como obstaculo. 

## 4 Flujo de Ejecución

1. Carga y procesar del mapa.  
2. Transformar coordenadas y preparar planificadores.
3. Para cada una de las 3 estanterias.
     - Planificar una ruta hacia ella.
     - Navegar con PID siguiendo los puntos del path.
     - Alinear el robot y levantar la estantería.
     - Cortar la estantería del mapa.
     - Planificar ruta hacia la zona de depósito.
     - Seguir la ruta.
     - Alinear y depositar la estantería.
     - Volver a añadir la estantería al mapa.
---

## 5 Problemas Encontrados

Durante el desarrollo y ejecución de la práctica se han presentado los siguientes inconvenientes:

- **Errores en la transformación (Gazebo, Mapa)**, Pequeñas imprecisiones en los puntos de referencia provocaban desalineaciones.
     - **Solución**: coger mas puntos en el mapa con sus correspondientes puntos en el mundo.
- **Validación incorrecta de colisiones**, Si la ventana en la que se recortaba la estantería no coincida bien, el validador detectaba colisión donde no la había.
     - **Solución**: Aumentar ligeramente el tamaño del polígono.
- **Rutas imposibles o subóptimas**,Al principio usaba RRTconnect pero este se basa en encontrar una solucion aunque sea poco optima, RRT* encuentra rutas bastante optimas, pero a veces muy ajustadas por lo que debemos añadir algunos pixel como seguridad.
- **Poca friccion con la estanteria**, Al levantar la estanteria, El robot ademas de tener otra geometria esta no esta anclada a el robot por lo que hay posibilidad de que se caiga o se de la vuelta, ademas en la simulacion la friccion funciona mal.
---

## 6 Video de la ejecución

