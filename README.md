<p align="center">
  <a href="https://www.conceptmem.dev">
    <img src="https://www.conceptmem.dev/brand/icon-256.png" alt="ConceptMem" width="88" height="88">
  </a>
</p>

<h1 align="center">ConceptMem</h1>

<p align="center">
  <strong>A managed ontological database for applications and AI agents.</strong><br>
  One memory for every agent. Always current.
</p>

<p align="center">
  <a href="https://www.conceptmem.dev">Website</a> ·
  <a href="https://www.conceptmem.dev/docs">Docs</a> ·
  <a href="https://www.conceptmem.dev/docs/mcp">MCP guide</a> ·
  <a href="https://www.conceptmem.dev/pricing">Pricing</a> ·
  <a href="https://github.com/conceptmem/benchmarks">Benchmarks</a>
</p>

---

### Agents remember what was said. Not what is true now.

Chat history and retrieved passages keep every version of a fact side by side, so the agent
has to guess which one still holds. ConceptMem checks each proposed update against your model
and policies, consolidates it into one current record, and keeps the history and provenance of
every change.

```text
CRM       10:32   city = Berlin   ─┐
Support   11:45   city = Lisbon   ─┤  proposals
Support   11:46   city = Lisbon   ─┘
                        │
               ontology + policy decide
                        ▼
customer:crm_1042
  city   Lisbon    current      · Support · 11:45 · confidence 0.97
         Berlin    superseded   · CRM     · 10:32
```

The repeated observation does not become a second fact, the old value moves to history, and
every agent reads the same answer: Lisbon, who said so, and when Berlin stopped holding.

## How it works

**1 · Model the record.** Entity types, fields, cardinality and typed relations give every
writer the same vocabulary. Types are flexible by default; mark one `strict` to refuse anything
it does not declare.

**2 · Govern the writes.** Every write is a proposal. Under your policy it is applied, skipped,
refused or held for review, and the result tells you which.

**3 · Read what's current.** Look up current facts, search by text, follow relations, read the
changelog, or ask what was true at a point in time.

## Built like a database

- **One current value per field.** A change supersedes the earlier value instead of sitting beside it.
- **Provenance on every change.** Source, actor, confidence and time are recorded with each mutation.
- **A receipt for every write.** Each result lists what was applied, skipped, refused or held for review. No silent writes.
- **Writes ordered per workspace.** Concurrent agents cannot interleave half-applied changes.
- **Resumable write plans.** An idempotency key lets a retry resume at the observation that failed.
- **Your records, exportable.** Export current records with their evidence as JSON or Markdown. An export is a view, never the source of record.

## Connect

ConceptMem is a managed service. Hosted MCP and REST share one access key and one set of rules,
so every update is checked the same way whichever client sends it.

**MCP** — works with ChatGPT, Claude, Cursor, VS Code and any client that supports Streamable
HTTP. Copy your database's endpoint from **Connections** in the console, then authorize with
OAuth or a workspace key:

```json
{
  "mcpServers": {
    "conceptmem": {
      "url": "YOUR_WORKSPACE_MCP_ENDPOINT",
      "headers": { "Authorization": "Bearer YOUR_WORKSPACE_KEY" }
    }
  }
}
```

**REST** — propose an observation, then read the record back:

```bash
curl https://www.conceptmem.dev/api/v1/observations \
  -H "X-API-Key: $CONCEPTMEM_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "entityType": "customer",
    "entityRef": "customer:crm_1042",
    "data": { "city": "Lisbon" },
    "provenance": { "source": "support", "confidence": 0.97, "extractionMethod": "structured_import" }
  }'

curl https://www.conceptmem.dev/api/v1/entities/customer%3Acrm_1042 \
  -H "X-API-Key: $CONCEPTMEM_API_KEY"
```

Check `operations`, `skipped`, `violations` and `pendingReview` in the result: a successful
response does not mean every proposed field was applied.

**TypeScript SDK** — `ConceptmemClient` from `@conceptmem/sdk` is in preview; the public npm
release is pending. See the [SDK docs](https://www.conceptmem.dev/docs/sdk).

<details>
<summary><strong>MCP tools</strong></summary>

| Tool               | What it does                                                 |
| ------------------ | ------------------------------------------------------------ |
| `memory_insert`    | Propose updates with their provenance                        |
| `memory_operation` | Recover confirmed receipts from a write plan                 |
| `memory_validate`  | Check a proposed update before writing                       |
| `memory_search`    | Find current records by text                                 |
| `memory_get`       | Read an entity's current facts and relationships             |
| `memory_history`   | Inspect an entity's change history                           |
| `memory_traverse`  | Follow typed relationships between entities                  |
| `memory_retract`   | Retract a current fact or relationship                       |
| `memory_review`    | Approve or reject a proposal held for review                 |
| `memory_export`    | Export a derived view of current records                     |
| `ontology_list`    | Read the shared vocabulary and its constraints               |
| `ontology_preview` | Check a proposed schema change before applying it            |
| `ontology_define`  | Propose entity types, fields and relationship definitions    |

</details>

## Benchmarks

On [LongMemEval-S](https://arxiv.org/abs/2410.10813) session retrieval (470 non-abstention
questions, each in its own store, written through the product's write path):

| Method                   | hit@1  | hit@5  |
| ------------------------ | -----: | -----: |
| Lexical search, no model | 89.36% | 98.09% |

hit@5 measures evidence retrieval, not answer accuracy. Rankings, methodology and a
verification script that recomputes every score are in
[conceptmem/benchmarks](https://github.com/conceptmem/benchmarks).

## What ConceptMem is not

- **Not a vector database.** Retrieval is exact lookup, text search and typed traversal.
- **Not a conversation log.** Duplicates collapse instead of accumulating, and current state is stored apart from history.
- **Not a judge of truth.** Accepted state reflects the observations received and the policy you set.

---

<p align="center">
  <a href="https://www.conceptmem.dev/signup"><strong>Start free</strong></a> — 5,000 operations a month over REST and MCP.<br>
  <sub>Built by LumenLabs.</sub>
</p>
