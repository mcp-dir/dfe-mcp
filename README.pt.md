# DF-e

### MCP do DF-e, as notas que os seus fornecedores emitiram contra o seu CNPJ

Puxa da **SEFAZ** os documentos fiscais eletrônicos emitidos **contra o seu CNPJ** (nota de fornecedor, CT-e e eventos), direto do **serviço oficial NFeDistribuicaoDFe**, com o **seu próprio certificado digital A1**. Sincronização incremental por NSU, com controle do limite de consultas para o seu CNPJ não ser bloqueado. Gera também o **DANFE em PDF** a partir do XML, que é o documento para imprimir ou anexar, porque a SEFAZ distribui XML e não PDF. Somente leitura. **Não afiliado à SEFAZ nem à Receita Federal.**

- 📥 **As notas que entraram, não as que você emitiu** — tudo que fornecedores e transportadoras emitiram contra o seu CNPJ, direto da SEFAZ
- 🔐 **O seu certificado A1, sem intermediário** — o handshake com a SEFAZ é feito com o e-CNPJ da sua própria empresa, cifrado em repouso
- 🔁 **Sincronização incremental por NSU** — cada chamada traz até 50 documentos e a seguinte continua de onde parou, até o fim da fila
- 🚦 **Não bloqueia o seu CNPJ** — a SEFAZ pune com 1 hora de bloqueio quem varre a fila além do fim, e há uma tool só para conferir o orçamento da hora
- 🧾 **DANFE em PDF a partir do XML** — a representação gráfica que a SEFAZ não distribui, gerada sem gastar consulta
- 💬 **Funciona com qualquer cliente MCP**: Claude Desktop, ChatGPT, Cursor, VS Code, Cline, Continue

[English version](README.md) · [Documentação completa](docs/) · [Skill pra agentes](skills/)

---

## Instalar em 1 clique

### Claude (Web e Desktop)

A Anthropic unificou a instalação de MCPs em `claude.ai/customize/connectors`. **O mesmo link serve pra Claude Web e Claude Desktop** (basta estar logado):

[➕ Abrir no Claude e conectar](https://claude.ai/new?modal=add-custom-connector#settings/customize-connectors)

**Manual** (se o deeplink não abrir): [claude.ai/customize/connectors](https://claude.ai/customize/connectors?surface=cowork) → **+** → **Adicionar conector personalizado** → cole **Nome** `DF-e` e **URL** `https://api.mcp.ai/p_dfe`.

### Cursor

[➕ Instalar DF-e no Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=dfe&config=eyJ1cmwiOiJodHRwczovL2FwaS5tY3AuYWkvcF9kZmUifQ==)

### VS Code (Copilot Chat)

[➕ Instalar DF-e no VS Code](vscode:mcp/install?name=dfe&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fapi.mcp.ai%2Fp_dfe%22%7D)

### ChatGPT, Manus, OpenClaw e mais 40+ clientes

Funciona em qualquer cliente MCP que suporte **MCP over HTTP**. A URL do servidor é sempre:

```
https://api.mcp.ai/p_dfe
```

Detalhes por cliente: [INSTALL.md](INSTALL.md).

---

## Exemplos de uso

```
Sincronize os documentos fiscais novos do meu CNPJ
Quantas consultas ainda posso fazer nesta hora?
Quais notas de fornecedor entraram esta semana e de quem?
Busque a nota da chave 3526...
Gere o PDF do DANFE das notas que sincronizei
Quanto somam as notas de entrada que chegaram no mês?
```

---

## 6 ferramentas disponíveis

| Tool | Descrição |
|---|---|
| `dfe_list_accounts` | Lista os CNPJs conectados neste install (razão social, CNPJ e UF). |
| `dfe_sincronizar` | Puxa os documentos fiscais NOVOS emitidos contra este CNPJ (nota de fornecedor, evento, CT-e) — NFeDistribuicaoDFe. |
| `dfe_consultar_nsu` | Busca UM documento específico pelo NSU. |
| `dfe_consultar_chave` | Busca um documento pela chave de acesso de 44 dígitos. |
| `dfe_gerar_danfe` | Gera o DANFE (o PDF da nota) a partir do XML. |
| `dfe_orcamento` | Quantas consultas ainda cabem nesta hora para este CNPJ, e se ele está bloqueado. |

Detalhe de cada tool: [docs/ferramentas.md](docs/ferramentas.md)


---

## O que dá para perguntar

| Área | Cobertura |
|---|---|
| **Conexões** | Os CNPJs conectados neste install, com razão social e UF |
| **Sincronização** | Fila incremental por NSU: NF-e de fornecedor, CT-e e eventos emitidos contra o CNPJ |
| **Documento avulso** | Busca por NSU (para recuperar um item que falhou) ou por chave de acesso de 44 dígitos |
| **DANFE** | PDF da nota a partir do XML, entregue como link de download |
| **Orçamento** | Quantas consultas ainda cabem na hora e se o CNPJ está bloqueado |

### Antes de assinar, três avisos honestos

1. **Não traz as notas que a SUA empresa emitiu.** O serviço de distribuição
   entrega ao *destinatário* o que foi emitido **contra** ele; a NT 2014.002
   exclui o emitente explicitamente. Para auditar a própria emissão, o XML sai do
   seu ERP.
2. **Sem manifestação do destinatário, muita nota vem só como RESUMO.** A SEFAZ
   entrega chave, emitente e valor, e só libera o XML completo (com os itens)
   depois que o destinatário se manifesta. Sem itens não há DANFE nem auditoria
   item a item. A manifestação ainda não é feita por este MCP.
3. **O limite de consultas é da SEFAZ e é por CNPJ.** São 20 consultas por hora,
   e elas são compartilhadas com **qualquer outro sistema** que consulte o mesmo
   CNPJ (o seu ERP, o seu contador). Por isso existe uma tool que só reporta o
   orçamento da hora, e por isso a sincronização para sozinha no fim da fila.

---

## Preços

Cobrança por conexão ativa. Veja [docs/precos.md](docs/precos.md).

---

## Privacidade & LGPD

- **Somente leitura**, nenhuma ferramenta altera dados na origem.
- **Sub-processadores**: o LLM host que você escolher (Claude, ChatGPT, Cursor, agente próprio). Lista completa em [docs/privacidade-lgpd.md](docs/privacidade-lgpd.md).
- Os dados retornados pelas tools são enviados ao **LLM host que você escolher**, sub-processador fora do nosso controle. Recomendamos planos com opt-out de treinamento.

---

## Perguntas frequentes

**É um serviço oficial da SEFAZ ou da Receita Federal?**
Não. Este é um MCP **independente** que fala com o web service público **NFeDistribuicaoDFe** usando o certificado digital da sua própria empresa. **Não é afiliado à SEFAZ nem à Receita Federal** e não implica vínculo oficial. Os nomes são citados de forma nominativa, para dizer de onde o dado vem.

**Traz as notas que a minha empresa emitiu?**
Não, e isso é limite da SEFAZ, não nosso: o serviço de distribuição entrega ao destinatário o que foi emitido **contra** ele, e a NT 2014.002 exclui o emitente. Para a sua própria emissão, o XML está no seu ERP.

**Por que algumas notas vêm sem os itens?**
Porque a SEFAZ só libera o XML completo depois da **manifestação do destinatário**. Antes disso ela entrega o resumo: chave de acesso, emitente e valor. O documento marca `completo: true/false` para você saber em qual dos dois casos está. Como o DANFE precisa dos itens, ele só é gerado a partir do XML completo.

**Posso usar o certificado do meu contador?**
Não funciona. A SEFAZ casa o **titular do certificado** com o CNPJ consultado, então é preciso o e-CNPJ A1 da própria empresa interessada. Um escritório contábil conecta um CNPJ por cliente, cada um com o certificado dele.

**Tem risco de bloquear o meu CNPJ?**
O risco existe e é da regra da SEFAZ: são 20 consultas por hora por CNPJ, e insistir depois do fim da fila rende 1 hora de bloqueio. O MCP para sozinho quando a fila acaba, e há uma tool que reporta o orçamento restante sem gastar consulta. O que ele não controla é o que **outros** sistemas seus consultam no mesmo CNPJ, porque o limite é compartilhado.

**Consigo buscar uma nota antiga pela chave de acesso?**
Sim, desde que ela tenha chegado ao Ambiente Nacional nos **últimos 90 dias**, que é a janela da SEFAZ para consulta por chave. Fora dela, o caminho é a fila incremental por NSU.

**Onde fica o meu certificado?**
Cifrado em repouso, e usado só no handshake TLS com a SEFAZ. Não há terceiro no caminho: quem bate na SEFAZ é o certificado da sua empresa. Os XMLs capturados são devolvidos a quem chamou e não são guardados por nós além dos logs operacionais.

**Como é a cobrança?**
Por **CNPJ conectado**, com documentos ilimitados. O eixo é o CNPJ porque é ele que consome o orçamento de consultas da SEFAZ; o número de notas que a sua empresa recebe não muda o nosso custo, então não faria sentido cobrar por isso. Valores atualizados em [mcp.ai/dfe](https://mcp.ai/dfe).


---

## Suporte

- 📧 [dfe@mcp.ai](mailto:dfe@mcp.ai)
- 🐛 [GitHub Issues](https://github.com/mcp-dir/dfe-mcp/issues)
- 📄 [docs/](docs/)

---

## Licença

MIT — veja [LICENSE](LICENSE). O servidor MCP em `api.mcp.ai/p_dfe` é proprietário (hosted); este repositório (manifestos, docs, skills) é MIT.
