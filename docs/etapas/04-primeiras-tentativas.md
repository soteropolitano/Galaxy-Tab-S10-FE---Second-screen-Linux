# Etapa 04: tentando conectar

21/10/2026

Abri “Segunda tela” no Samsung e o computador encontrou o S10 FE por Wi-Fi Direct. Antes, parei o serviço usado na tentativa com VNC para não confundir as telas. Ele não foi reativado.

A sessão de captura foi criada no KDE, a rede direta recebeu endereço e o aplicativo reconheceu o codificador. Mesmo assim, não aparecia imagem.

Nos registros, ele ficava em `WAIT_SOCKET`, aguardando a conexão de controle, e depois dava erro. O aplicativo também fechou em uma tentativa. Não encontrei um registro de queda que explicasse o fechamento.

A descoberta do tablet e a criação da rede estavam passando. O problema aparecia depois, antes do início da transmissão.

Ainda não tinha uma captura dos pacotes para dizer o que estava acontecendo. Encerrei o teste, conferi os monitores físicos e deixei essa observação para a etapa seguinte.
