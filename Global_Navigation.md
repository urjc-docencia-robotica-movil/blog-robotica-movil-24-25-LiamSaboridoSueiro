
## Indice

<details>
<summary>Práctica 4</summary>

- [Objetivo](#objetivo)
- [Teoría](#teoría)
  * [Wave Front Algorithm](#wave-front-algorithm)
  * [Expansión de Obstáculos](#expansión-de-obstáculos)
  * [Métodos de Navegación](#métodos-de-navegación)
- [Funcionalidad del Código](#funcionalidad-del-código)
  * [Cálculo del Mapa de Coste](#cálculo-del-mapa-de-coste)
  * [Expansión de Costes en Obstáculos](#expansión-de-costes-en-obstáculos)
  * [Navegación Basada en Gradiente](#navegación-basada-en-gradiente)
- [Dificultades Encontradas](#dificultades-encontradas)
- [Video](#video)

</details>

## Práctica 4 - Global Navigation


<p align="center">
  <img src="https://github.com/user-attachments/assets/7315da90-678d-46dd-b84f-3a58940e3cd6" width="900" height="600">
</p>


## Objetivo

El objetivo de esta práctica es implementar un algoritmo de navegación global basado en el **Wave Front Algorithm** y el **Gradient Path Planning (GPP)** para que un taxi pueda desplazarse por una ciudad. A través de un clic en el mapa, se selecciona el destino, y el taxi debe calcular la ruta más corta para llegar al objetivo utilizando el mapa de coste generado.

El ejercicio busca explorar diferentes enfoques de navegación, ya sea planificando una trayectoria completa o navegando dinámicamente basándose en el gradiente del mapa de coste, priorizando siempre la eficiencia y seguridad en la navegación.

## Teoría

### Wave Front Algorithm

Para esta práctica, implementé el **Wave Front Algorithm** como base para generar un mapa de costes. Este algoritmo es muy útil ya que te permite calcular la distancia de cualquier punto del mapa al objetivo de forma sistemática. Básicamente, empieza desde el objetivo (target) y expande los costes por el espacio libre usando una búsqueda por anchura (BFS). De esta manera, cada celda del mapa tiene asignado un "coste" que indica qué tan lejos está del objetivo, y el taxi puede usar esta información para moverse hacia el target por la ruta más corta.

### Expansión de Obstáculos

Un detalle interesante que tuve que manejar fue que, al calcular el mapa de costes, las rutas más cortas a veces pasan peligrosamente cerca de las paredes o los obstáculos. Para solucionar esto, implementé una **expansión de obstáculos**, donde aumento el coste de las celdas cercanas a los obstáculos. Esto hace que el coche prefiera rutas que mantengan una distancia segura de las paredes y otros objetos. Este paso fue clave para que la navegación sea mas segura y que no se coma las paredes.

### Métodos de Navegación

Una vez que tenía el mapa de costes, había dos opciones principales para navegar: 

1. **Planificación de trayectorias**: Aquí podría haber calculado una ruta completa desde el inicio hasta el objetivo, fijando waypoints intermedios para que el coche los siguiera. Es una estrategia más "precisa", pero menos dinámica. He utilizado este método para calcular la ruta que debería hacer, pero para navegar se haría con gradientes.
   
2. **Navegación por gradiente**: Con este método, el taxi revisa continuamente el mapa en su vecindad a un radio de 5 celdas y elige la dirección donde el coste disminuye más rápidamente (siguiendo el gradiente). Este enfoque hace que el coche se adapte mejor a cambios inesperados en el entorno y evita tener que planificar todo desde el inicio.

## Funcionalidad del Código

### Cálculo del Mapa de Coste

El primer paso fue generar el **mapa de coste** utilizando el algoritmo **Wave Front Algorithm**. Este algoritmo comienza desde el objetivo seleccionado y expande los valores de coste utilizando una búsqueda por anchura (BFS). Cada celda del mapa recibe un valor que representa su "distancia" al objetivo. Para ello, definí una función `wave_front_algorithm` que recorre el mapa, asignando los valores de coste a cada celda accesible. Este mapa permite que el coche calcule de forma eficiente cómo llegar al objetivo.

### Expansión de Costes en Obstáculos

Para evitar que el coche navegue demasiado cerca de las paredes u obstáculos, implementé un sistema de **expansión de obstáculos**. Esto aumenta el área ocupada por los obstáculos, asignando valores de coste altos a las celdas cercanas a ellos. Este paso asegura que las rutas más cortas eviten zonas peligrosas, mejorando la seguridad durante la navegación. Lo hice con la función `expandir_obstaculos`, que modifica el mapa inicial para reflejar esta expansión.

### Navegación Basada en Gradiente

Para la navegación del coche, implementé un enfoque basado en el **gradiente de costes**. En lugar de planificar toda la ruta de inicio a fin, el coche analiza continuamente las celdas de su entorno para decidir la mejor dirección en cada momento. Esto se logra evaluando un **radio de celdas** alrededor de su posición actual, seleccionando la celda con el menor coste en el mapa de costes.

El proceso funciona así:

1. **Detección de la posición actual**: La posición del coche se traduce a coordenadas del mapa mediante `worldToGrid`, lo que permite identificar en qué celda del mapa se encuentra el coche en cada instante.

2. **Selección del waypoint más cercano**: Dentro del radio definido (5 celdas), el coche evalúa el coste de cada celda en su vecindad. Si encuentra una celda con un coste menor al actual, selecciona esa celda como su siguiente waypoint. Esto garantiza que el coche siempre se mueva hacia la dirección de mayor descenso en el coste.

3. **Conversión de coordenadas**: Una vez seleccionado el waypoint, lo convierto de coordenadas del mapa (grid), a coordenadas absolutas del mundo utilizando `gridToWorld`. Luego, convierto estas coordenadas absolutas a coordenadas relativas al coche usando la función `absolute2relative`. Esto es esencial porque las velocidades del coche se definen en su sistema de referencia local.

4. **Ajuste de velocidades**: Con las coordenadas relativas del waypoint, configuro la velocidad lineal y angular del coche mediante `HAL.setV` y `HAL.setW`. La componente X relativa se utiliza para ajustar la velocidad lineal, mientras que la componente Y ajusta la velocidad angular, asegurando que el coche se dirija correctamente al waypoint. En caso de que se quiera hacer que el taxi vaya más rápido se añade velocidad en la variable "Velocidad", en caso de que se suba demasiado, el coche no navegará tan bien generando un comportamiento casi "borracho".

5. **Condición de llegada**: Este proceso se repite hasta que el coste de la celda actual del coche sea menor que un umbral definido (5 celdas de proximidad). Esto indica que el coche ha alcanzado el objetivo.

El uso del gradiente simplifica la navegación al evitar la necesidad de planificar toda la ruta desde el principio y haciendo que sea un comportamiento reactivo.

### Dificultades Encontradas

Uno de los principales problemas que enfrenté fue trabajar con el formato del mapa. Inicialmente, asumí que las coordenadas del mapa estaban en formato `(x, y)`, pero en realidad estaban en `(col, row)`. Este error hizo que el cálculo del mapa de costes y la navegación estuvieran completamente desalineados, y me llevó tiempo entender cómo traducir correctamente entre estos dos formatos.

Otra dificultad importante fue aprender a usar correctamente la función `absolute2relative`. Al principio, cometí errores al calcular las coordenadas relativas del waypoint respecto al coche, lo que causaba que el coche no girara correctamente o incluso se desviara de la ruta. Este problema complicó mucho la implementación de la navegación basada en gradiente, ya que la dirección que debía seguir no era la adecuada. Tras varias pruebas y ajustes, logré que las conversiones funcionaran correctamente, y el coche finalmente pudo orientarse hacia el objetivo de manera eficiente.







## Video

Disculpa la calidad del video, lo tuve que grabar con el móvil ya que mi portátil no tiene la capacidad suficiente para grabar y ejecutar la simulación al mismo tiempo.

[![Video de la Práctica 3](https://img.youtube.com/vi/-USR6Nm4pgg/0.jpg)](https://youtu.be/-USR6Nm4pgg?si=KU-xcmtXNo_DHixv)
