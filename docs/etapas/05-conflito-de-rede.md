# Etapa 05: procurando o erro na rede

21/10/2026

Preparei um observador para acompanhar a interface Wi-Fi Direct e registrar os pacotes de controle. Na primeira tentativa ele começou cedo demais: a interface já existia, mas ainda não estava ativa. Ajustei para esperar a ativação e refiz a captura.

O tablet repetia o pedido inicial TCP e não recebia a resposta esperada do computador. Eram retransmissões da mesma tentativa, não vários cliques meus.

Ao conferir os endereços, encontrei a duplicidade. O tablet e outra interface local usavam o mesmo IP. A consulta de rota tratava o destino como parte do próprio computador, em vez de apontar para o aparelho externo.

Uma consulta útil para investigar isso é:

```text
ip -4 route get <IP_DO_TABLET>
```

É preciso substituir `<IP_DO_TABLET>` pelo endereço daquela conexão. Se aparecer `local` e a interface `lo`, vale conferir se o mesmo IP está configurado no computador. Os endereços reais deste teste ficaram no registro local.

Encontrei o conflito, mas ainda precisava saber se era ele que impedia a conexão. Decidi retirar o endereço duplicado temporariamente, com a restauração preparada antes de começar. Fiz esse teste na etapa seguinte.
