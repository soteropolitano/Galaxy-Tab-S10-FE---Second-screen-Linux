# Etapa 02: compilação

Data: 21/10/2026.

Compilei o GNOME Network Displays a partir do código estudado, sem instalar o aplicativo no sistema.

Usei Meson e Ninja, com o aplicativo habilitado e o daemon desabilitado. As integrações de zona do firewalld e de systemd-resolved também ficaram desabilitadas nessa compilação.

## Resultado

- Preparei as dependências necessárias para compilar.
- A compilação terminou com seis avisos e nenhum erro.
- Os dois testes de metadados fornecidos pelo projeto passaram.
- A janela abriu pelo Wayland. O pedido inicial de captura foi cancelado.

Os testes de metadados não verificam a transmissão de vídeo.

## O que ficou pendente

O ambiente consultado não encontrou os codificadores `vah264enc` e `vaapih264enc`. Antes de tentar avaliar desempenho, precisava entender o driver, o acesso à GPU e o catálogo do GStreamer.

Na etapa seguinte, descobri uma limitação importante dessa consulta: o ambiente restrito não mostrava a GPU. A ausência observada aqui não comprovava falta de aceleração no computador.

Próximo passo definido: testar a codificação de verdade.
