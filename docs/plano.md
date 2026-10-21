# O que pretendo fazer agora

Vou começar pelo USB com o UScreen. É o projeto que reuniu mais coisas do que preciso no KDE, mas ainda não foi testado aqui.

Na próxima etapa, quero preparar a conexão:

1. Conferir a versão e o que a instalação vai alterar;
2. Verificar EVDI, compatibilidade com o kernel e Secure Boot, quando habilitado;
3. Preparar ADB e o acesso ao uinput;
4. Conferir se o programa Linux e o APK são de versões correspondentes;
5. Ver se o tablet é reconhecido e se a GPU consegue codificar com os parâmetros usados pelo programa.

Se alguma parte não funcionar, vou registrar onde parou e o erro que apareceu. Ao encerrar a etapa, entrego o relatório, explico as mudanças e como desfazê-las e paro antes de seguir.

## Depois da preparação

Quero colocar uma janela no tablet, manter outra nas telas físicas e conferir que são áreas independentes. Depois disso, testar a entrada:

| Parte | Teste |
| --- | --- |
| Touch | Tocar e arrastar nos cantos e no centro |
| Mais de um dedo | Conferir os contatos e usar gestos num aplicativo que os aceite |
| Pressão | Conferir valores diferentes e variar a espessura do traço no Krita |
| Inclinação | Conferir os eixos com um pincel preparado para responder à inclinação |
| Aproximação da S Pen | Mover o cursor sem encostar a ponta |
| Botão | Pressionar e soltar com a caneta próxima e durante o desenho |
| Mão apoiada | Desenhar e observar se aparecem cliques ou rolagens indevidos |
| Posição e escala | Conferir o alinhamento entre as telas e depois testar a rotação |
| Desconexão | Tirar o cabo e conferir se a tela e os contatos são liberados |

Se houver evento de borracha, preciso ver se a caneta e o Android realmente o enviam. Ter o campo no código não cria uma função física no aparelho.

## Sem fio e desempenho

O Miracast já transmitiu imagem. Ainda tenho que resolver a colisão de endereços, conseguir a tela independente e investigar a entrada. O UScreen também oferece Wi-Fi; vou tratar essa conexão como outro teste.

Quando imagem e entrada estiverem corretas, pretendo comparar atraso, nitidez, estabilidade e uso de CPU/GPU, mantendo computador, tablet, resolução e conteúdo. Os números de outros projetos não dizem qual será o resultado aqui.

A interface própria fica para depois dessas verificações. Primeiro preciso saber o que ela vai conseguir controlar.
