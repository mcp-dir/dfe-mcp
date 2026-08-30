---
name: dfe-mcp
description: Captura na SEFAZ os documentos fiscais eletrônicos emitidos CONTRA um CNPJ (NF-e de fornecedor, CT-e e eventos) com o certificado digital A1 da própria empresa, via MCP: sincronização incremental por NSU, busca de documento avulso por NSU ou por chave de acesso de 44 dígitos, geração do DANFE em PDF a partir do XML e consulta do orçamento de consultas da hora. Use quando o usuário pedir notas de entrada, notas de fornecedor, documentos da SEFAZ, DF-e, XML de NF-e ou o PDF do DANFE. Sincronize sempre passando o `proximo_nsu` da resposta anterior e pare no fim da fila, porque o limite da SEFAZ é por CNPJ e varrer além disso bloqueia o CNPJ por uma hora. Não serve para as notas que a própria empresa emitiu: a SEFAZ não devolve ao emitente a própria nota.
---

# DF-e — REST API skill

Você tem acesso à **DF-e** REST API na MCP.AI.

> Puxa da **SEFAZ** os documentos fiscais eletrônicos emitidos **contra o seu CNPJ** (nota de fornecedor, CT-e e eventos), direto do **serviço oficial NFeDistribuicaoDFe**, com o **seu próprio certificado digital A1**. Sincronização incremental por NSU, com controle do limite de consultas para o seu CNPJ não ser bloqueado. Gera também o **DANFE em PDF** a partir do XML, que é o documento para imprimir ou anexar, porque a SEFAZ distribui XML e não PDF. Somente leitura. **Não afiliado à SEFAZ nem à Receita Federal.**

## Base URL

```
https://api.mcp.ai/api/dfe
```

Todo endpoint é um **POST** na Base URL + o path abaixo. Os parâmetros vão no corpo JSON.

## Autenticação

Inclua em toda request:

```
Authorization: Bearer sk_live_...
Content-Type: application/json
```

> Gere sua chave em **https://app.mcp.ai/settings/api-keys** (workspace API key `sk_live_…`, não expira, revogável). Uma única chave serve pra todos os seus MCPs.

## Formato de resposta

```json
{ "ok": true, "tool": "<tool_id>", "result": <payload> }
```

## Exemplo cURL

```bash
curl -X POST https://api.mcp.ai/api/dfe/consultar/chave \
  -H "Authorization: Bearer sk_live_..." \
  -H "Content-Type: application/json" \
  -d '{"chave":"..."}'
```

## Reportar problemas

Se um endpoint retornar erro, vazio ou dado inesperado, reporte (não desista calado): **POST /api/dfe/report** com `{ "message": "...", "context"?: "...", "conversation"?: [...] }`. Isso notifica o time da MCP.AI.

## Endpoints (6)

#### `dfe_consultar_chave`

Busca um documento pela chave de acesso de 44 dígitos. _(POST /api/dfe/consultar/chave)_

| Parâmetro | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `chave` | string | Sim | Chave de acesso da NF-e (44 dígitos). |
| `account` | string | Não |  |

#### `dfe_consultar_nsu`

Busca UM documento específico pelo NSU. _(POST /api/dfe/consultar/nsu)_

| Parâmetro | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `nsu` | string | Sim | NSU do documento. |
| `account` | string | Não |  |

#### `dfe_gerar_danfe`

Gera o DANFE (o PDF da nota) a partir do XML. _(POST /api/dfe/gerar/danfe)_

| Parâmetro | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `xml` | string | Não | O XML da NF-e (`documentos[].xml` de dfe_sincronizar). Caminho preferido: não consome consulta na SEFAZ. |
| `chave` | string | Não | Chave de acesso de 44 dígitos. Busca na SEFAZ (só últimos 90 dias) e consome 1 consulta. |
| `nsu` | string | Não | NSU do documento. Busca na SEFAZ e consome 1 consulta. |
| `account` | string | Não |  |

#### `dfe_list_accounts`

Lista os CNPJs conectados neste install (razão social, CNPJ e UF). _(POST /api/dfe/list/accounts)_

| Parâmetro | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `account` | string | Não |  |

#### `dfe_orcamento`

Quantas consultas ainda cabem nesta hora para este CNPJ, e se ele está bloqueado. _(POST /api/dfe/orcamento)_

| Parâmetro | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `account` | string | Não |  |

#### `dfe_sincronizar`

Puxa os documentos fiscais NOVOS emitidos contra este CNPJ (nota de fornecedor, evento, CT-e) — NFeDistribuicaoDFe. _(POST /api/dfe/sincronizar)_

| Parâmetro | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `ult_nsu` | string | Não | O `proximo_nsu` da resposta anterior. Use "0" na primeira carga. NÃO invente nem ajuste este número. |
| `forcar_nsu` | boolean | Não | Só use se souber o que está fazendo: ignora a checagem de sequência. Sequência errada = CNPJ bloqueado por 1 hora pela SEFAZ. |
| `account` | string | Não |  |

---

Este MCP também funciona via **conexão MCP** (Claude / Cursor) em `https://api.mcp.ai/p_dfe` — veja o [README](../../README.md). A skill acima é pra consumir a **REST API** direto (agente próprio / código).
