# Decisões e Ajustes sobre o que o Modelo Gerou
 
## O que o modelo inferiu corretamente
- A separação em camadas (apresentação, orquestração, serviços e dados) — o modelo identificou sozinho a estrutura típica de uma plataforma BPM;
- O papel central do Camunda 8 como orquestrador de processos, e não como um serviço comum;
- A necessidade de integração com ERP legado e gateway de pagamento externo;
- O fluxo natural da jornada: inscrição → pagamento → notificação.
 
## O que precisei ajustar
- O modelo inicialmente fazia o API Gateway chamar os serviços diretamente, ignorando o Camunda como orquestrador — corrigi o fluxo para passar pelo orquestrador;
- O modelo omitiu o Redis (cache/sessão) e o serviço de decisões DMN como elementos próprios — adicionei ambos;
- A notação misturava elementos de flowchart genérico com C4 — padronizei para a semântica de containers;
- No diagrama de sequência, o modelo não representava a consulta ao ERP dentro do fluxo de pagamento — incluí essa etapa, que é crítica no meu contexto de legado.
 
## O que a documentação precisaria ter a mais para um agente construir o sistema sem inventar decisões
- Contratos de API (OpenAPI) de cada serviço — sem eles, o agente inventaria endpoints e payloads;
- ADRs registrando as decisões já tomadas (ex.: por que Camunda 8, por que integração via filas) — evita que o agente redecida o que já foi decidido;
- Modelo de dados (entidades e relacionamentos) — o agente precisa saber o que persistir e como;
- Regras de negócio formais (RN01, RN02...) — o agente não deve inferir regras de negócio a partir de exemplos;
- Políticas de integração (retry, timeout, idempotência, DLQ) — sem isso, o agente inventaria o comportamento em falha;
- Padrões de observabilidade e segurança/LGPD (trilhas de auditoria, anonimização) — requisitos não funcionais que o agente não consegue deduzir.
