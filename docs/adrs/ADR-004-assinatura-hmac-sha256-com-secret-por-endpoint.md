# ADR-004: Assinatura HMAC-SHA256 com secret por endpoint e rotação com grace period de 24h

## Status

Aceito (`[09:22] Sofia`)

## Data

Não informada na transcrição (reunião de quinta-feira, 09:00).

## Decisores

Sofia (Engenheira de Segurança), Bruno (Engenheiro Pleno, Pedidos), Diego (Engenheiro Sênior, Plataforma), Larissa (Tech Lead).

## Contexto

Os webhooks são apenas de saída: a plataforma envia, os clientes recebem (`[09:02] Marcos`). Os eventos carregam dados de pedidos para um endpoint fora da infraestrutura da empresa, e o cliente precisa conseguir validar que a requisição veio da plataforma e que o payload não foi adulterado no caminho (`[09:19] Sofia`).

Hoje o projeto não tem assinatura de requisições de saída. A única autenticação existente é a de entrada, por JWT (`src/middlewares/auth.middleware.ts`, segredo `JWT_SECRET` em `src/config/env.ts`). O logger Pino (`src/shared/logger/index.ts`) redige `authorization`, `cookie`, `password`, `passwordHash`, `token` e `accessToken`, sem nenhum campo de secret de webhook. Diego lembrou que um cliente já vazou uma secret em log da própria aplicação (`[09:22] Diego`).

## Decisão

- **HMAC-SHA256 sobre o corpo do request**, enviado no header `X-Signature`, para o cliente verificar do lado dele (`[09:20] Sofia`, fechado em `[09:22] Sofia`).
- **Uma secret por endpoint de webhook**, nunca uma secret global da plataforma (`[09:21] Sofia`). A configuração do webhook guarda url, secret, customer_id e estado ativo (`[09:21] Bruno`, confirmado em `[09:21] Sofia`). A secret é gerada pela plataforma e devolvida na criação do webhook (`[09:31] Marcos`).
- **Rotação pela API**: o cliente pede uma nova secret; a antiga continua válida em paralelo por 24 horas e depois é invalidada (`[09:21] Sofia`).
- **Header `X-Timestamp`** com o momento do envio, para o cliente poder detectar replay attack se quiser (`[09:44] Diego`).
- **TLS obrigatório**: URLs `http` são recusadas com erro de validação. A reunião classificou isso como validação de schema Zod, não como decisão arquitetural separada (`[09:23] Sofia`); fica registrado aqui por ser parte do mesmo modelo de segurança.
- **Revisão de segurança** do código de HMAC e de geração de secret por Sofia, com pelo menos dois dias úteis antes do deploy (`[09:46] Sofia`).

Lacunas que a reunião deixou abertas:

- Durante as 24 horas de grace period, com qual secret o worker assina: só a nova, ou as duas (por exemplo, duas assinaturas no header). "A antiga fica válida" (`[09:21] Sofia`) não diz como.
- Se `X-Timestamp` entra no conteúdo assinado. A decisão fala em HMAC "sobre o corpo do request" (`[09:22] Sofia`); sem incluir o timestamp, ele pode ser alterado sem invalidar a assinatura.
- Como a secret é armazenada no banco. Como o worker precisa da secret original para assinar, um hash de mão única não serve; a reunião não definiu se haverá criptografia em repouso.
- Inclusão dos campos de secret nos `redactPaths` de `src/shared/logger/index.ts`.

## Alternativas Consideradas

- **Secret global da plataforma, compartilhada por todos os clientes.** Descartada: "se vaza uma, vaza tudo" (`[09:21] Sofia`).
- **Secret fixa, sem rotação.** Descartada implicitamente pela exigência de rotação, motivada pelo histórico de vazamento em log de cliente (`[09:22] Diego`).
- **Token estático em header, sem assinatura do payload (não discutida na reunião).** Autenticaria a origem, mas não atenderia ao requisito de detectar adulteração do payload no caminho (`[09:19] Sofia`).

## Consequências

**Positivas**
- O cliente consegue verificar origem e integridade de cada entrega com bibliotecas padrão de mercado (`[09:20] Sofia`).
- O vazamento de uma secret afeta apenas um endpoint.
- A rotação com grace period permite trocar a secret sem janela de eventos rejeitados pelo cliente.

**Negativas**
- O worker precisa ler a secret em texto recuperável, o que aumenta a superfície de exposição no banco e nos logs.
- A rotação adiciona estado (secret antiga, prazo de expiração) e regras de transição à configuração do webhook.
- A verificação fica a cargo do cliente; quem não verificar a assinatura não ganha a proteção.
- O deploy depende da janela de revisão de segurança (`[09:46] Sofia`).

**Trade-off**

O time aceita guardar e gerenciar secrets recuperáveis por endpoint, com rotação, em troca de isolar o impacto de vazamentos e dar ao cliente garantia de origem e integridade de cada evento.
