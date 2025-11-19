Memory Management – Part 2 (ADK)

Long-term memory implementation for agentic systems using the Google ADK.
Extends Session-based short-term memory by adding a searchable, persistent Memory layer.

<p align="center"> <img src="https://i.imgur.com/aQjs6kN.png" width="700"> </p>
🔍 Overview

Part 1 covered Sessions (short-term context).
Part 2 introduces Memory, enabling:

Recall across conversations

Storing key facts instead of raw messages

Keyword or semantic search

Long-term personalized knowledge

<p align="center"> <img src="https://i.imgur.com/SI9g03K.png" width="600"> </p>
🚀 Core Components
Memory Workflow
Initialize → Ingest → Retrieve

Initialize Services
memory_service = InMemoryMemoryService()
session_service = InMemorySessionService()

Create Agent + Runner
agent = LlmAgent(model=Gemini("gemini-2.5-flash-lite"))
runner = Runner(
    agent=agent,
    app_name="MemoryDemoApp",
    session_service=session_service,
    memory_service=memory_service
)

📥 Ingest Session → Memory
await memory_service.add_session_to_memory(session)


Transfers session history into long-term storage.

🔎 Retrieve Memory
Agent Tools
tools=[load_memory]      # reactive
tools=[preload_memory]   # proactive

Manual Search
await memory_service.search_memory(APP_NAME, USER_ID, "favorite color")

🤖 Automatic Memory Saving
async def auto_save(ctx):
    await ctx._invocation_context.memory_service.add_session_to_memory(
        ctx._invocation_context.session
    )

agent = LlmAgent(..., tools=[preload_memory], after_agent_callback=auto_save)

🧠 Consolidation (Concept)

InMemoryMemoryService → raw storage (no persistence)

Vertex AI Memory Bank → semantic search + fact extraction + durable cloud memory

<p align="center"> <img src="https://i.imgur.com/3Qxr9yK.png" width="650"> </p>
✅ Summary

This compact notebook teaches:

How to integrate MemoryService

How to store session data for long-term recall

How to retrieve memory using tools or queries

How to automate memory management

How memory consolidation works conceptually

Perfect foundation for Vertex AI Memory Bank in the next module.
