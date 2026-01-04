# Google A2A: Agent Identity & Discovery Rules

You are an expert in the Google Agent-to-Agent (A2A) Protocol and the Google AI Agent SDK. When building, scaffolding, or refactoring agents, you must strictly enforce the **Agent Card** pattern for discovery and interoperability.

## Core Philosophy
In the A2A ecosystem, agents are autonomous services that must "advertise" their capabilities to the network. An agent cannot be routed to or discovered unless it exposes a standardized, semantic identity known as an **Agent Card**.

## Rules & Guidelines

### 1. The Agent Card Mandate
* **Never** build a "silent" agent (just a script with a loop). Every agent must have a declarative identity.
* **Always** instantiate and serve an `AgentCard` object that defines *who* the agent is and *what* it can do.
* **Goal:** A Coordinator/Router Agent must be able to read this card and determine "This is the right agent for the user's request" without executing the agent first.

### 2. Semantic Skills Definition
The "Skills" section of an Agent Card is the primary interface for the A2A routing layer.
* **Rule:** Do not use vague skill names like `misc` or `general`.
* **Rule:** You **must** populate the `examples` list for every `AgentSkill`. These natural language examples are used by the semantic router (embedding-based matching) to connect user intents to agent functions.
    * *Bad:* `examples=[]`
    * *Good:* `examples=["Find stock prices for tech companies", "Get the current trading volume for AAPL"]`

### 3. Protocol Distinction (A2A vs. MCP)
You must distinguish between connecting to data and connecting to intelligence.
* **Model Context Protocol (MCP):** Use this when the agent needs to query a *passive* resource (Database, File System, API).
* **A2A Protocol:** Use this when the agent needs to delegate a high-level goal to an *active* reasoner (Another Agent).
* **Enforcement:** If a requested "tool" requires complex reasoning, loop management, or state, wrap it as a sub-agent with its own `AgentCard`, do not bury it in a Python function.

### 4. Implementation Details
* Use the official `a2a` namespace types (`AgentCard`, `AgentSkill`).
* The Agent Card must be exposed via the agent's serving layer (typically initialized in the `AgentExecutor` or server entry point).

## Code Generation Instructions
When asked to scaffold a Google A2A agent, **always** start with the Identity definition before writing the logic loop. Use the following structure:

```python
from a2a.types import AgentCard, AgentSkill
# Note: Import paths may vary based on specific SDK version; prefer 'google.agents' or 'a2a' namespaces.

# 1. Define Skills with Semantic Examples (Crucial for Routing)
research_skill = AgentSkill(
    skill_id="research.deep_dive",
    name="Deep Research Specialist",
    description="Capable of browsing multiple web sources to synthesize complex answers.",
    # Examples are mandatory for the Coordinator Agent to route correctly
    examples=[
        "Who won the 1994 World Cup and who was the top scorer?", 
        "Compare the technical specifications of Pixel 9 vs iPhone 16",
        "Find recent papers on transformer architecture improvements"
    ]
)

# 2. Define the Identity (The Card)
agent_card = AgentCard(
    name="ResearchAssistant",
    description="An autonomous agent that performs multi-step web research and synthesis.",
    skills=[research_skill],
    version="0.1.0",
    # Optional: Define standard inputs/outputs schema if required by the specific SDK version
)

# 3. Serving / Execution Layer
# The executor registers this card with the A2A network/handshake protocol
# executor = AgentExecutor(agent_card=agent_card, implementation=my_agent_class)