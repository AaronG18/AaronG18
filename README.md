# AaronG

**Full-Stack & AI Engineer** — 4+ years building production AI systems end to
end, from the retrieval pipeline and agent runtime to the API and dashboard
behind them.

My work spans three layers, and most of what I ship crosses all three: back-end
foundations (REST APIs, ETL pipelines, IoT systems), full-stack + AI (RAG systems,
diagnostic tooling, visualization platforms), and full-stack + AI agents
(multi-stage LLM orchestration, autonomous workflows, agent-served products).

---

### What I work on

- **Multi-stage LLM orchestration** — intent classification → query planning
  → entity resolution → SQL generation → response synthesis, with retry,
  fallback and validation at every stage. Routes models by task complexity to
  balance inference cost against response quality.
- **Retrieval & RAG** — indexing large corpora into vector stores, retrieval
  optimization, LLM chat interfaces, and the evaluation workflow that measures
  whether retrieval is actually improving.
- **Autonomous workflows** — 20+ LLM workflows that produce multi-page
  PPTX/PDF decks with embedded charts, maps and business insights, cutting
  analyst report production from days to minutes.
- **Data platforms at scale** — layered ClickHouse warehouses over 290M+
  records with multi-tier Redis caching (97-99% hit rate), sub-second LLM
  context retrieval, and 10x faster API response on TB-scale datasets.
- **Enterprise-grade delivery** — multi-tenant data isolation, enterprise
  security compliance, and layered SQL safety (prompt constraints, zero raw
  SQL exposure, post-generation validation).
- **Real-time & streaming** — SSE streaming across edge functions for
  sub-second perceived latency on complex analytical queries.

---

### Tech I work with

| Layer | Tools |
|---|---|
| LLMs & orchestration | LangGraph, LangChain, Claude, GPT-4, embedding models |
| Agent memory | Mem0 (semantic, cross-session) |
| Vector DB | Milvus |
| Backend | Python · FastAPI · Flask · Django · DRF · REST APIs |
| Data & warehousing | ClickHouse · PostgreSQL · MySQL · BigQuery · MongoDB |
| Caching & messaging | Redis (multi-tier) · RabbitMQ · MQTT / EMQX · Modbus |
| Frontend | React · Redux Toolkit · ECharts · Plotly · Jinja2 |
| Validation & testing | Pydantic · pytest · Vitest · Playwright |
| Cloud & DevOps | Docker / Compose · AWS ECS · Firebase (Hosting, Functions) · GitHub Actions · CI/CD |

---

### Domain experience

Beyond the AI layer, I've shipped systems across these areas — each one
end-to-end, backend through frontend:

- **IoT & industrial systems** — energy management platforms orchestrating a
  Django backend, PostgreSQL and an MQTT broker, ingesting sensor streams over
  Modbus and MQTT, computing real-time consumption and savings, and issuing
  automated control commands based on preset modes.
- **ETL & data integration** — automated Python pipelines syncing commerce and
  warehouse sources into MySQL and on to collaboration tooling, cutting manual
  processing time by ~90%.
- **Commerce & operations back-office** — Django REST APIs as the central data
  hub for influencer/partner lifecycle management: profiles, collaboration
  records, financial transactions, order tracking, logistics.
- **Diagnostic & visualization tooling** — centralized Python/Plotly platforms
  letting engineers analyze gigabytes of daily multi-modal test data, pinpoint
  anomalies, and validate AI system performance; thermal heatmaps and
  statistical charts (histograms, box plots) for anomaly detection.
- **High-concurrency backends** — message queues for high-volume requests and
  large-scale data exports.
- **Product delivery** — Dockerized backends on managed container services,
  front ends on edge hosting, with dev/staging/prod promotion, PR preview
  environments, automated DB migrations and zero-downtime deploys.

---

### By the numbers

- **4+ years** building AI and full-stack systems
- **3** production AI & analytics platforms built 0 → 1
- **20+** autonomous LLM workflows in production
- **70+** services and edge functions under CI/CD

---

### Reach out

- GitHub: [@AaronG18](https://github.com/AaronG18)
- Email: aaron18g@outlook.com

<sub>:wave: Building something with agents, RAG, or the infrastructure
around them? Let's talk.</sub>
