# Unidad 3


## 🛠 Fase: Apply

### ACTIVITY 06

```
let STATE_CONFIG = 0;
let STATE_ARMED = 1;
let STATE_EXPLODED = 2;

let estado = STATE_CONFIG;
let clave = "AB";
let entrada = "";
let tiempoConfig = 10;
let tiempoInicio = 0;

function setup() {
  createCanvas(400, 200);
  textAlign(CENTER, CENTER);
  textSize(20);
}

function draw() {
  background(220);

  if (estado === STATE_CONFIG) {
    text("CONFIG", width / 2, 40);
    text("Time: " + tiempoConfig + " seg", width / 2, 80);
    text("Press S armed", width / 2, 120);
    text("A = +1 seg   B = -1 seg", width / 2, 160);

  } else if (estado === STATE_ARMED) {
    let tiempoPasado = int((millis() - tiempoInicio) / 1000);
    let tiempoRestante = tiempoConfig - tiempoPasado;
    text("BOMBA ARMED", width / 2, 40);
    text("TIME: " + tiempoRestante, width / 2, 80);
    text("PASSWORD FOR DEACTIVATE", width / 2, 120);

    if (tiempoRestante <= 0) {
      estado = STATE_EXPLODED;
    }

  } else if (estado === STATE_EXPLODED) {
    text("YOU ARE DEAD", width / 2, height / 2);
    text("PRESS T FOR RESTART", width / 2, height / 2 + 40);
  }
}

function keyPressed() {
  if (estado === STATE_CONFIG) {
    if (key === 'S') {
      estado = STATE_ARMED;
      tiempoInicio = millis();
      entrada = "";
    } else if (key === 'A') {
      tiempoConfig += 1;
    } else if (key === 'B') {
      tiempoConfig -= 1;
      if (tiempoConfig < 1) tiempoConfig = 1; 
    }

  } else if (estado === STATE_ARMED) {
    entrada += key;
    if (entrada === clave) {
      estado = STATE_CONFIG; 
    }
    if (key === 'T') {
      estado = STATE_EXPLODED;
    }

  } else if (estado === STATE_EXPLODED) {
    if (key === 'T') {
      estado = STATE_CONFIG;
    }
  }
}
```
### ACTIVIDAD 07

enlace p5
https://editor.p5js.org/mateorendon1710/sketches/AA1rIpBPA


codigo del p5.js


```
let STATE_CONFIG = 0;
let STATE_ARMED = 1;
let STATE_EXPLODED = 2;

let estado = STATE_CONFIG;
let clave = "AB";
let entrada = "";
let tiempoConfig = 10;
let tiempoInicio = 0;

let port; 
let connectionInitialized = false;

function setup() {
  createCanvas(400, 200);
  textAlign(CENTER, CENTER);
  textSize(20);

  
  port = createSerial();
  
  
  let connectBtn = createButton("Conectar micro:bit");
  connectBtn.position(10, height + 10);
  connectBtn.mousePressed(connectBtnClick);
}

function draw() {
  background(220);

  
  if (port.available() > 0) {
    let dato = port.read();
    if (dato) {
      manejarEntrada(dato);
    }
  }

  if (estado === STATE_CONFIG) {
    text("CONFIG", width / 2, 40);
    text("Time: " + tiempoConfig + " seg", width / 2, 80);
    text("Press S armed", width / 2, 120);
    text("A = +1 seg   B = -1 seg", width / 2, 160);

  } else if (estado === STATE_ARMED) {
    let tiempoPasado = int((millis() - tiempoInicio) / 1000);
    let tiempoRestante = tiempoConfig - tiempoPasado;
    text("BOMBA ARMED", width / 2, 40);
    text("TIME: " + tiempoRestante, width / 2, 80);
    text("PASSWORD FOR DEACTIVATE", width / 2, 120);

    if (tiempoRestante <= 0) {
      estado = STATE_EXPLODED;
    }

  } else if (estado === STATE_EXPLODED) {
    text("YOU ARE DEAD", width / 2, height / 2);
    text("PRESS T FOR RESTART", width / 2, height / 2 + 40);
  }
}

function keyPressed() {
  manejarEntrada(key); 
}


function manejarEntrada(tecla) {
  if (estado === STATE_CONFIG) {
    if (tecla === 'S') {
      estado = STATE_ARMED;
      tiempoInicio = millis();
      entrada = "";
    } else if (tecla === 'A') {
      tiempoConfig += 1;
    } else if (tecla === 'B') {
      tiempoConfig -= 1;
      if (tiempoConfig < 1) tiempoConfig = 1; 
    }

  } else if (estado === STATE_ARMED) {
    entrada += tecla;
    if (entrada === clave) {
      estado = STATE_CONFIG; 
    }
    if (tecla === 'T') {
      estado = STATE_EXPLODED;
    }

  } else if (estado === STATE_EXPLODED) {
    if (tecla === 'T') {
      estado = STATE_CONFIG;
    }
  }
}


function connectBtnClick() {
  if (!port.opened()) {
    port.open("MicroPython", 115200);
    connectionInitialized = false;
  } else {
    port.close();
  }
}
```


codigo del microbit

````
from microbit import *

while True:
    if button_a.is_pressed() and not button_b.is_pressed():
        uart.write("A\n")
        sleep(300)  # Pequeña pausa para evitar repeticiones rápidas

    elif button_b.is_pressed() and not button_a.is_pressed():
        uart.write("B\n")
        sleep(300)

    elif button_a.is_pressed() and button_b.is_pressed():
        uart.write("S\n")
        sleep(500)

    if accelerometer.was_gesture("shake"):
        uart.write("T\n")
        sleep(500)
````
