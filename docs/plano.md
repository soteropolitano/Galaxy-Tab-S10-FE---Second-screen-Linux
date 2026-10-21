# O que pretendo fazer agora

Prefiro usar o cabo sem ativar a depuração USB. A rede por ancoragem USB já funcionou no S10 FE e no Kubuntu: consegui abrir no tablet uma página servida pelo computador. O registro está na [etapa 08](testes.md#etapa-08).

O UScreen continua como referência para tela, S Pen e touch no KDE, mas seu modo USB atual usa ADB. Preciso avaliar como aproveitar essas partes com outro transporte. O TethrLink ajuda a estudar a rede USB, mas depende do GNOME e não pode ser adotado diretamente para resolver a entrada no KDE.

Na [etapa 09](testes.md#etapa-09), recebi pelo navegador dados de pressão e inclinação da S Pen e até cinco dedos simultâneos. Ainda falta usar essa entrada no Linux. Também mapeei as dependências de ADB no UScreen.

Agora quero preparar o programa e o aplicativo:

1. Preparar e verificar as ferramentas para compilar o programa Linux e o aplicativo Android;
2. Adaptar o endereço da conexão, a instalação e a abertura manual do cliente Android e a autorização da sessão, sem ADB;
3. Substituir a detecção de presença feita pelo monitor ADB pelo acompanhamento da sessão autenticada, incluindo desconexão e liberação dos contatos;
4. Restringir a escuta à rede escolhida e manter a autenticação para vídeo e entrada. Trocar o transporte não deve desligar a autorização da sessão;
5. Compilar os dois lados e conferir a conexão antes de iniciar o teste de tela. Depois, conferir EVDI ou captura pelo portal, acesso ao uinput e codificação pela GPU.

A rede por cabo e a chegada de dados pelo navegador passaram nos primeiros testes. Vídeo e entrada no Linux continuam pendentes. O APK atual do UScreen não tem esse caminho pronto; observar eventos num navegador também não substitui testar o cliente Android e os aplicativos Linux.

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
