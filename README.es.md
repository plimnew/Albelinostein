[Português](README.md) · [English](README.en.md) · [Español](README.es.md)

# Albelinostein

**El agente de IA que puede demostrar lo que hizo.**

**Albelinostein** es la aplicación para ordenador y móvil, y también el nombre de su mascota.
Por debajo funciona **Adelino**, el motor y el programa de línea de comandos (`adelino`).

Adelino es un agente de IA local: funciona en su ordenador, lee y escribe
archivos, ejecuta comandos y llama a servicios, como otros agentes. La diferencia
está en lo que rodea cada acción, para que después sea posible demostrar
qué ocurrió y quién lo autorizó.

<p>
  <img src="docs/img/albelinostein-3d-bicicleta.webp" alt="Albelinostein, la mascota, en bicicleta" width="300">
</p>

<p>
  <img src="docs/img/approval.png" alt="Albelinostein pidiendo permiso antes de ejecutar un comando: qué se va a ejecutar, el riesgo, por qué pregunta y cómo queda confinado el comando" width="560">
</p>

Este repositorio es público para que se pueda encontrar el producto: qué es,
el estado de cada versión compilada, cómo verificar una descarga, la guía de uso
y el whitepaper. El código fuente no es público.

Sitio web: <https://albelino.plimswitzzer.com>

## Las cinco garantías

1. **Una política determinista decide** si una acción puede ejecutarse. Nunca un
   segundo modelo.
2. **Una firma humana la aprueba** cuando importa, desde el terminal o desde un
   móvil emparejado que guarda su propia clave.
3. **Un registro firmado y encadenado por hashes lo anota todo.** Si después se
   altera un solo byte, `adelino verify` señala el evento exacto.
4. **Un sandbox del sistema operativo confina los comandos.** Un comando aprobado
   sigue sin poder escribir fuera de su proyecto ni acceder a la red.
5. **Deshacer** restaura cualquier archivo que Adelino haya escrito.

También tiene memoria entre sesiones, un mapa de la carpeta de trabajo y
*skills* creadas a partir de libros y documentos que aporta la persona. Consulte
el [whitepaper](docs/whitepaper.es.md).

## Estado de las versiones

La versión 0.1.0 se publicó el 26 de noviembre de 2026, **solo en la página de
descargas del sitio web**.

| Sistema | Estado |
|---|---|
| Linux: `.deb`, `.rpm`, AppImage y programa de línea de comandos | Validado por las pruebas de aceptación; publicado |
| macOS | No publicado: aún no hay cuenta de desarrollador de Apple (Developer ID y notarización) |
| Windows | No publicado: aún no hay certificado de firma de código para Windows |
| Android | Publicado: APK (arm64) |
| iOS | No publicado: aún no hay receta de compilación firmada |

Cada archivo publicado tiene su propio archivo de firma (`.asc`). La lista de
sumas y su firma están en <https://albelino.plimswitzzer.com/downloads/releases/0.1.0/SHA256SUMS>
y <https://albelino.plimswitzzer.com/downloads/releases/0.1.0/SHA256SUMS.asc>.

## Dónde están las descargas

- En la página de descargas del sitio web: <https://albelino.plimswitzzer.com/es/downloads/>
- Las **Releases** de este repositorio (GitHub) no tienen ninguna versión: la
  lista está vacía.

Archivos publicados. Linux (x86_64): `Albelino_0.1.0_x86_64.AppImage`,
`albelino_0.1.0_amd64.deb`, `albelino-0.1.0-1.x86_64.rpm` y el programa de
línea de comandos `adelino-cli-0.1.0-x86_64-unknown-linux-gnu.tar.gz`.
Android (arm64): `Albelino_0.1.0_arm64-v8a.apk`.

Esta tabla y la página de descargas son la fuente de verdad: si una versión
no aparece aquí, no existe.

## Cómo se verifica una descarga

1. Cada versión incluye un archivo `SHA256SUMS` con la suma SHA-256 de cada archivo.
2. `SHA256SUMS` está firmado con la clave GPG "Albelino Releases"; su parte
   pública está publicada en `docs/keys/albelino-releases.asc` (huella en
   `docs/keys/albelino-releases.fingerprint`) y en
   <https://albelino.plimswitzzer.com/downloads/releases/public-key.asc>.
3. Se comprueba la firma de la lista (`gpg --verify`) y después la suma del
   archivo (`sha256sum --check`).

Paso a paso: [docs/verificar.es.md](docs/verificar.es.md).

## Documentación

- [Guía de uso](docs/guia.es.md) ([Português](docs/guia.pt.md), [English](docs/guide.en.md))
- [Whitepaper](docs/whitepaper.es.md) ([Português](docs/whitepaper.pt.md), [English](docs/whitepaper.en.md))
- [Preguntas frecuentes](docs/faq.es.md) ([Português](docs/faq.pt.md), [English](docs/faq.en.md))
- [Cómo verificar una descarga](docs/verificar.es.md) ([Português](docs/verificar.pt.md), [English](docs/verify.en.md))
- [Seguridad](SECURITY.md)
- [Registro de cambios](CHANGELOG.md)

## Soporte

En la comunidad de Discord, donde el equipo responde: <https://discord.gg/AyZHZPtkHk>. Dudas en
#help, errores en #bug-reports, ideas en #ideas.

Las vulnerabilidades de seguridad, nunca en Discord: infórmalas en privado en
GitHub, en «Report a vulnerability» (<https://github.com/plimnew/Albelinostein/security/advisories/new>). Ver [SECURITY.md](SECURITY.md).

## Licencia

Binarios distribuidos por pLim. Todos los derechos reservados.
