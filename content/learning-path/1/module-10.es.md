---
style: module
title: Ejercicio de captura de bandera
description: También hemos diseñado un ejercicio de captura de banderas en el que los alumnos pueden analizar un correo electrónico de phishing y la infraestructura a la que se vincula. El ejercicio se puede utilizar como práctica adicional o como ejercicio de verificación de habilidades y se puede encontrar aquí.
weight: 10
---

También hemos diseñado un ejercicio de captura de bandera en el cual los estudiantes pueden analizar un correo electrónico de phishing y la infraestructura a la que enlaza. El ejercicio se puede utilizar como práctica adicional o como ejercicio de verificación de habilidades, y se puede encontrar a continuación.

Estás sentado en la bulliciosa redacción de la prensa donde trabajas como administrador de IT, totalmente concentrado en tus deberes rodeado de monitores brillantes. Tu colega Alia del departamento de Contabilidad se acerca corriendo con una expresión preocupada, y te informa que reenvió un correo electrónico que afirmaba ser de PayPal y que les urgía a tomar medidas inmediatas debido a una actividad sospechosa en su cuenta. La organización de prensa depende de PayPal para procesar los pagos de suscripción. Tu interés se despierta instantáneamente al reconocer el potencial de un ataque malicioso, y comienzas una investigación.

_Esta actividad utiliza un correo electrónico y una página de destino de muestra necesarios para la actividad. Descarga los archivos aquí: {{< fontawesome "solid/download" >}} [Materiales CTF](/files/ctf-materials.zip)_

### Pregunta 1: ¿Cuál es la dirección del remitente del correo electrónico?

{{< question title="Instrucciones" open="true" >}}
Descubre cómo aparecería la dirección del remitente en el cliente de correo electrónico si se abre el correo electrónico.
{{< /question >}}

{{< question title="Pistas" >}}
Hay varias formas de ver cómo aparecería el correo electrónico para el destinatario. La forma más directa es abrir el archivo en un cliente de correo electrónico, que es lo que hemos hecho en los ejemplos a continuación. Sin embargo, en el contexto de una amenaza dirigida, esto puede ser una mala idea en caso de que el archivo contenga scripts que puedan explotar clientes de correo electrónico, recopilar información sobre el dispositivo o cargar recursos externos (como imágenes/píxeles de seguimiento) que revelen tu IP al atacante. En el caso de este recorrido, es seguro abrir el archivo EML en tu cliente de correo electrónico, pero para el trabajo en vivo considera algunas alternativas:

- Usa un cliente de correo electrónico en una máquina virtual que se pueda restaurar a una instantánea segura.
- Abre el archivo en un editor de texto y lee directamente el contenido HTML.
- Renombra el archivo a .mht y ábrelo en un navegador web (considera usar una máquina en sandbox y conectarte a una VPN para evitar la recopilación de IP desde píxeles de seguimiento).
- Usa un servicio en línea como [https://www.emlreader.com/](https://www.emlreader.com/) o [https://www.encryptomatic.com/viewer/](https://www.encryptomatic.com/viewer/) para renderizar el correo electrónico. El analizador de encabezados de correo electrónico de MXToolBox [https://mxtoolbox.com/EmailHeaders.aspx](https://mxtoolbox.com/EmailHeaders.aspx) (utilizado más adelante en este recorrido) también renderizará contenido HTML si lo incluyes en los encabezados pegados.
- Usa una herramienta de eDiscovery que pueda renderizar archivos EML.
- Aloja tu propio servicio para renderizar archivos EML, como [https://github.com/xme/emlrender](https://github.com/xme/emlrender)

En este recorrido simplemente abriremos el correo electrónico (paypal.eml) en un programa de correo electrónico

![alt_text](/media/uploads/image1.png "image_tooltip")

Al observar el correo electrónico, vemos la dirección de correo electrónico del remitente visible

![alt_text](/media/uploads/image2.png "image_tooltip")
{{< /question >}}

{{< question title="Respuesta" >}}
El correo electrónico del remitente es: [paypal@service.com](mailto:paypal@service.com)
{{< /question >}}

### Pregunta 2: ¿Cuál es el asunto de este correo electrónico?

{{< question title="Instrucciones" open="true" >}}
A medida que continuamos revisando el correo electrónico, buscamos más características que podrían ser indicativas de spam o mensajes maliciosos. ¡Vamos a revisar el asunto y algunos otros signos dentro del texto! Si estás leyendo el correo electrónico en un editor de texto, lo encontrarás en la línea "Asunto:".
{{< /question >}}

{{< question title="Pistas" >}}
![alt_text](/media/uploads/image1.png "image_tooltip")

Aquí hay algunos puntos clave para detectar en un correo electrónico de phishing:

- Sentido de urgencia
- Apertura extraña, no te dirige por tu nombre
- Errores gramaticales
- La dirección del remitente o las URL dentro del correo electrónico están obfuscadas o no coinciden con el sitio web que el correo electrónico afirma ser

{{< /question >}}

{{< question title="Respuesta" >}}
La línea de asunto del correo electrónico es: _Te llamamos y no contestaste_
{{< /question >}}

### Pregunta 3: ¿Qué acción se solicita?

{{< question title="Instrucciones" open="true" >}}
Cuando examinamos un correo electrónico potencialmente malicioso, también necesitamos averiguar qué acción quería que hiciera el remitente. ¿Qué acción asumes que el remitente quería que hiciera el destinatario?
{{< /question >}}

{{< question title="Pistas" >}}
![alt_text](/media/uploads/image1.png "image_tooltip")
{{< /question >}}

{{< question title="Respuesta" >}}
Haz clic en uno de los dos enlaces dentro del correo electrónico.
{{< /question >}}

## Reconocimiento de la Amenaza

### Pregunta 4: Desactiva el enlace "Confirmar"

{{< question title="Instrucciones" open="true" >}}
A medida que avanzamos en el análisis, el primer paso que debemos hacer es comprender la diferencia entre los enlaces sospechosos. Cuando analizamos enlaces potencialmente sospechosos, típicamente los desactivamos; esto significa reemplazar algunos caracteres para que el enlace no se pueda hacer clic accidentalmente ni desencadene mecanismos de escaneo de enlaces automáticos o de virus. Desactivar los enlaces se considera una buena práctica en las investigaciones de seguridad. Los enlaces desactivados no se convertirán automáticamente en enlaces clicables, pero aún conservarán la información del enlace original, por ejemplo, hxxp[://]www[.]google[.]com.
{{< /question >}}

{{< question title="Pistas" >}}
Puedes desactivar un enlace en un editor de texto. Aquí usaremos [CyberChef](gchq.github.io/CyberChef) para desactivar la URL ya que usaremos CyberChef para otros pasos también. CyberChef es una aplicación web con una gran cantidad de funciones que pueden ayudarte a analizar datos relacionados con la seguridad. Aquí tienes una [introducción muy breve](https://udel.codes/cyberchef.html) a su diseño y funciones.

Como parte de este ejercicio, juega con CyberChef y desactiva el enlace "por favor confirma" del correo electrónico adjunto.

![alt_text](/media/uploads/image1.png "image_tooltip")
Primero, copiamos el hipervínculo del correo electrónico.

![alt_text](/media/uploads/image2.png "image_tooltip")
Luego, tomamos la entrada "Defang URL" de CyberChef y la arrastramos a la sección "Receta".

![alt_text](/media/uploads/image3.png "image_tooltip")

Una vez que hemos pegado la URL en la sección de entrada en CyberChef, automáticamente generará una versión desactivada de la misma.
{{< /question >}}

{{< question title="Respuesta" >}}
hxxps[://]d[.]pr/mUxkOm
{{< /question >}}

### Pregunta 5: Usa CyberChef para extraer y desactivar todos los enlaces en el correo electrónico

{{< question title="Instrucciones" open="true" >}}
Puedes usar CyberChef para realizar muchas tareas de análisis diferentes. Esta vez, encuentra y describe un flujo de trabajo para extraer fácilmente y desactivar todos los enlaces del correo electrónico.
{{< /question >}}

{{< question title="Respuesta" >}}
Puedes usar una 'receta', o una serie de pasos conectados, en CyberChef para llevar a cabo un análisis más complejo. Para obtener y desactivar todas las URLs en el mensaje, todo lo que necesitas hacer es ejecutar una receta con los flujos de trabajo "extraer URLs" y "desactivar URLs" y pegar el contenido completo del correo electrónico (copiado desde un editor de texto plano) como entrada. Si marcas la casilla "único" bajo "extraer URLs", verás que los resultados diferirán de los de la captura de pantalla, y solo se producirá una URL, la misma que desactivaste anteriormente. El hecho de que haya solo una URL, repetida muchas veces, dentro del correo electrónico es una gran noticia para nosotros: facilitará mucho nuestro análisis.

![alt_text](/media/uploads/image1.png "image_tooltip")
{{< /question >}}

## Investigación Pasiva de URLs, Nombres de Host e IP

### Pregunta 6: ¿Cuándo se envió la URL desactivada en la pregunta 4 a VirusTotal?

{{< question title="Pistas" >}}
Para las siguientes preguntas, utilizaremos [VirusTotal](https://www.virustotal.com/). Es un servicio en línea que actúa como un escáner de seguridad para archivos y URL sospechosos. Piénsalo como un inspector digital. Puedes subir un archivo o proporcionar una URL, y VirusTotal lo escanea con motores antivirus y verificadores de sitios web de docenas de compañías de seguridad diferentes. También realiza algunos análisis adicionales. Esto te proporciona una visión rápida de si el archivo o el sitio web es probablemente malicioso. Es una herramienta valiosa para ayudarte a identificar amenazas potenciales antes de abrir un archivo adjunto o hacer clic en un enlace. También contiene metadatos sobre archivos que pueden ser útiles. Aquí usaremos el historial de entradas para averiguar cuándo se observó por primera vez un indicador malicioso.

Pega la URL de la pregunta 4 en VirusTotal (esta vez, debes pegar la URL completa, no la versión desactivada). Ve a la pestaña "detalles" y mira el historial de captura de URL.

![alt_text](/media/uploads/image1.png "image_tooltip")
{{< /question >}}

{{< question title="Respuesta" >}}
08/20/2018
{{< /question >}}

### Pregunta 7: ¿Qué dirección IP sirve VirusTotal para la URL desactivada en la pregunta 4?

{{< question title="Pistas" >}}
También buscando a través de la pestaña "detalles" en VirusTotal, busca la dirección IP que sirve.

![alt_text](/media/uploads/image1.png "image_tooltip")
{{< /question >}}

{{< question title="Respuesta" >}}
52.89.102.146
{{< /question >}}

### Pregunta 8: ¿Cuántos proveedores en VirusTotal detectan esta URL como maliciosa?

{{< question title="Instrucciones" open="true" >}}
Al ver la URL en VirusTotal, busca todos los detalles bajo la pestaña "detección". Para profundizar en lo que VirusTotal significa con "detección" y cuáles son sus metodologías, consulta [su documentación](https://docs.virustotal.com/).
{{< /question >}}

{{< question title="Respuesta" >}}
5 Proveedores
{{< /question >}}

### Pregunta 9: ¿En qué registrador se registró el dominio desactivado en la pregunta 4?

{{< question title="Instrucciones" open="true" >}}
Para buscar información relacionada con el registro de un dominio, podemos utilizar una búsqueda whois. Puedes realizar dicha búsqueda ya sea a través de una herramienta de línea de comandos en tu dispositivo o a través de una aplicación dedicada.
{{< /question >}}

{{< question title="Pistas" >}}
Aquí utilizamos un sitio web whois para extraerlo

![alt_text](/media/uploads/image1.png "image_tooltip")
{{< /question >}}

{{< question title="Respuesta" >}}
Internet Technology Solutions
{{< /question >}}

### Pregunta 10: ¿Dónde se encuentra geográficamente ubicada la IP de servidor que identificaste a través de VirusTotal?

{{< question title="Instrucciones" open="true" >}}
Las direcciones IP están vagamente vinculadas a ubicaciones geográficas, como ciudades o distritos. Hay muchos servicios en línea donde puedes ingresar una dirección IP y aprender más sobre dónde probablemente esté ubicada. Aunque este tipo de verificación no es perfecta y a veces puede cometer errores, sigue siendo una parte importante de las investigaciones de infraestructura maliciosa.

Vale la pena comparar la información que recibes de una búsqueda whois con la que recibes de búsquedas de ubicación de IP. Podrías descubrir que la dirección IP que estás investigando pertenece a un proveedor de VPN o a una gran empresa tecnológica como Google; si este es el caso, entonces no aprenderás mucho de esas investigaciones, la ubicación IP probablemente corresponderá a una de las granjas de servidores de esas empresas y puede tener poco que ver con la ubicación de la persona o entidad que estás investigando.

![alt_text](/media/uploads/image1.png "image_tooltip")
{{< /question >}}

{{< question title="Respuesta" >}}
Portland, Oregon, Estados Unidos
{{< /question >}}

## Investigación Pasiva de Encabezados de Correo Electrónico

### Pregunta 11: ¿Cuál es la ruta de retorno del correo electrónico inicial que investigaste?

{{< question title="Instrucciones" open="true" >}}
Para las siguientes preguntas, utilizaremos una herramienta llamada [MxToolbox](https://mxtoolbox.com/). Es una herramienta que puede analizar encabezados de correo electrónico, nombres de host, estado de spam y mucho más. Nos centraremos en su característica de [análisis de encabezados](https://mxtoolbox.com/EmailHeaders.aspx), en la cual puedes copiar y pegar todos los encabezados de un correo electrónico (¡o incluso el correo electrónico completo!) y realizar algunos análisis básicos sobre ellos.
{{< /question >}}

{{< question title="Pistas" >}}
Primero, abre el correo electrónico con un editor de texto plano de tu elección y copia su contenido. Luego, pégalo en la herramienta "Analizar encabezados" de MxToolbox

![alt_text](/media/uploads/image1.png "image_tooltip")

Una vez que presionas "Analizar encabezado", puedes ver la ruta de retorno

![alt_text](/media/uploads/image2.png "image_tooltip")
{{< /question >}}

{{< question title="Respuesta" >}}
paparazi@rjttznyzjjzydnillquh.designclub.uk.com
{{< /question >}}

### Pregunta 12: ¿Cuál es el primer salto y la dirección del servidor SMTP de ese correo electrónico?

{{< question title="Instrucciones" open="true" >}}
Ve al archivo "mx-toolbox-header-analysis", mira la sección de información de retransmisión.

![alt_text](/media/uploads/image1.png "image_tooltip")
La dirección del servidor de correo

![alt_text](/media/uploads/image2.png "image_tooltip")
{{< /question >}}

{{< question title="Respuesta" >}}
Primer salto: efianalytics.com 216.244.76.116

SMTP: 2002:a59:ce05:0:b0:2d3:3de5:67a9
{{< /question >}}

## Investigación Activa de Páginas Web Maliciosas

### Pregunta 13: ¿Cuál es el ID de la víctima presente en el código del sitio web?

{{< question title="Instrucciones" open="true" >}}
Si el destinatario del correo electrónico hizo clic en el enlace, llegaría a una página de destino. Ve al archivo en el paquete de actividad para abrir "paypal.html", mira el código fuente y busca el ID de la víctima. Usa CyberChef para decodificarlo y encontrar una cadena de texto.
{{< /question >}}

{{< question title="Pistas" >}}
En este ejercicio, encontrarás una cadena de texto codificada en Base64. Base64 es una técnica para transformar texto que tiene muchos propósitos, pero en este caso tiene como objetivo ofuscar una cadena de texto: la cadena sigue ahí, solo que guardada de una manera que no puede ser fácilmente detectada por el ojo humano o por una simple búsqueda de texto. Si esta es la primera vez que te encuentras con Base64 en tu trabajo, vale la pena leer [un poco más sobre él y otros formatos de obfuscación](https://anithaana3.medium.com/common-text-encoding-methods-for-code-obfuscation-9399757eb5c3). Los autores de malware suelen obfuscar algunos textos dentro de sus programas utilizando una técnica como Base64 para dificultar el análisis.

CyberChef puede codificar y decodificar texto en Base64.

Abrimos una vez más el código adjunto de la página de phishing (.html)

![alt_text](/media/uploads/image1.png "image_tooltip")

Buscamos el ID de la víctima en el código fuente, después lo copiamos desde allí y lo pegamos en la receta de "decodificación Base64" de CyberChef

![alt_text](/media/uploads/image2.png "image_tooltip")
{{< /question >}}

{{< question title="Respuesta" >}}
Th1s_1s_pH1sh1ng_Em3il
{{< /question >}}

## Otros recursos y enlaces

{{% resource title="Documentación comunitaria de la línea de ayuda de Access Now para responder a correos electrónicos sospechosos o de phishing" languages="Inglés" cost="Gratis" description="El cliente recibe un correo electrónico sospechoso o de phishing" url="https://accessnowhelpline.gitlab.io/community-documentation/58-Suspicious_Phishing_Email.html#" %}}

{{% resource title="Lista de todos los tipos de registros DNS" languages="Inglés, Chino, Japonés, Coreano, Ruso, Serbio, Ucraniano, Esperanto, Húngaro, Vietnamita, Italiano, Español, Francés" cost="Gratis" description="Incluye (casi?) todos los tipos de registros DNS." url="https://es.wikipedia.org/wiki/Anexo:Tipos_de_registros_DNS" %}}

{{% resource title="Informes de Amnesty sobre campañas de phishing" languages="Varios, dependiendo del informe" cost="Gratis" description="Una lista de ejemplos de cómo se ve una campaña de phishing dirigida contra defensores de derechos humanos, activistas y periodistas." url="https://www.amnesty.org/en/search/phishing/" %}}
