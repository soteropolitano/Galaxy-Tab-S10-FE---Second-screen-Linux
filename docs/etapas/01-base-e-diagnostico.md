# Etapa 01: escolhendo a base

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
