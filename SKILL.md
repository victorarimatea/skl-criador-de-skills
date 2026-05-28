# skill-criador-de-skills

**Versão:** v1.0 — 2026-05-26
**Repositório:** https://github.com/victorarimatea/skill-criador-de-skills
**Mantenedor:** victorarimatea

Esta skill cria novas skills no ecossistema DTD/SETIS via API do GitHub,
garantindo conformidade com as Matrizes de Nomenclatura e Sumário.

---

## IDENTIDADE DO ECOSSISTEMA

- **Usuário GitHub:** victorarimatea
- **Repositório âncora:** ecossistema-sumario
- **URL do sumário:** https://raw.githubusercontent.com/victorarimatea/ecossistema-sumario/main/sumario.md
- **URL da nomenclatura:** https://raw.githubusercontent.com/victorarimatea/ecossistema-sumario/main/nomenclatura.md

---

## QUANDO ESTA SKILL É ACIONADA

Esta skill é acionada quando o usuário solicitar a criação de uma nova
skill, repositório de skill, ou automação no ecossistema. Exemplos de
frases que acionam esta skill:

- "Cria uma skill para..."
- "Quero uma nova skill que..."
- "Precisamos de uma skill de..."
- "Cria o repositório da skill..."

---

## PROTOCOLO DE EXECUÇÃO — SIGA ESTA SEQUÊNCIA EXATAMENTE

### ETAPA 1 — Leitura das Matrizes (obrigatória, sem exceções)

Antes de qualquer outra ação, leia os dois arquivos abaixo via
web_fetch. Nunca pule esta etapa, mesmo que o usuário peça urgência.

GET https://raw.githubusercontent.com/victorarimatea/ecossistema-sumario/main/sumario.md
GET https://raw.githubusercontent.com/victorarimatea/ecossistema-sumario/main/nomenclatura.md

A partir da leitura, extraia e registre internamente:
- Lista de repositórios já existentes (para verificar duplicatas)
- Regras de nomenclatura (para validar o nome proposto)
- Próximo ID disponível na seção [S] do sumário

### ETAPA 2 — Validação do nome proposto

Verifique se o nome proposto pelo usuário:

a) Já existe no sumário → se sim, informe o usuário e pergunte se
   quer usar um nome diferente ou atualizar a skill existente

b) Está em kebab-case minúsculo → se não, proponha a versão correta

c) É autoexplicativo → se não, sugira uma alternativa mais clara

d) Começa com o domínio correto para skills → não há prefixo
   obrigatório, mas nomes como `skill-[função]` são preferíveis
   para skills genéricas

Apresente o resultado da validação ao usuário antes de prosseguir.

### ETAPA 3 — Coleta de informações

Solicite ao usuário as informações necessárias para criar a skill,
caso não tenham sido fornecidas:

- **Nome do repositório** (validado na Etapa 2)
- **Descrição em uma linha** (será usada no GitHub e no sumário)
- **Função principal** (o que a skill faz, em 2-3 frases)
- **Quando é acionada** (frases ou contextos que disparam a skill)
- **Dependências** (quais outras skills ou matrizes ela consulta)

### ETAPA 4 — Apresentar plano e solicitar confirmação

Antes de executar qualquer ação no GitHub, apresente ao usuário
um resumo claro do que será feito:

PLANO DE EXECUÇÃO
Repositório a criar: [nome]
Descrição: [descrição]
Arquivos que serão criados:

README.md
SKILL.md
backlog-versoes.md

Repositórios que serão atualizados:

ecossistema-sumario/sumario.md (nova entrada [S0X])
ecossistema-sumario/backlog-versoes.md (registro da criação)

Confirma? (sim/não)

Só prossiga após confirmação explícita do usuário.

### ETAPA 5 — Solicitar o token

Se ainda não tiver o token nesta conversa, solicite:

### ETAPA 6 — Executar via API do GitHub

Execute as ações nesta ordem exata. A cada erro, pare e informe
o usuário antes de tentar continuar.

**6.1 — Criar o repositório**

POST https://api.github.com/user/repos
Headers:
Authorization: token {TOKEN}
Content-Type: application/json
Body:
{
"name": "{NOME_REPOSITORIO}",
"description": "{DESCRICAO}",
"private": true,
"auto_init": true
}

Aguarde 2 segundos após criar o repositório antes de criar arquivos.
O GitHub precisa deste tempo para inicializar o branch main.

**6.2 — Criar README.md**

Use o modelo abaixo, preenchendo os campos com as informações
coletadas na Etapa 3:

Aguarde 2 segundos após criar o repositório antes de criar arquivos.
O GitHub precisa deste tempo para inicializar o branch main.

**6.2 — Criar README.md**

Use o modelo abaixo, preenchendo os campos com as informações
coletadas na Etapa 3:

POST https://api.github.com/repos/victorarimatea/{NOME}/contents/README.md
Headers:
Authorization: token {TOKEN}
Content-Type: application/json
Body:
{
"message": "Adiciona README.md padronizado",
"content": "{BASE64_DO_CONTEUDO}"
}

Modelo do README.md a ser codificado em base64:

{NOME_REPOSITORIO}
Tipo: Skill (S)
Versão atual: v1.0 — {DATA}
Mantenedor: victorarimatea
Visibilidade: Privado
Status: Ativo

{DESCRICAO_LONGA}

Arquivos deste repositório
ArquivoFunçãoSKILL.mdInstrução técnica lida pelo Claude para executar a skillbacklog-versoes.mdHistórico auditável de todas as versões desta skill
O que esta skill faz
{FUNCAO_PRINCIPAL}
Como acionar esta skill
{QUANDO_ACIONADA}
Histórico resumido de versões
VersãoDataDescriçãov1.0{DATA}Criação inicial da skill

**6.3 — Criar SKILL.md**

O conteúdo do SKILL.md deve ser gerado por você com base nas
informações coletadas, seguindo a estrutura desta skill como modelo.
Todo SKILL.md do ecossistema deve ter obrigatoriamente:

- Cabeçalho com versão, repositório e mantenedor
- Seção IDENTIDADE DO ECOSSISTEMA com as URLs das matrizes
- Seção QUANDO ESTA SKILL É ACIONADA
- Seção PROTOCOLO DE EXECUÇÃO com etapas numeradas
- Etapa obrigatória de leitura do sumario.md e nomenclatura.md
- Etapa obrigatória de verificação própria no sumário
- Seção REGRA DE ACENTUAÇÃO (copiar da seção homônima desta skill)

ATENÇÃO: toda skill que gera documentos DOCX/PDF deve incluir
obrigatoriamente a seção REGRA DE ACENTUAÇÃO. Copie o bloco completo
desta skill para o SKILL.md da nova skill antes de criar o arquivo.

**6.4 — Criar backlog-versoes.md**

Modelo a ser preenchido:

Backlog de Versões — {NOME_REPOSITORIO}
v1.0 — {DATA}
Tipo de alteração: Criação
Autorizado por: victorarimatea
Exposição de motivos: Criação inicial da skill {NOME_REPOSITORIO}
no ecossistema DTD/SETIS. {MOTIVO_DA_CRIACAO}
Alterações realizadas

Criação do repositório {NOME_REPOSITORIO} (privado)
Criação do README.md
Criação do SKILL.md v1.0
Criação do backlog-versoes.md

**6.5 — Atualizar sumario.md**

Leia o sumario.md atual, adicione a nova entrada na seção [S],
e faça o commit com o conteúdo atualizado:

PUT https://api.github.com/repos/victorarimatea/ecossistema-sumario/contents/sumario.md
Headers:
Authorization: token {TOKEN}
Content-Type: application/json
Body:
{
"message": "Registra {NOME} no sumário do ecossistema",
"content": "{BASE64_CONTEUDO_ATUALIZADO}",
"sha": "{SHA_ATUAL_DO_ARQUIVO}"
}

Para obter o SHA atual do arquivo antes de atualizar:

GET https://api.github.com/repos/victorarimatea/ecossistema-sumario/contents/sumario.md

**6.6 — Atualizar backlog-versoes.md do ecossistema-sumario**

Mesmo processo do 6.5, adicionando entrada v0.X com:
- Tipo: Adição
- Descrição do que foi criado e por quê

### ETAPA 7 — Relatório final

Após todas as ações executadas com sucesso, apresente:

SKILL CRIADA COM SUCESSO
Repositório: https://github.com/victorarimatea/{NOME}
Arquivos criados: README.md, SKILL.md, backlog-versoes.md
Sumário atualizado: entrada {ID} adicionada
Backlog do ecossistema: versão {VX.Y} registrada
Próximo passo sugerido: adicione esta skill ao seu
projeto no Claude para ativá-la.

---

## REGRAS DE COMPORTAMENTO

- **Nunca pule a Etapa 1.** As matrizes são a fonte de verdade.
- **Nunca execute ações no GitHub sem confirmação explícita** do usuário
  na Etapa 4.
- **Nunca armazene o token** além do necessário para a sessão atual.
- **Sempre pare e informe** o usuário em caso de erro de API antes
  de tentar continuar.
- **Sempre verifique o SHA** antes de atualizar um arquivo existente —
  atualizar sem SHA causa erro 409 na API do GitHub.
- **Se o sumário estiver desatualizado** em relação ao que o usuário
  descreve, aponte a inconsistência e solicite autorização para corrigir
  antes de prosseguir.


---

## REGRA DE ACENTUAÇÃO — DOCUMENTOS EM PORTUGUÊS

Todo documento gerado por esta skill deve passar obrigatoriamente pelo
processo de correção de acentuação antes da entrega. Sem exceção.

### Por que esta regra existe

O pipeline Node.js + docx library + LibreOffice não garante preservação
de acentuação UTF-8 de forma consistente. Palavras acentuadas no código
fonte podem ser corrompidas no ambiente de execução restrito, resultando
em documentos com mistura de palavras acentuadas e não acentuadas —
aspecto incompatível com documentos institucionais.

### Protocolo obrigatório de correção (3 etapas)

**Etapa A — Substituição global no script de geração**

Antes de executar o script, aplicar substituição global. Cobertura mínima:

Substantivos institucionais: gestão, revisão, seção, informação, saúde,
versão, aprovação, análise, transformação, ação, ações, execução,
elaboração, integração, competência, competências, governança, deliberação,
publicação, comunicação, legislação, solicitação, manutenção, atualização,
identificação, consolidação, avaliação, decisão, criação, distribuição,
capacitação, certificação

Adjetivos: estratégico/a/os/as, técnico/a/os/as, específico/a/os/as,
público/a/os/as, básico/a/os/as, crítico/a/os/as, histórico/a/os/as,
obrigatório/a/os/as, próprio/a/os/as, único/a/os/as, próximo/a/os/as

Palavras funcionais: não, também, além, após, há, está, órgão, órgãos,
área, áreas, número, números, índice, índices

Nomes institucionais: Fórum de Subsecretários, Transformação Digital,
Saúde Digital, Secretaria de Estado de Saúde, Instrumento de Análise
Comparativa

**Etapa B — Correção individual de títulos de seção**

Verificar e corrigir cada título individualmente após a substituição
global. Títulos usam função separada e frequentemente escapam da
substituição global.

**Etapa C — Verificação automática do DOCX gerado**

Após gerar o DOCX e antes de converter para PDF:

```python
import zipfile, xml.etree.ElementTree as ET
with zipfile.ZipFile("arquivo.docx") as z:
    with z.open("word/document.xml") as f:
        tree = ET.parse(f)
root = tree.getroot()
ns = "{http://schemas.openxmlformats.org/wordprocessingml/2006/main}"
full = " ".join(t.text for t in root.iter(ns+"t") if t.text)
sem_acento = [
    "gestao","revisao","secao","informacao","saude","versao",
    "aprovacao","analise","transformacao","nao ","recomendacao",
    "convergencia","conclusao","indice ","forum ","deliberacao",
    "governanca","competencia","execucao","elaboracao","integracao",
    "publicacao","comunicacao","atualizacao","avaliacao","decisao"
]
encontradas = [w for w in sem_acento if w in full.lower()]
if encontradas:
    print(f"ATENÇÃO — palavras sem acento: {encontradas}")
else:
    print("Verificação OK — acentuação correta")
```

Se encontrar palavras sem acento, corrigir com substituição por contexto
e repetir a geração antes de prosseguir.

### Regra de entrega

Nunca entregar documento sem as 3 etapas concluídas.
A conversão para PDF só ocorre após Etapa C confirmar zero ocorrências.
