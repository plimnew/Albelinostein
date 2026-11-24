# Usar o Adelino

O Adelino é um agente de IA que trabalha nos seus ficheiros e pergunta
antes de alterar seja o que for. O que fez, e o que você decidiu, fica num
registo assinado que pode verificar a qualquer momento.

Este guia está organizado pelo que quer fazer. No fim há a referência de
todos os comandos. Em inglês: [guide.en.md](guide.en.md).

> O Adelino 0.1.0 está em preparação e ainda não foi publicado. Este guia
> descreve o programa tal como se vai comportar; as instruções de instalação
> serão acrescentadas com a primeira versão. Veja o
> [estado de cada versão](../README.md#estado-das-versões).

- [Começar](#começar)
- [Quanto pode fazer sozinho?](#quanto-pode-fazer-sozinho)
- [Responder quando pergunta](#responder-quando-pergunta)
- [O registo: verificar, repetir, desfazer, exportar](#o-registo-verificar-repetir-desfazer-exportar)
- [Memória e mapa do projeto](#memória-e-mapa-do-projeto)
- [Skills: notas de um livro](#skills-notas-de-um-livro)
- [Mais ferramentas: servidores e packs](#mais-ferramentas-servidores-e-packs)
- [Tarefas agendadas](#tarefas-agendadas)
- [Telemóveis e a chave de assinatura](#telemóveis-e-a-chave-de-assinatura)
- [Quando algo corre mal](#quando-algo-corre-mal)
- [Referência de comandos](#referência-de-comandos)

## Começar

```bash
adelino init
```

Liga um modelo. Se já existir uma chave da Anthropic no ambiente, ou um
servidor de modelos a correr neste computador, não pergunta nada. Caso
contrário faz uma pergunta: que modelo usar.

```bash
adelino demo
```

Visita de dois minutos com um assistente de demonstração. Não precisa de
modelo. Autoriza uma ação, recusa outra e vê o registo verificado.

```bash
adelino chat
```

Abre uma conversa na pasta atual. Essa pasta é a *pasta de trabalho*: o
Adelino pode ler dentro dela sem perguntar. Use `--workspace` para escolher
outra e `--mode` para escolher o grau de cuidado.

```bash
adelino run "resume o README"
```

Um pedido e termina. `adelino resume <sessão>` continua uma sessão que
ficou à espera ou foi interrompida; `adelino sessions` lista-as com o que
cada uma consumiu (chamadas ao modelo, tokens, custo quando conhecido, tempo).

## Quanto pode fazer sozinho?

Três modos, por sessão:

| Modo | Leituras na pasta de trabalho | Tudo o resto |
|---|---|---|
| `ask` (por omissão) | executa | pergunta |
| `read_only` | executa | recusa, sem perguntar |
| `autonomous` | executa | executa **só** se uma concessão o cobrir; senão pergunta |

```bash
adelino mode <sessão> autonomous
```

Uma *concessão* diz o que pode correr sem perguntar em modo `autonomous`.
São estreitas, podem ter prazo e limite de usos, e podem ser revogadas.

```bash
adelino grant path ./src --write --minutes 60
```

```bash
adelino grant command make --uses 20
```

```bash
adelino grant command git --args status
```

Esta última permite `git status …` mas não `git push`. A comparação é por
argumentos inteiros, a partir do início.

```bash
adelino grant domain api.example.com
```

```bash
adelino grant list
```

```bash
adelino grant revoke <id>
```

Três coisas perguntam sempre, em qualquer modo e com qualquer concessão:

- tudo o que não se pode desfazer;
- pedidos de rede e comandos, depois de a sessão ter lido conteúdo de fora
  (uma página web, um ficheiro fora da pasta de trabalho, um servidor de
  ferramentas);
- qualquer comando, quando este computador não o consegue confinar (ver
  `adelino doctor`).

## Responder quando pergunta

No terminal, o Adelino mostra o que vai correr, o risco, porque pergunta e
como o comando fica confinado, e espera por `y` ou `n`. Na aplicação aparece
o mesmo numa folha com **Autorizar** e **Recusar**.

Se ninguém responder, a ação fica à espera:

```bash
adelino approvals
```

```bash
adelino approve <pedido>
```

```bash
adelino deny <pedido>
```

```bash
adelino resume <sessão>
```

`<pedido>` são os primeiros caracteres do pedido mostrado na lista. Uma
ação à espera sobrevive a um reinício e a uma falha.

A sua resposta é assinada. Uma recusa fica registada com a mesma firmeza
que uma autorização.

## O registo: verificar, repetir, desfazer, exportar

```bash
adelino verify
```

Verifica cada evento: que não foi alterado, que segue o anterior, que foi
assinado por esta instalação, e que cada decisão foi assinada por um
dispositivo autorizado a decidir. Se algo foi alterado, pára no primeiro
evento errado e indica-o.

```bash
adelino replay <sessão>
```

Mostra a sessão tal como aconteceu, reconstruída só a partir do registo.

```bash
adelino undo <chamada>
```

Repõe um ficheiro que o Adelino escreveu. O identificador da chamada
aparece junto de cada ação. Recusa se você alterou o ficheiro depois, a
não ser que acrescente `--force`. Só escritas de ficheiros se desfazem;
comandos e pedidos de rede não, e por isso são tratados como mais arriscados.

```bash
adelino export <sessão> -o sessao.json
```

```bash
adelino verify --file sessao.json
```

Um ficheiro que qualquer pessoa pode verificar em qualquer máquina com o
Adelino instalado, sem acesso aos seus dados. Contém toda a cadeia de
eventos (resumos e metadados) e o conteúdo apenas dessa sessão.

```bash
adelino redact <hash>
```

Apaga um conteúdo guardado (uma mensagem, a versão antiga de um ficheiro).
O registo continua a verificar; o conteúdo desaparece de vez.

```bash
adelino log -n 50
```

Os eventos em bruto.

## Memória e mapa do projeto

O Adelino pode memorizar factos entre sessões. Cada facto aponta para o
evento que o criou.

```bash
adelino memory list
```

```bash
adelino memory add "As faturas ficam por mês em /financas"
```

```bash
adelino memory search faturas
```

O *mapa do projeto* permite ao Adelino perguntar como os ficheiros de um
projeto se ligam, em vez de os ler um a um. É construído neste computador
pelo [Graphify](https://github.com/Graphify-Labs/graphify), offline, e
guardado fora do seu projeto.

```bash
adelino graph install
```

```bash
adelino graph index
```

```bash
adelino graph query "o que chama o módulo de pagamentos?"
```

## Skills: notas de um livro

Uma *skill* é um conjunto de notas que o Adelino escreve a partir de um
documento que é seu, como um livro, um artigo ou um manual, para depois
poder consultá-lo. Lê ficheiros PDF, EPUB, Word (docx), HTML, Markdown e
texto por si; não há nada a instalar. O Adelino nunca procura nem
descarrega livros.

```bash
adelino skill add ddia.pdf --name ddia --mode study --focus "frameworks, trade-offs, architecture patterns and anti-patterns"
```

Antes de enviar seja o que for, mostra o título, o número de capítulos,
qualquer aviso do leitor, quantas chamadas ao modelo e quantos tokens vai
gastar e o custo estimado (ou que não há preço registado). Diz que o
documento segue para o modelo capítulo a capítulo e pergunta
`start? [y/N]`; com `--yes` não pergunta. `--mode study` explica cada ideia
e termina cada capítulo com perguntas; `--mode reference` (o predefinido) é
seco, para consulta. `--kind prose` serve para texto sem tabelas nem código;
`--kind technical` (o predefinido) mantém tabelas, fórmulas e código tal
como estão.

```bash
adelino skill list
```

```bash
adelino skill show ddia 3
```

Sem número de capítulo, `show` mostra o índice. `adelino skill off ddia`
guarda a skill mas o Adelino deixa de a consultar, `on` volta a ligá-la,
`remove` apaga-a e `export ddia -o ddia.zip` escreve-a num só ficheiro.

O que convém saber:

- Uma skill é material de consulta. Não dá permissão nenhuma e não dispensa
  nenhuma aprovação.
- O texto vem de um documento, por isso não é de confiança. O Adelino só o
  lê através de uma ferramenta que o marca como dados, e depois de o ler a
  sessão deixa de ser de confiança: a partir daí todos os comandos e todos
  os pedidos de rede são perguntados, com qualquer concessão (a mesma regra
  das páginas web e dos ficheiros fora da pasta de trabalho).
- Só o que você escreveu chega às instruções permanentes do Adelino: o nome
  da skill, o nome do ficheiro e o foco. As notas nunca chegam.
- Cada chamada ao modelo fica registada numa sessão própria, com o custo, e
  pára num limite de gasto como qualquer outra. As notas já escritas ficam
  guardadas, por isso tentar de novo depois de uma paragem ou de uma falha
  não se paga duas vezes.
- Juntar, remover e ligar ou desligar uma skill ficam no registo assinado;
  ao juntar guarda-se também o SHA-256 do documento de origem.
- As skills ficam na pasta de dados do Adelino, em `skills/<nome>/`, como
  `SKILL.md`, `chapters/` e `skill.json` (o formato aberto Agent Skills),
  por isso uma skill exportada serve a outros agentes.
- O texto dos PDF é lido sem esquema de página, pelo que tabelas, fórmulas
  e código podem perder a forma; o leitor avisa sempre. Um PDF só é
  dividido em capítulos onde uma linha como "Capítulo 3" começa um; senão é
  cortado em partes pelo tamanho. PDF digitalizados,
  sem texto, são recusados. As notas são escritas pelo modelo e podem estar
  erradas: dizem o que ele percebeu de cada capítulo.
- A aplicação tem uma página de Skills. Escolha "Adicionar documento", abra o
  ficheiro com o seletor do sistema e dê um nome à skill. O Adelino mostra
  primeiro quanto espera gastar, antes de enviar o que quer que seja; confirme
  para começar e pare quando quiser. Um limite de gasto pára o trabalho como
  qualquer outro. A página lista as suas skills e deixa ler as notas, ligá-las
  e desligá-las, removê-las e exportar uma para um ficheiro `.zip`. Não está
  disponível num telemóvel emparelhado.

## Mais ferramentas: servidores e packs

O Adelino pode usar ferramentas oferecidas por outros programas através do
Model Context Protocol (MCP). Um servidor é um programa arrancado neste
computador ou um endereço acedido por HTTP.

```bash
adelino mcp add notas -- npx -y some-mcp-server ~/notas
```

```bash
adelino mcp add pesquisa --url https://mcp.example.com/mcp
```

```bash
adelino mcp list
```

Cada ferramenta de um servidor pergunta **sempre** até lhe dar uma classe:

```bash
adelino mcp trust notas read_file read
```

Uma ferramenta com a classe `read` pode então ser coberta por uma concessão
(`adelino grant tool mcp__notas__read_file`); sem concessão continua a
perguntar. Se o servidor alterar mais tarde a descrição ou as entradas de
uma ferramenta, ela volta a perguntar sempre e a pergunta seguinte avisa que
mudou. O que uma ferramenta devolve é tratado como conteúdo de fora.

```bash
adelino mcp remove notas
```

Um *pack* é um ficheiro que lista servidores e as classes que o autor propõe.

```bash
adelino pack install notas.json
```

Mostra cada programa que iria correr e cada classe que propõe, e pergunta
primeiro. Instalar não concede nada.

## Tarefas agendadas

```bash
adelino schedule add "Resume o que mudou nesta pasta" --every 6h
```

```bash
adelino schedule list
```

```bash
adelino schedule remove <id>
```

Uma tarefa agendada corre enquanto `adelino serve` ou a aplicação estiver
aberta. Corre sempre em modo `ask`: pára na primeira coisa que precise da
sua autorização e espera. Não volta a correr enquanto uma estiver à sua espera.

## Pedidos, planos e etapas

Cada coisa que pede fica registada como um pedido, da sua mensagem ao fim
da resposta. Em trabalhos com várias etapas o Adelino escreve primeiro um
plano e vai marcando cada etapa.

```bash
adelino plan
```

Mostra todos os pedidos da sessão mais recente: se está a correr, à sua
espera, concluído, travado por um limite de gasto ou falhado; o plano com o
estado de cada etapa; e quantas ações fez. Um plano não autoriza nada: cada
ação continua a ser verificada e pode continuar a pedir-lhe autorização.

## Verticais: trabalhar dentro de uma área

Uma vertical é um ficheiro que ajusta o Adelino a uma área: o que é dito ao
modelo, a forma das respostas, que ferramentas lhe são oferecidas e que
ações têm sempre de ser autorizadas.

```bash
adelino vertical install robotics.json
```

Mostra o que a vertical faz e pergunta antes de instalar. Uma vertical não
concede nada e não salta nenhuma autorização; só acrescenta motivos para
perguntar.

```bash
adelino chat --vertical robotics
```

```bash
adelino vertical list
```

```bash
adelino vertical use robotics --session <id>
```

O registo diz sob que vertical e versão cada resposta foi dada. Se o
ficheiro instalado mudar, uma sessão que o use para antes de voltar a
perguntar ao modelo, até correr `adelino vertical use` para aceitar a nova
versão. `--none` retira a sessão da vertical.

### A vertical de engenharia

O Adelino tem uma vertical própria, para robótica, drones, machine learning,
matemática, física e computação quântica. Depois de instalada (ver
`adelino vertical install`), usa-se pelo nome:

```bash
adelino chat --vertical engineering
```

Dentro dela:

- Nada é alterado antes de o trabalho estar escrito como plano.
- Há três níveis de atividade: **simulação**, **bancada** (hardware ligado,
  sem se poder mover) e **campo** (o veículo move-se ou voa). Uma sessão
  começa em simulação.
- Para subir, o Adelino pergunta-lhe cada item da lista de verificação
  desse nível e depois mostra-lhe todas as suas respostas de uma vez. É
  isso, exatamente, que autoriza ou recusa. A bancada dura 8 horas e o
  campo 2 horas; depois a sessão volta à simulação.
- Gravar firmware num controlador é recusado em simulação. Programas que
  podem comandar hardware perguntam sempre.
- O código Python corre no ambiente próprio do projeto, em `.venv-adelino`
  dentro da pasta. Instalar pacotes pergunta sempre, e mostra quais.

O que não consegue fazer: ler uma resposta e avaliá-la. Unidades, fontes,
o que se recusa a ajudar a fazer, e não dar nada destinado a um nível
acima, são instruções para o modelo. Uma lista de verificação regista o que
declarou; não torna um voo seguro nem legal. Isso continua a ser seu.

## Verificar um modelo: avaliações

Antes de confiar trabalho real a um modelo, passe-o por um conjunto de casos.

```bash
adelino eval <ficheiro>
```

Cada caso corre numa pasta vazia só sua, dentro da vertical que o ficheiro
indica; as autorizações são respondidas automaticamente, como cada caso
define. O resultado é um relatório, `eval-report.md`, e o mesmo em
`eval-report.json`.

Um caso termina como passou, falhou, erro, ou **a rever**. As verificações
automáticas olham para o que foi chamado, recusado e escrito; não conseguem
dizer se uma resposta está certa ou é segura. Por isso a maior parte dos
casos termina "a rever": o relatório dá a pergunta, o registo completo e um
espaço para um especialista marcar o veredicto. O comando falha se algum
caso falhar, para poder ficar à frente de uma versão.

Correr contra um modelo pago custa dinheiro. Defina primeiro um limite:
`adelino usage limit set --day --usd 2`.

## Quanto custa, e um limite de gasto

Cada chamada a um modelo fica registada com os tokens que usou e o que custou.

```bash
adelino usage
```

Mostra o total e depois por sessão, por modelo e por dia. Um modelo que não
esteja na lista de preços (um que corra no seu computador, por exemplo)
aparece como sem preço; o custo não é contado e nada é estimado. Os preços
estão em `prices.json` na pasta de dados, se lá puser um; caso contrário usa
a lista que veio com o Adelino. O relatório diz que versão foi usada.

Um limite trava o Adelino antes de voltar a perguntar ao modelo:

```bash
adelino usage limit set --day --usd 5
```

```bash
adelino usage limit set --session <id> --calls 40 --action warn
```

Um limite é para uma sessão, para um dia inteiro (os dias contam em UTC) ou
para cada execução de um pedido agendado (`--schedule <id>`). Pode ser em
dólares, tokens ou chamadas ao modelo. `stop` é o comportamento por omissão;
`warn` avisa uma vez e continua. Uma sessão travada continua com
`adelino resume` depois de subir ou retirar o limite.

## Telemóveis e a chave de assinatura

Um telemóvel emparelhado pode conversar com o Adelino do seu computador e
aprovar ações. O telemóvel cria a sua própria chave, por isso uma aprovação
dada por ele não pode ser forjada pelo computador.

```bash
adelino serve
```

(ou mantenha a aplicação aberta)

```bash
adelino pair --host 192.168.1.20
```

Mostra uma ligação e, num terminal, um código QR. Funciona uma vez, durante
cinco minutos. Na aplicação do telemóvel, cole a ligação no primeiro ecrã. A
aplicação ainda não lê o código com a câmara; outro leitor de QR pode lê-lo e
dar-lhe a ligação para colar.

Revogar um dispositivo (`adelino devices --revoke <chave>`) também fecha a
ligação que ele tenha aberta.

```bash
adelino devices
```

```bash
adelino devices --revoke <chave>
```

`adelino remote …` faz o papel de um telemóvel a partir de outro terminal.

A sua instalação tem a sua própria chave de assinatura. Para a substituir:

```bash
adelino key rotate
```

A troca fica registada, assinada pela chave antiga. Os eventos anteriores
continuam verificáveis; a chave antiga deixa de poder escrever ou aprovar.
Reinicie depois o `adelino serve` e a aplicação.

```bash
adelino account status
```

Hoje não existe conta, e nada é enviado para lado nenhum.

## Quando algo corre mal

```bash
adelino doctor
```

Mostra onde o Adelino guarda os ficheiros, a sua chave, o tamanho do
registo, se os comandos podem ser confinados neste computador, e se o mapa
do projeto está instalado.

| O que vê | O que significa |
|---|---|
| `no Anthropic API key` | Corra `adelino init`, ou defina `ANTHROPIC_API_KEY`. |
| `this session has actions waiting` | Responda com `adelino approvals` e depois `adelino resume`. |
| `FAIL at seq N: …` | O registo foi alterado no evento N. Nada do que vem depois é de confiança. |
| `written by another device key` | O ficheiro da chave não corresponde a este registo. Reponha o `device.key` certo. |
| `the signing key was rotated; restart Adelino` | Um `serve` ou a aplicação ainda tem a chave antiga. |
| `none` na linha sandbox do `adelino doctor` | Aqui os comandos não podem ser confinados. O Adelino pergunta antes de cada um. |
| `marked interrupted, not re-run` | O Adelino parou a meio de um comando. Não o volta a correr sozinho. |

## Referência de comandos

Todos os comandos aceitam `--help`.

| Comando | O que faz |
|---|---|
| `adelino init` | Configuração inicial: ligar um modelo |
| `adelino demo` | Visita guiada, sem modelo |
| `adelino chat` | Conversar com o agente |
| `adelino run "<pedido>"` | Um pedido e termina |
| `adelino resume <sessão>` | Continuar uma sessão à espera ou interrompida |
| `adelino sessions` | Listar sessões e o que cada uma consumiu |
| `adelino mode <sessão> <modo>` | Mudar o modo de uma sessão |
| `adelino approvals` | Ações à espera de decisão |
| `adelino approve <pedido>` | Autorizar uma ação à espera |
| `adelino deny <pedido>` | Recusar uma ação à espera |
| `adelino grant …` | O que pode correr sem perguntar (`path`, `command`, `domain`, `tool`, `list`, `revoke`) |
| `adelino verify` | Verificar o registo; `--file` verifica um ficheiro exportado; `--witness` verifica um ponto que outro dispositivo guardou |
| `adelino replay <sessão>` | A sessão tal como aconteceu |
| `adelino log` | Eventos em bruto |
| `adelino undo <chamada>` | Repor um ficheiro que o Adelino escreveu |
| `adelino export <sessão> -o <ficheiro>` | Uma sessão num ficheiro verificável |
| `adelino redact <hash>` | Apagar um conteúdo guardado |
| `adelino memory …` | Factos memorizados (`list`, `add`, `search`) |
| `adelino eval <ficheiro>` | Corre um conjunto de casos de avaliação contra o modelo e escreve um relatório; `-o` para o destino |
| `adelino plan` | Os pedidos de uma sessão, cada um com o seu plano e como terminou; `--session <id>` |
| `adelino vertical …` | Áreas empacotadas como dados (`install`, `list`, `use`); comece uma sessão numa com `--vertical <nome>` |
| `adelino usage` | O que as chamadas ao modelo usaram e custaram, por sessão, modelo e dia; `--session <id>` para uma só |
| `adelino usage limit …` | Limites de gasto (`set`, `list`, `remove`) para uma sessão, um dia ou um pedido agendado |
| `adelino graph …` | O mapa do projeto (`install`, `index`, `query`, `explain`, `path`) |
| `adelino skill …` | Notas tiradas de um livro ou documento seu (`add`, `list`, `show`, `on`, `off`, `remove`, `export`) |
| `adelino mcp …` | Servidores de ferramentas (`add`, `list`, `trust`, `remove`) |
| `adelino pack install <ficheiro>` | Instalar um pack de servidores |
| `adelino schedule …` | Tarefas agendadas (`add`, `list`, `remove`) |
| `adelino serve` | Deixar os telemóveis emparelhados ligarem-se |
| `adelino pair --host <endereço>` | Criar uma ligação de emparelhamento |
| `adelino devices` | Dispositivos emparelhados; `--revoke` remove um |
| `adelino remote …` | Fazer o papel de um dispositivo emparelhado |
| `adelino key rotate` | Substituir a chave de assinatura |
| `adelino account status` | Se há uma conta em uso |
| `adelino doctor` | O que está ativo neste computador |
