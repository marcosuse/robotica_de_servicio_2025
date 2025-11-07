
# P3 PARKING CAR

## Índice

1. Descripción General
2. Objetivos de la Práctica
3. Componentes Principales
4. Flujo de Ejecución
5. Problemas Encontrados
6. Video de la ejecución

---

## 1. Descripción General

Esta práctica implementa un sistema de **aparcamiento autónomo** para un coche en un entorno simulado.
El vehículo se desplaza paralelo a una fila de obstáculos, **manteniendo una distancia constante** mediante control PID.
Mientras avanza, analiza el entorno utilizando **sensores láser frontal, trasero y lateral**, y detecta si existe una **plaza libre** para aparcar.

Una vez identificada la plaza, el sistema ejecuta una **maniobra de aparcamiento paralelo** dividida en varias fases controladas por una **máquina de estados**.

---

## 2. Objetivos de la Práctica

1. Implementar una **máquina de estados** que controle el movimiento del coche.
2. Utilizar datos de **láser** para detectar obstáculos y estimar líneas de referencia.
3. Mantener el vehículo **paralelo** a una línea mediante **control PID**.
4. Detectar automáticamente una **plaza libre** adecuada.
5. Realizar la maniobra de aparcamiento en varias etapas con control suave.

---

## 3. Componentes Principales

### 1. Máquina de Estados Finita (FSM)

Los estados utilizados son:

* **GO_PARALLEL:** el coche se alinea con la línea de obstáculos.
* **SEARCH_SPOT:** avanza manteniendo la distancia hasta encontrar hueco.
* **PARK_CAR:** ejecuta la maniobra de aparcamiento.
* **FINISH:** el coche queda estacionado.

### 2. Detección de Obstáculos

Se utilizan tres láseres:

* **Frontal**
* **Lateral derecho**
* **Trasero**

Cada uno se transforma en coordenadas (x,y) relativas al coche para generar una nube de puntos.

### 3. Ajuste de Línea

Se realiza una regresión lineal sobre los puntos detectados para estimar:

```
y = m x + b
```

Esta línea representa el borde de los coches aparcados o la pared.

### 4. Control PID

Se aplica un PID sobre el **error de orientación y distancia** respecto a la línea.
Esto permite mantener el coche **paralelo y estable**.

### 5. Maniobra de Aparcamiento

La función `park_car()` divide el aparcamiento en fases:

1. Gira hacia la plaza.
2. Ajusta el ángulo inverso.
3. Centra el coche usando las distancias detectadas.

Al finalizar, el estado cambia a **FINISH**.

---

## 4. Flujo de Ejecución

1. **Inicialización:** se leen los láseres y la posición del coche.
2. **GO_PARALLEL:** el coche avanza ajustando `W` para mantener la distancia `DIST_2_PARK`.
3. **SEARCH_SPOT:** se avanza mientras se analiza si hay un espacio libre.
4. **PARK_CAR:** se ejecuta la maniobra compuesta por varias fases con control.
5. **FINISH:** se detiene el vehículo.

---

## 5. Problemas Encontrados

* **Inestabilidad en el control PID** cuando la línea no está bien definida.

  * Solución: ajustar los coeficientes Kp y Kd para suavizar.

* **Ruido en la nube de puntos**, especialmente en ángulos.

  * Solución: aplicar filtrado y tolerancia en el ajuste de línea.

* **Detección incorrecta de plaza** cuando los obstáculos son irregulares.

  * Solución: ajustar `THRESHOLD_PARK` y dimensiones del vehículo.

---

## 6. Video de la ejecución

(Insertar enlace cuando esté disponible)

[![Ver video](https://github.com/marcosuse/robotica_de_servicio_2025/blob/main/p3_parking_car/park.png)](https://urjc-my.sharepoint.com/:v:/g/personal/m_useros_2022_alumnos_urjc_es/Efux4GFmKGJOlKMCoM-kQKgBggT8ISic0EBrDXufh2DECA?e=Hm6fFc&nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJTdHJlYW1XZWJBcHAiLCJyZWZlcnJhbFZpZXciOiJTaGFyZURpYWxvZy1MaW5rIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXcifX0%3D)
