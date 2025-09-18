
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

