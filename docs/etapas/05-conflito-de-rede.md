# Etapa 05: conflito de rede

Data: 21/10/2026.

Preparei um observador para aguardar a interface Wi-Fi Direct e registrar o tráfego de controle, os endereços e as rotas. A primeira captura começou cedo demais, então ajustei o observador para esperar a ativação da interface.

## O que apareceu

O tablet repetia o mesmo pedido inicial TCP e não recebia a resposta esperada do computador. Eram retransmissões de uma tentativa, não vários cliques.

Encontrei o mesmo endereço IP atribuído ao tablet e a outra interface local. A consulta de rota tratava esse destino como pertencente ao próprio computador, em vez de apontar para o aparelho externo.

## Como investigar uma situação parecida

Primeiro é preciso identificar os endereços da conexão real. Depois, consultar a rota para o tablet:

```text
ip -4 route get <IP_DO_TABLET>
```

O texto entre os sinais de menor e maior é um marcador, não um endereço para copiar. Se o resultado disser `local` e apontar para `lo`, vale conferir se esse endereço também está configurado no computador.

A duplicidade foi confirmada no teste. Ainda precisava retirar temporariamente o conflito para verificar se o resultado da conexão mudava.

Próximo passo definido: um experimento com restauração ao terminar. Os endereços reais e o inventário da rede ficaram no registro local.
