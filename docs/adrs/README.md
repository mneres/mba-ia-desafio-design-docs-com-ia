# Architectural Decision Records

Este diretório armazena os ADRs (Architectural Decision Records) do Sistema de Webhooks de Notificação de Pedidos.
Cada decisão arquitetural relevante é registrada em um arquivo individual, nomeado sequencialmente no formato `ADR-NNN-titulo-em-kebab-case.md`.

| ADR | Título | Status |
| --- | --- | --- |
| [ADR-001](ADR-001-outbox-no-mysql.md) | Padrão Outbox no MySQL, gravado na mesma transação do `changeStatus` | Aceito |
| [ADR-002](ADR-002-worker-separado-com-polling.md) | Worker em processo separado, lendo a outbox por polling a cada 2 segundos | Aceito |
| [ADR-003](ADR-003-retry-com-backoff-exponencial-e-dlq.md) | Retry com backoff exponencial e DLQ em tabela separada com replay manual | Aceito |
| [ADR-004](ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md) | Assinatura HMAC-SHA256 com secret por endpoint e rotação com grace period de 24h | Aceito |
| [ADR-005](ADR-005-entrega-at-least-once-com-x-event-id.md) | Entrega at-least-once com `X-Event-Id` para deduplicação no cliente | Aceito |
| [ADR-006](ADR-006-reuso-dos-padroes-existentes.md) | Webhooks como módulo do OMS, reaproveitando os padrões existentes do projeto | Aceito |
| [ADR-007](ADR-007-payload-snapshot-na-insercao.md) | Payload enxuto renderizado como snapshot na inserção da outbox | Aceito |
