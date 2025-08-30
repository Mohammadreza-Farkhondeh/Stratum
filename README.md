# Stratum

**Stratum** is a **stateless, typed-first Python library** that provides a **foundation layer** for building LLM-powered systems.

Unlike heavy frameworks, Stratum is **minimal and unopinionated**: it defines **interfaces (Protocols)**, **typed data models (Pydantic)**, and **small composable utilities**.
You can assemble your own architecture — chatbots, retrieval pipelines, or tool-using agents — with **no hidden magic**.

Think of Stratum as the **bedrock (strata)**: strong, layered, and designed to last.

---

## Philosophy

* **Minimal, not monolithic**
  Stratum is not another LangChain. It doesn’t try to “own” your flow. It provides *just enough* contracts and utilities.

* **Stateless, not stateful**
  Stratum does not hide memory. Everything is passed in explicitly: `Context`, `Message`, `Params`.

* **Interfaces, not implementations**
  The library is driven by **Protocols** (interfaces). Providers (OpenAI, FAISS, custom APIs) are plugged in as adapters.

* **Typed, not loose**
  Every input/output is a **Pydantic model** or **typed contract**. This ensures predictability in enterprise settings.

* **Composable, not prescriptive**
  Pipelines are simple functions. You can decorate them, compose them, or swap components.

---

Each domain has:

* **`base.py`** — Protocols (interfaces).
* **Adapters** — optional concrete implementations.
* **Utilities** — small helpers (pipelines, middlewares).

---

## Core Concepts

### 1. Context

* Immutable metadata passed into every call.
* Contains tenant ID, user ID, locale, and optional extras.
* Ensures **auditability** and **multi-tenant safety**.

### 2. Message

* One conversational unit.
* Roles: `system`, `user`, `assistant`, `tool`.
* Supports metadata (timestamps, tags, provenance).

### 3. Outcome

* Lightweight result wrapper: `ok` or `err`.
* Prevents uncontrolled exception bubbling.
* Helps make **pipelines predictable**.

### 4. Params

* Typed parameters for model calls (e.g. temperature, max tokens).
* Keeps hyperparameters explicit and controlled.

---

## Interfaces (Protocols)

* **LLM** → generates messages.
* **Embeddings** → converts text to vectors.
* **VectorStore** → inserts/queries vectors.
* **Retriever** → fetches documents given a query.
* **Command / ToolRegistry** → strongly typed tools with Pydantic I/O.

> All are **stateless protocols**. You can implement adapters for OpenAI, local models, FAISS, Pinecone, etc.

---

## Pipelines

Pipelines are **builders** that return **pure callables**.
They encapsulate common patterns but stay minimal.

### 1. Chat Pipeline

* Wraps an LLM.
* Signature: `(Context, [Messages]) → Message`.

### 2. Retrieval-Augmented Generation (RAG) Pipeline

* Combines LLM + Retriever.
* Fetches documents → appends as context → asks LLM.
* Signature: `(Context, [Messages]) → Message`.

### 3. Tool-Use Pipeline

* Lets the LLM request tool calls.
* Executes typed tools via registry.
* Returns combined response.

---

## Middlewares

Stratum supports **explicit functional middlewares**:

* **Retry** → retry on errors.
* **Tracing** → emit structured logs.
* **Redaction** → sanitize sensitive data.
* **Rate limiting** (future).

Apply them as decorators:

```
safe_rag = with_retry()(with_tracing("rag")(rag_pipeline))
```

No global injection, no hidden flow.

---

## Testing Support

Stratum includes **fakes** for rapid unit testing:

* `FakeLLM` — returns canned responses.
* `FakeVectorStore` — in-memory index.
* `FakeRetriever` — returns fixed docs.

This makes pipelines **deterministic** in tests and CI.

---

## Example Workflows

### Retrieval-Augmented Chat

1. Provide an LLM (e.g. OpenAI).
2. Provide a Retriever (e.g. FAISS).
3. Build pipeline with `build_rag()`.

Flow:

* User asks: *“Why are night alarms higher?”*
* Retriever fetches relevant docs.
* Pipeline feeds docs as context.
* LLM generates grounded answer.

---

### Tool-Using Assistant

1. Define tools with typed Pydantic I/O.
2. Register them in `InMemoryRegistry`.
3. Build pipeline with `build_tool_use()`.

Flow:

* User: *“Schedule pumps at 150 m³/h.”*
* LLM requests `compute_pump_schedule`.
* Registry executes typed tool.
* Tool result is appended.
* LLM generates final explanation.

---

## Design Patterns Applied

* **Adapter** — for providers (OpenAI, FAISS, etc.).
* **Strategy** — for retrieval, ranking, planning.
* **Command** — for typed tools.
* **Chain of Responsibility** — for middlewares.
* **Builder** — for pipelines.
* **Observer (opt-in)** — for tracing/telemetry.

This ensures Stratum is **extensible but stable**.

---

## Enterprise Features

* **Observability** → structured traces of LLM calls, retrievals, tool executions.
* **Compliance** → audit-ready `Context` with tenant/user metadata.
* **Flexibility** → swap providers without touching core.
* **Testability** → fakes and strict typing make regression testing robust.
* **Scalability** → works in serverless, microservices, or on-prem edge.

---

## Roadmap

* ✅ Core contracts, adapters, pipelines.
* ✅ In-memory & OpenAI examples.
* ⬜ Async interface support.
* ⬜ More adapters: Ollama, Pinecone, Elastic.
* ⬜ Middlewares: caching, circuit breaker.
* ⬜ Release to PyPI.
* ⬜ Documentation site with guides.

---

## Development Guide

* **Python**: 3.10+
* **Lint/format**: `ruff`, `black`
* **Typing**: `mypy --strict`
* **Tests**: `pytest`
* **CI**: GitHub Actions running lint + mypy + tests

---

## Philosophy Recap

* **Layered like geological strata** — stable foundation.
* **Minimal by design** — no overengineering.
* **Unopinionated** — bring your own stack.
* **Future-ready** — multi-LLM, multi-modal, edge/cloud.

Stratum is your **bedrock for AI systems**.

---

## License

[MIT](./LICENSE) — open and permissive.
