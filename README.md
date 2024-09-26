# blog-robotica-movil-24-25-LiamSaboridoSueiro

## Indice de prácticas
<details>
<summary>Práctica 1</summary>

- [Objetivo](#objetivo)
- [Teoría](#teoría)
  * [FSM](#fsm)
  * [Reactividad](#reactividad)
  * [Algoritmos de Cobertura](#algoritmos-de-cobertura)
- [Funcionalidad del código](#funcionalidad)
  * [Generación de Ángulos Aleatorios](#generación-de-ángulos-aleatorios)
  * [Movimiento en Dash](#movimiento-en-dash)
  * [Movimiento en Espiral](#movimiento-en-espiral)
- [Video](#video)

</details>

## P1 - Vacuum Cleaner

![442991](https://github.com/user-attachments/assets/15a1598a-b269-450b-9d2b-7640c1e55fd7)


## [Objetivo](#)

El objetivo de esta práctica es desarrollar y probar algoritmos de control para un robot móvil que debe limpiar un área específica, utilizando la plataforma Unibotics. A través de esta experiencia, se busca entender conceptos clave como máquinas de estado finito, reactividad y algoritmos de cobertura, implementando diversas funcionalidades de movimiento y limpieza en el robot.

## [Teoría](#)
### [FSM](#)

![imagen](https://github.com/user-attachments/assets/3a2c7b8f-d523-49b3-ab19-a0791577fd1a)
(gráfico hecho con [Graphviz](https://graphviz.org/))


La FSM del robot se basa en la detección de colisiones y la estrategia de limpieza, que se lleva a cabo en tres estados: Espiral, Giro, y Movimiento Recto. A continuación, se describe la lógica detrás de las transiciones entre estos estados:
1. De Estado Espiral a Estado Giro

    Condición de Transición: El robot detecta una colisión frontal (bumper_data == 1).
    Razón: El movimiento en espiral permite al robot cubrir un área amplia, pero cuando encuentra un obstáculo, es esencial que cambie su comportamiento para evitar el choque. La transición a Estado Giro permite al robot tomar acción inmediata para evadir el obstáculo.

2. De Estado Giro a Estado Movimiento Recto

    Condición de Transición: Se ha completado el tiempo de giro asignado, es decir, el tiempo transcurrido desde que comenzó a girar es mayor o igual al tiempo de giro determinado.
    Razón: Una vez que el robot ha girado para evitar el obstáculo, necesita avanzar hacia adelante para continuar su tarea de limpieza. Este estado de movimiento recto asegura que el robot no quede atrapado en una serie de giros y pueda seguir avanzando hacia nuevas áreas.

3. De Estado Movimiento Recto a Estado Giro

    Condición de Transición: Se detecta otra colisión (bumper_data == 1) durante el movimiento hacia adelante y no se ha alcanzado el máximo de choques permitidos.
    Razón: Si el robot encuentra un nuevo obstáculo mientras se mueve en línea recta, necesita girar nuevamente para evitar ese obstáculo. Esta lógica asegura que el robot pueda seguir navegando de manera efectiva en el entorno, adaptándose a los obstáculos que encuentra.

4. De Estado Movimiento Recto a Estado Espiral

    Condición de Transición: Se ha alcanzado el número máximo de choques permitidos (choques_realizados >= max_choques).
    Razón: Después de un número predefinido de colisiones, el robot debe volver a su patrón de espiral para continuar la limpieza de áreas no cubiertas. Esta transición permite que el robot restablezca su estrategia de cobertura después de haber evitado obstáculos durante un tiempo.

5. Regreso a Estado Espiral

    Condición de Transición: Si se trata de la última serie de choques o si el robot no ha chocado más (duración_movimiento completa).
    Razón: Al final de una serie de movimientos, el robot debe reiniciar su ciclo para continuar explorando nuevas áreas. Este enfoque garantiza que el robot maximice su cobertura y minimice la posibilidad de quedar atrapado en un patrón repetitivo de colisiones.

### [Reactividad](#)


### [Algoritmos de Cobertura](#)


## [Funcionalidad del código](#)
### [Generación de Ángulos Aleatorios](#)


### [Movimiento en Dash](#)


### [Movimiento en Espiral](#)


## [Video](#)

