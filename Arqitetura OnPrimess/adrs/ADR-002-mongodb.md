# ADR-002 - Adoção do MongoDB como Banco de Dados Principal

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas  necessita de um mecanismo de persistência capaz de armazenar informações relacionadas a:

- Clientes
- Produtos
- Estoque
- Pedidos
- Eventos de integração
- Dados operacionais

A solução deverá operar em ambiente On-Premises utilizando infraestrutura própria do cliente, com foco na redução de custos de licenciamento e facilidade de administração.

Além disso, a arquitetura adotada é baseada em Microsserviços, onde cada serviço possui autonomia sobre seus próprios dados.

Foi necessário definir a tecnologia de banco de dados que melhor atendesse aos requisitos de flexibilidade, escalabilidade e custo operacional.

---

# Decisão

Foi adotado o MongoDB como banco de dados principal da plataforma.

O MongoDB será utilizado para armazenamento dos dados operacionais dos microsserviços, utilizando coleções independentes para cada domínio de negócio.

Exemplos:

| Serviço | Coleções |
|----------|----------|
| User Service | customers |
| Product Service | products |
| Stock Service | stock_movements |
| Sales Service | orders |
| Notification Service | notifications |

---

# Motivação

A escolha do MongoDB foi baseada nos seguintes fatores:

- Banco Open Source
- Ausência de custos de licenciamento
- Facilidade de operação On-Premises
- Modelo orientado a documentos
- Escalabilidade horizontal
- Alta performance para leitura e escrita
- Integração nativa com aplicações .NET
- Facilidade de evolução dos modelos de dados

---

# Alternativas Consideradas

## SQL Server

### Vantagens

- Banco amplamente utilizado no mercado
- Excelente suporte transacional
- Ferramentas maduras de administração

### Desvantagens

- Licenciamento elevado
- Escalabilidade horizontal limitada
- Dependência de fornecedor

### Motivo da Rejeição

O custo de licenciamento não está alinhado com a estratégia de redução de custos do projeto.

---

## PostgreSQL

### Vantagens

- Open Source
- Excelente suporte SQL
- Alta confiabilidade

### Desvantagens

- Maior rigidez de esquema
- Evolução de estruturas mais complexa

### Motivo da Rejeição

Embora seja uma excelente alternativa, o modelo orientado a documentos do MongoDB oferece maior flexibilidade para os domínios da aplicação.

---

## Azure Cosmos DB

### Vantagens

- Escalabilidade global
- Banco totalmente gerenciado
- Alta disponibilidade nativa

### Desvantagens

- Dependência da Azure
- Custos recorrentes
- Incompatível com estratégia On-Premises

### Motivo da Rejeição

A solução deverá operar integralmente em infraestrutura própria do cliente.

---

## MongoDB

### Vantagens

- Open Source
- Alta performance
- Modelo orientado a documentos
- Flexibilidade de esquema
- Facilidade de replicação
- Escalabilidade horizontal

### Desvantagens

- Consistência eventual em alguns cenários
- Menor aderência a consultas relacionais complexas

### Motivo da Escolha

Melhor equilíbrio entre custo, flexibilidade e escalabilidade para o cenário proposto.

---

# Arquitetura de Persistência

Cada microsserviço será responsável pelos seus próprios dados.

```text
Sales Service
      │
      ▼
orders

Product Service
      │
      ▼
products

User Service
      │
      ▼
customers

Stock Service
      │
      ▼
stock_movements
```

---

# Estratégia de Alta Disponibilidade

Será utilizado MongoDB Replica Set.

```text
MongoDB Primary
        │
 ┌──────┴──────┐
 ▼             ▼

Secondary   Secondary
```

Benefícios:

- Failover automático
- Alta disponibilidade
- Replicação dos dados
- Continuidade operacional

---

# Estratégia de Backup

Serão realizados:

## Backup Diário

- Backup incremental

## Backup Semanal

- Backup completo

## Retenção

| Tipo | Retenção |
|--------|----------|
| Diário | 30 dias |
| Semanal | 12 semanas |
| Mensal | 12 meses |

---

# Modelo de Dados

Exemplo de Pedido:

```json
{
  "_id": "ORD-001",
  "customerId": "CUS-001",
  "createdAt": "2026-06-15",
  "items": [
    {
      "productId": "PROD-001",
      "quantity": 2,
      "unitPrice": 100
    }
  ],
  "totalAmount": 200,
  "status": "Created"
}
```

---

# Estratégia de Escalabilidade

Inicialmente:

```text
Replica Set
```

Futuramente:

```text
Sharding
```

Permitindo distribuição horizontal dos dados.

---

# Impacto na Aplicação

Os microsserviços utilizarão:

```text
MongoDB Driver for .NET
```

Exemplo:

```csharp
var database = client.GetDatabase("sales");
var collection = database.GetCollection<Order>("orders");
```

---

# Consequências

## Positivas

- Baixo custo operacional
- Alta flexibilidade de modelagem
- Escalabilidade horizontal
- Excelente integração com microsserviços

---

## Negativas

- Ausência de joins complexos
- Maior responsabilidade da aplicação na modelagem
- Consistência eventual em cenários distribuídos

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Flexibilidade | Alta | Menor rigidez |
| Escalabilidade | Alta | Complexidade futura |
| Licenciamento | Zero | Necessidade de administração |
| Performance | Alta | Menos recursos relacionais |

---

# Capacity Planning Inicial

## Produção

### MongoDB Cluster

| Recurso | Valor |
|----------|--------|
| Nodes | 3 |
| Tipo | Replica Set |
| CPU | 4 vCPU por Node |
| Memória | 16 GB por Node |
| Disco | 500 GB SSD por Node |

---

# Referências

- MongoDB Architecture Guide
- MongoDB Production Notes
- MongoDB Replica Set Documentation
- Building Microservices - Sam Newman

---

# Conclusão

O MongoDB foi escolhido como banco de dados principal da plataforma por oferecer um excelente equilíbrio entre custo, flexibilidade, escalabilidade e aderência à arquitetura baseada em Microsserviços.

Sua capacidade de operação em ambiente On-Premises e ausência de custos de licenciamento tornam a solução aderente aos objetivos estratégicos do projeto.