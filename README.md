# Galaxy Tab Screen — Samsung Galaxy Tab como segunda tela no Linux

O **Galaxy Tab Screen** é o projeto que estou desenvolvendo para usar meu Samsung Galaxy Tab como segunda tela no Linux. Os testes são com um **Galaxy Tab S10 FE no Kubuntu/KDE Plasma**.

Já uso a tela independente com S Pen, touch e som pelo cabo ou pelo Wi-Fi. Pelo cabo, mantenho a depuração USB desligada.

Comecei com VNC, mas não gostei da experiência. Depois consegui imagem pelo “Segunda tela” da Samsung, usando o GNOME Network Displays. Funcionou em espelhamento. Para seguir com tela independente, caneta e dedos, comecei uma adaptação do [UScreen](https://github.com/majmichu1/UScreen).

Já consegui desenhar no Krita com pressão e inclinação da S Pen, apoiar a mão sem interferência nas rodadas e usar dois dedos para mover e ampliar a folha. O computador também oferece uma saída de som para o tablet. Agora a tela chegou à resolução nativa, **2304 × 1440 a 90 Hz**, e posso escolher resolução e atualização pelo PC ou pelo aplicativo.

Escolhi o nome **Galaxy Tab Screen**. O repositório ficou como `galaxy-tab-screen-linux`, com o título explicando o objetivo de usar o tablet Samsung como segunda tela no Linux.

Estou deixando o processo aqui porque quero aprender durante o desenvolvimento e entender os problemas que forem aparecendo.

Esse projeto também vem sendo construído com a colaboração de [Edmundo Neto](https://github.com/emal0n). A troca de ideias sobre desenvolvimento e produto ajuda a pensar nas próximas etapas.

## O que já funcionou

| Parte | O que conferi no S10 FE |
| --- | --- |
| Cabo sem depuração | Vídeo, entrada e áudio pela rede criada com a Ancoragem USB, sem ADB |
| Wi-Fi sem cabo | Tela independente, S Pen, touch e som pela rede local, sem ADB |
| Tela independente | Área adicional no KDE, chegando a 2304 × 1440 a 90 Hz pelo Wi-Fi, mantendo as telas físicas |
| Resolução e atualização | Escolha no KDE e no aplicativo; os controles do tablet acompanham as alterações feitas no PC |
| S Pen no Krita | Desenho alinhado à ponta e pressão mudando a espessura pelo cabo e pelo Wi-Fi |
| Inclinação | Traço mais grosso ao inclinar, com um pincel preparado para responder só à inclinação |
| Botão lateral | Move a folha no Krita, como botão do meio nessa configuração |
| Touch | Até cinco dedos na área de teste; dois dedos movem e ampliam/reduzem a folha no Krita |
| Mão apoiada | Desenhei sem interferência nas rodadas curtas, aproximando a caneta antes de apoiar a mão |
| Som | Saída “Galaxy Tab S10 FE (teste)” no PC, com reprodução e troca de saída conferidas por USB e Wi-Fi |
| Retirada do cabo | Aviso de desconexão no tablet, liberação dos contatos e retirada da tela virtual |
| Reconexão USB | A imagem voltou sozinha depois de reconectar na mesma porta e reativar a Ancoragem USB |

Os testes de desenho no Krita foram feitos em 1280 × 800. Depois validei a imagem na resolução nativa e a troca de modos pelo Wi-Fi. As novas opções também foram preparadas para USB, mas ainda falta repetir essas trocas pelo cabo. Os testes com a mão apoiada foram curtos e não cobrem todas as posições de uso.

Os resultados, os erros e as correções estão no [registro dos testes](docs/testes.md). Também reuni [o que já conferi do UScreen e o que ainda falta](docs/testes.md#conferencia-uscreen). As medições de desempenho e a descrição de segurança do projeto original não são validações desta adaptação.

## Resolução e Hz agora são uma escolha

A primeira imagem pelo cabo estava em 1280 × 800, com alvo de 30 fps. Mudei para 60 fps, mas continuei achando lenta. Depois aumentei para 90 fps e 60 Mbps. Melhorou muito!

1280 × 800 era a configuração inicial do teste. O aplicativo e o KDE agora oferecem:

| Resolução | Atualização |
| --- | --- |
| 2304 × 1440, nativa | 30, 60 ou 90 Hz |
| 1920 × 1200 | 30, 60 ou 90 Hz |
| 1600 × 1000 | 30, 60 ou 90 Hz |
| 1280 × 800 | 30, 60 ou 90 Hz |

Encontrei um problema ao mudar pelo PC: a imagem mudava, mas o aplicativo continuava mostrando a escolha antiga. No teste 10 isso foi corrigido. A linha “Ativa no PC” e os controles se atualizam mesmo com as configurações do tablet abertas.

Deixei 2304 × 1440 a 90 Hz e mantive os 60 Mbps. Não retomei a investigação de fluidez, e configurar 90 Hz não comprova 90 quadros exibidos por segundo em qualquer situação. Ainda não tenho uma comparação medida com o aplicativo da Samsung no Windows.

## Como está funcionando

Pelo cabo, a adaptação usa a rede criada pela **Ancoragem USB** do tablet. Pelo Wi-Fi, o computador e o tablet ficam na mesma rede local. O programa Linux cria a tela com EVDI, captura e codifica H.264 pela GPU. O aplicativo Android recebe o vídeo e devolve os eventos da caneta e dos dedos ao Linux.

Instalei o aplicativo manualmente e confirmei a sessão no tablet. O endereço usado pelo aplicativo fica o mesmo ao reconectar na porta USB testada, mesmo quando a rede recebe outro endereço automático. A sessão Wi-Fi usa o endereço próprio dessa rede. Não precisei ativar a depuração USB em nenhum desses testes.

O som usa um canal separado. Escolho a saída do tablet nas configurações de áudio do computador. Havia um problema que repetia no tablet o áudio já enviado à caixa Bluetooth; corrigi a origem da captura e repeti a troca de saída pelo Wi-Fi e pelo cabo. O som ficou somente na saída escolhida.

Essas mudanças fazem parte da adaptação local. A versão do UScreen usada como base depende de ADB no caminho USB consultado. O resultado pelo Wi-Fi descrito aqui também usa o aplicativo adaptado; o teste com o receptor nativo da Samsung continua separado.

## Como estou organizando

Estou fazendo por etapas para conseguir acompanhar e entender o processo. Nesta parte preferi seguir com os testes e reunir o registro depois. Os relatórios completos ficam salvos no meu computador; aqui ficam as explicações sem os endereços da rede e o inventário do computador.

Reuni as etapas no [registro dos testes](docs/testes.md), incluindo cabo, Wi-Fi, áudio, Krita e a escolha de resolução e Hz.

O [plano de testes](docs/plano.md) mostra o que pretendo fazer em seguida. A comparação dos projetos, os arquivos que consultei e os créditos estão nas [referências](docs/referencias.md).

Este repositório reúne a documentação. Os APKs ficam apenas nas páginas locais de teste e não serão disponibilizados no GitHub. O código adaptado e os APKs de teste ainda estão no computador; não publiquei uma versão pronta para uso. A adaptação parte do [UScreen](https://github.com/majmichu1/UScreen/tree/66f6bcdab918b8dcc44227dde00197f30a778c44), com os créditos e a licença MIT preservados na cópia local. A base Miracast continua sendo o [GNOME Network Displays](https://gitlab.gnome.org/GNOME/gnome-network-displays), no commit `c305023f1e6d8ab48d18c66a4efbd18e1df7cdee`, sem mudanças no código.
