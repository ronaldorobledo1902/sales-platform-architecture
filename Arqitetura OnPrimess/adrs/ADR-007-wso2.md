# ADR-007 - Adoção do WSO2 API Manager como API Gateway Corporativo

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas  será composta por múltiplos microsserviços distribuídos.

Os consumidores das APIs incluem:

- Aplicações Web
- Aplicações Mobile
- Sistemas ERP
- Parceiros de Negócio
- Marketplaces
- Sistemas Internos

Foi necessário definir uma camada centralizada para gerenciamento, proteção e governança das APIs expostas pela plataforma.

Além disso, o cliente possui como premissa a utilização de soluções Open Source para redução de custos de licenciamento.

---

# Decisão

Foi adotado o WSO2 API Manager como plataforma corporativa de gerenciamento de APIs.

O WSO2 será responsável por centralizar:

- Exposição das APIs
- Autenticação
- Autorização
- Rate Limiting
- Versionamento
- Monitoramento
- Governança
- Portal para desenvolvedores

---

# Motivação

A adoção do WSO2 proporciona uma plataforma completa de API Management sem custos de licenciamento.

A solução oferece recursos normalmente encontrados apenas em produtos comerciais como:

- Azure API Management
- Kong Enterprise
- MuleSoft
- Apigee

---

# Arquitetura da Solução

```text
Internet

   │

   ▼

NGINX

   │

   ▼

WSO2 API Manager

   │

 ┌─┬───────────────┬───────────────┐
 ▼ ▼               ▼               ▼

Sales API     Product API     User API
```

---

# Responsabilidades do WSO2

## API Gateway

Receber e encaminhar requisições para os microsserviços.

---

## Autenticação

Suporte para:

```text
OAuth2

OpenID Connect

JWT
```

---

## Autorização

Controle de acesso baseado em:

```text
Roles

Scopes

Claims
```

---

## Rate Limiting

Controle de consumo das APIs.

Exemplo:

```text
1000 requisições por minuto
```

por consumidor.

---

## Versionamento

Permitir coexistência de múltiplas versões.

Exemplo:

```text
/api/v1/orders

/api/v2/orders
```

---

## Analytics

Monitoramento do consumo das APIs.

Exemplos:

- Quantidade de chamadas
- Tempo de resposta
- Erros
- Usuários consumidores

---

## Portal do Desenvolvedor

Disponibilização de:

- Documentação
- Swagger/OpenAPI
- Catálogo de APIs
- Solicitação de acesso

---

# Fluxo de Requisição

```text
Cliente

   │

   ▼

WSO2

   │

Validação JWT

   │

Rate Limit

   │

Roteamento

   ▼

Microsserviço
```

---

# Estratégia de Segurança

Todas as APIs serão protegidas através de:

```text
OAuth2
```

e

```text
JWT Bearer Token
```

---

## Fluxo de Autenticação

```text
Cliente

   │

   ▼

Identity Provider

   │

JWT

   ▼

WSO2

   │

Validação

   ▼

API
```

---

# Governança de APIs

O WSO2 permitirá:

- Cadastro centralizado
- Ciclo de vida das APIs
- Aprovação de publicação
- Gestão de consumidores
- Controle de versões

---

# Alternativas Consideradas

## Exposição Direta dos Microsserviços

### Vantagens

- Simplicidade

### Desvantagens

- Ausência de governança
- Segurança distribuída
- Dificuldade de manutenção

### Motivo da Rejeição

Não atende requisitos corporativos.

---

## Kong Community

### Vantagens

- Open Source
- Leve

### Desvantagens

- Menor conjunto de funcionalidades
- Recursos avançados dependem da versão Enterprise

### Motivo da Rejeição

WSO2 oferece maior cobertura funcional.

---

## Azure API Management

### Vantagens

- Serviço gerenciado
- Integração Azure

### Desvantagens

- Dependência de Cloud
- Licenciamento

### Motivo da Rejeição

Incompatível com a estratégia On-Premises.

---

## WSO2 API Manager

### Vantagens

- Open Source
- Completo
- API Gateway
- Developer Portal
- OAuth2
- Analytics

### Desvantagens

- Curva de aprendizado
- Maior esforço inicial de configuração

### Motivo da Escolha

Melhor aderência ao cenário corporativo On-Premises.

---

# Consequências

## Positivas

- Centralização da segurança
- Governança de APIs
- Melhor experiência para integrações
- Controle de consumo
- Padronização

---

## Negativas

- Novo componente na arquitetura
- Administração adicional
- Curva de aprendizado

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Segurança | Alta | Configuração adicional |
| Governança | Centralizada | Administração |
| Escalabilidade | Alta | Infraestrutura dedicada |
| Integrações | Simplificadas | Curva de aprendizado |

---

# Capacity Planning Inicial

## WSO2 API Manager

| Recurso | Valor |
|----------|--------|
| Instâncias | 2 |
| CPU | 4 vCPU |
| Memória | 8 GB |
| Disco | 100 GB SSD |

---

# Estratégia de Alta Disponibilidade

Inicialmente:

```text
WSO2-01

WSO2-02
```

Balanceados através do HAProxy.

```text
HAProxy

   │

 ┌─┴─────────┐
 ▼           ▼

WSO2-01   WSO2-02
```

---

# Observabilidade

Monitoramento através de:

- Prometheus
- Grafana
- ELK Stack

Métricas monitoradas:

- Requests por segundo
- Tempo médio de resposta
- Tokens emitidos
- APIs mais consumidas
- Erros de autenticação
- Erros HTTP

---

# Referências

- WSO2 API Manager Documentation
- OAuth2 RFC 6749
- OpenID Connect Specification
- API Management Best Practices

---

# Conclusão

O WSO2 API Manager foi escolhido como plataforma de gerenciamento de APIs por oferecer uma solução completa de API Gateway, segurança, governança e monitoramento sem custos de licenciamento.

A decisão está alinhada à estratégia On-Premises da organização e fornece os recursos necessários para suportar o crescimento da plataforma e suas futuras integrações.