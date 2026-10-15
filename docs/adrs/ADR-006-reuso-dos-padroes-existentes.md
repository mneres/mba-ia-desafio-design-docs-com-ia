# ADR-006: Webhooks como módulo do OMS, reaproveitando os padrões existentes do projeto

## Status

Aceito (`[09:30] Larissa`)

## Data

Não informada na transcrição (reunião de quinta-feira, 09:00).

## Decisores

Larissa (Tech Lead), Bruno (Engenheiro Pleno, Pedidos), Diego (Engenheiro Sênior, Plataforma), Sofia (Engenheira de Segurança), Marcos (Product Manager).

## Contexto

O OMS já tem um padrão claro: cada domínio é um módulo em `src/modules/` com controller, service, repository, routes e schemas (`[09:27] Bruno`). Os módulos existentes (`auth`, `users`, `customers`, `products`, `orders`) são instanciados manualmente em `buildControllers()` (`src/app.ts`) e montados sob `/api/v1` em `src/routes/index.ts`.

Erros de domínio estendem `AppError` (`src/shared/errors/app-error.ts`), com subclasses em `src/shared/errors/http-errors.ts` e códigos como `INSUFFICIENT_STOCK` e `INVALID_STATUS_TRANSITION` (`[09:28] Bruno`). `src/middlewares/error.middleware.ts` converte `AppError`, `ZodError` e erros conhecidos do Prisma em resposta HTTP; o logger Pino (`src/shared/logger/index.ts`) é usado no projeto inteiro (`[09:29] Bruno`). A validação de entrada usa `validate()` (`src/middlewares/validate.middleware.ts`) com schemas Zod, e a autorização usa `authenticate` e `requireRole` (`src/middlewares/auth.middleware.ts`).

O time é pequeno (`[09:07] Diego`) e o worker deve usar "mesmo banco, mesma stack" (`[09:11] Diego`).

## Decisão

Reuso máximo do que já existe; webhooks é um módulo igual aos outros (`[09:30] Larissa`):

- **Módulo `src/modules/webhooks/` (a criar)** com controller, service, repository, routes e schemas (`[09:27] Bruno`), registrado em `buildControllers()` e em `buildApiRouter()`. A lógica do worker fica no mesmo módulo ([ADR-002](ADR-002-worker-separado-com-polling.md)).
- **Erros como subclasses de `AppError`** com códigos prefixados por `WEBHOOK_`, por exemplo `WEBHOOK_NOT_FOUND`, `WEBHOOK_INVALID_URL`, `WEBHOOK_SECRET_REQUIRED` (`[09:28] Bruno`, `[09:29] Larissa`), tratados pelo `error.middleware.ts` sem alteração (`[09:29] Bruno`).
- **Logger Pino existente**, sem biblioteca nova (`[09:29] Bruno`).
- **Schemas Zod** para validação, incluindo a recusa de URL `http` (`[09:23] Sofia`).
- **Chaves primárias UUID** nas tabelas novas, como em todos os modelos de entidade de `prisma/schema.prisma` (`[09:51] Larissa`).
- **Autorização com os middlewares existentes**: o CRUD de configuração aceita qualquer papel autenticado por enquanto (`[09:37] Sofia`); o replay da DLQ exige `ADMIN` via `requireRole` ([ADR-003](ADR-003-retry-com-backoff-exponencial-e-dlq.md), `[09:36] Larissa`).
- **`customer_id` vem do body ou do path, não do JWT** (`[09:32] Larissa`). O JWT atual identifica o usuário operador, não o cliente (`[09:32] Bruno`): `AuthUser` em `auth.middleware.ts` carrega só `id`, `email` e `role`, e o modelo `User` não tem relação com `Customer`.

Lacunas que a reunião deixou abertas:

- O nome do arquivo de processamento (`webhook.worker.ts` ou `webhook.processor.ts`) ficou em aberto (`[09:28] Bruno`).
- Marcos disse que "a gente tem usuários que representam o cliente" (`[09:32] Marcos`), mas o schema não liga `User` a `Customer`. Com qualquer papel autenticado podendo gerenciar webhooks de qualquer customer, a reunião não definiu como impedir que um usuário altere webhooks de outro cliente; Sofia indicou que a regra pode ser endurecida "mais pra frente" (`[09:37] Sofia`).

## Alternativas Consideradas

- **Extrair `customer_id` do JWT.** Proposta inicial de Marcos (`[09:31] Marcos`). Descartada porque o JWT é do usuário operador e não carrega cliente (`[09:32] Bruno`, `[09:32] Larissa`).
- **Serviço de webhooks separado, com stack própria (não discutida na reunião).** Isolaria a feature, mas contraria "mesmo banco, mesma stack" (`[09:11] Diego`) e duplicaria tratamento de erro, logging, auth e validação que já existem, num time pequeno (`[09:07] Diego`).

## Consequências

**Positivas**
- Respostas de erro no mesmo formato `{ error: { code, message, details } }` das demais rotas, sem tocar no `error.middleware.ts`.
- Logs com a mesma redação e o mesmo formato do resto do OMS.
- Curva de aprendizado baixa: quem conhece `orders` ou `customers` reconhece a estrutura do módulo.
- Nenhuma dependência nova no `package.json`.

**Negativas**
- O módulo herda as limitações atuais: só dois papéis (`ADMIN`, `OPERATOR`) e nenhum vínculo entre usuário e cliente, o que deixa o controle de acesso por customer em aberto.
- A feature fica acoplada ao deploy e às versões do monólito; evoluir o worker exige o mesmo ciclo de release da API.
- A redação do logger precisa ser estendida para secrets de webhook ([ADR-004](ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md)).

**Trade-off**

O time ganha velocidade e consistência ao seguir padrões já testados no OMS, e aceita herdar o modelo de autorização atual, que não isola dados por cliente.
