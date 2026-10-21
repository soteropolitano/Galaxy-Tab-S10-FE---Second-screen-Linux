# Etapa 07: S Pen, touch e USB

21/10/2026

Depois de conseguir imagem, fui olhar como outros projetos tratam a caneta, o touch e a conexão por cabo. Quero essas partes funcionando também, então reuni os links em [referências](../referencias.md) e comecei a estudar documentação, código e relatos de uso.

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

Vou começar preparando o USB com UScreen: conferir EVDI, ADB, acesso ao uinput, versões do programa e do APK e o caminho da GPU. Depois quero testar a tela independente, o touch e os dados da S Pen, cada um com sua verificação.

O Miracast continua no projeto. Conseguir vídeo pela função nativa da Samsung ainda não resolveu o retorno da caneta e dos dedos.
