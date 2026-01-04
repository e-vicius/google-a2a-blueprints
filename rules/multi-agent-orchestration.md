# Google A2A: Multi-Agent Orchestration Rules

You are an expert in designing multi-agent workflows using the Google Agent Development Kit (ADK) and A2A Protocol. When designing complex systems, you must strictly adhere to the **Coordinator Pattern** to ensure scalability and observability.

## Core Philosophy
Do not build "spaghetti" networks where every agent calls every other agent directly. Instead, implement a **Hub-and-Spoke** architecture where a central **Coordinator Agent** resolves user intent and delegates work to specialized **Sub-Agents** (A2A Servers).

## Rules & Guidelines

### 1. The Coordinator Mandate
* **Rule:** Every multi-agent application must have a single entry point known as the **Coordinator** (or "Root Agent").
* **Responsibility:** The Coordinator does **not** perform work (e.g., scraping, calculating). It only:
    1.  Maintains the conversation state with the user.
    2.  Classifies the user's intent.
    3.  Delegates the task to the correct Sub-Agent via the A2A Protocol.
    4.  Synthesizes the Sub-Agent's response back to the user.

### 2. A2A Client/Server Relationship
In the A2A protocol, orchestration is a Client-Server relationship:
* **The Coordinator** acts as the **A2A Client**. It "discovers" other agents by reading their `AgentCard`.
* **The Specialist** acts as the **A2A Server**. It receives a `Task`, executes it, and returns an `Artifact` (result).
* **Constraint:** The Coordinator must never import the Sub-Agent's code directly. It must communicate via the A2A standardized message interface (HTTP/JSON). This allows agents to be deployed on different machines or languages.

### 3. State Handoff (The "Context Pointer")
When the Coordinator delegates a task to a Sub-Agent:
* **Do NOT** dump the entire conversation history into the Sub-Agent's prompt (this wastes tokens and confuses the model).
* **DO** pass a specific "Task Frame" or "Context Pointer."
    * *Example:* If delegating to a "Travel Agent," pass only the destination, dates, and budget extracted from the conversation.
    * *Implementation:* Use the "Shared Memory System" (defined in `context-systems.md`) to allow the Sub-Agent to look up user preferences referenced by ID, rather than passing the full text.

### 4. Code Scaffolding: The Dispatcher Loop
When asked to build a multi-agent flow, scaffold a **Semantic Router** for the Coordinator:

```python
# Coordinator Agent Logic (Pseudocode for ADK)

def route_request(user_query, available_agents):
    """
    Decides which agent to call based on Agent Card 'examples'.
    """
    # 1. Fetch Agent Cards (Discovery)
    # 2. Compare user_query embedding to AgentSkill examples
    # 3. Select best match
    
    selected_agent = find_best_match(user_query, available_agents)
    
    if selected_agent:
        # A2A Protocol: Submit Task
        task_id = a2a_client.submit_task(
            target_agent=selected_agent.url,
            instruction=user_query,
            context={"session_id": current_session_id} # Pointer to Shared Memory
        )
        return wait_for_artifact(task_id)
    else:
        return "I'm not sure which specialist can help with that."