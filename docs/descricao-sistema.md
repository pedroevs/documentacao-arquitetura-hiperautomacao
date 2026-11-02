# Plataforma de Hiperautomação de Processos — Documentação de Arquitetura (Visão de Containers)

## 1. Escopo

A plataforma orquestra processos de negócio ponta a ponta (BPMN) e decisões automatizadas (DMN) para clientes corporativos, integrando sistemas legados e modernizando fluxos de forma incremental. Esta documentação cobre a visão de containers do sistema — a estrutura de alto nível de aplicações e dados — e a jornada crítica de "inscrição com pagamento e notificação".

## 2. Nível da visão

Containers (nível 2 do modelo C4), com foco em como as aplicações se organizam e se comunicam, sem detalhar componentes internos de cada serviço.

## 3. Limites e responsabilidades

- **Portal Web:** interface única para analistas de negócio, operadores de processo e clientes finais.
- **API Gateway:** ponto único de entrada, autenticação, autorização e roteamento das requisições.
- **Camunda 8:** orquestrador central — executa instâncias de processo BPMN, avalia decisões DMN e coordena as chamadas aos serviços.
- **Serviços de domínio (Inscrições, Pagamentos, Notificações):** implementam regras de negócio específicas e são acionados pelo orquestrador.
- **PostgreSQL:** fonte de verdade dos dados transacionais; **Redis:** cache e sessão.

## 4. Integrações

- ERP legado (mainframe) para consulta/validação de dados de clientes;
- Gateway de pagamento externo;
- Serviço de e-mail transacional.

## 5. Restrições

- Equipe pequena (5–8 pessoas) com perfil BizDevOps;
- LGPD (dados pessoais atravessam os fluxos);
- Legado crítico que não pode ser reescrito;
- Soluções devem ser incrementais e simples de manter.

## 6. Lacunas conhecidas

- Não há definição formal de contratos OpenAPI versionados;
- Políticas de retry/timeout por integração não estão documentadas;
- Não há ADRs registrados para decisões já tomadas;
- Observabilidade (tracing distribuído) ainda não padronizada.
