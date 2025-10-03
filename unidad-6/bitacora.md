
# Evidencias de la unidad 6

## Actividad 01

¿Qué ocurrió en la terminal cuando ejecutaste npm install? ¿Cuál crees que es su propósito?
- Cuando ejecuté npm install empezaron a descargarse un montón de librerías y se creó la carpeta node_modules. Creo que el propósito es instalar todas las dependencias que el proyecto necesita para funcionar bien.

¿Qué mensaje específico apareció en la terminal después de ejecutar npm start? ¿Qué indica este mensaje?
- Me salió un mensaje como “Server listening on port 3000”. Eso significa que el servidor ya está corriendo y listo para recibir conexiones en ese puerto.

Describe lo que ves inicialmente en page1 y page2 en tu navegador.
- En page1 y page2 se ven unas ventanas de p5.js que parecen simples, pero están listas para reaccionar. Básicamente es como un espacio donde luego se nota la interacción entre ellas.

¿Qué mensajes aparecieron en la terminal del servidor cuando abriste page1 y page2?
- En la terminal salió que se conectó un cliente nuevo, con un ID distinto para cada pestaña.

Describe qué sucede en ambas páginas del navegador cuando mueves una de las ventanas.
- Cuando muevo la ventana de una página, la otra también refleja un cambio visual en tiempo real. En la consola del navegador salen mensajes de actualización y en la terminal aparecen datos de posición.



## Actividad 02

¿Qué pasaría si esa rampa (conexión a Internet) se corta?
- En mi caso casi siempre uso Wi-Fi. Si la conexión se corta, básicamente me quedo sin acceso a Internet y no podría comunicarme con ningún servidor ni abrir páginas.

Ejemplos de cliente-servidor en la vida diaria:
- En un restaurante, el cliente soy yo y el servidor es el mesero/cocina. Yo pido un plato y ellos me lo entregan.
- En una biblioteca, el usuario pide un libro (cliente) y el bibliotecario lo entrega (servidor).

Ejemplo con una URL de mi sitio favorito:
- URL: https://www.youtube.com/watch?v=abc123

Protocolo: https://

Dominio: www.youtube.com

Ruta: /watch?v=abc123
Si escribo solo youtube.com, el servidor me manda a la página principal por defecto.

Comparación HTTP con protocolos seriales:
- Ambos tienen reglas para que dos dispositivos se entiendan. La diferencia es que HTTP es mucho más complejo porque maneja cabeceras, métodos y estados, mientras que el protocolo serial era solo bytes simples. HTTP es más complejo porque en la web se necesita enviar más tipos de información y entre muchísimos clientes.

Página web simple (login):
- HTML serían los campos de texto y el botón de login.
- CSS sería el color del botón y la letra.
- JavaScript sería la validación de si escribí algo, y mostrar “contraseña incorrecta” sin recargar la página.

Comparar draw() de p5.js con el modelo de eventos:
- El modelo de eventos es mejor para la web porque solo se ejecuta cuando pasa algo, como un clic o un mensaje. No tendría sentido redibujar toda la página 60 veces por segundo si nada ha cambiado.

¿Por qué usar JavaScript en cliente y servidor?
- Porque se usa el mismo lenguaje en ambos lados, lo cual hace que el trabajo sea más fácil, más rápido de aprender y que se pueda compartir código entre cliente y servidor.

Diferencia HTTP vs WebSockets/Socket.IO:
- HTTP es pedir-responder, como mandar correos. WebSockets/Socket.IO es como una llamada telefónica: conexión directa en tiempo real. Se usa en chats, juegos online o cosas colaborativas como Google Docs.


## Actividad 03

Ruta /page1 → /pagina_uno:
- Cuando intenté abrir /page1 no funcionó, pero sí funcionó con /pagina_uno. Eso muestra que el servidor solo responde a las rutas exactas que están en el código.

IDs en conexiones y desconexiones:
- Cada vez que abría una página, en la terminal aparecía un ID único. Cuando cerraba esa pestaña, salía un mensaje de desconexión con ese mismo ID. O sea, el servidor identifica cada cliente de manera individual.

Eventos win1update y win2update:
- Cuando movía page1 salía win1update y datos en la terminal. Cuando movía page2 salía win2update con sus propios datos. Al cambiar broadcast.emit por emit, solo se enviaba el mensaje a la misma página, no a la otra, por eso no se actualizaba.

Cambio de puerto:
- Al cambiar el puerto a 3001, el servidor me dijo en la terminal que estaba escuchando en el puerto 3001. Ya no funcionaba en localhost:3000, pero sí en localhost:3001. Eso demuestra que el puerto define exactamente por dónde entra la conexión.

## Actividad 04

Errores al refrescar con el servidor apagado:
- Salió un error en la consola tipo “WebSocket connection failed”. Eso significa que la página trató de conectarse al servidor pero no lo encontró. Cuando volví a prender el servidor y refresqué, ya no salió el error.

Comentar socket.emit en connect:
- Al hacerlo, page2 ya no mandaba su información al servidor, entonces la otra página no recibía nada. Pasó porque quitamos la línea que transmitía los datos.

Mover page1 y ver en page2:
- Cuando movía page1, en la consola de page2 aparecían los datos recibidos. Igual al revés: si movía page2, los datos se veían en page1. Es porque cada cliente manda sus datos y el servidor los reenvía al otro.

checkWindowPosition():
- Al probar el if, noté que el bloque solo se ejecuta si la ventana realmente cambia de posición. Esto evita enviar datos innecesarios y hace más eficiente la comunicación.

Modificación creativa:
- Hice que el background cambie de color según la distancia entre las dos ventanas. También podría agregar que aparezcan círculos que se hacen más grandes cuanto más se alejan las ventanas.
