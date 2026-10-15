# PRD: Sistema de Webhooks de Notificação de Pedidos

Versão: 0.1
Data: 2026-10-15
Responsável: Marcos (Product Manager), que trouxe a demanda dos clientes (`[09:00] Marcos`)
Documentos relacionados: [RFC](RFC.md), [FDD](FDD.md), [ADRs](adrs/README.md)

Convenção: itens marcados **(hipótese)** não foram definidos na reunião nem nos documentos de design e devem ser confirmados por produto.

---

## 1. Resumo e contexto da feature

O OMS passa a avisar automaticamente os sistemas dos clientes B2B quando o status de um pedido muda, enviando uma notificação HTTPS assinada para um endereço que o próprio cliente cadastra. É um webhook só de saída: a plataforma envia, o cliente recebe (`[09:02] Marcos`).

Hoje o OMS controla o ciclo de vida do pedido (`PENDING`, `PAID`, `PROCESSING`, `SHIPPED`, `DELIVERED`, `CANCELLED`, em `src/modules/orders/order.status.ts`) e registra cada mudança no histórico do pedido, mas não tem nenhum mecanismo para avisar sistemas externos. A única forma de um cliente saber que algo mudou é consultar a API de pedidos periodicamente (`[09:00] Marcos`).

## 2. Problema e motivação

- **Dor:** os clientes ficam consultando `GET /orders` de tempos em tempos para ver se algo mudou, o que deixa a integração lenta e cara para eles (`[09:00] Marcos`).
- **Quem pediu:** três clientes B2B, Atlas Comercial, MaxDistribuição e Nova Cargo, num pedido formal na semana anterior à reunião (`[09:00] Marcos`).
- **Consequência de não entregar:** a Atlas sinalizou que pode migrar para um concorrente se a solução não sair até o fim do trimestre (`[09:00] Marcos`).
- **Por que agora:** a Atlas pediu a entrega para o fim de novembro (`[09:45] Marcos`), e a estimativa do time é de três sprints, incluindo a revisão de segurança (`[09:47] Larissa`).

## 3. Público-alvo e cenários de uso

**Público-alvo**
- **Clientes B2B integrados à plataforma** (inicialmente Atlas Comercial, MaxDistribuição e Nova Cargo): recebem as notificações nos seus sistemas.
- **Usuários autenticados do OMS que representam o cliente** (`[09:32] Marcos`): cadastram e mantêm os endereços de webhook pela API.
- **Administradores (papel `ADMIN`)**: reprocessam notificações que falharam definitivamente (`[09:36] Sofia`).
- **Desenvolvedores dos clientes**: integram a partir da documentação no portal de desenvolvedor (`[09:26] Marcos`, `[09:40] Marcos`).

**Cenários de uso**
1. **Rastrear envios.** A Nova Cargo cadastra um endereço para receber apenas `SHIPPED` e `DELIVERED` (exemplo de `[09:33] Marcos`). Quando um operador marca um pedido como enviado, o sistema da Nova Cargo recebe a notificação em poucos segundos, sem consultar a API.
2. **Cliente fora do ar.** O endpoint da MaxDistribuição fica duas horas indisponível por manutenção planejada, situação que já aconteceu com um cliente (`[09:16] Diego`). A plataforma tenta de novo automaticamente e entrega as notificações quando o endpoint volta, sem ação manual.
3. **Notificação recebida duas vezes.** A Atlas recebe a mesma notificação duas vezes depois de uma nova tentativa. O sistema dela reconhece o identificador do evento e descarta a repetição (`[09:25] Diego`).
4. **Investigar e reenviar.** Um cliente reclama que não recebeu atualizações. O usuário consulta o histórico de entregas do webhook, vê as falhas e o tempo de resposta (`[09:34] Marcos`), e um administrador reenvia o evento que esgotou as tentativas (`[09:18] Diego`).

## 4. Objetivos e métricas de sucesso

| Objetivo | Métrica | Linha de base | Meta | Fonte |
| --- | --- | --- | --- | --- |
| Notificar em "tempo real" na percepção do cliente | Tempo entre a mudança de status e a entrega ao cliente, quando o endpoint está no ar | Não há notificação hoje; o cliente depende do intervalo do próprio polling | Abaixo de 10 segundos; atraso máximo de 2 segundos causado pela plataforma antes do envio | `[09:02] Marcos`, `[09:10] Larissa` |
| Entregar no prazo pedido pelo cliente | Data de disponibilização em produção | Nenhuma entrega | Até o fim de novembro, em três sprints | `[09:45] Marcos`, `[09:47] Larissa` |
| Nunca perder uma notificação | Eventos que não foram entregues nem estão na fila de falhas | Não se aplica | 0 | [ADR-001](adrs/ADR-001-outbox-no-mysql.md), [ADR-005](adrs/ADR-005-entrega-at-least-once-com-x-event-id.md) |
| Recuperar sozinho de indisponibilidades do cliente | Janela coberta por novas tentativas automáticas | Não se aplica | Cerca de 15 horas | `[09:17] Diego` |
| Reter os clientes solicitantes | Clientes solicitantes usando webhooks em produção (hipótese) | 0 de 3 | 3 de 3 após o lançamento (hipótese) | `[09:00] Marcos` |
| Reduzir o polling dos clientes solicitantes | Chamadas a `GET /orders` por esses clientes (hipótese) | Não medida | Queda após a adoção; valor a definir por produto (hipótese) | `[09:00] Marcos` |

## 5. Escopo (incluso e fora de escopo)

**Incluso**
- Cadastro, listagem, edição e remoção de endpoints de webhook por customer, com escolha dos status de interesse.
- Notificação automática a cada mudança de status do pedido que algum endpoint do customer escuta.
- Assinatura de cada notificação e rotação da secret pelo próprio cliente.
- Novas tentativas automáticas, fila de falhas definitivas e reenvio manual por administrador.
- Histórico de entregas por endpoint.

**Fora de escopo**

| Item | Situação | Motivo | Fonte |
| --- | --- | --- | --- |
| Aviso por email quando o webhook do cliente falha repetidamente | Adiado para uma próxima fase | Medir o impacto da primeira versão antes | `[09:37] Larissa` |
| Limite de taxa de envio por cliente (rate limiting) | Adiado | Observar em produção e implementar se virar problema | `[09:39] Diego`, `[09:39] Larissa` |
| Painel visual para o cliente acompanhar seus webhooks | Descartado nesta feature | É projeto separado do time de frontend; nesta fase só há endpoints de API | `[09:40] Larissa` |
| Webhooks de entrada (cliente enviando para a plataforma) | Descartado | Os clientes querem só receber | `[09:02] Marcos` |
| Garantia de ordem global entre todos os eventos | Descartado | Os clientes só precisam saber se cada pedido mudou; a ordem é garantida apenas por pedido | `[09:13] Larissa`, `[09:14] Marcos` |
| Entrega exatamente uma vez (exactly-once) | Descartado | Exigiria coordenação dos dois lados; o cliente deduplica | `[09:25] Diego` |
| Arquivamento de notificações antigas | Adiado | Fora do escopo desta feature | `[09:08] Diego` |
| Restrição por papel no cadastro de webhooks | Adiado | Qualquer usuário autenticado por enquanto; pode ser endurecido depois | `[09:37] Sofia` |

## 6. Requisitos funcionais

| ID | Requisito | Fonte |
| --- | --- | --- |
| FR-01 | Um usuário autenticado cadastra um endpoint de webhook informando o customer, a URL e a lista de status que quer receber. O customer é informado na requisição, não deduzido do login | `[09:31] Marcos`, `[09:32] Larissa` |
| FR-02 | No cadastro, a plataforma gera a secret do endpoint e a devolve ao usuário | `[09:31] Marcos` |
| FR-03 | O usuário lista os webhooks de um customer | `[09:33] Bruno` |
| FR-04 | O usuário edita um webhook (URL, status de interesse, ativo) | `[09:33] Bruno` |
| FR-05 | O usuário remove um webhook | `[09:33] Bruno` |
| FR-06 | Cada endpoint recebe apenas os status que escolheu; se nenhum endpoint do customer quer aquele status, nenhuma notificação é gerada | `[09:33] Marcos`, `[09:34] Bruno` |
| FR-07 | Toda mudança de status de um pedido gera notificação para os endpoints interessados do customer do pedido, e não pode haver mudança de status sem a notificação correspondente | `[09:40] Bruno` |
| FR-08 | A notificação traz identificador do evento, tipo `order.status_changed`, data e hora, pedido, número do pedido, status anterior e novo, customer e valor total; não traz os itens, que o cliente consulta em `GET /orders/:id` | `[09:43] Diego` |
| FR-09 | Cada notificação é assinada, para o cliente verificar que veio da plataforma e não foi alterada, e informa o identificador do evento, o endpoint de origem e o momento do envio | `[09:20] Sofia`, `[09:44] Diego`, `[09:44] Sofia` |
| FR-10 | O cliente pede uma nova secret pela API; a anterior continua válida por 24 horas e depois deixa de valer | `[09:21] Sofia` |
| FR-11 | O usuário consulta o histórico de entregas de um webhook: sucesso ou falha, conteúdo enviado, resposta recebida e tempo de resposta | `[09:34] Marcos` |
| FR-12 | Quando o endpoint do cliente falha ou não responde, a plataforma tenta de novo automaticamente em intervalos crescentes | `[09:15] Diego`, `[09:17] Larissa` |
| FR-13 | Esgotadas as tentativas, a notificação fica guardada numa fila de falhas, com o conteúdo e o motivo da falha | `[09:18] Diego` |
| FR-14 | Somente um administrador (`ADMIN`) reenvia uma notificação da fila de falhas, e a plataforma registra quem fez o reenvio | `[09:36] Sofia`, `[09:36] Larissa` |
| FR-15 | URLs que não usam `https` são recusadas no cadastro e na edição | `[09:23] Sofia` |

Contratos, payloads e códigos de erro de cada requisito estão no [FDD](FDD.md), seção 5.

## 7. Requisitos não funcionais

| ID | Categoria | Requisito | Meta | Fonte |
| --- | --- | --- | --- | --- |
| NFR-01 | Desempenho | Tempo entre a mudança de status e a entrega, com o endpoint do cliente no ar | Abaixo de 10 segundos | `[09:02] Marcos` |
| NFR-02 | Desempenho | Atraso da plataforma antes de iniciar o envio | Até 2 segundos | `[09:10] Larissa` |
| NFR-03 | Confiabilidade | Garantia de entrega: o cliente pode receber a mesma notificação mais de uma vez e deve deduplicar pelo identificador do evento | Pelo menos uma vez (at-least-once) | `[09:26] Larissa` |
| NFR-04 | Confiabilidade | Tempo máximo de espera pela resposta do cliente em cada envio | 10 segundos | `[09:42] Diego` |
| NFR-05 | Confiabilidade | Intervalos das novas tentativas | 1 min, 5 min, 30 min, 2 h, 12 h (cerca de 15 horas no total) | `[09:17] Diego` |
| NFR-06 | Confiabilidade | Ordem das notificações de um mesmo pedido | Garantida por pedido enquanto houver um único processo de envio | `[09:13] Larissa` |
| NFR-07 | Segurança | Assinatura HMAC-SHA256 com secret exclusiva por endpoint, nunca global | 100% das notificações | `[09:21] Sofia`, `[09:22] Sofia` |
| NFR-08 | Segurança | Somente endereços `https` | 0 URLs `http` aceitas | `[09:23] Sofia` |
| NFR-09 | Limites | Tamanho máximo de uma notificação; acima disso gera erro, sem truncar | 64KB | `[09:24] Larissa` |
| NFR-10 | Auditoria | Registro de quem reenviou cada notificação da fila de falhas | 100% dos reenvios | `[09:36] Sofia` |
| NFR-11 | Compatibilidade | A feature segue os padrões já usados no OMS (módulos, erros, logs, validação). As rotas existentes não mudam (hipótese, [FDD](FDD.md) seção 10); a única mudança é que a mudança de status de um pedido passa a falhar se a notificação não puder ser registrada | Nenhuma rota existente alterada | `[09:30] Larissa`, `[09:40] Bruno` |
| NFR-12 | Operação | O envio roda em processo separado da API, para que um reinício da API não interrompa as notificações | Processo independente | `[09:11] Diego` |

## 8. Decisões e trade-offs principais

- **Notificação registrada junto com a mudança de status:** o cliente nunca deixa de ser avisado de uma mudança confirmada que ele escuta, e um cliente lento não atrasa a operação dos demais. Em troca, a notificação sai alguns segundos depois, e não no mesmo instante. Ver [ADR-001](adrs/ADR-001-outbox-no-mysql.md).
- **Envio por um processo separado que verifica novos eventos a cada 2 segundos:** sem infraestrutura nova e dentro da meta de 10 segundos. Em troca, há até 2 segundos de espera e um único processo de envio, que limita a escala. Ver [ADR-002](adrs/ADR-002-worker-separado-com-polling.md).
- **Novas tentativas por cerca de 15 horas e fila de falhas:** cobre manutenções de horas sem ação manual. Em troca, um cliente fora do ar por mais tempo depende de reenvio por um administrador e não é avisado. Ver [ADR-003](adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md).
- **Secret por endpoint com rotação:** o vazamento de uma secret afeta só um endpoint, e o cliente tem 24 horas para migrar os sistemas dele para a nova secret (`[09:21] Sofia`). Em troca, o cliente precisa implementar a verificação da assinatura. Ver [ADR-004](adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md).
- **Entrega pelo menos uma vez:** nenhuma notificação se perde por falha temporária. Em troca, o cliente pode receber repetições e precisa descartá-las. Ver [ADR-005](adrs/ADR-005-entrega-at-least-once-com-x-event-id.md).
- **Reuso dos padrões do OMS:** entrega mais rápida e comportamento igual ao resto da API. Em troca, herda o controle de acesso atual, que não separa usuários por cliente. Ver [ADR-006](adrs/ADR-006-reuso-dos-padroes-existentes.md).
- **Conteúdo da notificação fixado no momento da mudança:** a notificação mostra o pedido como estava quando o status mudou, mesmo se entregue horas depois. Em troca, quem precisa dos itens faz uma consulta extra. Ver [ADR-007](adrs/ADR-007-payload-snapshot-na-insercao.md).

## 9. Dependências

- **Revisão de segurança por Sofia:** pelo menos dois dias úteis, antes do deploy, focada em assinatura e geração de secret (`[09:46] Sofia`, `[09:49] Sofia`).
- **Documentação no portal de desenvolvedor por Marcos:** como integrar via API e, em destaque, como tratar notificações repetidas (`[09:26] Marcos`, `[09:40] Marcos`).
- **Confirmação do prazo com a Atlas por Marcos** (`[09:47] Marcos`).
- **Revisão do doc de design por Larissa, Bruno e Diego** antes do início do código (`[09:50] Larissa`).
- **Infraestrutura existente:** o mesmo MySQL e a mesma stack do OMS (`[09:11] Diego`), e mais um processo para implantar e manter no ar (`src/worker.ts`, novo).
- **Lado do cliente:** endpoint `https`, verificação da assinatura e descarte de repetições pelo identificador do evento.

## 10. Riscos e mitigação

As probabilidades são estimativas (hipótese); a reunião não as quantificou.

| Risco | Probabilidade | Impacto | Mitigação |
| --- | --- | --- | --- |
| Entrega depois do fim de novembro | Média (hipótese) | Possível perda da Atlas para um concorrente (`[09:00] Marcos`) | Estimativa de três sprints por bloco, com revisão de segurança já incluída (`[09:46] Larissa`, `[09:47] Larissa`); prazo confirmado com a Atlas (`[09:47] Marcos`) |
| Cliente não trata notificações repetidas | Média | O sistema do cliente processa a mesma mudança duas vezes | Identificador estável do evento e destaque no portal de desenvolvedor (`[09:26] Marcos`) |
| Cliente fora do ar por mais de cerca de 15 horas sem perceber | Baixa | Notificações param na fila de falhas e o cliente não é avisado, porque o email ficou para depois (`[09:37] Larissa`) | Histórico de entregas consultável pelo cliente (`[09:34] Marcos`) e reenvio por administrador (`[09:18] Diego`) |
| Muitas mudanças de status de um cliente em pouco tempo | Média | 50 mudanças em um minuto viram 50 chamadas ao cliente (`[09:38] Diego`) | Observar em produção e decidir sobre limite de taxa depois (`[09:39] Larissa`) |
| Vazamento de secret pelo cliente | Média | Terceiros podem forjar notificações para aquele endpoint; já houve vazamento em log de cliente (`[09:22] Diego`) | Secret por endpoint e rotação com 24 horas de transição (`[09:21] Sofia`) |
| Um usuário autenticado altera webhooks de outro cliente | Média (hipótese) | Notificações de um cliente desviadas ou desligadas por quem não deveria | Hoje qualquer papel autenticado gerencia webhooks e não há vínculo entre usuário e cliente no OMS (`prisma/schema.prisma`); endurecimento previsto para depois (`[09:37] Sofia`) |
| Número de tentativas diferente do esperado | Média | Notificações vão para a fila de falhas uma tentativa antes ou depois do combinado | Questão em aberto no [RFC](RFC.md); o [FDD](FDD.md) deixa o valor configurável |

## 11. Critérios de aceitação

- [ ] Um usuário autenticado consegue cadastrar, listar, editar e remover webhooks de um customer, e recebe a secret no cadastro (FR-01 a FR-05).
- [ ] Um endpoint `http` é recusado (FR-15, NFR-08).
- [ ] Uma mudança de status gera notificação apenas para os endpoints que escolheram aquele status (FR-06, FR-07).
- [ ] A notificação traz os campos de FR-08 e nenhum item do pedido.
- [ ] O cliente consegue validar a assinatura com a secret do endpoint, inclusive durante as 24 horas após uma rotação (FR-09, FR-10).
- [ ] Com o endpoint no ar, a notificação chega em menos de 10 segundos (NFR-01).
- [ ] Com o endpoint fora do ar, as novas tentativas seguem 1 min, 5 min, 30 min, 2 h e 12 h e, esgotadas, a notificação vai para a fila de falhas (FR-12, FR-13, NFR-05).
- [ ] Um operador não consegue reenviar da fila de falhas; um administrador consegue, e o reenvio fica registrado com o autor (FR-14, NFR-10).
- [ ] O histórico de entregas mostra sucesso ou falha, conteúdo, resposta e tempo de resposta (FR-11).
- [ ] As rotas existentes do OMS continuam iguais, e a mudança de status só falha por causa da feature quando a notificação não pode ser registrada (NFR-11).
- [ ] A revisão de segurança foi concluída antes do deploy (`[09:46] Sofia`).
- [ ] Os critérios técnicos da seção 11 do [FDD](FDD.md) foram atendidos.

## 12. Estratégia de testes e validação

- **Testes automatizados:** testes de integração com Vitest contra um MySQL real, no padrão já usado pelo projeto (`tests/setup.ts`, `tests/orders.test.ts`), cobrindo cadastro, filtro por status, notificação gerada junto com a mudança de status, assinatura, novas tentativas, fila de falhas e permissão de reenvio. Os casos estão detalhados na seção 11 do [FDD](FDD.md).
- **Testes de ponta a ponta:** mudança de status via API com um endpoint de teste recebendo a notificação, medindo o tempo de entrega e simulando indisponibilidade e timeout (`[09:46] Larissa` prevê integração e testes ponta a ponta no plano).
- **Revisão de segurança:** Sofia revisa assinatura e geração de secret antes do deploy (`[09:46] Sofia`).
- **Validação com os clientes solicitantes (hipótese):** homologar a integração com Atlas, MaxDistribuição e Nova Cargo antes de liberar para outros clientes, usando a documentação do portal.
- **Medição das métricas:** a latência de entrega e o volume da fila de falhas vêm das métricas de observabilidade definidas no [FDD](FDD.md), seção 8, acompanhadas desde o lançamento. A adoção pelos três clientes e a queda do polling em `GET /orders` são medidas nas primeiras semanas após o lançamento (hipótese) e servem de base para decidir sobre email e limite de taxa (`[09:37] Larissa`, `[09:39] Larissa`).
