# ADR-004 - Adoção do Redis como Cache Distribuído

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas deverá suportar múltiplos acessos simultâneos aos serviços de:

- Clientes
- Produtos
- Estoque
- Pedidos

Diversas consultas apresentam características de leitura frequente e baixa taxa de alteração, tornando inadequado acessar o banco de dados principal em todas as requisições.

Além disso, a solução precisa reduzir latência, melhorar o tempo de resposta e minimizar a carga sobre o MongoDB.

Foi necessário definir uma tecnologia de cache distribuído para atender esses requisitos.

---

# Decisão

Foi adotado o Redis como mecanismo de cache distribuído da plataforma.

O Redis será utilizado para:

- Cache de consultas
- Cache de produtos
- Cache de estoque
- Cache de configurações
- Rate Limiting
- Armazenamento temporário de sessões
- Controle de bloqueios distribuídos

---

# Motivação

A escolha do Redis foi baseada nos seguintes fatores:

- Open Source
- Alta performance
- Baixa latência
- Facilidade de integração com .NET
- Estruturas de dados avançadas
- Suporte a clusterização
- Ampla adoção no mercado

---

# Arquitetura de Cache

```text
Cliente
   │
   ▼

Sales Service

   │
   ▼

Redis Cache

   │
 Cache Hit
   │
   ▼

Resposta

---------------------------------

Cache Miss

   │
   ▼

MongoDB

   │
   ▼

Redis

   │
   ▼

Resposta
```

---

# Casos de Uso

## Consulta de Produtos

Informações frequentemente consultadas:

- Nome
- Preço
- Categoria
- Disponibilidade

Exemplo de chave:

```text
product:123
```

---

## Consulta de Estoque

Informações frequentemente consultadas:

- Quantidade disponível
- Reservas
- Localização

Exemplo:

```text
stock:123
```

---

## Consulta de Clientes

Exemplo:

```text
customer:456
```

---

# Estratégia de Cache

## Cache Aside Pattern

Fluxo adotado:

```text
Aplicação
     │
     ▼

Redis

     │
 Cache Miss
     ▼

MongoDB

     │
     ▼

Redis

     │
     ▼

Aplicação
```

---

## Tempo de Expiração

| Informação | TTL |
|------------|------|
| Produtos | 30 minutos |
| Estoque | 5 minutos |
| Clientes | 15 minutos |
| Configurações | 1 hora |

---

# Alternativas Consideradas

## Cache em Memória da Aplicação

### Vantagens

- Simplicidade
- Baixa latência

### Desvantagens

- Não compartilhado
- Não escalável
- Inconsistência entre instâncias

### Motivo da Rejeição

Não atende ambientes distribuídos.

---

## Memcached

### Vantagens

- Simples
- Rápido

### Desvantagens

- Menos funcionalidades
- Menor flexibilidade

### Motivo da Rejeição

Redis oferece mais recursos e maior adoção.

---

## Redis

### Vantagens

- Open Source
- Distribuído
- Persistência opcional
- Estruturas avançadas
- Escalável

### Desvantagens

- Necessidade de monitoramento
- Consumo de memória

### Motivo da Escolha

Melhor equilíbrio entre desempenho, simplicidade e recursos.

---

# Estratégia de Invalidação

A atualização de dados no MongoDB deverá invalidar o cache correspondente.

Exemplo:

```text
Produto Atualizado
       │
       ▼

Remover

product:123
```

Na próxima consulta:

```text
MongoDB
   │
   ▼

Redis Atualizado
```

---

# Uso para Rate Limiting

Redis será utilizado para controle de requisições.

Exemplo:

```text
100 requisições
por minuto
por usuário
```

Benefícios:

- Proteção contra abuso
- Controle de consumo de APIs
- Redução de carga

---

# Uso para Locks Distribuídos

Redis poderá ser utilizado para evitar processamento concorrente.

Exemplo:

```text
Processamento de Pedido
```

Apenas uma instância poderá executar determinada operação simultaneamente.

---

# Consequências

## Positivas

- Redução da carga no MongoDB
- Melhor tempo de resposta
- Menor latência
- Escalabilidade horizontal
- Maior experiência do usuário

---

## Negativas

- Complexidade adicional
- Necessidade de sincronização dos dados
- Monitoramento do cache

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Performance | Alta | Mais infraestrutura |
| Escalabilidade | Alta | Administração adicional |
| Latência | Baixa | Estratégia de invalidação |
| Custo | Baixo | Consumo de memória |

---

# Capacity Planning Inicial

## Redis

| Recurso | Valor |
|----------|--------|
| Nodes | 1 |
| CPU | 2 vCPU |
| Memória | 8 GB |
| Disco | 50 GB SSD |

---

## Crescimento Futuro

Possibilidade de evolução para:

```text
Redis Sentinel
```

ou

```text
Redis Cluster
```

garantindo alta disponibilidade e escalabilidade.

---

# Observabilidade

Monitoramento através de:

- Prometheus
- Grafana

Métricas monitoradas:

- Hit Rate
- Miss Rate
- Uso de Memória
- Conexões Ativas
- Latência
- Throughput

---

# Referências

- Redis Documentation
- Redis Design Patterns
- Microsoft Distributed Caching Guidelines
- High Performance Browser Networking

---

# Conclusão

O Redis foi escolhido como solução de cache distribuído da plataforma devido à sua alta performance, simplicidade operacional e ampla adoção no mercado.

Sua utilização permitirá reduzir a carga sobre o MongoDB, melhorar o tempo de resposta das APIs e fornecer mecanismos adicionais de controle como rate limiting e locks distribuídos.