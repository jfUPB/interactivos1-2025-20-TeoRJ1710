# Evidencias de la unidad 4

## Código

[Enlace a la aplicación a modificar](https://editor.p5js.org/generative-design/sketches/P_2_0_03)

Código a modificar:

``` js
// P_2_0_03
//
// Generative Gestaltung – Creative Coding im Web
// ISBN: 978-3-87439-902-9, First Edition, Hermann Schmidt, Mainz, 2018
// Benedikt Groß, Hartmut Bohnacker, Julia Laub, Claudius Lazzeroni
// with contributions by Joey Lee and Niels Poldervaart
// Copyright 2018
//
// http://www.generative-gestaltung.de
//
// Licensed under the Apache License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License.
// You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
// See the License for the specific language governing permissions and
// limitations under the License.

/**
 * drawing with a changing shape by draging the mouse.
 *
 * MOUSE
 * position x          : length
 * position y          : thickness and number of lines
 * drag                : draw
 *
 * KEYS
 * 1-3                 : stroke color
 * del, backspace      : erase
 * s                   : save png
 */
'use strict';

var strokeColor;

function setup() {
  createCanvas(720, 720);
  colorMode(HSB, 360, 100, 100, 100);
  noFill();
  strokeWeight(2);
  strokeColor = color(0, 10);
}

function draw() {
  if (mouseIsPressed && mouseButton == LEFT) {
    push();
    translate(width / 2, height / 2);

    var circleResolution = int(map(mouseY + 100, 0, height, 2, 10));
    var radius = mouseX - width / 2;
    var angle = TAU / circleResolution;

    stroke(strokeColor);

    beginShape();
    for (var i = 0; i <= circleResolution; i++) {
      var x = cos(angle * i) * radius;
      var y = sin(angle * i) * radius;
      vertex(x, y);
    }
    endShape();

    pop();
  }
}

function keyReleased() {
  if (keyCode == DELETE || keyCode == BACKSPACE) background(0, 0, 100);
  if (key == 's' || key == 'S') saveCanvas(gd.timestamp(), 'png');

  if (key == '1') strokeColor = color(0, 10);
  if (key == '2') strokeColor = color(192, 100, 64, 10);
  if (key == '3') strokeColor = color(52, 100, 71, 10);
}

```

[Enlace a la aplicación modificada](https://editor.p5js.org/mateorendon1710/sketches/QJQpIpiqz)

Código modificado:

``` js
'use strict';

let port;
let connectBtn;
let connectionInitialized = false;
let microBitConnected = false;

const STATES = {
  WAIT_MICROBIT_CONNECTION: "WAIT_MICROBIT_CONNECTION",
  RUNNING: "RUNNING",
};
let appState = STATES.WAIT_MICROBIT_CONNECTION;


let microBitX = 0;
let microBitY = 0;
let microBitAState = false;
let microBitBState = false;
let prevA = false;
let prevB = false;


let strokeColor;
let colorIndex = 0;
const colorsHSB = [
  () => color(0, 10),                 
  () => color(192, 100, 64, 10),      
  () => color(52, 100, 71, 10)        
];


function setup() {
  createCanvas(windowWidth, windowHeight);
  colorMode(HSB, 360, 100, 100, 100);
  background(0, 0, 100);
  noFill();
  strokeWeight(2);
  strokeColor = colorsHSB[colorIndex]();

  
  port = createSerial();
  connectBtn = createButton("Connect to micro:bit");
  connectBtn.position(10, 10);
  connectBtn.mousePressed(connectBtnClick);

  
  textFont('monospace');
  textSize(14);
}


function connectBtnClick() {
  if (!port.opened()) {
    port.open("MicroPython", 115200);
    connectionInitialized = false;
  } else {
    port.close();
  }
}

function updateButtonStates(newA, newB) {
  
  if (newA === true && prevA === false) {
    
  }

  
  if (newB === false && prevB === true) {
    colorIndex = (colorIndex + 1) % colorsHSB.length;
    strokeColor = colorsHSB[colorIndex]();
    // print("B released: color changed");
  }

  prevA = newA;
  prevB = newB;
}


function draw() {
  
  if (!port.opened()) {
    connectBtn.html("Connect to micro:bit");
    microBitConnected = false;
  } else {
    microBitConnected = true;
    connectBtn.html("Disconnect");

    if (!connectionInitialized) {
      port.clear(); // limpia buffer al conectar
      connectionInitialized = true;
    }

    if (port.availableBytes() > 0) {
      let data = port.readUntil("\n");
      if (data) {
        data = data.trim();
        let values = data.split(",");
        if (values.length === 4) {
          microBitX = int(values[0]) + windowWidth / 2;
          microBitY = int(values[1]) + windowHeight / 2;
          microBitAState = values[2].toLowerCase() === "true";
          microBitBState = values[3].toLowerCase() === "true";
          updateButtonStates(microBitAState, microBitBState);
        } else {
          
        }
      }
    }
  }
  

  
  switch (appState) {
    case STATES.WAIT_MICROBIT_CONNECTION: {
      if (frameCount % 30 === 0) { // re-dibuja texto cada tanto
        push();
        noStroke();
        fill(0, 0, 100);
        rect(0, 0, 400, 90);
        fill(0, 0, 20);
        text("1) Conecta el micro:bit con el botón\n2) Mantén pulsado A para dibujar\n3) Pulsa B para cambiar color", 14, 50);
        pop();
      }

      if (microBitConnected === true) {
        noCursor();
        appState = STATES.RUNNING;
      }
      break;
    }

    case STATES.RUNNING: {
      
      if (microBitConnected === false) {
        cursor();
        appState = STATES.WAIT_MICROBIT_CONNECTION;
        break;
      }

     
      if (microBitAState === true) {
        push();
        translate(width / 2, height / 2);

        
        let yPix = constrain(microBitY, 0, height);
        let circleResolution = int(map(yPix + 100, 0, height, 2, 10));

        
        let thickness = map(yPix, 0, height, 1, 6);
        strokeWeight(thickness);

        
        let radius = microBitX - width / 2;
        let angle = TAU / max(circleResolution, 2);

        stroke(strokeColor);

        beginShape();
        for (let i = 0; i <= circleResolution; i++) {
          let x = cos(angle * i) * radius;
          let y = sin(angle * i) * radius;
          vertex(x, y);
        }
        endShape();

        pop();
      }
      break;
    }
  }
}


function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

function keyReleased() {

  if (keyCode === DELETE || keyCode === BACKSPACE) background(0, 0, 100);

  
  if (key === 's' || key === 'S') {
    let ts = year() + nf(month(), 2) + nf(day(), 2) + "_" + nf(hour(), 2) + nf(minute(), 2) + nf(second(), 2);
    saveCanvas(ts, 'png');
  }

  
  if (key === '1') { colorIndex = 0; strokeColor = colorsHSB[colorIndex](); }
  if (key === '2') { colorIndex = 1; strokeColor = colorsHSB[colorIndex](); }
  if (key === '3') { colorIndex = 2; strokeColor = colorsHSB[colorIndex](); }
}

```

## Video

[Video demostratativo](URL)


