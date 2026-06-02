# skill-criador-de-skills

**Tipo:** Skill (S)
**Versão atual:** v1.0 — 2026-05-26
**Mantenedor:** victorarimatea
**Visibilidade:** Privado
**Status:** Ativo

> Skill responsável por criar novas skills no ecossistema DTD/SETIS.
> Garante conformidade com a Matriz de Nomenclatura, atualização
> automática do Sumário e estrutura padronizada em todos os repositórios
> criados. É a primeira skill a ser executada sempre que uma nova
> automação for necessária no ecossistema.

## Arquivos deste repositório

| Arquivo | Função |
|---|---|
| `SKILL.md` | Instrução técnica lida pelo Claude para executar a skill |
| `backlog-versoes.md` | Histórico auditável de todas as versões desta skill |

## O que esta skill faz

Quando acionada, executa automaticamente esta sequência:

1. Consulta `sumario.md` no GitHub — verifica duplicatas e conformidade
2. Consulta `nomenclatura.md` — carrega regras de estrutura obrigatória
3. Solicita confirmação antes de criar qualquer coisa
4. Cria o repositório via API do GitHub
5. Cria os arquivos obrigatórios (`README.md`, `SKILL.md`, `backlog-versoes.md`)
6. Atualiza `sumario.md` — solicita autorização e registra a nova skill
7. Atualiza `backlog-versoes.md` do `ecossistema-sumario`

## Como acionar esta skill

Em uma conversa com o Claude que tenha esta skill carregada:

> *"Cria uma nova skill chamada [nome] com a seguinte função: [descrição]"*

O Claude vai conduzir todo o processo, solicitando confirmação
nos pontos críticos antes de executar ações irreversíveis.

## Histórico resumido de versões

| Versão | Data | Descrição |
|---|---|---|
| v1.0 | 2026-05-26 | Criação inicial da skill |

---

## Navegação rápida

→ **[INDICE.md](./INDICE.md)** — mapa completo de todos os arquivos deste repositório
