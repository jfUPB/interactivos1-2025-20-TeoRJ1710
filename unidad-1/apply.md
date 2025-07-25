# Unidad 1

## 🛠 Fase: Apply

### ACTIVIDAD 05

EXPLICA COMO FUNCIONA EL SISTEMA QUE ACABAMOS DE CREAR ?
R: primero se abre la aplicacion del microbit para implementar el codigo en el microbit de forma que si presionamos la tecla A el microbit reaccione a eso en el serial, despues poniendo el codigo en el p5 que nos explico el profesor el programa hace que al reconocer el p5 al presionar la tecla A cambie el cuadro a color rojo mientras se mantiene presionado y si no se esta presionado se  muestre el color verde

### ACTIVIDAD 06
Escribe el enlace a tu programa en el editor de p5.js.

https://editor.p5js.org/mateorendon1710/sketches/t39IWzrpY

Copia el código de tu programa en la bitácora (recuerda insertarlo usando markdown y el lenguaje javascript).

p5:

``` js
let port;
let connectBtn;
let connectionInitialized = false;

let circleX = 200; 

function setup() {
  createCanvas(400, 400);
  background(220);

  
  port = createSerial();
  connectBtn = createButton("Connect to micro:bit");
  connectBtn.position(80, 300);
  connectBtn.mousePressed(connectBtnClick);
}

function draw() {
  background(220);

  
  if (port.opened() && !connectionInitialized) {
    port.clear(); // Limpiar buffer serial
    connectionInitialized = true;
  }

  if (port.availableBytes() > 0) {
    let dataRx = port.read(1); 
    if (dataRx === "A") {
      circleX -= 10; 
    } else if (dataRx === "B") {
      circleX += 10; 
    }

    circleX = constrain(circleX, 25, width - 25);
  }

  // Dibujar el círculo
  fill("blue");
  noStroke();
  circle(circleX, height / 2, 50);

  // Actualizar el texto del botón
  if (!port.opened()) {
    connectBtn.html("Connect to micro:bit");
  } else {
    connectBtn.html("Disconnect");
  }
}

function connectBtnClick() {
  if (!port.opened()) {
    port.open("MicroPython", 115200); // Abrir conexión
    connectionInitialized = false;
  } else {
    port.close(); // Cerrar conexión
  }
}
```

Copia el código del micro:bit en la bitácora (recuerda insertarlo usando markdown y el lenguaje python).

microbit:

``` py
from microbit import *

while True:

    if button_a.is_pressed():
        uart.write('A')
    if button_b.was_pressed():
        uart.write("B")

    sleep(100)
```

