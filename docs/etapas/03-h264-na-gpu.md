# Etapa 03: H.264 na GPU

Data: 21/10/2026.

Corrigi a investigação da GPU. Fora do ambiente restrito e com um catálogo novo do GStreamer, o driver instalado oferecia `vah264lpenc`, com o modo de controle CQP. A base procurava outros nomes de codificador.

Extraí um pacote do driver Intel completo em uma pasta local, sem substituir o driver instalado. Com esse driver selecionado apenas para os processos de teste, `vah264enc` ficou disponível com CBR.

## Como conferi

Gerei um vídeo sintético de 300 quadros, em 1920 × 1080 a 60 quadros/s, usando `vah264enc`. Depois, conferi o arquivo e fiz a decodificação.

O arquivo tinha os 300 quadros, duração de cinco segundos, formato H.264 e decodificação sem erros. A execução levou cerca de cinco segundos.

Isso mostrou que o caminho de codificação usado no teste funcionava. Não foi uma medida de atraso entre computador e tablet, nem uma transmissão de uma tela real.

## O que aprendi

Encontrar o nome de um codificador não basta. É preciso conseguir codificar quadros com o driver e os parâmetros que serão usados. Também é preciso separar uma limitação do ambiente de teste de uma limitação da máquina.

Próximo passo definido: conectar ao S10 FE.
