# Etapa 04: primeiras tentativas com o tablet

Data: 21/10/2026.

Abri “Segunda tela” no Samsung. O aplicativo no computador encontrou o S10 FE por Wi-Fi Direct.

Antes do teste, parei o serviço da tentativa anterior com VNC para não confundir as telas. Ele não foi reativado.

## Até onde chegou

O KDE criou uma sessão de captura. A ligação P2P recebeu endereço IPv4, o aplicativo reconheceu o codificador e ficou escutando na porta TCP de controle.

Depois, a conexão parava em `WAIT_SOCKET` e passava para erro. Não chegou ao estado de transmissão e não apareceu imagem no tablet. O aplicativo também fechou em uma tentativa; não encontrei um registro de queda que permitisse atribuir uma causa.

## O que isso significava

A descoberta do aparelho e a criação da rede já estavam funcionando. Faltava entender por que o canal de controle não começava.

Ainda não havia captura de pacotes para localizar a falha. A etapa terminou sem uma causa confirmada.

Encerrei o aplicativo e preservei as posições dos monitores físicos.

Próximo passo definido: observar os pacotes de controle durante uma tentativa.
