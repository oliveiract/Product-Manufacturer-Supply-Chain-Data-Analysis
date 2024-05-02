# Análise na Cadeia de Suprimentos

<img src="https://imgur.com/HkUXqr1.png" width="100%"/>

<br>

# 1. Problema de Negócios

FastProduce é uma empresa em ascensão no ramo de fabricação de bens de consumo de rápida movimentação (FMCG), com sede em Gujarat, Índia. No momento, a empresa está ativa em três cidades: Surat, Ahmedabad e Vadodara. No entanto, a FastProduce tem planos de expandir sua presença para outras importantes cidades metropolitanas de nível 1 nos próximos 2 anos.

A FastProduce está enfrentando um desafio significativo: clientes importantes não renovaram seus contratos anuais devido a problemas no serviço. Especula-se que a entrega pontual e completa de produtos essenciais não tenha sido realizada, o que resultou em insatisfação dos clientes. Para resolver esse problema antes de expandir para novas cidades, a administração solicitou que a equipe de análise da cadeia de suprimentos monitore diariamente os níveis de serviço de entrega para todos os clientes. Isso possibilitará respostas rápidas e eficazes aos problemas identificados.

Com a implementação de análises para rastrear os níveis de serviço, a FastProduce poderá aprimorar sua capacidade de avaliar o atendimento em relação às expectativas dos clientes e identificar prontamente áreas que requerem melhorias.

## 1.1. Sobre os dados

Os dados utilizados neste projeto foram adquiridos por meio do desafio número 2, organizado pela Plataforma de Educação [CodeBasics](https://codebasics.io/challenge/codebasics-resume-project-challenge). O conjunto de dados compreende as seguintes informações:


### fact_order_lines: 
Contém todas as informações sobre pedidos e cada item dentro dos pedidos.

Variável | Descrição
---------|------------
order_id | ID único para cada pedido feito pelo cliente.
order_placement_date | É a data em que o cliente fez o pedido.
customer_id | ID único dado a cada um dos clientes.
product_id | ID único dado a cada um dos produtos.
order_qty | É o número de produtos solicitados pelo cliente para serem entregues.
agreed_delivery_date | É a data acordada entre o cliente e Fast Produce para entregar os produtos.
actual_delivery_date | É a data real em que a Fast Produce entregou o produto ao cliente.
delivered_qty | É o número de produtos que realmente foram entregues ao cliente.
on_time | '1' denota que o pedido foi entregue pontualmente. '0' denota que o pedido não foi entregue pontualmente.
in_full | '1' denota que o pedido foi entregue na quantidade total. '0' denota que o pedido não foi entregue na quantidade total.
on_time_in_full | '1' denota que o pedido foi entregue tanto pontualmente quanto na quantidade total. '0' denota que o pedido não foi entregue pontualmente ou na quantidade total.

### dim_customers: 
Contém todas as informações sobre os clientes.

Variável | Descrição
---------|------------
customer_id | ID único dado a cada cliente.
customer_name | Nome do cliente.
city | É a cidade onde o cliente está localizado.

### dim_products: 
Contém todas as informações sobre os produtos

Variável | Descrição
---------|-----------
product_name | É o nome do produto.
product_id | ID único dado a cada um dos produtos.
category | É a classe à qual o produto pertence.

### dim_date: 
Contém as datas no nível diário, mensal e os números das semanas do ano.

Variável | Descrição
---------|-----------
date | Data no nível diário.
mmm_yy | Data no nível mensal.
week_no | Número da semana do ano conforme a coluna de data.

### dim_targets_orders: 
Contém todos os dados de destino no nível do cliente.

Variável | Descrição
---------|-----------
customer_id | ID único dado a cada um dos clientes.
ontime_target % | Alvo atribuído para a porcentagem de Pontualidade para um determinado cliente.
infull_target % | Alvo atribuído para a porcentagem de Completo para um determinado cliente.
otif_target % | Alvo atribuído para a porcentagem de OTIF para um determinado cliente.
<br>

# 2. Premissas de Negócio

## 2.1. Definição de Métricas

Para avaliar o desempenho da FastProduce na entrega de seus produtos, o nível de serviço será medido utilizando uma abordagem padrão, com os seguintes indicadores:

### 2.1.1 Entrega no Prazo (On TIme - OT %)

Trata-se da pontualidade, que é atingida quando uma empresa consegue entregar os pedidos dos clientes dentro do prazo ou horário acordado, sem qualquer tipo de atraso. O tempo é fundamental para o sucesso das operações e para garantir a satisfação do cliente. A fórmula do on-time é:

 #### OT % = (Número de pedidos entregues dentro do prazo / Total de pedidos) * 100

### 2.1.2. Entrega Completa (In Full - IF %)

Trata-se da integridade, o que indica que uma empresa possui todos os itens solicitados disponíveis para entrega e pode disponibilizá-los ao cliente sem deixar nenhum item de fora. Isso garante a satisfação do cliente e evita problemas como devoluções ou reclamações. A  fórmula do in-full é:

 #### IF % = (Número de pedidos entregues completos / Total de pedidos) * 100

### 2.1.3. Entrega Completa e no Prazo (On Time & In Full - OTIF %)

Trata-se da combinação das duas métricas acima: pontualidade e integridade na entrega. A empresa se compromete a entregar os pedidos dentro do prazo estabelecido e também garantir a disponibilidade de todos os itens do pedido. É fundamental para avaliar o desempenho da cadeia de suprimentos e a eficiência das operações logísticas. A fórmua do on-time-in-full é:

 #### OTIF % = (Número de pedidos entregues dentro do prazo e completos / Total de pedidos) * 100

### 2.1.4. Preenchimento de Linha (Line Fill Rate - LIFR %)

  Ocorre quando uma empresa atende a um pedido do cliente, incluindo todos os itens diferentes solicitados, sem qualquer ausência, na primeira tentativa, respeitando a data especificada. Taxas elevadas de preenchimento de linha indicam uma boa capacidade de atendimento ao cliente e gestão de estoque. A fórmula para calcular a taxa de preenchimento de linha é: 
  
  #### LIFR % = (Número de linhas atendidas integralmente / Total de linhas de itens no pedido) * 100


### 2.1.5. Preenchimento de Volume (Volume Fill Rate - VOFR %)

 Ocorre quando a empresa atende integralmente ao pedido do cliente, entregando a quantidade total solicitada sem nenhum item faltando, logo na primeira vez. Uma alta taxa de preenchimento de volume indica uma eficaz capacidade de atender às demandas dos clientes. A fórmula para calcular a taxa de preenchimento de volume é:

#### VOFR % = (Volume total de itens entregues integralmente / Volume total de itens no pedido ) * 100 

## 2.2. Processo de Avaliação de Pedidos 

Os pedidos dos clientes serão avaliadas diariamente com base no nível de serviço alvo estipulado para cada cliente. Os indicadores mencionados serão demonstrados em comparação com as metas das métricas, com segmentação por cidade e por cliente. O propósito é examinar minuciosamente o desempenho da empresa em diversas localidades e em relação a cada cliente individualmente.

# 3. Planejamento da Solução

- Criar um painel de acordo com os requisitos fornecidos pelas partes interessadas.

- Apresentar a análise para o diretor de vendas.

# 4. Processo

## 4.1. Etapa 1 - Estatística Descritiva

Depois de carregar os conjuntos de dados, dedicamos tempo para compreender o significado de cada atributo e para limpar e transformar os dados conforme necessário. Exploramos os dados por meio da análise estatística descritiva, permitindo-nos examinar tanto as variáveis numéricas quanto as categóricas. Durante essa etapa, adquirimos conhecimento sobre a operação, bem como alguns insights de negócios.

- ### Atributos Númericos:

  <div align="center">
    <img src="https://imgur.com/TlLlIXN.png" width="90%">
  </div>  

  <br>

  - Para a empresa FastProduce, a entrega dos pedidos dos clientes dentro do prazo acordado parece ser uma prioridade, pois a métrica de On Time Target % apresenta valores mais elevados em comparação com a métrica In Full Target %. A métrica On Time Target % varia de um mínimo de 75% a um máximo de 92%, enquanto a métrica In Full Target % varia de um mínimo de 65% a um máximo de 82%.

  - Há um pedido registrado de 20 produtos, mas apenas 16 foram entregues. No entanto, em outro caso, um pedido de 500 produtos foi entregue integralmente.

- ### Atributos Categóricos:

  - Conforme mencionado anteriormente, vamos analisar as métricas em relação à segmentação por cidade e por cliente, com foco na comparação das metas determinadas de OTIF.

  <div align="center">
  <img src="https://imgur.com/Hl732WH.png" width="90%">
  </div>

  <br>

  - **Distribuição OTIF% Cidade**

      Os dados mostram que Surat demonstra um desempenho mais forte, com taxas de entrega dentro do prazo e completas consistentemente superiores em comparação com Vadodara.  No entanto, é importante notar que todas as cidades enfrentam metas consideradas baixas, uma vez que atingir apenas 51% já é considerado baixo, e mesmo alcançando 75% pode ser visto como um padrão baixo para o máximo. Essas metas sugerem oportunidades para melhorias adicionais na eficiência das entregas em todas as cidades.


  - **Distribuição OTIF% Clientes**

       As análises indicam que as empresas Chipter Stores, Viveks Stores e Expert Mart tiveram metas OTIF mais elevadas, todas com valores mínimos próximos a 70%. Em contrapartida, empresas como Lotus Mart, Coolblue e Acclaimed Stores tiveram metas inferiores, não atingindo sequer 65% da porcentagem da meta OTIF.