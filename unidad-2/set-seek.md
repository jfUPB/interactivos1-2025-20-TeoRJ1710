# Unidad 2

## 🔎 Fase: Set + Seek

### ACTIVIDAD 01

Describe detalladamente cómo funciona este ejemplo.

R: 

Este programa crea dos pixeles parpadeantes en la pantalla de la Microbituno en la esquina superior izquierda que cambia su estado otro en la esquina inferior derecha que cambia el cambio consiste en alternar entre encendido y apagado con base en un temporizador interno.

¿Cuáles son los estados en el programa?

R:

La máquina de estados está dentro del método update de la clase Pixel. Solo hay dos estados posibles:

1: Init estado inicial, solo se ejecuta una vez por pixel.

2: WaitTimeout estado que verifica si ha pasado el tiempo necesario para cambiar el estado del píxel

¿Cuáles son los eventos/inputs en el programa?

R:
````
utime.ticks_diff(utime.ticks_ms(), self.startTime) > self.interval
````
un evento posible puede ser este


¿Cuáles son las acciones en el programa?

R:

Las acciones son lo que ocurre dentro de los estados, es decir, los efectos visibles:

En el estado Init se guarda el tiempo actual (startTime) despues se cambia al estado "WaitTimeout" y despues se enciende el píxel

En el estado "WaitTimeout":

Se verifica si ha pasado el tiempo (interval). Si sí, se reinicia el temporizador, alterna el estado del pixely se actualiza la pantalla.

### ACTVIDAD 02

R:
````
from microbit import *
import utime

class Semaforo:
    def __init__(self):
        self.state = "Rojo"
        self.startTime = utime.ticks_ms()

    def update(self):
        tiempo_actual = utime.ticks_ms()
        tiempo_pasado = utime.ticks_diff(tiempo_actual, self.startTime)

        if self.state == "Rojo":
            self.mostrar_rojo()
            if tiempo_pasado >= 3000:  
                self.state = "Verde"
                self.startTime = tiempo_actual

        elif self.state == "Verde":
            self.mostrar_verde()
            if tiempo_pasado >= 3000: 
                self.state = "Amarillo"
                self.startTime = tiempo_actual

        elif self.state == "Amarillo":
            self.mostrar_amarillo()
            if tiempo_pasado >= 1000:  
                self.state = "Rojo"
                self.startTime = tiempo_actual

    def mostrar_rojo(self):
        display.clear()
        display.set_pixel(0, 0, 9)

    def mostrar_amarillo(self):
        display.clear()
        display.set_pixel(1, 0, 9)

    def mostrar_verde(self):
        display.clear()
        display.set_pixel(2, 0, 9)

semaforo = Semaforo()

while True:
    semaforo.update()
````

Los estados están definidos en la variable self.state y son:

"Rojo" estado en el que el semáforo muestra el color rojo.

"Verde" estado en el que se muestra el verde.

"Amarillo" estado en el que se muestra el amarillo.


Los eventos son el paso del tiempo, verificado con:

````
utime.ticks_diff(utime.ticks_ms(), self.startTime)
````
Cada estado cambia cuando se cumple un tiempo determinado:
Rojo → Verde: después de 3 segundos.

Verde → Amarillo: después de 3 segundos.

Amarillo → Rojo: después de 1 segundo.


### ACTVIDAD 03

1: Explica por qué decimos que este programa permite realizar de manera concurrente varias tareas.

R: 

Temporización no bloqueante: Usa utime.ticks_diff() para contar el tiempo sin detener el flujo del programa, lo que permite hacer otras verificaciones mientras tanto.


Manejo simultáneo de eventos: Dentro del mismo ciclo, puede:

Detectar si ha pasado el tiempo (evento de temporizador).

Detectar si se presionó el botón A (evento externo).

2: Identifica los estados, eventos y acciones en el programa.

R:

1: Los estados son valores de la variable current_state:

- STATE_INIT (0): estado inicial, muestra HAPPY y pasa a STATE_HAPPY.

- STATE_HAPPY (1): muestra la cara feliz.

- STATE_SMILE (2): muestra la sonrisa.

2: Los eventos

- Presión del botón A (button_a.was_pressed())

- Tiempo transcurrido (utime.ticks_diff(...) > interval)

3: 

- Mostrar una imagen con display.sho

- Actualizar el tiempo de inicio con start_time = utime.ticks_ms

- Cambiar el estado current_state

- Actualizar el valor del intervalo interval 


3: Describe y aplica al menos 3 vectores de prueba para el programa. Para definir un vector de prueba debes llevar al sistema a un estado, generar los eventos y observar el estado siguiente y las acciones que ocurrirán. Por tanto, un vector de prueba tiene unas condiciones iniciales del sistema, unos resultados esperados y los resultados realmente obtenidos. Si el resultado obtenido es igual al esperado entonces el sistema pasó el vector de prueba, de lo contrario el sistema puede tener un error.

R:

1: STATE_INIT	Inicia el ciclo	y se espera STATE_HAPPY	 Mostrar HAPPY	

2: STATE_HAPPY	Botón A presionado	y se espera STATE_SAD	Mostrar SAD, timer = 2000	

3: STATE_SMILE	Pasan 1000 ms	y se espera STATE_SAD	Mostrar SAD, timer = 2000


