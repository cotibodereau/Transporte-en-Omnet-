# Laboratorio 3 – Redes y Sistemas Distribuidos

## Informe

**Autores:**

* Aravena Aaron Lihuel
* Ferández Bodereau Constanza
* Fonseca Gonzalo Agustín


### Tabla de Contenidos

1. [Objetivos](#objetivos)
2. [Tarea 1 – Análisis](#tarea-1--análisis)

   1. [Modelo implementado](#modelo-implementado)
   2. [Resultados](#resultados)
   3. [Discusión](#discusión)
3. [Tarea 2 – Diseño](#tarea-2--diseño)

   1. [Algoritmo propuesto](#algoritmo-propuesto)
   2. [Resultados](#resultados-1)
   3. [Discusión](#discusión-1)
4. [Conclusiones Generales](#conclusiones-generales)
5. [ Detalle de uso de IA](#detalle-de-uso-de-ia)

---

## Abstract

Este laboratorio usa OMNeT++ para simular redes y ver cómo se comportan con distintos tipos de tráfico y capacidad.  Primero evaluamos cómo se saturan los buffers y se pierden paquetes cuando los parámetros no están bien ajustados. Después diseñamos un algoritmo sencillo de control de flujo y congestión para evitar esos problemas. Estas simulaciones nos permiten ver cómo reacciona la red paso a paso.

## Objetivos

El objetivo es describir, simular y analizar un sistema de colas en OMNeT++ considerando límites en la tasa de transferencia y capacidad de buffers, además de crear un método simple para controlar el flujo y evitar sobrecargas que provoquen pérdidas de paquetes.

---

## Tarea 1 – Análisis

### Modelo implementado

Usamos una configuración básica: NodeTx → Queue → NodeRx → Sink, donde NodeTx representa al generador y primer buffer, Queue es la red, y NodeRx representa el receptor y su buffer. Usamos paquetes tipo cPacket con tamaño 12500 Bytes.

| Enlace         | datarate | delay  |
| -------------- | -------- | ------ |
| NodeTx → Queue | 1 Mbps   | 100 µs |
| Queue → NodeRx | 1 Mbps   | 100 µs |
| NodeRx → Sink  | 0.5 Mbps | –      |

### Resultados

#### Hipótesis primer caso:
En este primer caso evaluamos la ocupación de los tres buffers (NodeRx, NodeTx y Queue) para distintos valores de generationInterval.  

Como podemos ver desde el inicio, la tasa de transferencia hacia NodeRx es mayor que la tasa que va desde NodeRx hacia Sink. Esto provoca que entren más paquetes al sistema de los que pueden salir. Por lo tanto, si el generationInterval es muy pequeño y se generan muchos paquetes por segundo, el buffer de NodeRx se empieza a llenar hasta colapsar, ya que los paquetes se acumulan más rápido de lo que pueden ser procesados.

*Figura 1.* generationInterval = 1.0 s  
![Buffer size vector (interval 1.0 s)](/mnt/data/Line Chart(1).png)

*Figura 2.* generationInterval = 0.3 s  
![Buffer size vector (interval 0.3 s)](/mnt/data/Line Chart(0.3).png)

*Figura 3.* generationInterval = 0.2 s  
![Buffer size vector (interval 0.2 s)](/mnt/data/Line Chart(0.2).png)

Observaciones:

- Para generationInterval = 1.0 s (Figura 1) los tres buffers se mantienen casi vacíos, sin picos de saturación.
- Con generationInterval = 0.3 s (Figura 2)  empiezan a haber pequeñas oscilaciones en el buffer de recepción (NodeRx), pero sin llegar a colapsar.
- Al reducir a generationInterval = 0.2 s (Figura 3) el buffer de NodeRx se satura claramente.