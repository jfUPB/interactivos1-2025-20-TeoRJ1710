
# Evidencias de la unidad 8

## Actividad 01

🧐🧪✍️ Reporta en tu bitácora

1: Documenta los referentes visuales que te inspiren.

R: escoji una cancion que se llama "shape of you - Ed Sheran" que tiene un ritmo marcado 
y caracteristico que casi todo el mundo econoce o ha escuchado alguna vez lo cual no seria tan dificil porque el ritmo se marca en toda la cancion

2: Define el concepto de las visuales que quieres crear.

R: Quiero crear un tipo de visual tipo el que aparece en esos canales de youtube que muestran una cancion pero que aunque sea una cancion el ritmo se hace adictivo ya que ellos hacen es que en el centro hay como una bola casi siemore con el nombre o el logo del canal y que en su borde o pues el borde la circunferencia salen distintos tipos de ondas que van siguiendo el ritmo de la cancion lo cual lo hace interesante de ver.
 
3: Explica cómo el móvil y el micro:bit controlarán las visuales.

R: quisiera que el movil lo que hiciera es que controlara las visuales de forma que de inicio a la cancion y el microbit controle la intensidad del efecto que se ve o que cambie el color del fondo, peo yo lo que quiero es que las ondas vayan de acuerdo a la cancion y que no las controle un boton o algo por el estilo.

4: Haz un bocetos de todas las interfaces del sistema.

<img width="1154" height="647" alt="image" src="https://github.com/user-attachments/assets/f738e843-6ac4-4ab6-99d3-abbd0f4c1a56" />


5: Haz un diagrama que explique cómo se comunicarán los diferentes componentes del sistema.

<img width="799" height="439" alt="image" src="https://github.com/user-attachments/assets/efc9ff2d-795d-4215-b510-883bd61ace19" />

## ACTIVIDAD 02

🧐🧪✍️ Reporta en tu bitácora

Documenta todo el proceso de construcción.

R: primero lo primero partiendo ya del diseño y la idea del proyecto empece primero preguntandole a la ia si el diseño y todo estaba bien para poder proseguir, la IA respondio positivamente apoyando la idea

Ahora empezamos primero por el cerebro de todo el visualizador de p5.js, le pedi a la ia que me ayudara con los codigos y empezamos con el index y con el skecth


Incluye todos los códigos: servidor, cliente móvil, cliente de escritorio y micro:bit.

server
```
/* =========================================
   CODIGO DEL SERVIDOR (server.js) - v3
   Conexión con Puerto COM Específico
   ========================================= */

const express = require('express');
const http = require('http');
const path = require('path');
const socketIO = require('socket.io');
const { SerialPort, ReadlineParser } = require('serialport'); // Mantenemos nuestro 'require'

const app = express();
const server = http.createServer(app);
const io = socketIO(server);

const port = 3000;
app.use(express.static(path.join(__dirname, 'public')));

// --- SECCIÓN DEL MICRO:BIT (MÉTODO DIRECTO) ---

// ⚠️ ¡CAMBIA "COM5" por el puerto que encontraste en el Paso 1!
const SERIAL_PORT_PATH = "COM5"; 

try {
  const serial = new SerialPort({
    path: SERIAL_PORT_PATH,
    baudRate: 115200 // Tasa de baudios de MakeCode/Python
  });

  const parser = serial.pipe(new ReadlineParser({ delimiter: '\r\n' }));

  serial.on("open", () => console.log(`✅ Puerto serial ${SERIAL_PORT_PATH} abierto con micro:bit`));
  serial.on("error", (err) => console.error(`❌ Error puerto serial ${SERIAL_PORT_PATH}:`, err.message));

  // Escucha los datos que llegan del micro:bit
  parser.on('data', (data) => {
    const msg = data.trim(); // data será "A" o "B"
    console.log('📡 Datos del micro:bit:', msg);
    
    // Retransmite el dato a TODOS los clientes (al p5.js)
    io.emit('accion-microbit', msg);
  });

} catch (err) {
  console.error(`⚠️ No se pudo abrir el puerto ${SERIAL_PORT_PATH}:`, err.message);
  console.warn("Verifica que el COM es correcto y que el micro:bit está conectado.");
}

// --- FIN SECCIÓN MICRO:BIT ---


// --- SECCIÓN DE SOCKET.IO ---
io.on('connection', (socket) => {
    console.log('✅ Un cliente se ha conectado:', socket.id);

    // Escucha el evento 'control-musica' que viene del móvil
    socket.on('control-musica', (data) => {
        console.log('📲 Mensaje de "control-musica" recibido:', data);
        socket.broadcast.emit('control-musica', data);
    });
    
    socket.on('disconnect', () => {
        console.log('❌ Un cliente se ha desconectado:', socket.id);
    });
});

// Inicia el servidor
server.listen(port, () => {
    console.log(`🚀 Servidor corriendo en http://localhost:${port}`);
    console.log('Abre el MÓVIL en http://localhost:3000/mobile/');
    console.log('Abre el DESKTOP en http://localhost:3000/desktop/');
});
```

dekstop
```
// 1. Conexión con el servidor
const socket = io();
console.log("Visualizador Desktop conectado al servidor");

let song;
let fft;
let spectrum;

let logoDiameter = 180;
let vizColor;
let vizIntensity = 1.0; // --- NUEVA VARIABLE (1.0 = 100% de intensidad)

// --- 1. PRE-CARGA ---
function preload() {
  song = loadSound('Shape of You.mp3');
  print("Canción cargada.");
}

// --- 2. CONFIGURACIÓN INICIAL ---
function setup() {
  createCanvas(windowWidth, windowHeight);
  angleMode(DEGREES);
  vizColor = color(255, 0, 255); // Color inicial
  fft = new p5.FFT(0.8, 128);
  
  // Escucha el control de música (Móvil)
  socket.on('control-musica', (accion) => {
    console.log('Recibido:', accion);
    if (accion === 'play') {
      if (getAudioContext().state === 'running' && song.isLoaded() && !song.isPlaying()) {
        song.loop();
      }
    } else if (accion === 'pause') {
      if (song.isPlaying()) {
        song.pause();
      }
    }
  });
  
  // --- SECCIÓN ACTUALIZADA ---
  // Escucha las acciones del micro:bit
  socket.on('accion-microbit', (data) => {
    print("Recibido del microbit:", data);
    
    if(data.includes('A')){ // Usamos .includes() por si acaso
        // Botón A: Cambia a un color aleatorio
        vizColor = color(random(100, 255), random(100, 255), random(100, 255));
    }
    
    if(data.includes('B')){ // Usamos .includes() por si acaso
        // Botón B: Cambia la intensidad (cicla entre 0.5, 1.0, 1.5)
        if (vizIntensity === 1.0) {
          vizIntensity = 1.5;
        } else if (vizIntensity === 1.5) {
          vizIntensity = 0.5;
        } else {
          vizIntensity = 1.0;
        }
        print("Nueva intensidad:", vizIntensity);
    }
  });
  // --- FIN DE SECCIÓN ---
}

// --- 3. BUCLE DE DIBUJO ---
function draw() {
  background(10, 10, 25);
  spectrum = fft.analyze();
  translate(width / 2, height / 2);

  // 1. Dibuja el "logo" central
  fill(255); noStroke();
  ellipse(0, 0, logoDiameter, logoDiameter);
  
  // 2. Dibuja el texto (PLAY o PAUSE)
  fill(0); textSize(25); textAlign(CENTER, CENTER);
  if (song.isPlaying()) {
    text('SONANDO', 0, 0);
  } else {
    text('PAUSADO', 0, 0);
    if (getAudioContext().state !== 'running') {
      fill(150); textSize(15);
      text('(Haz clic aquí para activar el audio)', 0, 30);
    }
  }

  // 3. Dibuja el visualizador
  noFill();
  stroke(vizColor); // Color controlado por micro:bit
  strokeWeight(3);

  beginShape();
  for (let i = 0; i < spectrum.length; i++) {
    let amplitude = spectrum[i];
    
    // --- CÁLCULO ACTUALIZADO ---
    // Usamos 'vizIntensity' para escalar el tamaño de las ondas
    let baseRadius = logoDiameter / 2 + 15;
    let maxAmplitude = 350; // El tamaño máximo original
    
    // Mapeamos la amplitud y la multiplicamos por la intensidad
    let amplitudeEffect = map(amplitude, 0, 255, 0, maxAmplitude - baseRadius);
    let r = baseRadius + (amplitudeEffect * vizIntensity); // ¡Aquí se usa la intensidad!
    // --- FIN DEL CÁLCULO ---

    let angle = map(i, 0, spectrum.length, 0, 360);
    let x = r * cos(angle);
    let y = r * sin(angle);
    vertex(x, y);
  }
  endShape(CLOSE);
}

// --- 4. HABILITAR AUDIO ---
function mousePressed() {
  if (getAudioContext().state !== 'running') {
    userStartAudio();
    print("Contexto de audio iniciado por el usuario.");
  }
}
function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}
```

mobile
```
// 1. Conexión con el servidor
const socket = io();
console.log("Controlador Móvil conectado al servidor");

// 2. Obtener los botones del HTML
const playButton = document.getElementById('playBtn');
const pauseButton = document.getElementById('pauseBtn');

// 3. Enviar mensajes al hacer clic
playButton.addEventListener('click', () => {
  console.log('Enviando: play');
  // Envía un evento 'control-musica' con el valor 'play'
  socket.emit('control-musica', 'play');
});

pauseButton.addEventListener('click', () => {
  console.log('Enviando: pause');
  // Envía un evento 'control-musica' con el valor 'pause'
  socket.emit('control-musica', 'pause');
});
```

microbit
```
from microbit import *

# Este bucle se ejecuta para siempre
while True:
    
    # Si el botón A fue presionado
    if button_a.was_pressed():
        try:
            print("A")  # Envía el texto "A" por el USB
            display.show("A")
            sleep(500) # Espera medio segundo (500 ms)
            display.clear()
        except OSError:
            pass # Si hay un error de USB, lo ignora y sigue

    # Si el botón B fue presionado
    if button_b.was_pressed():
        try:
            print("B")  # Envía el texto "B" por el USB
            display.show("B")
            sleep(500) # Espera medio segundo (500 ms)
            display.clear()
        except OSError:
            pass # Si hay un error de USB, lo ignora y sigue

    sleep(20) # Pequeña pausa
```
