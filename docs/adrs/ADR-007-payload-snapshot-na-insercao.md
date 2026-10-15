# ADR-007: Payload enxuto renderizado como snapshot na inserção da outbox

## Status

Aceito (`[09:52] Bruno`)

## Data

Não informada na transcrição (reunião de quinta-feira, 09:00).

## Decisores

Larissa (Tech Lead), Diego (Engenheiro Sênior, Plataforma), Bruno (Engenheiro Pleno, Pedidos). Sofia (Engenheira de Segurança) participou apenas da definição do limite de tamanho; já tinha saído da call quando o snapshot foi decidido.

## Contexto

Depois do encerramento formal da reunião, Bruno perguntou se a linha da outbox ([ADR-001](ADR-001-outbox-no-mysql.md)) guarda o payload já renderizado ou só o `order_id`, renderizando na hora do envio (`[09:51] Bruno`). A diferença importa porque o envio pode acontecer bem depois da mudança de status: até cerca de 15 horas com o retry de [ADR-003](ADR-003-retry-com-backoff-exponencial-e-dlq.md), ou mais com replay da DLQ. Nesse intervalo o pedido pode ter mudado de novo.

O pedido em `prisma/schema.prisma` (`Order`) tem `orderNumber`, `customerId`, `status`, `totalCents` e uma lista de `OrderItem`. Antes, a reunião já tinha definido o formato do payload (`[09:43] Diego`) e um limite de tamanho, que a própria reunião classificou como requisito não funcional e não como decisão separada (`[09:24] Larissa`).

## Decisão

- **Snapshot na inserção**: o payload é renderizado no momento em que o evento entra na outbox, dentro da transação de `changeStatus`, e reflete o estado do pedido naquela mudança de status (`[09:52] Larissa`, `[09:52] Diego`, fechado em `[09:52] Bruno`).
- **Payload JSON enxuto** com `event_id`, `event_type` (por exemplo `order.status_changed`), timestamp ISO 8601, `order_id`, `order_number`, `from_status`, `to_status`, `customer_id` e campos básicos do pedido como `total_cents`. **Sem itens**: quem precisar de detalhes consulta `GET /orders/:id` (`[09:43] Diego`, `[09:44] Bruno`). Enviado com `Content-Type: application/json` (`[09:44] Diego`). O contrato completo fica no FDD.
- **Limite de 64KB**: evento acima disso não é enviado e gera erro, sem truncamento (`[09:23] Sofia`, `[09:24] Diego`, `[09:24] Larissa`).

Lacunas que a reunião deixou abertas:

- Quais são, além de `total_cents`, os "campos básicos da order" do payload (`[09:43] Diego`).
- Em que ponto o limite de 64KB é verificado (na inserção, dentro da transação, ou no envio) e o que acontece com o evento que o excede (bloquear a mudança de status, ir para a DLQ ou ser descartado com log).

## Alternativas Consideradas

- **Guardar só o `order_id` e renderizar no envio.** Descartada: se o pedido mudar depois, o evento deixaria de refletir o estado da mudança que o originou (`[09:52] Larissa`).
- **Incluir os itens do pedido no payload.** Descartada para não inflar o payload (`[09:43] Diego`).
- **Truncar payloads acima do limite.** Descartada por Sofia, que preferiu erro: um evento desse tamanho indica que algo está errado (`[09:23] Sofia`).

## Consequências

**Positivas**
- Cada evento é fiel ao momento da mudança de status, mesmo entregue horas depois ou reprocessado da DLQ.
- O worker não precisa consultar `orders` para enviar, nem lidar com pedidos alterados ou removidos depois do evento.
- Payload pequeno e previsível, bem abaixo do limite de 64KB (`[09:24] Diego`).

**Negativas**
- O payload é duplicado em cada linha da outbox e da DLQ, aumentando o volume armazenado.
- Mudanças futuras no formato do payload não alcançam eventos já enfileirados.
- Clientes que precisam de itens fazem uma chamada extra a `GET /orders/:id`.

**Trade-off**

O time aceita armazenar o payload pronto em cada evento e exigir uma chamada extra para detalhes, em troca de eventos que representam fielmente a mudança de status que os gerou.
