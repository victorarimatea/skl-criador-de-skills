# skl-criador-de-skills

**Versão:** v2.0 — 2026-06-16
**Repositório:** https://github.com/victorarimatea/skl-criador-de-skills
**Mantenedor:** victorarimatea

Skill fundacional do ecossistema ATLAS. Cria novas skills no ecossistema
DTD/SETIS/SES-DF — seja como repositório server-side (GitHub) ou como
pacote client-side (hub-client-side) — garantindo conformidade com a
nomenclatura, propagação ao sumário e estrutura padronizada.

---

## IDENTIDADE DO ECOSSISTEMA

- **Usuário GitHub:** victorarimatea
- **Repositório âncora:** hub-fonte
- **Sumário canônico:** `https://api.github.com/repos/victorarimatea/hub-fonte/contents/sumario.md`
- **Nomenclatura canônica:** `https://api.github.com/repos/victorarimatea/hub-fonte/contents/nomenclatura.md`
- **Repositório client-side:** `https://api.github.com/repos/victorarimatea/hub-client-side/contents/`
- **Orquestração GitHub:** skl-github-orquestracao (S04) — seguir obrigatoriamente

> **Regra de acesso:** toda leitura de ecossistema usa a Contents API
> (`api.github.com`). Nunca usar `raw.githubusercontent.com` em sessões
> com token de escrita — risco de cache CDN (Erro #003 SEV1).

---

## QUANDO ESTA SKILL É ACIONADA

Frases que ativam esta skill:

- "cria uma skill para..."
- "quero uma nova skill que..."
- "precisamos de uma skill de..."
- "cria o repositório da skill..."
- "criar versão client-side de [skill]"
- "quero instalar [skill] no Claude"
- "gerar pacote instalável de [skill/workflow]"

---

## PROTOCOLO DE EXECUÇÃO — SIGA ESTA SEQUÊNCIA EXATAMENTE

### ETAPA 0 — Bifurcação: server-side ou client-side?

Antes de qualquer outra ação, determinar a natureza da skill a criar.

**Perguntar ao usuário se não estiver claro:**

> "Esta skill vai operar dentro do ecossistema (repositório GitHub, requer
> token) ou será um pacote instalável para uso direto no Claude, sem
> sessão formal (client-side)?"

**Classificação:**

| Tipo | Características | Destino |
|---|---|---|
| **Server-side** | Lê/escreve no ecossistema; requer token; vive em repositório próprio | Novo repositório `skl-[nome]` no GitHub → ir para ETAPA 1-SERVER |
| **Client-side** | Opera com ou sem token; embutida no Claude (Project Knowledge); não cria repositório próprio | Arquivo `.md` depositado em `hub-client-side` → ir para ETAPA 1-CLIENT |

Se a resposta for ambígua: defaultar para **server-side** e registrar a
opção client-side como evolução futura no staging.

---

## FLUXO SERVER-SIDE

### ETAPA 1-SERVER — Leitura das matrizes (obrigatória, sem exceções)

Ler via Contents API com token de leitura:

```
GET https://api.github.com/repos/victorarimatea/hub-fonte/contents/sumario.md
GET https://api.github.com/repos/victorarimatea/hub-fonte/contents/nomenclatura.md
```

Headers obrigatórios:
```
Authorization: Bearer {TOKEN_LEITURA}
Accept: application/vnd.github+json
```

Decodificar campo `content` de base64:
```python
import base64
conteudo = base64.b64decode(resp["content"].replace("\n", "")).decode("utf-8")
```

Extrair e registrar internamente:
- Lista de repositórios já existentes (verificar duplicatas)
- Regras de nomenclatura (validar nome proposto)
- Próximo ID disponível na seção Skills (S) do sumário

### ETAPA 2-SERVER — Validação do nome proposto

Verificar se o nome proposto:

a) **Já existe no sumário** → informar o usuário; perguntar se quer nome
   diferente ou atualizar a skill existente

b) **Está em kebab-case minúsculo** → se não, propor versão correta

c) **Segue o prefixo `skl-`** → prefixo obrigatório para Skills (S) no
   ecossistema; propor correção se ausente

d) **É autoexplicativo** → se não, sugerir alternativa mais clara

Apresentar resultado da validação ao usuário antes de prosseguir.

### ETAPA 3-SERVER — Coleta de informações

Solicitar ao usuário, se não fornecidas:

- **Nome do repositório** (validado na Etapa 2)
- **Descrição em uma linha** (usada no GitHub e no sumário)
- **Função principal** (o que a skill faz, em 2-3 frases)
- **Quando é acionada** (frases ou contextos que disparam a skill)
- **Dependências** (quais outras skills ou matrizes ela consulta)
- **Visibilidade** (público ou privado — padrão: **público**)
- **Gera documentos DOCX/PDF?** (sim → incluir REGRA DE ACENTUAÇÃO no SKILL.md gerado)

### ETAPA 4-SERVER — Apresentar plano e solicitar confirmação

Antes de executar qualquer ação no GitHub, apresentar:

```
PLANO DE EXECUÇÃO — SKILL SERVER-SIDE

Repositório a criar: skl-[nome]
Descrição: [descrição]
Visibilidade: [público/privado]
ID no sumário: S[XX]

Arquivos que serão criados:
  skl-[nome]/README.md
  skl-[nome]/SKILL.md
  skl-[nome]/INDICE.md
  skl-[nome]/backlog-versoes.md

Arquivos que serão atualizados:
  hub-fonte/sumario.md — nova entrada S[XX]
  hub-fonte/backlog-versoes.md — registro da criação

Confirma? (sim/não)
```

**Só prosseguir após confirmação explícita.**

### ETAPA 5-SERVER — Token de escrita

Se ainda não disponível na sessão, solicitar o token de escrita.
Lembrar ao usuário: revogar em https://github.com/settings/tokens
ao final da sessão.

### ETAPA 6-SERVER — Execução via API GitHub

Usar exclusivamente `urllib.request` em Python. Nunca usar `curl`
(risco de falha silenciosa com caracteres especiais — Erro #003).

**6.1 — Criar o repositório**

```python
import urllib.request, json

payload = json.dumps({
    "name": "{NOME}",
    "description": "{DESCRICAO}",
    "private": False,   # ajustar se privado
    "auto_init": False  # nunca auto_init — criar README no primeiro PUT
}).encode("utf-8")

req = urllib.request.Request(
    "https://api.github.com/user/repos",
    data=payload,
    headers={
        "Authorization": "Bearer {TOKEN_ESCRITA}",
        "Accept": "application/vnd.github+json",
        "Content-Type": "application/json"
    },
    method="POST"
)
```

**6.2 — Criar README.md**

Modelo obrigatório — preencher campos:

```markdown
# [NOME_REPOSITORIO]

**Tipo:** Skill (S[ID])
**Versão:** v1.0 — [DATA]
**Mantenedor:** victorarimatea
**Visibilidade:** [Público/Privado]
**Status:** Ativo

> [DESCRICAO_LONGA]

## Arquivos deste repositório

| Arquivo | Função |
|---|---|
| `SKILL.md` | Instrução técnica lida pelo Claude para executar a skill |
| `INDICE.md` | Mapa de navegação dos arquivos do repositório |
| `backlog-versoes.md` | Histórico auditável de todas as versões desta skill |

## O que esta skill faz

[FUNCAO_PRINCIPAL]

## Como acionar esta skill

[QUANDO_ACIONADA]

## Bloco para agentes de IA

> **Leitura obrigatória para qualquer agente que opere neste repositório.**

| Marcação | Significado | Comportamento esperado |
|---|---|---|
| 🔒 | Interno — relevância de rastreabilidade | Registrar normalmente |
| 📊 | Estratégico — valor narrativo para SETIS | Destacar em resumos executivos |
| 🌐 | Público — comunicável externamente | Incluir em materiais de divulgação |
| 🧪 | Em teste — experimentação ativa, não vinculante | Reportar ao final de cada sessão; não bloqueia operações |

## Histórico resumido de versões

| Versão | Data | Descrição |
|---|---|---|
| v1.0 | [DATA] | Criação inicial da skill |

---

## Navegação rápida

→ **[INDICE.md](./INDICE.md)** — mapa completo de todos os arquivos deste repositório
```

**6.3 — Criar SKILL.md**

Gerar SKILL.md com base nas informações coletadas.
Estrutura obrigatória de todo SKILL.md do ecossistema:

- Cabeçalho com versão, repositório e mantenedor
- Seção IDENTIDADE DO ECOSSISTEMA com URLs via Contents API
- Seção QUANDO ESTA SKILL É ACIONADA
- Seção PROTOCOLO DE EXECUÇÃO com etapas numeradas
- Etapa obrigatória de leitura do sumario.md e nomenclatura.md via API
- Etapa obrigatória de verificação de duplicata no sumário
- Seção INTENÇÃO DO COMANDANTE ao final

> **Se a skill gera documentos DOCX/PDF:** incluir obrigatoriamente
> a seção REGRA DE ACENTUAÇÃO (copiar da S02 ou S03 como modelo).
> Se não gera DOCX/PDF: **não incluir** — a seção não se aplica.

**6.4 — Criar INDICE.md**

Modelo obrigatório:

```markdown
# Índice — [NOME_REPOSITORIO]

**Tipo:** Skill (S[ID])
**Última atualização:** [DATA]

> [DESCRICAO_CURTA]

---

## Arquivos na raiz

| Arquivo | Descrição | Quando consultar |
|---|---|---|
| [`SKILL.md`](./SKILL.md) | Instruções completas para o Claude executar a skill | Para executar a skill |
| [`backlog-versoes.md`](./backlog-versoes.md) | Histórico de versões e alterações | Para rastrear evoluções |
| [`README.md`](./README.md) | Apresentação do repositório | Para entender o propósito |
| [`INDICE.md`](./INDICE.md) | Este arquivo | Para navegação rápida |

---

*Mantido por victorarimatea — DTD/SETIS/SES-DF*
```

**6.5 — Criar backlog-versoes.md**

```markdown
# Backlog de Versões — [NOME_REPOSITORIO]

## v1.0 — [DATA]

**Tipo de alteração:** Criação
**Autorizado por:** victorarimatea
**Exposição de motivos:** Criação inicial da skill [NOME] no ecossistema
DTD/SETIS/SES-DF. [MOTIVO_DA_CRIACAO]

### Alterações realizadas

- Criação do repositório `[NOME]` ([público/privado])
- Criação do `README.md` v1.0
- Criação do `SKILL.md` v1.0
- Criação do `INDICE.md`
- Criação do `backlog-versoes.md`
- `hub-fonte/sumario.md`: entrada S[ID] adicionada
```

**6.6 — Atualizar sumario.md do hub-fonte**

Ler SHA imediatamente antes do PUT:

```python
# GET para obter SHA atual
req = urllib.request.Request(
    "https://api.github.com/repos/victorarimatea/hub-fonte/contents/sumario.md",
    headers={"Authorization": "Bearer {TOKEN}", "Accept": "application/vnd.github+json"}
)
# ... decodificar, inserir nova linha na seção Skills, PUT com SHA
```

Aguardar ≥3s após o PUT antes do GET de confirmação.

**6.7 — Atualizar backlog-versoes.md do hub-fonte**

Mesma disciplina de SHA: GET imediato antes do PUT.
Nova entrada com tipo "feat", data e descrição da skill criada.

### ETAPA 7-SERVER — Relatório final

```
SKILL SERVER-SIDE CRIADA

Repositório: https://github.com/victorarimatea/[NOME]
Arquivos criados: README.md, SKILL.md, INDICE.md, backlog-versoes.md
Sumário: entrada S[ID] adicionada (hub-fonte/sumario.md)
Backlog do ecossistema: v[X.Y] registrada

Próximo passo: adicionar SKILL.md ao Project Knowledge do Claude
para ativar a skill nas sessões.
```

---

## FLUXO CLIENT-SIDE

### ETAPA 1-CLIENT — Leitura da skill/workflow alvo

A criação de um pacote client-side é uma conversão de um artefato
server-side existente. Ler obrigatoriamente via Contents API:

1. `hub-fonte/sumario.md` — confirmar repositório e versão atual do alvo
2. `[repo-alvo]/SKILL.md` ou `[repo-alvo]/WORKFLOW.md` — conteúdo a converter
3. `[repo-alvo]/INDICE.md` se existir — dependências declaradas

Se o alvo não existir no sumário: interromper e informar o usuário.
Não criar client-side de algo que não existe como server-side canônico
(excepcionalmente aprovado pelo mantenedor para protótipos).

### ETAPA 2-CLIENT — Mapeamento de dependências

Classificar todas as dependências da skill alvo:

| Categoria | Definição | Tratamento na client-side |
|---|---|---|
| **A — Embutível** | Padrões estáticos, estruturas, taxonomias | Incorporar diretamente no arquivo |
| **B — Referenciável** | Skills consultadas, glossários, índices | A client-side busca via API quando há token; opera em fallback sem token |
| **C1 — Bloqueia Modo 1** | Sem token, etapa inoperável de forma confiável | Declarar bloqueio explícito; descrever comportamento sem token |
| **C2 — Bloqueia Modo 2** | Requer token de leitura | Declarar; fornecer instrução de desbloqueio |
| **C3 — Bloqueia Modo 3** | Requer token de escrita | Declarar; a client-side nunca escreve por conta própria |

### ETAPA 3-CLIENT — Diagnóstico de viabilidade

Apresentar ao usuário antes de gerar qualquer arquivo:

```
DIAGNÓSTICO — [Nome da Skill] → Client-Side

Skill alvo: [código] — [nome] — v[versão]

Dependências mapeadas:
| Dependência | Categoria | Modo afetado | Tratamento proposto |
|---|---|---|---|
[linhas]

Viabilidade por modo:
| Modo | Viabilidade | Limitações |
|---|---|---|
| Modo 1 — Sem token      | ✅/⚠️/🚫 | [bloqueios C1] |
| Modo 2 — Token leitura  | ✅/⚠️/🚫 | [bloqueios C2] |
| Modo 3 — Token escrita  | ✅/⚠️/🚫 | [bloqueios C3] |

Recomendação: ✅ VIÁVEL / ⚠️ VIÁVEL COM RESTRIÇÕES / 🚫 BLOQUEADO
```

**Aguardar aprovação explícita antes de prosseguir.**

### ETAPA 4-CLIENT — Geração do arquivo client-side

Gerar o arquivo com estrutura obrigatória:

```markdown
# SKILL — [Nome]
**Código:** [CODIGO]-CS
**Versão local:** v1.0
**Importada de:** [repositório]/SKILL.md — v[versão] — [data original]
**Data de importação:** [hoje]
**Ecossistema:** DTD/SETIS/SES-DF — github.com/victorarimatea

> Nota de design: [o que faz, que é client-side, onde vive o canônico]

---

## Declaração de dependências e modos

| Modo | Requer | Viabilidade | Limitações |
|---|---|---|---|
| Modo 1 — Sem token      | nada            | [✅/⚠️/🚫] | [ou "nenhuma"] |
| Modo 2 — Token leitura  | token leitura   | [✅/⚠️/🚫] | [ou "nenhuma"] |
| Modo 3 — Token escrita  | leitura+escrita | [✅/⚠️/🚫] | [ou "nenhuma"] |

## Como acionar esta skill
[frases de ativação]

## Verificação de versão (Modos 2 e 3)
[comparar versão local com ecossistema e alertar se divergente]

## Padrão embutido (fallback — [skill] v[versão] importada em [data])
[conteúdo adaptado para uso client-side]

## Modos de operação
### MODO 1 — Sem token
### MODO 2 — Com token de leitura
### MODO 3 — Com token de escrita

## Endereços do ecossistema
## Intenção do Comandante
```

Nomenclatura do arquivo: `SKILL-[nome]-clientside.md`

### ETAPA 5-CLIENT — Entrega e depósito

**5a — Entregar no chat** para revisão e aprovação antes de qualquer depósito.

**5b — Depositar em hub-client-side** (requer token de escrita):
- Caminho: `hub-client-side/SKILL-[nome]-clientside.md`
- Atualizar `hub-client-side/INDICE.md` com nova entrada
- **Não** criar repositório separado — client-sides vivem no hub-client-side

**5c — Sem propagação ao sumario.md do hub-fonte:** pacotes client-side
não são listados individualmente no sumário — o sumário registra apenas
`hub-client-side` como S08.

### ETAPA 6-CLIENT — Relatório final

```
PACOTE CLIENT-SIDE CRIADO

Arquivo: SKILL-[nome]-clientside.md
Depositado em: hub-client-side/
INDICE.md: atualizado

Próximo passo: copiar o arquivo para o Project Knowledge do Claude.
```

---

## REGRAS DE COMPORTAMENTO (ambos os fluxos)

- **Nunca pular a leitura das matrizes.** O sumário é a fonte de verdade.
- **Nunca executar ações no GitHub sem confirmação explícita** do usuário.
- **Nunca reutilizar SHA** de leitura anterior — buscar SHA imediatamente antes de cada PUT.
- **Aguardar ≥3s** após todo PUT antes do GET de confirmação (S04 v2.10 — Erro #015).
- **Sempre fazer GET de confirmação** após cada PUT — verificar campos críticos, não apenas HTTP 200.
- **Nunca armazenar token** além do necessário para a sessão atual.
- **Parar e informar** o usuário em caso de qualquer erro de API antes de continuar.
- **Usar exclusivamente `urllib.request`** em Python para chamadas à API — nunca `curl`.
- **Client-side nunca escreve no ecossistema** durante execução — apenas o mantenedor deposita via token de escrita em sessão formal.

---

## INTENÇÃO DO COMANDANTE

O estado final desejado desta skill é que qualquer nova skill necessária
no ecossistema — seja para operar dentro do GitHub com acesso pleno
(server-side) ou para ser instalada diretamente no Claude e funcionar
em qualquer contexto (client-side) — nasça em conformidade com os
padrões do ecossistema ATLAS, com rastreabilidade completa e sem
dependência de memória manual do mantenedor.

Uma skill bem criada declara sua natureza, conhece seus limites,
e pode ser auditada de ponta a ponta.
