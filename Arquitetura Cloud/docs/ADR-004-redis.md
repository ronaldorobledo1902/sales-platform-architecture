# ADR-004: Utilização do Azure Cache for Redis

## Status

Accepted

## Data

2026-06-09

## Contexto

A plataforma de Gestão de Vendas Omnichannel deverá atender um grande volume de consultas relacionadas a:

- Catálogo de Produtos
- Consulta de Estoque
- Dados de Clientes
- Sessões de Usuário

Grande parte dessas consultas possui características de leitura intensiva (read-heavy workload), podendo gerar sobrecarga no banco de dados principal.

Além disso, um dos requisitos não funcionais da solução é manter baixa latência de resposta para os usuários finais.

A utilização exclusiva do Azure Cosmos DB para todas as consultas aumentaria:

- Consumo de RU/s
- Custos operacionais
- Tempo médio de resposta

## Decisão

Utilizar Azure Cache for Redis como camada de cache distribuído da plataforma.

O Redis será utilizado para armazenar temporariamente informações frequentemente acessadas.

## Casos de Uso

### Catálogo de Produtos

Cache de informações dos produtos mais consultados.

### Estoque

Consulta rápida de disponibilidade.

### Sessões de Usuário

Armazenamento temporário de sessões autenticadas.

### Configurações

Dados de configuração acessados frequentemente.

## Estratégia de Cache

### Cache Aside Pattern

Fluxo:

1. Aplicação consulta o Redis.
2. Se existir:
   - Retorna o valor.
3. Caso não exista:
   - Consulta Cosmos DB.
   - Atualiza Redis.
   - Retorna resultado.

## Arquitetura Simplificada

```text
Cliente
   |
Sales Service
   |
Redis Cache
   |
Cosmos DB
```

## Alternativas Consideradas

### Sem Cache

#### Vantagens

- Arquitetura mais simples
- Menor número de componentes

#### Desvantagens

- Maior latência
- Maior consumo de RU/s
- Escalabilidade reduzida

---

### Cache Local na Aplicação

#### Vantagens

- Simplicidade

#### Desvantagens

- Inconsistência entre instâncias
- Não compartilhado entre pods

---

### Azure Managed Memory Cache

#### Vantagens

- Serviço gerenciado

#### Desvantagens

- Menor aderência ao ecossistema atual

## Consequências

### Positivas

- Redução da latência
- Menor carga no Cosmos DB
- Redução do consumo de RU/s
- Melhor experiência do usuário
- Escalabilidade horizontal

### Negativas

- Necessidade de gerenciamento de invalidação
- Possibilidade de dados desatualizados
- Complexidade adicional

## Riscos

### Risco 1

Dados inconsistentes.

#### Impacto

Informações desatualizadas para usuários.

#### Mitigação

Definição adequada de TTL e políticas de invalidação.

---

### Risco 2

Indisponibilidade do Redis.

#### Impacto

Aumento da carga sobre o Cosmos DB.

#### Mitigação

Fallback automático para banco principal.

## Estratégia de Expiração

| Tipo de Dado | TTL |
|--------------|------|
| Produtos | 30 minutos |
| Estoque | 5 minutos |
| Clientes | 15 minutos |
| Configurações | 60 minutos |

## Capacity Planning Inicial

### Ano 1

| Métrica | Valor |
|----------|--------|
| Cache Size | 2 GB |
| Requests/dia | 50.000 |
| Hit Rate Esperado | 70% |

### Ano 3

| Métrica | Valor |
|----------|--------|
| Cache Size | 8 GB |
| Requests/dia | 500.000 |
| Hit Rate Esperado | 85% |

## Impacto Arquitetural

O Redis será consumido por:

- User Service
- Sales Service
- Stock Service

A estratégia de cache deverá ser transparente para as camadas de negócio.

## Referências

- Azure Cache for Redis Documentation
- Azure Architecture Center
- Redis Documentation
- Designing Data Intensive Applications
- Microsoft Well-Architected Framework