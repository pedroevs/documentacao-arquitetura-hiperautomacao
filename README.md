# Documentação de Arquitetura — Plataforma de Hiperautomação de Processos

## Descrição
Documentação de arquitetura produzida com a abordagem **diagrams as code** (Mermaid), como atividade prática da disciplina de Projeto e Arquitetura de Software com Apoio de IA Generativa. O repositório concentra a descrição do sistema em linguagem natural, diagramas estrutural (visão de containers inspirada no C4) e comportamental (sequência da jornada crítica), além das decisões e ajustes feitos sobre o que o modelo de IA gerou.

## Estrutura
documentacao-arquitetura-hiperautomacao/
├── README.md
└── docs/
    ├── descricao-sistema.md
    ├── diagrama-containers.mmd
    ├── diagrama-sequencia.mmd
    └── decisoes-ajustes.md

## Descrição do sistema (resumo)
Plataforma que orquestra processos de negócio (BPMN) e decisões automatizadas (DMN) com Camunda 8, integrando sistemas legados de forma incremental. Stack: Java 17, Spring Boot, Camunda 8, PostgreSQL, Redis, mensageria. Restrições: equipe pequena, LGPD, legado crítico.

## Diagramas

### Visão de Containers (estrutural)

```mermaid
flowchart TB
    subgraph Usuarios["Usuários"]
        U1[Analista de Negócios]
        U2[Operador de Processos]
        U3[Cliente Final]
    end

    subgraph Apresentacao["Camada de Apresentação"]
        WEB[Portal Web - React / TypeScript]
    end

    subgraph Orquestracao["Camada de Orquestração"]
        API[API Gateway - Spring Boot / Java 17]
        CAM[Camunda 8 - Orquestração BPMN e Decisões DMN]
    end

    subgraph Servicos["Camada de Serviços"]
        SVC_INS[Serviço de Inscrições - Spring Boot]
        SVC_PAG[Serviço de Pagamentos - Spring Boot]
        SVC_NOT[Serviço de Notificações - Spring Boot]
    end

    subgraph Dados["Camada de Dados"]
        PG[(PostgreSQL - Dados transacionais)]
        RD[(Redis - Cache / Sessão)]
    end

    subgraph Externos["Integrações Externas"]
        ERP[ERP Legado - Mainframe]
        PAY[Gateway de Pagamento]
        MAIL[Serviço de E-mail]
    end

    U1 --> WEB
    U2 --> WEB
    U3 --> WEB
    WEB --> API
    API --> CAM
    CAM --> SVC_INS
    CAM --> SVC_PAG
    CAM --> SVC_NOT
    SVC_INS --> PG
    SVC_PAG --> PG
    SVC_NOT --> PG
    SVC_INS --> RD
    SVC_PAG --> PAY
    SVC_NOT --> MAIL
    SVC_INS --> ERP
```

### Jornada crítica: inscrição com pagamento e notificação (comportamental)

```mermaid
sequenceDiagram
    participant U as Participante
    participant WEB as Portal Web
    participant API as API Gateway
    participant CAM as Camunda 8
    participant INS as Serviço de Inscrições
    participant PAG as Serviço de Pagamentos
    participant NOT as Serviço de Notificações
    participant ERP as ERP Legado
    participant DB as PostgreSQL

    U->>WEB: Solicita inscrição no evento
    WEB->>API: POST /inscricoes
    API->>CAM: Inicia instância do processo BPMN
    CAM->>INS: Executa tarefa "Registrar inscrição"
    INS->>DB: Insere inscrição (status PENDENTE)
    INS-->>CAM: Inscrição registrada
    CAM->>PAG: Executa tarefa "Processar pagamento"
    PAG->>ERP: Consulta dados do participante
    ERP-->>PAG: Dados validados
    PAG->>PAG: Processa pagamento (cartão)
    PAG-->>CAM: Pagamento aprovado
    CAM->>INS: Atualiza status para CONFIRMADA
    INS->>DB: Atualiza inscrição (CONFIRMADA)
    CAM->>NOT: Executa tarefa "Enviar confirmação"
    NOT-->>U: E-mail de confirmação
    CAM-->>API: Processo concluído
    API-->>WEB: HTTP 201 - Inscrição confirmada
    WEB-->>U: Comprovante exibido
```

## Decisões e ajustes sobre o que o modelo gerou
Ver [docs/decisoes-ajustes.md](docs/decisoes-ajustes.md).

## Uso futuro como contexto para agentes
Este repositório foi pensado como base de documentação que pode servir de contexto para agentes de desenvolvimento implementarem o sistema com aderência à arquitetura documentada. Para isso, ainda faltam: contratos OpenAPI, ADRs, modelo de dados e políticas de integração (ver seção de lacunas na descrição).
