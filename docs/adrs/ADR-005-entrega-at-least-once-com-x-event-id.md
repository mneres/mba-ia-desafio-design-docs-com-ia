# ADR-005: Entrega at-least-once com `X-Event-Id` para deduplicação no cliente

## Status

Aceito (`[09:26] Larissa`)

## Data

Não informada na transcrição (reunião de quinta-feira, 09:00).

## Decisores

Diego (Engenheiro Sênior, Plataforma), Larissa (Tech Lead), Sofia (Engenheira de Segurança), Marcos (Product Manager), Bruno (Engenheiro Pleno, Pedidos).

## Contexto

Com outbox ([ADR-001](ADR-001-outbox-no-mysql.md)), worker por polling ([ADR-002](ADR-002-worker-separado-com-polling.md)) e retry ([ADR-003](ADR-003-retry-com-backoff-exponencial-e-dlq.md)), o mesmo evento pode chegar mais de uma vez ao cliente: por exemplo, quando o cliente processa a requisição mas a resposta não volta dentro do timeout de 10 segundos (`[09:42] Diego`) e o evento é retentado, ou quando um ADMIN faz replay a partir da DLQ.

Diego definiu que a garantia é at-least-once e que o cliente "tem que estar preparado" para duplicatas (`[09:24] Diego`). Bruno perguntou como o cliente diferencia uma entrega repetida (`[09:25] Bruno`). Sofia apontou que isso transfere responsabilidade ao cliente (`[09:25] Sofia`).

O projeto já gera UUIDs para todas as chaves primárias (`@default(uuid()) @db.Char(36)` em `prisma/schema.prisma`) e tem a biblioteca `uuid` em `package.json`.

## Decisão

- **Garantia de entrega at-least-once**: o cliente pode receber o mesmo evento mais de uma vez (`[09:24] Diego`), decisão fechada em `[09:26] Larissa`.
- **Header `X-Event-Id`** com um UUID gerado quando o evento entra na outbox, único por evento; o cliente deduplica por ele (`[09:25] Diego`). O mesmo identificador vai no corpo como `event_id` ([ADR-007](ADR-007-payload-snapshot-na-insercao.md)).
- **Header `X-Webhook-Id`** com o id do endpoint configurado, para clientes com vários cadastros saberem qual deles originou o envio (`[09:44] Sofia`, aceito em `[09:45] Diego`).
- **Comunicação ao cliente**: o comportamento at-least-once será documentado em destaque no portal de desenvolvedor (`[09:26] Marcos`).

Lacunas que a reunião deixou abertas:

- Se o replay manual da DLQ, que "recoloca na outbox como pendente" (`[09:18] Diego`), preserva o `event_id` original. Se gerar um novo, a deduplicação do cliente não reconhece a reentrega.
- Se cada tentativa de retry de um mesmo evento envia o mesmo `X-Event-Id` foi implicitamente assumido ("único por evento", `[09:25] Diego`), mas não dito explicitamente.

## Alternativas Consideradas

- **Exactly-once.** Descartada: exigiria coordenação entre os dois lados e "fica muito mais complexo"; at-least-once com `event_id` "resolve 99% dos casos" e é o modelo usado por Stripe e GitHub (`[09:25] Diego`).
- **At-most-once, sem retry (não discutida na reunião).** Eliminaria duplicatas, mas contradiz a política de retry já decidida em [ADR-003](ADR-003-retry-com-backoff-exponencial-e-dlq.md) e a necessidade de cobrir indisponibilidades de horas (`[09:16] Diego`).

## Consequências

**Positivas**
- O lado da plataforma fica simples: retry e replay não precisam saber se o cliente já processou o evento.
- Combinado com a outbox, nenhum evento é perdido por falha transitória.
- O cliente tem um identificador estável para deduplicar e para correlacionar entregas no histórico.

**Negativas**
- A responsabilidade de idempotência fica com o cliente (`[09:25] Sofia`); quem não deduplicar pode processar a mesma mudança de status duas vezes.
- Exige comunicação clara no portal de desenvolvedor (`[09:26] Marcos`).
- A deduplicação só funciona se o `event_id` for preservado em retries e replays, o que ainda precisa ser garantido no desenho.

**Trade-off**

O time ganha entrega confiável sem coordenação entre as partes, e aceita que duplicatas aconteçam e que deduplicá-las seja trabalho do cliente.
