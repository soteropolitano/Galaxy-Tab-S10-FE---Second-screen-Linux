# O que pretendo fazer agora

Prefiro usar o cabo sem ativar a depuração USB. A rede por ancoragem USB já funcionou no S10 FE e no Kubuntu: consegui abrir no tablet uma página servida pelo computador. O registro está na [etapa 08](testes.md#etapa-08).

Já compilei uma adaptação do UScreen para conectar diretamente pela rede USB, sem ADB. O aplicativo de teste foi instalado manualmente e confirmou a conexão. Recebi até cinco dedos simultâneos no modo de diagnóstico. Também criei e capturei uma tela virtual no KDE, com H.264 recebido e decodificado num cliente simulado no próprio computador. O andamento está na [etapa 10](testes.md#etapa-10).

A S Pen ainda precisa ser conferida no aplicativo nativo. Pressão e inclinação chegaram pelo navegador na etapa 09, mas não apareceram como eventos de caneta na rodada do aplicativo. Preparei outra versão para observar o tipo e o encaminhamento dos eventos Android; ela ainda não foi instalada.

Os próximos passos são:

1. Instalar a versão de diagnóstico quando eu puder testar no tablet e observar separadamente caneta, dedos, aproximação e botão;
2. Entender por que não chegaram eventos nativos de caneta e conferir como o Android distingue a S Pen dos dedos;
3. Mostrar no tablet a tela virtual pelo cabo, começando em 1280 × 800 e com alvo de 30 quadros por segundo. Conferir movimento de verdade antes de avaliar resolução maior;
4. Ligar a entrada à tela correta no KDE e testar nos aplicativos Linux;
5. Conferir encerramento, liberação dos contatos e retirada da tela. Depois tratar mudanças de endereço e reconexão ao recolocar o cabo.

O vídeo local passou pela criação da tela, captura e codificação. Ainda falta testar o caminho completo até a imagem no S10 FE. Também preciso conferir o movimento com a sessão desbloqueada: a tentativa com padrão animado capturou a tela de bloqueio.

Por enquanto continuo com uma versão de teste local. Os relatórios completos e os registros brutos ficam no computador, e o andamento fica reunido no registro dos testes.

## Com imagem no tablet

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
