# Google Agent Blueprints

> **System instructions, architectural rules, and `.cursorrules` for building scalable AI Agents with the Google A2A Protocol and Google AI Agent SDK.**

![Google AI](https://img.shields.io/badge/Google_AI-Agent_SDK-blue?style=flat-square) ![Protocol](https://img.shields.io/badge/Protocol-A2A-red?style=flat-square) ![IDE](https://img.shields.io/badge/Compatible_With-Cursor_|_Windsurf-success?style=flat-square)

## 📖 About This Repository

Building production-grade AI agents requires more than just good prompts—it requires strict architectural enforcement. 

**Google Agent Blueprints** is a curated collection of **System Prompts** and **IDE Rulesets** designed to force your AI coding assistant (Cursor, Windsurf, Antigravity, etc.) to adhere to best practices defined by Google's Agent-to-Agent (A2A) protocol and the Google AI Agent SDK.

Instead of manually reminding your AI to "use a shared database" or "follow the A2A handshake," you can drop these files into your project to enforce those patterns automatically.

## 🚀 The Tech Stack

This repository focuses specifically on:
* **Google AI Agent SDK:** Best practices for scaffolding agents, tool definitions, and standard implementations.
* **Google A2A (Agent-to-Agent) Protocol:** Rules for defining how independent agents discover, handshake, and communicate with each other securely.
* **Context Systems:** Architectural patterns for shared memory and state management across multi-agent fleets.

## 📂 Repository Contents

| Blueprint | Description | Use Case |
| :--- | :--- | :--- |
| **[`context-systems.md`](./context-systems.md)** | Enforces the "Shared Memory" architecture. Prevents siloed agent state by mandating a central memory microservice. | Multi-Agent Fleets |

## 🛠 How to Use

### For Cursor / Windsurf / Antigravity
1.  Navigate to the pattern you want to implement (e.g., [`context-systems.md`](./context-systems.md)).
2.  Copy the raw content.
3.  Paste it into your project's **`.cursorrules`** or **`.windsurfrules`** file (or your specific IDE's system instruction settings).
4.  **Result:** Your AI assistant now "knows" how to architect systems according to that specific Google protocol and will refuse to generate code that violates it.

### For Custom Agents
You can also inject these markdown files directly into your Agent's **System Prompt** to ensure they understand their own architectural constraints at runtime.

## 🤝 Contributing

We welcome contributions! If you have optimized a set of rules for the Google Agent ecosystem or found a better way to instruct AI IDEs on A2A patterns, please submit a PR.

---

### **Looking for the latest Context System rules?**
Check out [context-systems.md](./context-systems.md) to see how we define shared memory architectures based on the latest Google Cloud Tech deep dives.