## v2.0 — 2026-06-16

**Tipo de alteração:** Evolução maior
**Autorizado por:** victorarimatea
**Exposição de motivos:** A criação do hub-client-side (S08) e da meta-skill
S-CSC revelou que o ecossistema passou a ter dois tipos de skill: as
server-side (repositório GitHub, acesso via token) e as client-side
(pacotes instaláveis no Claude, vivem no hub-client-side). A S01, como
skill fundacional responsável por criar todas as outras, precisava conhecer
e orquestrar ambos os fluxos. A v2.0 incorpora esta bifurcação como Etapa 0
e corrige divergências técnicas acumuladas desde v1.1.

### Alterações realizadas

- `SKILL.md`: Etapa 0 adicionada — bifurcação server-side/client-side antes
  de qualquer outra ação
- `SKILL.md`: Fluxo reorganizado em dois ramos independentes (FLUXO SERVER-SIDE
  e FLUXO CLIENT-SIDE), cada um com etapas próprias e relatório final
- `SKILL.md`: IDENTIDADE DO ECOSSISTEMA corrigida — `ecossistema-sumario` → `hub-fonte`;
  URLs migradas de `raw.githubusercontent.com` para Contents API (`api.github.com`)
- `SKILL.md`: `Authorization: token` → `Authorization: Bearer` em todos os exemplos
- `SKILL.md`: Template README gerado pela skill inclui bloco-para-agentes obrigatório
- `SKILL.md`: `auto_init: true` → `auto_init: false` no POST de criação de repositório
- `SKILL.md`: `private: true` → `private: false` como padrão (ajustável)
- `SKILL.md`: `INDICE.md` adicionado ao conjunto de arquivos criados (era 3, agora 4)
- `SKILL.md`: REGRA DE ACENTUAÇÃO removida da S01 — substituída por nota contextual
  em 6.3 direcionando apenas skills geradoras de DOCX/PDF (S02/S03 como modelo);
  a seção não se aplica à skill criadora de repositórios
- `SKILL.md`: Seção INTENÇÃO DO COMANDANTE adicionada ao final
- `README.md`: atualizado para v2.0 — descreve os dois fluxos; remove referência
  a `ecossistema-sumario`

### Impacto no ecossistema

- A S01 passa a ser a única skill de criação para ambos os tipos de skill do ecossistema
- Toda skill futura criada via S01 nasce com bloco-para-agentes, INDICE.md e Intenção
  do Comandante — padrões obrigatórios do ecossistema ATLAS
- Elimina o risco de criação de client-sides fora do hub-client-side por falta de instrução

---

# Backlog de Versões — skill-criador-de-skills

## v1.1 — 2026-05-27

**Tipo de alteração:** Melhoria crítica
**Autorizado por:** victorarimatea
**Exposição de motivos:** Incorporação do protocolo obrigatório de correção
de acentuação em português (REGRA DE ACENTUAÇÃO) ao SKILL.md. A necessidade
foi descoberta na prática durante a geração dos documentos IAC do PDTIC:
o pipeline Node.js + docx library + LibreOffice não preserva acentuação
UTF-8 de forma consistente no ambiente de execução restrito do Claude,
resultando em documentos com palavras sem acento — incompatível com
padrão institucional. A correção trouxe melhoria significativa e imediata
na qualidade dos documentos gerados. A regra foi também incorporada ao
ecossistema como padrão obrigatório (nomenclatura.md v0.4) e à
skill-iac-pdtic, com instrução de propagação a toda nova skill criada.

### Alterações realizadas
- `SKILL.md` atualizado: seção REGRA DE ACENTUAÇÃO adicionada ao final,
  com protocolo completo de 3 etapas:
  - Etapa A: substituição global de palavras acentuadas no script
  - Etapa B: correção individual de títulos de seção
  - Etapa C: verificação automática do DOCX gerado via script Python
    antes da conversão para PDF
- Instrução adicionada na Etapa 6.3: toda nova skill geradora de DOCX/PDF
  deve herdar obrigatoriamente o bloco REGRA DE ACENTUAÇÃO

### Impacto no ecossistema
- Documentos institucionais gerados passam a ter acentuação 100% correta
- Padrão propagado ao ecossistema via `nomenclatura.md` v0.4 (seção 9)
- Toda skill futura criada pelo ecossistema nasce com este padrão

---

## v1.0 — 2026-05-26

**Tipo de alteração:** Criação
**Autorizado por:** victorarimatea
**Exposição de motivos:** Criação da skill fundacional do ecossistema
DTD/SETIS. A skill-criador-de-skills é a peça que garante que todo
repositório criado a partir deste momento nasça em conformidade com
as Matrizes de Nomenclatura e Sumário, eliminando a dependência de
memória manual do mantenedor para manter a consistência do ecossistema.

### Alterações realizadas
- Criação do repositório `skill-criador-de-skills` (privado)
- Criação do `README.md` com estrutura padronizada
- Criação do `SKILL.md` v1.0 com protocolo completo de 7 etapas
- Criação deste `backlog-versoes.md`

### Impacto no ecossistema
- Habilita criação automatizada de novos repositórios de skill
- Garante atualização automática do `sumario.md` a cada nova skill
- Estabelece o `SKILL.md` desta skill como modelo de referência
  para todas as skills do tipo S criadas no futuro
