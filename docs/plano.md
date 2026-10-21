# Plano de testes

Quero fechar uma parte por vez e registrar o resultado antes de seguir. Ter uma biblioteca ou um recurso declarado no README não conta como teste no meu computador.

## Etapa 08 proposta: preparar o caminho USB

1. Conferir a versão do UScreen e o que a instalação vai mudar no sistema.
2. Verificar EVDI, compatibilidade com o kernel e, quando habilitado, Secure Boot. Não substituir um módulo funcional sem necessidade.
3. Preparar ADB e o acesso a `uinput`, limitando as mudanças ao necessário para o teste.
4. Usar versões correspondentes do aplicativo Linux e do APK Android.
5. Conferir o reconhecimento do tablet e o caminho de codificação da GPU. A disponibilidade precisa ser confirmada no processo que vai transmitir.
6. Registrar a preparação, os resultados e como desfazer as mudanças. Parar ao entregar o relatório.

Se a preparação revelar um bloqueio, vou documentá-lo antes de insistir na conexão.

## Depois: tela independente e entrada

| Verificação | Como vou conferir |
| --- | --- |
| Extensão da área de trabalho | Mover uma janela para o tablet mantendo outra nas telas físicas |
| Touch | Tocar e arrastar nos quatro cantos e no centro da tela do tablet |
| Mais de um dedo | Conferir contatos separados e gestos num aplicativo que aceite multitouch |
| Pressão da S Pen | Observar pressão variável e traços com espessura variável no Krita |
| Inclinação | Conferir os eixos e um pincel configurado para responder à inclinação |
| Aproximação | Mover o cursor com a caneta próxima, sem pressionar a tela |
| Botão | Conferir eventos de pressionar/soltar durante aproximação e desenho |
| Rejeição da mão | Apoiar a mão enquanto desenho e verificar cliques/rolagens indesejados |
| Escala e posição | Conferir alinhamento com os monitores físicos e testar após rotação |
| Desconexão | Tirar o cabo e conferir remoção da tela e liberação dos contatos/botões |

O suporte a um evento de borracha no código só será relevante se a caneta e o Android realmente o emitirem. Não vou presumir uma função física que não esteja disponível no aparelho.

## Conexão sem fio e desempenho

O Miracast já transmitiu imagem, mas ainda precisa de uma solução para a colisão de endereços, tela independente e entrada. O UScreen também documenta uma opção Wi-Fi; ela deve ser testada separadamente do USB e do Miracast.

Quando imagem e entrada estiverem corretas, vou comparar atraso, estabilidade, nitidez e uso de CPU/GPU no mesmo computador, tablet, resolução e conteúdo. Os números publicados por outros projetos servem de referência de método, não como previsão para este conjunto.

Uma interface própria só faz sentido depois de validar os caminhos de vídeo e entrada que ela vai controlar.
