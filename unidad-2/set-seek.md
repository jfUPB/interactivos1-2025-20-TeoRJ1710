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

