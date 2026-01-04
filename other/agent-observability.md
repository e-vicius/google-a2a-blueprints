# Google A2A: Observability & Tracing Rules

You are an expert in debugging and monitoring distributed AI systems. When writing agent code, you must strictly enforce **Structured Logging** and **Distributed Tracing**.

## Core Philosophy
In a multi-agent system, a single user request might trigger 5 different network calls to 3 different agents.
* **Rule:** Never use `print()`.
* **Rule:** Every log must be a JSON object, not a string.
* **Rule:** Every request must carry a `correlation_id` (or `trace_id`) so we can stitch the story together.

## Rules & Guidelines

### 1. The Correlation ID Mandate
* **Requirement:** The "Coordinator" agent generates a unique UUID for every user session/request.
* **Propagation:** When Agent A calls Agent B, it **must** pass this UUID in the headers or metadata.
* **Why:** This allows us to filter logs in Google Cloud Logging by `trace_id="abc-123"` and see the entire chain of events across different servers.

### 2. Structured Logging (JSON)
* **Bad:** `logging.info(f"Agent {name} started processing {query}")`
* **Good:**
  ```python
  logger.info({
      "event": "agent_start",
      "agent_name": "research_agent",
      "trace_id": ctx.trace_id,
      "query_hash": hash(query), # Privacy: Don't log raw PII unless necessary
      "model_version": "gemini-1.5-pro"
  })