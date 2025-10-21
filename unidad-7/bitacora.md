
# Evidencias de la unidad 7


## Actividad 01 

1: ¿Qué URL de Dev Tunnels obtuviste? ¿Por qué crees que necesitamos usar esta URL en lugar de [http://localhost:3000](http://localhost:3000) o la IP local de tu computador para que el celular se conecte?

La URL que generé fue del tipo https://[...].use2.devtunnels.ms/. La razón fundamental para usar esta URL es que actúa como un puente público hacia un servicio privado. localhost es una dirección que solo mi computadora entiende (es como decir "yo mismo"), y la IP local (ej. 192.168.1.5) solo es visible para dispositivos dentro de mi misma red Wi-Fi. Mi celular, al usar datos móviles o incluso a veces la misma red Wi-Fi, no puede "ver" ese servicio local. Dev Tunnels expone mi servidor local a la internet global a través de una dirección pública y segura, permitiendo que cualquier dispositivo, en cualquier lugar, pueda encontrarlo y conectarse.

2: Describe brevemente qué hace npm install y npm start.

npm install: Este comando actúa como un administrador de suministros para el proyecto. Lee el archivo package.json para ver la lista de herramientas (dependencias) que el servidor necesita para funcionar, como express y socket.io, y las descarga de internet a una carpeta local llamada node_modules.

npm start: Este es un comando de atajo, definido en package.json, que inicia la ejecución del script principal del servidor (usualmente node server.js). Esencialmente, es el comando que "enciende" nuestro servidor para que empiece a escuchar conexiones.


3: ¿Qué mensajes observaste en la terminal del servidor al conectar el cliente de escritorio y el cliente móvil? ¿Eran diferentes los mensajes o identificadores?
En la terminal se mostraron mensajes como:

La terminal del servidor mostró un registro de eventos en tiempo real. Al conectar cada dispositivo, apareció un mensaje como A client has connected: seguido de un identificador alfanumérico único (el socket.id). Este ID era diferente para el escritorio y para el móvil, lo que confirma que el servidor los reconoce como dos clientes distintos. Posteriormente, solo cuando movía el dedo en el móvil, aparecían los mensajes con los datos de las coordenadas, confirmando que la comunicación fluía correctamente.

4: Describe el comportamiento observado: ¿Funcionó la interacción? ¿Hubo algún retraso (latencia)?
Sí, la interacción funcionó correctamente. Al mover el dedo en el celular, el círculo rojo en el computador se movía al mismo tiempo. Hubo una leve latencia, pero mínima y no afectó la experiencia.

La interacción fue exitosa y la sincronización entre dispositivos fue casi perfecta. El movimiento del círculo en la pantalla del escritorio reflejaba de manera fluida y responsiva el trazado de mi dedo en el móvil. Si bien existe una latencia técnica inherente al pasar por internet, esta fue prácticamente imperceptible, lo que resultó en una experiencia de usuario muy directa e intuitiva.


## Actividad 02 

1: Explica con tus propias palabras: ¿Por qué es necesario Dev Tunnels en este escenario y cómo funciona conceptualmente?

Dev Tunnels es indispensable aquí porque rompe la barrera entre mi red local y la internet pública. Mi servidor Node.js opera en un entorno cerrado y privado (localhost). Para que mi celular pueda comunicarse con él desde fuera, necesita una "dirección postal" pública. Conceptualmente, Dev Tunnels crea un conducto encriptado desde una URL pública y amigable hasta el puerto específico de mi máquina donde se ejecuta el servidor. Todo el tráfico que llega a esa URL pública es redirigido de forma segura a través del túnel hasta mi servidor local, y viceversa.

2: Describe la función de touchMoved() y por qué se usa la variable threshold en el cliente móvil.

touchMoved(): Es una función de p5.js que actúa como un detector de eventos. Se activa automáticamente y de forma continua cada vez que el sistema operativo del móvil registra un movimiento del dedo sobre la pantalla táctil. Su propósito en este proyecto es capturar las coordenadas (x, y) de ese toque en tiempo real.

threshold: Esta variable funciona como un filtro de sensibilidad. Sin ella, touchMoved() enviaría datos al servidor con cada mínimo píxel de movimiento, generando una avalancha de mensajes (lo que se conoce como "spam de red"). El threshold establece una distancia mínima que el dedo debe recorrer antes de que se considere un movimiento "significativo" y se envíe un nuevo paquete de datos. Es una optimización crucial para garantizar que la comunicación sea eficiente y no sobrecargue la red.

3: Compara brevemente Dev Tunnels con simplemente usar la IP local. ¿Cuáles son las ventajas y desventajas de cada uno?

Usar la IP local solo funciona si ambos dispositivos están en la misma red.
Dev Tunnels es mucho mas seguro y permite la conexion desde cual lugar del mundo.

4: Capturas de pantalla:
<img width="630" height="563" alt="image" src="https://github.com/user-attachments/assets/dd2a8afe-cc5a-42d4-ab05-a38728d992b4" />

<img width="643" height="633" alt="image" src="https://github.com/user-attachments/assets/79e7ff33-bf5b-4542-bc04-f4fab5467373" />





## Actividad 03 

1: ¿Cuál es la función principal de express.static('public') en este servidor? ¿Cómo se compara con el uso de app.get('/ruta', …)?

La función de express.static('public') es designar la carpeta public como un directorio de archivos estáticos. Esto significa que Express servirá automáticamente cualquier archivo dentro de esa carpeta (como index.html, sketch.js, style.css) si un navegador lo solicita. Es como abrir una "ventana de autoservicio". En cambio, app.get('/ruta', ...) requiere definir manualmente una ruta específica para cada recurso que quieras servir, lo cual es útil para lógica dinámica pero ineficiente para servir archivos simples. express.static es la solución ideal para servir la parte visible (frontend) de una aplicación web.


2: Explica detalladamente el flujo de un mensaje táctil.

El flujo es un ciclo de eventos bien definido:

Emisión (Móvil): El cliente móvil detecta un gesto táctil y emite un evento con la etiqueta 'message' a través de su conexión de socket. El contenido de este evento (el payload) es un objeto JSON con las coordenadas.
Recepción (Servidor): El servidor, que está escuchando permanentemente, recibe el evento 'message' de ese cliente específico.
Retransmisión (Servidor): Al recibirlo, el servidor ejecuta el comando socket.broadcast.emit('message', message). broadcast es la instrucción clave aquí: significa "enviar este mensaje a todos los clientes conectados, excepto al que me lo acaba de enviar".
Recepción (Escritorio): El cliente de escritorio, que también está escuchando el evento 'message', lo recibe y ejecuta la función asociada, que es usar las coordenadas del payload para redibujar el círculo en la pantalla.

3: Si conectaras dos computadores de escritorio y un móvil, y movieras el dedo en el móvil, ¿Quién recibiría el mensaje retransmitido por el servidor? ¿Por qué?

Los dos computadores de escritorio recibirían el mensaje simultáneamente. La razón es el uso del método socket.broadcast.emit(). Este método está diseñado específicamente para reenviar un mensaje a todos los sockets conectados al servidor, excluyendo al socket original que envió el mensaje. Como el móvil fue el emisor, queda fuera de la retransmisión, y los dos escritorios, al ser los otros clientes conectados, son los destinatarios.

4: ¿Qué información útil te proporcionan los mensajes console.log en el servidor durante la ejecución?

Los console.log en el servidor son una herramienta de depuración y monitoreo fundamental. Nos proporcionan una "bitácora en vivo" de la actividad del servidor. Gracias a ellos podemos:
Confirmar conexiones: Ver en tiempo real cuándo se conecta y desconecta un cliente.
Inspeccionar datos: Verificar la estructura y el contenido de los mensajes que se están transmitiendo.
Diagnosticar problemas: Si algo no funciona, estos logs son la primera pista para saber en qué punto del flujo de datos se está produciendo el error.



## Actividad 04 

<img width="1393" height="227" alt="image" src="https://github.com/user-attachments/assets/30e10cbf-7b58-46d1-8dfa-da3f84f36871" />


El usuario toca la pantalla del móvil → touchMoved() detecta el movimiento, despues el móvil envía las coordenadas (x, y) al servidor con socket.emit('message'), luego el servidor Node.js recibe el mensaje → socket.on('message'), despues el servidor retransmite los datos a los demás clientes (escritorio) con socket.broadcast.emit('message') y por ultimo el escritorio recibe los datos → actualiza la posición del círculo rojo en el canvas de p5.js.

## Actividad 05

Bueno el diseño que estoy pensando hacer y que en cierta parte me recomendo la ia es este:

Sintetizador de Ondas Reactivas
La idea es crear un visualizador musical donde el escritorio muestra una serie de ondas o partículas que reaccionan a una "melodía" imaginaria controlada desde el celular. El móvil se convierte en un panel de control táctil (como un sintetizador simple) para manipular el comportamiento de las visuales en tiempo real.


server.js

```
// ----------------------------------------------------
// Servidor para Visuales Musicales Interactivas
// ----------------------------------------------------

const express = require('express');
const http = require('http');
const socketIO = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = socketIO(server);

const PORT = 3000;

// Servir los archivos de la carpeta 'public'
app.use(express.static('public'));

// Eventos de Socket.IO
io.on('connection', (socket) => {
  console.log('✨ Nuevo cliente conectado:', socket.id);

  // Cuando se recibe data del control (móvil)...
  socket.on('control-data', (data) => {
    // ...se retransmite a todos los demás clientes (el escritorio).
    socket.broadcast.emit('visual-data', data);
  });

  socket.on('disconnect', () => {
    console.log('🔌 Cliente desconectado:', socket.id);
  });
});

server.listen(PORT, () => {
  console.log(`🚀 Servidor escuchando en el puerto ${PORT}`);
});
```


sketch.js mobile

```
// ----------------------------------------------------
// Cliente Móvil: Panel de Control Táctil
// ----------------------------------------------------

// Reemplaza esta URL con la tuya de Dev Tunnels
const SERVER_URL = 'https://TU_URL.use2.devtunnels.ms/';
let socket;

function setup() {
  createCanvas(windowWidth, windowHeight);
  
  // Conectar al servidor
  socket = io.connect(SERVER_URL, {
    reconnection: true
  });

  socket.on('connect', () => {
    console.log('Conectado al servidor!');
  });
  
  background(30);
  fill(255);
  textAlign(CENTER, CENTER);
  textSize(24);
  text('Mueve el dedo para controlar las visuales', width / 2, height / 2);
}

// Envía datos cuando se mueve el dedo
function touchMoved() {
  // Normalizar las coordenadas (valores entre 0 y 1)
  const data = {
    x: touchX / width,
    y: touchY / height,
    touched: false // No es un toque nuevo, es un movimiento
  };
  socket.emit('control-data', data);
  
  // Evita el comportamiento por defecto del navegador
  return false;
}

// Envía un evento de "beat" cuando se toca la pantalla
function touchStarted() {
  background(30, 200, 30); // Feedback visual
  setTimeout(() => background(30), 100);

  const data = {
    x: touchX / width,
    y: touchY / height,
    touched: true // ¡Es un toque nuevo!
  };
  socket.emit('control-data', data);
  
  return false;
}

```

sketch.js desktop
```
// ----------------------------------------------------
// Cliente Escritorio: Visualizador de Ondas
// ----------------------------------------------------

const SERVER_URL = 'https://TU_URL.use2.devtunnels.ms/';
let socket;

// Variables para las visuales
let colorPrincipal;
let frecuencia = 0.02;
let tiempo = 0;
let particulas = [];

function setup() {
  createCanvas(windowWidth, windowHeight);
  colorPrincipal = color(0, 150, 255); // Color inicial

  // Conectar al servidor
  socket = io.connect(SERVER_URL);

  // Escuchar los datos que vienen del servidor
  socket.on('visual-data', (data) => {
    // Mapear la posición X (0 a 1) a un color entre azul y fucsia
    let colorAzul = color(0, 150, 255);
    let colorFucsia = color(255, 0, 150);
    colorPrincipal = lerpColor(colorAzul, colorFucsia, data.x);

    // Mapear la posición Y (0 a 1) a la frecuencia de la onda
    frecuencia = map(data.y, 0, 1, 0.01, 0.09);

    // Si hubo un toque, crear una explosión de partículas
    if (data.touched) {
      crearExplosion();
    }
  });
}

function draw() {
  background(10, 20); // Fondo oscuro con un leve rastro

  // --- DIBUJAR LA ONDA ---
  beginShape();
  stroke(colorPrincipal);
  strokeWeight(4);
  noFill();
  for (let x = 0; x < width; x += 5) {
    // Usar ruido Perlin para una onda orgánica
    let y = map(noise(x * frecuencia, tiempo), 0, 1, height * 0.2, height * 0.8);
    vertex(x, y);
  }
  endShape();

  tiempo += 0.01; // Hacer que la onda se mueva en el tiempo

  // --- DIBUJAR Y ACTUALIZAR PARTÍCULAS ---
  for (let i = particulas.length - 1; i >= 0; i--) {
    particulas[i].update();
    particulas[i].show();
    if (particulas[i].isFinished()) {
      particulas.splice(i, 1);
    }
  }
}

function crearExplosion() {
  for (let i = 0; i < 100; i++) {
    particulas.push(new Particula(width / 2, height / 2, colorPrincipal));
  }
}

// --- CLASE PARTÍCULA ---
class Particula {
  constructor(x, y, col) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(random(2, 6));
    this.lifespan = 255;
    this.col = col;
  }

  isFinished() {
    return this.lifespan < 0;
  }

  update() {
    this.pos.add(this.vel);
    this.vel.mult(0.98); // Fricción
    this.lifespan -= 4;
  }

  show() {
    noStroke();
    fill(red(this.col), green(this.col), blue(this.col), this.lifespan);
    ellipse(this.pos.x, this.pos.y, 8, 8);
  }
}
```

## Autoevaluacion uniudad

Pienso que la nota pude ser 5 o 4 porque la ver es que algunas actividades pueden estar medio incompletas pero de resto creo que esta todo bien
