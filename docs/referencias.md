# O que aprendi com os projetos

Consulta: 21/10/2026. Li a documentação dos projetos, os caminhos de código indicados abaixo e relatos de uso. Não instalei nem executei esses projetos nesta etapa.

## Comparação para o meu objetivo

| Referência | Caminho de vídeo e USB | Caneta e touch | Aproveitamento no Kubuntu |
| --- | --- | --- | --- |
| [UScreen](https://github.com/majmichu1/UScreen) | Tela EVDI, H.264/HEVC, cliente Android e ADB por USB; também documenta Wi-Fi | Código de pressão, inclinação, aproximação, botão e contatos de toque via uinput | Referência prioritária; tem integração específica com KDE Wayland |
| [Android-as-second-monitor-for-linux](https://github.com/user12043/Android-as-second-monitor-for-linux) | XRandR e programa remoto externo; sugere ADB ou rede USB | Depende do programa remoto; o script não implementa caneta com pressão | Ajuda a entender o fluxo, mas depende de X11 |
| [Second-Screen-Samsung-Linux](https://github.com/Mathweuzz/Second-Screen-Samsung-Linux) | Portal/PipeWire e MJPEG no navegador | O cliente lido só exibe a imagem; não tem envio de eventos | Referência de captura virtual; não substitui o receptor Miracast da Samsung |
| [arch-galaxytab-submonitor](https://github.com/yanghoeg/arch-galaxytab-submonitor) | Saída HDMI forçada por EDID, Sunshine/Moonlight e rede USB | O autor registra retorno de entrada como não verificado | Aproveitar a separação entre tela, vídeo e entrada; não aplicar o instalador Arch diretamente |
| [Gist de 8bitbuddhist](https://gist.github.com/8bitbuddhist/7ab180286d6eb7fdc68d374063814175?permalink_comment_id=4065391) | XRandR, x11vnc e `adb reverse` | O script não implementa um protocolo de caneta | Exemplo simples de USB levando uma conexão TCP |
| [VirtScreen](https://github.com/kbumsik/VirtScreen) | Interface para XRandR e x11vnc | Não apresenta implementação própria de pressão da caneta | Ideias de interface e organização da tela; base feita para Xorg |
| [Artigo sobre USB / TethrLink](https://medium.com/@princesavsaviya2023.learning/i-turned-my-android-tablet-into-a-real-second-monitor-over-a-usb-cable-962c4c2e9758) | GNOME/Mutter, H.264 e rede USB, com cliente Android | Emulação de ponteiro; gestos não equivalem a uma mesa digitalizadora | Aproveitar ideias de transporte e vídeo; as APIs de entrada lidas são específicas do GNOME |
| [Tópico screen-extender](https://github.com/topics/screen-extender) | Lista de projetos, não uma implementação | Varia por projeto | Usei para localizar Moreland e conferir seu escopo |

## UScreen: a referência mais próxima

O projeto usa EVDI para apresentar uma tela ao compositor. Depois captura os quadros, codifica e leva o vídeo ao aplicativo Android. O retorno da entrada usa outro canal.

Isso permite tratar caneta como caneta e dedos como touchscreen no Linux. No código consultado:

- [TouchCapture.kt](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/android/app/src/main/java/com/uscreen/TouchCapture.kt) lê pressão, tipo de ferramenta, inclinação/orientação, aproximação e botão. Também acompanha os contatos dos dedos e filtra toques enquanto a caneta está ativa.
- [input.rs](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/host/src/input.rs) cria dispositivos uinput separados, declara os eixos de pressão/inclinação e usa contatos de multitouch. A presença desse código não comprova o resultado em cada aplicativo.
- [kwin.rs](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/host/src/kwin.rs) e o mapeamento em input.rs usam D-Bus para associar os dispositivos à saída correta. Esse ponto merece teste em áreas de trabalho com várias telas.
- [encoders.rs](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/host/src/encoders.rs) faz uma codificação de prova para decidir se um codificador realmente funciona. VA-API é um dos caminhos a avaliar no teste.
- A [arquitetura](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/docs/architecture.md) descreve vídeo TCP e entrada WebSocket em portas locais, levadas pelo `adb reverse`. Não exige compartilhamento de rede USB nesse caminho.

O [relato #23](https://github.com/majmichu1/UScreen/issues/23) é de um S10 FE+ com KDE Wayland, não do meu S10 FE. O botão não funcionava inicialmente. Depois da correção no aplicativo Android, o usuário confirmou eventos de pressionar e soltar. Isso é mais útil para planejar o teste do que apenas uma lista de recursos no README.

A [documentação de compatibilidade](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/docs/compatibility.md) também registra resultados muito diferentes de atraso entre aparelhos. Não vou transferir os números do hardware de referência para o meu computador.

## TethrLink: boas ideias, outro compositor

O artigo aponta para o [TethrLink](https://github.com/princesavsaviya/TethrLink). O autor descreve um caminho por rede USB, captura virtual e H.264. As ideias de testar o codificador, controlar as filas e respeitar a proporção da tela ajudam no projeto.

No código do repositório, [remote_input.py](https://github.com/princesavsaviya/TethrLink/blob/75a66798bb10aee0d883dba3ef1a2360f72a5c73/server/core/remote_input.py) usa `org.gnome.Mutter.RemoteDesktop`. O [protocolo de entrada](https://github.com/princesavsaviya/TethrLink/blob/75a66798bb10aee0d883dba3ef1a2360f72a5c73/server/core/input_protocol.py) transporta movimento de ponteiro, botão e rolagem. Não encontrei nesse protocolo campos de pressão e inclinação.

Portanto, não é uma solução pronta para o KDE nem uma referência completa de caneta. Gestos convertidos em cliques e rolagem podem ser úteis, mas são diferentes de contatos de toque independentes.

## Sunshine/Moonlight e o cuidado com a tela física

O README do arch-galaxytab-submonitor separa bem o que foi testado do que ainda não foi. O autor validou vídeo por rede USB, mas registra caneta/touch como não verificados.

Ele usa parâmetros de kernel e EDID para forçar uma saída HDMI. Antes de aplicar esse método, é preciso conferir se a saída escolhida já atende a um monitor. Não posso presumir que forçar um conector criará uma tela livre sem afetar uma existente.

Outra lição é de transporte: `adb reverse` encaminha TCP; o caminho Sunshine/Moonlight descrito usa UDP e por isso adota rede por USB. “Tem USB” não significa que todos os projetos usam o mesmo método.

## Pedido de tela virtual não é garantia de criação

O projeto Second-Screen-Samsung-Linux solicita `types = 4` em [wayland_capture.c](https://github.com/Mathweuzz/Second-Screen-Samsung-Linux/blob/dff5eb594a5808eee25fc343759b8cc91927c1ea/src/wayland_capture.c). O [cliente HTML](https://github.com/Mathweuzz/Second-Screen-Samsung-Linux/blob/dff5eb594a5808eee25fc343759b8cc91927c1ea/client/index.html) lido contém a imagem MJPEG, sem um caminho de retorno de entrada.

A [documentação oficial do portal](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.ScreenCast.html) define o tipo VIRTUAL e a consulta `AvailableSourceTypes`. A disponibilidade precisa ser consultada no ambiente do teste. Ainda falta validar a criação e o uso da saída na prática.

O nome do projeto não comprova integração com o aplicativo “Segunda tela” da Samsung. Também não tratei as afirmações de latência do README como medidas reproduzidas aqui.

## O que encontrei no tópico screen-extender

O [Moreland](https://github.com/adiimanav/moreland) tem um caminho de vídeo por ADB/USB e VA-API. O próprio README diz que não implementa entrada de toque ou caneta e que o caminho de captura precisa de outro backend para o KWin.

Serve como referência de transporte e medição. Não é a opção prioritária para fechar os requisitos no Kubuntu atual.

## O que muda na base Miracast

Na versão do GNOME Network Displays estudada, encontrei `wfd_uibc_capability` em uma lista de parâmetros. Não encontrei implementação de recepção e injeção da caneta/touch no código pesquisado. As ocorrências de `touch_session` são manutenção da sessão RTSP, não toque na tela.

Vou manter o resultado de vídeo já conseguido. Para usar entrada pelo receptor nativo da Samsung, ainda seria necessário investigar o que ele anuncia, implementar o retorno compatível e testar quais dados realmente envia. Pressão e inclinação não estão garantidas nesse caminho.

## Pontos técnicos para o desenvolvimento

A [documentação Android de caneta](https://developer.android.com/develop/ui/views/touch-and-input/stylus-input/advanced-stylus-features) explica os dados de pressão, inclinação, orientação e aproximação que o cliente pode ler. Esses dados precisam atravessar o protocolo e chegar ao aplicativo Linux.

A [documentação do uinput](https://docs.kernel.org/input/uinput.html) explica como criar dispositivos de entrada virtuais a partir de um programa. É a peça usada pelo UScreen para apresentar a entrada recebida ao Linux.

Se o Android ou a caneta não gerar determinado evento, declarar o recurso no lado Linux não o cria.

## Versões consultadas

| Projeto | Commit |
| --- | --- |
| UScreen | `66f6bcdab918b8dcc44227dde00197f30a778c44` |
| Android-as-second-monitor-for-linux | `bb94169edde8cde9dc280373c091ca9a679245ce` |
| Second-Screen-Samsung-Linux | `dff5eb594a5808eee25fc343759b8cc91927c1ea` |
| arch-galaxytab-submonitor | `a40a995c17a76999a827dddb99d81e41ab8a376a` |
| VirtScreen | `9637d628165cd46db0873dbd82af2bfd91d0b7d2` |
| TethrLink | `75a66798bb10aee0d883dba3ef1a2360f72a5c73` |
| Moreland | `71187c3c8e50d70b8e93ed9ae10934f7d01ad315` |

São versões de consulta, não versões instaladas ou testes de compatibilidade deste projeto.
