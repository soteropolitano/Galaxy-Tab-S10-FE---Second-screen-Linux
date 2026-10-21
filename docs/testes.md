# Testes do Galaxy Tab S10 FE no Linux

Reuni aqui os registros que estavam separados por etapa. O caminho começou pelo Miracast e chegou à rede USB com a depuração desligada, incluindo os testes de pressão, inclinação e dedos pelo navegador.

Os relatos seguem a ordem em que fiz os testes, com os erros, as correções e o que ainda faltava em cada momento. Até agora consegui imagem por Miracast, em espelhamento, e dados de caneta e touch pela rede USB. A tela independente e a entrada nos aplicativos Linux continuam pendentes.

1. [Etapa 01: escolhendo a base](#etapa-01)
2. [Etapa 02: compilando e abrindo](#etapa-02)
3. [Etapa 03: conferindo a GPU](#etapa-03)
4. [Etapa 04: tentando conectar](#etapa-04)
5. [Etapa 05: procurando o erro na rede](#etapa-05)
6. [Etapa 06: apareceu imagem!](#etapa-06)
7. [Etapa 07: S Pen, touch e USB](#etapa-07)
8. [Etapa 08: a rede pelo cabo funcionou](#etapa-08)
9. [Etapa 09: S Pen e dedos pelo cabo](#etapa-09)

---

<a id="etapa-01"></a>

## Etapa 01: escolhendo a base

21/10/2026

Depois da experiência ruim com VNC, comecei a estudar o GNOME Network Displays. Queria ver se conseguiria conectar pelo próprio “Segunda tela” do Samsung, usando Miracast.

Baixei a versão 0.99.0 do código oficial, no commit `c305023f1e6d8ab48d18c66a4efbd18e1df7cdee`, e comecei pelas consultas ao computador. O caminho precisava funcionar no Kubuntu com Wayland, então fui conferir captura de tela, codificação de vídeo e Wi-Fi Direct.

O driver de Wi-Fi anunciava os modos P2P usados pela conexão. Na base, encontrei captura pelo portal/PipeWire, codificação pelo GStreamer e a opção de solicitar uma tela virtual. Isso era suficiente para tentar, mas ainda não dizia se funcionaria com o tablet.

O vídeo deve passar por este caminho:

```text
Tela do KDE -> portal/PipeWire -> H.264 -> Miracast -> tablet
```

A caneta e os dedos precisam fazer o caminho de volta. Essa parte ainda não tinha sido testada.

Nesta etapa só estudei o código e conferi os requisitos. A compilação ficou para a seguinte.

---

<a id="etapa-02"></a>

## Etapa 02: compilando e abrindo

21/10/2026

Preparei as dependências e compilei o GNOME Network Displays com Meson e Ninja. Mantive o aplicativo habilitado e deixei desabilitados o daemon e as integrações de zona do firewalld e de systemd-resolved.

A compilação terminou com seis avisos e nenhum erro. Os dois testes de metadados do projeto também passaram. Eles conferem os metadados, não a transmissão de vídeo.

A janela abriu pelo Wayland, mas o pedido inicial de captura foi cancelado. O aplicativo ficou na pasta de compilação; não foi instalado no sistema.

Ao conferir o vídeo, não encontrei `vah264enc` nem `vaapih264enc` no ambiente consultado. Antes de seguir para o tablet, precisava entender isso.

Na etapa seguinte descobri que aquela consulta estava limitada: o ambiente restrito não mostrava a GPU. Portanto, o resultado não significava que o computador não tinha aceleração. Precisava testar fora dele e tentar codificar algum vídeo de verdade.

---

<a id="etapa-03"></a>

## Etapa 03: conferindo a GPU

21/10/2026

Refiz a consulta fora do ambiente restrito, com um catálogo novo do GStreamer. O driver instalado tinha `vah264lpenc`, com controle CQP, enquanto o aplicativo procurava outros nomes de codificador.

Extraí um pacote do driver Intel completo em uma pasta local, sem substituir o instalado. Selecionei esse driver apenas para os processos de teste. Com ele, `vah264enc` ficou disponível com controle CBR.

Para conferir, gerei 300 quadros de um vídeo sintético em 1920 × 1080 a 60 quadros/s. Depois abri o arquivo para contar os quadros e fiz a decodificação.

O arquivo tinha os 300 quadros, cinco segundos de duração e decodificação sem erros. A execução levou cerca de cinco segundos. Consegui codificar H.264 pela GPU nesse teste.

Ainda não era a tela do computador sendo enviada ao tablet, então isso não mede o atraso da conexão. Serviu para confirmar que o codificador funcionava com aquele driver e aqueles parâmetros.

Encontrar o nome do componente não tinha sido suficiente. Só consegui confirmar quando ele realmente produziu vídeo.

---

<a id="etapa-04"></a>

## Etapa 04: tentando conectar

21/10/2026

Abri “Segunda tela” no Samsung e o computador encontrou o S10 FE por Wi-Fi Direct. Antes, parei o serviço usado na tentativa com VNC para não confundir as telas. Ele não foi reativado.

A sessão de captura foi criada no KDE, a rede direta recebeu endereço e o aplicativo reconheceu o codificador. Mesmo assim, não aparecia imagem.

Nos registros, ele ficava em `WAIT_SOCKET`, aguardando a conexão de controle, e depois dava erro. O aplicativo também fechou em uma tentativa. Não encontrei um registro de queda que explicasse o fechamento.

A descoberta do tablet e a criação da rede estavam passando. O problema aparecia depois, antes do início da transmissão.

Ainda não tinha uma captura dos pacotes para dizer o que estava acontecendo. Encerrei o teste, conferi os monitores físicos e deixei essa observação para a etapa seguinte.

---

<a id="etapa-05"></a>

## Etapa 05: procurando o erro na rede

21/10/2026

Preparei um observador para acompanhar a interface Wi-Fi Direct e registrar os pacotes de controle. Na primeira tentativa ele começou cedo demais: a interface já existia, mas ainda não estava ativa. Ajustei para esperar a ativação e refiz a captura.

O tablet repetia o pedido inicial TCP e não recebia a resposta esperada do computador. Eram retransmissões da mesma tentativa, não vários cliques meus.

Ao conferir os endereços, encontrei a duplicidade. O tablet e outra interface local usavam o mesmo IP. A consulta de rota tratava o destino como parte do próprio computador, em vez de apontar para o aparelho externo.

Uma consulta útil para investigar isso é:

```text
ip -4 route get <IP_DO_TABLET>
```

É preciso substituir `<IP_DO_TABLET>` pelo endereço daquela conexão. Se aparecer `local` e a interface `lo`, vale conferir se o mesmo IP está configurado no computador. Os endereços reais deste teste ficaram no registro local.

Encontrei o conflito, mas ainda precisava saber se era ele que impedia a conexão. Decidi retirar o endereço duplicado temporariamente, com a restauração preparada antes de começar. Fiz esse teste na etapa seguinte.

---

<a id="etapa-06"></a>

## Etapa 06: apareceu imagem!

21/10/2026

Conferi que a interface com o endereço duplicado não estava atendendo a uma carga ativa, guardei a configuração e retirei somente aquele endereço. A rede continuou existindo.

Também deixei um processo separado encarregado da restauração, com limite de tempo. Queria conseguir comparar o resultado e depois voltar à configuração anterior.

Dessa vez conectou. A captura mostrou a resposta do computador ao pedido TCP e a negociação RTSP, com `SETUP`, `PLAY` e respostas de sucesso. O aplicativo chegou a `STREAMING` e a imagem apareceu no S10 FE.

Por enquanto foi espelhamento. Ainda não era uma tela independente.

O aplicativo escolheu 1920 × 1080 a 30 quadros/s, com `vah264enc` e perfil H.264 Constrained Baseline pela GPU. Essa é a configuração registrada; não medi quantos quadros o tablet recebeu de fato.

Deixei transmitir por cerca de 43 segundos, contados entre o início do vídeo e a parada manual. Não testei áudio, touch ou S Pen nesse intervalo.

Ao terminar, parei o aplicativo e restaurei o endereço. Conferi a configuração da rede e a disposição dos monitores físicos.

O observador teve um problema ao tentar sinalizar o tcpdump: recebeu erro de permissão. Quando o aplicativo fechou, a interface desapareceu e a captura terminou. O arquivo permaneceu legível, com 56 pacotes e zero descartes pelo kernel informados pelo tcpdump. A restauração, que tinha outro processo, foi concluída.

Retirar o endereço duplicado fez a conexão passar de onde vinha falhando. Isso deu uma evidência forte de que a colisão estava bloqueando o controle. Como restaurei a configuração original, ainda preciso resolver o conflito de forma permanente.

---

<a id="etapa-07"></a>

## Etapa 07: S Pen, touch e USB

21/10/2026

Depois de conseguir imagem, fui olhar como outros projetos tratam a caneta, o touch e a conexão por cabo. Quero essas partes funcionando também, então reuni os links em [referências](referencias.md) e comecei a estudar documentação, código e relatos de uso.

O UScreen foi o que mais se aproximou do que procuro para KDE Wayland. Ele cria uma tela com EVDI, transmite o vídeo para um aplicativo Android e devolve os dados de entrada ao Linux:

```text
KDE -> EVDI -> captura/H.264 -> ADB/USB -> aplicativo Android

Caneta e dedos -> Android -> canal de entrada -> uinput -> KDE
```

Encontrei no cliente Android a leitura de pressão, inclinação, orientação, aproximação e botão. No Linux, há dispositivos separados para caneta e touch e um mapeamento pelo KWin para mandar a entrada à tela correta.

Há também um relato positivo de S10 FE+ no KDE, com a confirmação do botão depois de uma correção na versão 1.2.8. Ainda falta testar no meu S10 FE.

Os outros projetos ajudaram a entender as diferenças. O TethrLink usa rede por USB, mas a entrada depende do GNOME e emula ponteiro. O arch-galaxytab-submonitor usa Sunshine/Moonlight e registra caneta e touch como não verificados. Já o Second-Screen-Samsung-Linux transmite MJPEG para uma página que, no código lido, não envia entrada de volta.

Os projetos com XRandR e VNC ficam como referência para organizar telas. Para o Wayland e os recursos de caneta que quero testar, não são a primeira opção.

Nesta etapa não instalei nenhum deles nem iniciei outra conexão com o tablet. Fiz o estudo, algumas consultas de preparação e organizei a documentação no GitHub. O inventário do computador ficou no relatório local.

Depois desse estudo, corrigi a preferência de conexão: quero usar o cabo com a depuração USB desligada. O fluxo acima descreve como o UScreen funciona hoje; não é o transporte escolhido para o próximo teste.

Vou começar conferindo o compartilhamento de rede por USB, sem ADB. O UScreen fica como referência para tela e entrada no KDE, mas ainda preciso avaliar as adaptações do programa e do aplicativo Android para esse transporte. Depois quero testar a tela independente, o touch e os dados da S Pen, cada um com sua verificação. O [plano](plano.md) foi atualizado com essa sequência.

O Miracast continua no projeto. Conseguir vídeo pela função nativa da Samsung ainda não resolveu o retorno da caneta e dos dedos.

---

<a id="etapa-08"></a>

## Etapa 08: a rede pelo cabo funcionou

Consegui conectar o S10 FE ao Kubuntu por ancoragem USB, com a depuração desligada. Abri no navegador do tablet uma página que estava sendo servida pelo computador. O servidor registrou o pedido vindo do tablet, e confirmei que a página apareceu.

No começo eu tinha selecionado apenas transferência de arquivos. O Linux reconhecia essa função, mas a rede não aparecia. Depois ativei **Ancoragem USB** nas configurações de conexões do tablet. A partir daí, o Kubuntu reconheceu a interface e recebeu um endereço automaticamente.

### Como conferi

Primeiro consultei a rota até o tablet e enviei cinco pacotes de ping pela interface USB. Os cinco responderam, sem perdas. Depois abri a página no S10 FE, fazendo a comunicação no sentido contrário.

O servidor escutava somente no endereço da rede USB e aceitava a página apenas do tablet. A primeira janela de teste expirou antes de receber um pedido; reabri o endereço e a página abriu. Depois encerrei o servidor.

Não instalei driver nem aplicativo, não alterei o firewall e não usei ADB. O driver de rede USB do Linux já deu conta dessa conexão. A sub-rede recebida também não se sobrepôs às redes existentes no início do teste.

Mantive a depuração desligada durante o teste. Do lado do computador, os descritores USB ativos não mostraram uma interface ADB. Essa consulta apoia a confirmação feita na conversa, mas não lê diretamente o ajuste interno do Android.

### Até onde fui

A rede pelo cabo está funcionando. Ainda falta levar vídeo, touch e S Pen por ela. O ping respondeu rápido, mas esse tempo não mede atraso de vídeo nem velocidade útil da conexão.

A imagem que já consegui transmitir continua sendo a da [etapa 06](#etapa-06), por Miracast e em espelhamento. Não fiz uma nova transmissão nesta etapa.

O NetworkManager criou a conexão automaticamente. A conexão Ethernet conservou a prioridade e não precisei mudar as outras redes. A ancoragem ficou ativa porque pedi para continuar o trabalho. Para retirar essa rede, basta desativar a ancoragem no tablet ou tirar o cabo.

Agora preciso preparar o programa Linux e o aplicativo Android para conectar sem ADB: endereço do computador, abertura manual e autorização da sessão. Também vou observar quais dados da caneta e do touch chegam pelo cabo antes de ligar a entrada ao Linux.

A [orientação da Samsung sobre conexões e compartilhamento](https://www.samsung.com/us/support/answer/ANS10002546/) ajudou a localizar a configuração. O funcionamento acima foi conferido no meu tablet. O relatório completo desta etapa fica salvo no computador.

---

<a id="etapa-09"></a>

## Etapa 09: S Pen e dedos pelo cabo

Depois de confirmar a rede USB, fiz um teste separado de entrada. Abri uma página no navegador do S10 FE, desenhei na área de teste e mandei os eventos para o computador pelo cabo, mantendo a depuração desligada.

A primeira rodada registrou 2.975 eventos de caneta. A pressão variou, e a inclinação apareceu nos dois eixos. Na segunda rodada, com a S Pen afastada, chegaram os eventos dos dedos: foram registrados até cinco contatos simultâneos.

### O que chegou

| Parte | Resultado do teste |
| --- | --- |
| Pressão da S Pen | 1.498 valores distintos durante contato, entre aproximadamente 0,000244 e 0,589988 na escala do navegador |
| Inclinação X | De -1 a 41 graus |
| Inclinação Y | De -20 a 40 graus |
| Touch | 3.364 eventos PointerEvent e até cinco dedos ao mesmo tempo |
| Finalização | As duas rodadas registraram conclusão; os contatos de touch terminaram no registro |
| Aproximação e botão lateral | Não apareceram nos eventos recebidos; continuam pendentes |

Os valores de pressão mostram variação nesta amostra. Não representam uma calibração de força nem a quantidade de níveis de pressão do hardware.

Na primeira rodada não chegou nenhum evento de dedo. Repeti o teste só com os dedos, afastando a caneta, e passei a registrar também TouchEvent, separado de PointerEvent. As duas interfaces mostraram até cinco contatos simultâneos.

Não somei os dois contadores como gestos independentes, porque podem descrever os mesmos contatos. Também não concluí que a diferença entre as rodadas comprova rejeição de palma: esse comportamento ainda precisa de um teste próprio.

### Ainda falta ligar isso ao Linux

A página serviu para conferir quais dados atravessavam o cabo. Ela não controlava o cursor, não criava dispositivos de entrada e não transmitia a área de trabalho. Pressão no Krita, multitouch nos aplicativos e alinhamento com a tela do tablet continuam pendentes.

O receptor passou em cinco testes locais, incluindo validação dos dados e reenvio sem duplicação. Depois da conclusão no tablet, encerrei o servidor. A ancoragem ficou ativa para a continuação. Não instalei APK nem alterei permissões de entrada nesta etapa.

### O que encontrei no UScreen

Li os arquivos de conexão no commit `66f6bcdab918b8dcc44227dde00197f30a778c44`. O modo Wi-Fi também usa ADB. O programa usa ADB para detectar o tablet, abrir o aplicativo e entregar o token da sessão; os canais de vídeo e entrada escutam em loopback, e o aplicativo Android se conecta a endereços de loopback fixos.

Para usar a rede USB, preciso adaptar os dois lados. O cliente deve receber o endereço do computador e abrir manualmente. O programa deve acompanhar uma sessão autenticada e liberar a tela e os contatos quando ela terminar. Trocar só o endereço não resolve tudo isso.

O código nativo de caneta, o decodificador e a entrada do UScreen continuam como referência. Não alterei nem compilei esses componentes nesta etapa. O próximo trabalho é preparar a compilação e uma primeira adaptação de conexão sem ADB, preservando a autorização de vídeo e entrada.

Usei a documentação de [PointerEvent](https://developer.mozilla.org/en-US/docs/Web/API/PointerEvent) e [touch-action](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/touch-action) para preparar o diagnóstico. A arquitetura do aplicativo de referência está no [UScreen](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/docs/architecture.md). O relatório completo e os registros brutos ficam no computador.
