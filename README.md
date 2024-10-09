# blog-robotica-movil-24-25-LiamSaboridoSueiro

## Indice de prácticas
<details>
<summary>Práctica 1</summary>

- [Objetivo](#objetivo)
- [Teoría](#teoría)
  * [FSM](#fsm)
  * [Reactividad](#reactividad)
- [Funcionalidad del código](#funcionalidad)
  * [Algoritmos de Cobertura](#algoritmos-de-cobertura)
  * [Dificultades](#dificultades)
- [Video](#video)

</details>

<details>
<summary>Práctica 2</summary>

- [Objetivo](#objetivo-p2)
- [Teoría](#teoría-p2)
  * [Controlador PID](#controlador-pid-p2)
- [Funcionalidad del Código](#funcionalidad-del-código-p2)
  * [Detección de la Línea](#detección-de-la-línea-p2)
  * [Ajuste de Parámetros del controlador](#ajuste-de-parámetros-del-controlador-p2)
- [Dificultades](#dificultades-p2)
- [Video](#video-p2)

</details>


# P1 - Vacuum Cleaner

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
El código es reactivo porque utiliza time.time() en lugar de sleep(), lo que permite que el robot esté siempre atento a su entorno sin detenerse por un tiempo fijo. Esto significa que sigue midiendo el tiempo mientras puede reaccionar a eventos, como colisiones, de forma inmediata. Además, está programado con FSM, lo que le permite verificar constantemente si necesita cambiar de estado, por ejemplo, de moverse en espiral a girar o avanzar en línea recta. De esta forma, el robot siempre está preparado para adaptarse en tiempo real según lo que detecta.


## [Funcionalidad del código](#)

### [Algoritmos de Cobertura](#)

El robot comienza moviéndose en espiral, aumentando su velocidad lineal y disminuyendo gradualmente su velocidad angular para expandir la espiral. Si detecta una colisión con el bumper, interrumpe este movimiento y entra en un estado de giro, ajustando su dirección según el lugar del choque (izquierda, derecha o frontal). El giro se realiza durante un tiempo aleatorio, lo que le permite evitar obstáculos de manera no predecible.

Tras completar el giro, el robot entra en un estado de movimiento recto, avanzando hasta detectar otra colisión o, en el caso de la última serie de movimientos, deteniéndose tras un tiempo aleatorio para evitar choques. Este ciclo de estados se repite varias veces hasta que el robot completa un número definido de colisiones, volviendo entonces al estado de espiral.

En total, el robot realiza hasta cuatro series de colisiones. En cada serie, después de colisionar y girar, el robot se mueve de nuevo en línea recta hasta el siguiente choque. Sin embargo, en la última serie, es decir, tras el tercer choque (cuarta colisión siendo la última), el robot no avanza hasta chocar de nuevo, sino que se mueve en línea recta por un tiempo aleatorio antes de detenerse. Esto asegura que el robot finalice su recorrido sin necesidad de realizar una colisión final.

Este enfoque permite que el robot cubra el área eficientemente mientras ajusta su comportamiento de acuerdo con las colisiones que experimenta, lo que es ideal para entornos dinámicos donde el robot debe adaptarse constantemente.

### [Dificultades](#)

La mayor dificultad que enfrenté fui yo mismo ya que me puse a realizar la práctica sin haber leído todo el enunciado de la práctica, las ganas de programar y trabajar me cegaron y eso me convirtió en mi mayor enemigo. Había escrito el código sin usar una Máquina de Estados Finita (FSM) y solo con "sleep" para manejar los tiempos, lo que hizo que el diseño fuera torpe y complicado de ajustar. Así que tuve que reescribir gran parte del código para implementar bien los estados y eliminar los "sleep". He de decir que menos mal que lo hice ya que ahora funciona mucho mejor.

## [Video](#)
[![Mira este video](https://img.youtube.com/vi/5KjhgpSd9Ww/maxresdefault.jpg)](https://youtu.be/5KjhgpSd9Ww?si=QhySTQuyrdwZsCam)


# P2 - Follow Line
<p align="center">
  <img src="https://github.com/user-attachments/assets/604ab9f8-8ac5-43a5-aae0-b09938547472" width="750" height="750">
</p>

<h2 id="objetivo-p2">Objetivo</h2>

El objetivo de esta práctica es programar un controlador PID para que un coche de fórmula 1 siga una línea roja en el centro del circuito y complete la vuelta lo más rápido posible. Usando la cámara frontal, detectaremos la línea y ajustaremos la velocidad y dirección. El controlador PID debe mantener el coche estable en el centro del carril e intentar evitar oscilaciones.


<h2 id="teoría-p2">Teoría</h2>

<h3 id="controlador-pid-p2">Controlador PID</h3>

Un controlador PID es un sistema de control que ajusta el comportamiento del coche para que siga una línea objetivo de forma precisa. PID significa Proporcional, Integral y Derivativo, y estos tres términos trabajan juntos para corregir la posición del coche en relación con la línea a seguir.

1. Componente Proporcional (P): Este término se basa en el error actual, es decir, la distancia entre la línea roja detectada y el centro del coche. Multiplica el error por una constante (Kp) y aplica una corrección. Si el error es grande, el ajuste también será fuerte. Solo con el término P, el coche puede tener problemas de oscilación o no lograr centrarse del todo en la línea.

2. Componente Integral (I): Este término se ocupa de corregir errores acumulados en el tiempo, especialmente cuando el coche se va desviando poco a poco. Integrando el error, el componente I elimina desvíos sostenidos que el P por sí solo no corrige, como esos desajustes menores que se van sumando.

3. Componente Derivativo (D): Este término predice cambios futuros al calcular la rapidez con la que cambia el error. Ayuda a suavizar la corrección, evitando oscilaciones grandes, sobre todo en curvas o cambios bruscos del circuito. El D le permite al coche ajustar su posición de manra anticipada y ganar estabilidad a alta velocidad.

Para ajustar estos compnentes, se recomienda el método de prueba y error o el Ziegler-Nichols, que ayudan a afinar los valores Kp, Ki y Kd y mejorar así el rendimiento.


<h2 id="funcionalidad-del-código-p2">Funcionalidad del Código</h2>

<h3 id="detección-de-la-línea-p2">Detección de la Línea</h3>

En este código, me enfoqué en detectar la línea roja solo entre las alturas 200 y 250 de la imagen, ya que en esa área es más sencillo determinar la dirección que necesita tomar el coche. Esto me permite anticipar si el coche debe girar, al captar cambios en la posición de la línea antes de que el coche los alcance. Para lograr esto, primero extraigo el canal rojo de la imagen en esa sección y aplico un filtro para destacar solo los píxeles que representan la línea, creando así una máscara binaria.

Luego, calculo el centroide de la línea usando los momentos de la imagen de la máscara, lo cual me da la posición exacta del centro de la línea en esas alturas específicas. Este centroide se convierte en mi punto de referencia o "punto de seguimiento" para el controlador PID, que ajusta la velocidad angular del coche para que se mantenga centrado en el carril y siga la línea de manera precisa.


<h3 id="ajuste-de-parámetros-del-controlador-p2">Ajuste de Parámetros del Controlador</h3>

En el controlador PID de mi código, establecí constntes para los tres términos del PID: Kp (proporcional), Ki (integral) y Kd (derivativo). La idea es ajustar la velocidad angular del coche para que se mantenga alineado con la línea roja detectada. Primero, calculo el error de posición, que es la diferencia entre el centro de la imagen (donde debería estar el coche) y la posición del centroide de la línea roja. Este error se usa en cada componente del PID.

Para el término proporcional (P), multiplico el error de posición por Kp para aplicar una corrección que sea proporcional al tamaño del error; si el coche está muy desviado, la corrección será mayor. Luego, para el término integral (I), acumulo el error en cada iteración (sumándolo a lo largo del tiempo) y lo multiplico por Ki, lo que ayuda a corregir desvíos constantes y evitar que el coche se quede desalineado. Para evitar que este valor se vuelva excesivo, limito la suma con un máximo que llamé max_suma_error.

El término derivativo (D) calcula la variación del error desde el último ciclo (delta_error), ayudándome a predecir hacia dónde se dirige el error y hacer correcciones suaves. Este término es crucial para evitar oscilaciones en el coche. Finalmente, sumo los tres términos (proporcional, integral y derivativo) y los aplico como la velocidad angular angular_vel, que ajusta el giro del coche para mantenerlo en el centro del carril mientras sigue la línea roja.


<h2 id="dificultades-p2">Dificultades</h2>

Una de las mayores dificultades al desarrollar el controlador PID fue ajustar los parámetros para que el coche siguiera la línea de forma estable, sin demasiadas oscilaciones. Esto fue especialmente complicado en el sistema de dirección tipo Ackerman, donde los ajustes incorrectos provocaban que el coche oscilara mucho y terminara derrapando, sobre todo en las curvas y cambios bruscos de dirección. Cada ajuste de los valores PID generaba respuestas muy distintas, por lo que fue necesario hacer muchas pruebas para encontrar un equilibrio adecuado, en especial en el término derivativo, que ayudó a reducir las oscilaciones sin perder la capacidad del coche para seguir la línea correctamente.

<h2 id="video-p2">Video</h2>

En el código, hay una tabla que indica qué valores cambiar dependiendo del mapa que quieras utilizar. También es importante considerar que, dependiendo del ordenador que uses, el rendimiento puede variar y el coche podría ir más lento.

- Video en el **Mapa Simple** (33 segundos):  
  [![Mapa Simple](https://img.youtube.com/vi/eKodGntwkJ0/0.jpg)](https://youtu.be/eKodGntwkJ0)

- Video en el **Mapa Simple Ackerman** (150 segundos):  
  [![Mapa Simple Ackerman](https://img.youtube.com/vi/93viZgo9WaU/0.jpg)](https://youtu.be/93viZgo9WaU)









