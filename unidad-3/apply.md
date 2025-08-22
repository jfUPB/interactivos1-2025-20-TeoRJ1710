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
