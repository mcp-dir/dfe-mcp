# DF-e

### DF-e MCP, the invoices your suppliers issued against your Brazilian company

Pulls the electronic fiscal documents issued **against your Brazilian company** (supplier invoices, CT-e freight bills and events) straight from **SEFAZ**, through the **official NFeDistribuicaoDFe web service**, using **your own A1 digital certificate**. Incremental NSU sync with query-budget control so your tax ID never gets blocked. It also renders the **DANFE PDF** from the XML, which is the printable document, since SEFAZ distributes XML and not PDF. Read-only. **Not affiliated with SEFAZ or the Brazilian Federal Revenue Service.**

- 📥 **The invoices that came IN, not the ones you issued** — everything suppliers and carriers issued against your tax ID, straight from SEFAZ
- 🔐 **Your own A1 certificate, no middleman** — the SEFAZ handshake uses your company's own e-CNPJ, encrypted at rest
- 🔁 **Incremental NSU sync** — each call brings up to 50 documents and the next one resumes where it stopped, until the queue ends
- 🚦 **It will not get your tax ID blocked** — SEFAZ enforces a 1-hour block on whoever keeps polling past the end of the queue, and one tool just reports the hour's remaining budget
- 🧾 **DANFE PDF from the XML** — the printable representation SEFAZ does not distribute, rendered without spending a query
- 💬 **Works with any MCP client**: Claude Desktop, ChatGPT, Cursor, VS Code, Cline, Continue

[Versão em português](README.pt.md) · [Full docs (PT-BR)](docs/) · [Agent skill](skills/)

---

## One-click install

### Claude (Web and Desktop)

Anthropic unified MCP installation at `claude.ai/customize/connectors`. **The same link works for Claude Web and Claude Desktop** (just be logged in):

[➕ Open in Claude and connect](https://claude.ai/new?modal=add-custom-connector#settings/customize-connectors)

**Manual** (if the deeplink does not open): [claude.ai/customize/connectors](https://claude.ai/customize/connectors?surface=cowork) → **+** → **Add custom connector** → paste **Name** `DF-e` and **URL** `https://api.mcp.ai/p_dfe`.

### Cursor

[➕ Install DF-e in Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=dfe&config=eyJ1cmwiOiJodHRwczovL2FwaS5tY3AuYWkvcF9kZmUifQ==)

### VS Code (Copilot Chat)

[➕ Install DF-e in VS Code](vscode:mcp/install?name=dfe&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fapi.mcp.ai%2Fp_dfe%22%7D)

### ChatGPT, Manus, OpenClaw and 40+ other clients

Works with any MCP client that speaks **MCP over HTTP**. The server URL is always:

```
https://api.mcp.ai/p_dfe
```

Per-client details: [INSTALL.md](INSTALL.md).

---

## Example prompts

```
Sync the new fiscal documents for my company
How many SEFAZ queries do I have left this hour?
Which supplier invoices came in this week, and from whom?
Fetch the invoice with access key 3526...
Render the DANFE PDF for the invoices I just synced
What is the total of the inbound invoices that arrived this month?
```

---

## 6 tools available

| Tool | Description |
|---|---|
| `dfe_list_accounts` | Lista os CNPJs conectados neste install (razão social, CNPJ e UF). |
| `dfe_sincronizar` | Puxa os documentos fiscais NOVOS emitidos contra este CNPJ (nota de fornecedor, evento, CT-e) — NFeDistribuicaoDFe. |
| `dfe_consultar_nsu` | Busca UM documento específico pelo NSU. |
| `dfe_consultar_chave` | Busca um documento pela chave de acesso de 44 dígitos. |
| `dfe_gerar_danfe` | Gera o DANFE (o PDF da nota) a partir do XML. |
| `dfe_orcamento` | Quantas consultas ainda cabem nesta hora para este CNPJ, e se ele está bloqueado. |

Details for each tool: [docs/ferramentas.md](docs/ferramentas.md) (PT-BR)


---

## What you can ask

| Area | Coverage |
|---|---|
| **Connections** | The tax IDs connected to this install, with company name and state |
| **Sync** | Incremental NSU queue: supplier NF-e, CT-e freight bills and events issued against the company |
| **Single document** | Lookup by NSU (to recover an item that failed) or by the 44-digit access key |
| **DANFE** | The invoice PDF rendered from the XML, delivered as a download link |
| **Budget** | How many queries are left this hour and whether the tax ID is blocked |

### Three honest notes before you subscribe

1. **It does not return the invoices YOUR company issued.** The distribution
   service hands the *recipient* what was issued **against** them; technical note
   2014.002 excludes the issuer explicitly. To audit your own output, the XML
   comes from your ERP.
2. **Without recipient acknowledgement, many documents arrive as a SUMMARY only.**
   SEFAZ releases the access key, the issuer and the amount, and only unlocks the
   full XML (with line items) after the recipient acknowledges the invoice. No
   line items means no DANFE and no line-by-line audit. That acknowledgement is
   not issued by this MCP yet.
3. **The query limit belongs to SEFAZ and is per tax ID.** It is 20 queries per
   hour, shared with **any other system** querying the same company (your ERP,
   your accountant). That is why one tool does nothing but report the hour's
   budget, and why the sync stops on its own at the end of the queue.

---

## Pricing

Billed per connected account. See [docs/precos.md](docs/precos.md) (PT-BR).

---

## Privacy & data protection

- **Read-only**, no tool changes data at the source.
- **Sub-processors**: the LLM host you choose (Claude, ChatGPT, Cursor, your own agent). Full list in [docs/privacidade-lgpd.md](docs/privacidade-lgpd.md).
- Data returned by the tools is sent to **the LLM host you choose**, a sub-processor outside our control. We recommend plans with training opt-out.

---

## FAQ

**Is this an official SEFAZ or Federal Revenue service?**
No. This is an **independent** MCP that talks to the public **NFeDistribuicaoDFe** web service using your own company's digital certificate. It is **not affiliated with SEFAZ or the Brazilian Federal Revenue Service** and implies no official relationship. The names are used nominatively, to say where the data comes from.

**Does it return the invoices my company issued?**
No, and that is a SEFAZ limit rather than ours: the distribution service hands the recipient what was issued **against** them, and technical note 2014.002 excludes the issuer. For your own output, the XML lives in your ERP.

**Why do some documents arrive without line items?**
Because SEFAZ only unlocks the full XML after the **recipient acknowledges** the invoice. Until then it delivers a summary: access key, issuer and amount. Each document carries `completo: true/false` so you know which of the two you got. Since the DANFE needs the line items, it is rendered only from the full XML.

**Can I use my accountant's certificate?**
It will not work. SEFAZ matches the **certificate holder** against the queried tax ID, so you need the A1 e-CNPJ of the company itself. An accounting firm connects one tax ID per client, each with that client's own certificate.

**Could this get my tax ID blocked?**
The risk is real and it comes from the SEFAZ rule: 20 queries per hour per tax ID, and polling past the end of the queue earns a 1-hour block. The MCP stops on its own when the queue ends, and one tool reports the remaining budget without spending a query. What it cannot control is what **your other** systems query against the same tax ID, because the limit is shared.

**Can I fetch an older invoice by its access key?**
Yes, as long as it reached the national environment within the **last 90 days**, which is the SEFAZ window for key lookups. Outside it, the incremental NSU queue is the way.

**Where does my certificate live?**
Encrypted at rest, and used only for the TLS handshake with SEFAZ. There is no third party in the path: the certificate hitting SEFAZ is your company's. The captured XMLs are returned to the caller and are not stored by us beyond operational logs.

**How is it billed?**
Per **connected tax ID**, with unlimited documents. The tax ID is the axis because it is what consumes the SEFAZ query budget; how many invoices your company receives does not change our cost, so charging for that would make no sense. Current pricing at [mcp.ai/dfe](https://mcp.ai/dfe).


---

## Support

- 📧 [dfe@mcp.ai](mailto:dfe@mcp.ai)
- 🐛 [GitHub Issues](https://github.com/mcp-dir/dfe-mcp/issues)
- 📄 [docs/](docs/)

---

## License

MIT — see [LICENSE](LICENSE). The MCP server at `api.mcp.ai/p_dfe` is proprietary (hosted); this repo (manifests, docs, skills) is MIT.
