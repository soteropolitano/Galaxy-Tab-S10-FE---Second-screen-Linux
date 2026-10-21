# Etapa 06: primeira imagem no tablet

Data: 21/10/2026.

Conferi que a interface conflitante não atendia a uma carga ativa e guardei sua configuração. Retirei temporariamente apenas o endereço duplicado.

A restauração foi preparada antes do teste, com um processo separado e limite de tempo. A rede continuou existindo.

## Resultado

A conexão passou. A captura mostrou o pedido TCP, a resposta do computador e a negociação RTSP, incluindo `SETUP`, `PLAY` e respostas de sucesso.

O aplicativo chegou a `STREAMING`. A imagem apareceu no tablet em espelhamento.

| Verificação | Resultado |
| --- | --- |
| Configuração de vídeo escolhida | 1920 × 1080 a 30 quadros/s |
| Codificador identificado | `vah264enc`, pela GPU |
| Perfil H.264 | Constrained Baseline |
| Tela independente | Não validada; o teste foi em espelhamento |
| Duração entre STREAMING e parada manual | Cerca de 43 segundos |
| Pressão, touch, áudio e S Pen | Não testados |

A taxa configurada não comprova a taxa efetivamente recebida pelo tablet. Não medi atraso ou estabilidade longa.

## Como terminou

Parei o aplicativo e restaurei o endereço da ponte. Comparei a configuração da rede e a disposição dos monitores físicos com o estado anterior.

O observador teve erro de permissão ao tentar sinalizar o tcpdump. Ao fechar o aplicativo, a interface desapareceu e a captura terminou. O arquivo continuou legível, com 56 pacotes e zero descartes pelo kernel informados pelo tcpdump. A restauração tinha um processo separado e foi concluída.

## O que aprendi

Retirar o endereço duplicado permitiu passar do ponto onde as tentativas anteriores falhavam. É uma evidência forte de que a colisão bloqueava o canal de controle.

Como restaurei a rede original, o conflito voltou a existir. Este foi um experimento, não uma correção permanente.
