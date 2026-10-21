# O que pretendo fazer agora

Prefiro usar o cabo sem ativar a depuração USB. Vou começar conferindo o compartilhamento de rede por USB no S10 FE e no Kubuntu. Esse transporte ainda não foi testado aqui.

O UScreen continua como referência para tela, S Pen e touch no KDE, mas seu modo USB atual usa ADB. Preciso avaliar como aproveitar essas partes com outro transporte. O TethrLink ajuda a estudar a rede USB, mas depende do GNOME e não pode ser adotado diretamente para resolver a entrada no KDE.

Na próxima etapa, quero preparar a conexão:

1. Manter a depuração USB desligada e conferir se o tablet oferece compartilhamento de rede pelo cabo;
2. Verificar se o Kubuntu reconhece a interface, quais endereços foram recebidos e se há algum conflito com outras redes;
3. Conferir a comunicação entre computador e tablet por essa interface, sem depender de ADB;
4. Avaliar o que precisa mudar no programa e no aplicativo Android para conectar, abrir uma sessão e autorizar vídeo e entrada por essa rede. A instalação e a abertura do aplicativo no tablet devem ter um caminho manual, sem ADB;
5. Definir o transporte antes de instalar os componentes da tela e da entrada. Depois, conferir EVDI ou captura pelo portal, acesso ao uinput, versões Linux/Android e codificação pela GPU.

O primeiro teste é dessa rede por cabo. Ainda não é um teste completo de tela, caneta e touch. Não vou presumir que o APK atual do UScreen já funciona assim.

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
