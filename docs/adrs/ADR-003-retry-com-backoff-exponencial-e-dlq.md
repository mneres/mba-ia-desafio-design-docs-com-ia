# ADR-003: Retry com backoff exponencial e DLQ em tabela separada com replay manual

## Status

Aceito (`[09:17] Larissa`)

## Data

Não informada na transcrição (reunião de quinta-feira, 09:00).

## Decisores

Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma), Bruno (Engenheiro Pleno, Pedidos), Marcos (Product Manager), Sofia (Engenheira de Segurança).

## Contexto

O worker ([ADR-002](ADR-002-worker-separado-com-polling.md)) vai encontrar endpoints de clientes fora do ar (`[09:14] Larissa`). Diego propôs backoff exponencial com um teto de tentativas, após o qual o evento é considerado falha permanente e vai para uma DLQ (`[09:15] Diego`).

O número de tentativas foi debatido. Retry indefinido deixaria eventos pendurados para sempre se o cliente sumisse (`[09:15] Diego`). Três tentativas seriam pouco: o retry se esgotaria em cerca de 30 minutos, e já houve cliente com indisponibilidade de duas horas em manutenção planejada (`[09:16] Diego`). Marcos considerou aceitável a janela de cerca de 15 horas: um cliente fora do ar por esse tempo "já tá com problema sério dele" (`[09:17] Marcos`).

Para a DLQ, Larissa perguntou se seria uma tabela separada ou um status "failed" na própria outbox (`[09:17] Larissa`). Não existe hoje no código nenhuma tabela de falhas nem endpoint administrativo de reprocessamento; o controle de papel existente é `requireRole` em `src/middlewares/auth.middleware.ts`, com os papéis `ADMIN` e `OPERATOR`.

## Decisão

- **Backoff exponencial com os intervalos 1m, 5m, 30m, 2h e 12h**, "total de quase 15 horas entre primeira falha e última tentativa" (`[09:17] Diego`), fechado por `[09:17] Larissa` e repetido no resumo final (`[09:48] Larissa`).
- **Timeout de 10 segundos** por chamada HTTP; cliente que não responde nesse prazo é tratado como falha e marcado para retry (`[09:42] Diego`).
- **Esgotado o retry, o evento vai para a tabela `webhook_dead_letter` (nova)**, separada da outbox, com o payload, o motivo da falha e o timestamp (`[09:18] Diego`).
- **Replay manual por endpoint administrativo** `POST /admin/webhooks/dead-letter/:id/replay` (novo), que recoloca o evento na outbox como pendente (`[09:18] Diego`, `[09:35] Diego`). O endpoint exige papel `ADMIN`, reaproveitando o `requireRole` existente, e deve registrar quem fez o replay, para auditoria (`[09:36] Sofia`, `[09:36] Larissa`); ver [ADR-006](ADR-006-reuso-dos-padroes-existentes.md).

Lacunas que a reunião deixou abertas:

- **Número total de envios.** O resumo diz "total 5 tentativas" (`[09:48] Larissa`), mas cinco intervalos somando quase 15 horas entre a primeira falha e a última tentativa (`[09:17] Diego`) só fecham com um envio inicial mais 5 retentativas (6 envios). A reunião não resolveu essa contradição; fica como questão em aberto até revisão com o time.
- Quais respostas HTTP contam como falha. A reunião só definiu o timeout; não tratou de códigos 4xx, 5xx ou redirecionamentos.
- Onde e como o registro de auditoria do replay é persistido (log Pino ou tabela).
- Se o evento reprocessado mantém o mesmo `event_id`; ver [ADR-005](ADR-005-entrega-at-least-once-com-x-event-id.md).

## Alternativas Consideradas

- **Retry indefinido com backoff.** Descartado: evento fica pendurado para sempre se o cliente sumiu (`[09:15] Diego`).
- **Apenas 3 tentativas.** Proposto por Bruno como mais agressivo (`[09:16] Bruno`). Descartado: esgotaria em cerca de 30 minutos e não cobriria manutenções de duas horas já observadas (`[09:16] Diego`).
- **Status "failed" na própria outbox, sem tabela de DLQ.** Levantado por Larissa (`[09:17] Larissa`). Descartado em favor da tabela separada, que mantém a leitura da outbox principal limpa e serve de evidência para debug e reprocessamento (`[09:18] Diego`).

## Consequências

**Positivas**
- Cobre indisponibilidades de horas, como manutenções planejadas, sem intervenção humana.
- Eventos nunca ficam pendurados indefinidamente na outbox.
- Falhas permanentes ficam preservadas com motivo e payload, e podem ser reenviadas por um ADMIN com trilha de auditoria.

**Negativas**
- Um cliente fora do ar por mais de cerca de 15 horas só recebe os eventos se um ADMIN fizer replay manual; não há aviso automático ao cliente, porque notificação por email ficou para uma próxima fase (`[09:37] Larissa`).
- Um evento pode chegar com até cerca de 15 horas de atraso em relação à mudança de status.
- Mais uma tabela e mais um endpoint para manter, e o replay depende de ação humana.

**Trade-off**

O time ganha uma janela de recuperação automática de cerca de 15 horas e um destino explícito para falhas permanentes, e aceita que eventos além dessa janela dependem de replay manual por um ADMIN.
