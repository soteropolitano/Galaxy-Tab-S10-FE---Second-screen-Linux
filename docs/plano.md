# O que pretendo fazer agora

A tela independente já funcionou pelo cabo e pelo Wi-Fi, com S Pen, touch e saída de som selecionável no computador. No Krita, confirmei pressão, inclinação, botão lateral, gestos de dois dedos e desenho com a mão apoiada nas rodadas curtas.

Também consegui a imagem em 2304 × 1440 a 90 Hz. Posso escolher resolução e atualização no KDE ou no aplicativo, que agora acompanha as mudanças feitas no PC. Os relatos estão nas [etapas 13 a 17](testes.md#etapa-13).

## Repetir as novas opções pelo cabo

As trocas de resolução e Hz e a sincronização dos controles foram validadas pelo Wi-Fi. As configurações USB foram preparadas, mas ainda preciso repetir essas trocas pelo cabo, mantendo a depuração desligada.

Vou manter os endereços já usados. Se houver conflito ou mudança da sub-rede, preciso conferir antes de trocar. A reserva USB foi validada na porta usada nos testes.

A configuração ao fim da última rodada ficou em 2304 × 1440 a 90 Hz, com 60 Mbps. A investigação de fluidez continua encerrada por enquanto, como decidi na [etapa 12](testes.md#etapa-12).

## Facilitar o uso

Por enquanto a instalação é manual, as sessões são temporárias e as permissões dos dispositivos foram preparadas para os testes. Quero organizar a abertura, a conexão, a autorização e o encerramento para usar sem depender dos comandos e links de cada rodada.

O áudio também precisa acompanhar esse início e encerramento. A troca entre tablet e outra saída já funcionou por USB e Wi-Fi depois da correção da captura. Ainda falta conferir reprodução prolongada, retorno após suspensão e sincronização entre áudio e vídeo.

## Continuar usando a caneta e o touch

| Parte | O que ainda quero conferir |
| --- | --- |
| Resolução nativa | Repetir o desenho completo e conferir cantos e centro em 2304 × 1440 |
| Mão apoiada | Desenhar por mais tempo, variando posição e sequência de aproximação |
| Botão lateral | Escolher a ação mais útil e conferir em outros programas |
| Posição e escala | Conferir outras escalas e rotação sem mexer nas telas físicas |
| Suspensão e reconexão | Fechar e reabrir o aplicativo e suspender os aparelhos durante o uso |

Os testes do Krita pelo cabo e pelo Wi-Fi foram em 1280 × 800. A imagem e as trocas na resolução nativa vieram depois; não vou tratar as duas rodadas como se fossem o mesmo teste. Se houver evento de borracha, preciso conferir se a caneta e o Android realmente o enviam.

## Antes de publicar algo pronto para uso

Quero repetir os ciclos de conexão e desconexão, conferir sessões mais longas e revisar o transporte na rede local. A conexão atual usa autorização por sessão e aceita o tablet selecionado, mas não ganhou uma camada de criptografia. Os testes sem fio foram feitos na mesma rede local.

O código adaptado e os APKs continuam no computador. Ainda preciso organizar a instalação e as permissões antes de distribuir uma versão.

O Miracast transmitiu imagem em espelhamento. Nesse caminho ainda faltam resolver a colisão de endereços e investigar tela independente e retorno da caneta e dos dedos. O Wi-Fi já confirmado neste projeto usa o aplicativo adaptado.

## Conferir as outras opções do UScreen

Ainda faltam testar o modo mesa digitalizadora, rotação, todas as posições automáticas, alternância do comportamento do cursor, outros codificadores, redução da escala de transmissão, tela preta, touch desligado, vários tablets, troca automática entre USB e Wi-Fi e os avisos de atualização. Reuni os limites de cada ponto na [conferência do UScreen](testes.md#conferencia-uscreen).

A segurança também precisa de uma revisão própria, porque a Ancoragem USB e o Wi-Fi usam rede, enquanto o caminho USB original usa ADB e portas locais. A autorização por token já usada não substitui criptografia ou uma auditoria completa. Os números de desempenho e o comparativo de alternativas do original continuam como referências, sem tratar esses dados como medições minhas.

Os testes e as correções continuam no [registro único](testes.md). Os relatórios completos ficam salvos no meu computador.
