# Galaxy Tab S10 FE como segunda tela no Linux

Estou usando meu Galaxy Tab S10 FE como segunda tela no Kubuntu, com a S Pen e o touch funcionando pelo cabo, mantendo a depuração USB desligada.

Comecei com VNC, mas não gostei da experiência. Depois consegui imagem pelo “Segunda tela” da Samsung, usando o GNOME Network Displays. Funcionou em espelhamento. Para seguir com tela independente, caneta e dedos, comecei uma adaptação do [UScreen](https://github.com/majmichu1/UScreen).

Agora já aparece uma tela adicional no tablet! Consegui desenhar numa área de teste no Linux, variar a espessura com a pressão da S Pen e usar até cinco dedos, com os toques alinhados. Com 90 Hz, a fluidez melhorou muito.

Estou deixando o processo aqui porque quero aprender durante o desenvolvimento e entender os problemas que forem aparecendo.

## O que já funcionou

| Parte | O que conferi no S10 FE |
| --- | --- |
| Cabo sem depuração | Vídeo e entrada pela rede criada com a Ancoragem USB, sem ADB |
| Tela independente | Área adicional de 1280 × 800 no KDE, mantendo as telas físicas |
| S Pen | Desenho no Linux com pressão mudando a espessura do traço |
| Inclinação e aproximação | Dados recebidos na área de teste do Linux |
| Botão lateral | Eventos de pressionar e soltar recebidos no Linux, sem abrir menu da Samsung na rodada |
| Touch | Até cinco dedos simultâneos, com posição alinhada |
| Mão apoiada | Desenhei sem interferência na rodada com a caneta próxima da tela |
| Retirada do cabo | Aviso de desconexão no tablet, liberação dos contatos e retirada da tela virtual |
| Reconexão | A imagem voltou sozinha depois de reconectar na mesma porta e reativar a Ancoragem USB |

O teste com a mão apoiada foi curto e seguiu essa sequência: aproximar a caneta primeiro e manter a ponta perto da tela. Ainda preciso conferir outras situações. Também falta usar um pincel que responda à inclinação num programa de desenho de uso diário.

Os detalhes, incluindo os erros até chegar aqui, estão nas [etapas 10](docs/testes.md#etapa-10) e [11](docs/testes.md#etapa-11).

## A mudança para 90 Hz

A primeira imagem pelo cabo estava configurada em 30 quadros por segundo. Mudei para 60 fps; a imagem voltou, mas continuei achando lenta. Depois aumentei para 90 fps e 60 Mbps, mantendo 1280 × 800. Melhorou muito!

Conferi que o KDE aplicou aproximadamente 90 Hz à tela virtual. Vou manter essa configuração e seguir com o uso, sem continuar a investigação de fluidez agora. O registro das mudanças e da amostra de captura está na [etapa 12](docs/testes.md#etapa-12).

Ainda não tenho uma comparação medida com o aplicativo da Samsung no Windows.

## Como está funcionando pelo cabo

A adaptação usa a rede criada pela **Ancoragem USB** do tablet. O programa Linux cria a tela com EVDI, captura e codifica H.264 pela GPU. O aplicativo Android recebe o vídeo e devolve os eventos da caneta e dos dedos ao Linux.

Instalei o aplicativo manualmente e confirmei a sessão no tablet. O endereço usado pelo aplicativo agora fica o mesmo ao reconectar na porta testada, mesmo quando a rede recebe outro endereço automático. Não precisei ativar a depuração USB.

A versão do UScreen que usei como base depende de ADB nesse caminho. A conexão direta pela ancoragem, os ajustes de desconexão e a configuração de endereço descritos aqui fazem parte da adaptação local.

O caminho sem fio continua no plano. O Miracast já deu imagem em espelhamento, mas ainda falta resolver o conflito de endereços encontrado naquele teste e investigar o retorno da S Pen e do touch pela função nativa da Samsung.

## Como estou organizando

Estou fazendo por etapas para conseguir acompanhar e entender o processo. Nesta parte preferi seguir com os testes e reunir o registro depois. Os relatórios completos ficam salvos no meu computador; aqui ficam as explicações sem os endereços da rede e o inventário do computador.

Reuni as etapas no [registro dos testes](docs/testes.md), da escolha da base aos testes de desenho, desconexão, reconexão e fluidez pelo cabo.

O [plano de testes](docs/plano.md) mostra o que pretendo fazer em seguida. A comparação dos projetos, os arquivos que consultei e os créditos estão nas [referências](docs/referencias.md).

Este repositório reúne a documentação. O código adaptado e os APKs de teste ainda estão no computador; não publiquei uma versão pronta para uso. A adaptação parte do [UScreen](https://github.com/majmichu1/UScreen/tree/66f6bcdab918b8dcc44227dde00197f30a778c44), com os créditos e a licença MIT preservados na cópia local. A base Miracast continua sendo o [GNOME Network Displays](https://gitlab.gnome.org/GNOME/gnome-network-displays), no commit `c305023f1e6d8ab48d18c66a4efbd18e1df7cdee`, sem mudanças no código.
