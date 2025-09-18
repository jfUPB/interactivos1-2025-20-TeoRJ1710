
# Evidencias de la unidad 5

### ACTIVIDAD 02

1. Primer experimento (datos binarios vistos como texto)

🧐🧪✍️ Captura el resultado del experimento anterior. ¿Por qué se ve este resultado?

Se ve como una secuencia de símbolos raros, caracteres ilegibles o “basura” porque el micro:bit está enviando datos en formato binario.
La aplicación SerialTerminal está tratando de interpretar esos bytes como si fueran texto ASCII, pero realmente cada número (xValue, yValue, aState, bState) está empaquetado en binario con struct.pack.
Como no corresponden a letras, aparecen símbolos extraños.



2. Segundo experimento (datos en formato Hex)

🧐🧪✍️ Captura el resultado del experimento anterior. Lo que ves ¿Cómo está relacionado con esta línea de código?
````
data = struct.pack('>2h2B', xValue, yValue, int(aState), int(bState))
````

En formato Hex, ahora sí se ven los bytes reales enviados.
Cada mensaje ocupa 6 bytes:
2h → dos enteros cortos (2 bytes cada uno = 4 bytes en total).
2B → dos enteros sin signo de 1 byte cada uno = 2 bytes en total.
Lo que aparece en Hex corresponde exactamente a esos valores binarios, solo que mostrados en base 16 en lugar de texto.


3. Ventajas y desventajas del formato binario

🧐🧪✍️ ¿Qué ventajas y desventajas ves en usar un formato binario en lugar de texto en ASCII?

Ventajas:

Más compacto: ocupa menos espacio (6 bytes vs ~15–20 en ASCII).
Más rápido de transmitir y procesar.
Evita problemas de interpretación con caracteres especiales.

Desventajas:

Es más difícil de leer e interpretar a simple vista.
Requiere saber exactamente el formato (>2h2B) para poder decodificarlo.
No es tan “humano” como ver directamente números separados por comas.


4. Experimento con was_gesture('shake')

🧐🧪✍️ Captura el resultado del experimento. ¿Cuántos bytes se están enviando por mensaje? ¿Cómo se relaciona esto con el formato '>2h2B'? ¿Qué significa cada uno de los bytes que se envían?

Se están enviando 6 bytes por mensaje:

2 bytes → xValue (entero corto, puede ser positivo o negativo).

2 bytes → yValue (entero corto, puede ser positivo o negativo).

1 byte → aState (0 si no está presionado, 1 si está presionado).

1 byte → bState (igual que el anterior).

El formato >2h2B define exactamente esa estructura: 2 enteros cortos con signo + 2 enteros sin signo.


5. Números positivos y negativos en >2h2B

🧐🧪✍️ ¿Cómo se verían esos números en el formato '>2h2B'?

Como los h son enteros cortos con signo (16 bits), se usan dos bytes en complemento a dos:

Un número positivo (ej: 200) aparece como 00 C8 en Hex,
Un número negativo (ej: -200) aparece como FF 38 en Hex.

Así se representan los valores de aceleración positivos y negativos.


6. Comparación ASCII vs Binario

🧐🧪✍️ Captura el resultado del experimento. ¿Qué diferencias ves entre los datos en ASCII y en binario? ¿Qué ventajas y desventajas ves en usar un formato binario en lugar de texto en ASCII? ¿Qué ventajas y desventajas ves en usar un formato ASCII en lugar de binario?

Diferencias observadas:

El binario aparece como bytes en Hex (compacto, ilegible a simple vista).
El ASCII se ve como texto legible (-20,50,0,1 por ejemplo).

Ventajas del binario:

Compacto y rápido.
Mejor para transmitir grandes cantidades de datos en tiempo real.

Desventajas del binario:

No es legible sin herramientas de decodificación.
Mayor riesgo de error si el formato no se interpreta bien.

Ventajas del ASCII:

Fácil de leer e interpretar a simple vista.
No depende de conocer la estructura de bytes.

Desventajas del ASCII:

Ocupa más espacio (cada número puede ser 3-5 caracteres).
Más lento de enviar y procesar.





### ACTIVIDAD 03


Pregunta 1

¿Por qué en la unidad anterior teníamos que enviar la información delimitada con un salto de línea y ahora no es necesario?

Antes:

Se usaban cadenas de texto ("500,524,true,false\n").
Cada valor tenía distinto tamaño (500 → 3 caracteres, true → 4 caracteres).
El receptor no podía saber dónde terminaba un número y empezaba el siguiente sin un delimitador.
El salto de línea (\n) servía como “marca de fin de paquete”.

Ahora:

Se usan paquetes binarios de tamaño fijo (6 bytes o 8 bytes con framing).
Cada campo ocupa un tamaño exacto y conocido:

xValue → 2 bytes

yValue → 2 bytes

aState → 1 byte

bState → 1 byte

Ya no hace falta un delimitador porque el receptor sabe que cada paquete mide exactamente 6 bytes.

Pregunta 2

Compara el código de la unidad anterior con el nuevo (recepción de datos seriales). ¿Qué cambios observas?

Antes:

Leía líneas completas de texto con port.readUntil("\n").
Convertía cadenas a enteros/booleanos (int(values[0]), values[2].toLowerCase() === "true").
Dependía de que el micro:bit enviara texto delimitado por comas y salto de línea.

Ahora:

Lee un número fijo de bytes (port.readBytes(6) o port.readBytes(8) con framing).
Usa DataView para interpretar bytes como enteros (getInt16, getUint8).
No hay parsing de strings, sino lectura binaria directa → más eficiente y rápido.

Pregunta 3

¿Qué ves en la consola cuando ocurre el error? ¿Por qué se produce?

Ejemplo de salida con error:

microBitX: 500 microBitY: 524 microBitAState: true microBitBState: false
microBitX: 500 microBitY: 513 microBitAState: false microBitBState: false
microBitX: 3073 microBitY: 1 microBitAState: false microBitBState: false


Lo que pasa:

Los bytes llegan en “pedazos” y a veces p5.js empieza a leer en medio de un paquete.
Eso desordena los valores → el microBitY cambia bruscamente (524 → 513), o aparecen valores absurdos (microBitX: 3073).
Es un problema de sincronización típico en comunicaciones seriales.

Pregunta 4

¿Qué cambia con la estrategia de framing (versión final del código)? ¿Qué ves ahora en consola?

Cambios claves:

Se agrega un header fijo (0xAA) → marca el inicio del paquete.

Se incluye un checksum → garantiza integridad de los datos.

En el código p5.js:
Se almacena todo en un serialBuffer.
Se buscan headers válidos antes de procesar un paquete.
Se descartan bytes basura o paquetes dañados.

Resultado en consola:

Ahora los valores son estables y coherentes (microBitX: … microBitY: …).
Si hay un error de transmisión → aparece "Checksum error in packet", pero el programa no se desincroniza.



### ACTIVIDAD 04


Cabe aclarar que en el momento en el que hice la ultima actividad no tenia el microbit asi que le pido disculpas profesor porque en realidad para el desarrollo de la actividad que fue modificando el codigo mio que hice antes si por no tener el microbit y directamente sin poder probar mi aplicacion se me hizo imposible ver los errores principales para el desarrollo de esta actividad, pero tampoco queria dejarlo imcom´pleto entonces le pedi a la ia generativa que me simulara la situacion porque yo no tenia el microbit y me explico que las cosas que habia que cambiar que podrian dar errores en el codigo mio originla de la unidad anterior eran pocas enrealidad como por ejemplo cambiar ciertas cosas para que fuera compatible mi codigo con el codigo del microbit nuevo, tambien actualize algunas variable para que fuera igual al nuevo codiugo del microbit y etc.

<img width="1903" height="549" alt="image" src="https://github.com/user-attachments/assets/dc1c31c4-9273-4d3f-90e8-2f680d584669" />
el codigo de esta unidad en el microbit

<img width="1903" height="828" alt="image" src="https://github.com/user-attachments/assets/5d2a25f1-fdd0-4cff-8d0e-ae94428d7601" />
este es el codigo funcionando efectivamente ya modificado y listo para conectar con el microbit

<img width="1645" height="808" alt="image" src="https://github.com/user-attachments/assets/f0234f91-0edb-49aa-9c34-666263bf58f7" />
parte del codigo modificada para procesar el buffer serial

<img width="887" height="306" alt="image" src="https://github.com/user-attachments/assets/dd8229c2-fde2-456f-9eab-df7639fddf53" />
variables cambiadas




