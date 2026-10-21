# S Pen e dedos pelo cabo

Depois de confirmar a rede USB, fiz um teste separado de entrada. Abri uma página no navegador do S10 FE, desenhei na área de teste e mandei os eventos para o computador pelo cabo, mantendo a depuração desligada.

A primeira rodada registrou 2.975 eventos de caneta. A pressão variou, e a inclinação apareceu nos dois eixos. Na segunda rodada, com a S Pen afastada, chegaram os eventos dos dedos: foram registrados até cinco contatos simultâneos.

## O que chegou

| Parte | Resultado do teste |
| --- | --- |
| Pressão da S Pen | 1.498 valores distintos durante contato, entre aproximadamente 0,000244 e 0,589988 na escala do navegador |
| Inclinação X | De -1 a 41 graus |
| Inclinação Y | De -20 a 40 graus |
| Touch | 3.364 eventos PointerEvent e até cinco dedos ao mesmo tempo |
| Finalização | As duas rodadas registraram conclusão; os contatos de touch terminaram no registro |
| Aproximação e botão lateral | Não apareceram nos eventos recebidos; continuam pendentes |

Os valores de pressão mostram variação nesta amostra. Não representam uma calibração de força nem a quantidade de níveis de pressão do hardware.

Na primeira rodada não chegou nenhum evento de dedo. Repeti o teste só com os dedos, afastando a caneta, e passei a registrar também TouchEvent, separado de PointerEvent. As duas interfaces mostraram até cinco contatos simultâneos.

Não somei os dois contadores como gestos independentes, porque podem descrever os mesmos contatos. Também não concluí que a diferença entre as rodadas comprova rejeição de palma: esse comportamento ainda precisa de um teste próprio.

## Ainda falta ligar isso ao Linux

A página serviu para conferir quais dados atravessavam o cabo. Ela não controlava o cursor, não criava dispositivos de entrada e não transmitia a área de trabalho. Pressão no Krita, multitouch nos aplicativos e alinhamento com a tela do tablet continuam pendentes.

O receptor passou em cinco testes locais, incluindo validação dos dados e reenvio sem duplicação. Depois da conclusão no tablet, encerrei o servidor. A ancoragem ficou ativa para a continuação. Não instalei APK nem alterei permissões de entrada nesta etapa.

## O que encontrei no UScreen

Li os arquivos de conexão no commit `66f6bcdab918b8dcc44227dde00197f30a778c44`. O modo Wi-Fi também usa ADB. O programa usa ADB para detectar o tablet, abrir o aplicativo e entregar o token da sessão; os canais de vídeo e entrada escutam em loopback, e o aplicativo Android se conecta a endereços de loopback fixos.

Para usar a rede USB, preciso adaptar os dois lados. O cliente deve receber o endereço do computador e abrir manualmente. O programa deve acompanhar uma sessão autenticada e liberar a tela e os contatos quando ela terminar. Trocar só o endereço não resolve tudo isso.

O código nativo de caneta, o decodificador e a entrada do UScreen continuam como referência. Não alterei nem compilei esses componentes nesta etapa. O próximo trabalho é preparar a compilação e uma primeira adaptação de conexão sem ADB, preservando a autorização de vídeo e entrada.

Usei a documentação de [PointerEvent](https://developer.mozilla.org/en-US/docs/Web/API/PointerEvent) e [touch-action](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/touch-action) para preparar o diagnóstico. A arquitetura do aplicativo de referência está no [UScreen](https://github.com/majmichu1/UScreen/blob/66f6bcdab918b8dcc44227dde00197f30a778c44/docs/architecture.md). O relatório completo e os registros brutos ficam no computador.
