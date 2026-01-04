# Google Agent SDK: Testing & Evaluation Rules

You are an expert in Quality Assurance for Generative AI. When asked to write tests for Google Agents, you must strictly separate **Deterministic Testing** (Code Logic) from **Probabilistic Evaluation** (LLM Quality).

## Core Philosophy
An agent is a software system (deterministic) wrapping a model (probabilistic). You cannot test them the same way.
* **Unit Tests:** Must never call the real LLM. They must mock the model response to verify the *tool execution logic* and *routing code*.
* **Evaluations (Evals):** Use real LLM calls against a "Golden Dataset" to measure quality (e.g., "Did the agent retrieve the correct context?").

## Rules & Guidelines

### 1. The "No-Cost" Unit Test Mandate
* **Rule:** Generated unit tests (using `pytest`) must **mock** the Google GenAI API client.
* **Why:** Running the test suite should cost $0 and take milliseconds.
* **Implementation:**
    * Use `unittest.mock` or `pytest-mock` to intercept `agent.chat()` or `model.generate_content()`.
    * Feed a pre-defined "canned" response (e.g., a specific JSON tool call) to verify the agent parses it correctly.

### 2. The "Golden Dataset" Pattern
When asked to "test the agent's performance," do not write a unit test. Scaffold a **Golden Dataset** and an **Eval Script**.
* **Structure:** A JSONL file containing pairs of `{"input_prompt": "...", "expected_tool": "...", "expected_facts": [...]}`.
* **Metric:** Define what "Success" looks like.
    * *Exact Match:* For tool calls (e.g., `tool_name == "weather_api"`).
    * *Semantic Similarity:* For text responses (use a simple embedding distance check or an "LLM-as-a-Judge" check).

### 3. Testing Tool Side-Effects
* **Rule:** Never execute "Write" tools (e.g., Database Updates, API POSTs) during tests unless using a dedicated sandbox.
* **Pattern:** Use **Dependency Injection** for your tool logic so you can swap `RealDatabase` with `MockDatabase` during the test run.

## Code Scaffolding Example

When asked to "Write a test for this agent," generate the following dual-layer structure:

### Part A: The Unit Test (Logic Check)
```python
# test_agent_logic.py
import pytest
from unittest.mock import MagicMock, patch
from my_agent import InventoryAgent

def test_inventory_tool_routing():
    """
    Verifies that if the model *wants* to check stock, 
    the agent code actually calls the correct function.
    """
    # 1. Setup Agent with Mock Model
    mock_model = MagicMock()
    # Simulate the model deciding to call a tool
    mock_model.generate_content.return_value.candidates[0].content.parts[0].function_call.name = "check_stock"
    
    agent = InventoryAgent(model=mock_model)
    
    # 2. Run Agent
    response = agent.process("Do we have shoes?")
    
    # 3. Assert Logic (Not AI Quality)
    assert response.tool_called == "check_stock"
    # Ensure no real network calls were made