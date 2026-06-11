# ADR-006: Utilização do Azure Front Door com Web Application Firewall (WAF)

## Status

Accepted

## Data

2026-06-09

## Contexto

A plataforma de Gestão de Vendas  será exposta publicamente através da internet para acesso de:

- Clientes
- Parceiros
- Marketplaces
- Aplicações Mobile

Os requisitos não funcionais da solução incluem:

- Alta disponibilidade
- Baixa latência
- Segurança
- Proteção contra ataques web
- Escalabilidade global

A exposição direta do Azure API Management para a internet aumenta a superfície de ataque da aplicação e não oferece recursos 
avançados de proteção e otimização de tráfego.

Além disso, existe a necessidade futura de suportar múltiplas regiões Azure para garantir continuidade de negócio.

## Decisão

Utilizar Azure Front Door como camada global de entrada da plataforma.

O Azure Front Door será configurado juntamente com o Web Application Firewall (WAF).

A arquitetura seguirá o fluxo:

```text
Internet
   |
Azure Front Door + WAF
   |
Azure API Management
   |
AKS
```

## Responsabilidades

### Azure Front Door

- Entrada global da aplicação
- Balanceamento de tráfego
- Distribuição geográfica
- SSL/TLS
- Failover entre regiões

### Web Application Firewall

- Proteção contra ataques OWASP Top 10
- Bloqueio de IPs maliciosos
- Proteção contra SQL Injection
- Proteção contra Cross Site Scripting (XSS)
- Rate Limiting

## Alternativas Consideradas

### Exposição Direta do API Management

#### Vantagens

- Menor custo
- Arquitetura mais simples

#### Desvantagens

- Menor proteção
- Ausência de camada global
- Menor capacidade de mitigação de ataques

---

### Azure Application Gateway

#### Vantagens

- WAF integrado
- Balanceamento local

#### Desvantagens

- Escopo regional
- Menor capacidade global

---

### CDN Tradicional

#### Vantagens

- Redução de latência

#### Desvantagens

- Não oferece proteção completa de aplicações
- Não substitui WAF

## Consequências

### Positivas

- Proteção adicional da aplicação
- Menor superfície de ataque
- Melhor disponibilidade global
- Menor latência para usuários
- Escalabilidade geográfica
- Failover automatizado

### Negativas

- Aumento de custos
- Componente adicional para administração
- Maior complexidade da arquitetura

## Riscos

### Risco 1

Configuração incorreta de regras WAF.

#### Impacto

Bloqueio indevido de usuários legítimos.

#### Mitigação

Execução inicial em modo Detection antes da ativação em modo Prevention.

---

### Risco 2

Configuração incorreta de roteamento.

#### Impacto

Indisponibilidade parcial da aplicação.

#### Mitigação

Testes automatizados e validações em ambiente de homologação.

---

### Risco 3

Custos superiores ao previsto.

#### Impacto

Aumento do custo operacional.

#### Mitigação

Monitoramento contínuo de tráfego e revisão periódica de capacidade.

## Estratégia de Segurança

### Regras Gerenciadas

Utilizar conjunto padrão:

- OWASP 3.2

### Rate Limiting

Aplicar limites por IP para evitar abuso.

### TLS

Versão mínima:

```text
TLS 1.2
```

### Proteção de APIs

Somente o Azure Front Door poderá acessar o Azure API Management.

## Capacity Planning Inicial

### Ano 1

| Métrica | Valor |
|----------|--------|
| Usuários Ativos | 5.000 |
| Requisições/dia | 100.000 |
| Banda Mensal | 500 GB |

### Ano 3

| Métrica | Valor |
|----------|--------|
| Usuários Ativos | 50.000 |
| Requisições/dia | 1.000.000 |
| Banda Mensal | 5 TB |

## Impacto Arquitetural

O Azure Front Door torna-se o único ponto de entrada público da plataforma.

Todos os acessos externos deverão seguir o fluxo:

```text
Cliente
   |
Front Door + WAF
   |
API Management
   |
Microsserviços
```

Isso reduz a exposição direta dos serviços internos e melhora a postura de segurança da solução.

## Referências

- Azure Front Door Documentation
- Azure WAF Documentation
- Azure Architecture Center
- OWASP Top 10
- Microsoft Well-Architected Framework