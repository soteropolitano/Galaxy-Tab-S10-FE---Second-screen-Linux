# Etapa 02: compilando e abrindo

21/10/2026

Preparei as dependências e compilei o GNOME Network Displays com Meson e Ninja. Mantive o aplicativo habilitado e deixei desabilitados o daemon e as integrações de zona do firewalld e de systemd-resolved.

A compilação terminou com seis avisos e nenhum erro. Os dois testes de metadados do projeto também passaram. Eles conferem os metadados, não a transmissão de vídeo.

A janela abriu pelo Wayland, mas o pedido inicial de captura foi cancelado. O aplicativo ficou na pasta de compilação; não foi instalado no sistema.

Ao conferir o vídeo, não encontrei `vah264enc` nem `vaapih264enc` no ambiente consultado. Antes de seguir para o tablet, precisava entender isso.

Na etapa seguinte descobri que aquela consulta estava limitada: o ambiente restrito não mostrava a GPU. Portanto, o resultado não significava que o computador não tinha aceleração. Precisava testar fora dele e tentar codificar algum vídeo de verdade.
