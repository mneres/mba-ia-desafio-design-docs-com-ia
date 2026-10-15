# Tracker de Rastreabilidade

Mapeia cada item registrado em `docs/PRD.md`, `docs/RFC.md`, `docs/FDD.md` e `docs/adrs/` à sua origem na transcrição (`TRANSCRICAO.md`) ou no código.

Regras aplicadas:
- Cada linha tem uma única origem: a fala que pediu ou fechou o item, ou o arquivo de código que descreve o sistema atual, um ponto de integração ou o padrão seguido por uma hipótese.
- Hipóteses sem padrão de código e itens sem origem não têm linha; estão listados ao final.
- Caminhos marcados como novos nos documentos (por exemplo `src/modules/webhooks/` e `src/worker.ts`) não aparecem como Localização `CODIGO`; esses itens são rastreados pela fala que os decidiu.

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
| --- | --- | --- | --- | --- | --- |
| PRD-CTX-01 | docs/PRD.md | Contexto | OMS tem máquina de estados do pedido e nenhum mecanismo de notificação externa | CODIGO | src/modules/orders/order.status.ts |
| PRD-CTX-02 | docs/PRD.md | Restrição | Webhook só de saída: a plataforma envia, o cliente recebe | TRANSCRICAO | [09:02] Marcos |
| PRD-PROB-01 | docs/PRD.md | Problema | Clientes fazem polling em GET /orders, integração lenta e cara | TRANSCRICAO | [09:00] Marcos |
| PRD-PROB-02 | docs/PRD.md | Problema | Pedido formal de Atlas Comercial, MaxDistribuição e Nova Cargo | TRANSCRICAO | [09:00] Marcos |
| PRD-PROB-03 | docs/PRD.md | Problema | Atlas pode migrar para concorrente se não houver entrega até o fim do trimestre | TRANSCRICAO | [09:00] Marcos |
| PRD-PROB-04 | docs/PRD.md | Restrição | Atlas pediu a entrega para o fim de novembro | TRANSCRICAO | [09:45] Marcos |
| PRD-PROB-05 | docs/PRD.md | Restrição | Estimativa de três sprints com revisão de segurança incluída | TRANSCRICAO | [09:47] Larissa |
| PRD-PUB-01 | docs/PRD.md | Público-alvo | Usuários do OMS que representam o cliente cadastram webhooks pela API | TRANSCRICAO | [09:32] Marcos |
| PRD-PUB-02 | docs/PRD.md | Público-alvo | Administradores (ADMIN) reprocessam falhas definitivas | TRANSCRICAO | [09:36] Sofia |
| PRD-PUB-03 | docs/PRD.md | Público-alvo | Desenvolvedores dos clientes integram pelo portal de desenvolvedor | TRANSCRICAO | [09:40] Marcos |
| PRD-CEN-01 | docs/PRD.md | Cenário | Cliente escolhe receber só SHIPPED e DELIVERED | TRANSCRICAO | [09:33] Marcos |
| PRD-CEN-02 | docs/PRD.md | Cenário | Endpoint do cliente fora do ar por duas horas em manutenção planejada | TRANSCRICAO | [09:16] Diego |
| PRD-CEN-03 | docs/PRD.md | Cenário | Cliente descarta notificação repetida pelo identificador do evento | TRANSCRICAO | [09:25] Diego |
| PRD-CEN-04 | docs/PRD.md | Cenário | Usuário consulta o histórico de entregas para investigar falhas | TRANSCRICAO | [09:34] Marcos |
| PRD-OBJ-01 | docs/PRD.md | Métrica | Entrega abaixo de 10 segundos ("tempo real" para o cliente) | TRANSCRICAO | [09:02] Marcos |
| PRD-OBJ-02 | docs/PRD.md | Métrica | Atraso da plataforma de até 2 segundos antes do envio | TRANSCRICAO | [09:10] Larissa |
| PRD-OBJ-03 | docs/PRD.md | Métrica | Disponível em produção até o fim de novembro | TRANSCRICAO | [09:45] Marcos |
| PRD-OBJ-04 | docs/PRD.md | Métrica | Zero eventos perdidos: evento existe se e somente se a transação commitou | TRANSCRICAO | [09:06] Diego |
| PRD-OBJ-05 | docs/PRD.md | Métrica | Janela de recuperação automática de cerca de 15 horas | TRANSCRICAO | [09:17] Diego |
| PRD-ESC-01 | docs/PRD.md | Escopo | CRUD de endpoints com escolha dos status de interesse | TRANSCRICAO | [09:33] Bruno |
| PRD-ESC-02 | docs/PRD.md | Escopo | Notificação automática a cada mudança de status | TRANSCRICAO | [09:40] Bruno |
| PRD-ESC-03 | docs/PRD.md | Escopo | Assinatura de cada notificação e rotação de secret | TRANSCRICAO | [09:22] Sofia |
| PRD-ESC-04 | docs/PRD.md | Escopo | Retry automático, fila de falhas e reenvio por administrador | TRANSCRICAO | [09:48] Larissa |
| PRD-ESC-05 | docs/PRD.md | Escopo | Histórico de entregas por endpoint | TRANSCRICAO | [09:34] Marcos |
| PRD-ESC-06 | docs/PRD.md | Fora de Escopo | Aviso por email em falhas repetidas, adiado para próxima fase | TRANSCRICAO | [09:37] Larissa |
| PRD-ESC-07 | docs/PRD.md | Fora de Escopo | Rate limiting de envio, observar e decidir depois | TRANSCRICAO | [09:39] Larissa |
| PRD-ESC-08 | docs/PRD.md | Fora de Escopo | Painel visual do cliente, projeto separado do frontend | TRANSCRICAO | [09:40] Larissa |
| PRD-ESC-09 | docs/PRD.md | Fora de Escopo | Webhooks de entrada descartados | TRANSCRICAO | [09:02] Marcos |
| PRD-ESC-10 | docs/PRD.md | Fora de Escopo | Sem garantia de ordem global, apenas por pedido | TRANSCRICAO | [09:13] Larissa |
| PRD-ESC-11 | docs/PRD.md | Fora de Escopo | Exactly-once descartado | TRANSCRICAO | [09:25] Diego |
| PRD-ESC-12 | docs/PRD.md | Fora de Escopo | Arquivamento de notificações antigas fora desta feature | TRANSCRICAO | [09:08] Diego |
| PRD-ESC-13 | docs/PRD.md | Fora de Escopo | Restrição por papel no CRUD adiada | TRANSCRICAO | [09:37] Sofia |
| PRD-FR-01 | docs/PRD.md | Requisito Funcional | Cadastrar endpoint com customer, URL e status; customer informado na requisição, não do login | TRANSCRICAO | [09:32] Larissa |
| PRD-FR-02 | docs/PRD.md | Requisito Funcional | Secret gerada pela plataforma e devolvida no cadastro | TRANSCRICAO | [09:31] Marcos |
| PRD-FR-03 | docs/PRD.md | Requisito Funcional | Listar os webhooks de um customer | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-04 | docs/PRD.md | Requisito Funcional | Editar webhook | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-05 | docs/PRD.md | Requisito Funcional | Remover webhook | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-06 | docs/PRD.md | Requisito Funcional | Endpoint recebe só os status escolhidos; sem interessado, nada é gerado | TRANSCRICAO | [09:34] Bruno |
| PRD-FR-07 | docs/PRD.md | Requisito Funcional | Toda mudança de status gera notificação; nunca status sem evento | TRANSCRICAO | [09:40] Bruno |
| PRD-FR-08 | docs/PRD.md | Requisito Funcional | Conteúdo da notificação, sem itens do pedido | TRANSCRICAO | [09:43] Diego |
| PRD-FR-09 | docs/PRD.md | Requisito Funcional | Notificação assinada para o cliente validar origem e integridade | TRANSCRICAO | [09:20] Sofia |
| PRD-FR-10 | docs/PRD.md | Requisito Funcional | Rotação de secret com a anterior válida por 24 horas | TRANSCRICAO | [09:21] Sofia |
| PRD-FR-11 | docs/PRD.md | Requisito Funcional | Histórico de entregas com sucesso, payload, resposta e tempo | TRANSCRICAO | [09:34] Marcos |
| PRD-FR-12 | docs/PRD.md | Requisito Funcional | Retry automático em intervalos crescentes | TRANSCRICAO | [09:17] Larissa |
| PRD-FR-13 | docs/PRD.md | Requisito Funcional | Fila de falhas com conteúdo e motivo | TRANSCRICAO | [09:18] Diego |
| PRD-FR-14 | docs/PRD.md | Requisito Funcional | Só ADMIN reenvia da fila de falhas, com registro do autor | TRANSCRICAO | [09:36] Larissa |
| PRD-FR-15 | docs/PRD.md | Requisito Funcional | URLs sem https recusadas | TRANSCRICAO | [09:23] Sofia |
| PRD-NFR-01 | docs/PRD.md | Requisito Não Funcional | Entrega abaixo de 10 segundos | TRANSCRICAO | [09:02] Marcos |
| PRD-NFR-02 | docs/PRD.md | Requisito Não Funcional | Atraso da plataforma até 2 segundos | TRANSCRICAO | [09:10] Larissa |
| PRD-NFR-03 | docs/PRD.md | Requisito Não Funcional | Entrega at-least-once com deduplicação pelo cliente | TRANSCRICAO | [09:26] Larissa |
| PRD-NFR-04 | docs/PRD.md | Requisito Não Funcional | Timeout de 10 segundos por envio | TRANSCRICAO | [09:42] Diego |
| PRD-NFR-05 | docs/PRD.md | Requisito Não Funcional | Intervalos 1m, 5m, 30m, 2h, 12h | TRANSCRICAO | [09:17] Diego |
| PRD-NFR-06 | docs/PRD.md | Requisito Não Funcional | Ordem garantida por pedido com um único processo de envio | TRANSCRICAO | [09:13] Larissa |
| PRD-NFR-07 | docs/PRD.md | Requisito Não Funcional | HMAC-SHA256 com secret por endpoint | TRANSCRICAO | [09:22] Sofia |
| PRD-NFR-08 | docs/PRD.md | Requisito Não Funcional | Somente endereços https | TRANSCRICAO | [09:23] Sofia |
| PRD-NFR-09 | docs/PRD.md | Requisito Não Funcional | Notificação de no máximo 64KB, erro acima disso | TRANSCRICAO | [09:24] Larissa |
| PRD-NFR-10 | docs/PRD.md | Requisito Não Funcional | Registro de quem fez cada reenvio | TRANSCRICAO | [09:36] Sofia |
| PRD-NFR-11 | docs/PRD.md | Requisito Não Funcional | Reuso dos padrões existentes do OMS, sem alterar rotas existentes | TRANSCRICAO | [09:30] Larissa |
| PRD-NFR-12 | docs/PRD.md | Requisito Não Funcional | Envio em processo separado da API | TRANSCRICAO | [09:11] Diego |
| PRD-DEC-01 | docs/PRD.md | Decisão | Notificação registrada junto com a mudança de status (ADR-001) | TRANSCRICAO | [09:08] Larissa |
| PRD-DEC-02 | docs/PRD.md | Decisão | Processo separado verificando a cada 2 segundos (ADR-002) | TRANSCRICAO | [09:10] Larissa |
| PRD-DEC-03 | docs/PRD.md | Decisão | Retry por cerca de 15 horas e fila de falhas (ADR-003) | TRANSCRICAO | [09:17] Larissa |
| PRD-DEC-04 | docs/PRD.md | Decisão | Secret por endpoint com rotação (ADR-004) | TRANSCRICAO | [09:22] Sofia |
| PRD-DEC-05 | docs/PRD.md | Decisão | Entrega pelo menos uma vez (ADR-005) | TRANSCRICAO | [09:26] Larissa |
| PRD-DEC-06 | docs/PRD.md | Decisão | Reuso dos padrões do OMS (ADR-006) | TRANSCRICAO | [09:30] Larissa |
| PRD-DEC-07 | docs/PRD.md | Decisão | Conteúdo fixado no momento da mudança (ADR-007) | TRANSCRICAO | [09:52] Bruno |
| PRD-DEP-01 | docs/PRD.md | Dependência | Revisão de segurança de pelo menos dois dias úteis | TRANSCRICAO | [09:46] Sofia |
| PRD-DEP-02 | docs/PRD.md | Dependência | Documentação no portal de desenvolvedor | TRANSCRICAO | [09:26] Marcos |
| PRD-DEP-03 | docs/PRD.md | Dependência | Confirmação do prazo com a Atlas | TRANSCRICAO | [09:47] Marcos |
| PRD-DEP-04 | docs/PRD.md | Dependência | Revisão do doc de design por Larissa, Bruno e Diego | TRANSCRICAO | [09:50] Larissa |
| PRD-DEP-05 | docs/PRD.md | Dependência | Mesmo MySQL e mesma stack do OMS | TRANSCRICAO | [09:11] Diego |
| PRD-DEP-06 | docs/PRD.md | Dependência | Cliente deduplica pelo identificador do evento | TRANSCRICAO | [09:25] Diego |
| PRD-RISCO-01 | docs/PRD.md | Risco | Atraso no prazo pode levar à perda da Atlas | TRANSCRICAO | [09:00] Marcos |
| PRD-RISCO-02 | docs/PRD.md | Risco | Cliente não trata repetições; responsabilidade fica com ele | TRANSCRICAO | [09:25] Sofia |
| PRD-RISCO-03 | docs/PRD.md | Risco | Cliente fora do ar mais de 15 horas sem aviso, email adiado | TRANSCRICAO | [09:37] Larissa |
| PRD-RISCO-04 | docs/PRD.md | Risco | Rajada de mudanças vira rajada de chamadas ao cliente | TRANSCRICAO | [09:38] Diego |
| PRD-RISCO-05 | docs/PRD.md | Risco | Vazamento de secret, já ocorrido em log de cliente | TRANSCRICAO | [09:22] Diego |
| PRD-RISCO-06 | docs/PRD.md | Risco | Sem vínculo entre usuário e cliente, um usuário pode alterar webhooks de outro cliente | CODIGO | prisma/schema.prisma |
| PRD-RISCO-07 | docs/PRD.md | Risco | Número de tentativas ambíguo ("total 5 tentativas") | TRANSCRICAO | [09:48] Larissa |
| PRD-CA-01 | docs/PRD.md | Critério de Aceite | CRUD de webhooks com secret no cadastro | TRANSCRICAO | [09:33] Bruno |
| PRD-CA-02 | docs/PRD.md | Critério de Aceite | Endpoint http recusado | TRANSCRICAO | [09:23] Sofia |
| PRD-CA-03 | docs/PRD.md | Critério de Aceite | Notificação só para endpoints que escolheram o status | TRANSCRICAO | [09:34] Bruno |
| PRD-CA-04 | docs/PRD.md | Critério de Aceite | Notificação com os campos definidos e sem itens | TRANSCRICAO | [09:43] Diego |
| PRD-CA-05 | docs/PRD.md | Critério de Aceite | Assinatura validável inclusive nas 24 horas após rotação | TRANSCRICAO | [09:21] Sofia |
| PRD-CA-06 | docs/PRD.md | Critério de Aceite | Entrega em menos de 10 segundos com endpoint no ar | TRANSCRICAO | [09:02] Marcos |
| PRD-CA-07 | docs/PRD.md | Critério de Aceite | Sequência de retry e envio à fila de falhas | TRANSCRICAO | [09:17] Diego |
| PRD-CA-08 | docs/PRD.md | Critério de Aceite | Operador não reenvia; administrador reenvia com autor registrado | TRANSCRICAO | [09:36] Sofia |
| PRD-CA-09 | docs/PRD.md | Critério de Aceite | Histórico de entregas completo | TRANSCRICAO | [09:34] Marcos |
| PRD-CA-11 | docs/PRD.md | Critério de Aceite | Rotas existentes iguais; mudança de status só falha se a notificação não puder ser registrada | TRANSCRICAO | [09:40] Bruno |
| PRD-CA-10 | docs/PRD.md | Critério de Aceite | Revisão de segurança concluída antes do deploy | TRANSCRICAO | [09:46] Sofia |
| PRD-TEST-01 | docs/PRD.md | Estratégia de Teste | Testes de integração com Vitest contra MySQL real | CODIGO | tests/setup.ts |
| PRD-TEST-02 | docs/PRD.md | Estratégia de Teste | Testes ponta a ponta previstos no plano | TRANSCRICAO | [09:46] Larissa |
| PRD-TEST-03 | docs/PRD.md | Estratégia de Teste | Revisão de assinatura e geração de secret por Sofia | TRANSCRICAO | [09:46] Sofia |
| PRD-TEST-04 | docs/PRD.md | Estratégia de Teste | Medir o impacto antes de decidir sobre email | TRANSCRICAO | [09:37] Larissa |
| RFC-CTX-01 | docs/RFC.md | Contexto | Três clientes B2B pediram notificação de mudança de status | TRANSCRICAO | [09:00] Marcos |
| RFC-CTX-02 | docs/RFC.md | Contexto | "Tempo real" é abaixo de 10 segundos | TRANSCRICAO | [09:02] Marcos |
| RFC-CTX-03 | docs/RFC.md | Contexto | Fluxo apenas de saída | TRANSCRICAO | [09:02] Marcos |
| RFC-CTX-04 | docs/RFC.md | Contexto | Atlas quer a feature até o fim de novembro | TRANSCRICAO | [09:45] Marcos |
| RFC-CTX-05 | docs/RFC.md | Contexto | OMS modular montado em buildControllers | CODIGO | src/app.ts |
| RFC-CTX-06 | docs/RFC.md | Contexto | Máquina de estados do pedido | CODIGO | src/modules/orders/order.status.ts |
| RFC-CTX-07 | docs/RFC.md | Contexto | changeStatus em prisma.$transaction com estoque e histórico | CODIGO | src/modules/orders/order.service.ts |
| RFC-CTX-08 | docs/RFC.md | Contexto | Único segredo existente é o JWT_SECRET | CODIGO | src/config/env.ts |
| RFC-RES-01 | docs/RFC.md | Restrição | HTTP dentro da transação travaria outros pedidos | TRANSCRICAO | [09:04] Bruno |
| RFC-RES-02 | docs/RFC.md | Restrição | Time pequeno, sem nova infraestrutura | TRANSCRICAO | [09:07] Diego |
| RFC-RES-03 | docs/RFC.md | Restrição | Cliente precisa validar origem e integridade | TRANSCRICAO | [09:19] Sofia |
| RFC-RES-04 | docs/RFC.md | Restrição | Três sprints com revisão de segurança | TRANSCRICAO | [09:47] Larissa |
| RFC-DEC-01 | docs/RFC.md | Decisão | Configuração de webhooks com URL, status e secret gerada | TRANSCRICAO | [09:31] Marcos |
| RFC-DEC-02 | docs/RFC.md | Decisão | customer_id no body ou no path, não do JWT | TRANSCRICAO | [09:32] Larissa |
| RFC-DEC-03 | docs/RFC.md | Decisão | Publicação dentro da transação, rollback se falhar | TRANSCRICAO | [09:40] Bruno |
| RFC-DEC-04 | docs/RFC.md | Decisão | Payload renderizado como snapshot | TRANSCRICAO | [09:52] Larissa |
| RFC-DEC-05 | docs/RFC.md | Decisão | Worker separado com PrismaClient próprio | TRANSCRICAO | [09:30] Bruno |
| RFC-DEC-06 | docs/RFC.md | Decisão | Polling a cada 2 segundos em batch | TRANSCRICAO | [09:09] Diego |
| RFC-DEC-07 | docs/RFC.md | Decisão | Timeout de 10 segundos | TRANSCRICAO | [09:42] Diego |
| RFC-DEC-08 | docs/RFC.md | Decisão | Backoff 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Diego |
| RFC-DEC-09 | docs/RFC.md | Decisão | Dead letter em tabela separada | TRANSCRICAO | [09:18] Diego |
| RFC-DEC-10 | docs/RFC.md | Decisão | Replay por ADMIN com registro de autor | TRANSCRICAO | [09:36] Sofia |
| RFC-DEC-11 | docs/RFC.md | Decisão | Garantia de consistência entre status e evento | TRANSCRICAO | [09:06] Diego |
| RFC-DEC-12 | docs/RFC.md | Decisão | Latência de até 2 segundos aceita | TRANSCRICAO | [09:10] Larissa |
| RFC-DEC-13 | docs/RFC.md | Decisão | At-least-once com X-Event-Id | TRANSCRICAO | [09:25] Diego |
| RFC-DEC-14 | docs/RFC.md | Decisão | Ordenação por pedido com worker único | TRANSCRICAO | [09:13] Larissa |
| RFC-DEC-15 | docs/RFC.md | Decisão | HMAC-SHA256, secret por endpoint, rotação 24h | TRANSCRICAO | [09:22] Sofia |
| RFC-DEC-16 | docs/RFC.md | Contrato | Header X-Webhook-Id | TRANSCRICAO | [09:44] Sofia |
| RFC-DEC-17 | docs/RFC.md | Contrato | Header X-Timestamp para detecção de replay | TRANSCRICAO | [09:44] Diego |
| RFC-DEC-18 | docs/RFC.md | Restrição | Payload limitado a 64KB, erro sem truncar | TRANSCRICAO | [09:24] Larissa |
| RFC-INT-01 | docs/RFC.md | Integração | Erros como subclasses de AppError | CODIGO | src/shared/errors/app-error.ts |
| RFC-INT-02 | docs/RFC.md | Integração | Error middleware trata novos erros sem alteração | CODIGO | src/middlewares/error.middleware.ts |
| RFC-INT-03 | docs/RFC.md | Integração | Logger Pino reaproveitado | CODIGO | src/shared/logger/index.ts |
| RFC-INT-04 | docs/RFC.md | Integração | authenticate e requireRole reaproveitados | CODIGO | src/middlewares/auth.middleware.ts |
| RFC-INT-05 | docs/RFC.md | Decisão | CRUD aberto a qualquer papel autenticado | TRANSCRICAO | [09:37] Sofia |
| RFC-INT-06 | docs/RFC.md | Decisão | Chaves primárias UUID | TRANSCRICAO | [09:51] Larissa |
| RFC-ALT-01 | docs/RFC.md | Alternativa Descartada | Disparo HTTP síncrono em changeStatus | TRANSCRICAO | [09:04] Bruno |
| RFC-ALT-02 | docs/RFC.md | Alternativa Descartada | Redis Streams ou fila equivalente | TRANSCRICAO | [09:07] Diego |
| RFC-ALT-03 | docs/RFC.md | Alternativa Descartada | Trigger do MySQL para acordar o worker | TRANSCRICAO | [09:09] Diego |
| RFC-ALT-04 | docs/RFC.md | Alternativa Descartada | Worker dentro do processo da API | TRANSCRICAO | [09:11] Diego |
| RFC-ALT-05 | docs/RFC.md | Alternativa Descartada | Retry indefinido ou apenas 3 tentativas | TRANSCRICAO | [09:16] Diego |
| RFC-ALT-06 | docs/RFC.md | Alternativa Descartada | Secret global da plataforma | TRANSCRICAO | [09:21] Sofia |
| RFC-ALT-07 | docs/RFC.md | Alternativa Descartada | Exactly-once | TRANSCRICAO | [09:25] Diego |
| RFC-ALT-08 | docs/RFC.md | Alternativa Descartada | Renderizar o payload só no envio | TRANSCRICAO | [09:52] Larissa |
| RFC-QA-01 | docs/RFC.md | Questão em Aberto | Total de envios: 5 ou envio inicial mais 5 retentativas | TRANSCRICAO | [09:48] Larissa |
| RFC-QA-02 | docs/RFC.md | Questão em Aberto | Rate limiting de envio por cliente | TRANSCRICAO | [09:39] Larissa |
| RFC-QA-03 | docs/RFC.md | Questão em Aberto | Escala para mais de um worker | TRANSCRICAO | [09:13] Diego |
| RFC-QA-04 | docs/RFC.md | Questão em Aberto | Restrição de papéis no CRUD | TRANSCRICAO | [09:37] Sofia |
| RFC-QA-05 | docs/RFC.md | Questão em Aberto | Retenção e arquivamento da outbox | TRANSCRICAO | [09:08] Diego |
| RFC-QA-06 | docs/RFC.md | Questão em Aberto | Qual secret assina durante as 24h de rotação | TRANSCRICAO | [09:21] Sofia |
| RFC-QA-07 | docs/RFC.md | Questão em Aberto | Replay da DLQ preserva o event_id | TRANSCRICAO | [09:18] Diego |
| RFC-QA-08 | docs/RFC.md | Questão em Aberto | Eventos do mesmo pedido enquanto um anterior está em retry | TRANSCRICAO | [09:12] Diego |
| RFC-QA-09 | docs/RFC.md | Questão em Aberto | OrderService.create gera evento | CODIGO | src/modules/orders/order.service.ts |
| RFC-QA-10 | docs/RFC.md | Questão em Aberto | URL http no Zod vira VALIDATION_ERROR, nunca WEBHOOK_INVALID_URL | CODIGO | src/middlewares/validate.middleware.ts |
| RFC-ESC-01 | docs/RFC.md | Fora de Escopo | Notificação por email adiada | TRANSCRICAO | [09:37] Larissa |
| RFC-ESC-02 | docs/RFC.md | Fora de Escopo | Dashboard visual é projeto do frontend | TRANSCRICAO | [09:40] Larissa |
| RFC-INT-07 | docs/RFC.md | Integração | changeStatus passa a chamar a publicação de evento | TRANSCRICAO | [09:41] Bruno |
| RFC-INT-08 | docs/RFC.md | Integração | Novas tabelas no schema Prisma | CODIGO | prisma/schema.prisma |
| RFC-INT-09 | docs/RFC.md | Integração | Registro do novo módulo e rotas | CODIGO | src/routes/index.ts |
| RFC-INT-10 | docs/RFC.md | Integração | Novo script npm run worker | TRANSCRICAO | [09:11] Larissa |
| RFC-INT-11 | docs/RFC.md | Integração | Histórico de entregas pedido, forma de armazenamento não definida | TRANSCRICAO | [09:34] Marcos |
| RFC-RISCO-01 | docs/RFC.md | Risco | Falha na outbox faz a mudança de status falhar | TRANSCRICAO | [09:40] Bruno |
| RFC-RISCO-02 | docs/RFC.md | Risco | Cliente processa evento duas vezes; mitigação no portal | TRANSCRICAO | [09:26] Marcos |
| RFC-RISCO-03 | docs/RFC.md | Risco | Cliente fora do ar mais de 15 horas sem aviso | TRANSCRICAO | [09:37] Larissa |
| RFC-RISCO-04 | docs/RFC.md | Risco | Vazamento de secret, inclusive em logs | TRANSCRICAO | [09:22] Diego |
| RFC-RISCO-05 | docs/RFC.md | Risco | Rajada de eventos para um mesmo cliente | TRANSCRICAO | [09:38] Diego |
| RFC-RISCO-06 | docs/RFC.md | Risco | Worker único parado acumula eventos | TRANSCRICAO | [09:11] Diego |
| RFC-RISCO-07 | docs/RFC.md | Risco | Prazo de três sprints contra pedido da Atlas | TRANSCRICAO | [09:00] Marcos |
| FDD-CTX-01 | docs/FDD.md | Contexto | changeStatus em transação única, sem mecanismo de evento | CODIGO | src/modules/orders/order.service.ts |
| FDD-CTX-02 | docs/FDD.md | Contexto | Clientes fazem polling em GET /orders | TRANSCRICAO | [09:00] Marcos |
| FDD-CTX-03 | docs/FDD.md | Contexto | Webhooks de saída | TRANSCRICAO | [09:03] Sofia |
| FDD-CTX-04 | docs/FDD.md | Contexto | Usuários do OMS representam o cliente | TRANSCRICAO | [09:32] Marcos |
| FDD-RES-01 | docs/FDD.md | Restrição | Mesmo MySQL, sem infraestrutura nova | TRANSCRICAO | [09:07] Diego |
| FDD-RES-02 | docs/FDD.md | Restrição | Um único worker | TRANSCRICAO | [09:12] Diego |
| FDD-RES-03 | docs/FDD.md | Restrição | Revisão de segurança antes do deploy | TRANSCRICAO | [09:46] Sofia |
| FDD-RES-04 | docs/FDD.md | Responsável | Larissa abre e revisa o doc de design | TRANSCRICAO | [09:50] Larissa |
| FDD-OBJ-01 | docs/FDD.md | Objetivo Técnico | Evento disponível em até 2 segundos | TRANSCRICAO | [09:10] Larissa |
| FDD-OBJ-02 | docs/FDD.md | Objetivo Técnico | Atomicidade entre status e outbox | TRANSCRICAO | [09:06] Diego |
| FDD-OBJ-03 | docs/FDD.md | Objetivo Técnico | Entrega at-least-once | TRANSCRICAO | [09:24] Diego |
| FDD-OBJ-04 | docs/FDD.md | Objetivo Técnico | Ordenação por order_id com worker único | TRANSCRICAO | [09:12] Diego |
| FDD-OBJ-05 | docs/FDD.md | Objetivo Técnico | 100% assinadas e nenhuma URL http | TRANSCRICAO | [09:23] Sofia |
| FDD-OBJ-06 | docs/FDD.md | Objetivo Técnico | Timeout de 10 segundos por envio | TRANSCRICAO | [09:42] Diego |
| FDD-ESC-01 | docs/FDD.md | Escopo | Tabelas de outbox e dead letter | TRANSCRICAO | [09:06] Diego |
| FDD-ESC-02 | docs/FDD.md | Escopo | Publicação com filtro por status na inserção | TRANSCRICAO | [09:34] Bruno |
| FDD-ESC-03 | docs/FDD.md | Escopo | Worker, assinatura, timeout, retry e DLQ | TRANSCRICAO | [09:48] Larissa |
| FDD-ESC-04 | docs/FDD.md | Escopo | Endpoints de CRUD | TRANSCRICAO | [09:33] Bruno |
| FDD-ESC-05 | docs/FDD.md | Escopo | Headers X-Event-Id, X-Signature, X-Timestamp, Content-Type | TRANSCRICAO | [09:44] Diego |
| FDD-ESC-06 | docs/FDD.md | Fora de Escopo | Email adiado | TRANSCRICAO | [09:37] Larissa |
| FDD-ESC-07 | docs/FDD.md | Fora de Escopo | Rate limiting adiado | TRANSCRICAO | [09:39] Larissa |
| FDD-ESC-08 | docs/FDD.md | Fora de Escopo | Dashboard visual | TRANSCRICAO | [09:40] Larissa |
| FDD-ESC-09 | docs/FDD.md | Fora de Escopo | Múltiplos workers e particionamento | TRANSCRICAO | [09:13] Diego |
| FDD-ESC-10 | docs/FDD.md | Fora de Escopo | Arquivamento após 30 dias | TRANSCRICAO | [09:08] Diego |
| FDD-ESC-11 | docs/FDD.md | Fora de Escopo | Webhooks de entrada | TRANSCRICAO | [09:02] Marcos |
| FDD-ESC-12 | docs/FDD.md | Fora de Escopo | Restrição de papéis no CRUD | TRANSCRICAO | [09:37] Sofia |
| FDD-DADO-01 | docs/FDD.md | Modelo de Dados | Tabela webhook_outbox | TRANSCRICAO | [09:06] Diego |
| FDD-DADO-02 | docs/FDD.md | Modelo de Dados | PK UUID Char(36) e @@map snake_case (hipótese) | CODIGO | prisma/schema.prisma |
| FDD-DADO-03 | docs/FDD.md | Modelo de Dados | Configuração com url, secret, customer_id e ativo | TRANSCRICAO | [09:21] Bruno |
| FDD-DADO-04 | docs/FDD.md | Modelo de Dados | Índices em status e created_at da outbox | TRANSCRICAO | [09:08] Diego |
| FDD-DADO-05 | docs/FDD.md | Modelo de Dados | webhook_dead_letter com payload, motivo e timestamp | TRANSCRICAO | [09:18] Diego |
| FDD-DADO-06 | docs/FDD.md | Modelo de Dados | Entregas com sucesso, payload, resposta e tempo | TRANSCRICAO | [09:34] Marcos |
| FDD-DADO-07 | docs/FDD.md | Modelo de Dados | Estados pendente, processando, falhou, entregue | TRANSCRICAO | [09:08] Diego |
| FDD-DADO-08 | docs/FDD.md | Modelo de Dados | Colunas de auditoria do replay | TRANSCRICAO | [09:36] Sofia |
| FDD-FLUXO-01 | docs/FDD.md | Fluxo | Passos atuais de changeStatus mantidos | CODIGO | src/modules/orders/order.service.ts |
| FDD-FLUXO-02 | docs/FDD.md | Fluxo | publishWebhookEvent(tx, order, from, to) após o histórico | TRANSCRICAO | [09:41] Bruno |
| FDD-FLUXO-03 | docs/FDD.md | Fluxo | Filtro de endpoints ativos do customer pelo status | TRANSCRICAO | [09:34] Bruno |
| FDD-FLUXO-04 | docs/FDD.md | Fluxo | Sem interessado, nenhuma linha | TRANSCRICAO | [09:34] Diego |
| FDD-FLUXO-05 | docs/FDD.md | Fluxo | eventId UUID v4 com o pacote uuid (hipótese) | CODIGO | src/middlewares/request-logger.middleware.ts |
| FDD-FLUXO-06 | docs/FDD.md | Fluxo | Payload snapshot na inserção | TRANSCRICAO | [09:52] Larissa |
| FDD-FLUXO-07 | docs/FDD.md | Fluxo | Payload acima de 64KB gera erro | TRANSCRICAO | [09:24] Larissa |
| FDD-FLUXO-08 | docs/FDD.md | Fluxo | Falha no insert faz rollback de tudo | TRANSCRICAO | [09:40] Bruno |
| FDD-FLUXO-09 | docs/FDD.md | Fluxo | Resposta de PATCH /orders/:id/status inalterada | CODIGO | src/modules/orders/order.routes.ts |
| FDD-FLUXO-10 | docs/FDD.md | Fluxo | Worker em src/worker.ts (novo) com npm run worker | TRANSCRICAO | [09:11] Larissa |
| FDD-FLUXO-11 | docs/FDD.md | Fluxo | Lógica em webhook.worker.ts ou webhook.processor.ts | TRANSCRICAO | [09:28] Bruno |
| FDD-FLUXO-12 | docs/FDD.md | Fluxo | PrismaClient próprio no worker | TRANSCRICAO | [09:30] Bruno |
| FDD-FLUXO-13 | docs/FDD.md | Fluxo | Polling a cada 2 segundos dos pendentes mais antigos | TRANSCRICAO | [09:09] Diego |
| FDD-FLUXO-14 | docs/FDD.md | Fluxo | Processamento em ordem de createdAt | TRANSCRICAO | [09:12] Diego |
| FDD-FLUXO-15 | docs/FDD.md | Fluxo | POST com timeout de 10 segundos | TRANSCRICAO | [09:42] Diego |
| FDD-FLUXO-25 | docs/FDD.md | Fluxo | fetch nativo do Node 20, sem dependência nova (hipótese) | CODIGO | package.json |
| FDD-FLUXO-16 | docs/FDD.md | Fluxo | Cada tentativa registrada no histórico de entregas | TRANSCRICAO | [09:34] Marcos |
| FDD-FLUXO-17 | docs/FDD.md | Fluxo | Sucesso marca o evento como entregue | TRANSCRICAO | [09:08] Diego |
| FDD-FLUXO-18 | docs/FDD.md | Fluxo | Encerramento em SIGINT/SIGTERM com $disconnect (hipótese) | CODIGO | src/server.ts |
| FDD-FLUXO-19 | docs/FDD.md | Fluxo | Reagendamento pelos intervalos 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Diego |
| FDD-FLUXO-20 | docs/FDD.md | Fluxo | Retry esgotado: dead letter com payload e motivo | TRANSCRICAO | [09:18] Diego |
| FDD-FLUXO-24 | docs/FDD.md | Fluxo | Entrada na DLQ numa única transação (hipótese) | CODIGO | src/modules/orders/order.service.ts |
| FDD-FLUXO-21 | docs/FDD.md | Fluxo | Replay exige ADMIN | TRANSCRICAO | [09:36] Larissa |
| FDD-FLUXO-22 | docs/FDD.md | Fluxo | Replay recoloca o evento na outbox como pendente | TRANSCRICAO | [09:18] Diego |
| FDD-FLUXO-23 | docs/FDD.md | Fluxo | Replay logado com o autor para auditoria | TRANSCRICAO | [09:36] Sofia |
| FDD-QA-01 | docs/FDD.md | Questão em Aberto | Envio inicial mais 5 retentativas, pendente de confirmação | TRANSCRICAO | [09:48] Larissa |
| FDD-CONTRATO-01 | docs/FDD.md | Contrato | Rotas sob /api/v1 com authenticate no router (hipótese) | CODIGO | src/routes/index.ts |
| FDD-CONTRATO-02 | docs/FDD.md | Contrato | Corpo da API em camelCase (hipótese) | CODIGO | src/modules/customers/customer.schemas.ts |
| FDD-CONTRATO-03 | docs/FDD.md | Contrato | Envelope de paginação data e pagination | CODIGO | src/shared/http/response.ts |
| FDD-CONTRATO-04 | docs/FDD.md | Contrato | Formato de erro code, message, details | CODIGO | src/middlewares/error.middleware.ts |
| FDD-CONTRATO-05 | docs/FDD.md | Contrato | customerId no body ou path | TRANSCRICAO | [09:32] Larissa |
| FDD-CONTRATO-06 | docs/FDD.md | Contrato | Payload ao cliente em snake_case | TRANSCRICAO | [09:43] Diego |
| FDD-CONTRATO-07 | docs/FDD.md | Contrato | POST /api/v1/webhooks devolve a secret gerada | TRANSCRICAO | [09:31] Marcos |
| FDD-CONTRATO-08 | docs/FDD.md | Contrato | events é a lista de status de interesse | TRANSCRICAO | [09:33] Marcos |
| FDD-CONTRATO-09 | docs/FDD.md | Contrato | GET de webhooks de um customer | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-10 | docs/FDD.md | Contrato | PATCH /api/v1/webhooks/:id | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-11 | docs/FDD.md | Contrato | Body parcial no padrão de updateCustomerSchema (hipótese) | CODIGO | src/modules/customers/customer.schemas.ts |
| FDD-CONTRATO-12 | docs/FDD.md | Contrato | DELETE /api/v1/webhooks/:id | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-13 | docs/FDD.md | Contrato | Remoção com onDelete Cascade como OrderItem (hipótese) | CODIGO | prisma/schema.prisma |
| FDD-CONTRATO-14 | docs/FDD.md | Contrato | DELETE responde 204 sem corpo (hipótese) | CODIGO | src/modules/customers/customer.controller.ts |
| FDD-CONTRATO-15 | docs/FDD.md | Contrato | Rotação de secret pela API | TRANSCRICAO | [09:21] Sofia |
| FDD-CONTRATO-16 | docs/FDD.md | Contrato | GET /api/v1/webhooks/:id/deliveries | TRANSCRICAO | [09:34] Marcos |
| FDD-CONTRATO-17 | docs/FDD.md | Contrato | pageSize máximo 100 como listOrdersQuerySchema (hipótese) | CODIGO | src/modules/orders/order.schemas.ts |
| FDD-CONTRATO-18 | docs/FDD.md | Contrato | POST /admin/webhooks/dead-letter/:id/replay | TRANSCRICAO | [09:35] Diego |
| FDD-CONTRATO-19 | docs/FDD.md | Contrato | requireRole('ADMIN') por rota como user.routes.ts | CODIGO | src/modules/users/user.routes.ts |
| FDD-CONTRATO-20 | docs/FDD.md | Contrato | reason até 500 caracteres (hipótese) | CODIGO | src/modules/orders/order.schemas.ts |
| FDD-CONTRATO-21 | docs/FDD.md | Contrato | Entrega por POST https com timeout de 10s | TRANSCRICAO | [09:42] Diego |
| FDD-CONTRATO-22 | docs/FDD.md | Contrato | Headers X-Event-Id, X-Signature, X-Timestamp, Content-Type | TRANSCRICAO | [09:44] Diego |
| FDD-CONTRATO-23 | docs/FDD.md | Contrato | Header X-Webhook-Id | TRANSCRICAO | [09:44] Sofia |
| FDD-CONTRATO-24 | docs/FDD.md | Contrato | HMAC-SHA256 sobre o corpo do request | TRANSCRICAO | [09:22] Sofia |
| FDD-CONTRATO-25 | docs/FDD.md | Contrato | Cliente deduplica por X-Event-Id | TRANSCRICAO | [09:25] Diego |
| FDD-CONTRATO-26 | docs/FDD.md | Contrato | Campos do payload, sem itens | TRANSCRICAO | [09:43] Diego |
| FDD-CONTRATO-27 | docs/FDD.md | Contrato | order_number no formato ORD-NNNNNN | CODIGO | src/modules/orders/order.service.ts |
| FDD-ERRO-01 | docs/FDD.md | Erro | Subclasses de AppError como InvalidStatusTransitionError | CODIGO | src/shared/errors/http-errors.ts |
| FDD-ERRO-02 | docs/FDD.md | Erro | Prefixo WEBHOOK_ em todo o módulo | TRANSCRICAO | [09:29] Larissa |
| FDD-ERRO-03 | docs/FDD.md | Erro | WEBHOOK_NOT_FOUND | TRANSCRICAO | [09:28] Bruno |
| FDD-ERRO-04 | docs/FDD.md | Erro | WEBHOOK_CUSTOMER_NOT_FOUND (hipótese) | CODIGO | src/shared/errors/http-errors.ts |
| FDD-ERRO-05 | docs/FDD.md | Erro | WEBHOOK_INVALID_URL via schema Zod | TRANSCRICAO | [09:23] Sofia |
| FDD-ERRO-06 | docs/FDD.md | Erro | WEBHOOK_ROTATION_IN_PROGRESS como ConflictError (hipótese) | CODIGO | src/shared/errors/http-errors.ts |
| FDD-ERRO-07 | docs/FDD.md | Erro | WEBHOOK_DEAD_LETTER_NOT_FOUND (hipótese) | CODIGO | src/shared/errors/app-error.ts |
| FDD-ERRO-08 | docs/FDD.md | Erro | WEBHOOK_DEAD_LETTER_ALREADY_REPLAYED como ConflictError (hipótese) | CODIGO | src/shared/errors/http-errors.ts |
| FDD-ERRO-09 | docs/FDD.md | Erro | WEBHOOK_PAYLOAD_TOO_LARGE acima de 64KB | TRANSCRICAO | [09:24] Larissa |
| FDD-ERRO-10 | docs/FDD.md | Erro | Reuso de VALIDATION_ERROR para falhas de Zod | CODIGO | src/middlewares/validate.middleware.ts |
| FDD-ERRO-11 | docs/FDD.md | Erro | WEBHOOK_DELIVERY_TIMEOUT após 10s | TRANSCRICAO | [09:42] Diego |
| FDD-ERRO-12 | docs/FDD.md | Erro | WEBHOOK_SECRET_REQUIRED | TRANSCRICAO | [09:28] Bruno |
| FDD-ERRO-13 | docs/FDD.md | Erro | WEBHOOK_RETRIES_EXHAUSTED: falha permanente vai para DLQ | TRANSCRICAO | [09:15] Diego |
| FDD-RESIL-01 | docs/FDD.md | Resiliência | Timeout de 10 segundos | TRANSCRICAO | [09:42] Diego |
| FDD-RESIL-02 | docs/FDD.md | Resiliência | Tabela de backoff 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Diego |
| FDD-RESIL-03 | docs/FDD.md | Resiliência | Reprocessamento manual da DLQ | TRANSCRICAO | [09:18] Diego |
| FDD-RESIL-04 | docs/FDD.md | Resiliência | Sem aviso automático ao cliente, email adiado | TRANSCRICAO | [09:37] Larissa |
| FDD-RESIL-05 | docs/FDD.md | Resiliência | Cliente reconcilia por GET /orders/:id | TRANSCRICAO | [09:43] Diego |
| FDD-RESIL-06 | docs/FDD.md | Resiliência | Restart da API não afeta o worker | TRANSCRICAO | [09:11] Diego |
| FDD-RESIL-07 | docs/FDD.md | Resiliência | Apenas uma instância de worker | TRANSCRICAO | [09:12] Diego |
| FDD-RESIL-08 | docs/FDD.md | Resiliência | Invariante: nenhum status sem outbox | TRANSCRICAO | [09:40] Bruno |
| FDD-RESIL-09 | docs/FDD.md | Parâmetro | WEBHOOK_POLL_INTERVAL_MS = 2000 | TRANSCRICAO | [09:09] Diego |
| FDD-RESIL-10 | docs/FDD.md | Parâmetro | WEBHOOK_BATCH_SIZE para "batch pequeno" (valor 10 é hipótese) | TRANSCRICAO | [09:08] Diego |
| FDD-RESIL-11 | docs/FDD.md | Parâmetro | WEBHOOK_HTTP_TIMEOUT_MS = 10000 | TRANSCRICAO | [09:42] Diego |
| FDD-RESIL-12 | docs/FDD.md | Parâmetro | WEBHOOK_MAX_PAYLOAD_BYTES = 65536 | TRANSCRICAO | [09:24] Larissa |
| FDD-RESIL-13 | docs/FDD.md | Parâmetro | WEBHOOK_MAX_RETRIES = 5, ligado à questão em aberto | TRANSCRICAO | [09:48] Larissa |
| FDD-RESIL-14 | docs/FDD.md | Parâmetro | WEBHOOK_SECRET_GRACE_PERIOD_HOURS = 24 | TRANSCRICAO | [09:21] Sofia |
| FDD-RESIL-15 | docs/FDD.md | Parâmetro | Variáveis validadas no schema Zod de env (hipótese) | CODIGO | src/config/env.ts |
| FDD-OBS-01 | docs/FDD.md | Observabilidade | Projeto sem biblioteca de métricas ou tracing | CODIGO | package.json |
| FDD-OBS-02 | docs/FDD.md | Observabilidade | Nada novo de logging além do Pino | TRANSCRICAO | [09:29] Bruno |
| FDD-OBS-03 | docs/FDD.md | Métrica | Latência ponta a ponta valida a meta abaixo de 10s | TRANSCRICAO | [09:02] Marcos |
| FDD-OBS-04 | docs/FDD.md | Observabilidade | Logs JSON do Pino com service, env e time | CODIGO | src/shared/logger/index.ts |
| FDD-OBS-05 | docs/FDD.md | Observabilidade | Log de auditoria do replay | TRANSCRICAO | [09:36] Sofia |
| FDD-OBS-06 | docs/FDD.md | Observabilidade | Secrets não podem ser logadas | TRANSCRICAO | [09:22] Diego |
| FDD-OBS-07 | docs/FDD.md | Observabilidade | requestId do request logger na correlação | CODIGO | src/middlewares/request-logger.middleware.ts |
| FDD-OBS-08 | docs/FDD.md | Observabilidade | Assinatura de publishWebhookEvent sem contexto da requisição | TRANSCRICAO | [09:41] Bruno |
| FDD-INT-01 | docs/FDD.md | Integração | changeStatus estendido com publishWebhookEvent | CODIGO | src/modules/orders/order.service.ts |
| FDD-INT-02 | docs/FDD.md | Integração | OrderStatus reusado como domínio de events | CODIGO | src/modules/orders/order.status.ts |
| FDD-INT-03 | docs/FDD.md | Integração | AppError reusado pelos erros do módulo | CODIGO | src/shared/errors/app-error.ts |
| FDD-INT-04 | docs/FDD.md | Integração | Error middleware sem mudança | CODIGO | src/middlewares/error.middleware.ts |
| FDD-INT-05 | docs/FDD.md | Integração | authenticate e requireRole reusados | CODIGO | src/middlewares/auth.middleware.ts |
| FDD-INT-06 | docs/FDD.md | Integração | validate reusado nas rotas novas | CODIGO | src/middlewares/validate.middleware.ts |
| FDD-INT-07 | docs/FDD.md | Integração | buildControllers instancia o módulo | CODIGO | src/app.ts |
| FDD-INT-08 | docs/FDD.md | Integração | buildApiRouter monta /webhooks e /admin/webhooks | CODIGO | src/routes/index.ts |
| FDD-INT-09 | docs/FDD.md | Integração | paginated reusado nas listagens | CODIGO | src/shared/http/response.ts |
| FDD-INT-10 | docs/FDD.md | Integração | redactPaths estendido com campos de secret | CODIGO | src/shared/logger/index.ts |
| FDD-INT-11 | docs/FDD.md | Integração | Schema de env estendido com WEBHOOK_* | CODIGO | src/config/env.ts |
| FDD-INT-12 | docs/FDD.md | Integração | Worker usa createPrismaClient | CODIGO | src/config/database.ts |
| FDD-INT-13 | docs/FDD.md | Integração | server.ts é o modelo do entry-point do worker | CODIGO | src/server.ts |
| FDD-INT-14 | docs/FDD.md | Integração | Novos modelos e relação inversa em Customer | CODIGO | prisma/schema.prisma |
| FDD-INT-15 | docs/FDD.md | Integração | Scripts worker e dev:worker no padrão de start e dev (hipótese) | CODIGO | package.json |
| FDD-INT-16 | docs/FDD.md | Integração | Limpeza das novas tabelas antes de cada teste | CODIGO | tests/setup.ts |
| FDD-INT-17 | docs/FDD.md | Integração | Factory de webhook endpoint (hipótese) | CODIGO | tests/helpers/factories.ts |
| FDD-INT-18 | docs/FDD.md | Integração | Módulo src/modules/webhooks (novo) com as camadas do projeto | TRANSCRICAO | [09:27] Bruno |
| FDD-INT-19 | docs/FDD.md | Integração | Entry-point src/worker.ts (novo) | TRANSCRICAO | [09:28] Bruno |
| FDD-INT-20 | docs/FDD.md | Integração | Novas tabelas de outbox e dead letter | TRANSCRICAO | [09:18] Diego |
| FDD-DEP-01 | docs/FDD.md | Dependência | Node.js 20 com fetch e crypto nativos | CODIGO | package.json |
| FDD-DEP-02 | docs/FDD.md | Dependência | MySQL 8.0 | CODIGO | docker-compose.yml |
| FDD-DEP-03 | docs/FDD.md | Dependência | Prisma 5.22.0 | CODIGO | package.json |
| FDD-DEP-04 | docs/FDD.md | Dependência | Express, Zod, Pino e uuid nas versões atuais | CODIGO | package.json |
| FDD-DEP-05 | docs/FDD.md | Compatibilidade | Rotas novas em prefixos próprios, routers atuais inalterados (hipótese) | CODIGO | src/routes/index.ts |
| FDD-DEP-06 | docs/FDD.md | Compatibilidade | PATCH de status pode falhar se a gravação da outbox falhar | TRANSCRICAO | [09:40] Bruno |
| FDD-DEP-07 | docs/FDD.md | Compatibilidade | Migração só cria tabelas; relação inversa sem coluna como Customer.orders (hipótese) | CODIGO | prisma/schema.prisma |
| FDD-DEP-08 | docs/FDD.md | Compatibilidade | Sem webhooks cadastrados, uma consulta a mais e nenhum insert | TRANSCRICAO | [09:34] Bruno |
| FDD-DEP-09 | docs/FDD.md | Compatibilidade | Worker implantável depois da API, eventos acumulam na outbox (hipótese) | TRANSCRICAO | [09:06] Diego |
| FDD-CA-01 | docs/FDD.md | Critério de Aceite | Uma linha PENDING por endpoint interessado na mesma transação | TRANSCRICAO | [09:40] Bruno |
| FDD-CA-02 | docs/FDD.md | Critério de Aceite | Status não escutado não cria linha | TRANSCRICAO | [09:34] Bruno |
| FDD-CA-03 | docs/FDD.md | Critério de Aceite | Falha no insert faz rollback completo | TRANSCRICAO | [09:41] Diego |
| FDD-CA-04 | docs/FDD.md | Critério de Aceite | Payload gravado não muda com o pedido | TRANSCRICAO | [09:52] Larissa |
| FDD-CA-05 | docs/FDD.md | Critério de Aceite | URL http retorna 400 | TRANSCRICAO | [09:23] Sofia |
| FDD-CA-06 | docs/FDD.md | Critério de Aceite | Replay: OPERATOR 403, ADMIN 202 | TRANSCRICAO | [09:36] Larissa |
| FDD-CA-07 | docs/FDD.md | Critério de Aceite | p95 de entrega abaixo de 10s | TRANSCRICAO | [09:02] Marcos |
| FDD-CA-08 | docs/FDD.md | Critério de Aceite | Polling a cada 2s | TRANSCRICAO | [09:09] Diego |
| FDD-CA-09 | docs/FDD.md | Critério de Aceite | Timeout gera reagendamento em 1 minuto | TRANSCRICAO | [09:42] Diego |
| FDD-CA-10 | docs/FDD.md | Critério de Aceite | Sequência de backoff termina na dead letter | TRANSCRICAO | [09:17] Diego |
| FDD-CA-11 | docs/FDD.md | Critério de Aceite | Crash e restart sem perda de eventos | TRANSCRICAO | [09:24] Diego |
| FDD-CA-12 | docs/FDD.md | Critério de Aceite | Eventos do mesmo pedido em ordem | TRANSCRICAO | [09:12] Diego |
| FDD-CA-13 | docs/FDD.md | Critério de Aceite | X-Signature confere com HMAC-SHA256 do corpo | TRANSCRICAO | [09:20] Sofia |
| FDD-CA-14 | docs/FDD.md | Critério de Aceite | Secrets ausentes dos logs | TRANSCRICAO | [09:22] Diego |
| FDD-CA-15 | docs/FDD.md | Critério de Aceite | Revisão de segurança concluída | TRANSCRICAO | [09:46] Sofia |
| FDD-CA-16 | docs/FDD.md | Critério de Aceite | Cada tentativa gera log e linha de entrega | TRANSCRICAO | [09:34] Marcos |
| FDD-CA-17 | docs/FDD.md | Critério de Aceite | Replay gera log com userId | TRANSCRICAO | [09:36] Sofia |
| FDD-RISCO-01 | docs/FDD.md | Risco | Falha na outbox bloqueia mudanças de status | TRANSCRICAO | [09:40] Bruno |
| FDD-RISCO-02 | docs/FDD.md | Risco | Entrega duplicada processada duas vezes | TRANSCRICAO | [09:25] Sofia |
| FDD-RISCO-03 | docs/FDD.md | Risco | Vazamento de secret | TRANSCRICAO | [09:22] Diego |
| FDD-RISCO-04 | docs/FDD.md | Risco | Worker único parado ou lento | TRANSCRICAO | [09:11] Diego |
| FDD-RISCO-05 | docs/FDD.md | Risco | Payload acima de 64KB bloqueia mudança de status | TRANSCRICAO | [09:24] Diego |
| FDD-RISCO-06 | docs/FDD.md | Risco | Rajada de eventos para um cliente | TRANSCRICAO | [09:38] Diego |
| FDD-RISCO-07 | docs/FDD.md | Risco | Interpretação errada do número de tentativas | TRANSCRICAO | [09:48] Larissa |
| ADR-001 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Outbox no MySQL na transação de changeStatus | TRANSCRICAO | [09:08] Larissa |
| ADR-001-CTX-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Contexto | changeStatus valida, mexe no estoque, atualiza orders e histórico | CODIGO | src/modules/orders/order.service.ts |
| ADR-001-DEC-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Falha na outbox faz rollback da mudança de status | TRANSCRICAO | [09:40] Bruno |
| ADR-001-DEC-02 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | publishWebhookEvent recebe o tx | TRANSCRICAO | [09:41] Bruno |
| ADR-001-DEC-03 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Filtro de interessados na inserção | TRANSCRICAO | [09:34] Bruno |
| ADR-001-DEC-04 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Índices em status e created_at | TRANSCRICAO | [09:08] Diego |
| ADR-001-DEC-05 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Chave primária UUID | TRANSCRICAO | [09:51] Larissa |
| ADR-001-ALT-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Alternativa Descartada | Chamada HTTP síncrona | TRANSCRICAO | [09:04] Bruno |
| ADR-001-ALT-02 | docs/adrs/ADR-001-outbox-no-mysql.md | Alternativa Descartada | Redis Streams | TRANSCRICAO | [09:07] Diego |
| ADR-001-ALT-03 | docs/adrs/ADR-001-outbox-no-mysql.md | Alternativa Descartada | Evento fora da transação | TRANSCRICAO | [09:41] Diego |
| ADR-001-QA-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Questão em Aberto | Criação do pedido gera evento | CODIGO | src/modules/orders/order.service.ts |
| ADR-001-QA-02 | docs/adrs/ADR-001-outbox-no-mysql.md | Questão em Aberto | Retenção de linhas entregues | TRANSCRICAO | [09:08] Diego |
| ADR-001-TO-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Trade-off | Custo na transação em troca de consistência sem nova infra | TRANSCRICAO | [09:07] Diego |
| ADR-002 | docs/adrs/ADR-002-worker-separado-com-polling.md | Decisão | Worker em polling de 2 segundos | TRANSCRICAO | [09:10] Larissa |
| ADR-002-CTX-01 | docs/adrs/ADR-002-worker-separado-com-polling.md | Contexto | Único entry-point atual com tratamento de sinais | CODIGO | src/server.ts |
| ADR-002-DEC-01 | docs/adrs/ADR-002-worker-separado-com-polling.md | Decisão | Processo separado da API | TRANSCRICAO | [09:11] Diego |
| ADR-002-DEC-02 | docs/adrs/ADR-002-worker-separado-com-polling.md | Decisão | src/worker.ts (novo) e npm run worker | TRANSCRICAO | [09:11] Larissa |
| ADR-002-DEC-03 | docs/adrs/ADR-002-worker-separado-com-polling.md | Decisão | PrismaClient próprio, mesma DATABASE_URL | TRANSCRICAO | [09:30] Bruno |
| ADR-002-DEC-04 | docs/adrs/ADR-002-worker-separado-com-polling.md | Decisão | Worker único com ordenação por order_id | TRANSCRICAO | [09:12] Diego |
| ADR-002-ALT-01 | docs/adrs/ADR-002-worker-separado-com-polling.md | Alternativa Descartada | Trigger do MySQL | TRANSCRICAO | [09:09] Diego |
| ADR-002-ALT-02 | docs/adrs/ADR-002-worker-separado-com-polling.md | Alternativa Descartada | Worker dentro da API | TRANSCRICAO | [09:11] Diego |
| ADR-002-ALT-03 | docs/adrs/ADR-002-worker-separado-com-polling.md | Alternativa Descartada | Vários workers em paralelo | TRANSCRICAO | [09:13] Diego |
| ADR-002-QA-01 | docs/adrs/ADR-002-worker-separado-com-polling.md | Questão em Aberto | Tamanho do batch | TRANSCRICAO | [09:08] Diego |
| ADR-002-QA-02 | docs/adrs/ADR-002-worker-separado-com-polling.md | Questão em Aberto | Ordem enquanto um evento aguarda retry | TRANSCRICAO | [09:12] Diego |
| ADR-002-TO-01 | docs/adrs/ADR-002-worker-separado-com-polling.md | Trade-off | 2 segundos de latência aceitos | TRANSCRICAO | [09:10] Marcos |
| ADR-003 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Decisão | Backoff 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Larissa |
| ADR-003-CTX-01 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Contexto | requireRole com papéis ADMIN e OPERATOR | CODIGO | src/middlewares/auth.middleware.ts |
| ADR-003-DEC-01 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Decisão | Timeout de 10 segundos conta como falha | TRANSCRICAO | [09:42] Diego |
| ADR-003-DEC-02 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Decisão | Tabela webhook_dead_letter separada | TRANSCRICAO | [09:18] Diego |
| ADR-003-DEC-03 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Decisão | Endpoint de replay manual | TRANSCRICAO | [09:35] Diego |
| ADR-003-DEC-04 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Decisão | Replay exige ADMIN e registro do autor | TRANSCRICAO | [09:36] Sofia |
| ADR-003-ALT-01 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Alternativa Descartada | Retry indefinido | TRANSCRICAO | [09:15] Diego |
| ADR-003-ALT-02 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Alternativa Descartada | Apenas 3 tentativas | TRANSCRICAO | [09:16] Diego |
| ADR-003-ALT-03 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Alternativa Descartada | Status failed na própria outbox | TRANSCRICAO | [09:17] Larissa |
| ADR-003-QA-01 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Questão em Aberto | Total de envios ambíguo | TRANSCRICAO | [09:48] Larissa |
| ADR-003-TO-01 | docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md | Trade-off | Após 15 horas depende de replay, sem email | TRANSCRICAO | [09:37] Larissa |
| ADR-004 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Decisão | HMAC-SHA256 sobre o corpo | TRANSCRICAO | [09:22] Sofia |
| ADR-004-CTX-01 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Contexto | redactPaths do logger não cobrem secret de webhook | CODIGO | src/shared/logger/index.ts |
| ADR-004-DEC-01 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Decisão | Secret única por endpoint | TRANSCRICAO | [09:21] Sofia |
| ADR-004-DEC-02 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Decisão | Configuração com url, secret, customer_id, ativo | TRANSCRICAO | [09:21] Bruno |
| ADR-004-DEC-03 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Decisão | Secret gerada pela plataforma na criação | TRANSCRICAO | [09:31] Marcos |
| ADR-004-DEC-04 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Decisão | Header X-Timestamp | TRANSCRICAO | [09:44] Diego |
| ADR-004-DEC-05 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Restrição | TLS obrigatório via schema Zod | TRANSCRICAO | [09:23] Sofia |
| ADR-004-DEC-06 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Decisão | Revisão de segurança de dois dias úteis | TRANSCRICAO | [09:46] Sofia |
| ADR-004-ALT-01 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Alternativa Descartada | Secret global | TRANSCRICAO | [09:21] Sofia |
| ADR-004-ALT-02 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Alternativa Descartada | Secret sem rotação | TRANSCRICAO | [09:22] Diego |
| ADR-004-ALT-03 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Alternativa Descartada | Token estático sem assinatura (não discutida), não atende integridade | TRANSCRICAO | [09:19] Sofia |
| ADR-004-QA-01 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Questão em Aberto | Com qual secret assinar no grace period | TRANSCRICAO | [09:21] Sofia |
| ADR-004-QA-02 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Questão em Aberto | Armazenamento da secret, para a revisão de segurança | TRANSCRICAO | [09:46] Sofia |
| ADR-004-TO-01 | docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md | Trade-off | Gerenciar secrets por endpoint para isolar vazamentos | TRANSCRICAO | [09:22] Diego |
| ADR-005 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Decisão | Entrega at-least-once | TRANSCRICAO | [09:26] Larissa |
| ADR-005-CTX-01 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Contexto | PKs UUID no schema | CODIGO | prisma/schema.prisma |
| ADR-005-DEC-01 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Decisão | X-Event-Id UUID gerado na entrada na outbox | TRANSCRICAO | [09:25] Diego |
| ADR-005-DEC-02 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Decisão | Header X-Webhook-Id | TRANSCRICAO | [09:44] Sofia |
| ADR-005-DEC-03 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Decisão | Documentação no portal de desenvolvedor | TRANSCRICAO | [09:26] Marcos |
| ADR-005-ALT-01 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Alternativa Descartada | Exactly-once | TRANSCRICAO | [09:25] Diego |
| ADR-005-ALT-02 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Alternativa Descartada | At-most-once (não discutida), contradiz retry | TRANSCRICAO | [09:16] Diego |
| ADR-005-QA-01 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Questão em Aberto | Replay preserva o event_id | TRANSCRICAO | [09:18] Diego |
| ADR-005-TO-01 | docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md | Trade-off | Responsabilidade de deduplicação com o cliente | TRANSCRICAO | [09:25] Sofia |
| ADR-006 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Decisão | Reuso máximo dos padrões existentes | TRANSCRICAO | [09:30] Larissa |
| ADR-006-CTX-01 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Contexto | Módulos instanciados em buildControllers | CODIGO | src/app.ts |
| ADR-006-CTX-02 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Contexto | AuthUser sem vínculo com cliente | CODIGO | src/middlewares/auth.middleware.ts |
| ADR-006-DEC-01 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Decisão | Módulo src/modules/webhooks (novo) | TRANSCRICAO | [09:27] Bruno |
| ADR-006-DEC-02 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Decisão | Erros como AppError com códigos WEBHOOK_ | TRANSCRICAO | [09:28] Bruno |
| ADR-006-DEC-03 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Decisão | Prefixo WEBHOOK_ para tudo do módulo | TRANSCRICAO | [09:29] Larissa |
| ADR-006-DEC-04 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Decisão | Pino e error middleware sem mudança | TRANSCRICAO | [09:29] Bruno |
| ADR-006-DEC-05 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Decisão | PKs UUID nas tabelas novas | TRANSCRICAO | [09:51] Larissa |
| ADR-006-DEC-06 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Decisão | CRUD aberto a qualquer papel autenticado | TRANSCRICAO | [09:37] Sofia |
| ADR-006-DEC-07 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Decisão | customer_id não vem do JWT | TRANSCRICAO | [09:32] Larissa |
| ADR-006-ALT-01 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Alternativa Descartada | Extrair customer_id do JWT | TRANSCRICAO | [09:32] Bruno |
| ADR-006-ALT-02 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Alternativa Descartada | Serviço separado (não discutida), contraria mesma stack | TRANSCRICAO | [09:11] Diego |
| ADR-006-QA-01 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Questão em Aberto | Nome do arquivo de processamento | TRANSCRICAO | [09:28] Bruno |
| ADR-006-QA-02 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Questão em Aberto | User sem relação com Customer | CODIGO | prisma/schema.prisma |
| ADR-006-QA-03 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Questão em Aberto | ZodError vira VALIDATION_ERROR, conflito com WEBHOOK_INVALID_URL | CODIGO | src/middlewares/validate.middleware.ts |
| ADR-006-TO-01 | docs/adrs/ADR-006-reuso-dos-padroes-existentes.md | Trade-off | Herda o modelo de autorização atual | TRANSCRICAO | [09:37] Sofia |
| ADR-007 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Decisão | Payload snapshot na inserção | TRANSCRICAO | [09:52] Bruno |
| ADR-007-CTX-01 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Contexto | Campos do modelo Order | CODIGO | prisma/schema.prisma |
| ADR-007-DEC-01 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Decisão | Campos do payload sem itens | TRANSCRICAO | [09:43] Diego |
| ADR-007-DEC-02 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Decisão | Payload enxuto | TRANSCRICAO | [09:44] Bruno |
| ADR-007-DEC-03 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Decisão | Limite de 64KB com erro | TRANSCRICAO | [09:24] Larissa |
| ADR-007-DEC-04 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Decisão | Content-Type application/json | TRANSCRICAO | [09:44] Diego |
| ADR-007-ALT-01 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Alternativa Descartada | Renderizar no envio | TRANSCRICAO | [09:52] Larissa |
| ADR-007-ALT-02 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Alternativa Descartada | Incluir itens do pedido | TRANSCRICAO | [09:43] Diego |
| ADR-007-ALT-03 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Alternativa Descartada | Truncar payload grande | TRANSCRICAO | [09:23] Sofia |
| ADR-007-QA-01 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Questão em Aberto | Campos básicos além de total_cents | TRANSCRICAO | [09:43] Diego |
| ADR-007-QA-02 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Questão em Aberto | Onde verificar o limite de 64KB | TRANSCRICAO | [09:24] Diego |
| ADR-007-TO-01 | docs/adrs/ADR-007-payload-snapshot-na-insercao.md | Trade-off | Armazenar payload pronto em troca de fidelidade ao momento | TRANSCRICAO | [09:52] Diego |

## Cobertura

Itens identificáveis = itens com linha + itens sem origem (listados abaixo). Hipóteses puras não entram na contagem. O TL;DR do RFC e as listas de links entre documentos repetem itens já rastreados nas seções de origem e não são contados de novo.

| Documento | Itens identificáveis | Itens com linha | Cobertura |
| --- | --- | --- | --- |
| docs/PRD.md | 94 | 94 | 100,0% |
| docs/RFC.md | 68 | 68 | 100,0% |
| docs/FDD.md | 176 | 176 | 100,0% |
| docs/adrs/ | 87 | 87 | 100,0% |
| **Total** | 425 | 425 | 100,0% |

- Linhas com Fonte TRANSCRICAO: 347 de 425 (81,6%)
- Linhas com Fonte CODIGO: 78

## Hipóteses puras (sem padrão de código, sem linha)

Rotuladas como hipótese nos documentos e fora da contagem de itens identificáveis.

- **docs/PRD.md:**
  - Meta de adoção por 3 de 3 clientes.
  - Queda do polling em `GET /orders`.
  - Validação com os clientes solicitantes antes de liberar para outros.
- **docs/FDD.md, objetivos e dados:**
  - `eventId` estável em retries e replays.
  - Uma linha de outbox por endpoint com `eventId` compartilhado.
  - `responseBody` truncado em 2KB.
  - Geração de secret com `crypto.randomBytes(32)`.
- **docs/FDD.md, fluxos:**
  - `OrderService.create()` não publica evento.
  - Recuperação de `PROCESSING` na inicialização.
  - Reivindicação com `updateMany`.
  - Endpoint inativo vai direto para a DLQ.
  - `redirect: 'manual'`.
  - Retry não bloqueia eventos posteriores do mesmo pedido.
  - `WEBHOOK_DEAD_LETTER_ALREADY_REPLAYED` ao repetir o replay.
- **docs/FDD.md, contratos:**
  - Secret exibida só na criação e na rotação.
  - URL de até 2048 caracteres.
  - Rota `GET /webhooks/customers/:customerId`.
  - Filtros `active` e `success`.
  - Rota `rotate-secret` e conflito de rotação.
  - Prefixo `sha256=` em hex.
  - `X-Timestamp` fora da assinatura.
  - Dupla assinatura no grace period.
  - Classificação de status HTTP como falha.
  - Payload apenas com `total_cents`.
- **docs/FDD.md, erros e resiliência:**
  - `WEBHOOK_DELIVERY_HTTP_ERROR`.
  - `WEBHOOK_DELIVERY_NETWORK_ERROR`.
  - `WEBHOOK_ENDPOINT_INACTIVE`.
  - Comportamento com banco indisponível.
  - Intervalos como constante no código.
- **docs/FDD.md, observabilidade:**
  - Nomes e tipos de 6 métricas.
  - Nomes dos eventos de log.
  - Spans de tracing.
  - Amostragem de 100%.
  - Dashboards e alertas.
- **docs/FDD.md, critérios de aceite:**
  - Listagens sem `secret`.
  - Dupla assinatura nas 24h após rotação.

## Itens sem origem

Nenhum. As quatro afirmações sem origem apontadas na versão anterior (seção 11 do PRD e três garantias da seção 10 do FDD) foram reescritas nos próprios documentos com citação ou como hipótese ligada a código, e agora têm linha: PRD-CA-11 e FDD-DEP-05 a FDD-DEP-09.
