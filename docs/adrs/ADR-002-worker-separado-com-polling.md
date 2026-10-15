# ADR-002: Worker em processo separado, lendo a outbox por polling a cada 2 segundos

## Status

Aceito (`[09:10] Larissa`)

## Data

Não informada na transcrição (reunião de quinta-feira, 09:00).

## Decisores

Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma), Bruno (Engenheiro Pleno, Pedidos), Marcos (Product Manager).

## Contexto

Com a outbox definida em [ADR-001](ADR-001-outbox-no-mysql.md), faltava decidir como os eventos saem da tabela (`[09:08] Larissa`). O requisito de produto é entregar em menos de 10 segundos (`[09:02] Marcos`).

Bruno perguntou se um trigger do banco tornaria o worker mais reativo (`[09:09] Bruno`). Diego explicou que o MySQL não tem listener nativo como o `NOTIFY/LISTEN` do Postgres: um trigger só executa SQL e não avisa processo externo, e notificar o worker exigiria improvisos como escrever em arquivo ou chamar um endpoint (`[09:09] Diego`).

Hoje o projeto tem um único entry-point, `src/server.ts`, que sobe a API Express, trata `SIGINT`/`SIGTERM` e chama `prisma.$disconnect()`. O `package.json` não tem script de worker. Diego observou que, se o worker rodasse dentro da API, um restart da API derrubaria o worker (`[09:11] Diego`).

## Decisão

- **Polling em loop a cada 2 segundos**, buscando os eventos pendentes mais antigos, processando e marcando o resultado (`[09:09] Diego`). A latência mínima aceita é de 2 segundos no pior caso (`[09:10] Larissa`), dentro da meta de 10 segundos (`[09:10] Marcos`). O worker lê só pendentes, em batch pequeno (`[09:08] Diego`).
- **Processo separado da API**, mesmo banco e mesma stack (`[09:11] Diego`). Entry-point novo `src/worker.ts` (a criar) ao lado de `src/server.ts`, com script `npm run worker` (a criar) no `package.json` (`[09:11] Larissa`). A lógica de processamento fica dentro do módulo, em `src/modules/webhooks/webhook.worker.ts` ou `webhook.processor.ts` (a criar) (`[09:28] Bruno`).
- **PrismaClient próprio** no processo do worker, com a mesma `DATABASE_URL` (`[09:30] Bruno`). O código já expõe a fábrica `createPrismaClient()` em `src/config/database.ts`, e `DATABASE_URL` já é validada em `src/config/env.ts`.
- **Uma única instância de worker.** Com um worker só, os eventos são processados em ordem de `created_at`, o que dá ordering implícita por `order_id` (`[09:12] Diego`). A ausência de ordering global, e de ordering quando houver vários workers, é registrada como limitação conhecida (`[09:13] Larissa`); os clientes não pediram ordering global (`[09:14] Marcos`).

Lacunas que a reunião deixou abertas:

- O tamanho do batch ("batch pequeno") e se o intervalo de 2 segundos será configurável por variável de ambiente.
- A ordering foi discutida para entregas bem-sucedidas em sequência. A reunião não definiu o que acontece com o próximo evento do mesmo pedido enquanto um evento anterior aguarda retry ([ADR-003](ADR-003-retry-com-backoff-exponencial-e-dlq.md)).
- Como o worker reivindica eventos (o status "processando" foi citado em `[09:08] Diego`) e como trata eventos presos nesse status após um restart.
- Encerramento gracioso do worker; `src/server.ts` é o padrão existente de tratamento de sinais.

## Alternativas Consideradas

- **Trigger no MySQL notificando o worker.** Descartada: o MySQL não oferece `NOTIFY/LISTEN`, e qualquer notificação a processo externo seria improvisada (`[09:09] Diego`).
- **Worker rodando dentro do processo da API.** Descartada: um restart da API derrubaria o worker (`[09:11] Diego`).
- **Vários workers em paralelo.** Adiada: escalar perde a garantia de ordering; se for necessário, particionar por `order_id` ou usar lock pessimista, mas "é problema do futuro" (`[09:13] Diego`).

## Consequências

**Positivas**
- Implementação simples sobre o banco existente, sem dependência nova.
- Ciclo de vida independente da API: deploys e restarts da API não interrompem entregas, e vice-versa.
- Ordering por pedido enquanto houver um único worker, suficiente para o que os clientes pediram (`[09:14] Marcos`).

**Negativas**
- Latência de até 2 segundos só de espera pelo próximo ciclo, além do tempo de envio.
- Consultas periódicas ao MySQL mesmo sem eventos pendentes.
- Mais um processo para operar e monitorar em produção.
- Throughput limitado a um worker; escalar exige rever a garantia de ordering.

**Trade-off**

O time aceita latência de 2 segundos, carga constante de polling e um único worker em troca de não operar infraestrutura de mensageria e de manter ordering por pedido sem coordenação.
