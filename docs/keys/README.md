# Chaves públicas · Public keys

As metades públicas das chaves que assinam as versões do Albelinostein. As
metades privadas nunca entram num repositório.

The public halves of the keys that sign Albelinostein releases. The private
halves never enter a repository.

| Ficheiro · File | O que é · What it is |
|---|---|
| `albelino-releases.asc` | Chave pública GPG «Albelino Releases»: assina o `SHA256SUMS` e cada ficheiro nele listado · GPG public key "Albelino Releases": signs `SHA256SUMS` and every file listed in it |
| `albelino-releases.fingerprint` | A impressão digital dessa chave, para comparar depois de importar · Its fingerprint, to compare after importing |
| `android-cert-sha256.txt` | SHA-256 do certificado que assina o APK de Android · SHA-256 of the certificate that signs the Android APK |
| `albelino-updater.pub` | Chave pública com que a aplicação verifica atualizações; não serve para verificar um download · Public key the app uses to check updates; not a download signature |

A mesma chave GPG está no site · The same GPG key is on the site:
<https://albelino.plimswitzzer.com/downloads/releases/public-key.asc>

Como verificar um download · How to verify a download:
[`docs/verificar.pt.md`](../verificar.pt.md)
