# Etapa 06: apareceu imagem!

21/10/2026

Conferi que a interface com o endereço duplicado não estava atendendo a uma carga ativa, guardei a configuração e retirei somente aquele endereço. A rede continuou existindo.

Também deixei um processo separado encarregado da restauração, com limite de tempo. Queria conseguir comparar o resultado e depois voltar à configuração anterior.

Dessa vez conectou. A captura mostrou a resposta do computador ao pedido TCP e a negociação RTSP, com `SETUP`, `PLAY` e respostas de sucesso. O aplicativo chegou a `STREAMING` e a imagem apareceu no S10 FE.

Por enquanto foi espelhamento. Ainda não era uma tela independente.

O aplicativo escolheu 1920 × 1080 a 30 quadros/s, com `vah264enc` e perfil H.264 Constrained Baseline pela GPU. Essa é a configuração registrada; não medi quantos quadros o tablet recebeu de fato.

Deixei transmitir por cerca de 43 segundos, contados entre o início do vídeo e a parada manual. Não testei áudio, touch ou S Pen nesse intervalo.

Ao terminar, parei o aplicativo e restaurei o endereço. Conferi a configuração da rede e a disposição dos monitores físicos.

O observador teve um problema ao tentar sinalizar o tcpdump: recebeu erro de permissão. Quando o aplicativo fechou, a interface desapareceu e a captura terminou. O arquivo permaneceu legível, com 56 pacotes e zero descartes pelo kernel informados pelo tcpdump. A restauração, que tinha outro processo, foi concluída.

Retirar o endereço duplicado fez a conexão passar de onde vinha falhando. Isso deu uma evidência forte de que a colisão estava bloqueando o controle. Como restaurei a configuração original, ainda preciso resolver o conflito de forma permanente.
