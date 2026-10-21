# Projetos que estou estudando

Consulta feita em 21/10/2026.

Reuni esses projetos para entender como cada um resolve a tela, o vídeo, a conexão e a entrada. Li a documentação, os trechos de código indicados abaixo e alguns relatos de uso. Ainda não instalei nem executei nenhum deles neste projeto.

## UScreen

O [UScreen](https://github.com/majmichu1/UScreen) parece o mais próximo do que procuro. Ele apresenta uma tela ao KDE usando EVDI, envia o vídeo ao aplicativo Android e recebe os eventos da caneta e dos dedos por outro canal. O caminho principal USB usa ADB; também existe uma opção Wi-Fi.

Fui conferir no código como a entrada é tratada:

- [TouchCapture.kt](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/android/app/src/main/java/com/uscreen/TouchCapture.kt) lê pressão, inclinação, orientação, aproximação e botão. Também acompanha os contatos dos dedos e filtra toques enquanto a caneta está ativa;
- [input.rs](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/host/src/input.rs) cria dispositivos uinput separados para caneta e touchscreen, com pressão, inclinação e contatos de multitouch;
- O mapeamento em input.rs usa o código de [kwin.rs](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/host/src/kwin.rs) para associar a entrada à tela correta;
- [encoders.rs](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/host/src/encoders.rs) tenta codificar um quadro para escolher um codificador que realmente funcione.

A [arquitetura](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/docs/architecture.md) descreve vídeo por TCP e entrada por WebSocket, em portas locais encaminhadas pelo `adb reverse`. Esse caminho não exige compartilhamento de rede USB.

Corrigi a preferência do projeto depois dessa leitura: quero o cabo com a depuração USB desligada. O modo USB atual do UScreen exige ADB, então vou usá-lo como referência para tela e entrada e avaliar outro transporte. A documentação dele coloca o transporte AOA, para remover essa exigência, entre os próximos trabalhos; não considerei isso um recurso já disponível.

O caminho a investigar primeiro é o compartilhamento de rede por USB. Para aproveitar o UScreen assim, preciso conferir conexão entre os aplicativos, endereços, abertura e autorização da sessão sem depender do ADB. Só ativar a rede USB no tablet não faz essa adaptação.

Encontrei o [relato de um S10 FE+ com KDE](https://github.com/majmichu1/UScreen/issues/23). O botão da S Pen não funcionava inicialmente; depois da correção no aplicativo Android, o usuário confirmou os eventos de pressionar e soltar. É uma referência útil, mas ainda não é um teste do meu S10 FE.

Também olhei a [lista de compatibilidade](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/docs/compatibility.md). Há diferenças grandes de atraso entre aparelhos. Vou precisar medir aqui, sem assumir o resultado do hardware usado pelo autor.

## TethrLink e o artigo sobre USB

O [artigo enviado](https://medium.com/@princesavsaviya2023.learning/i-turned-my-android-tablet-into-a-real-second-monitor-over-a-usb-cable-962c4c2e9758) leva ao [TethrLink](https://github.com/princesavsaviya/TethrLink). Ele usa uma rede criada pelo compartilhamento USB, tela virtual e H.264. As ideias de testar o codificador, controlar filas e respeitar a proporção da tela ajudam no estudo.

A entrada, porém, está ligada ao GNOME. O arquivo [remote_input.py](https://github.com/princesavsaviya/TethrLink/blob/75a66798bb10aee0d883dba3ef1a2360f72a5c73/server/core/remote_input.py) usa `org.gnome.Mutter.RemoteDesktop`. No [protocolo](https://github.com/princesavsaviya/TethrLink/blob/75a66798bb10aee0d883dba3ef1a2360f72a5c73/server/core/input_protocol.py), encontrei movimento de ponteiro, botão e rolagem, mas não campos de pressão e inclinação.

Com a preferência por cabo sem depuração, essa referência de rede USB passa a ter mais peso. Isso não muda a dependência de GNOME da implementação atual nem resolve a S Pen no KDE por si só.

Portanto, não consigo usar esse caminho diretamente no KDE para atender à caneta que quero. Converter gestos em cliques e rolagem também é diferente de enviar contatos de toque separados.

## arch-galaxytab-submonitor

O [projeto](https://github.com/yanghoeg/arch-galaxytab-submonitor) usa EDID e parâmetros de kernel para forçar uma saída HDMI, depois transmite com Sunshine/Moonlight. O autor validou vídeo por rede USB, mas registra a entrada de caneta e touch como não verificada.

Antes de tentar esse método, preciso conferir se a saída escolhida já atende a um monitor. Forçar um conector pode interferir numa tela existente. O instalador também é voltado ao Arch, então não é algo para aplicar diretamente no Kubuntu.

Outra diferença está no transporte: `adb reverse` encaminha TCP, enquanto o caminho Sunshine/Moonlight descrito usa UDP. Por isso, ali foi usada uma rede por USB.

## Second-Screen-Samsung-Linux

O [projeto](https://github.com/Mathweuzz/Second-Screen-Samsung-Linux) solicita uma tela virtual ao portal e transmite MJPEG para o navegador. Em [wayland_capture.c](https://github.com/Mathweuzz/Second-Screen-Samsung-Linux/blob/dff5eb594a5808eee25fc343759b8cc91927c1ea/src/wayland_capture.c), encontrei o pedido com `types = 4`. O [cliente HTML](https://github.com/Mathweuzz/Second-Screen-Samsung-Linux/blob/dff5eb594a5808eee25fc343759b8cc91927c1ea/client/index.html) lido mostra o vídeo, mas não envia entrada de volta.

Ajuda a entender a captura pelo portal. Apesar do nome, isso não demonstra uma integração com o receptor “Segunda tela” da Samsung. As afirmações de latência do README também não foram reproduzidas aqui.

A [documentação do portal](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.ScreenCast.html) explica o tipo VIRTUAL e a consulta `AvailableSourceTypes`. Preciso conferir o suporte no ambiente do teste e depois criar e usar a saída de fato.

## Android-as-second-monitor-for-linux

O [script](https://github.com/user12043/Android-as-second-monitor-for-linux) organiza uma tela virtual com XRandR e deixa a conexão para outro programa remoto. A documentação sugere ADB ou compartilhamento de rede USB.

Ele ajuda a entender a sequência de criar uma tela e conectar um cliente. Depende de X11 e não implementa um protocolo próprio para caneta com pressão. A entrada fica por conta do programa remoto escolhido.

## Gist de 8bitbuddhist

O [gist](https://gist.github.com/8bitbuddhist/7ab180286d6eb7fdc68d374063814175?permalink_comment_id=4065391) combina XRandR, x11vnc e `adb reverse`. É um exemplo simples de como o cabo pode levar uma conexão TCP.

Fica como referência desse transporte. O script não implementa os dados da caneta e o caminho de tela também é de X11.

## VirtScreen

O [VirtScreen](https://github.com/kbumsik/VirtScreen) coloca uma interface sobre XRandR e x11vnc, com opções de posição, retrato e HiDPI. Essas opções ajudam a pensar na organização da tela.

Foi feito para Xorg e não apresenta uma implementação própria de pressão da caneta. Como já tive uma experiência ruim com VNC e estou usando Wayland, não será a primeira tentativa.

## Tópico screen-extender e Moreland

O [tópico](https://github.com/topics/screen-extender) é uma lista de projetos. Por ele cheguei ao [Moreland](https://github.com/adiimanav/moreland), que tem vídeo por ADB/USB e VA-API.

O próprio README informa que não há entrada de toque ou caneta e que o KWin precisa de outro backend de captura. Vou aproveitar as referências de transporte e medição, mas ele não fecha os requisitos atuais.

## E a base Miracast que já estou usando?

Na versão estudada do GNOME Network Displays, encontrei `wfd_uibc_capability` numa lista de parâmetros. Não encontrei implementação de recepção e injeção da caneta e do touch na pesquisa feita. As ocorrências de `touch_session` mantêm a sessão RTSP ativa; não são eventos de dedo na tela.

Vou manter o resultado de vídeo. Para ter a entrada pelo receptor nativo da Samsung, ainda preciso investigar o que ele anuncia, implementar um retorno compatível e conferir quais dados realmente envia. Pressão e inclinação continuam pendentes nesse caminho.

Para acompanhar essa parte, estou usando a [documentação de caneta do Android](https://developer.android.com/develop/ui/views/touch-and-input/stylus-input/advanced-stylus-features) e a [documentação do uinput](https://docs.kernel.org/input/uinput.html). Os dados precisam sair do Android, atravessar o protocolo e chegar ao aplicativo Linux. Se o aparelho não emitir um evento, declarar o campo no computador não resolve.

## Versões consultadas

Deixei os commits abaixo para conseguir voltar ao código que li. São versões de consulta, não versões instaladas neste projeto.

| Projeto | Commit |
| --- | --- |
| UScreen | `66f6bcdab918b8dcc44227dde00197f30a778c44` |
| Android-as-second-monitor-for-linux | `bb94169edde8cde9dc280373c091ca9a679245ce` |
| Second-Screen-Samsung-Linux | `dff5eb594a5808eee25fc343759b8cc91927c1ea` |
| arch-galaxytab-submonitor | `a40a995c17a76999a827dddb99d81e41ab8a376a` |
| VirtScreen | `9637d628165cd46db0873dbd82af2bfd91d0b7d2` |
| TethrLink | `75a66798bb10aee0d883dba3ef1a2360f72a5c73` |
| Moreland | `71187c3c8e50d70b8e93ed9ae10934f7d01ad315` |
