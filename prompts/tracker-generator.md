# Prompt para geração do Tracker de Rastreabilidade

Prompt para Geração do Tracker (`docs/TRACKER.md`)

---

## Objetivo

Criar ou revisar o arquivo `docs/TRACKER.md`: uma tabela Markdown que mapeia cada item registrado nos documentos do pacote (PRD, RFC, FDD e ADRs) à sua origem na transcrição da reunião ou no código.

O tracker funciona como referência cruzada. Ele permite que qualquer leitor entenda de onde veio cada decisão, requisito ou restrição, e garante que a documentação está alinhada com o que foi efetivamente discutido e com o que existe no código.

O tracker não é um documento padrão de mercado. É uma exigência específica deste desafio, e seu papel principal é proteger a documentação contra alucinações: um item que não encontra origem na transcrição nem no código é um sinal de conteúdo inventado.

- **Se `docs/TRACKER.md` não existir ou for apenas um placeholder:** gere o tracker completo.
- **Se já existir com conteúdo:** revise o documento inteiro e atualize com todas as correções necessárias (linhas faltando, linhas de itens que não existem mais nos documentos, IDs duplicados, timestamps ou falantes errados, caminhos de arquivo inexistentes, resumos desatualizados).

O tracker é escrito em português.

---

## Papel

Você é um assistente especializado em **rastreabilidade de documentação**.

Seu papel é:

- Inventariar os itens identificáveis de cada documento do pacote.
- Encontrar, para cada item, a origem real na transcrição ou no código, e conferir que a origem diz de fato o que o item afirma.
- Registrar cada mapeamento como uma linha da tabela, no formato obrigatório.
- Apontar no relatório final os itens que não têm origem, em vez de inventar uma.

---

## Fontes de Informação

Leia, antes de criar ou alterar o arquivo:

1. **Instruções do projeto** (`CLAUDE.md`, `README.md`): requisitos e critérios de aceite do tracker.
2. **Os documentos a rastrear:** `docs/PRD.md`, `docs/RFC.md`, `docs/FDD.md` e todos os arquivos `docs/adrs/ADR-*.md`. Documento que ainda for placeholder não tem itens; registre isso no relatório.
3. **A transcrição** `TRANSCRICAO.md`, na íntegra: é a fonte de cada timestamp e falante.
4. **O código** (`src/`, `prisma/`, `tests/`, `package.json`): é a fonte dos itens que descrevem o sistema existente e os pontos de integração.
5. **O `docs/TRACKER.md` atual**, se tiver conteúdo.

---

## Formato Obrigatório da Tabela

```markdown
| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
| --- | --- | --- | --- | --- | --- |
```

- **ID:** identificador único do item (ex.: `PRD-FR-01`, `RFC-ALT-02`, `FDD-CONTRATO-03`, `ADR-002`).
- **Documento:** arquivo onde o item aparece (`docs/PRD.md`, `docs/RFC.md`, `docs/FDD.md`, `docs/adrs/ADR-002-...md`), com o nome real do arquivo.
- **Tipo:** Requisito Funcional, Requisito Não Funcional, Decisão, Restrição, Trade-off, Alternativa Descartada, Questão em Aberto, Fora de Escopo, Risco, Contrato, Erro, Métrica, Integração, entre outros.
- **Conteúdo (resumo):** descrição de uma linha do item.
- **Fonte:** exatamente `TRANSCRICAO` ou `CODIGO`.
- **Localização:** para `TRANSCRICAO`, timestamp e nome do falante no formato `[hh:mm] Nome` (ex.: `[09:17] Diego`). Para `CODIGO`, caminho do arquivo (ex.: `src/modules/orders/order.service.ts`).

Não acrescente nem remova colunas. Explicações, legendas e métricas de cobertura ficam fora da tabela.

---

## Princípios

- **Nada inventado.** Cada linha aponta para um trecho que existe e que sustenta o item. Um timestamp próximo, mas de outra fala, é um erro. Um caminho de arquivo plausível, mas inexistente, é um erro.
- **Uma origem por linha.** A tabela tem uma única Fonte por linha. Escolha a origem principal do item: a fala onde foi decidido ou pedido (para requisitos, decisões, alternativas, questões em aberto, fora de escopo) ou o arquivo de código (para itens que descrevem o sistema atual ou o ponto de integração). Se um item depende de mais de uma fala, use a fala que fecha a decisão.
- **Falante correto.** O nome em `[hh:mm] Nome` é de quem disse aquilo naquele minuto. Quando várias pessoas falam no mesmo minuto, confira qual delas.
- **IDs estáveis e únicos.** Use o prefixo do documento e o tipo do item (ver convenção abaixo). Se o documento já numera o item (por exemplo `RF-03` no PRD), reaproveite a numeração dele no ID. Em uma revisão, mantenha os IDs que continuam válidos; não renumere a tabela inteira sem necessidade.
- **Hipóteses.** Uma hipótese rotulada que cita o arquivo de código cujo padrão segue (ex.: "(hipótese, seguindo `src/shared/errors/http-errors.ts`)") ganha linha com Fonte `CODIGO` e esse caminho, depois de conferir que o arquivo existe e contém o padrão. Hipóteses sem padrão de código por trás não ganham linha, **não entram** na contagem de itens identificáveis e são listadas no relatório final como hipóteses puras.
- **Itens sem origem não ganham linha.** Qualquer afirmação que não é hipótese rotulada e não tem respaldo na transcrição nem no código fica fora da tabela, **conta** como item identificável (reduz a cobertura) e é listada no relatório final como item sem origem. Não altere os outros documentos para corrigir isso; apenas reporte.
- **Caminhos de arquivos novos.** Um caminho marcado nos documentos como novo ("(novo)", "a criar") não existe no repositório e não pode ser Localização de linha `CODIGO`. Rastreie o item pela fala que o decidiu (ex.: o módulo `src/modules/webhooks` em `[09:48] Larissa`).
- **Ordem previsível.** Agrupe as linhas por documento (PRD, RFC, FDD, ADRs em ordem numérica) e, dentro de cada documento, na ordem em que os itens aparecem.
- **Execução repetível.** Rodar o prompt de novo sobre fontes iguais não deve duplicar linhas nem mudar o que já está correto.
- **Não modifique** código, PRD, RFC, FDD ou ADRs. A única saída é `docs/TRACKER.md`.

---

## Convenção de IDs

Formato: `<DOCUMENTO>-<TIPO>-<NN>`, com `NN` sequencial dentro de cada par documento/tipo. Sugestão de tipos:

| Prefixo de tipo | Uso |
| --- | --- |
| `FR` / `NFR` | Requisito funcional / não funcional |
| `OBJ` | Objetivo ou métrica de sucesso |
| `ESC` | Item de escopo ou fora de escopo |
| `DEC` | Decisão |
| `RES` | Restrição |
| `TO` | Trade-off |
| `ALT` | Alternativa considerada e descartada |
| `QA` | Questão em aberto |
| `RISCO` | Risco |
| `CONTRATO` | Contrato público (endpoint, payload, header) |
| `ERRO` | Item da matriz de erros |
| `FLUXO` | Passo ou regra de fluxo |
| `OBS` | Métrica, log ou trace de observabilidade |
| `INT` | Ponto de integração com o código existente |
| `DEP` | Dependência |
| `CA` | Critério de aceite |

Para os ADRs, use o número do ADR como ID da decisão principal (`ADR-002`) e sufixos para os demais itens do mesmo ADR (`ADR-002-ALT-01`, `ADR-002-TO-01`).

---

## Processo de Elaboração

1. **Inventário.** Percorra cada documento e liste os itens identificáveis: requisitos funcionais e não funcionais, objetivos e metas, itens de escopo e fora de escopo, decisões, restrições, trade-offs, alternativas descartadas, questões em aberto, riscos, contratos públicos, códigos de erro, parâmetros de resiliência, itens de observabilidade, pontos de integração, dependências e critérios de aceite. Um item é qualquer afirmação que alguém poderia perguntar "de onde veio isso?". Guarde a contagem por documento; ela é a base do cálculo de cobertura.
2. **Mapeamento.** Para cada item, encontre a origem:
    - Se o documento já cita a fonte inline (ex.: `` `[09:04] Bruno` `` ou um caminho de arquivo), use-a como ponto de partida, mas confira na transcrição ou no código.
    - Se não cita, procure na transcrição pelo tema e confirme a fala exata.
    - Para itens sobre o sistema existente, confirme o arquivo no repositório.
3. **Verificação.** Para cada linha:
    - O timestamp existe na transcrição, e o falante daquele minuto é o informado.
    - A fala citada sustenta o resumo da linha.
    - O caminho de arquivo existe no repositório e contém o que o item descreve.
    - O caminho do documento na coluna Documento existe.
4. **Revisão (quando o tracker já existe).** Compare a tabela atual com o inventário: acrescente as linhas faltantes, remova as de itens que não existem mais, corrija localizações, falantes, tipos e resumos, elimine IDs duplicados. Preserve as linhas que continuam corretas.
5. **Cobertura.** Calcule:
    - **Cobertura:** itens com linha / itens identificáveis, por documento e no total. Meta: pelo menos 80%.
    - **Proporção TRANSCRICAO:** linhas com Fonte `TRANSCRICAO` e timestamp válido / total de linhas. Meta: pelo menos 70%.
    - **Linhas CODIGO:** quantidade de linhas com Fonte `CODIGO` e caminho real. Meta: pelo menos 5.

    Se uma meta não for atingida, volte ao inventário e procure itens rastreáveis que ficaram de fora. Não force uma origem para atingir a meta. Atenção ao equilíbrio: cada linha `CODIGO` (inclusive as de hipóteses derivadas de código) reduz a proporção `TRANSCRICAO`. Quando um item tem origem tanto numa fala quanto no código, prefira a fala, exceto para itens que descrevem o sistema existente ou um ponto de integração.

---

## Esqueleto do Tracker (modelo de saída)

```markdown
# Tracker de Rastreabilidade

Mapeia cada item registrado em `docs/PRD.md`, `docs/RFC.md`, `docs/FDD.md` e `docs/adrs/` à sua origem na transcrição (`TRANSCRICAO.md`) ou no código.

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
| --- | --- | --- | --- | --- | --- |
| PRD-FR-01 | docs/PRD.md | Requisito Funcional | [resumo de uma linha] | TRANSCRICAO | [hh:mm] Nome |
| FDD-INT-01 | docs/FDD.md | Integração | [resumo de uma linha] | CODIGO | src/[caminho real] |
| ADR-001 | docs/adrs/ADR-001-[nome real].md | Decisão | [resumo de uma linha] | TRANSCRICAO | [hh:mm] Nome |

## Cobertura

| Documento | Itens identificáveis | Itens com linha | Cobertura |
| --- | --- | --- | --- |
| docs/PRD.md | [n] | [n] | [%] |
| docs/RFC.md | [n] | [n] | [%] |
| docs/FDD.md | [n] | [n] | [%] |
| docs/adrs/ | [n] | [n] | [%] |
| **Total** | [n] | [n] | [%] |

- Linhas com Fonte TRANSCRICAO: [n] de [total] ([%])
- Linhas com Fonte CODIGO: [n]
```

---

## Critérios de Aceite

Antes de finalizar, valide todos os itens abaixo. Todos são obrigatórios.

- [ ] O arquivo `docs/TRACKER.md` existe e segue o formato de tabela definido acima
- [ ] Pelo menos 80% dos itens identificáveis dos documentos têm linha correspondente
- [ ] Pelo menos 70% das linhas têm Fonte = `TRANSCRICAO` com timestamp válido no formato `[hh:mm] Nome`
- [ ] Pelo menos 5 linhas têm Fonte = `CODIGO` com caminho de arquivo real
- [ ] Nenhum ID duplicado
- [ ] Todo timestamp existe na transcrição com o falante informado
- [ ] Todo caminho de arquivo citado existe no repositório
- [ ] Nenhum arquivo além de `docs/TRACKER.md` foi alterado

Se algum critério não puder ser atendido por falta de evidência, não invente origens nem declare o critério como atendido.

---

## Validação e Relatório Final

Depois de criar ou atualizar o tracker:

1. Releia `docs/TRACKER.md` por inteiro.
2. Confira que todas as linhas têm exatamente 6 colunas e Fonte igual a `TRANSCRICAO` ou `CODIGO`.
3. Confira cada `[hh:mm] Nome` contra a transcrição, por exemplo procurando a linha `[hh:mm] Nome:` em `TRANSCRICAO.md`.
4. Confira cada caminho de arquivo (Documento e Localização) no repositório.
5. Recalcule as três metas de cobertura e atualize a seção de cobertura.
6. Confira o diff para garantir que apenas `docs/TRACKER.md` mudou.

Entregue um relatório curto com:

- **Ação:** tracker criado, atualizado ou sem alterações necessárias.
- **Documentos rastreados:** quais tinham conteúdo e quais ainda eram placeholder.
- **Correções feitas** (em revisão): linhas adicionadas, removidas e corrigidas.
- **Cobertura:** os três números contra as metas.
- **Itens sem origem:** afirmações dos documentos que não encontraram respaldo na transcrição nem no código, com documento e seção, para correção nos próprios documentos.
- **Critérios de aceite:** quais passaram e quais ficaram bloqueados.
