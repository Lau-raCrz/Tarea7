# Tarea9



---

## Punto 1. Brazo robotic.
En este punto de la tarea, el objetivo era crear un entorno aislado usando Docker para ejecutar un brazo robótico simulado en PyBullet, de manera que se pudiera visualizar con una interfaz gráfica (GUI) y controlar con el teclado.
La idea es tener todo lo necesario dentro del contenedor —Python, PyBullet y dependencias gráficas— para que el proyecto funcione sin importar el sistema operativo de la máquina.



### 3. Construcción de la imagen
Ahí se especificó todo lo que necesitaba el entorno: una base de Python 3.10, las librerías del sistema necesarias para que PyBullet pueda mostrar gráficos (como libgl1, libxrender1, etc.), y luego se instalaron las dependencias de Python desde un archivo requirements.txt.

La lógica detrás de esto es que Docker actúa como una "caja virtual" donde se puede montar un sistema completo.
Así, si en otro computador no tienes PyBullet, OpenGL o incluso Python, igual puedes ejecutar el mismo proyecto sin problemas de compatibilidad.

Para construir la imagen se ejecutó:


```

docker build -t pybullet-arm:latest .
```


Una parte importante fue permitir que la simulación pudiera verse en una ventana fuera del contenedor.
Docker, por defecto, no tiene acceso a la pantalla del sistema host, así que se configuró el acceso con los siguientes pasos:

```
xhost +local:root
```

### Ejecutar el contenedor

```
docker run --rm -it \
    -e DISPLAY=$DISPLAY \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    --device /dev/dri \
    pybullet-arm:latest
```
### Resultado final

Una vez iniciado el contenedor, PyBullet se lanza en modo GUI mostrando el brazo robótico sobre un plano.
El movimiento de cada articulación se controla con las teclas (Q/A, W/S, E/D, R/F), lo que permite comprobar el correcto funcionamiento del modelo.

Este proceso demuestra cómo Docker facilita la portabilidad del proyecto, ya que el mismo entorno puede ejecutarse en cualquier computadora sin preocuparse por dependencias o configuraciones específicas del sistema operativo.







##  Punto dos, Carrera

En este punto del trabajo, el objetivo fue crear un videojuego sencillo de carreras en Python utilizando Tkinter como interfaz gráfica, y luego empaquetarlo en un contenedor Docker para que se pueda ejecutar fácilmente en cualquier sistema.


### Funcionamiento del juego

El archivo racing_game.py implementa un pequeño juego en 2D hecho con Tkinter, donde el jugador controla un auto en un circuito cerrado.

El auto puede acelerar, frenar y girar con las flechas del teclado.

La pista está dibujada con formas geométricas (óvalos, rectas, curvas) sobre un lienzo (Canvas).

Hay detección de colisiones: si el auto se sale de la pista, rebota o pierde velocidad.

Se llevan estadísticas del juego como vueltas completadas, velocidad actual y mejor tiempo de vuelta.

En la parte superior se muestra una interfaz de usuario (UI) con toda la información en tiempo real.

El resultado es un mini simulador de conducción tipo arcade, completamente hecho con Python puro, sin motores de juego externos.


###  Creación del contenedor con Docker y Construcción de la imagen
El Dockerfile fue la clave para poder ejecutar el juego sin errores en cualquier computadora.
Allí se definió un entorno basado en Python 3.10 con todas las librerías del sistema necesarias para que Tkinter y Pillow (PIL) funcionaran correctamente dentro del contenedor.

Para construir la imagen se ejecutó:


```

docker build -t racing-game:latest .
```

Para que la ventana del juego pudiera mostrarse fuera del contenedor, se configuró el acceso al servidor gráfico del sistema (X11), de manera similar al caso anterior del brazo robótico.
```
xhost +local:root
```

### Ejecutar el contenedor

```
docker run --rm -it \
    -e DISPLAY=$DISPLAY \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    racing-game:latest
```
### Resultado final

Al ejecutar el contenedor, se abre una ventana titulada "🏎️ Juego de Carreras Pro" con un circuito cerrado y un auto rojo en la parte inferior.
El jugador puede usar las flechas del teclado para acelerar, girar y frenar.
El juego muestra los datos en tiempo real:

- Número de vueltas completadas.

- Velocidad actual (en km/h).

- Mejor tiempo de vuelta.

Además, hay efectos visuales como líneas discontinuas, una bandera de meta y una textura verde de césped, lo que le da un aspecto bastante completo para ser un juego hecho únicamente con Tkinter.
