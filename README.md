# Da Reunião ao Documento: Webhooks de Notificação de Pedidos

Documentação do processo de produção do pacote de design docs do Sistema de Webhooks de Notificação de Pedidos. O enunciado original do desafio está no repositório base, [devfullcycle/mba-ia-desafio-design-docs-com-ia](https://github.com/devfullcycle/mba-ia-desafio-design-docs-com-ia), e pode ser lido localmente com `git show main:README.md`.

## Sobre o desafio

O ponto de partida era uma reunião técnica de cerca de 55 minutos (`TRANSCRICAO.md`) em que tech lead, PM, dois engenheiros e uma engenheira de segurança decidiram construir webhooks de saída sobre um Order Management System já em produção. Nada tinha sido registrado além da transcrição. A tarefa foi transformar essa conversa, junto com o código existente em `src/` e `prisma/`, num pacote de documentação acionável: ADRs para cada decisão, um RFC com a proposta e o que ficou em aberto, um FDD com o desenho de implementação, um PRD com a visão de produto e um Tracker que liga cada afirmação à sua origem.

A dificuldade real não estava em escrever, e sim em não escrever demais. A transcrição mistura decisões fechadas, ideias descartadas, itens adiados e detalhes deixados indefinidos, e uma IA tende a preencher lacunas com valores plausíveis. Por isso o processo foi organizado em torno de rastreabilidade: cada documento cita `[hh:mm] Nome` ou um caminho de arquivo real, o que a reunião não definiu é rotulado como hipótese, e o Tracker funciona como verificação contra alucinação. O código da aplicação foi usado apenas como referência e não foi alterado.

## Ferramentas de IA utilizadas

- **Claude Code (CLI), com o modelo Claude Opus 5.5.** Foi a ferramenta de produção de todos os documentos em `docs/`. Em cada etapa recebeu um dos prompts de `prompts/` e executou o ciclo completo:
  - leitura da transcrição, do código e dos documentos anteriores;
  - escrita do documento;
  - verificação mecânica com comandos de shell e scripts Python ad hoc (citações conferidas contra a transcrição, caminhos conferidos no repositório, links, travessões, contagem de itens);
  - relatório final com hipóteses, lacunas e pendências.

  Esses relatórios foram a base da minha revisão antes de passar à etapa seguinte. As instruções permanentes do projeto ficam em `CLAUDE.md`, que o Claude Code carrega em toda sessão.
- **Instruções para outros agentes.** O repositório também tem `AGENTS.md`, com o mesmo conteúdo de `CLAUDE.md`, para agentes como o Codex. Não há registro no repositório de que outro agente tenha gerado algum dos documentos entregues, por isso não o listo como ferramenta utilizada.

## Workflow adotado

A sequência abaixo foi reconstruída a partir do histórico do git (`git log`) e da sessão de produção.

1. **Preparação das instruções.** Antes de gerar qualquer documento, escrevi um prompt gerador/revisor por documento em `prompts/`, versionados no commit `a3d6b46` (2026-10-15). Cada prompt trata três situações:
   - documento placeholder: cria;
   - documento com conteúdo: revisa no lugar;
   - documento já correto: "sem alterações".

   Assim, rodar de novo converge em vez de duplicar seções. Em seguida, `CLAUDE.md` e `AGENTS.md` (commit `e1a2930`) fixaram as convenções transversais:
   - citação `[hh:mm] Nome` com um único falante;
   - precedência transcrição/código > ADR > RFC > FDD;
   - rótulo `(hipótese)`;
   - marcação `(novo)` para arquivos a criar;
   - colunas fixas do Tracker;
   - a ambiguidade conhecida do número de tentativas de retry.
2. **ADRs primeiro** (`prompts/adrs-generator.md`). Os ADRs são a base dos outros documentos, então vieram antes. O prompt pede um inventário das decisões fechadas na transcrição, com o falante que fechou cada uma, e respeita a classificação da própria reunião. Por exemplo, Sofia disse que o TLS "nem é decisão arquitetural" (`[09:23] Sofia`) e Larissa tratou o limite de 64KB como requisito não funcional (`[09:24] Larissa`), então nenhum dos dois ganhou ADR próprio. Resultado: 7 ADRs, cobrindo as 6 decisões exigidas mais o snapshot do payload, fechado depois do encerramento formal da call (`[09:52] Bruno`).
3. **RFC** (`prompts/rfc-generator.md`). O RFC consolida a proposta sobre os ADRs, com 8 alternativas descartadas na reunião e as questões em aberto. O prompt separa as questões levantadas na reunião das lacunas encontradas ao escrever os ADRs, e impõe o limite de 700 a 2.600 palavras. O documento ficou com 2.432 palavras na iteração 1 e 2.473 após a revisão da iteração 2.
4. **FDD** (`prompts/fdd-generator.md`). O FDD desce ao nível de implementação: fluxos numerados, 7 endpoints, matriz de erros `WEBHOOK_*`, resiliência, observabilidade e 18 arquivos existentes na seção de integração. Antes de escrever, li padrões concretos do código: `validate.middleware.ts`, `response.ts`, `customer.controller.ts`, `user.routes.ts` e `tests/setup.ts`. Assim cada hipótese cita o arquivo cujo padrão segue.
5. **PRD** (`prompts/prd-generator.md`). O PRD veio por último entre os documentos grandes, como consolidação. Traduz as decisões para termos de produto e lista 8 itens fora de escopo, todos com fala de origem.
6. **Tracker** (`prompts/tracker-generator.md`). Montei o Tracker com os quatro documentos prontos. Um script Python conferiu cada linha:
   - 6 colunas por linha;
   - Fonte `TRANSCRICAO` ou `CODIGO`;
   - cada `[hh:mm] Nome:` existe na transcrição com aquele falante;
   - cada caminho existe no repositório;
   - nenhum ID duplicado.

   O Tracker não aceita caminhos marcados como `(novo)` como fonte de código. Na iteração 1, resultado de 415 linhas, 99,0% de cobertura, 82,7% de linhas da transcrição e 72 de código, com 4 afirmações sem origem.
7. **README por último.** Este README descreve o processo, então só podia ser escrito depois que todas as etapas anteriores existiam. O prompt `prompts/readme-generator.md` exige reconstruir a jornada a partir de evidências (git, prompts, relatórios) e não de um fluxo idealizado. A iteração 1 foi fechada no commit `8d5d7a3`.
8. **Iteração 2: os mesmos prompts em modo de revisão.** Rodei de novo a cadeia completa (ADRs, RFC, FDD, PRD, Tracker e este README) sobre os documentos já existentes. Desta vez cada prompt entrou no ramo "documento com conteúdo": releitura integral, comparação com as fontes e com o que as etapas posteriores tinham descoberto, e correção no lugar, sem duplicar seções. Uma mudança num ADR foi propagada para o RFC, o FDD e o PRD na mesma rodada. O Tracker terminou com 425 linhas, 100% de cobertura, 81,6% de linhas da transcrição, 78 de código e nenhum item sem origem.
9. **Revisão final** (`prompts/final-review.md`). É uma auditoria somente leitura, critério a critério, com veredito binário. Ela roda depois deste README, porque também avalia o README.

A ordem real seguiu a sugerida pelo enunciado. A dependência entre etapas é explícita nos prompts: quando um documento muda, os que dependem dele devem ser regenerados. Uma mudança em ADR, por exemplo, afeta RFC, FDD, PRD, Tracker e README.

**Como inconsistências foram tratadas.** A regra adotada foi reportar e não resolver em silêncio:

- **Número de tentativas.** "Total 5 tentativas" (`[09:48] Larissa`) contra cinco intervalos que somam "quase 15 horas" (`[09:17] Diego`). Ficou como questão em aberto em todos os documentos. O FDD adota uma leitura rotulada como hipótese e deixa o valor configurável.
- **`WEBHOOK_INVALID_URL`.** Divergências entre transcrição e código, como o código de erro citado por Bruno (`[09:28] Bruno`) contra a validação Zod pedida por Sofia, foram registradas como pendentes no FDD.

## Prompts customizados

Os prompts completos estão em `prompts/`. Abaixo, trechos dos que mais influenciaram o resultado.

### 1. Gerador de ADRs (trecho)

Arquivo completo: [`prompts/adrs-generator.md`](prompts/adrs-generator.md). Foi usado na primeira etapa e define o que conta como decisão. As regras "Respect the meeting's own classification" e "Gaps are named, not filled" foram as que mais pesaram: impediram ADRs para itens que a reunião classificou como validação ou requisito não funcional, e fizeram cada ADR terminar com lacunas explícitas em vez de valores inventados.

```markdown
## Principles

- **Nothing invented.** Every decision, alternative, driver and consequence must be traceable to the transcript (timestamp and speaker, for example `[09:17] Diego`) or to a real file in the code. Cite inline.
- **Real decisions only.** An ADR records something the team decided. An idea that was mentioned but not closed is not an ADR; it is a gap or an open question, recorded inside the related ADR.
- **Respect the meeting's own classification.** If the meeting explicitly said something is not an architectural decision (for example "this is just a schema validation" or "this is a non-functional requirement, not a separate decision"), do not give it its own ADR; record it inside the ADR it belongs to.
- **Alternatives: real first.** Prefer alternatives actually discussed and discarded in the meeting, with the reason given. When an ADR has no discussed alternative, you may add one plausible alternative, explicitly labeled as "not discussed in the meeting", with the reason it would not meet the requirements stated in the sources. Never present a plausible alternative as if it had been discussed.
- **Explicit trade-off.** Consequences must list positive and negative effects and end with an explicit trade-off: what the team gains and what it accepts in exchange.
- **Gaps are named, not filled.** When the meeting left a detail of the decision undefined, state exactly what is undefined and where it will be resolved (for example, the FDD or a review), instead of choosing a value.
- **Verified file paths.** Only cite as existing the files that exist. A file or module the decision will create (for example `src/modules/webhooks/`) must be marked explicitly as new (for example "(novo)" or "a criar").
```

### 2. Gerador do Tracker (trecho)

Arquivo completo: [`prompts/tracker-generator.md`](prompts/tracker-generator.md). Foi usado depois dos quatro documentos. É o mecanismo contra alucinação: separa hipóteses com padrão de código (ganham linha `CODIGO`) de hipóteses puras (ficam fora da contagem) e de afirmações sem origem (contam contra a cobertura). Proíbe forçar uma origem para bater a meta.

```markdown
## Princípios

- **Nada inventado.** Cada linha aponta para um trecho que existe e que sustenta o item. Um timestamp próximo, mas de outra fala, é um erro. Um caminho de arquivo plausível, mas inexistente, é um erro.
- **Falante correto.** O nome em `[hh:mm] Nome` é de quem disse aquilo naquele minuto. Quando várias pessoas falam no mesmo minuto, confira qual delas.
- **Hipóteses.** Uma hipótese rotulada que cita o arquivo de código cujo padrão segue ganha linha com Fonte `CODIGO` e esse caminho, depois de conferir que o arquivo existe e contém o padrão. Hipóteses sem padrão de código por trás não ganham linha, **não entram** na contagem de itens identificáveis e são listadas no relatório final como hipóteses puras.
- **Itens sem origem não ganham linha.** Qualquer afirmação que não é hipótese rotulada e não tem respaldo na transcrição nem no código fica fora da tabela, **conta** como item identificável (reduz a cobertura) e é listada no relatório final como item sem origem. Não altere os outros documentos para corrigir isso; apenas reporte.
- **Caminhos de arquivos novos.** Um caminho marcado nos documentos como novo ("(novo)", "a criar") não existe no repositório e não pode ser Localização de linha `CODIGO`.
```

### 3. Revisão final dos critérios de aceite (trecho)

Arquivo completo: [`prompts/final-review.md`](prompts/final-review.md). Fecha o processo com uma auditoria que não pode alterar arquivos nem flexibilizar critérios.

```markdown
3. Não considere um critério atendido sem evidências concretas. Quando necessário, abra os arquivos referenciados, confira seu conteúdo e valide os caminhos no repositório.
5. Para referências ao código, confirme que os arquivos, módulos, classes e caminhos mencionados como existentes realmente existem no repositório. Caminhos de arquivos novos, que a feature vai criar (por exemplo em `src/modules/webhooks/`), só são aceitos se o documento os marcar explicitamente como novos ("(novo)", "a criar" ou uma seção de arquivos novos). Um caminho inexistente apresentado como se já existisse é falha.
6. Para requisitos quantitativos, conte os itens explicitamente. Não utilize estimativas ou interpretações subjetivas para declarar um critério como atendido.
9. Se não for possível verificar um critério por falta de evidências ou de arquivos necessários, marque-o como **FALHOU** e explique a limitação. Não presuma que o requisito foi atendido.
11. Não modifique os arquivos do repositório durante a revisão. O objetivo desta etapa é exclusivamente auditar e reportar os resultados.
```

## Iterações e ajustes

O histórico do git não registra quantas versões cada prompt teve antes do commit `a3d6b46`; os sete prompts entraram num único commit, e esse número não pode ser reconstruído. Na produção dos documentos houve **2 iterações principais**:

- **Iteração 1** (commit `8d5d7a3`): seis ciclos, um por prompt (ADRs, RFC, FDD, PRD, Tracker, README), cada um com geração, verificação mecânica e uma rodada de correções.
- **Iteração 2**: a mesma cadeia em modo de revisão, motivada pelas pendências que a iteração 1 deixou registradas.

Nenhum documento foi revertido. Os ajustes concretos foram estes:

1. **Paráfrase que exagerava a fala e decisor ausente (iteração 1, ADRs).**
   - *Problema:* a primeira versão do ADR-003 dizia que Marcos aceitou que um cliente fora do ar por 15 horas "perca eventos". O ADR-007 listava Sofia entre os decisores do snapshot do payload.
   - *Por que estava errado:* Marcos disse apenas que um cliente fora do ar por 15 horas "já tá com problema sério dele" (`[09:17] Marcos`). Sofia saiu da call às 09:50, antes do snapshot ser decidido (`[09:52] Bruno`).
   - *Correção:* reescrevi a frase com a citação literal. Sofia ficou registrada só como participante da definição do limite de tamanho.
   - *No mesmo ciclo:* uma citação duplicada no ADR-004, e uma menção a `webhook_outbox` no ADR-001 sem o marcador `(nova)`, encontrada pela checagem de arquivos novos sem marcação.
2. **Tabela inventada no RFC (iteração 1).**
   - *Problema:* a seção de impacto do RFC listava uma "tabela de histórico de entregas" como mudança no schema.
   - *Por que estava errado:* a reunião pediu o histórico (`[09:34] Marcos`), mas nunca disse como ele seria armazenado.
   - *Correção:* a frase passou a dizer que a forma de armazenamento não foi definida e fica para o FDD. No FDD, a tabela `webhook_deliveries` aparece rotulada como hipótese.
3. **Conflito entre transcrição e código descoberto no FDD (iteração 1).**
   - *Problema:* ao montar a matriz de erros, a leitura de `src/middlewares/validate.middleware.ts` mostrou que todo `ZodError` vira `VALIDATION_ERROR`.
   - *Por que importa:* se a regra https fica no schema Zod, como pediu Sofia (`[09:23] Sofia`), o código `WEBHOOK_INVALID_URL` citado por Bruno (`[09:28] Bruno`) nunca chega ao cliente.
   - *Decisão:* em vez de escolher um lado, o FDD documenta a resposta efetiva (`400 VALIDATION_ERROR`) e marca a decisão como pendente para a revisão. A mesma checagem encontrou o entry-point do worker, um arquivo novo que ainda não existe, citado sem `(novo)` na tabela de integração, e corrigi.
4. **Afirmações sem origem detectadas pelo Tracker (iterações 1 e 2).**
   - *Problema:* o mapeamento linha a linha encontrou quatro afirmações que soavam como fatos mas eram conclusões de desenho, sem fala nem arquivo que as sustentasse. Três estão na seção 10 do FDD ("nenhum endpoint existente muda de contrato", "a migração só cria tabelas", "o worker pode ser implantado depois da API"). A quarta é um critério de aceite equivalente na seção 11 do PRD.
   - *Tratamento na iteração 1:* seguindo o prompt, ficaram sem linha, contaram contra a cobertura (99,0% em vez de 100%) e foram listadas em "Itens sem origem" no Tracker.
   - *Correção na iteração 2:* a revisão do FDD transformou as três garantias em hipóteses ligadas a código, por exemplo "seguindo `src/routes/index.ts`" e "seguindo `prisma/schema.prisma`, como `Customer.orders`", ou em afirmações com citação (`[09:40] Bruno`, `[09:06] Diego`). O critério do PRD foi reescrito. Na nova rodada do Tracker as quatro ganharam linha e a cobertura chegou a 100%.
5. **Benefício que contradizia a própria lacuna (iteração 2, ADR-004 e PRD).**
   - *Problema:* o ADR-004 listava entre as consequências positivas que a rotação permite trocar a secret "sem janela de eventos rejeitados". O próprio ADR registra como lacuna que a reunião não definiu com qual secret o worker assina durante as 24 horas. O PRD repetia a promessa ("sem perder notificações").
   - *Por que estava errado:* o benefício dependia exatamente do ponto em aberto. A transcrição só diz que a antiga "fica válida por 24 horas" para o cliente "ter tempo de migrar" (`[09:21] Sofia`).
   - *Correção:* o ADR e o PRD passaram a afirmar só o que a fala sustenta, e o ADR deixa explícito que o efeito real depende da lacuna.
6. **Requisito não funcional que contradizia o FDD (iteração 2, PRD).**
   - *Problema:* o NFR-11 do PRD prometia "nenhuma quebra de contrato" nas APIs existentes.
   - *Por que estava errado:* o FDD mostra que `PATCH /api/v1/orders/:id/status` passa a poder falhar quando a gravação da outbox falha, porque a outbox participa da transação (`[09:40] Bruno`). Para o produto essa mudança é relevante: operadores podem ver mudanças de status falharem por causa da feature.
   - *Correção:* o requisito passou a dizer que as rotas existentes não mudam (hipótese) e a nomear essa única mudança de comportamento.
7. **Conflito propagado para o lugar certo (iteração 2, ADR-006 e RFC).**
   - *Problema:* o conflito de `WEBHOOK_INVALID_URL` encontrado no FDD (item 3) só existia no FDD. Ele não aparecia no ADR-006, que lista o código e a validação Zod lado a lado, nem nas questões em aberto do RFC.
   - *Correção:* a revisão registrou o conflito como lacuna no ADR-006 e como questão 10 no RFC. O FDD passou a apontar para os dois, e o Tracker ganhou as linhas `ADR-006-QA-03` e `RFC-QA-10`.

## Como navegar a entrega

Ordem de leitura sugerida, da origem à rastreabilidade:

1. **`TRANSCRICAO.md`**: a fonte primária. Vale ler inteira; as decisões finais estão no resumo de Larissa (`[09:48]`) e em dois refinamentos depois do encerramento formal: PKs UUID (`[09:51]`) e snapshot do payload (`[09:52]`).
2. **`docs/adrs/`**: as 7 decisões, com o índice em `docs/adrs/README.md`. Procure em cada ADR a seção "Alternativas Consideradas", com as alternativas não discutidas rotuladas, o trade-off explícito ao fim de "Consequências" e as lacunas nomeadas em "Decisão".
   - `docs/adrs/ADR-001-outbox-no-mysql.md`
   - `docs/adrs/ADR-002-worker-separado-com-polling.md`
   - `docs/adrs/ADR-003-retry-com-backoff-exponencial-e-dlq.md`
   - `docs/adrs/ADR-004-assinatura-hmac-sha256-com-secret-por-endpoint.md`
   - `docs/adrs/ADR-005-entrega-at-least-once-com-x-event-id.md`
   - `docs/adrs/ADR-006-reuso-dos-padroes-existentes.md`
   - `docs/adrs/ADR-007-payload-snapshot-na-insercao.md`
3. **`docs/RFC.md`**: a proposta em nível de arquitetura, construída sobre os ADRs e com link para eles. Pontos de atenção: a tabela de garantias, as 8 alternativas descartadas e a separação entre questões levantadas na reunião e lacunas encontradas na consolidação.
4. **`docs/FDD.md`**: o desenho de implementação, que depende dos ADRs e do RFC. Pontos de atenção:
   - os fluxos 4.1 a 4.4;
   - os contratos da seção 5;
   - a matriz de erros;
   - a seção 9, "Integração com o sistema existente", que liga cada arquivo real de `src/`, `prisma/` e `tests/` ao que muda nele;
   - os itens marcados `(hipótese)`.
5. **`docs/PRD.md`**: a visão de produto, consolidando os documentos anteriores. Pontos de atenção: objetivos com meta numérica, os 15 requisitos funcionais com fonte e a tabela "Fora de escopo".
6. **`docs/TRACKER.md`**: a rastreabilidade de todos os documentos acima, com cada item ligado a `[hh:mm] Nome` ou a um caminho de arquivo, mais a seção de cobertura e as listas de hipóteses puras e de itens sem origem.
7. **`prompts/`**: os prompts usados em cada etapa, para reproduzir ou revisar o processo.
8. **`CLAUDE.md` e `AGENTS.md`**: as convenções que valeram para todos os documentos.

O código em `src/`, `prisma/` e `tests/` é o sistema de referência e não foi modificado.
