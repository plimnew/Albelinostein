# Perguntas frequentes

A versão 0.1.0 foi publicada a 26 de novembro de 2026. As respostas
descrevem o programa tal como foi desenhado e testado; ver o
[estado das versões](../README.md#estado-das-versões).

## Os meus dados saem do meu computador?

Só se escolher. O Adelino corre no seu computador e funciona com um modelo
local e sem rede. Se ligar um modelo remoto, o que enviar ao modelo segue
para o fornecedor desse modelo. Pedidos de rede feitos pelo agente
perguntam antes, e perguntam sempre depois de a sessão ter lido conteúdo de
fora. Hoje não existe conta, e nada é enviado para a pLim.

## Que modelos posso usar?

Claude, através da API da Anthropic, ou qualquer servidor compatível com a
API da OpenAI a correr no seu computador (por exemplo Ollama, LM Studio,
llama.cpp ou vLLM), para que nada saia da máquina. Uma ressalva honesta: as
chamadas a um modelo verdadeiro ainda não foram verificadas; foram testadas
contra um servidor de substituição, ao nível do protocolo.

## Quanto custa?

Não há preço publicado neste repositório e nenhuma versão está à venda. O que
se pode dizer: cada chamada a um modelo remoto custa o que o fornecedor
cobrar, e o Adelino regista os tokens e o custo estimado de cada chamada. Um
modelo local não tem custo contado. Pode definir um limite de gasto por
sessão, por dia ou por pedido agendado, que pára o Adelino antes de voltar a
perguntar ao modelo.

## O que fica registado?

Cada mensagem, cada ação proposta, a decisão da política, cada aprovação ou
recusa (assinada), o resultado e o custo de cada chamada. O registo é
assinado e encadeado por somas, por isso uma alteração posterior é detetada.
O conteúdo (mensagens, argumentos, resultados) fica guardado à parte e pode
ser apagado com `adelino redact` sem invalidar o registo. As chaves e os
segredos não são escritos no registo.

## Como sei que o registo não foi alterado?

Com `adelino verify`: confirma cada evento, a ligação ao anterior, a
assinatura de quem o escreveu e a de quem decidiu. Se algo foi alterado,
pára no primeiro evento errado e indica-o. Também pode exportar uma sessão
para um ficheiro que qualquer pessoa verifica noutra máquina, sem aceder aos
seus dados.

## Posso desfazer o que o Adelino fez?

As escritas de ficheiros, sim: `adelino undo` repõe a versão anterior ou
apaga o ficheiro que ele criou. Recusa se o ficheiro foi alterado depois,
a não ser que force. Comandos e pedidos de rede não se desfazem, e por isso
perguntam mais vezes.

## O Adelino pode apagar ou estragar os meus ficheiros sem eu saber?

Por omissão não age sozinho: no modo predefinido só lê dentro da pasta de
trabalho e pergunta por tudo o resto. Nada de irreversível corre sem uma
decisão humana assinada. Um comando aprovado corre confinado (no Linux) à
pasta de trabalho e sem rede. Mas uma aprovação é uma assinatura sua sobre o
que lhe foi mostrado: ler o que vai correr continua a ser consigo.

## O que são skills?

Notas que o Adelino escreve, com um modelo, a partir de um livro, artigo ou
manual seu (PDF, EPUB, Word, HTML, Markdown ou texto), capítulo a capítulo,
para as poder consultar depois. Mostra o custo estimado antes de enviar o que
quer que seja e só avança com a sua concordância. Uma skill não dá
permissão nenhuma, o seu texto é tratado como não fiável e as notas podem
estar erradas. O Adelino nunca procura nem descarrega livros.

## Que sistemas são suportados?

Estão publicados o Linux (x86_64: `.deb`, `.rpm`, AppImage e programa de
linha de comandos), o macOS para Apple Silicon (`.dmg`, versão 0.1.1,
assinado com Developer ID e notarizado pela Apple; não funciona em Macs com
processador Intel) e o Android (APK, arm64). O Linux está validado pelo guião
de aceitação. macOS, Windows, Android e iOS estão por validar. O Windows
(ainda sem certificado de assinatura de código) e o iOS (ainda sem receita de
compilação assinada) não estão publicados. No Windows e no
macOS ainda não há sandbox de comandos: cada comando pergunta.

## Posso aprovar ações a partir do telemóvel?

O protocolo está construído e testado: um telemóvel emparelhado tem a sua
própria chave e a sua aprovação é uma assinatura que o computador não
consegue forjar. Mas a aplicação de telemóvel ainda não foi corrida num
aparelho verdadeiro, e a leitura do código QR com a câmara ainda não está
construída.

## O código é aberto?

A licença é a Apache-2.0: o Albelinostein pode ser usado gratuitamente para
qualquer fim, incluindo comercial. O código-fonte ainda não está publicado.
Este repositório existe para publicar o estado das versões, o guia, o
documento técnico e as chaves para verificar os downloads, que estão na
página de downloads do site
(<https://albelino.plimswitzzer.com/downloads/>). Os binários são
distribuídos pela pLim.

## Como verifico um download?

Cada versão tem uma lista de somas (`SHA256SUMS`) assinada com uma chave
GPG, cuja parte pública está publicada em `docs/keys/albelino-releases.asc`
e em <https://albelino.plimswitzzer.com/downloads/releases/public-key.asc>.
Passo a passo em [verificar.pt.md](verificar.pt.md). As Releases deste
repositório não têm nenhuma versão: os downloads estão só na página de
downloads do site.

## Onde obtenho suporte?

Na comunidade do Discord, <https://discord.gg/AyZHZPtkHk>, onde a equipa responde: perguntas
em #help e erros em #bug-reports. Vulnerabilidades de segurança nunca no
Discord: comunique-as em privado no GitHub, em «Report a vulnerability»;
ver [SECURITY.md](../SECURITY.md).
