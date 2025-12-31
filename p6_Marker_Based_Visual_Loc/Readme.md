# P5 LOCALIZACIÓN VISUAL CON APRILTAGS

## Índice
1. Descripción General
2. Objetivos de la Práctica
3. Componentes Principales
4. Flujo de Ejecución
5. Problemas Encontrados
6. Video de la ejecución

---

## Descripción General

En esta práctica se desarrolla un sistema de autolocalización robusta para un robot doméstico utilizando balizas visuales (AprilTags) y odometría.
El robot debe:
  - Navegar de forma autónoma por el entorno explorando diferentes zonas (algoritmo reactivo).
  - Detectar marcas visuales para calcular su posición absoluta (Global).
  - Fusionar la información visual con la odometría para mantener la localización cuando no hay balizas a la vista (Local).

El sistema integra visión por computador, resolución del problema *Perspective-n-Point* (PnP), transformaciones de matrices homogéneas y lógica de navegación basada en sensores láser.

---

## Objetivos de la Práctica

1. **Exploración Autónoma:** Implementar un algoritmo de navegación reactiva que permita al robot recorrer el entorno sin quedarse atascado, utilizando el sensor láser para evitar obstáculos.
2. **Localización Visual (PnP):** Calcular la pose relativa de la cámara respecto a una baliza detectada y, mediante transformaciones de coordenadas, obtener la posición absoluta del robot en el mapa.
3. **Fusión de Sensores:** Integrar la corrección absoluta de las balizas con la estimación incremental de la odometría para cubrir los tiempos muertos (zonas ciegas) donde no se detectan marcas.

---

## Componentes Principales

### 1. Sistema de Navegación (Choca-Gira)
Se ha implementado un autómata de navegación reactiva basado en lecturas del láser.
- El robot avanza hasta que detecta un obstáculo a menos de 1 metro en su sector central.
- Al detectar colisión inminente, se detiene y aplica una velocidad angular con un signo aleatorio para desatascarse y continuar explorando.

### 2. Detección y PnP (Perspective-n-Point)
- Se detectan las esquinas de los códigos QR en la imagen.
- Se resuelve la pose 3D de la baliza respecto a la cámara, que devuelve los vectores de rotación y traslación.
- Se selecciona siempre la baliza más cercana (mayor área en imagen) para minimizar el error de estimación.

### 3. Transformación de Coordenadas
El núcleo de la localización reside en la cadena de transformaciones para pasar del sistema de coordenadas de la cámara al mundo:
1. **Inversión PnP:** Se invierte la transformación para obtener la posición de la cámara respecto al Tag.
2. **Offset del Robot:** Se aplica la transformación fija de la cámara montada en el robot, ajustando traslación y rotación.
3. **Mundo:** Usando la posición conocida del Tag en el mapa, se rota y traslada la posición relativa para obtener las coordenadas globales del robot.

### 4. Estimación Híbrida (Odometría + Visión)
- **Cuando hay Tags:** La posición se sobrescribe con el cálculo visual (absoluto). Se guarda esta posición y se resetea la referencia de la odometría interna.
- **Cuando NO hay Tags:** Se calcula el diferencial de desplazamiento usando la odometría y se suma a la última posición visual conocida fiable.

---

## 4 Flujo de Ejecución

1. **Inicialización:** Carga de parámetros de la cámara y posiciones de los Tags desde el archivo.
2. **Bucle de Control:**
   - **Lectura de Sensores:** Obtención de imagen y datos láser.
   - **Navegación:** Ejecución lógica de evitación de obstáculos (Stop -> Gira -> Avanza).
   - **Procesado de Imagen:** Detección de AprilTags.
   - **Decisión de Localización:**
     - *Caso 0 Tags:* Posición = Última_Estimación + (Odom_Actual - Odom_Previa).
     - *Caso >0 Tags:* - `Prespective n Point` al tag más cercano.
       - Transformación de coordenadas.
       - **Filtrado:** Verificación de consistencia (si la nueva posición salta demasiado respecto a la anterior sin justificación, se descarta para evitar ruido).
       - Actualización de `Posición` y `Odom_Previa`.
   - **Visualización:**.

---

## 5 Problemas Encontrados

Durante el desarrollo se han presentado y solucionado los siguientes inconvenientes:

- **Ejes de Coordenadas (OpenCV vs Mundo):** La cámara en OpenCV usa el eje Z como profundidad, mientras que el robot usa el eje X como avance.
     - **Solución:** Se realizó una rotación manual y un mapeo de ejes para alinear los sistemas.
- **Ruido en la detección PnP:** A veces, detecciones lejanas o con mucho ángulo generaban "saltos" bruscos en la posición estimada.
     - **Solución:** Se implementó una función que actúa como filtro de rechazo de outliers. Si la nueva posición visual difiere más de una tolerancia (0.5m) de la anterior, se ignora temporalmente hasta que se estabilice (usando un contador `counter_lost`).
- **Pérdida de calibración de cámara:** La matriz de la cámara aproximada generaba errores de escala.
     - **Solución:** Se ajustaron los coeficientes de la matriz intrínseca empíricamente para minimizar el error en distancias conocidas.
- **Deriva de la Odometría:** Al navegar mucho tiempo sin ver marcas, el robot acumulaba error.
     - **Solución:** La estrategia de exploración aleatoria asegura que el robot visite frecuentemente zonas con balizas para "resetear" el error acumulado.

---

## 6 Video de la ejecución

- Localización visual con AprilTags
  
  [![Ver video](https://github.com/marcosuse/robotica_de_servicio_2025/blob/main/P6_Marker_Based_Visual_Loc/loc.png)](https://urjc-my.sharepoint.com/:v:/g/personal/m_useros_2022_alumnos_urjc_es/IQAeRoPOrntmRqHPfOfk4ooxAeAE06SfAw07FBroo0j4pxY?e=Xqwk7M&nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJTdHJlYW1XZWJBcHAiLCJyZWZlcnJhbFZpZXciOiJTaGFyZURpYWxvZy1MaW5rIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXcifX0%3D)
