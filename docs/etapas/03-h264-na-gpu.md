# Etapa 03: conferindo a GPU

21/10/2026

Refiz a consulta fora do ambiente restrito, com um catálogo novo do GStreamer. O driver instalado tinha `vah264lpenc`, com controle CQP, enquanto o aplicativo procurava outros nomes de codificador.

Extraí um pacote do driver Intel completo em uma pasta local, sem substituir o instalado. Selecionei esse driver apenas para os processos de teste. Com ele, `vah264enc` ficou disponível com controle CBR.

Para conferir, gerei 300 quadros de um vídeo sintético em 1920 × 1080 a 60 quadros/s. Depois abri o arquivo para contar os quadros e fiz a decodificação.

O arquivo tinha os 300 quadros, cinco segundos de duração e decodificação sem erros. A execução levou cerca de cinco segundos. Consegui codificar H.264 pela GPU nesse teste.

Ainda não era a tela do computador sendo enviada ao tablet, então isso não mede o atraso da conexão. Serviu para confirmar que o codificador funcionava com aquele driver e aqueles parâmetros.

Encontrar o nome do componente não tinha sido suficiente. Só consegui confirmar quando ele realmente produziu vídeo.
