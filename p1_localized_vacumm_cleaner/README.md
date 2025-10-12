# P1 LOCALIZED VACUMM CLEANER

## Índice
1. Descripción General
2. Objetivos de la Práctica
3. Componentes Principales
4. Flujo de Ejecución
5. Problemas Encontrados
6. Video de la ejecucion

---

## Descripción General

Esta práctica implementa un sistema de **exploración autónoma** para un robot móvil dentro de un entorno conocido con su mapa correspon. El objetivo es que el robot sea capaz de **recorrer completamente todo el área**, planificando su movimiento mediante una representación **discretizada del mapa** en una rejilla de navegacion.  

---

## Objetivos de la Práctica

1. **Procesar un mapa de entorno** y transformarlo en una rejilla.  
2. **Identificar áreas libres y obstáculos** mediante procesamiento de imágenes.  
3. **Planificar rutas de exploración** basadas en el algoritmo de BSA .  
4. **Ejecutar control PID** para el movimiento lineal y rotacional del robot.  
5. **Realizar una exploración completa del mapa**, marcando puntos críticos y rutas de retorno.  

---

## Componentes Principales

### 1. Conversión de Coordenadas  
Se establecen transformaciones entre los sistemas de coordenadas del **simulador (Gazebo)** y del **mapa (imagen)**, permitiendo sincronizar la posición real del robot con la representación visual.

### 2. Procesamiento del Mapa  
El mapa base se carga desde una imagen y se convierte a escala de grises para su posterior análisis. Se aplica una operación de **dilatación o erosión** para generar un mapa navegable que considera el tamaño del robot y su margen de seguridad.

### 3. Representación en Grid  
El mapa se divide en **celdas cuadradas**. Cada celda se clasifica como:
- Obstáculo  
- Espacio libre  
- Punto crítico  
- Ruta de retorno  
- Posición del robot  

### 4. Algoritmo de BSA  
El robot utiliza el **Backtracking Spiral Algorithm (BSA)** para recorrer todas las celdas libres del mapa.  
Este método:
- Explora sistemáticamente las celdas adyacentes.  
- Marca puntos críticos y posibles rutas de retorno.  
- Emplea **búsqueda BFS** cuando no hay movimientos directos disponibles.  
- Garantiza la exploración completa del entorno sin omitir áreas accesibles.

### 5. Control PID  
Se implementan dos controladores PID:
- Uno para la **velocidad lineal**, controlando la distancia al objetivo.  
- Otro para la **velocidad angular**, corrigiendo el error de orientación.  

Estos controladores permiten un desplazamiento suave, estable y preciso hacia cada celda objetivo.

### 6. Visualización y Monitoreo  
Durante la ejecución:
- El mapa se actualiza en tiempo real mostrando la posición y trayectoria del robot.  
- Se visualizan las celdas exploradas, obstáculos, puntos críticos y rutas de retorno.  
- La interfaz gráfica permite verificar el avance de la exploración.

---

## 4 Flujo de Ejecución

1. Carga y preprocesamiento del mapa.  
2. Generación del grid y clasificación de las celdas.  
3. Transformación de coordenadas entre Gazebo y el mapa.  
4. Inicialización del algoritmo de BSA.  
5. Ejecución del bucle principal:
   - Cálculo del error lineal y angular.  
   - Aplicación del control PID.  
   - Avance del robot y actualización del mapa.  
6. Finalización cuando todas las celdas accesibles han sido visitadas.

---

## 5 Problemas Encontrados

Durante el desarrollo y ejecución de la práctica se pueden presentar los siguientes inconvenientes:

- **Desajustes en la transformación de coordenadas**, provocando que la posición del robot en el mapa no coincida con su posición real en el simulador.
     - **Solución**: coger mas puntos en el mapa con sus correspondientes puntos en el mundo.
- **Errores de calibración del PID**, causando oscilaciones y movimientos bruscos.
     - **Solución**: probar mas valores.
- **Algoritmo BSA**, cuando llegan a puntos criticos, antes de usar BFS, se usaba distancia euclidia causando que los planes sean muy poco optimos y las rutas hacia los puntos de retornos sean dificiles o imposibles de seguir.
  **Solución**: usar bfs.
- **Retrasos o sobrecarga visual**, debido a la actualización frecuente de la interfaz gráfica.
     - **Solución**: mostrar el mapa cada un numero de iteraciones del bucle.

---

## 6 Video de la ejecución

[![Ver video](https://github.com/marcosuse/robotica_de_servicio_2025/blob/main/p1_localized_vacumm_cleaner/local_vacumm.png)](https://urjc-my.sharepoint.com/:v:/g/personal/m_useros_2022_alumnos_urjc_es/EWBF2h6yHfZPgWEQOqXcoxIBcpV9NeJ50szexMsVlmyR4A?e=1gFHZB&nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJTdHJlYW1XZWJBcHAiLCJyZWZlcnJhbFZpZXciOiJTaGFyZURpYWxvZy1MaW5rIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXcifX0%3D)


