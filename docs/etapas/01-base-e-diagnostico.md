# Etapa 01: base e diagnóstico

Data: 21/10/2026.

Quero usar o S10 FE como uma tela a mais no Kubuntu. A primeira experiência com VNC ficou ruim, então comecei a estudar o GNOME Network Displays para usar o recurso “Segunda tela” do Samsung por Miracast.

Baixei o código oficial, versão 0.99.0, no commit `c305023f1e6d8ab48d18c66a4efbd18e1df7cdee`. Nesta etapa, o objetivo foi entender a base e conferir os requisitos.

## O que encontrei

- O caminho escolhido deve funcionar no Kubuntu com sessão Wayland.
- Consultei os requisitos de captura, codificação e conexão direta.
- O driver de Wi-Fi anuncia os modos P2P necessários para uma tentativa de Wi-Fi Direct.
- A base usa captura por portal/PipeWire e codificação de vídeo pelo GStreamer.
- O código solicita também a possibilidade de capturar uma tela virtual.

Isso justificava testar, mas ainda não comprovava uma conexão com o tablet. Não compilei nem transmiti nesta etapa.

## Como a imagem deve chegar ao tablet

```text
Tela do KDE -> portal e PipeWire -> codificador H.264
            -> ligação Miracast -> receptor no tablet
```

O retorno da caneta e do touch é outro caminho. Não basta o vídeo chegar para esses recursos funcionarem.

Próximo passo definido: compilar a base e conferir a primeira abertura.
