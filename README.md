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

As análises realizadas foram direcionadas pelas perguntas de negócio definidas inicialmente.

### 7.1 Evolução das vendas

Foi analisada a evolução da quantidade de pedidos, do valor das vendas e do ticket médio ao longo do período disponível na base.

Os resultados mostram crescimento do volume de pedidos e do valor movimentado, enquanto o ticket médio permaneceu relativamente estável nos períodos de maior movimentação.

### 7.2 Categorias de produtos

Foram comparadas as categorias considerando quantidade de itens vendidos, valor total das vendas e valor médio por item.

Os resultados mostram que maior volume de itens não significa necessariamente maior valor financeiro, devido às diferenças de valor médio entre as categorias.

### 7.3 Clientes

Foram analisados os clientes a partir do `customer_unique_id`, permitindo identificar compras recorrentes.

A base possui 96.096 clientes únicos, dos quais 2.997 realizaram mais de uma compra.

Os clientes recorrentes representam uma parcela reduzida da base, mas apresentam valor médio de compras superior ao grupo de clientes que realizou apenas uma compra.

### 7.4 Avaliações

Foi analisada a distribuição das notas atribuídas pelos clientes.

As avaliações apresentam concentração nas notas mais altas, com 77,07% das avaliações com nota disponível apresentando pontuação 4 ou 5.

## 8. Arquitetura do pipeline

O fluxo desenvolvido pode ser resumido como:

**Arquivos CSV → Unity Catalog Volume → Bronze → Silver → Gold → Análises**

A arquitetura permite separar a ingestão, o tratamento, a modelagem e o consumo analítico dos dados.

## 9. Autoavaliação

O projeto permitiu aplicar conceitos de ingestão, armazenamento em nuvem, arquitetura Medallion, transformação, modelagem, qualidade e análise de dados utilizando Databricks e SQL.

Um dos principais aprendizados foi a importância de compreender a granularidade e a cardinalidade das tabelas antes de realizar os relacionamentos, especialmente entre pedidos, itens e pagamentos.

Como evolução futura, o pipeline poderia incorporar novas fontes de dados, maior automação e novas análises a partir da camada Gold.
