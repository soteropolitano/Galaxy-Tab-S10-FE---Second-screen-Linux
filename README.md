# Galaxy Tab S10 FE como segunda tela no Linux

Quero usar meu Galaxy Tab S10 FE como uma segunda tela no Kubuntu, com S Pen e touch funcionando. Também quero ter a opção de conectar por USB.

Comecei testando VNC, mas a experiência ficou ruim. Depois parti para o GNOME Network Displays e consegui transmitir imagem para o tablet usando o recurso “Segunda tela” da Samsung.

Por enquanto, funcionou em espelhamento. A tela independente, a caneta e o touch ainda precisam ser testados.

Este repositório é o registro desse desenvolvimento. Vou deixar aqui o que fiz, o que deu errado e como resolvi, para aprender durante o processo e facilitar a reprodução dos testes.

## Como está agora

| Parte | Situação |
| --- | --- |
| Encontrar o tablet por Wi-Fi Direct | Funcionou |
| Transmitir imagem com Miracast | Funcionou em um teste curto, em espelhamento |
| Codificar H.264 pela GPU | Validado em teste sintético e identificado na transmissão real |
| Usar o tablet como tela independente | Pendente |
| Touch e gestos | Pendente |
| S Pen com pressão, inclinação e botão | Pendente |
| Conexão USB | Referências estudadas; teste pendente |
| Solução permanente para o conflito de rede | Pendente |

Não tenho ainda uma comparação de desempenho com o aplicativo da Samsung no Windows. O teste que deu imagem durou cerca de 43 segundos entre o início da transmissão e a parada manual.

## O problema que estava impedindo a conexão

O tablet e outra interface do computador estavam usando o mesmo endereço IP. O pedido de conexão chegava, mas não recebia a resposta inicial.

Retirei temporariamente o endereço conflitante de uma interface sem uso ativo naquele momento. A transmissão começou. Depois do teste, restaurei a configuração original.

Isso deu uma evidência forte de que o endereço duplicado bloqueava a conexão. Como a configuração foi restaurada, ainda falta uma solução permanente.

O procedimento e os limites estão na [etapa 06](docs/etapas/06-primeira-imagem.md). Os comandos dessa investigação dependem da rede de cada computador.

## S Pen, touch e USB

Passei a estudar outros projetos para aproveitar o que já existe.

O [UScreen](https://github.com/majmichu1/UScreen) é a referência mais próxima do que quero: tem cliente Android, vídeo por USB usando ADB, tela estendida e um caminho de entrada para caneta e touch no KDE Wayland. Encontrei código para pressão, inclinação, aproximação da caneta e botão, além de contatos de toque separados.

Também há um [relato com S10 FE+ e KDE](https://github.com/majmichu1/UScreen/issues/23), incluindo a confirmação de uma correção para o botão da S Pen. Isso ajuda a escolher o próximo teste, mas não confirma o funcionamento no meu S10 FE.

Vou manter o teste Miracast como base do caminho sem fio e usar o UScreen como referência prioritária para USB e entrada. Se ele atender ao objetivo, faz sentido aproveitar o projeto e contribuir com correções antes de refazer tudo.

USB, nesse caso, é transporte de dados para um aplicativo no tablet. Não é transformar o aparelho em uma entrada HDMI. O caminho ADB também exige depuração USB; outros projetos usam compartilhamento de rede por USB.

A comparação dos links e os detalhes estão no [estudo das referências](docs/referencias.md).

## O que preciso considerar funcionando

- Uma área de trabalho independente no tablet, com janelas diferentes das telas do computador.
- Touch no lugar certo, sem mandar o toque para outro monitor.
- Gestos com mais de um dedo nos aplicativos que os aceitam.
- S Pen reconhecida como caneta, com pressão, inclinação, aproximação e botão testados no aparelho.
- Rejeição de toques da mão enquanto desenho, com comportamento previsível.
- Conexão por USB validada e o caminho sem fio documentado separadamente.
- Desconectar sem deixar tela virtual, toque ou botão presos.

Usar a ponta da S Pen como um clique de mouse não fecha o requisito de caneta. Quero verificar os dados de pressão e o resultado em um aplicativo de desenho, como o Krita.

## Registro das etapas

| Etapa | O que foi feito |
| --- | --- |
| [01](docs/etapas/01-base-e-diagnostico.md) | Escolha da base e diagnóstico do computador |
| [02](docs/etapas/02-compilacao.md) | Compilação e primeira abertura |
| [03](docs/etapas/03-h264-na-gpu.md) | Validação de H.264 pela GPU |
| [04](docs/etapas/04-primeiras-tentativas.md) | Descoberta do tablet e primeiras falhas de conexão |
| [05](docs/etapas/05-conflito-de-rede.md) | Captura de controle e identificação do endereço duplicado |
| [06](docs/etapas/06-primeira-imagem.md) | Primeira imagem com o conflito temporariamente retirado |
| [07](docs/etapas/07-caneta-touch-usb.md) | Estudo de S Pen, touch e USB; documentação no GitHub |

O [plano de testes](docs/plano.md) mostra a sequência proposta. Cada etapa termina com um registro legível e uma pausa antes de continuar.

## Sobre este repositório

Ainda é um projeto em desenvolvimento. Não há aqui um aplicativo próprio pronto para instalar.

A base Miracast estudada é o [GNOME Network Displays](https://gitlab.gnome.org/GNOME/gnome-network-displays), no commit `c305023f1e6d8ab48d18c66a4efbd18e1df7cdee`, sem modificações no código até a etapa 07.

Os relatórios completos de cada etapa são entregues localmente. Aqui ficam os registros públicos, sem o inventário da máquina ou os endereços da rede.

Os projetos consultados estão creditados nas referências. Não publiquei aqui o código deles nem os arquivos brutos de captura de rede. Quando houver código derivado, vou manter a identificação da origem e os avisos da licença correspondente.
