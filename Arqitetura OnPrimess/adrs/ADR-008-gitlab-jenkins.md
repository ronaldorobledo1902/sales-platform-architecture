# ADR-008 - Adoção do GitLab CE e Jenkins para CI/CD

## Status

Aprovado

## Data

2026-06-15

---

# Contexto

A Plataforma de Gestão de Vendas  será composta por múltiplos microsserviços desenvolvidos de forma independente.

A estratégia de desenvolvimento exige:

- Controle de versão centralizado
- Integração contínua
- Entrega contínua
- Automação de testes
- Padronização dos deploys
- Rastreabilidade das mudanças
- Redução de erros manuais

Além disso, a organização possui como premissa a utilização de soluções Open Source e infraestrutura On-Premises.

Foi necessário definir uma plataforma de versionamento e automação do ciclo de vida das aplicações.

---

# Decisão

Foi adotada uma solução composta por:

- GitLab Community Edition (GitLab CE)
- Jenkins
- Harbor Registry

Cada componente será responsável por uma etapa do ciclo DevOps.

---

# Motivação

A combinação entre GitLab CE e Jenkins oferece:

- Controle de código-fonte
- Gestão de branches
- Pull Requests (Merge Requests)
- Automação de build
- Execução de testes
- Criação de imagens Docker
- Deploy automatizado

Sem custos de licenciamento.

---

# Arquitetura da Solução

```text
Desenvolvedor

      │

      ▼

GitLab CE

      │

      ▼

Webhook

      │

      ▼

Jenkins

      │

 ┌────┼──────────────┬─────────────┐
 ▼    ▼              ▼             ▼

Build Testes Docker Build SonarQube

      │

      ▼

Harbor

      │

      ▼

Docker Compose
```

---

# Responsabilidades do GitLab CE

## Versionamento de Código

Armazenamento de:

- Código-fonte
- Infraestrutura como código
- Pipelines
- ADRs
- Diagramas

---

## Controle de Branches

Estratégia inicial:

```text
main

develop

feature/*
```

---

## Merge Requests

Permitir:

- Code Review
- Aprovação
- Auditoria

---

# Responsabilidades do Jenkins

## Integração Contínua

Execução automática de:

```text
Build

Testes

Validação
```

---

## Entrega Contínua

Automação de:

```text
Docker Build

Docker Push

Deploy
```

---

# Pipeline Padrão

```text
Commit

   │

   ▼

GitLab

   │

Webhook

   ▼

Jenkins

   │

   ▼

Build

   │

   ▼

Unit Tests

   │

   ▼

Code Quality

   │

   ▼

Docker Build

   │

   ▼

Harbor

   │

   ▼

Deploy
```

---

# Jenkinsfile Exemplo

```groovy
pipeline {

    agent any

    stages {

        stage('Restore') {
            steps {
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t sales-service .'
            }
        }
    }
}
```

---

# Responsabilidades do Harbor

## Docker Registry Privado

Armazenamento de:

- Imagens Docker
- Versionamento de imagens
- Controle de acesso

---

## Fluxo

```text
Jenkins

   │

Docker Push

   ▼

Harbor

   │

Docker Pull

   ▼

Produção
```

---

# Estratégia de Qualidade

Todo deploy deverá passar por:

## Build

Validação de compilação.

---

## Testes Unitários

Cobertura mínima:

```text
80%
```

---

## Code Review

Obrigatório para Merge Request.

---

## Análise Estática

Ferramenta sugerida:

```text
SonarQube
```

---

# Estratégia de Versionamento

Versionamento semântico.

Exemplo:

```text
1.0.0

1.1.0

2.0.0
```

---

# Alternativas Consideradas

## GitHub + GitHub Actions

### Vantagens

- Simplicidade
- Integração nativa

### Desvantagens

- Dependência de Cloud
- Custos em larga escala

### Motivo da Rejeição

Cliente exige ambiente On-Premises.

---

## Azure DevOps

### Vantagens

- Solução completa

### Desvantagens

- Licenciamento
- Dependência Microsoft

### Motivo da Rejeição

Incompatível com estratégia de redução de custos.

---

## GitLab CI/CD

### Vantagens

- Solução integrada

### Desvantagens

- Maior consumo de recursos

### Motivo da Rejeição

Jenkins possui maior maturidade interna e flexibilidade.

---

## GitLab CE + Jenkins

### Vantagens

- Open Source
- Flexível
- Escalável
- Amplamente adotado

### Desvantagens

- Dois componentes para administrar

### Motivo da Escolha

Melhor aderência aos requisitos da organização.

---

# Consequências

## Positivas

- Automação de deploy
- Redução de erros manuais
- Maior qualidade
- Auditoria de mudanças
- Padronização

---

## Negativas

- Curva de aprendizado
- Administração dos servidores CI/CD

---

# Trade-offs

| Aspecto | Benefício | Custo |
|----------|------------|--------|
| Automação | Alta | Infraestrutura adicional |
| Qualidade | Melhor | Tempo de pipeline |
| Auditoria | Completa | Governança |
| Escalabilidade | Alta | Administração |

---

# Capacity Planning Inicial

## GitLab CE

| Recurso | Valor |
|----------|--------|
| CPU | 4 vCPU |
| Memória | 8 GB |
| Disco | 200 GB SSD |

---

## Jenkins

| Recurso | Valor |
|----------|--------|
| CPU | 4 vCPU |
| Memória | 8 GB |
| Disco | 100 GB SSD |

---

## Harbor

| Recurso | Valor |
|----------|--------|
| CPU | 2 vCPU |
| Memória | 4 GB |
| Disco | 500 GB SSD |

---

# Observabilidade

Monitoramento através de:

- Prometheus
- Grafana
- ELK Stack

Métricas monitoradas:

- Tempo de build
- Tempo de deploy
- Falhas de pipeline
- Utilização de agentes
- Taxa de sucesso

---

# Referências

- GitLab Documentation
- Jenkins Documentation
- Harbor Documentation
- Continuous Delivery - Jez Humble

---

# Conclusão

A combinação GitLab CE, Jenkins e Harbor foi escolhida para suportar o ciclo completo de desenvolvimento e entrega contínua da plataforma.

A solução oferece automação, rastreabilidade, governança e redução de custos, alinhando-se à estratégia On-Premises e Open Source da organização.