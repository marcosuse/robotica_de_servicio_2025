# P2 RESCUE DRONE

## Índice
1. Descripción General  
2. Objetivos de la Práctica  
3. Componentes Principales  
4. Flujo de Ejecución  
5. Problemas Encontrados  
6. Video de la ejecución  

---

## 1. Descripción General

Esta práctica implementa un sistema de **búsqueda y rescate autónomo** utilizando un dron en un entorno simulado.  
El dron despega, se desplaza hacia una zona de búsqueda predefinida, y realiza un **patrón de exploración en espiral** para detectar personas en el terreno mediante **visión artificial y detección facial**.  

Además, el sistema incorpora una **gestión inteligente de batería**, que obliga al dron a **retornar automáticamente a la base** cuando el nivel de energía es bajo, realizar un proceso de **recarga simulada** y **reanudar la misión** desde el punto en el que se detuvo.  

---

## 2. Objetivos de la Práctica

1. Implementar un **autómata de estados** que controle el comportamiento del dron durante toda la misión.  
2. Realizar **detección facial robusta** utilizando la librería OpenCV y un **clasificador Haar Cascade**.  
3. Ejecutar un patrón de búsqueda en **espiral**, garantizando la cobertura total del área.  
4. Integrar la **gestión de batería**, permitiendo retorno, aterrizaje y recarga automática.  
5. Transformar coordenadas de **imagen a coordenadas del mundo (Gazebo)** para registrar las posiciones donde se detectaron personas.  
6. Visualizar en tiempo real las imágenes frontales y ventrales, así como las detecciones realizadas.  

---

## 3. Componentes Principales

### 1. Máquina de Estados Finita (FSM)
El comportamiento del dron se organiza en una **máquina de estados** con las siguientes fases:  

- **TAKEOFF:** despegue hasta una altura fija.  
- **GO_TO_POSITION:** vuelo hacia la zona objetivo.  
- **SEARCH:** exploración en espiral buscando rostros humanos.  
- **RETURN_2_BOAT:** regreso a la base cuando se detectan suficientes personas o la batería está baja.  
- **LAND:** aterrizaje controlado.  
- **RECHARGING:** recarga de batería antes de retomar la misión.  

### 2. Detección Facial  
Se utiliza un clasificador **Haar Cascade** (`haarcascade_frontalface_default.xml`) para identificar rostros en las imágenes captadas por la cámara ventral.  
El sistema aplica una **rotación progresiva de la imagen** (de 0° a 360° en pasos de 15°) para garantizar detección robusta independientemente de la orientación del rostro.

### 3. Eliminación de Color Azul  
Antes de procesar la imagen, se elimina el color azul para evitar falsos positivos causados por el color del mar.  
Esto se logra aplicando un **filtro HSV** y una **máscara binaria inversa** sobre las zonas azules de la imagen.  

### 4. Exploración en Espiral  
El movimiento de búsqueda se basa en el algoritmo `do_spiral`, que calcula la siguiente posición a explorar a partir del contador de pasos, la distancia entre puntos y la dirección actual.  
Este patrón asegura que el dron **cubra todo el terreno de forma ordenada**, evitando solapamientos.

### 5. Transformación de Coordenadas  
Cuando se detectan rostros en la imagen, sus coordenadas de píxeles se transforman a **coordenadas del mundo (Gazebo)** mediante relaciones métricas y trigonometría.  
Esto permite estimar la **posición real de las víctimas** detectadas.  

### 6. Gestión de Batería  
El sistema simula una batería que se descarga progresivamente durante el vuelo.  
Si el nivel baja del 30%, el dron **interrumpe su misión** y retorna automáticamente a la base para **recargar** antes de continuar.  

### 7. Visualización  
- La **imagen frontal** del dron se muestra en el panel principal de la interfaz.  
- La **imagen ventral sin zonas azules** se muestra en el panel lateral.  
- Los rostros detectados se marcan con **elipses de color magenta**.  

---

## 4. Flujo de Ejecución

1. **Inicialización:** se cargan las constantes, los límites de color azul y el clasificador facial.  
2. **Despegue (TAKEOFF):** el dron asciende hasta la altura de búsqueda.  
3. **Navegación inicial (GO_TO_POSITION):** se dirige hacia la zona de rescate definida por coordenadas UTM.  
4. **Exploración (SEARCH):**  
   - Realiza el movimiento en espiral.  
   - Captura imágenes ventrales.  
   - Elimina el color azul.  
   - Detecta rostros y guarda sus coordenadas transformadas.  
   - Si se detectan 6 o más personas, pasa al estado de retorno.  
5. **Retorno a la base (RETURN_2_BOAT):** vuelve a la posición inicial cuando completa la misión o la batería está baja.  
6. **Aterrizaje (LAND):** se posa sobre la base de recarga.  
7. **Recarga (RECHARGING):** simula la carga hasta alcanzar el 100%. Si estaba en medio de una misión, la reanuda automáticamente.  

---

## 5. Problemas Encontrados

Durante el desarrollo de la práctica se identificaron varios desafíos:

- **Falsos positivos en la detección facial**, especialmente con reflejos o sombras en el agua.  
  - **Solución:** aplicar rotaciones y eliminar el color azul antes de detectar.  

- **Inestabilidad del patrón espiral**, que a veces provocaba saltos irregulares o repeticiones.  
  - **Solución:** ajustar los parámetros de distancia (`DIST_SPIRAL`) y control de yaw.  

- **Desincronización en las coordenadas Gazebo ↔ Imagen**, causando pequeñas desviaciones en las posiciones detectadas.  
  - **Solución:** calibrar la relación metros/píxeles (`LEN_HOR_CAM`, `LEN_VERT_CAM`).  

- **Problemas con la recarga simulada**, ya que la transición de estados debía conservar la posición anterior.  
  - **Solución:** guardar el estado previo (`state_battery_low`) antes de cambiar a recarga.  

- **Rendimiento del procesamiento visual**, debido a la detección en múltiples rotaciones.  
  - **Solución:** reducir el número de rotaciones o procesar solo cada ciertos ciclos del bucle principal.  

---

## 6. Video de la ejecución

[![Ver video](https://github.com/marcosuse/robotica_de_servicio_2025/blob/main/p2_rescue_people/rescue.png)](https://urjc-my.sharepoint.com/:v:/g/personal/m_useros_2022_alumnos_urjc_es/EYTJUBE4g1pJv93l-LNOAeYBZuHwFXYdY0VC3lNGuiYOTQ?e=pUnuJB&nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJTdHJlYW1XZWJBcHAiLCJyZWZlcnJhbFZpZXciOiJTaGFyZURpYWxvZy1MaW5rIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXcifX0%3D)
