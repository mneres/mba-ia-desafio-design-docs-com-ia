# ADR-001: Padrão Outbox no MySQL, gravado na mesma transação do `changeStatus`

## Status

Aceito (`[09:08] Larissa`)

## Data

Não informada na transcrição (reunião de quinta-feira, 09:00).

## Decisores

Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma), Bruno (Engenheiro Pleno, Pedidos).

## Contexto

Três clientes B2B (Atlas Comercial, MaxDistribuição e Nova Cargo) pediram para ser notificados quando o status dos seus pedidos muda, em vez de fazer polling no `GET /orders` (`[09:00] Marcos`). Para eles, "tempo real" é qualquer coisa abaixo de 10 segundos (`[09:02] Marcos`). A pergunta inicial foi se o disparo seria síncrono no service de pedidos ou passaria por alguma fila/outbox (`[09:03] Larissa`).

Hoje a mudança de status acontece em `OrderService.changeStatus()` (`src/modules/orders/order.service.ts`), dentro de um único `prisma.$transaction`: valida a transição com `canTransition` (`src/modules/orders/order.status.ts`), debita estoque em `PENDING → PAID` (`shouldDebitStock`) ou repõe em cancelamentos a partir de `PAID`/`PROCESSING` (`shouldReplenishStock`), atualiza `orders` e insere em `order_status_history`. Bruno descreveu essa transação como "já pesada" e apontou que uma chamada HTTP dentro dela faria um cliente lento travar mudanças de status de outros pedidos (`[09:04] Bruno`), e que não há como dar rollback na mudança de status porque o cliente está fora do ar (`[09:04] Bruno`). Não existe hoje nenhum mecanismo de evento, fila ou outbox no código.

Diego propôs o padrão outbox: inserir o evento numa tabela `webhook_outbox` (nova) dentro da mesma transação SQL que atualiza `orders` e `order_status_history`, com um worker separado lendo a tabela e fazendo as chamadas HTTP; se a transação commita, o evento existe, se dá rollback, o evento some junto (`[09:06] Diego`). O argumento para usar o MySQL existente foi o tamanho do time: subir Redis Cluster seria overengineering (`[09:07] Diego`).

## Decisão

Os eventos de webhook são registrados numa tabela outbox no MySQL existente (`webhook_outbox`, nova), gravada **dentro** da transação de `OrderService.changeStatus()`. Se a inserção na outbox falhar, a transação inteira faz rollback: não pode haver mudança de status sem evento (`[09:40] Bruno`, confirmado em `[09:41] Diego`).

Refinamentos fechados na reunião:

- **Ponto de integração:** uma função `publishWebhookEvent(tx, order, fromStatus, toStatus)` (nova, no módulo `src/modules/webhooks/`, a criar) recebe o client da transação corrente e é chamada por `order.service.ts`, em vez de injetar um repository de webhooks inteiro no `OrderService` (`[09:41] Bruno`, `[09:41] Diego`). O tipo `TxClient = Prisma.TransactionClient` já usado em `order.service.ts` é o client que essa função recebe.
- **Filtro na inserção:** só se insere na outbox se algum webhook do customer do pedido escuta aquele status; se nenhum escuta, nenhuma linha é criada (`[09:34] Bruno`, `[09:34] Diego`).
- **Índices e ciclo de vida da linha:** a tabela tem índice no status do evento (pendente, processando, falhou, entregue) e em `created_at` (`[09:08] Diego`).
- **Chave primária UUID**, seguindo o padrão do schema (`[09:51] Larissa`); ver [ADR-006](ADR-006-reuso-dos-padroes-existentes.md).
- O conteúdo do evento é um snapshot renderizado na inserção; ver [ADR-007](ADR-007-payload-snapshot-na-insercao.md).

Lacunas que a reunião deixou abertas (a resolver no FDD):

- A reunião só discutiu o gatilho em `changeStatus`. Não definiu se a criação do pedido em `OrderService.create()`, que grava o histórico inicial `null → PENDING`, também gera evento.
- O arquivamento de linhas entregues "depois de 30 dias ou assim" foi declarado fora do escopo desta feature (`[09:08] Diego`); não há política de retenção definida.

## Alternativas Consideradas

- **Chamada HTTP síncrona dentro de `changeStatus`.** Descartada: um cliente lento seguraria a transação e bloquearia mudanças de status de outros pedidos (`[09:04] Bruno`), e falha do cliente não pode desfazer a mudança de status (`[09:04] Bruno`). "Síncrono está fora de questão" (`[09:06] Diego`).
- **Redis Streams ou fila equivalente.** Levantada por Larissa, com o custo de "subir mais infra" (`[09:07] Larissa`). Descartada por ser overengineering para um time pequeno (`[09:07] Diego`).
- **Inserir o evento fora da transação de mudança de status.** Descartada explicitamente: "se ficar fora da transação, perde a garantia toda" (`[09:41] Diego`).

## Consequências

**Positivas**
- Atomicidade entre mudança de status e evento: não existe status alterado sem evento, nem evento de uma mudança que deu rollback (`[09:06] Diego`).
- A latência e a disponibilidade dos endpoints dos clientes deixam de afetar `changeStatus`.
- Nenhuma infraestrutura nova: mesmo MySQL e mesmo Prisma já configurados em `prisma/schema.prisma` e `src/config/database.ts`.

**Negativas**
- A transação de `changeStatus` ganha mais escritas (consulta das configurações de webhook do customer e insert na outbox) e passa a falhar se a outbox falhar.
- `order.service.ts` passa a depender do módulo de webhooks.
- A tabela cresce sem limite enquanto o arquivamento não for definido.
- A entrega deixa de ser imediata: depende do ciclo do worker ([ADR-002](ADR-002-worker-separado-com-polling.md)).

**Trade-off**

O time troca um pouco de custo na transação de mudança de status e uma tabela a mais no MySQL pela garantia de consistência entre pedido e evento, sem operar nova infraestrutura.
