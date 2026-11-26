[Português](README.md) · [English](README.en.md) · [Español](README.es.md)

# Albelinostein

**O agente de IA que consegue provar o que fez.**

O **Albelinostein** é a aplicação para computador e telemóvel, e também o
nome da sua mascote. Por baixo corre o **Adelino**, o motor e o programa de
linha de comandos (`adelino`).

O Adelino é um agente de IA local: corre no seu computador, lê e escreve
ficheiros, executa comandos e chama serviços, como outros agentes. A
diferença é o que rodeia cada ação, para que, depois, seja possível provar o
que aconteceu e quem autorizou.

<p>
  <img src="docs/img/albelinostein-3d-bicicleta.webp" alt="O Albelinostein, a mascote, de bicicleta" width="300">
</p>

<p>
  <img src="docs/img/approval.png" alt="O Albelinostein a pedir autorização antes de executar um comando: o que vai correr, o risco, porque pergunta e como o comando fica confinado" width="560">
</p>

Este repositório é público e serve para encontrar o produto: o que é, o
estado de cada versão compilada, como verificar um download, o guia de
utilização e o documento técnico. O código-fonte não é público.

Site: <https://albelino.plimswitzzer.com>

## As cinco garantias

1. **Uma política determinística decide** se uma ação pode correr. Nunca um
   segundo modelo.
2. **Uma assinatura humana aprova** quando importa, no terminal ou num
   telemóvel emparelhado que tem a sua própria chave.
3. **Um registo assinado e encadeado por somas** guarda tudo. Se alterar um
   único byte mais tarde, `adelino verify` indica o evento exato.
4. **Uma sandbox do sistema operativo confina os comandos.** Um comando
   aprovado continua a não poder escrever fora do seu projeto nem usar a rede.
5. **Desfazer** repõe qualquer ficheiro que o Adelino tenha escrito.

Há ainda memória entre sessões, um mapa da pasta de trabalho e *skills*
criadas a partir de livros e documentos que a pessoa fornece. Ver o
[documento técnico](docs/whitepaper.pt.md).

## Estado das versões

A versão 0.1.0 foi publicada a 26 de novembro de 2026, **apenas na página de
downloads do site**.

| Sistema | Estado |
|---|---|
| Linux: `.deb`, `.rpm`, AppImage e programa de linha de comandos | Validado pelo guião de aceitação; publicado |
| macOS | Não publicado: ainda não há conta de programador da Apple (Developer ID e notarização) |
| Windows | Não publicado: ainda não há certificado de assinatura de código para Windows |
| Android | Publicado: APK (arm64) |
| iOS | Não publicado: ainda não há receita de compilação assinada |

Cada ficheiro publicado tem o seu ficheiro de assinatura (`.asc`). A lista de
somas e a sua assinatura estão em <https://albelino.plimswitzzer.com/downloads/releases/0.1.0/SHA256SUMS>
e <https://albelino.plimswitzzer.com/downloads/releases/0.1.0/SHA256SUMS.asc>.

## Onde estão os downloads

- Na página de downloads do site: <https://albelino.plimswitzzer.com/downloads/>
- As **Releases** deste repositório (GitHub) não têm nenhuma versão: a lista
  está vazia.

Ficheiros publicados. Linux (x86_64): `Albelino_0.1.0_x86_64.AppImage`,
`albelino_0.1.0_amd64.deb`, `albelino-0.1.0-1.x86_64.rpm` e o programa de
linha de comandos `adelino-cli-0.1.0-x86_64-unknown-linux-gnu.tar.gz`.
Android (arm64): `Albelino_0.1.0_arm64-v8a.apk`.

Esta tabela e a página de downloads são a fonte de verdade: se aqui não há
uma versão, ela não existe.

## Como se verifica um download

1. Cada versão traz um `SHA256SUMS` com a soma SHA-256 de cada ficheiro.
2. O `SHA256SUMS` é assinado com a chave GPG «Albelino Releases», cuja parte
   pública está publicada em `docs/keys/albelino-releases.asc` (impressão
   digital em `docs/keys/albelino-releases.fingerprint`) e em
   <https://albelino.plimswitzzer.com/downloads/releases/public-key.asc>.
3. Confirma-se a assinatura da lista (`gpg --verify`) e depois a soma do
   ficheiro (`sha256sum --check`).

Passo a passo: [docs/verificar.pt.md](docs/verificar.pt.md).

## Documentação

- [Guia de utilização](docs/guia.pt.md) ([English](docs/guide.en.md))
- [Documento técnico](docs/whitepaper.pt.md) ([English](docs/whitepaper.en.md))
- [Perguntas frequentes](docs/faq.pt.md)
- [Como verificar um download](docs/verificar.pt.md)
- [Segurança](SECURITY.md)
- [Registo de alterações](CHANGELOG.md)

## Suporte

Na comunidade do Discord, onde a equipa responde: <https://discord.gg/AyZHZPtkHk>. Dúvidas em #help,
erros em #bug-reports, ideias em #ideas.

Vulnerabilidades de segurança nunca no Discord: comunique-as em privado no
GitHub, em «Report a vulnerability» (<https://github.com/plimnew/Albelinostein/security/advisories/new>). Ver [SECURITY.md](SECURITY.md).

## Licença

Binários distribuídos pela pLim. Todos os direitos reservados.
