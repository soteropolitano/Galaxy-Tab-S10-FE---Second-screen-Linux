# O que pretendo fazer agora

Já consegui a tela independente pelo cabo, com a depuração USB desligada, desenho com pressão da S Pen e até cinco dedos no Linux. Também testei a retirada do cabo e a reconexão automática na mesma porta, mantendo o endereço usado pelo aplicativo.

A mudança para 90 fps e 60 Mbps melhorou muito a fluidez, mantendo 1280 × 800. Vou manter esses valores e seguir com o uso. A investigação de lentidão fica encerrada por enquanto, como registrei na [etapa 12](testes.md#etapa-12).

Vou manter também o mesmo endereço de conexão. Se houver conflito ou mudança da sub-rede, preciso conferir antes de trocar. A reserva atual foi validada na porta USB usada nos testes.

## Caneta e touch no uso diário

A área de desenho no Linux já recebeu pressão, inclinação, aproximação, botão e contatos separados dos dedos. Agora falta sair desse teste controlado:

| Parte | O que quero conferir |
| --- | --- |
| Pressão | Desenhar no Krita e ajustar o pincel ao uso |
| Inclinação | Usar um pincel que responda aos eixos recebidos |
| Botão lateral | Conferir a ação nos programas de desenho |
| Mão apoiada | Variar posição e sequência de aproximação da caneta e da mão |
| Multitouch | Testar gestos num aplicativo que os aceite |
| Posição e escala | Conferir cantos, outras escalas e rotação |

O teste curto com a mão apoiada passou, mas ainda não cobre todas as condições de uso. Se houver evento de borracha, preciso conferir se a caneta e o Android realmente o enviam.

## Estabilidade e instalação

A retirada com contato ativo já liberou a entrada e retirou a tela virtual. A reconexão também funcionou mantendo o aplicativo aberto e reativando a Ancoragem USB. Quero repetir esse ciclo, testar sessões mais longas e conferir o retorno depois de suspender ou fechar o aplicativo.

Por enquanto a instalação é manual e as permissões de dispositivos foram preparadas para os testes. Ainda preciso organizar instalação, abertura, encerramento e autorização antes de publicar algo pronto para uso. O código adaptado e os APKs continuam locais.

## Sem fio

O Miracast transmitiu imagem em espelhamento. Nesse caminho ainda falta resolver a colisão de endereços, conseguir a tela independente e investigar o retorno da caneta e dos dedos. A opção Wi-Fi do UScreen também fica para uma rodada própria.

Os testes e as correções continuam no [registro único](testes.md). Os relatórios completos ficam salvos no meu computador.
