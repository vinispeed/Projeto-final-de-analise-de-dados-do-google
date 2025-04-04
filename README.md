# Análise de Dados de Dispositivos Inteligentes para Bellabeat

[![Status](https://img.shields.io/badge/Status-Concluído-brightgreen)](https://github.com/SEU_USUARIO/SEU_REPOSITORIO)
[![Linguagem](https://img.shields.io/badge/Linguagem-R-blue)](https://www.r-project.org/)
[![Licença](https://img.shields.io/badge/Licen%C3%A7a-MIT-green)](https://opensource.org/licenses/MIT)

## Visão Geral do Projeto

Este projeto consiste em uma análise de dados de uso de dispositivos inteligentes que não são da Bellabeat. O objetivo principal é obter insights sobre o comportamento e as preferências dos consumidores nesse mercado, a fim de informar e otimizar a estratégia de marketing e aumentar a competitividade de um produto específico da Bellabeat.

A análise foi realizada utilizando a linguagem de programação R e diversas bibliotecas para manipulação, visualização e análise de dados. Os resultados visam fornecer informações acionáveis para a Bellabeat em suas decisões de negócios.

## Problema a Ser Resolvido

O projeto busca responder à seguinte questão: Como os consumidores utilizam dispositivos inteligentes que não são da Bellabeat, e como esses insights podem ser aplicados para melhorar a estratégia de marketing e a competitividade da Bellabeat no mercado de dispositivos inteligentes voltados para a saúde feminina?

## Insights e Decisões de Negócios

Os insights derivados desta análise podem auxiliar a Bellabeat a:

* **Identificar funcionalidades valorizadas:** Compreender quais recursos são mais utilizados e desejados pelos usuários de dispositivos concorrentes.
* **Otimizar o desenvolvimento de produtos:** Informar a criação de novos produtos ou a melhoria de produtos existentes, focando em funcionalidades relevantes.
* **Direcionar estratégias de marketing:** Personalizar mensagens e campanhas para diferentes segmentos de clientes, com base em seus padrões de uso e preferências.
* **Aumentar a competitividade:** Posicionar os produtos da Bellabeat de forma mais eficaz no mercado, destacando seus diferenciais e atendendo às necessidades dos consumidores.

## Tarefa de Negócios

A tarefa principal é analisar dados de uso de dispositivos inteligentes de outras marcas para identificar tendências e comportamentos dos consumidores. Com base nesses insights, a Bellabeat poderá ajustar e aprimorar suas estratégias de marketing para um de seus produtos, visando aumentar sua participação no mercado de dispositivos inteligentes voltados para a saúde das mulheres.

## Principais Partes Interessadas

* **Clientes:** Usuários atuais e potenciais dos produtos Bellabeat.
* **Urška Sršen:** Cofundadora e CEO da Bellabeat.
* **Sando Mur:** Matemático e cofundador da Bellabeat.
* **Equipe de Análise de Marketing da Bellabeat.**
* **Equipe de Desenvolvimento de Produtos da Bellabeat.**
* **Acionistas e Investidores da Bellabeat.**
* **Parceiros Comerciais da Bellabeat.**

## Sobre os Dados

Os dados utilizados nesta análise são provenientes de múltiplos arquivos CSV contendo informações sobre atividades diárias (passos, calorias, intensidade), sono, peso e frequência cardíaca de usuários de dispositivos FitBit. Os dados foram coletados em diferentes períodos e organizados por granularidade temporal (segundos, minutos, horas, dias). Os arquivos originais estavam armazenados em pastas nomeadas "Fitabase Data 4.12.16-5.12.16" e "Fitabase Data 3.12.16-4.11.16".

Os seguintes dataframes foram criados para a análise:

* `atividade_diaria`: Contém dados sobre a atividade diária dos usuários.
* `dia_sono_mesclado`: Contém dados sobre o sono diário dos usuários.

```r
# Carregando os dados de atividades diárias
atividade_diaria <- read.csv("Projeto Final/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
head(atividade_diaria)

# Carregando os dados de sono diário
dia_sono_mesclado <- read.csv("Projeto Final/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
head(dia_sono_mesclado)

Preparação e Exploração Inicial dos Dados
As seguintes bibliotecas R foram utilizadas:

R

library(tidyverse)
library(ggplot2)
library(dplyr)
library(cluster)
A exploração inicial envolveu:

Visualização das colunas dos dataframes (colnames()).
Verificação do número de participantes únicos (n_distinct()).
Contagem do número de observações (nrow()).
Identificação de valores ausentes (sum(is.na())).
Identificação de valores duplicados (sum(duplicated())).
Análise da estrutura dos dados (str() e glimpse()).
Obtenção de estatísticas descritivas (summary()).
Insights da Exploração Inicial
Foram identificados 33 participantes únicos nos dados de atividade diária e 24 nos dados de sono diário.
Não foram encontrados valores ausentes significativos, mas foram identificadas 3 linhas duplicadas nos dados de sono.
As estatísticas resumidas forneceram uma visão geral dos níveis de atividade e padrões de sono dos participantes.
Considerações Éticas
Licenciamento: Os dados e softwares utilizados são de código aberto, respeitando suas licenças.
Privacidade: A análise foi realizada com dados anonimizados, garantindo a privacidade dos usuários e em conformidade com a LGPD.
Segurança: Os dados foram processados em um ambiente seguro (R Studio).
Acessibilidade: Foram consideradas práticas de acessibilidade na análise e documentação.
Integridade dos Dados: Foram realizadas verificações para garantir a qualidade e a consistência dos dados.
Fontes de Dados
dailyActivity_merged.csv
sleepDay_merged.csv
Exploração e Visualização
Foram realizadas explorações visuais para entender a relação entre diferentes variáveis:

Relação entre Passos Dados e Minutos Sedentários
R

ggplot(data=atividade_diaria, aes(x=TotalSteps, y=SedentaryMinutes, color=TotalSteps)) +
    geom_point()
Este gráfico sugere uma correlação negativa entre o número de passos e os minutos sedentários.

Relação entre Minutos Dormindo e Tempo na Cama
R

ggplot(data=dia_sono_mesclado, aes(x=TotalMinutesAsleep, y=TotalTimeInBed)) + geom_point()
Este gráfico demonstra uma forte correlação positiva, indicando que mais tempo na cama geralmente leva a mais tempo dormindo.

Mesclagem dos Dados
Os dataframes foram mesclados para uma análise mais completa:

R

# Mesclagem externa completa dos dataframes
dados_combinados <- merge(dia_sono_mesclado, atividade_diaria, by = "Id", all = TRUE)

# Tratamento de valores ausentes (substituição por zero)
dados_combinados <- dados_combinados %>%
    mutate_all(~ ifelse(is.na(.), 0, .))
Análise de Cluster
A análise de cluster (K-means) foi utilizada para segmentar os usuários com base em seus padrões de sono e atividade:

R

# Selecionando variáveis para clusterização
cluster_dados <- dados_combinados %>%
    select(Id, TotalMinutesAsleep, TotalSteps)

# Escalonando os dados
cluster_dados_scalados <- scale(cluster_dados[, c("TotalMinutesAsleep", "TotalSteps")])

# Aplicando K-means
set.seed(123)
kmeans_fit <- kmeans(cluster_dados_scalados, centers = 3, nstart = 25)

# Adicionando os clusters ao dataframe
cluster_dados$cluster <- kmeans_fit$cluster

# Visualização dos clusters
ggplot(cluster_dados, aes(x = TotalSteps, y = TotalMinutesAsleep, color = factor(cluster))) +
    geom_point() +
    labs(title = "Segmentação de Clientes: Sono vs Atividade Física",
         x = "Total de Passos",
         y = "Total de Minutos Dormidos",
         color = "Cluster") +
    theme_minimal()
A análise de cluster identificou três segmentos de clientes com diferentes perfis de atividade e sono.

Resposta às Perguntas de Negócios
Tendências no uso de dispositivos inteligentes: Monitoramento de atividade física, sono, saúde mental e hidratação, com valorização da personalização e integração com aplicativos.
Aplicação aos clientes da Bellabeat: Desenvolvimento e marketing de produtos que incentivem atividade, melhorem o sono e promovam a saúde holística, com personalização e integração.
Influência na estratégia de marketing da Bellabeat: Foco em personalização, integração e abordagem holística da saúde nas campanhas de marketing, destacando os benefícios dos produtos Bellabeat para um estilo de vida saudável.
Conclusão
A análise dos dados revelou insights importantes sobre o comportamento dos usuários de dispositivos inteligentes, que podem ser utilizados pela Bellabeat para otimizar suas estratégias de marketing e desenvolvimento de produtos. A segmentação de clientes com base em seus padrões de atividade e sono oferece oportunidades para abordagens mais direcionadas e personalizadas.

Recomenda-se que a Bellabeat continue explorando esses insights para fortalecer sua posição no mercado e atender às necessidades de seus clientes de forma eficaz.

Próximos Passos
Análise mais aprofundada das características de cada cluster de clientes.
Investigação de outras variáveis relevantes para a segmentação.
Desenvolvimento de personas para cada segmento de cliente identificado.
Formulação de recomendações específicas de marketing e produto para a Bellabeat.
Contribuição
Contribuições para este projeto não estão abertas ao público neste momento, pois trata-se de um projeto acadêmico individual.

Licença
Este projeto está sob a licença MIT. Consulte o arquivo LICENSE para obter mais detalhes.
