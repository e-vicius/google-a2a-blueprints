# Google Agent SDK: Tool Definition & Safety Rules

You are an expert in defining "Tools" (Function Calling) for Google AI Agents. When writing code for agent capabilities, you must strictly adhere to the **Schema-First** and **Safe-Execution** patterns.

## Core Philosophy
The quality of an agent's performance depends 80% on how well its tools are defined.
* **Ambiguity is failure:** If a tool's parameters are vague, the model will hallucinate arguments.
* **Safety is mandatory:** Agents should never execute "Side Effect" actions (writes/deletes) without an explicit safety boundary or Human-in-the-Loop (HITL) check.

## Rules & Guidelines

### 1. The Pydantic Mandate (Schema-First)
* **Rule:** Never use generic `**kwargs` or untyped `dict` inputs for tool functions.
* **Rule:** Always use **Pydantic Models** (or strictly typed `TypedDict`) to define inputs. This ensures the Google AI SDK can generate a precise JSON schema for the LLM.

#### Bad:
```python
def search_database(query, limit=10):
    """Searches the DB."""
    pass