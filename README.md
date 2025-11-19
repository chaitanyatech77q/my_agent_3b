Memory Management – Part 2 (Long-Term Memory for Agents)

This repository contains the implementation of Memory Management – Part 2, built using the Google Agent Development Kit (ADK) as part of the Kaggle 5-Day Agents Course (Day 3).
It extends the previous work on Sessions by introducing Memory, enabling long-term recall across multiple conversations.

Overview

In Part 1, Sessions provided short-term memory for a single conversation.
In Part 2, Memory introduces:

Long-term knowledge storage

Cross-conversation recall

Semantic and keyword-based search

Persistent or temporary storage (depending on backend)

Memory allows agents to remember information such as user preferences, personal details, or events — even across multiple sessions.

Why Memory?

Memory provides capabilities that Sessions alone cannot:

Capability	Description	Example
Cross-Conversation Recall	Retrieves past information across different chats	“What preferences has this user mentioned before?”
Intelligent Extraction	Extracts key facts instead of raw messages	“Allergic to peanuts”
Semantic Search	Meaning-based retrieval	“Preferred hue” matches “favorite color”
Persistent Storage	Retains information across restarts	Long-term personalization
What You Will Learn

How to initialize and configure a MemoryService

How to connect Memory to your agent via the Runner

How to ingest session data into memory

How to search and retrieve memory

How to automate memory storage using callbacks

Difference between raw storage and memory consolidation

Setup
1. Installation (Optional for local development)
pip install google-adk

2. Configure the Gemini API Key
from kaggle_secrets import UserSecretsClient
import os

os.environ["GOOGLE_API_KEY"] = UserSecretsClient().get_secret("GOOGLE_API_KEY")

Importing Required Components
from google.adk.agents import LlmAgent
from google.adk.models.google_llm import Gemini
from google.adk.runners import Runner
from google.adk.sessions import InMemorySessionService
from google.adk.memory import InMemoryMemoryService
from google.adk.tools import load_memory, preload_memory
from google.genai import types

Memory Workflow

The Memory integration follows three core steps:

Initialize → Ingest → Retrieve

1. Initialize MemoryService
memory_service = InMemoryMemoryService()

2. Create Agent
user_agent = LlmAgent(
    model=Gemini(model="gemini-2.5-flash-lite"),
    name="MemoryDemoAgent",
    instruction="Answer user questions in simple words."
)

3. Create Runner with Session + Memory Services
session_service = InMemorySessionService()

runner = Runner(
    agent=user_agent,
    app_name="MemoryDemoApp",
    session_service=session_service,
    memory_service=memory_service
)

Ingesting Session Data into Memory
await memory_service.add_session_to_memory(session)


This transfers the conversation’s events into the MemoryService so that future sessions can retrieve them.

Memory Retrieval Tools

ADK provides two built-in tools for retrieval:

Tool	Description
load_memory	Reactive — agent searches memory only when needed
preload_memory	Proactive — searches memory before every turn
Example:
user_agent = LlmAgent(
    model=Gemini(model="gemini-2.5-flash-lite"),
    tools=[load_memory]
)

Manual Memory Search
response = await memory_service.search_memory(
    app_name=APP_NAME,
    user_id=USER_ID,
    query="favorite color"
)


Useful for debugging, analytics, or dashboards.

Automating Memory Storage (Callbacks)
Callback for automatic saving:
async def auto_save_to_memory(callback_context):
    await callback_context._invocation_context.memory_service.add_session_to_memory(
        callback_context._invocation_context.session
    )

Agent with automated memory management:
auto_agent = LlmAgent(
    model=Gemini(model="gemini-2.5-flash-lite"),
    tools=[preload_memory],
    after_agent_callback=auto_save_to_memory
)


This setup automatically saves memory after each turn and loads memory automatically for every new input.

Memory Consolidation (Concept)

There are two types of memory storage:

Raw Storage

Used by InMemoryMemoryService

Stores all events as-is

Keyword-based search

No persistence

Consolidated Memory

Used by Vertex AI Memory Bank (covered later)

LLM-powered extraction

Semantic search

Deduplication

Persistent cloud storage

Summary

This project demonstrates:

How to initialize and integrate Memory into ADK-based agents

How to transfer session data to Memory

How to retrieve and search for memories

How to use load_memory and preload_memory tools

How to automate memory with callbacks

The conceptual foundation of memory consolidation

This completes Memory Management (Part 2) of the Kaggle Agents Course.
