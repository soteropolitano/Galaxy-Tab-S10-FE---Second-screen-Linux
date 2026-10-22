# Testes do Galaxy Tab S10 FE no Linux

Reuni aqui os registros que estavam separados por etapa. O caminho começou pelo Miracast e chegou à tela independente pelo cabo e pelo Wi-Fi, com S Pen, touch e som no Linux. A depuração USB continua desligada.

Os relatos seguem a ordem em que fiz os testes, com os erros, as correções e o que ainda faltava em cada momento. As pendências dos primeiros relatos devem ser lidas como o estado daquela tentativa. As continuações mostram o que consegui resolver depois.

Até 22/10/2026, consegui usar o Krita pelo cabo e pelo Wi-Fi, com pressão, inclinação, botão lateral e gestos de dois dedos. O som e a troca de saída também funcionaram pelos dois caminhos. Depois dos testes iniciais em 1280 × 800, a imagem chegou a 2304 × 1440 a 90 Hz, com resolução e atualização selecionáveis no PC e no aplicativo.

1. [Etapa 01: escolhendo a base](#etapa-01)
2. [Etapa 02: compilando e abrindo](#etapa-02)
3. [Etapa 03: conferindo a GPU](#etapa-03)
4. [Etapa 04: tentando conectar](#etapa-04)
5. [Etapa 05: procurando o erro na rede](#etapa-05)
6. [Etapa 06: apareceu imagem!](#etapa-06)
7. [Etapa 07: S Pen, touch e USB](#etapa-07)
8. [Etapa 08: a rede pelo cabo funcionou](#etapa-08)
9. [Etapa 09: S Pen e dedos pelo cabo](#etapa-09)
10. [Etapa 10: o aplicativo conectou sem ADB](#etapa-10)
11. [Etapa 11: retirar o cabo e conectar de novo](#etapa-11)
12. [Etapa 12: a mudança para 90 Hz](#etapa-12)
13. [Etapa 13: Wi-Fi sem cabo](#etapa-13)
14. [Etapa 14: som e troca de saída](#etapa-14)
15. [Etapa 15: Krita pelo cabo](#etapa-15)
16. [Etapa 16: Krita pelo Wi-Fi](#etapa-16)
17. [Etapa 17: resolução, Hz e sincronização](#etapa-17)

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


---

<a id="etapa-10"></a>

## Etapa 10: o aplicativo conectou sem ADB

21 e 22/10/2026

Os primeiros testes abaixo foram feitos em 21/10. A continuação com imagem e entrada no tablet está no fim desta etapa.

Comecei a adaptar o UScreen para usar a ancoragem USB. Já consegui instalar o aplicativo no S10 FE e conectar ao computador com a depuração desligada. Apareceu “Conexão USB confirmada”, o contador aumentou durante o teste e consegui encerrá-lo pelo tablet.

Depois fiz testes de tela virtual e vídeo no próprio computador. Essa parte também avançou, mas ainda não apareceu imagem no tablet pelo cabo. A imagem confirmada no tablet continua sendo a do teste anterior com Miracast.

### O que foi adaptado

Usei o código do [UScreen neste commit](https://github.com/majmichu1/UScreen/tree/66f6bcdab918b8dcc44227dde00197f30a778c44). Preparei as ferramentas de Rust, Java e Android numa pasta de trabalho, sem instalar ADB nem substituir essas ferramentas no sistema. Compilei o programa Linux e o APK de teste, mantendo os créditos e a licença MIT da base.

No caminho USB adaptado, o programa usa o endereço da rede da ancoragem e aceita a conexão do tablet escolhido. O aplicativo recebe um link com os dados da sessão, pede confirmação e conecta diretamente. A instalação e a abertura são manuais. A presença do tablet passa a ser acompanhada pela sessão autenticada, com verificação de conexão e limpeza ao desconectar, em vez de depender do monitor ADB.

Mantive a autorização por token nos canais de vídeo e entrada. Os endereços e os dados de autorização ficam só nos arquivos locais de sessão. Isso é uma primeira adaptação em teste, sem uma revisão completa de segurança.

Criei primeiro um modo de diagnóstico que recebe os eventos, mas não captura a tela nem injeta entrada no Linux. Assim consegui conferir a conexão sem movimentar o cursor ou criar uma tela a cada tentativa.

### Os erros até conectar

Na primeira versão o aplicativo abria e ficava aguardando, sem explicar o que tinha acontecido. Durante as tentativas, a rede USB foi recriada e o endereço do computador mudou. O link anterior continuava apontando para o endereço antigo.

Acrescentei mensagens de estado e erro, uma opção para colar o link da sessão e a identificação da interface USB atual ao iniciar o teste. Instalei a segunda versão e usei o endereço atualizado. Foi nessa tentativa que apareceu a confirmação da conexão.

Ainda preciso tratar a troca de endereço durante uma sessão e a reconexão automática. Identificar a rede ao iniciar não resolve todo o ciclo de tirar e recolocar o cabo.

### O que o aplicativo enviou

O computador registrou 1.895 mensagens: uma de resolução e 1.894 de touch. Foram até cinco dedos simultâneos, sem contatos ativos no fim do registro.

Não chegou nenhum evento identificado como caneta nessa rodada. Portanto, pressão e inclinação da S Pen estão confirmadas apenas no teste pelo navegador da etapa anterior. Ainda não sei se a diferença veio do encaminhamento dos eventos no aplicativo ou de outro ponto do teste.

Preparei uma terceira versão com mais informações sobre os eventos que o Android entrega, incluindo o tipo de ferramenta, a origem e os botões. Ela foi compilada e teve a assinatura verificada, mas ainda não foi instalada no tablet. O próximo teste precisa mostrar onde a caneta está sendo reconhecida antes de tentar corrigir essa parte.

O modo de diagnóstico não desenha na tela nem controla os programas do computador. O contador confirma a chegada de mensagens, não o funcionamento de um pincel no Krita. Aproximação, botão lateral, rejeição de palma e alinhamento da entrada continuam pendentes.

### Tela virtual e vídeo no computador

Com a autorização no computador, carreguei o módulo EVDI que já estava instalado e concedi acesso temporário aos dispositivos necessários para o teste. Não configurei um serviço nem regras permanentes de permissão. O acesso temporário precisa ser revisto ao encerrar os testes; o módulo não foi removido à força.

Encontrei um erro na procura do dispositivo de vídeo: o código descartava o índice zero. Corrigi a leitura do nome e acrescentei um teste para aceitar esse índice e rejeitar nomes inválidos. Depois disso consegui criar a tela virtual no KDE e passar pela captura, codificação H.264 por GPU e envio no formato usado pelo aplicativo.

Usei um cliente simulado no próprio computador para receber o vídeo. Uma amostra teve 96 quadros em 1280 × 800; outra teve 95. As duas foram decodificadas sem erros. Isso verifica o caminho local até o recebimento e a leitura do H.264, mas ainda não testa a rede USB com vídeo nem o decodificador do tablet.

Tentei também colocar um padrão animado na tela virtual. A sessão estava bloqueada durante a pausa, e a captura mostrou a tela de bloqueio. Por isso não considerei o teste de movimento concluído. A configuração tinha como alvo 30 quadros por segundo, mas não medi essa taxa com conteúdo em movimento. As confirmações do cliente simulado também não medem atraso até a imagem aparecer no tablet.

### Conferências e próximos testes

Na compilação de verificação da adaptação passaram 39 testes Rust e três testes Android. O teste em C para os nomes dos dispositivos também passou. Eles ajudam a conferir conexão, autorização e validação dos dados; não substituem o uso no tablet.

Agora falta instalar a versão de diagnóstico da caneta, conferir os eventos nativos da S Pen e fazer o primeiro teste de vídeo USB no S10 FE. Depois preciso usar caneta e dedos nos aplicativos Linux, conferir a posição dos toques e testar a desconexão. Desempenho comparável ao aplicativo da Samsung no Windows ainda não foi demonstrado.

O código adaptado, os APKs e os registros brutos continuam no computador. Esta atualização publica o andamento dos testes e os limites encontrados, sem os endereços da rede, os dados de sessão ou o inventário da máquina.


### Continuação em 22/10: apareceu imagem pelo cabo

Instalei a terceira versão de diagnóstico e fiz uma rodada só com a S Pen. Chegaram 3.057 eventos nativos de caneta, com pressão, inclinação e aproximação. A ausência de caneta na rodada anterior não se repetiu; aquele resultado não era suficiente para concluir que o aplicativo não reconhecia a S Pen.

Na primeira tentativa de vídeo, o aplicativo ficou aguardando ou com a tela preta. Acrescentei informações de erro e adiei a preparação do decodificador até receber os parâmetros da sessão. A mensagem mostrada na versão de teste 4 revelou outro problema: a conexão de vídeo estava tentando usar a porta zero.

Esse erro estava na adaptação. Dentro de `Socket().apply`, o nome `port` apontava para a propriedade do próprio socket, ainda sem destino, em vez da porta recebida na sessão. Separei o destino da criação da conexão e acrescentei um teste com uma conexão TCP local real, além da rejeição de porta zero.

Instalei o teste 5 e apareceu imagem no S10 FE. A tela virtual de 1280 × 800 passou pelo EVDI, pela codificação H.264 na GPU e pela rede USB até o aplicativo Android. A depuração continuou desligada.

### Caneta e dedos desenhando no Linux

Abri uma área de desenho GTK na tela virtual. Dessa vez o teste usou os dispositivos virtuais de entrada do Linux: os eventos passaram pelo aplicativo Android, pelo programa no computador e pelo KDE até a área de desenho.

| Rodada | Resultado |
| --- | --- |
| S Pen sem apoiar a mão | O traço acompanhou a ponta e mudou de espessura conforme a pressão |
| Inclinação e aproximação | Chegaram à área de teste no Linux |
| Dedos com a caneta afastada | Até cinco contatos simultâneos; as marcas ficaram alinhadas aos toques |
| Mão apoiada com a caneta próxima | Consegui desenhar sem interferência; não chegaram eventos de dedos nessa rodada |
| Botão lateral | A área Linux recebeu 16 pares de pressionar e soltar; não abriu menu da Samsung |
| Fim da rodada de touch | Nenhum contato ficou ativo no registro |

O teste com a mão apoiada foi feito aproximando primeiro a S Pen e mantendo a ponta perto da tela entre os traços. Ainda preciso variar a sequência e a posição da mão. Receber inclinação também não substitui conferir o efeito num pincel preparado para responder a ela.

A tela do tablet era uma área adicional do KDE. As configurações das telas físicas foram preservadas. Depois, organizei a tela virtual abaixo da principal.

### Encerramento e verificações

No encerramento por prazo do programa, a tela virtual foi retirada e os dispositivos de entrada foram destruídos. As telas físicas conservaram a configuração. A retirada física do cabo ficou para a etapa seguinte.

Depois da correção da conexão de vídeo, passaram os cinco testes Android, e a assinatura do APK foi conferida. O teste em C para os nomes dos dispositivos também continuou passando. Os resultados com o aparelho confirmam imagem e entrada nessa configuração, mas ainda não demonstram estabilidade prolongada ou desempenho igual ao aplicativo da Samsung no Windows.

O módulo EVDI permaneceu carregado, sem remoção forçada, e os acessos preparados para os dispositivos continuaram temporários. Não configurei serviço nem regras permanentes de permissão.

---

<a id="etapa-11"></a>

## Etapa 11: retirar o cabo e conectar de novo

22/10/2026

Com a imagem funcionando, fiz um teste de retirada do cabo enquanto mantinha um dedo encostado na área de desenho. Queria conferir se o Linux soltava o contato e retirava a tela virtual, além de ver o que o aplicativo mostrava.

### A primeira retirada

O tablet ficou com a última imagem. No primeiro relato pareceu que alguma tela tinha travado, mas conferi depois: foi só a imagem no tablet; as telas do computador continuaram normais.

O computador levou cerca de dez segundos para liberar o contato e retirar a tela virtual. O encerramento ainda dependia do prazo de verificação da conexão. Precisava detectar a retirada antes e explicar o estado no aplicativo.

### O que corrigi e como repeti

O programa Linux passou a verificar a presença da rede USB a cada 200 ms. Ao perder essa rede, encerra o canal de controle e libera os contatos da sessão. Acrescentei uma verificação local para esse caminho de encerramento.

No Android, acrescentei a observação do estado USB e o aviso de cabo desconectado. Corrigi também a interface ao parar o vídeo, para a última imagem não continuar aparecendo como se a conexão estivesse ativa.

Passaram 40 testes Rust e cinco testes Android. Compilei o teste 6, conferi a assinatura e instalei a atualização manualmente no S10 FE, ainda sem depuração USB.

Repeti a retirada com um dedo encostado. Desta vez apareceu o aviso de cabo USB desconectado. O observador dos dispositivos virtuais confirmou a liberação dos botões e dos contatos. Na primeira amostra em que a rede apareceu ausente, a tela virtual já tinha sido retirada.

A observação da rede era feita aproximadamente a cada segundo. Por isso, o teste confirma o encerramento, mas não mede com precisão o tempo físico de resposta. Os 200 ms são o intervalo configurado no programa, não uma latência medida no aparelho.

### Mantendo o mesmo endereço

Os endereços automáticos mudavam quando eu recriava a ancoragem. Preferi manter o endereço do aplicativo e só trocar se realmente fosse necessário.

Criei um perfil no NetworkManager que mantém a configuração automática da ancoragem e acrescenta um endereço fixo para o aplicativo. A ativação verifica endereço duplicado. O perfil cabeado que já existia no computador foi preservado.

A identificação usa a porta USB e o driver da conexão. Ela não depende do nome da interface, que mudou depois de reconectar. O iniciador também passou a conferir o endereço reservado, evitando trocar o link apenas porque recebeu outro endereço automático.

Retirei o cabo, esperei, reconectei na mesma porta e reativei a Ancoragem USB. Mantive o Galaxy USB aberto, sem voltar à página da sessão. A imagem voltou sozinha, e o endereço do aplicativo foi mantido.

Esse resultado foi conferido na mesma porta do computador, com o aplicativo aberto e a ancoragem reativada manualmente. Outra porta precisa ter a identificação revista. Se o tablet mudar a sub-rede ou houver conflito, será necessário revisar o endereço.

Consultei a documentação do NetworkManager sobre [identificação da conexão](https://www.networkmanager.dev/docs/api/latest/settings-match.html) e [endereços IPv4](https://www.networkmanager.dev/docs/api/latest/settings-ipv4.html), além do [código do Android para o estado USB](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/hardware/usb/UsbManager.java). Os endereços e a identificação da porta ficaram no registro local.

---

<a id="etapa-12"></a>

## Etapa 12: a mudança para 90 Hz

22/10/2026

Percebi que a tela virtual estava em 30 Hz nas configurações do KDE. Esse era o alvo inicial usado para começar os testes de conexão e imagem.

Mudei o aplicativo para 60 fps. O vídeo reiniciou e a imagem voltou, mas continuei achando lenta. Depois aumentei para 90 fps e 60 Mbps, mantendo a resolução de 1280 × 800. A fluidez melhorou muito. Com esse resultado, decidi seguir usando essa configuração e encerrar a investigação de lentidão por enquanto.

### O que foi aplicado e o que foi medido

Conferi a configuração do programa e o modo informado pelo KDE. Abri também um padrão animado na tela virtual para observar a captura com movimento, em vez de usar somente uma imagem parada.

| Consulta | Resultado até este momento |
| --- | --- |
| Configuração atual | 1280 × 800, alvo de 90 fps, bitrate de 60 Mbps |
| Modo da tela virtual no KDE | Aproximadamente 90 Hz, registrado como 89,996 Hz |
| Captura com o padrão animado | Aproximadamente 56 a 70 quadros/s nas janelas completas de cinco segundos da rodada observada |
| Codificação na mesma rodada | Aproximadamente 58 a 70 quadros/s nas janelas completas |
| Avaliação após mudar para 60 fps | A imagem voltou, mas ainda pareceu lenta |
| Avaliação de uso com 90 fps e 60 Mbps | Melhorou muito; decidi manter essa configuração |

A rodada do padrão durou cerca de 45 segundos. Para as faixas acima, usei as janelas completas de cinco segundos durante o movimento; a primeira janela misturava o início do teste com o estado anterior. Esses números são da captura e do codificador, não uma medição óptica dos quadros exibidos pelo painel do tablet.

Os 90 Hz foram aplicados à tela virtual, mas ainda não obtive 90 quadros por segundo constantes no caminho observado. Também não tenho uma medida do atraso completo, desde tocar com a caneta até ver o traço no tablet.

Neste caminho, o codificador está em qualidade constante e recebe o bitrate como parâmetro de limite. Configurar 60 Mbps não quer dizer que todo conteúdo vá consumir essa taxa. O padrão simples transmitiu bem menos; aumentar esse valor não basta para concluir que a fluidez melhorou.

A melhora no uso foi suficiente para encerrar esta investigação por enquanto. Vou manter 90 fps, 60 Mbps e o mesmo endereço de conexão. A amostra acima fica como registro do teste; não vou continuar medindo ou tentando otimizar essa parte agora. Ainda não fiz uma comparação medida com o aplicativo da Samsung no Windows em condições equivalentes.

O código adaptado, os APKs e os registros brutos continuam no computador. Os testes seguem reunidos neste documento.

---

<a id="etapa-13"></a>

## Etapa 13: a tela continuou pelo Wi-Fi, sem cabo

22/10/2026

Conectei computador e tablet à mesma rede Wi-Fi e comecei abrindo no tablet uma página servida pelo computador. Depois instalei o aplicativo de teste 7 e confirmei a sessão sem fio. Apareceu imagem.

Retirei o cabo com o aplicativo aberto. A imagem continuou, e consegui desenhar com pressão da S Pen. Numa rodada separada, com a caneta afastada, os dedos deixaram marcas alinhadas. No computador, conferi que vídeo e controle estavam usando a rede Wi-Fi e que não havia rede USB ativa.

Na área de teste chegaram 1.102 eventos de caneta, com 27 contatos completos da ponta, aproximação e pressão variável. O registro dos dedos teve 2.120 eventos, até cinco contatos simultâneos e nenhum contato restante ao terminar.

### O que mudou

A sessão passou a distinguir USB e Wi-Fi. No modo sem fio, retirar o cabo não encerra a conexão. O programa Linux ganhou a conexão direta pela rede local, sem o caminho Wi-Fi por ADB da base consultada. Mantive os links USB anteriores, a confirmação no tablet, a autorização da sessão e a restrição ao tablet selecionado.

A conexão desta versão usa TCP/WebSocket, sem uma camada de criptografia adicionada. Fiz a rodada na rede Wi-Fi compartilhada entre os aparelhos, mantendo a depuração USB desligada.

O teste manteve 1280 × 800, alvo de 90 fps e 60 Mbps. Passaram 41 testes Rust e sete testes Android, e a assinatura do APK foi conferida. O endereço reservado para o USB foi preservado; o Wi-Fi usa o endereço próprio dessa rede. Não testei troca de roteador ou suspensão prolongada.

---

<a id="etapa-14"></a>

## Etapa 14: som no tablet e o problema ao trocar de saída

22/10/2026

Queria que o computador reconhecesse o tablet como uma saída de áudio. Acrescentei um canal separado para o som e a recepção no aplicativo Android de teste 8; essa transmissão não existia na versão do UScreen consultada.

Ao conectar o tablet ao canal autenticado, o programa cria a saída temporária **Galaxy Tab S10 FE (teste)** no PipeWire. Escolho essa saída nas configurações de som do PC. O transmissor capta o monitor dessa saída virtual e envia PCM estéreo, 48 kHz e 16 bits, ao aplicativo, que reproduz pelo AudioTrack. O aplicativo não pede acesso ao microfone.

### Primeiro teste e o erro que apareceu no uso

Antes de usar o tablet, conferi o áudio com um cliente simulado e tons diferentes nos dois canais. A autorização inválida não criou saída, uma segunda sessão foi recusada e o encerramento retirou a saída virtual. A saída padrão do computador foi preservada. Passaram dez testes Android, incluindo o cabeçalho de áudio recebido em partes e a compatibilidade com os links anteriores.

Instalei o teste 8. A imagem voltou, a saída apareceu no PC e ouvi no tablet o som curto enviado somente para ela, pelo Wi-Fi.

Depois usei o Spotify. Ao mudar do tablet para a caixa Bluetooth, o som continuou nos dois. A conferência mostrou que o Spotify já estava na saída escolhida. O capturador é que tinha sido redirecionado para o monitor da caixa e estava reenviando esse áudio ao tablet.

### A correção e a repetição

O capturador ganhou uma identificação própria e passou a solicitar que sua origem não seja movida, religada a outro dispositivo ou substituída pela origem padrão. Também passei a conferir a origem real antes de enviar áudio e durante a sessão. Se ela deixar de ser o monitor da saída virtual, o envio é encerrado. Não alterei a política global de áudio do computador.

Repeti o caso com duas saídas virtuais e um tom sintético. Ao mover o aplicativo de teste para a outra saída, o canal do tablet passou a receber silêncio. A captura permaneceu no monitor correto, sem mudar a saída padrão nem as rotas dos outros aplicativos.

No uso com Spotify, a troca pelo Wi-Fi passou a deixar o som somente na caixa escolhida. Depois reconectei o cabo, reativei a Ancoragem USB com a depuração desligada e confirmei imagem e saída de som. Os canais de vídeo, entrada e áudio estavam pela rede USB. O Spotify tocou no tablet quando escolhi essa saída e somente na caixa ao mudar novamente.

A correção foi no computador; não precisei reinstalar o aplicativo para repeti-la. Assim, áudio e troca de saída ficaram confirmados pelo Wi-Fi e pelo cabo. Ainda faltam reprodução prolongada e uma medida de sincronização entre áudio e vídeo.

---

<a id="etapa-15"></a>

## Etapa 15: desenhando no Krita pelo cabo

22/10/2026

Abri o Krita portátil 5.3.4.1 na tela do tablet, com um perfil separado para os testes. Ele usou XWayland dentro da sessão Wayland do KDE. Mantive 1280 × 800, 90 fps e 60 Mbps, com USB sem depuração.

Com o pincel **Basic-5 Size**, o desenho acompanhou a ponta da S Pen e a espessura variou entre traços leves e fortes.

### A inclinação precisava aparecer no desenho

Na primeira tentativa, com **Pencil-5 Tilted**, não percebi diferença ao inclinar. Os dados de inclinação chegavam ao Krita, mas isso ainda não confirmava um efeito visível no traço.

Preparei uma folha separada e um pincel redondo chamado **Teste de inclinacao Galaxy**. Nele, o tamanho depende só da inclinação; a pressão não controla tamanho, opacidade nem fluxo. A diferença foi ampliada para facilitar a comparação.

O traço ficou mais grosso ao inclinar a caneta. Assim, consegui confirmar o uso da inclinação no desenho. A configuração do pincel faz diferença: receber o dado não significa que todo pincel vá usá-lo da mesma forma.

### Mão apoiada, botão e dedos

Aproximei a S Pen, apoiei a mão e desenhei. Não percebi marcas da mão, deslocamento da folha ou interrupção nessa rodada. Isso ainda não cobre todas as posições de apoio ou sessões longas.

O botão lateral arrastou a folha. No Krita, ele chegou como botão do meio, que nessa configuração aciona esse movimento. Depois afastei a caneta e usei dois dedos para mover a folha e ampliar e reduzir o desenho. Funcionou.

Os desenhos foram salvos e a integridade dos arquivos foi conferida. Os registros de eventos Qt podem ter repetições ao passar por diferentes objetos da interface; suas contagens não foram tratadas como número de traços ou cliques físicos.

---

<a id="etapa-16"></a>

## Etapa 16: repetindo o Krita pelo Wi-Fi

22/10/2026

Retirei o cabo e conectei o aplicativo pelo mesmo endereço Wi-Fi usado antes, sem reinstalar. A folha do Krita apareceu. Conferi no computador que vídeo e entrada usavam o Wi-Fi, sem interface de rede USB ativa.

Repeti o desenho com pressão e com a mão apoiada. O traço ficou alinhado, variou de espessura e a mão não interferiu nessa rodada. Com o mesmo pincel de diagnóstico da etapa anterior, o traço ficou mais grosso ao inclinar a S Pen.

O botão lateral continuou movendo a folha. Com a caneta afastada, os gestos de dois dedos também moveram e ampliaram/reduziram o desenho.

Essas funções do Krita ficaram confirmadas pelo cabo e pelo Wi-Fi, nas rodadas em 1280 × 800. O desenho foi salvo e os monitores físicos foram preservados. Ainda não foi uma avaliação de uso prolongado nem uma nova investigação de desempenho.

---

<a id="etapa-17"></a>

## Etapa 17: escolher resolução e Hz no PC e no aplicativo

22/10/2026

Percebi que a tela continuava limitada à configuração inicial de 1280 × 800. O S10 FE vai além disso: a resolução nativa é **2304 × 1440**, com tela de 90 Hz. Pedi também a escolha de resolução e atualização nas configurações do PC e do aplicativo.

### Por que o KDE só mostrava um modo

O programa entregava ao Linux uma identificação de monitor com um único modo. Essa identificação é o EDID, que informa ao sistema quais resoluções e taxas a tela oferece.

O catálogo passou a anunciar as combinações abaixo, sem perder a opção nativa quando uma resolução menor é escolhida:

| Resolução | Atualização |
| --- | --- |
| 2304 × 1440, nativa | 30, 60 ou 90 Hz |
| 1920 × 1200 | 30, 60 ou 90 Hz |
| 1600 × 1000 | 30, 60 ou 90 Hz |
| 1280 × 800 | 30, 60 ou 90 Hz |

O KDE também apresenta modos básicos de 640 × 480 para compatibilidade do formato. As opções principais para este teste são as da tabela. Corrigi também a informação do tamanho físico no EDID, usada pelo sistema no cálculo da densidade da tela.

A captura e o codificador passaram a acompanhar o modo escolhido no KDE, incluindo uma troca só de Hz. O aplicativo ganhou a opção **Nativa do tablet**, as resoluções menores e a escolha de 30, 60 ou 90 Hz.

### A imagem mudava, mas o aplicativo mostrava a escolha antiga

Instalei o teste 9 e a imagem voltou em 2304 × 1440 a 90 Hz. O KDE reconheceu as doze combinações principais. Mudei pelo computador para 1920 × 1200 e 60 Hz: a imagem continuou, e o codificador acompanhou esses valores. Depois voltei à nativa com 90 Hz pelo aplicativo.

Encontrei então outra falha. Ao mudar pelo PC, a imagem mudava, mas o aplicativo continuava mostrando “nativa” e 90 Hz. Ele exibia a preferência salva, sem receber o modo realmente ativo.

No **teste 10**, o computador passou a enviar o modo ativo pelo canal autenticado. A linha **Ativa no PC**, as escolhas de resolução e Hz e as preferências de reconexão passaram a acompanhar esse estado. Receber o aviso não faz o aplicativo enviar automaticamente o mesmo comando de volta.

Instalei a atualização e repeti a mudança para 1920 × 1200 e 60 Hz com as configurações do tablet abertas. Os valores mudaram sozinhos, sem fechar e abrir a janela.

### Como terminou a rodada

Voltei a 2304 × 1440 a 90 Hz, mantendo 60 Mbps. O modo do KDE e o codificador foram conferidos, e as configurações dos monitores físicos foram preservadas.

Passaram 43 testes Rust e 16 testes Android. O teste do canal autenticado conferiu o modo inicial, uma mudança de resolução e uma mudança apenas dos Hz. O EDID passou na verificação de conformidade, e a assinatura do APK foi conferida como compatível com as atualizações anteriores.

A validação no aparelho foi pelo Wi-Fi, sem cabo. As configurações USB foram preparadas para os novos modos, mas ainda falta repetir essas trocas pelo cabo. Os testes completos de desenho anteriores foram em 1280 × 800; ainda quero repeti-los na resolução nativa.

Configurar 90 Hz e o codificador a 90 fps não comprova que todos os quadros serão exibidos nessa taxa em qualquer situação. Não retomei a investigação de fluidez nem fiz comparação medida com o aplicativo da Samsung no Windows.

O código adaptado, os APKs, os desenhos e os relatórios completos continuam salvos no computador. Os testes seguem reunidos neste documento.
