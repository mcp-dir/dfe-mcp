# Ferramentas

DF-e expõe 6 ferramentas (todas somente leitura).

### 1. `dfe_list_accounts`
**Input**: `account` (opcional)

Lista os CNPJs conectados neste install (razão social, CNPJ e UF).

### 2. `dfe_sincronizar`
**Input**: `ult_nsu` (opcional), `forcar_nsu` (opcional), `account` (opcional)

Puxa os documentos fiscais NOVOS emitidos contra este CNPJ (nota de fornecedor, evento, CT-e) — NFeDistribuicaoDFe.

### 3. `dfe_consultar_nsu`
**Input**: `nsu`, `account` (opcional)

Busca UM documento específico pelo NSU.

### 4. `dfe_consultar_chave`
**Input**: `chave`, `account` (opcional)

Busca um documento pela chave de acesso de 44 dígitos.

### 5. `dfe_gerar_danfe`
**Input**: `xml` (opcional), `chave` (opcional), `nsu` (opcional), `account` (opcional)

Gera o DANFE (o PDF da nota) a partir do XML.

### 6. `dfe_orcamento`
**Input**: `account` (opcional)

Quantas consultas ainda cabem nesta hora para este CNPJ, e se ele está bloqueado.

## Prompts de exemplo

```
Sincronize os documentos fiscais novos do meu CNPJ
Quantas consultas ainda posso fazer nesta hora?
Quais notas de fornecedor entraram esta semana e de quem?
Busque a nota da chave 3526...
Gere o PDF do DANFE das notas que sincronizei
Quanto somam as notas de entrada que chegaram no mês?
```
