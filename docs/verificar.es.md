# Cómo verificar una descarga

Sirve para confirmar que el archivo que has descargado es el original,
publicado por Albelinostein, y que nadie lo ha alterado por el camino.

> **Estado actual.** La versión 0.1.0 se publicó el 26 de noviembre de 2026,
> solo en la página de descargas del sitio
> (<https://albelino.plimswitzzer.com/es/downloads/>). Cada archivo publicado
> tiene su archivo de firma (`.asc`) y la clave pública está
> publicada. Consulta el [estado de las versiones](../README.es.md).

## Qué prueba cada cosa

| Verificación | Qué prueba |
|---|---|
| Suma SHA-256 igual a la de `SHA256SUMS` | El archivo llegó completo y es el que figura en la lista |
| Firma de `SHA256SUMS` (`SHA256SUMS.asc`) | La lista la hizo quien tiene la clave «Albelino Releases» |
| Las dos juntas | El archivo es el original |

La suma sola no basta: quien alterase un archivo en un servidor podría
alterar también la lista. Es la firma la que cierra el círculo.

## Dónde están la clave y los archivos

La clave pública está publicada en este repositorio, en `docs/keys/`, y en el
sitio, en <https://albelino.plimswitzzer.com/downloads/releases/public-key.asc>.
La huella digital es 44E5 30D3 FCDF B80C 9EAB F680 5EB9 C85F 1DA4 BC91. La
carpeta contiene:

| Archivo | Qué es |
|---|---|
| `albelino-releases.asc` | La clave pública GPG «Albelino Releases», que firma el `SHA256SUMS` y cada archivo que figura en él |
| `albelino-releases.fingerprint` | Su huella digital, para comparar después de importarla |
| `android-cert-sha256.txt` | La suma SHA-256 del certificado que firma el APK de Android |

La versión 0.1.0 tiene su `SHA256SUMS` y el `SHA256SUMS.asc` en la página de
descargas del sitio
(<https://albelino.plimswitzzer.com/downloads/releases/0.1.0/SHA256SUMS> y
<https://albelino.plimswitzzer.com/downloads/releases/0.1.0/SHA256SUMS.asc>).
Las Releases de este repositorio no tienen ninguna versión.

## Tres pasos

Necesitas `gpg` (Linux: ya viene instalado; macOS: `brew install gnupg`;
Windows: Gpg4win). Descarga en la misma carpeta el archivo que quieres, el
`SHA256SUMS` y el `SHA256SUMS.asc`.

**1. Obtener la clave pública, una vez.** Está en
`docs/keys/albelino-releases.asc`, en este repositorio.

```bash
gpg --import albelino-releases.asc
```

```bash
gpg --fingerprint "Albelino Releases"
```

La huella digital mostrada tiene que ser igual a la de
`docs/keys/albelino-releases.fingerprint`. Si es diferente, detente: la clave
no es la nuestra.

**2. Verificar la lista.**

```bash
gpg --verify SHA256SUMS.asc SHA256SUMS
```

Tiene que decir `Good signature from "Albelino Releases"`. El aviso de que la
clave "no está certificada con una firma de confianza" es normal: solo
significa que fuiste tú quien confirmó la huella digital, en el paso 1.

**3. Verificar el archivo.**

Linux:

```bash
sha256sum --check --ignore-missing SHA256SUMS
```

macOS:

```bash
shasum -a 256 --check --ignore-missing SHA256SUMS
```

Windows (PowerShell), comparando a mano con la línea del archivo en `SHA256SUMS`:

```powershell
Get-FileHash .\Albelino_0.1.0_x64-setup.exe -Algorithm SHA256
```

El archivo tiene que aparecer con `OK`, o con la misma suma.

## Por plataforma

Cada archivo descargable tendrá su propia firma GPG, con el nombre del
archivo más `.asc`, que se verifica sin la lista:
`gpg --verify albelino_0.1.0_amd64.deb.asc albelino_0.1.0_amd64.deb`.
Esto vale para los paquetes de Linux, el instalador de Windows, el `.apk`, el
`.dmg` y los programas de línea de comandos. (Puede haber archivos `.sig`
junto a algunos instaladores; son solo para el actualizador de la propia
aplicación, no son la firma de la descarga y no hace falta hacer nada con
ellos.)

- **Linux** (`.deb`, `.rpm`, `.AppImage`, línea de comandos): verifica con
  los tres pasos anteriores y, si quieres, también el `.asc` del paquete.
- **Android** (`.apk`): Android solo instala una actualización firmada con la
  misma clave que la versión instalada. Para confirmarlo antes de instalar:
  `apksigner verify --print-certs Albelino_0.1.0_arm64-v8a.apk` y comparar el
  `SHA-256 digest` del certificado con `docs/keys/android-cert-sha256.txt`.
- **Windows** (`-setup.exe`): todavía **no** tiene firma Authenticode.
  Windows mostrará el aviso "editor desconocido"; es lo esperado. Verifica
  con los tres pasos anteriores.
- **macOS** (`.dmg`): la versión 0.1.0 no tiene `.dmg`. A partir de la 0.1.1,
  el `.dmg` es solo para Macs con **Apple Silicon** (M1 o posterior; no
  funciona en Macs con procesador Intel). Está firmado con el Developer ID
  del equipo de Apple `B4S6KAG56M` y notarizado por Apple: se abre con doble
  clic. Además de los tres pasos anteriores, en un Mac, en Terminal, en la
  carpeta de la descarga, sustituyendo FILE por el nombre del archivo:
  `xcrun stapler validate FILE` debe decir `The validate action worked!`, y
  `spctl -a -vv -t open --context context:primary-signature FILE` debe
  responder `accepted` y `source=Notarized Developer ID`, con `(B4S6KAG56M)`
  en la línea `origin`.

Windows, macOS y Android son plataformas aún sin validar; consulta el
[estado de las versiones](../README.es.md).

## Nombres de los archivos

| Archivo | Plataforma |
|---|---|
| `albelino_<versión>_amd64.deb` | Linux (Debian, Ubuntu) |
| `albelino-<versión>-1.x86_64.rpm` | Linux (Fedora, openSUSE) |
| `Albelino_<versión>_x86_64.AppImage` | Linux (cualquiera) |
| `Albelino_<versión>_x64-setup.exe` | Windows |
| `Albelino_<versión>_arm64-v8a.apk` | Android |
| `Albelino_<versión>_<arquitectura>.dmg` | macOS |
| `adelino-cli-<versión>-<destino>.tar.gz` o `.zip` | Línea de comandos |

Un archivo con `debug` o `unsigned` en el nombre no es una versión publicada.

## Si algo no coincide

No lo instales. Borra el archivo y comunícalo en privado en GitHub, en
[«Report a vulnerability»](https://github.com/plimnew/Albelinostein/security/advisories/new): una descarga que no coincide puede haber
sido manipulada.
