# blog-robotica-movil-24-25-LiamSaboridoSueiro

## Indice

<details>
<summary>Práctica 3</summary>

- [Objetivo](#objetivo)
- [Teoría](#teoría)
  * [Algoritmo Virtual Force Field (VFF)](#algoritmo-virtual-force-field)
  * [Vectores de Fuerza](#vectores-de-fuerza)
- [Funcionalidad del Código](#funcionalidad-del-código)
  * [Generación de Fuerzas](#generación-de-fuerzas)
  * [Ajuste de Fuerzas Atractivas y Repulsivas](#ajuste-de-fuerzas-atractivas-y-repulsivas)
  * [Cambio de Objetivos](#cambio-de-objetivos)
  * [Velocidades](#velocidades)
- [Dificultades Encontradas](#dificultades-encontradas)
- [Video](#video)

## Práctica 3 - Obstacle Avoidance

</details>
<p align="center">
  <img src="https://github.com/user-attachments/assets/020ecf19-518a-4222-a03a-43d326023946" width="900" height="750">
</p>


## Objetivo

El objetivo de esta práctica es implementar un algoritmo de navegación basado en el método de **Virtual Force Field (VFF)** para que el coche de fórmula 1 pueda moverse por el circuito evitando obstáculos y dirigiéndose hacia los waypoints de manera eficiente. El coche utiliza un sensor láser frontal para detectar los obstáculos y calcular las fuerzas repulsivas, mientras que los waypoints generan una fuerza atractiva que guía al coche hacia el siguiente objetivo.

## Teoría

### Algoritmo Virtual Force Field (VFF)

En este algoritmo, el coche es guiado mediante la combinación de dos fuerzas: la **fuerza atractiva** que lo impulsa hacia el próximo waypoint, y la **fuerza repulsiva**, que lo aleja de los obstáculos detectados por el sensor láser. El resultado de la suma de estas fuerzas determina la dirección final que tomará el coche, ajustando tanto su velocidad lineal como angular para evitar colisiones y avanzar hacia el objetivo.

### Vectores de Fuerza

- **Fuerza Atractiva**: Este vector se calcula en función de la posición del coche y la del próximo waypoint. El coche es atraído hacia el waypoint y la dirección se ajusta constantemente a medida que el coche se acerca.
- **Fuerza Repulsiva**: Esta fuerza se genera a partir de los datos del sensor láser. Cada obstáculo detectado produce un vector repulsivo cuyo impacto aumenta cuanto más cercano esté el obstáculo.
- **Fuerza total**: Esta se calcula sumando las dos fuerzas anteriores..

## Funcionalidad del Código

### Generación de Fuerzas

Para implementar el algoritmo de VFF, primero me enfoqué en la generación de las fuerzas atractiva y repulsiva. La fuerza atractiva la calculo en la función calcular_fuerza_atractiva, donde obtengo el siguiente waypoint utilizando la función obtener_target. Una vez que tengo las coordenadas del waypoint, las convierto de absolutas a relativas para dirigir el coche en la dirección correcta. De esta manera, el coche siempre se siente atraído hacia el siguiente objetivo.

La fuerza repulsiva se calcula a partir de los datos del láser, que proporcionan 180 mediciones en un rango de 180 grados. Convierto estas mediciones en coordenadas cartesianas y, para cada obstáculo detectado, genero un vector repulsivo cuya magnitud es inversamente proporcional al cuadrado de la distancia, dando más importancia a los objetos cercanos. Luego, sumo todos estos vectores para obtener la fuerza repulsiva total, que aleja al coche de los obstáculos. Este método permite que el coche mantenga una distancia segura de los obstáculos más cercanos, asegurando una navegación fluida.

La atractiva la normalizo dividiendo el vector por su módulo, de manera que siempre apunte hacia el objetivo con una magnitud constante, sin importar la distancia. Esto me permite que el coche se dirija hacia el waypoint de forma controlada. En cambio, la repulsiva no, al no normalizarla, tiene un impacto mucho más fuerte cuando el coche se acerca a un objeto.

### Ajuste de Fuerzas Atractivas y Repulsivas

En la línea principal, hay dos variables que combina las dos fuerzas utilizando los factores de ponderación **ALPHA** y **BETA** para ajustar la influencia de cada fuerza en la dirección del coche. **ALPHA** controla la influencia de la fuerza atractiva, mientras que **BETA** ajusta el peso de la fuerza repulsiva, permitiendo que el coche equilibre de forma eficiente su avance hacia el objetivo mientras esquiva obstáculos.


### Cambio de Objetivos

Cuando el coche se acerca lo suficiente al objetivo actual (waypoint), el código llama a la función `obtener_target`, que detecta si el coche ha alcanzado el waypoint. Si es así, marca el target como alcanzado y selecciona el siguiente waypoint, asegurando que el coche siempre se mueva hacia el próximo objetivo.

### Velocidades

Para controlar el movimiento del coche, implementé la función `aplicar_velocidades`, donde combino las fuerzas atractiva y repulsiva para obtener la fuerza total que determina tanto la velocidad lineal como la angular del coche. La velocidad lineal se ajusta utilizando la componente de avance (fuerza total en el eje X), mientras que la velocidad angular se controla mediante la componente de giro (fuerza total en el eje Y).

Además, añadí una lógica extra para mejorar la capacidad de evitar colisiones. Utilizando los datos del láser, detecto si hay obstáculos cercanos en los lados izquierdo o derecho del coche, y en esos casos, ajusto la velocidad angular de forma brusca, forzando al coche a girar rápidamente para esquivar los objetos. Esto asegura que cuando haya coches completamente en frente y la repulsiva este en perpendicular al objeto, haga un giro rapido para evitar la colisión.

## Dificultades Encontradas

Una de las principales dificultades fue que la fuerza atractiva me daba valores NaN en algunas situaciones, debido a errores en la normalización del vector cuando la magnitud era demasiado pequeña o cercana a cero. Para solucionarlo, implementé una verificación que evita la normalización cuando el módulo del vector es muy pequeño, lo que estabilizó el cálculo.

Otra complicación fue evitar que el coche se chocara con obstáculos que tenía justo enfrente cuando había un waypoint detrás de ellos. Tras añadir la "lógica extra" comentada en el apartado anterior, y ajustar el valor ALPHA ya todo iba sobre ruedas.

## Video

Disculpa la calidad del video, lo tuve que grabar con el móvil ya que mi portátil no tiene la capacidad suficiente para grabar y ejecutar la simulación al mismo tiempo.

[![Video de la Práctica 3](https://img.youtube.com/vi/-USR6Nm4pgg/0.jpg)](https://youtu.be/-USR6Nm4pgg?si=KU-xcmtXNo_DHixv)
