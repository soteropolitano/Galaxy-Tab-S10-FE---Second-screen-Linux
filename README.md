# Galaxy Tab S10 FE como segunda tela no Linux

Estou tentando usar meu Galaxy Tab S10 FE como segunda tela no Kubuntu, com a S Pen e o touch funcionando também. Prefiro conectar por cabo, mantendo a depuração USB desligada no tablet.

A primeira tentativa foi com VNC. Não gostei da experiência, então comecei a testar o GNOME Network Displays para conectar pelo recurso “Segunda tela” da Samsung.

Depois de algumas tentativas com erro, consegui fazer a imagem aparecer por Miracast! Esse teste foi em espelhamento. Pelo cabo, já conectei um aplicativo Android adaptado e recebi os eventos dos dedos. A tela independente no tablet, a S Pen no aplicativo e o desempenho no uso diário ainda estão em teste.

Estou deixando o processo aqui porque quero aprender durante o desenvolvimento. A ideia é conseguir acompanhar o que foi feito e entender os problemas que forem aparecendo.

## Até agora

O computador encontra o tablet por Wi-Fi Direct e já consegui transmitir imagem com Miracast. A codificação H.264 pela GPU também funcionou nos testes.

O problema que estava impedindo a conexão era um endereço IP duplicado: o tablet e outra interface do computador usavam o mesmo endereço. Retirei o endereço conflitante por um momento e a transmissão começou. Depois restaurei a configuração, então ainda preciso resolver isso de forma permanente. O teste está descrito na [etapa 06](docs/testes.md#etapa-06).

Essa transmissão foi curta, cerca de 43 segundos entre o início do vídeo e a parada manual. Ainda não tenho uma comparação de desempenho com o aplicativo da Samsung no Windows.

## S Pen, touch e USB

Comecei a olhar outros projetos para entender como fizeram essas partes. O [UScreen](https://github.com/majmichu1/UScreen) é uma referência útil para a tela e a entrada no KDE: tem aplicativo Android, tela estendida e código para levar pressão, inclinação e botão da caneta ao Linux, além do touch. O USB da versão que usei como base exige ADB e depuração. Comecei a adaptar essa parte para usar a rede criada pela ancoragem USB.

Encontrei também um [relato com S10 FE+ no KDE](https://github.com/majmichu1/UScreen/issues/23), com a confirmação de uma correção no botão da S Pen. Isso ajuda, mas ainda preciso testar no meu S10 FE.

Quero conferir algumas coisas:

- Colocar uma janela no tablet e continuar usando outras nas telas do computador;
- Tocar no lugar certo, arrastar e usar mais de um dedo nos aplicativos que aceitam;
- Desenhar com pressão, testar inclinação, aproximação e botão da S Pen;
- Apoiar a mão enquanto desenho sem provocar cliques e rolagens;
- Desconectar sem deixar uma tela virtual ou algum toque preso.

O compartilhamento de rede por USB, também chamado de USB tethering, já funcionou no S10 FE com a depuração desligada. O Kubuntu reconheceu a rede automaticamente, o tablet respondeu ao teste de comunicação e consegui abrir nele uma página servida pelo computador. Registrei esse teste na [etapa 08](docs/testes.md#etapa-08).

Já compilei uma adaptação do programa Linux e do aplicativo Android para conectar por essa rede sem ADB. Instalei o aplicativo de teste manualmente no tablet, confirmei a sessão e apareceu “Conexão USB confirmada”. O computador recebeu 1.894 eventos de touch, com até cinco dedos simultâneos. Esse modo só registra a entrada; ainda não controla os aplicativos Linux.

Fiz também um teste separado pelo navegador: o computador recebeu pressão e inclinação da S Pen e até cinco dedos simultâneos pela rede USB. Ainda não liguei esses eventos à entrada do Linux. Aproximação, botão lateral e rejeição de palma continuam pendentes. Os resultados estão na [etapa 09](docs/testes.md#etapa-09).

No aplicativo nativo ainda não chegaram eventos identificados como caneta. Preparei outra versão para entender o que o Android está enviando, mas ela ainda não foi instalada. O resultado do navegador não confirma a S Pen no aplicativo.

Também consegui criar uma tela virtual no KDE, capturá-la e codificar H.264 pela GPU para um cliente simulado no próprio computador. O vídeo recebido foi decodificado sem erros em 1280 × 800. Ainda falta mostrar essa imagem no S10 FE pelo cabo e conferir movimento, atraso e estabilidade. O relato está na [etapa 10](docs/testes.md#etapa-10).

O caminho sem fio continua no plano. O Miracast já deu imagem, mas a entrada pela função nativa da Samsung ainda precisa ser investigada. O próprio UScreen também tem uma opção Wi-Fi para testar depois.

## Como estou organizando

Estou fazendo por etapas para conseguir acompanhar e entender o processo. Nesta parte preferi seguir com os testes e reunir o registro depois. Os relatórios completos ficam salvos no meu computador; aqui ficam as explicações sem os endereços da rede e o inventário do computador.

Reuni as etapas no [registro dos testes](docs/testes.md), da escolha da base à adaptação do aplicativo e aos primeiros testes de vídeo pelo novo caminho.

O [plano de testes](docs/plano.md) mostra o que pretendo fazer em seguida. A comparação dos projetos, os arquivos que consultei e os créditos estão nas [referências](docs/referencias.md).

Este repositório reúne a documentação. O código adaptado e os APKs de teste ainda estão no computador; não publiquei uma versão pronta para uso. A adaptação parte do [UScreen](https://github.com/majmichu1/UScreen/tree/66f6bcdab918b8dcc44227dde00197f30a778c44), com os créditos e a licença MIT preservados na cópia local. A base Miracast continua sendo o [GNOME Network Displays](https://gitlab.gnome.org/GNOME/gnome-network-displays), no commit `c305023f1e6d8ab48d18c66a4efbd18e1df7cdee`, sem mudanças no código.
