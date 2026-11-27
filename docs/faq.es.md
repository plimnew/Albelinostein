# Preguntas frecuentes

La versión 0.1.0 se publicó el 26 de noviembre de 2026. Las respuestas
describen el programa tal como fue diseñado y probado; consulta el
[estado de las versiones](../README.es.md).

## ¿Mis datos salen de mi ordenador?

Solo si tú lo eliges. Adelino se ejecuta en tu ordenador y funciona con un
modelo local y sin red. Si conectas un modelo remoto, lo que envíes al modelo
va al proveedor de ese modelo. Las peticiones de red hechas por el agente
preguntan antes, y preguntan siempre después de que la sesión haya leído
contenido externo. Hoy no existe ninguna cuenta, y no se envía nada a pLim.

## ¿Qué modelos puedo usar?

Claude, a través de la API de Anthropic, o cualquier servidor compatible con
la API de OpenAI que se ejecute en tu ordenador (por ejemplo Ollama, LM
Studio, llama.cpp o vLLM), para que nada salga de la máquina. Una salvedad
honesta: las llamadas a un modelo real aún no se han verificado; se probaron
contra un servidor de sustitución, a nivel de protocolo.

## ¿Cuánto cuesta?

No hay ningún precio publicado en este repositorio y ninguna versión está a
la venta. Lo que se puede decir: cada llamada a un modelo remoto cuesta lo
que cobre el proveedor, y Adelino registra los tokens y el coste estimado de
cada llamada. Un modelo local no tiene coste contabilizado. Puedes definir un
límite de gasto por sesión, por día o por petición programada, que detiene a
Adelino antes de volver a consultar al modelo.

## ¿Qué queda registrado?

Cada mensaje, cada acción propuesta, la decisión de la política, cada
aprobación o rechazo (firmado), el resultado y el coste de cada llamada. El
registro está firmado y encadenado por sumas, de modo que cualquier
alteración posterior se detecta. El contenido (mensajes, argumentos,
resultados) se guarda aparte y se puede borrar con `adelino redact` sin
invalidar el registro. Las claves y los secretos no se escriben en el
registro.

## ¿Cómo sé que el registro no se ha alterado?

Con `adelino verify`: comprueba cada evento, el enlace con el anterior, la
firma de quien lo escribió y la de quien decidió. Si algo se ha alterado, se
detiene en el primer evento erróneo y lo indica. También puedes exportar una
sesión a un archivo que cualquier persona verifica en otra máquina, sin
acceder a tus datos.

## ¿Puedo deshacer lo que ha hecho Adelino?

Las escrituras de archivos, sí: `adelino undo` restaura la versión anterior o
borra el archivo que él creó. Se niega si el archivo se modificó después, a
no ser que lo fuerces. Los comandos y las peticiones de red no se deshacen,
y por eso preguntan más veces.

## ¿Puede Adelino borrar o estropear mis archivos sin que yo lo sepa?

Por defecto no actúa por su cuenta: en el modo predeterminado solo lee dentro
de la carpeta de trabajo y pregunta por todo lo demás. Nada irreversible se
ejecuta sin una decisión humana firmada. Un comando aprobado se ejecuta
confinado (en Linux) a la carpeta de trabajo y sin red. Pero una aprobación
es una firma tuya sobre lo que se te mostró: leer lo que se va a ejecutar
sigue siendo cosa tuya.

## ¿Qué son las skills?

Notas que Adelino escribe, con un modelo, a partir de un libro, artículo o
manual tuyo (PDF, EPUB, Word, HTML, Markdown o texto), capítulo a capítulo,
para poder consultarlas después. Muestra el coste estimado antes de enviar
nada y solo avanza con tu conformidad. Una skill no concede ningún permiso,
su texto se trata como no fiable y las notas pueden ser erróneas. Adelino
nunca busca ni descarga libros.

## ¿Qué sistemas son compatibles?

Están publicados Linux (x86_64: `.deb`, `.rpm`, AppImage y programa de línea
de comandos), macOS para Apple Silicon (`.dmg`, versión 0.1.1, firmado con
Developer ID y notarizado por Apple; no funciona en Macs con procesador
Intel) y Android (APK, arm64). Linux está validado por el guion de
aceptación. macOS, Windows, Android e iOS están sin validar. Windows (aún sin
certificado de firma de código) e iOS (aún sin receta de compilación firmada)
no están publicados.
En Windows y macOS aún no hay sandbox de comandos: cada comando pregunta.

## ¿Puedo aprobar acciones desde el móvil?

El protocolo está construido y probado: un móvil emparejado tiene su propia
clave y su aprobación es una firma que el ordenador no puede falsificar. Pero
la aplicación móvil aún no se ha ejecutado en un dispositivo real, y la
lectura del código QR con la cámara aún no está construida.

## ¿El código es abierto?

La licencia es Apache-2.0: Albelinostein se puede usar gratis para cualquier
fin, incluido el comercial. El código fuente aún no está publicado. Este
repositorio existe para publicar el estado de las versiones, la
guía, el documento técnico y las claves para verificar las descargas, que
están en la página de descargas del sitio
(<https://albelino.plimswitzzer.com/es/downloads/>). Los binarios los
distribuye pLim.

## ¿Cómo verifico una descarga?

Cada versión tiene una lista de sumas (`SHA256SUMS`) firmada con una clave
GPG, cuya parte pública está publicada en `docs/keys/albelino-releases.asc`
y en <https://albelino.plimswitzzer.com/downloads/releases/public-key.asc>.
Paso a paso en [verificar.es.md](verificar.es.md). Las Releases de este
repositorio no tienen ninguna versión: las descargas están solo en la página
de descargas del sitio.

## ¿Dónde obtengo soporte?

En la comunidad de Discord, <https://discord.gg/AyZHZPtkHk>, donde el equipo
responde: preguntas en #help y errores en #bug-reports. Las vulnerabilidades
de seguridad nunca en Discord: comunícalas en privado en GitHub, en «Report a
vulnerability»; consulta [SECURITY.md](../SECURITY.md).
