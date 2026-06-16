# skl-criador-de-skills

**Tipo:** Skill (S01)
**Versão:** v2.0 — 2026-06-16
**Mantenedor:** victorarimatea
**Visibilidade:** Público
**Status:** Ativo

> Skill fundacional do ecossistema ATLAS. Cria novas skills no ecossistema
> DTD/SETIS/SES-DF — seja como repositório server-side (GitHub) ou como
> pacote client-side (hub-client-side) — garantindo conformidade com a
> nomenclatura, propagação ao sumário e estrutura padronizada.

## Arquivos deste repositório

| Arquivo | Função |
|---|---|
| `SKILL.md` | Instrução técnica lida pelo Claude para executar a skill |
| `INDICE.md` | Mapa de navegação dos arquivos do repositório |
| `backlog-versoes.md` | Histórico auditável de todas as versões desta skill |

## O que esta skill faz

Quando acionada, determina primeiro a natureza da skill a criar
(server-side ou client-side) e então executa o fluxo adequado:

**Fluxo server-side:**
1. Lê `sumario.md` e `nomenclatura.md` via Contents API — verifica duplicatas e conformidade
2. Valida nome, coleta informações, apresenta plano
3. Cria repositório `skl-[nome]` + 4 arquivos obrigatórios (README, SKILL, INDICE, backlog)
4. Atualiza `hub-fonte/sumario.md` e `hub-fonte/backlog-versoes.md`

**Fluxo client-side:**
1. Lê a skill/workflow alvo no ecossistema via Contents API
2. Mapeia dependências (categorias A/B/C1/C2/C3) e apresenta diagnóstico de viabilidade
3. Gera arquivo `SKILL-[nome]-clientside.md` com três modos de operação declarados
4. Deposita em `hub-client-side/` e atualiza `hub-client-side/INDICE.md`

## Como acionar esta skill

Em uma conversa com o Claude que tenha esta skill carregada:

> *"Cria uma nova skill chamada [nome] com a seguinte função: [descrição]"*
> *"Criar versão client-side de [skill/workflow]"*

O Claude vai conduzir todo o processo, solicitando confirmação
nos pontos críticos antes de executar ações irreversíveis.

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
| v2.0 | 2026-06-16 | Bifurcação server-side/client-side; Contents API; Bearer token; INDICE.md no template; bloco-para-agentes; Intenção do Comandante |
| v1.1 | 2026-05-27 | REGRA DE ACENTUAÇÃO adicionada (skills DOCX/PDF) |
| v1.0 | 2026-05-26 | Criação inicial |

---

## Navegação rápida

→ **[INDICE.md](./INDICE.md)** — mapa completo de todos os arquivos deste repositório
