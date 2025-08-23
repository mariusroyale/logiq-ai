# Logiq-AI

**Logiq-AI** is an open-source, AI-powered observability and diagnostics platform for payment terminals (e.g., Ingenico) deployed across distributed merchant networks.

It captures logs and health metrics from terminals, detects anomalies, parses logs using local LLMs, and provides actionable insights and suggestions to improve performance, reduce downtime, and streamline support.

---

## 🧱 Architecture Overview

### 📍 1. Data Collection (Edge to Cloud)

| Component         | Tool              | Purpose                                           |
|------------------|-------------------|---------------------------------------------------|
| Agent on Terminal | Fluent Bit        | Collect logs and forward to backend              |
| Metrics Collector | Telegraf          | Collect system metrics (CPU, memory, etc.)       |
| Transport Protocol| MQTT or HTTPS     | Lightweight and reliable communication protocol  |
| Gateway           | Mosquitto         | Open-source MQTT broker                          |

---

### 🧬 2. Ingestion & Normalization

| Component        | Tool                 | Purpose                              |
|-----------------|----------------------|--------------------------------------|
| Log Pipeline     | Logstash / Vector.dev| Parse and normalize logs             |
| Metrics Pipeline | OpenTelemetry        | Normalize metrics from Telegraf      |
| Buffering Layer  | Apache Kafka         | Stream and buffer data at scale      |

---

### 🛢️ 3. Storage

| Type     | Tool                          | Purpose                                 |
|----------|-------------------------------|-----------------------------------------|
| Logs     | Elasticsearch or Grafana Loki| Structured log storage with search      |
| Metrics  | Prometheus or VictoriaMetrics| Time-series storage for metrics         |
| AI Logs  | PostgreSQL + pgvector         | Store parsed logs and LLM embeddings    |

---

### 📊 4. Visualization

| Component     | Tool             | Purpose                                           |
|---------------|------------------|---------------------------------------------------|
| Dashboard     | Grafana          | Unified dashboards for metrics, logs, AI insights|
| Log Viewer    | Kibana / Grafana | Explore logs and error details                   |
| Alerting      | Alertmanager     | Trigger alerts based on thresholds or anomalies  |

---

### 🧠 5. AI & Anomaly Detection

| Component         | Tool                     | Purpose                                       |
|------------------|--------------------------|-----------------------------------------------|
| LLM Integration   | Ollama + Mistral / LLaMA | Self-hosted local LLMs for log parsing        |
| AI Framework      | LangChain (Python)       | Pipeline for prompt engineering + retrieval   |
| Vector Search     | Qdrant / Weaviate / PGVector | Semantic search on parsed logs           |
| Embedding Model   | sentence-transformers     | Convert logs into vector format               |
| Anomaly Detection | PyOD / Scikit-learn      | ML-based anomaly detection (e.g., Isolation Forest) |

---

### 🔌 6. API & Orchestration

| Component    | Tool           | Purpose                                      |
|-------------|----------------|----------------------------------------------|
| API Server   | FastAPI        | REST endpoints for ingestion, querying, AI   |
| Task Queue   | Celery + Redis | Background processing of logs and alerts     |
| Auth         | Keycloak       | Optional OAuth2-based user management        |

---

## 📦 Tech Stack Summary

### 🚀 Core Language & API Framework
| Purpose        | Tool        | Notes                                   |
|----------------|-------------|-----------------------------------------|
| API Server     | FastAPI     | Async Python web framework, fast & modern |
| Task Queue     | Celery      | Background job processing               |
| Queue Backend  | Redis       | Celery broker and caching layer         |

### 📥 Data Collection
| Component         | Tool           | Notes                              |
|------------------|----------------|------------------------------------|
| Log Forwarder     | Fluent Bit     | Log collection from terminals      |
| Metric Collector  | Telegraf       | System metrics from terminals      |
| Messaging Broker  | Mosquitto      | MQTT broker for device messages    |

### 📡 Ingestion & Streaming
| Component     | Tool             | Notes                              |
|---------------|------------------|------------------------------------|
| Message Bus   | Apache Kafka     | Scalable ingestion                 |
| Log Parser    | Logstash / Vector| Transform raw logs into structure  |
| Metrics Agent | OpenTelemetry    | Normalize metrics and traces       |

### 💾 Storage
| Data Type   | Tool                   | Purpose                           |
|-------------|------------------------|-----------------------------------|
| Logs        | Elasticsearch / Loki   | Structured log storage            |
| Metrics     | Prometheus             | Time-series metrics               |
| AI Results  | PostgreSQL + pgvector  | Store structured insights + vectors |

### 🧠 AI Stack
| Component         | Tool                  | Purpose                        |
|------------------|-----------------------|--------------------------------|
| Local LLM        | Ollama + Mistral/LLaMA| Parse & summarize logs locally |
| AI Orchestration | LangChain             | Prompt, route, parse           |
| Embedding        | sentence-transformers | Log → Vector embeddings        |
| Vector DB        | Qdrant / Weaviate     | Semantic search on log history |
| Anomaly Detect   | PyOD / Scikit-learn   | Detect behavioral anomalies     |

### 📊 Visualization
| Tool       | Purpose                        |
|------------|--------------------------------|
| Grafana    | Unified dashboards             |
| Loki       | Log exploration and search     |
| Alertmanager| Alerting and notifications    |
| Kibana     | Optional UI for Elasticsearch  |

---

## 🧪 Local Development

Logiq-AI is fully containerized using Docker Compose for easy local development and testing.

### 📁 Suggested Project Structure

```
(to review)

logiq-ai/
├── backend/
│   ├── main.py               # FastAPI entrypoint
│   ├── ai/
│   │   └── log_parser.py     # LLM logic (LangChain + Ollama)
│   ├── models/
│   ├── routes/
│   └── tasks/
│       └── celery_worker.py
├── docker/
│   ├── Dockerfile.api
│   ├── Dockerfile.celery
│   └── ollama_start.sh
├── docker-compose.yml
├── grafana/
│   └── dashboards/
├── prometheus/
│   └── prometheus.yml
├── requirements.txt
└── README.md
```

### 🐳 Docker Compose Services

| Service         | Description                                |
|----------------|--------------------------------------------|
| fastapi-api     | Main API service (FastAPI)                 |
| celery-worker   | Handles background log parsing, AI jobs    |
| redis           | Celery broker and cache                    |
| postgres        | Structured storage (with pgvector)         |
| ollama          | Local LLM hosting                          |
| qdrant          | Vector DB for log search                   |
| kafka + zookeeper | Message streaming backend               |
| mosquitto       | MQTT broker for terminal simulation        |
| fluent-bit      | Simulate terminal log forwarding           |
| prometheus      | Metrics backend                            |
| grafana         | Dashboards for logs, metrics, and insights|
| loki            | Log storage backend                        |

### 🔌 Ports Exposed

- FastAPI: `localhost:8000`
- Grafana: `localhost:3000`
- Prometheus: `localhost:9090`
- PostgreSQL: `localhost:5432`
- Ollama: `localhost:11434`
- Qdrant: `localhost:6333`
- Kafka: `localhost:9092`
- MQTT: `localhost:1883`

---

## 📈 Example AI Pipeline

1. Terminal logs collected via Fluent Bit
2. Logs sent through Kafka → parsed with Logstash / Vector.dev
3. Logs passed to LLM (via LangChain + Ollama)
4. AI returns:
   - Error category
   - Suggested fix
   - Natural-language summary
5. Results embedded + stored in vector DB
6. Dashboards display health, log trends, and AI summaries

---

## ✅ Implementation Plan

### Short-Term (Weeks 1–4)

- [ ] Set up development environment with Docker Compose
- [ ] Deploy MQTT broker and simulate terminal logs with Fluent Bit
- [ ] Stand up Kafka + Prometheus + Loki + Grafana stack
- [ ] Implement FastAPI API and Celery worker scaffold
- [ ] Parse logs via Logstash / Vector.dev → Loki
- [ ] Build initial Grafana dashboard for log/metrics visualization

### Mid-Term (Weeks 5–10)

- [ ] Integrate Ollama with local LLM (e.g., Mistral 7B)
- [ ] Set up LangChain pipeline for log classification and suggestions
- [ ] Deploy Qdrant or PGVector for vector storage
- [ ] Store AI-parsed logs + metadata in PostgreSQL
- [ ] Display AI insights in Grafana
- [ ] Add rule-based and AI-based anomaly detection
- [ ] Implement alerting via Alertmanager

---

## 🧪 Future Enhancements

- Predictive maintenance using trend analysis
- ChatOps interface (e.g., Slack bot: "What’s wrong with Terminal 232?")
- Remote remediation suggestions or actions
- Multi-tenant support for MSPs managing fleets of terminals

---

## 📝 License

This project is 100% open source and licensed under the MIT License.

---

## 🧠 Project
