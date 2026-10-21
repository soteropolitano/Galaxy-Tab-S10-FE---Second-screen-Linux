# Etapa 07: S Pen, touch, USB e documentação

Data: 21/10/2026.

Depois da primeira imagem, ampliei o objetivo: quero a S Pen e o touch funcionando, além da opção USB. Estudei os links reunidos em [referências](../referencias.md), incluindo documentação, trechos do código e relatos de uso.

Esta etapa foi de estudo e preparação da documentação. Não instalei os projetos nem fiz outro teste com o tablet.

## A principal referência

O UScreen reúne o caminho mais próximo do objetivo no KDE Wayland:

```text
KDE -> tela virtual EVDI -> captura -> H.264 -> ADB/USB -> app Android

Caneta e dedos -> eventos Android -> canal de entrada
              -> uinput -> KDE -> aplicativo
```

No cliente Android, encontrei a leitura de pressão, inclinação, orientação, aproximação e botão. No computador, encontrei dispositivos separados para caneta e touch, com eixos de pressão/inclinação e contatos de toque.

O código também faz a associação desses dispositivos à saída correta pelo KWin. Isso importa porque o computador usa monitores com escalas diferentes.

Há um relato positivo com S10 FE+ e KDE, incluindo a confirmação do botão após a versão 1.2.8. Ainda falta validar o meu S10 FE e a GPU deste computador.

## O que os outros projetos acrescentaram

O TethrLink ajuda a estudar transporte por rede USB, filas de vídeo e teste real do codificador. O retorno de entrada dele emula ponteiro e depende das APIs do GNOME; não atende diretamente ao KDE nem comprova pressão da S Pen.

O projeto arch-galaxytab-submonitor usa Sunshine/Moonlight por rede USB. O próprio autor registra a entrada de caneta e touch como não verificada. Uma configuração que force HDMI deve ser conferida antes de aplicar, para preservar uma saída que já esteja em uso.

O projeto Second-Screen-Samsung-Linux ajuda a estudar o pedido de captura virtual ao portal, mas transmite MJPEG para uma página simples e não apresenta um caminho de retorno de entrada no cliente lido.

Os projetos com XRandR e VNC ajudam a entender a criação e organização de telas, mas não são a base prioritária para esta sessão Wayland e para os requisitos de caneta.

## Preparação ainda necessária

O caminho USB exige conferir ADB, criação da saída virtual, codificação e acesso ao uinput. Essa conferência precisa ser feita no computador do teste, sem presumir que a instalação descrita por outro projeto terá o mesmo resultado.

O inventário local e as consultas detalhadas ficaram no relatório entregue fora deste repositório público.

## Próximo teste proposto

Preparar uma tentativa USB com UScreen, começando pela conferência dos requisitos. Depois, validar separadamente a tela independente, o touch e a pressão da S Pen. Inclinação, aproximação, botão, rejeição da mão e gestos precisam de verificações próprias.

O teste Miracast continua registrado. A entrada completa pelo receptor nativo da Samsung ainda exige investigação; não está resolvida pela transmissão de vídeo.

Também comecei a documentação no GitHub, com README, histórico e plano de testes. A ideia é registrar cada avanço sem chamar de pronto o que ainda não foi testado.

