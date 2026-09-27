# MVP — Pipeline de Dados na Nuvem

## 1. Contexto e objetivo

Este projeto tem como objetivo construir um pipeline de dados em ambiente de nuvem utilizando o dataset público de e-commerce da Olist.

A partir dos dados de pedidos, produtos, clientes, vendedores, pagamentos e avaliações, o projeto busca organizar os dados e gerar informações para análise de vendas e comportamento dos clientes.

### Perguntas de negócio

O projeto foi orientado pelas seguintes perguntas:

1. Como o volume de pedidos e o valor das vendas evoluíram ao longo do período analisado?
2. Quais categorias apresentam maior volume de vendas e maior valor financeiro?
3. Qual é o comportamento dos clientes em relação à recorrência de compras?
4. Como estão distribuídas as avaliações dos clientes?

## 2. Carga dos dados

Os dados utilizados são provenientes do **Olist Brazilian E-Commerce Public Dataset**, disponibilizado no Kaggle.

Os arquivos CSV foram carregados para um Volume do Unity Catalog no Databricks, mantendo os dados de origem para utilização no pipeline.

As principais tabelas utilizadas são:

- Orders
- Order Items
- Products
- Customers
- Sellers
- Order Payments
- Order Reviews

## 3. Camada Bronze — Ingestão

A primeira etapa do pipeline consiste em disponibilizar os dados de origem no ambiente Databricks e realizar sua ingestão na camada Bronze.

Os arquivos CSV são lidos diretamente do Volume do Unity Catalog e transformados em tabelas Bronze.

A camada Bronze tem como objetivo preservar uma representação dos dados de origem, sem aplicação de regras de negócio ou transformações analíticas.

## 4. Camada Silver — Tratamento e validação

Após a ingestão, os dados são tratados e validados na camada Silver.

Foram realizadas verificações relacionadas a:

- tipos de dados;
- valores nulos;
- duplicidades;
- identificadores;
- valores negativos;
- granularidade das tabelas;
- relacionamentos entre as entidades.

Também foi considerada a cardinalidade entre as tabelas. Por exemplo, um pedido pode possuir vários itens e vários pagamentos.

Essas relações foram consideradas para evitar duplicações durante as análises.

## 5. Camada Gold — Modelagem analítica

A camada Gold concentra as informações utilizadas nas análises.

Foram construídas estruturas para análise de:

- pedidos e vendas;
- vendas por produto e categoria;
- comportamento de clientes.

Na construção das tabelas Gold, as tabelas com relacionamento 1:N foram previamente agregadas quando necessário, evitando a multiplicação de registros e a distorção dos indicadores.

## 6. Qualidade dos dados

A qualidade dos dados foi avaliada durante as etapas de ingestão e transformação.

Foram verificadas principalmente:

- completude;
- unicidade;
- consistência;
- validade;
- granularidade.

Foram identificados alguns valores nulos em atributos específicos, como categorias de produtos e notas de avaliações. Esses casos foram considerados durante a construção das camadas analíticas.

## 7. Análise dos dados

As análises foram realizadas a partir das estruturas construídas na camada Gold e direcionadas pelas perguntas de negócio definidas inicialmente.

A base analisada possui 99.441 pedidos, 112.650 itens vendidos, 32.951 produtos e 96.096 clientes únicos, permitindo explorar diferentes dimensões do negócio, como evolução das vendas, categorias de produtos, recorrência de clientes e avaliações.

### 7.1 Evolução das vendas

Foi analisada a evolução da quantidade de pedidos, do valor dos produtos vendidos e do ticket médio ao longo do período disponível na base.

No período analisado, foram identificados:

- **2016:** 329 pedidos e aproximadamente R$ 49,8 mil em valor de produtos;
- **2017:** 45.101 pedidos e aproximadamente R$ 6,16 milhões em valor de produtos;
- **2018:** 54.011 pedidos e aproximadamente R$ 7,39 milhões em valor de produtos.

O ticket médio foi de **R$ 151,32 em 2016**, **R$ 136,49 em 2017** e **R$ 136,75 em 2018**.

Entre os meses com maior volume de pedidos, novembro de 2017 apresentou **7.544 pedidos**, com aproximadamente **R$ 1,01 milhão** em valor de produtos. Em 2018, janeiro, março, abril e maio também apresentaram volumes superiores a 6,8 mil pedidos.

Os resultados indicam crescimento significativo do volume de pedidos e do valor movimentado ao longo do período disponível, enquanto o ticket médio permaneceu relativamente estável nos períodos de maior movimentação.

A comparação anual deve considerar que 2016 e 2018 não representam necessariamente anos completos na base.

### 7.2 Categorias de produtos

Foram comparadas as categorias considerando quantidade de itens vendidos, valor total dos produtos vendidos e valor médio por item.

Em quantidade de itens, `cama_mesa_banho` apresentou o maior volume, com **11.115 itens vendidos**, seguida por `beleza_saude`, com **9.670 itens**, e `esporte_lazer`, com **8.641 itens**.

Em valor total de vendas, `beleza_saude` apresentou o maior resultado, com aproximadamente **R$ 1,41 milhão**, seguida por `relogios_presentes`, com **R$ 1,31 milhão**, e `cama_mesa_banho`, com **R$ 1,24 milhão**.

A análise do valor médio por item também apresentou diferenças relevantes entre as categorias. Entre as categorias com pelo menos 1.000 itens vendidos, `relogios_presentes` apresentou o maior valor médio, de aproximadamente **R$ 217,92 por item**, enquanto `beleza_saude`, apesar de apresentar o maior valor total de vendas, apresentou valor médio de **R$ 149,04 por item**.

Os resultados mostram que volume de itens e valor financeiro são dimensões diferentes da análise. O valor total movimentado por uma categoria resulta da combinação entre quantidade vendida e valor médio dos produtos.

### 7.3 Clientes e recorrência de compras

A análise dos clientes utilizou o `customer_unique_id`, permitindo diferenciar clientes recorrentes de diferentes registros de pedidos associados ao mesmo consumidor.

Foram identificados **96.096 clientes únicos**. Desse total, **93.099 clientes realizaram apenas uma compra**, enquanto **2.997 clientes realizaram duas ou mais compras**, representando aproximadamente **3,12% da base de clientes**.

Apesar de representarem uma parcela reduzida da quantidade de clientes, os clientes recorrentes movimentaram aproximadamente **R$ 922 mil**, contra **R$ 1,49 milhão** movimentados pelos clientes que realizaram apenas uma compra.

O valor médio de compras foi de aproximadamente **R$ 307,66 por cliente recorrente**, enquanto clientes de compra única apresentaram média de **R$ 160,28**.

Assim, os clientes recorrentes representam uma parcela pequena da base em quantidade, mas apresentam maior valor médio de compras e representam aproximadamente **38,2% do valor total de compras analisado**.

Essa análise é descritiva e não permite, isoladamente, estabelecer uma relação causal entre recorrência e maior valor de compras.

### 7.4 Avaliações dos clientes

Foi analisada a distribuição das avaliações atribuídas aos pedidos.

A base apresentou **99.249 registros de avaliações**, sendo **99.223 avaliações com nota disponível**.

A distribuição apresentou forte concentração nas notas mais altas:

- **Nota 5:** 57,78%;
- **Nota 4:** 19,29%;
- **Nota 3:** 8,24%;
- **Nota 2:** 3,18%;
- **Nota 1:** 11,51%.

Somadas, as notas **4 e 5 representam 77,07% das avaliações com nota disponível**.

A análise permite observar a distribuição geral da satisfação registrada na base, sem buscar estabelecer relações causais entre as avaliações e outras características dos pedidos ou clientes.

### 7.5 Síntese dos resultados

As análises realizadas permitiram responder às quatro perguntas de negócio definidas no início do projeto.

A análise temporal mostrou crescimento do volume de pedidos e do valor movimentado ao longo do período disponível, com manutenção de um ticket médio próximo de R$ 137 nos períodos de maior movimentação.

A análise por categoria mostrou que volume e valor financeiro não são necessariamente proporcionais. `cama_mesa_banho` apresentou o maior volume de itens vendidos, enquanto `beleza_saude` apresentou o maior valor total de vendas. A análise do valor médio por item também evidenciou diferenças relevantes entre as categorias.

Na dimensão de clientes, a utilização do `customer_unique_id` permitiu identificar que apenas 3,12% dos clientes realizaram mais de uma compra. Apesar da menor participação em quantidade, esse grupo apresentou valor médio de compras de R$ 307,66, aproximadamente 1,9 vez o valor médio observado entre os clientes de compra única.

Por fim, a análise das avaliações mostrou concentração nas notas mais altas, com 77,07% das avaliações com nota disponível apresentando pontuação 4 ou 5.

Em conjunto, os resultados demonstram que o pipeline desenvolvido foi capaz de transformar os dados brutos em informações estruturadas para responder às perguntas de negócio propostas, explorando diferentes dimensões da operação de e-commerce: evolução temporal, produtos, clientes e percepção dos consumidores.

## 8. Autoavaliação

O objetivo inicial do projeto era construir um pipeline de dados em ambiente de nuvem, utilizando o Databricks, capaz de transformar os dados públicos de e-commerce da Olist em estruturas organizadas para análise e responder a perguntas de negócio relacionadas a vendas, produtos, clientes e avaliações.

Considero que o objetivo foi atingido. O projeto percorreu as principais etapas propostas, desde a disponibilização dos arquivos no ambiente de nuvem e sua ingestão na camada Bronze, passando pelo tratamento e validação na camada Silver, até a construção de estruturas analíticas na camada Gold. A partir dessas estruturas, foi possível realizar análises que responderam às quatro perguntas de negócio definidas inicialmente.

### Principais dificuldades

Uma das principais dificuldades encontradas foi compreender a granularidade e a cardinalidade das tabelas antes de realizar os relacionamentos. As relações de um para muitos entre pedidos, itens e pagamentos poderiam gerar duplicação de registros e distorção dos indicadores caso fossem realizadas junções diretamente entre as tabelas. Esse problema levou à necessidade de realizar agregações prévias na construção das tabelas Gold.

Outra dificuldade foi compreender a diferença entre `customer_id` e `customer_unique_id`. Enquanto `customer_id` está associado ao registro do cliente em um pedido, `customer_unique_id` permite identificar o mesmo consumidor ao longo de diferentes pedidos. A compreensão dessa diferença foi fundamental para realizar corretamente a análise de recorrência.

Também foram encontrados problemas de qualidade e interpretação durante a ingestão dos dados, incluindo valores nulos, diferenças de completude entre as tabelas e a necessidade de tratamento específico da tabela de avaliações. Esses problemas reforçaram a importância de validar os dados antes de utilizá-los nas análises.

Por fim, houve uma curva de aprendizado relacionada à utilização do Databricks, à organização das camadas Bronze, Silver e Gold e à utilização de SQL para realizar as transformações e análises no ambiente de nuvem.

### Trabalhos futuros

Como evolução do projeto, seria possível ampliar o pipeline com novas fontes de dados e automatizar a execução das etapas de ingestão, transformação e validação.

Também seria possível aprofundar as análises realizadas, incluindo indicadores de logística e prazo de entrega, análise geográfica de clientes e vendedores, comportamento de pagamento e relacionamento entre avaliações e características dos pedidos.

Outra evolução seria a criação de dashboards a partir das tabelas Gold, permitindo acompanhar os principais indicadores de forma visual e facilitar a utilização dos resultados por usuários de negócio.

Para fins de portfólio, uma evolução futura seria transformar o MVP em um pipeline mais próximo de um ambiente produtivo, incorporando maior automação, monitoramento da qualidade dos dados, versionamento estruturado e documentação da execução do pipeline.
