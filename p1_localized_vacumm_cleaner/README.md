# P1 LOCALIZED VACUMM CLEANER

## 📑 Índice
1. [Descripción General](#-descripción-general)  
2. [Objetivos de la Práctica](#-objetivos-de-la-práctica)  
3. [Componentes Principales](#-componentes-principales)  
4. [Flujo de Ejecución](#-flujo-de-ejecución)  
5. [Posibles Problemas Encontrados](#️-posibles-problemas-encontrados)  
6. [Evidencias en Video](#-evidencias-en-video)  

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

## Flujo de Ejecución

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

## Posibles Problemas Encontrados

Durante el desarrollo y ejecución de la práctica se pueden presentar los siguientes inconvenientes:

- **Desajustes en la transformación de coordenadas**, provocando que la posición del robot en el mapa no coincida con su posición real en el simulador.  
- **Errores de calibración del PID**, que pueden causar oscilaciones o movimientos bruscos.  
- **Problemas de discretización del mapa**, especialmente si las dimensiones del robot y las celdas no están bien ajustadas.  
- **Bloqueos del algoritmo BSA**, cuando no se detectan correctamente los puntos de retorno o las celdas críticas.  
- **Retrasos o sobrecarga visual**, debido a la actualización frecuente de la interfaz gráfica.  
- **Imprecisiones en la detección de obstáculos**, si el mapa original tiene ruido o sombras.  

---

## Evidencias en Video

Se incluyen videos demostrativos del comportamiento del robot en diferentes escenarios:

1. **Exploración completa del entorno principal.**  
2. **Visualización del movimiento y cobertura de celdas.**  
3. **Comportamiento del PID ante distintos ángulos y distancias.**  
4. **Ejemplo de detección y retorno desde un punto crítico.**

> Los videos muestran el correcto funcionamiento del algoritmo de exploración y la estabilidad del control del robot en entornos simulados.
