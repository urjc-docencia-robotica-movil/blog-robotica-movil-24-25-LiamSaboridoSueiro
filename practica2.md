# blog-robotica-movil-24-25-LiamSaboridoSueiro

## Indice
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










