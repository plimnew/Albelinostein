# Como verificar um download

Serve para confirmar que o ficheiro que descarregou é o original, publicado
pelo Albelinostein, e que ninguém o alterou pelo caminho.

> **Estado atual.** Ainda não foi publicada nenhuma versão e nenhum ficheiro
> está assinado. As chaves de assinatura ainda não foram geradas. Este
> documento descreve como a verificação vai funcionar quando a versão 0.1.0
> for publicada. Ver o [estado das versões](../README.md#estado-das-versões).

## O que prova o quê

| Verificação | O que prova |
|---|---|
| Soma SHA-256 igual à do `SHA256SUMS` | O ficheiro chegou inteiro e é o que está na lista |
| Assinatura do `SHA256SUMS` (`SHA256SUMS.asc`) | A lista foi feita por quem tem a chave «Albelino Releases» |
| As duas juntas | O ficheiro é o original |

A soma sozinha não chega: quem alterasse um ficheiro num servidor podia
alterar também a lista. É a assinatura que fecha o círculo.

## Onde ficam a chave e os ficheiros

A chave pública será publicada neste repositório, em `docs/keys/`, com a
primeira versão. Até lá a pasta não existe e nenhum download pode ser
verificado. Quando existir, conterá:

| Ficheiro | O que é |
|---|---|
| `albelino-releases.asc` | A chave pública GPG «Albelino Releases», que assina o `SHA256SUMS` e cada ficheiro nele listado |
| `albelino-releases.fingerprint` | A sua impressão digital, para comparar depois de importar |
| `android-cert-sha256.txt` | A soma SHA-256 do certificado que assina o APK do Android |

Cada versão publicada terá o seu `SHA256SUMS` e o `SHA256SUMS.asc` junto
dos ficheiros, nas Releases deste repositório e na página de downloads do
site.

## Três passos

Precisa do `gpg` (Linux: já vem; macOS: `brew install gnupg`; Windows: Gpg4win).
Descarregue para a mesma pasta o ficheiro que quer, o `SHA256SUMS` e o `SHA256SUMS.asc`.

**1. Obter a chave pública, uma vez.** Quando existir, estará em
`docs/keys/albelino-releases.asc`, neste repositório.

```bash
gpg --import albelino-releases.asc
```

```bash
gpg --fingerprint "Albelino Releases"
```

A impressão digital mostrada tem de ser igual à de
`docs/keys/albelino-releases.fingerprint`. Se for diferente, pare: a chave
não é a nossa.

**2. Verificar a lista.**

```bash
gpg --verify SHA256SUMS.asc SHA256SUMS
```

Tem de dizer `Good signature from "Albelino Releases"`. O aviso de que a
chave "não é certificada por uma assinatura de confiança" é normal: quer
dizer só que foi o próprio a confirmar a impressão digital, no passo 1.

**3. Verificar o ficheiro.**

Linux:

```bash
sha256sum --check --ignore-missing SHA256SUMS
```

macOS:

```bash
shasum -a 256 --check --ignore-missing SHA256SUMS
```

Windows (PowerShell), comparando à mão com a linha do ficheiro em `SHA256SUMS`:

```powershell
Get-FileHash .\Albelino_0.1.0_x64-setup.exe -Algorithm SHA256
```

O ficheiro tem de aparecer com `OK`, ou com a mesma soma.

## Por plataforma

Cada ficheiro descarregável terá a sua própria assinatura GPG, com o nome
do ficheiro mais `.asc`, que se verifica sem a lista:
`gpg --verify albelino_0.1.0_amd64.deb.asc albelino_0.1.0_amd64.deb`.
Isto vale para os pacotes Linux, o instalador Windows, o `.apk`, o `.dmg` e
os programas de linha de comandos. (Pode haver ficheiros `.sig` ao lado de
alguns instaladores; são só para o atualizador da própria aplicação, não são
a assinatura do download e não é preciso fazer nada com eles.)

- **Linux** (`.deb`, `.rpm`, `.AppImage`, linha de comandos): verifique pelos
  três passos acima e, se quiser, também o `.asc` do pacote.
- **Android** (`.apk`): o Android só instala uma atualização assinada com a
  mesma chave da versão instalada. Para confirmar antes de instalar:
  `apksigner verify --print-certs Albelino_0.1.0_arm64-v8a.apk` e comparar o
  `SHA-256 digest` do certificado com `docs/keys/android-cert-sha256.txt`.
- **Windows** (`-setup.exe`): ainda **não** tem assinatura Authenticode. O
  Windows mostrará o aviso "editor desconhecido"; é esperado. Verifique pelos
  três passos acima.
- **macOS** (`.dmg`): ainda **não** está assinado com Developer ID nem
  notarizado. O macOS recusará abri-lo com um duplo clique; é esperado.
  Verifique pelos três passos acima.

Windows, macOS e Android são plataformas ainda por validar; ver o
[estado das versões](../README.md#estado-das-versões).

## Nomes dos ficheiros

| Ficheiro | Plataforma |
|---|---|
| `albelino_<versão>_amd64.deb` | Linux (Debian, Ubuntu) |
| `albelino-<versão>-1.x86_64.rpm` | Linux (Fedora, openSUSE) |
| `Albelino_<versão>_x86_64.AppImage` | Linux (qualquer) |
| `Albelino_<versão>_x64-setup.exe` | Windows |
| `Albelino_<versão>_arm64-v8a.apk` | Android |
| `Albelino_<versão>_<arquitetura>.dmg` | macOS |
| `adelino-cli-<versão>-<alvo>.tar.gz` ou `.zip` | Linha de comandos |

Um ficheiro com `debug` ou `unsigned` no nome não é uma versão publicada.

## Se algo não bater

Não instale. Apague o ficheiro e peça ajuda pelo
[formulário de suporte](https://albelino.plimswitzzer.com/suporte/).
