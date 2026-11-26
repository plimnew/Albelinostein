# Usar Adelino

Adelino es un agente de IA que trabaja en tus archivos y pregunta antes de
cambiar nada. Lo que hizo, y lo que tú decidiste, queda en un registro
firmado que puedes verificar en cualquier momento.

Esta guía está organizada según lo que quieres hacer. Al final está la
referencia de todos los comandos. En inglés: [guide.en.md](guide.en.md).

> Adelino 0.1.0 se publicó el 26 de noviembre de 2026, en la página de
> descargas del sitio (<https://albelino.plimswitzzer.com/es/downloads/>).
> Esta guía describe el programa tal como se va a comportar; las
> instrucciones de instalación se añadirán con la primera versión. Consulta
> el [estado de cada versión](../README.es.md).

- [Empezar](#empezar)
- [¿Cuánto puede hacer solo?](#cuánto-puede-hacer-solo)
- [Responder cuando pregunta](#responder-cuando-pregunta)
- [El registro: verificar, repetir, deshacer, exportar](#el-registro-verificar-repetir-deshacer-exportar)
- [Memoria y mapa del proyecto](#memoria-y-mapa-del-proyecto)
- [Skills: notas de un libro](#skills-notas-de-un-libro)
- [Más herramientas: servidores y packs](#más-herramientas-servidores-y-packs)
- [Tareas programadas](#tareas-programadas)
- [Móviles y la clave de firma](#móviles-y-la-clave-de-firma)
- [Cuando algo sale mal](#cuando-algo-sale-mal)
- [Referencia de comandos](#referencia-de-comandos)

## Empezar

```bash
adelino init
```

Conecta un modelo. Si ya existe una clave de Anthropic en el entorno, o un
servidor de modelos funcionando en este ordenador, no pregunta nada. Si no,
hace una pregunta: qué modelo usar.

```bash
adelino demo
```

Visita de dos minutos con un asistente de demostración. No necesita modelo.
Autoriza una acción, rechaza otra y ve el registro verificado.

```bash
adelino chat
```

Abre una conversación en la carpeta actual. Esa carpeta es la *carpeta de
trabajo*: Adelino puede leer dentro de ella sin preguntar. Usa `--workspace`
para elegir otra y `--mode` para elegir el grado de cuidado.

```bash
adelino run "resume el README"
```

Una petición y termina. `adelino resume <sesión>` continúa una sesión que
quedó en espera o se interrumpió; `adelino sessions` las lista con lo que ha
consumido cada una (llamadas al modelo, tokens, coste cuando se conoce,
tiempo).

## ¿Cuánto puede hacer solo?

Tres modos, por sesión:

| Modo | Lecturas en la carpeta de trabajo | Todo lo demás |
|---|---|---|
| `ask` (por defecto) | ejecuta | pregunta |
| `read_only` | ejecuta | rechaza, sin preguntar |
| `autonomous` | ejecuta | ejecuta **solo** si lo cubre una concesión; si no, pregunta |

```bash
adelino mode <sesión> autonomous
```

Una *concesión* dice qué puede ejecutarse sin preguntar en modo
`autonomous`. Son estrechas, pueden tener plazo y límite de usos, y pueden
revocarse.

```bash
adelino grant path ./src --write --minutes 60
```

```bash
adelino grant command make --uses 20
```

```bash
adelino grant command git --args status
```

Esta última permite `git status …` pero no `git push`. La comparación se
hace por argumentos completos, desde el principio.

```bash
adelino grant domain api.example.com
```

```bash
adelino grant list
```

```bash
adelino grant revoke <id>
```

Tres cosas preguntan siempre, en cualquier modo y con cualquier concesión:

- todo lo que no se puede deshacer;
- las peticiones de red y los comandos, después de que la sesión haya leído
  contenido externo (una página web, un archivo fuera de la carpeta de
  trabajo, un servidor de herramientas);
- cualquier comando, cuando este ordenador no puede confinarlo (consulta
  `adelino doctor`).

## Responder cuando pregunta

En el terminal, Adelino muestra lo que va a ejecutar, el riesgo, por qué
pregunta y cómo queda confinado el comando, y espera `y` o `n`. En la
aplicación aparece lo mismo en una hoja con **Autorizar** y **Rechazar**.

Si nadie responde, la acción queda en espera:

```bash
adelino approvals
```

```bash
adelino approve <petición>
```

```bash
adelino deny <petición>
```

```bash
adelino resume <sesión>
```

`<petición>` son los primeros caracteres de la petición que se muestra en la
lista. Una acción en espera sobrevive a un reinicio y a un fallo.

Tu respuesta se firma. Un rechazo queda registrado con la misma firmeza que
una autorización.

## El registro: verificar, repetir, deshacer, exportar

```bash
adelino verify
```

Verifica cada evento: que no se ha alterado, que sigue al anterior, que lo
firmó esta instalación y que cada decisión la firmó un dispositivo
autorizado a decidir. Si algo se ha alterado, se detiene en el primer evento
erróneo y lo indica.

```bash
adelino replay <sesión>
```

Muestra la sesión tal como ocurrió, reconstruida solo a partir del registro.

```bash
adelino undo <llamada>
```

Restaura un archivo que Adelino escribió. El identificador de la llamada
aparece junto a cada acción. Se niega si tú modificaste el archivo después,
a no ser que añadas `--force`. Solo las escrituras de archivos se pueden
deshacer; los comandos y las peticiones de red no, y por eso se tratan como
más arriesgados.

```bash
adelino export <sesión> -o sesion.json
```

```bash
adelino verify --file sesion.json
```

Un archivo que cualquier persona puede verificar en cualquier máquina con
Adelino instalado, sin acceso a tus datos. Contiene toda la cadena de
eventos (resúmenes y metadatos) y el contenido solo de esa sesión.

```bash
adelino redact <hash>
```

Borra un contenido guardado (un mensaje, la versión antigua de un archivo).
El registro sigue verificándose; el contenido desaparece para siempre.

```bash
adelino log -n 50
```

Los eventos en bruto.

## Memoria y mapa del proyecto

Adelino puede memorizar hechos entre sesiones. Cada hecho apunta al evento
que lo creó.

```bash
adelino memory list
```

```bash
adelino memory add "Las facturas se guardan por mes en /finanzas"
```

```bash
adelino memory search facturas
```

El *mapa del proyecto* permite a Adelino preguntar cómo se conectan los
archivos de un proyecto, en lugar de leerlos uno a uno. Se construye en este
ordenador con [Graphify](https://github.com/Graphify-Labs/graphify), sin
conexión, y se guarda fuera de tu proyecto.

```bash
adelino graph install
```

```bash
adelino graph index
```

```bash
adelino graph query "¿qué llama al módulo de pagos?"
```

## Skills: notas de un libro

Una *skill* es un conjunto de notas que Adelino escribe a partir de un
documento tuyo, como un libro, un artículo o un manual, para poder
consultarlo después. Lee por sí mismo archivos PDF, EPUB, Word (docx), HTML,
Markdown y texto; no hay nada que instalar. Adelino nunca busca ni descarga
libros.

```bash
adelino skill add ddia.pdf --name ddia --mode study --focus "frameworks, trade-offs, architecture patterns and anti-patterns"
```

Antes de enviar nada, muestra el título, el número de capítulos, cualquier
aviso del lector, cuántas llamadas al modelo y cuántos tokens va a gastar y
el coste estimado (o que no hay precio registrado). Dice que el documento
viaja al modelo capítulo a capítulo y pregunta `start? [y/N]`; con `--yes`
no pregunta. `--mode study` explica cada idea y termina cada capítulo con
preguntas; `--mode reference` (el predeterminado) es seco, para consulta.
`--kind prose` sirve para texto sin tablas ni código; `--kind technical` (el
predeterminado) mantiene las tablas, las fórmulas y el código tal como
están.

```bash
adelino skill list
```

```bash
adelino skill show ddia 3
```

Sin número de capítulo, `show` muestra el índice. `adelino skill off ddia`
conserva la skill pero Adelino deja de consultarla, `on` la vuelve a
activar, `remove` la borra y `export ddia -o ddia.zip` la escribe en un solo
archivo.

Lo que conviene saber:

- Una skill es material de consulta. No da ningún permiso y no dispensa de
  ninguna aprobación.
- El texto procede de un documento, por lo que no es de confianza. Adelino
  solo lo lee a través de una herramienta que lo marca como datos, y después
  de leerlo la sesión deja de ser de confianza: a partir de ahí se pregunta
  por todos los comandos y por todas las peticiones de red, con cualquier
  concesión (la misma regla que para las páginas web y los archivos fuera de
  la carpeta de trabajo).
- Solo lo que tú escribiste llega a las instrucciones permanentes de
  Adelino: el nombre de la skill, el nombre del archivo y el foco. Las notas
  nunca llegan.
- Cada llamada al modelo queda registrada en una sesión propia, con su
  coste, y se detiene en un límite de gasto como cualquier otra. Las notas ya
  escritas se conservan, así que volver a intentarlo tras una parada o un
  fallo no se paga dos veces.
- Añadir, quitar y activar o desactivar una skill quedan en el registro
  firmado; al añadir también se guarda el SHA-256 del documento de origen.
- Las skills se guardan en la carpeta de datos de Adelino, en
  `skills/<nombre>/`, como `SKILL.md`, `chapters/` y `skill.json` (el formato
  abierto Agent Skills), por lo que una skill exportada sirve a otros
  agentes.
- El texto de los PDF se lee sin esquema de página, por lo que las tablas,
  las fórmulas y el código pueden perder su forma; el lector siempre avisa.
  Un PDF solo se divide en capítulos donde una línea como "Capítulo 3"
  empieza uno; si no, se corta en partes según su tamaño. Los PDF
  escaneados, sin texto, se rechazan. Las notas las escribe el modelo y
  pueden ser erróneas: dicen lo que él entendió de cada capítulo.
- La aplicación tiene una página de Skills. Elige "Añadir documento", abre
  el archivo con el selector del sistema y ponle un nombre a la skill.
  Adelino muestra primero cuánto espera gastar, antes de enviar nada;
  confirma para empezar y detén el trabajo cuando quieras. Un límite de
  gasto detiene el trabajo como cualquier otro. La página lista tus skills y
  te deja leer las notas, activarlas y desactivarlas, quitarlas y exportar
  una a un archivo `.zip`. No está disponible en un móvil emparejado.

## Más herramientas: servidores y packs

Adelino puede usar herramientas que ofrecen otros programas a través del
Model Context Protocol (MCP). Un servidor es un programa que se arranca en
este ordenador o una dirección a la que se accede por HTTP.

```bash
adelino mcp add notas -- npx -y some-mcp-server ~/notas
```

```bash
adelino mcp add busqueda --url https://mcp.example.com/mcp
```

```bash
adelino mcp list
```

Cada herramienta de un servidor pregunta **siempre** hasta que le des una
clase:

```bash
adelino mcp trust notas read_file read
```

Una herramienta con la clase `read` puede entonces quedar cubierta por una
concesión (`adelino grant tool mcp__notas__read_file`); sin concesión sigue
preguntando. Si el servidor cambia más tarde la descripción o las entradas
de una herramienta, vuelve a preguntar siempre y la pregunta siguiente
avisa de que ha cambiado. Lo que devuelve una herramienta se trata como
contenido externo.

```bash
adelino mcp remove notas
```

Un *pack* es un archivo que lista servidores y las clases que propone su
autor.

```bash
adelino pack install notas.json
```

Muestra cada programa que se ejecutaría y cada clase que propone, y
pregunta primero. Instalar no concede nada.

## Tareas programadas

```bash
adelino schedule add "Resume lo que cambió en esta carpeta" --every 6h
```

```bash
adelino schedule list
```

```bash
adelino schedule remove <id>
```

Una tarea programada se ejecuta mientras `adelino serve` o la aplicación
estén abiertos. Se ejecuta siempre en modo `ask`: se detiene en la primera
cosa que necesite tu autorización y espera. No vuelve a ejecutarse mientras
una esté esperándote.

## Peticiones, planes y etapas

Cada cosa que pides queda registrada como una petición, desde tu mensaje
hasta el final de la respuesta. En trabajos de varias etapas, Adelino
escribe primero un plan y va marcando cada etapa.

```bash
adelino plan
```

Muestra todas las peticiones de la sesión más reciente: si está en
ejecución, en espera, concluida, frenada por un límite de gasto o fallida;
el plan con el estado de cada etapa; y cuántas acciones hizo. Un plan no
autoriza nada: cada acción se sigue comprobando y puede seguir pidiéndote
autorización.

## Verticales: trabajar dentro de un área

Una vertical es un archivo que ajusta Adelino a un área: lo que se le dice
al modelo, la forma de las respuestas, qué herramientas se le ofrecen y qué
acciones tienen que autorizarse siempre.

```bash
adelino vertical install robotics.json
```

Muestra lo que hace la vertical y pregunta antes de instalar. Una vertical
no concede nada y no se salta ninguna autorización; solo añade motivos para
preguntar.

```bash
adelino chat --vertical robotics
```

```bash
adelino vertical list
```

```bash
adelino vertical use robotics --session <id>
```

El registro dice bajo qué vertical y versión se dio cada respuesta. Si el
archivo instalado cambia, una sesión que lo use se detiene antes de volver a
preguntar al modelo, hasta que ejecutes `adelino vertical use` para aceptar
la nueva versión. `--none` saca la sesión de la vertical.

### La vertical de ingeniería

Adelino tiene una vertical propia, para robótica, drones, aprendizaje
automático (machine learning), matemáticas, física y computación cuántica.
Una vez instalada (consulta `adelino vertical install`), se usa por su
nombre:

```bash
adelino chat --vertical engineering
```

Dentro de ella:

- Nada se modifica antes de que el trabajo esté escrito como plan.
- Hay tres niveles de actividad: **simulación**, **banco** (hardware
  conectado, sin poder moverse) y **campo** (el vehículo se mueve o vuela).
  Una sesión empieza en simulación.
- Para subir, Adelino te pregunta cada elemento de la lista de verificación
  de ese nivel y después te muestra todas tus respuestas de una vez. Eso,
  exactamente, es lo que autorizas o rechazas. El banco dura 8 horas y el
  campo 2 horas; después la sesión vuelve a la simulación.
- Grabar firmware en un controlador se rechaza en simulación. Los programas
  que pueden controlar hardware preguntan siempre.
- El código Python se ejecuta en el entorno propio del proyecto, en
  `.venv-adelino` dentro de la carpeta. Instalar paquetes pregunta siempre, y
  muestra cuáles.

Lo que no puede hacer: leer una respuesta y evaluarla. Las unidades, las
fuentes, aquello con lo que se niega a ayudar, y no dar nada destinado a un
nivel superior, son instrucciones para el modelo. Una lista de verificación
registra lo que declaraste; no hace que un vuelo sea seguro ni legal. Eso
sigue siendo cosa tuya.

## Verificar un modelo: evaluaciones

Antes de confiar trabajo real a un modelo, pásalo por un conjunto de casos.

```bash
adelino eval <archivo>
```

Cada caso se ejecuta en una carpeta vacía solo suya, dentro de la vertical
que indica el archivo; las autorizaciones se responden automáticamente,
según defina cada caso. El resultado es un informe, `eval-report.md`, y lo
mismo en `eval-report.json`.

Un caso termina como superado, fallido, error o **por revisar**. Las
comprobaciones automáticas miran lo que se llamó, se rechazó y se escribió;
no pueden decir si una respuesta es correcta o segura. Por eso la mayoría de
los casos terminan "por revisar": el informe da la pregunta, el registro
completo y un espacio para que un especialista marque el veredicto. El
comando falla si algún caso falla, para poder frenar una versión.

Ejecutar contra un modelo de pago cuesta dinero. Define antes un límite:
`adelino usage limit set --day --usd 2`.

## Cuánto cuesta, y un límite de gasto

Cada llamada a un modelo queda registrada con los tokens que usó y lo que
costó.

```bash
adelino usage
```

Muestra el total y después por sesión, por modelo y por día. Un modelo que
no esté en la lista de precios (uno que se ejecute en tu ordenador, por
ejemplo) aparece como sin precio; el coste no se cuenta y no se estima nada.
Los precios están en `prices.json` en la carpeta de datos, si pones uno ahí;
si no, se usa la lista que venía con Adelino. El informe dice qué versión se
usó.

Un límite frena a Adelino antes de que vuelva a preguntar al modelo:

```bash
adelino usage limit set --day --usd 5
```

```bash
adelino usage limit set --session <id> --calls 40 --action warn
```

Un límite es para una sesión, para un día entero (los días cuentan en UTC)
o para cada ejecución de una petición programada (`--schedule <id>`). Puede
ser en dólares, tokens o llamadas al modelo. `stop` es el comportamiento por
defecto; `warn` avisa una vez y continúa. Una sesión frenada continúa con
`adelino resume` después de subir o retirar el límite.

## Móviles y la clave de firma

Un móvil emparejado puede conversar con el Adelino de tu ordenador y aprobar
acciones. El móvil crea su propia clave, así que una aprobación dada por él
no puede ser falsificada por el ordenador.

```bash
adelino serve
```

(o mantén la aplicación abierta)

```bash
adelino pair --host 192.168.1.20
```

Muestra un enlace y, en un terminal, un código QR. Funciona una vez,
durante cinco minutos. En la aplicación del móvil, pega el enlace en la
primera pantalla. La aplicación todavía no lee el código con la cámara; otro
lector de QR puede leerlo y darte el enlace para pegar.

Revocar un dispositivo (`adelino devices --revoke <clave>`) también cierra la
conexión que tenga abierta.

```bash
adelino devices
```

```bash
adelino devices --revoke <clave>
```

`adelino remote …` hace el papel de un móvil desde otro terminal.

Tu instalación tiene su propia clave de firma. Para sustituirla:

```bash
adelino key rotate
```

El cambio queda registrado, firmado con la clave antigua. Los eventos
anteriores siguen siendo verificables; la clave antigua deja de poder
escribir o aprobar. Después reinicia `adelino serve` y la aplicación.

```bash
adelino account status
```

Hoy no existe cuenta, y no se envía nada a ninguna parte.

## Cuando algo sale mal

```bash
adelino doctor
```

Muestra dónde guarda Adelino los archivos, su clave, el tamaño del
registro, si los comandos pueden confinarse en este ordenador y si el mapa
del proyecto está instalado.

| Lo que ves | Lo que significa |
|---|---|
| `no Anthropic API key` | Ejecuta `adelino init`, o define `ANTHROPIC_API_KEY`. |
| `this session has actions waiting` | Responde con `adelino approvals` y después `adelino resume`. |
| `FAIL at seq N: …` | El registro se alteró en el evento N. Nada de lo que viene después es de confianza. |
| `written by another device key` | El archivo de la clave no corresponde a este registro. Restaura el `device.key` correcto. |
| `the signing key was rotated; restart Adelino` | Un `serve` o la aplicación todavía tiene la clave antigua. |
| `none` en la línea sandbox de `adelino doctor` | Aquí los comandos no pueden confinarse. Adelino pregunta antes de cada uno. |
| `marked interrupted, not re-run` | Adelino se detuvo a mitad de un comando. No lo vuelve a ejecutar por sí solo. |

## Referencia de comandos

Todos los comandos aceptan `--help`.

| Comando | Lo que hace |
|---|---|
| `adelino init` | Configuración inicial: conectar un modelo |
| `adelino demo` | Visita guiada, sin modelo |
| `adelino chat` | Conversar con el agente |
| `adelino run "<petición>"` | Una petición y termina |
| `adelino resume <sesión>` | Continuar una sesión en espera o interrumpida |
| `adelino sessions` | Listar sesiones y lo que ha consumido cada una |
| `adelino mode <sesión> <modo>` | Cambiar el modo de una sesión |
| `adelino approvals` | Acciones en espera de decisión |
| `adelino approve <petición>` | Autorizar una acción en espera |
| `adelino deny <petición>` | Rechazar una acción en espera |
| `adelino grant …` | Lo que puede ejecutarse sin preguntar (`path`, `command`, `domain`, `tool`, `list`, `revoke`) |
| `adelino verify` | Verificar el registro; `--file` verifica un archivo exportado; `--witness` verifica un punto que guardó otro dispositivo |
| `adelino replay <sesión>` | La sesión tal como ocurrió |
| `adelino log` | Eventos en bruto |
| `adelino undo <llamada>` | Restaurar un archivo que Adelino escribió |
| `adelino export <sesión> -o <archivo>` | Una sesión en un archivo verificable |
| `adelino redact <hash>` | Borrar un contenido guardado |
| `adelino memory …` | Hechos memorizados (`list`, `add`, `search`) |
| `adelino eval <archivo>` | Ejecuta un conjunto de casos de evaluación contra el modelo y escribe un informe; `-o` para el destino |
| `adelino plan` | Las peticiones de una sesión, cada una con su plan y cómo terminó; `--session <id>` |
| `adelino vertical …` | Áreas empaquetadas como datos (`install`, `list`, `use`); empieza una sesión en una con `--vertical <nombre>` |
| `adelino usage` | Lo que han usado y costado las llamadas al modelo, por sesión, modelo y día; `--session <id>` para una sola |
| `adelino usage limit …` | Límites de gasto (`set`, `list`, `remove`) para una sesión, un día o una petición programada |
| `adelino graph …` | El mapa del proyecto (`install`, `index`, `query`, `explain`, `path`) |
| `adelino skill …` | Notas tomadas de un libro o documento tuyo (`add`, `list`, `show`, `on`, `off`, `remove`, `export`) |
| `adelino mcp …` | Servidores de herramientas (`add`, `list`, `trust`, `remove`) |
| `adelino pack install <archivo>` | Instalar un pack de servidores |
| `adelino schedule …` | Tareas programadas (`add`, `list`, `remove`) |
| `adelino serve` | Dejar que se conecten los móviles emparejados |
| `adelino pair --host <dirección>` | Crear un enlace de emparejamiento |
| `adelino devices` | Dispositivos emparejados; `--revoke` quita uno |
| `adelino remote …` | Hacer el papel de un dispositivo emparejado |
| `adelino key rotate` | Sustituir la clave de firma |
| `adelino account status` | Si hay una cuenta en uso |
| `adelino doctor` | Lo que está activo en este ordenador |
