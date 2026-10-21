# A rede pelo cabo funcionou

Consegui conectar o S10 FE ao Kubuntu por ancoragem USB, com a depuração desligada. Abri no navegador do tablet uma página que estava sendo servida pelo computador. O servidor registrou o pedido vindo do tablet, e confirmei que a página apareceu.

No começo eu tinha selecionado apenas transferência de arquivos. O Linux reconhecia essa função, mas a rede não aparecia. Depois ativei **Ancoragem USB** nas configurações de conexões do tablet. A partir daí, o Kubuntu reconheceu a interface e recebeu um endereço automaticamente.

## Como conferi

Primeiro consultei a rota até o tablet e enviei cinco pacotes de ping pela interface USB. Os cinco responderam, sem perdas. Depois abri a página no S10 FE, fazendo a comunicação no sentido contrário.

O servidor escutava somente no endereço da rede USB e aceitava a página apenas do tablet. A primeira janela de teste expirou antes de receber um pedido; reabri o endereço e a página abriu. Depois encerrei o servidor.

Não instalei driver nem aplicativo, não alterei o firewall e não usei ADB. O driver de rede USB do Linux já deu conta dessa conexão. A sub-rede recebida também não se sobrepôs às redes existentes no início do teste.

Mantive a depuração desligada durante o teste. Do lado do computador, os descritores USB ativos não mostraram uma interface ADB. Essa consulta apoia a confirmação feita na conversa, mas não lê diretamente o ajuste interno do Android.

## Até onde fui

A rede pelo cabo está funcionando. Ainda falta levar vídeo, touch e S Pen por ela. O ping respondeu rápido, mas esse tempo não mede atraso de vídeo nem velocidade útil da conexão.

A imagem que já consegui transmitir continua sendo a da [etapa 06](06-primeira-imagem.md), por Miracast e em espelhamento. Não fiz uma nova transmissão nesta etapa.

O NetworkManager criou a conexão automaticamente. A conexão Ethernet conservou a prioridade e não precisei mudar as outras redes. A ancoragem ficou ativa porque pedi para continuar o trabalho. Para retirar essa rede, basta desativar a ancoragem no tablet ou tirar o cabo.

Agora preciso preparar o programa Linux e o aplicativo Android para conectar sem ADB: endereço do computador, abertura manual e autorização da sessão. Também vou observar quais dados da caneta e do touch chegam pelo cabo antes de ligar a entrada ao Linux.

A [orientação da Samsung sobre conexões e compartilhamento](https://www.samsung.com/us/support/answer/ANS10002546/) ajudou a localizar a configuração. O funcionamento acima foi conferido no meu tablet. O relatório completo desta etapa fica salvo no computador.
