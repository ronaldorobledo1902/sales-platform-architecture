# ADR-002: Utilização do Azure Cosmos DB como Banco de Dados Principal

## Status

Accepted

## Data

2026-06-09

## Contexto

A plataforma de Gestão de Vendas  deverá suportar crescimento contínuo do volume de clientes, produtos, pedidos e integrações externas.

Os requisitos não funcionais definidos para a solução incluem:

- Escalabilidade horizontal
- Alta disponibilidade
- Baixa latência de leitura e escrita
- Distribuição geográfica futura
- Alta taxa de processamento de pedidos
- Flexibilidade para evolução do modelo de dados

O domínio de vendas possui entidades com características naturalmente orientadas a documentos, tais como:

- Pedidos
- Itens do Pedido
- Histórico de Status
- Catálogo de Produtos
- Dados de Clientes

Além disso, espera-se crescimento do volume transacional ao longo dos próximos anos, tornando necessária uma solução capaz de escalar sem interrupções.

## Decisão

Utilizar Azure Cosmos DB como banco de dados principal da plataforma.

A solução utilizará:

- Azure Cosmos DB SQL API
- Modelo NoSQL orientado a documentos
- Escalabilidade baseada em RU/s (Request Units)
- Particionamento automático
- Alta disponibilidade gerenciada pela plataforma Azure

Os principais containers previstos são:

| Container | Responsabilidade |
|------------|------------------|
| Customers | Dados de clientes |
| Products | Catálogo de produtos |
| Orders | Pedidos |
| Inventory | Estoque |
| Events | Auditoria e eventos |

### Estratégia de Particionamento

A estratégia inicial será:

| Container | Partition Key |
|------------|--------------|
| Customers | customerId |
| Products | productId |
| Orders | customerId |
| Inventory | productId |

Essa abordagem busca distribuir adequadamente a carga e minimizar hotspots.

## Alternativas Consideradas

### Azure SQL Database

#### Vantagens

- Modelo relacional tradicional
- Facilidade para consultas complexas
- Forte consistência transacional

#### Desvantagens

- Escalabilidade horizontal limitada
- Necessidade de particionamento mais complexo
- Menor aderência ao modelo orientado a documentos

### PostgreSQL

#### Vantagens

- Open Source
- Grande comunidade
- Suporte a JSON

#### Desvantagens

- Maior esforço operacional
- Escalabilidade horizontal mais complexa

### MongoDB Atlas

#### Vantagens

- Modelo documental
- Escalabilidade horizontal

#### Desvantagens

- Plataforma adicional fora do ecossistema Azure
- Custos operacionais adicionais
- Integração menos nativa com serviços Azure

## Consequências

### Positivas

- Escalabilidade horizontal praticamente ilimitada
- Baixa latência para leitura e escrita
- Distribuição geográfica nativa
- Alta disponibilidade gerenciada
- Integração nativa com Azure
- Flexibilidade para evolução do modelo de dados
- Menor necessidade de gerenciamento operacional

### Negativas

- Custo baseado em RU/s pode aumentar significativamente com o crescimento da plataforma
- Necessidade de modelagem orientada a acesso
- Consultas relacionais complexas tornam-se mais difíceis
- Curva de aprendizado para equipes acostumadas apenas com bancos relacionais

## Riscos

### Risco 1

Dimensionamento incorreto de RU/s.

#### Impacto

Aumento inesperado de custos ou degradação de performance.

#### Mitigação

Monitoramento contínuo via Azure Monitor e ajuste periódico da capacidade provisionada.

### Risco 2

Escolha inadequada da Partition Key.

#### Impacto

Hot partitions e perda de performance.

#### Mitigação

Realização de testes de carga antes da entrada em produção.

### Risco 3

Consultas complexas não previstas.

#### Impacto

Necessidade de reestruturação do modelo de dados.

#### Mitigação

Aplicação de modelagem baseada em casos de uso e consultas mais frequentes.

## Capacity Planning Inicial

### Ano 1

| Item | Valor |
|--------|--------|
| Usuários | 5.000 |
| Pedidos/Dia | 1.000 |
| RU/s Provisionadas | 4.000 |
| Armazenamento | 100 GB |

### Ano 3

| Item | Valor |
|--------|--------|
| Usuários | 50.000 |
| Pedidos/Dia | 10.000 |
| RU/s Provisionadas | 25.000 |
| Armazenamento | 1 TB |

## Impacto Arquitetural

A utilização do Cosmos DB impacta diretamente os seguintes componentes:

- Sales Service
- User Service
- Stock Service
- Azure Service Bus
- Azure Cache for Redis

A modelagem dos dados deverá seguir princípios de design orientado a documentos e otimização para leitura.

## Referências

- Azure Cosmos DB Documentation
- Azure Well-Architected Framework
- Microsoft Cloud Adoption Framework
- NoSQL Distilled – Martin Fowler
- Azure Architecture Center