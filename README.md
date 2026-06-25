# Redag

> Enterprise‑grade AI workflow automation platform — design reusable DAG pipelines that combine files, models, prompts, tools, and notifications.

[English](README.en.md) &middot; [Architecture](Architect.md) &middot; [Contracts](docs/COMMON_CONTRACTS.md) &middot; [Project Structure](docs/PROJECT_STRUCTURE.md)

![Java 17](https://img.shields.io/badge/Java-17-007396?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2.12-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)
![Vue 3](https://img.shields.io/badge/Vue-3.5-42B883?style=for-the-badge&logo=vuedotjs&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-8-646CFF?style=for-the-badge&logo=vite&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)

---

## Overview

Redag is an AI-native orchestration layer for enterprise automation. Users visually compose DAG workflows in a drag‑and‑drop canvas, connecting file uploads, OCR, transcription, summarization, translation, model calls, data processing, and task notifications into traceable, reusable pipelines.

The frontend handles visual design and runtime observability; Java micro‑services manage identity, file governance, workflow definitions, task dispatch, and AI orchestration; a Python service stays close to model inference and media processing.

![Workflow Editor]()

---

## Features

| Capability | Description |
|---|---|
| **Visual DAG Editor** | Vue Flow canvas for arranging nodes and connections |
| **File Governance** | Chunked upload, MinIO object storage, metadata tracking |
| **AI Task Scheduling** | RabbitMQ async queues, Python AI service adapters |
| **Real‑time Feedback** | WebSocket / SSE streams for run status, node progress, and errors |
| **Enterprise Infrastructure** | Spring Cloud Gateway, Nacos, Sentinel, Seata, Redis, MySQL |
| **One‑click Deploy** | Docker Compose + Nginx entry point |

---

## Use Cases

- **Document Processing** &ndash; upload PDF/Word, extract text, summarize, translate, and export.
- **OCR** &ndash; recognize scanned documents, validate fields, route for review and archive.
- **Meeting & Audio/Video** &ndash; transcribe, summarize, generate minutes, produce SRT subtitles.
- **AI Video Generation** &ndash; prompt &rarr; storyboard &rarr; image/video model &rarr; encode &rarr; publish.
- **Reporting** &ndash; load CSV/Excel, clean, aggregate, chart, and schedule distribution.
- **Knowledge Base** &rarr; ingest documents, chunk, embed, tag, and serve Q&A.

---

## Architecture

```
                    +----------------------+
                    |      Web Console     |
                    | Vue 3 + Vue Flow     |
                    +----------+-----------+
                               |
                        Nginx / Gateway
                               |
        +----------------------+----------------------+
        |                      |                      |
  auth-service          workflow-service       notify-service
  JWT / OAuth           DAG definitions        WebSocket / SSE
        |                      |                      |
        +-----------+----------+-----------+----------+
                    |                      |
              file-service            task-service
        MinIO / metadata / cache       RabbitMQ tasks
                    |                      |
                    +----------+-----------+
                               |
                           ai-service
                 provider orchestration / node runtime
                               |
                       python-ai-service
             Whisper / OCR / media / model adapters
```

**Core run path:**

```
User uploads file / submits parameters
&rarr; file-service stores to MinIO
&rarr; workflow-service creates instance & parses DAG
&rarr; task-service records & queues to RabbitMQ
&rarr; ai-service consumes & delegates to python-ai-service or external providers
&rarr; file-service saves derived files & structured results
&rarr; notify-service pushes status via WebSocket / SSE
```

---

## Tech Stack

| Layer | Technologies |
|---|---|
| **Backend** | Java 17, Maven, Spring Boot 3.2.12, Spring Cloud 2023.0.5 |
| **Service Mesh** | Gateway, Nacos, OpenFeign, Sentinel, Seata, Springdoc OpenAPI |
| **Data & Middleware** | MyBatis Plus, MySQL 8.0, Redis, RabbitMQ, MinIO, XXL-Job |
| **AI & Media** | Spring AI, FastAPI, OpenAI SDK, Ollama, faster-whisper, FFmpeg |
| **Frontend** | Vue 3, Vue Router 5, Pinia, Vite 8, TypeScript, Tailwind CSS, Vue Flow |
| **Deployment** | Docker Compose, Nginx, WebSocket, SSE |

---

## Quick Start

### Prerequisites

- JDK 17, Maven 3.9+, Node.js 20+
- Docker Desktop / Docker Engine

### Local Build

```powershell
$env:JAVA_HOME = 'C:\Program Files\Microsoft\jdk-17.0.19.10-hotspot'
$env:Path = "$env:JAVA_HOME\bin;$env:Path"
mvn test
```

```powershell
cd frontend
npm install
npm run build
```

### Docker Compose

```powershell
docker compose up -d --build
```

Open [http://localhost](http://localhost). If port 80 is occupied, set `NGINX_HTTP_PORT` in `.env`.

---

## New in This Build

- **Workflow Duplication** &ndash; the `Duplicate` button in the workflow editor clones the current pipeline under a new name, creating a fresh definition in one click.
- **Portfolio‑ready branding** &ndash; header, copyright, and UI strings are customised for presentation.

---

## Ports

| Service | Port |
|---|---|
| Nginx | 80 |
| gateway-service | 8080 |
| auth-service | 8101 |
| workflow-service | 8102 |
| task-service | 8103 |
| ai-service | 8104 |
| file-service | 8105 |
| notify-service | 8106 |
| python-ai-service | 8200 |
| Nacos | 8848 |
| MySQL | 3307 &rarr; 3306 |
| Redis | 6379 |
| RabbitMQ | 5672 / 15672 |
| MinIO | 9000 / 9001 |
| Seata | 8091 |

---

## Project Structure

```
backend/
  common/             Shared DTOs, error codes, JWT, MQ event envelopes
  gateway-service/    API gateway
  auth-service/       Authentication & identity
  workflow-service/   Workflow definitions & instances
  task-service/       Task records & message dispatch
  ai-service/         AI node orchestration & provider calls
  file-service/       File uploads, MinIO, metadata
  notify-service/     WebSocket / SSE notifications
frontend/             Vue 3 workspace UI
python-ai-service/    Python AI inference & media processing
ai-runtime/           Windows local demo runtime
docker/               Infrastructure configs (MySQL, RabbitMQ, Seata, etc.)
performance-test/     JMeter scripts
docs/                 Contracts, deployment guides, architecture
```

---

## Contributing

Please read the [common contracts](docs/COMMON_CONTRACTS.md) before submitting changes. All micro‑services share the response structure, error codes, JWT helpers, and MQ event envelopes from `backend/common`.

Recommended entry points:
- [Architect.md](Architect.md) &ndash; overall architecture
- [Project Structure](docs/PROJECT_STRUCTURE.md)
- [Common Contracts](docs/COMMON_CONTRACTS.md)
- [Deployment Checklist](docs/deployment/final-production-like-checklist.md)

---

*Redag &mdash; AI workflow orchestration for the modern enterprise.*
