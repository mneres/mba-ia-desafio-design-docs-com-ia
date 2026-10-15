# Prompt: Revisão dos Critérios de Aceite

Revise o repositório item por item, verificando rigorosamente todos os critérios de aceite definidos abaixo.

## Instruções de execução

1. **Avalie cada critério individualmente**, seguindo a ordem apresentada neste documento. Todos os critérios são obrigatórios.
2. Inspecione os arquivos do repositório, incluindo o código-fonte, a transcrição da reunião e os documentos existentes, para verificar cada item. O `README.md` da raiz já foi substituído pela documentação do processo; se precisar consultar o enunciado original, use `git show main:README.md`. As listas de seções obrigatórias de cada documento estão reproduzidas abaixo, junto de cada critério.
3. Não considere um critério atendido sem evidências concretas. Quando necessário, abra os arquivos referenciados, confira seu conteúdo e valide os caminhos no repositório.
4. Para requisitos relacionados à transcrição, confirme que os requisitos funcionais, decisões, alternativas descartadas, itens fora de escopo, riscos e questões em aberto estão fundamentados no que foi discutido na reunião.
5. Para referências ao código, confirme que os arquivos, módulos, classes e caminhos mencionados como existentes realmente existem no repositório. Caminhos de arquivos novos, que a feature vai criar (por exemplo em `src/modules/webhooks/`), só são aceitos se o documento os marcar explicitamente como novos ("(novo)", "a criar" ou uma seção de arquivos novos). Um caminho inexistente apresentado como se já existisse é falha.
6. Para requisitos quantitativos, conte os itens explicitamente. Não utilize estimativas ou interpretações subjetivas para declarar um critério como atendido.
7. Para verificar a cobertura e a consistência dos documentos, compare o PRD, a RFC, o FDD, as ADRs, o Tracker, o README, a transcrição e o código-fonte, conforme aplicável.
8. Marque cada critério como **PASSOU** ou **FALHOU**. Um critério só pode ser marcado como PASSOU quando todas as condições descritas naquele item forem satisfeitas.
9. Se não for possível verificar um critério por falta de evidências ou de arquivos necessários, marque-o como **FALHOU** e explique a limitação. Não presuma que o requisito foi atendido.
10. Não altere os critérios de aceite, não flexibilize suas exigências e não ignore itens por considerá-los secundários.
11. Não modifique os arquivos do repositório durante a revisão. O objetivo desta etapa é exclusivamente auditar e reportar os resultados.

## Formato do relatório

Apresente os resultados em uma tabela com as seguintes colunas:

| Documento/Área | Critério de aceite | Resultado | Evidência ou justificativa |
|---|---|---|---|
| PRD | Descrição do critério | PASSOU ou FALHOU | Evidência concreta, incluindo caminho e seção, quando aplicável |

Inclua uma linha para **cada critério de aceite**, sem agrupar itens diferentes em uma única avaliação.

Após a tabela, apresente:

### Resumo da revisão

- Total de critérios avaliados.
- Quantidade de critérios aprovados.
- Quantidade de critérios reprovados.
- Lista dos arquivos inspecionados.
- Principais problemas encontrados.

### Falhas identificadas

Para cada critério reprovado, informe:

- **Critério:** descrição exata do item que falhou.
- **Motivo:** explicação objetiva do problema.
- **Evidência:** arquivo, seção, caminho ou ausência de evidência que justifique a reprovação.
- **Correção necessária:** ação recomendada para atender ao critério, sem executar a alteração.

### Resultado final

Aplique obrigatoriamente a seguinte regra:

- **REVISÃO APROVADA:** somente se todos os critérios de aceite forem aprovados.
- **REVISÃO REPROVADA:** se pelo menos um critério falhar.

Se qualquer item falhar, declare explicitamente que a revisão foi reprovada e destaque todos os motivos da reprovação.

Se todos os itens passarem, declare explicitamente que a revisão foi aprovada e confirme que todos os critérios obrigatórios foram verificados e atendidos.

Não declare sucesso parcial como aprovação final. Não omita falhas nem considere o trabalho concluído enquanto houver critérios reprovados.

---

## Requisitos de referência (copiados do enunciado original)

Os critérios abaixo citam "requisito 1" a "requisito 6". Como o `README.md` da raiz já foi substituído, as listas desses requisitos estão reproduzidas aqui. Confira cada seção pelo nome; uma seção equivalente com outro nome só passa se o conteúdo corresponder claramente.

**Requisito 1: seções obrigatórias do PRD**
Resumo e contexto da feature; Problema e motivação; Público-alvo e cenários de uso; Objetivos e métricas de sucesso; Escopo (incluso e fora de escopo); Requisitos funcionais; Requisitos não funcionais; Decisões e trade-offs principais; Dependências; Riscos e mitigação; Critérios de aceitação; Estratégia de testes e validação.

**Requisito 2: seções obrigatórias do RFC**
Metadados (autor, status, data, revisores, com os participantes da reunião como revisores); Resumo executivo (TL;DR); Contexto e problema; Proposta técnica (sem descer ao detalhe do FDD); Alternativas consideradas; Questões em aberto; Impacto e riscos; Decisões relacionadas (links para os ADRs). O RFC deve ser conciso (2 a 4 páginas) e não duplicar o detalhamento do FDD.

**Requisito 3: seções obrigatórias do FDD**
Contexto e motivação técnica; Objetivos técnicos; Escopo e exclusões; Fluxos detalhados (criação do evento na outbox, processamento pelo worker, retry, DLQ); Contratos públicos (endpoints HTTP com payloads de exemplo, headers, status codes, semântica); Matriz de erros previstos com códigos `WEBHOOK_*`; Estratégias de resiliência (timeouts, retries, backoff, fallback); Observabilidade (métricas, logs, tracing); Dependências e compatibilidade; Critérios de aceite técnicos; Riscos e mitigação; Integração com o sistema existente.

**Requisito 4: decisões principais que os ADRs devem cobrir (pelo menos 5 das 6)**
Padrão Outbox no MySQL; Política de retry com backoff e DLQ; Autenticação HMAC-SHA256 com secret por endpoint; Garantia at-least-once com `X-Event-Id`; Worker em processo separado em polling; Reuso dos padrões existentes do projeto. Cada ADR deve ter pelo menos 1 alternativa e consequências positivas e negativas com trade-off explícito.

**Requisito 5: formato do Tracker**
Tabela com exatamente as colunas `| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |`. Fonte é `TRANSCRICAO` ou `CODIGO`. Localização é `[hh:mm] Nome` para `TRANSCRICAO` e caminho de arquivo para `CODIGO`.

**Requisito 6: seções obrigatórias do README**
Sobre o desafio; Ferramentas de IA utilizadas; Workflow adotado; Prompts customizados; Iterações e ajustes; Como navegar a entrega.

---

## Critérios de Aceite

A entrega é avaliada contra os critérios abaixo. Todos são obrigatórios.

### PRD (docs/PRD.md)

- ☐ Arquivo existe e está em Markdown
- ☐ Contém todas as seções obrigatórias listadas no requisito 1
- ☐ Identifica no mínimo 8 requisitos funcionais discutidos na reunião
- ☐ Inclui pelo menos 1 objetivo com métrica e meta quantitativa
- ☐ Seção "Fora de escopo" lista pelo menos 2 itens explicitamente descartados ou adiados na reunião
- ☐ Seção "Riscos" inclui pelo menos 2 riscos com probabilidade, impacto e mitigação

### RFC (docs/RFC.md)

- ☐ Arquivo existe e está em Markdown
- ☐ Contém todas as seções obrigatórias listadas no requisito 2
- ☐ Seção "Alternativas consideradas" lista pelo menos 2 alternativas descartadas na reunião, cada uma com o trade-off que motivou o descarte
- ☐ Seção "Questões em aberto" lista pelo menos 2 pontos adiados ou não decididos na reunião
- ☐ Referencia, com link, pelo menos 2 ADRs do pacote

### FDD (docs/FDD.md)

- ☐ Arquivo existe e está em Markdown
- ☐ Contém todas as seções obrigatórias listadas no requisito 3
- ☐ Seção "Contratos públicos" inclui pelo menos 4 endpoints HTTP com payload de exemplo (request e response) e status codes
- ☐ Matriz de erros usa códigos com prefixo WEBHOOK_
- ☐ Seção "Integração com o sistema existente" referencia pelo menos 4 caminhos de arquivo reais do código base
- ☐ Seção "Observabilidade" cita métricas, logs e tracing

### ADRs (docs/adrs/ADR-NNN-*.md)

- ☐ Pasta docs/adrs/ contém entre 5 e 8 arquivos no formato ADR-NNN-titulo-em-kebab-case.md
- ☐ Cada ADR contém as seções Status, Contexto, Decisão, Alternativas Consideradas, Consequências
- ☐ O conjunto cobre pelo menos 5 das 6 decisões principais listadas no requisito 4
- ☐ Pelo menos 1 ADR referencia explicitamente arquivos, módulos ou classes do código base

### Tracker (docs/TRACKER.md)

- ☐ Arquivo existe e segue o formato de tabela definido no requisito 5
- ☐ Pelo menos 80% dos itens identificáveis dos documentos têm linha correspondente
- ☐ Pelo menos 70% das linhas têm Fonte = TRANSCRICAO com timestamp válido no formato [hh:mm] Nome
- ☐ Pelo menos 5 linhas têm Fonte = CODIGO com caminho de arquivo real

### README (README.md)

- ☐ Contém todas as seções obrigatórias listadas no requisito 6
- ☐ Lista pelo menos 1 ferramenta de IA utilizada
- ☐ Mostra pelo menos 2 prompts customizados em blocos de código
- ☐ Descreve pelo menos 2 iterações ou ajustes concretos feitos durante a produção

### Consistência geral

- ☐ Nenhum requisito, decisão ou restrição registrada nos documentos contradiz a transcrição ou o código
- ☐ Nenhum arquivo de código mencionado nos documentos é inexistente no repositório

### Restrições do enunciado (verificação adicional)

- ☐ Nenhum arquivo de `src/`, `prisma/`, `tests/` ou de configuração foi alterado em relação à `main` (`git diff main --stat -- src prisma tests package.json tsconfig*.json`)
- ☐ `TRANSCRICAO.md` não foi alterado (`git diff main -- TRANSCRICAO.md` vazio)
- ☐ O RFC tem entre 2 e 4 páginas (aproximadamente 700 a 2.600 palavras) e não repete o detalhamento do FDD (schemas, contratos completos, passo a passo)
- ☐ Não há conteúdo duplicado entre os documentos: cada um fica na sua altura (PRD produto, RFC arquitetura, ADRs decisão, FDD implementação)
- ☐ Os arquivos citados na seção "Prompts customizados" do README (por exemplo em `prompts/`) estão versionados no git (`git ls-files prompts`)

**Execute agora a revisão completa e apresente o relatório final seguindo rigorosamente as instruções acima.**