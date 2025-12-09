# P5 AUTONOMOUS FRONTIER EXPLORATION

## Índice
1. Descripción General
2. Objetivos de la Práctica
3. Componentes Principales
4. Flujo de Ejecución
5. Problemas Encontrados
6. Video de la ejecucion

---

## Descripción General

Esta práctica implementa un sistema de **mapeo y exploración autónoma** para un robot móvil en un entorno desconocido. A diferencia de prácticas anteriores, el robot no dispone del mapa a priori, sino que debe construirlo en tiempo real utilizando un **Grid de Ocupación Probabilístico (Bayesiano)**. El objetivo es que el robot navegue hacia las zonas desconocidas (**fronteras**) hasta completar el mapa, planificando su movimiento mediante una representación **discretizada del entorno** y algoritmos de búsqueda.

---

## Objetivos de la Práctica

1. **Construir un mapa de ocupación** en tiempo real utilizando un algoritmo probabilístico de Log Odds.
2. **Procesar datos del laser** y transformarlos al sistema de coordenadas del mapa global.
3. **Identificar fronteras**, definidas como los límites entre el espacio libre conocido y el espacio desconocido.
4. **Planificar rutas de navegación** hacia la frontera más cercana mediante el algoritmo **BFS (Breadth-First Search)**.
5. **Realizar una exploración completa**, terminando la ejecución cuando no queden áreas alcanzables sin explorar.

---

## Componentes Principales

### 1. Mapeo Probabilístico
Se utiliza un grid bayesiano para estimar la probabilidad de ocupación de cada celda. Se definen constantes logarítmicas para actualizar el mapa:
- Incremento de probabilidad cuando el láser detecta un obstáculo.
- Decremento de probabilidad cuando el rayo láser atraviesa una celda libre.
- Se limita el valor máximo y mínimo para evitar saturación.

### 2. Ray Tracing y Procesamiento Láser
Para proyectar la visión del robot en el mapa:
- Se convierten las lecturas del láser de coordenadas polares a cartesianas.
- Se utiliza un algoritmo de **trazado de rayos** para determinar qué celdas del grid son atravesadas por el láser (libres) y en cuál impacta (obstáculo).

### 3. Representación en Grid de Navegación
El mapa de probabilidades se simplifica en una matriz de navegación con celdas de tamaño 20px. Cada celda se clasifica en tres estados discretos:
- **OBSTACLE**: Probabilidad de ocupación alta.
- **EMPTY**: Probabilidad de ocupación baja (espacio seguro).
- **UNKNOWN**: Áreas aún no visitadas por los sensores.

### 4. Detección de Fronteras
El sistema escanea el grid de navegación buscando celdas clasificadas como **EMPTY** que sean adyacentes a celdas **UNKNOWN**. Estas celdas se agrupan como **puntos de frontera**, convirtiéndose en los objetivos potenciales a los que el robot debe viajar para expandir el conocimiento del mapa.

### 5. Algoritmo de Navegación (BFS)
Para llegar a la frontera más cercana, se utiliza el algoritmo de **Búsqueda en Anchura (BFS)**:
- Encuentra el camino más corto en el grid hacia la primera celda etiquetada como frontera.
- Evita obstáculos conocidos y devuelve una lista de coordenadas para seguir.
  
---

## 4 Flujo de Ejecución

1. Lectura de odometría y datos láser.
2. Transformación de coordenadas del robot y láser al sistema del mapa.
3. **Actualización del Mapa Bayesiano**: Cálculo de Log Odds y Ray Tracing para actualizar.
4. Generación del grid de navegación y umbralización de obstáculos/espacio libre.
5. **Detección de Fronteras**: Identificación de bordes explorables.
6. Planificación de ruta mediante **BFS** hacia la frontera más cercana.
7. Ejecución del bucle de control PID para mover el robot.
8. Repetición continua hasta completar la exploración.

---

## 5 Problemas Encontrados

- **Ruido en el mapeo**, donde lecturas erróneas del láser crean obstáculos temporales que bloquean rutas válidas.
     - **Solución**: Ajustar los valores del ajuste bayesiano para dar más "peso" a la evidencia acumulada y filtrar lecturas esporádicas.
- **Navegacion ineficiente** debido a que anteriormente no se hacia BFS hasta que se llegaba a la anterior frontera.
     - **Solución**: buscar siempre las fronteras mas cercanas. 
---

## 6 Video de la ejecución

[![Ver video]()
