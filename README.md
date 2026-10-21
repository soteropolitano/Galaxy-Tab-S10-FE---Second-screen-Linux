# Galaxy Tab S10 FE como segunda tela no Linux

Estou tentando usar meu Galaxy Tab S10 FE como segunda tela no Kubuntu, com a S Pen e o touch funcionando também. Se conseguir usar por USB, melhor ainda.

A primeira tentativa foi com VNC. Não gostei da experiência, então comecei a testar o GNOME Network Displays para conectar pelo recurso “Segunda tela” da Samsung.

Depois de algumas tentativas com erro, consegui fazer a imagem aparecer! Por enquanto está espelhando uma tela do computador. Ainda preciso conseguir uma área independente, testar a caneta e o touch e ver como fica o desempenho no uso diário.

Estou deixando o processo aqui porque quero aprender durante o desenvolvimento. A ideia é conseguir acompanhar o que foi feito e entender os problemas que forem aparecendo.

## Até agora

O computador encontra o tablet por Wi-Fi Direct e já consegui transmitir imagem com Miracast. A codificação H.264 pela GPU também funcionou nos testes.

O problema que estava impedindo a conexão era um endereço IP duplicado: o tablet e outra interface do computador usavam o mesmo endereço. Retirei o endereço conflitante por um momento e a transmissão começou. Depois restaurei a configuração, então ainda preciso resolver isso de forma permanente. O teste está descrito na [etapa 06](docs/etapas/06-primeira-imagem.md).

Essa transmissão foi curta, cerca de 43 segundos entre o início do vídeo e a parada manual. Ainda não tenho uma comparação de desempenho com o aplicativo da Samsung no Windows.

## S Pen, touch e USB

Comecei a olhar outros projetos para entender como fizeram essas partes. O [UScreen](https://github.com/majmichu1/UScreen) parece o caminho mais próximo do que estou procurando: tem aplicativo Android, conexão USB por ADB, tela estendida e código para levar pressão, inclinação e botão da caneta ao Linux, além do touch.

Encontrei também um [relato com S10 FE+ no KDE](https://github.com/majmichu1/UScreen/issues/23), com a confirmação de uma correção no botão da S Pen. Isso ajuda, mas ainda preciso testar no meu S10 FE.

Quero conferir algumas coisas:

- Colocar uma janela no tablet e continuar usando outras nas telas do computador;
- Tocar no lugar certo, arrastar e usar mais de um dedo nos aplicativos que aceitam;
- Desenhar com pressão, testar inclinação, aproximação e botão da S Pen;
- Apoiar a mão enquanto desenho sem provocar cliques e rolagens;
- Desconectar sem deixar uma tela virtual ou algum toque preso.

Vou começar pelo caminho USB do UScreen. Nesse caso, o cabo leva os dados para o aplicativo Android usando ADB e exige depuração USB. Outros projetos fazem isso por compartilhamento de rede USB. Não são o mesmo procedimento.

O caminho sem fio continua no plano. O Miracast já deu imagem, mas a entrada pela função nativa da Samsung ainda precisa ser investigada. O próprio UScreen também tem uma opção Wi-Fi para testar depois.

## Como estou organizando

Estou fazendo por etapas, com um registro do que aconteceu e uma pausa antes de continuar. Os relatórios completos são entregues localmente; aqui ficam as explicações sem os endereços da rede e o inventário do computador.

1. [Escolha da base e diagnóstico](docs/etapas/01-base-e-diagnostico.md)
2. [Compilação e primeira abertura](docs/etapas/02-compilacao.md)
3. [Teste de H.264 pela GPU](docs/etapas/03-h264-na-gpu.md)
4. [Primeiras tentativas com o tablet](docs/etapas/04-primeiras-tentativas.md)
5. [Investigação do conflito de rede](docs/etapas/05-conflito-de-rede.md)
6. [Primeira imagem](docs/etapas/06-primeira-imagem.md)
7. [Estudo de S Pen, touch e USB](docs/etapas/07-caneta-touch-usb.md)

O [plano de testes](docs/plano.md) mostra o que pretendo fazer em seguida. A comparação dos projetos, os arquivos que consultei e os créditos estão nas [referências](docs/referencias.md).

Ainda não há um aplicativo próprio pronto para instalar neste repositório. A base Miracast usada é o [GNOME Network Displays](https://gitlab.gnome.org/GNOME/gnome-network-displays), no commit `c305023f1e6d8ab48d18c66a4efbd18e1df7cdee`, sem alterações no código até a etapa 07.
