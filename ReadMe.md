# LangChain Agents – Tool Calling Examples

This notebook demonstrates how to build simple LLM agents using **LangChain** that can call external tools (functions) to answer user questions, rather than relying purely on the model's internal knowledge.

## Overview

**Agent 1** follows this pattern:
1. Take a question from the user.
2. Decide whether a tool is needed.
3. Call the tool (a Python function) and use its output to produce the final answer.

Two implementations of this pattern are included, using different LLM providers.

---

## 1. Agent 1 – LangChain + OpenAI (Function-Calling Agent)

Uses `langchain_openai.ChatOpenAI` with the `gpt-4o` model and the `create_openai_functions_agent` constructor to build a full agent executor.

**Tool defined:** `get_weather(city: str)` – returns a mock weather response for a given city.

**Flow:**
- A prompt template (system message + user input + agent scratchpad placeholder) is built with `ChatPromptTemplate`.
- `create_openai_functions_agent` wires the LLM, tool, and prompt together.
- `AgentExecutor` runs the agent with `verbose=True` so you can see the reasoning/tool-call steps.
- Example query: *"What is the weather in San Francisco?"*

**Requirements:**
- `OPENAI_API_KEY` environment variable set to a valid OpenAI API key.

---

## 2. Agent 1 – LangChain + Groq (Manual Tool Calling)

Uses `langchain_groq.ChatGroq` with the `llama-3.3-70b-versatile` model, demonstrating the **raw tool-calling loop** without an `AgentExecutor`.

**Tool defined:** `get_code()` – returns a hardcoded "secret code" (`cod-12345`) that the model has no way of knowing without calling the tool.

**Flow:**
1. A system prompt instructs the model that it must use the tool to answer.
2. `llm.bind_tools(tools)` binds the tool to the model.
3. **Step 1:** The model is invoked and (if it decides to) returns a `tool_call` request instead of a direct answer.
4. The tool is executed manually in Python, and its result is wrapped in a `ToolMessage`.
5. **Step 2:** The full conversation (original messages + model's tool-call response + tool result) is sent back to the model to produce the **final answer**.

This example is useful for understanding what an `AgentExecutor` does under the hood.

**Requirements:**
- `GROQ_API_KEY` environment variable set to a valid Groq API key.

---

## Setup

Install dependencies:

```bash
pip install langchain langchain-openai langchain-groq python-dotenv
```

Set API keys either via environment variables or a `.env` file (loaded with `dotenv`):

```bash
export OPENAI_API_KEY="your-openai-key"
export GROQ_API_KEY="your-groq-key"
```

> ⚠️ **Security note:** The notebook currently sets API keys directly via `os.environ[...] = ""` in code cells. Before sharing or committing this notebook, remove any hardcoded keys and load them from a `.env` file or environment variables instead.

## Running

Run the notebook cells in order:
1. Set environment variables / load `.env`.
2. Run the OpenAI-based agent example.
3. Run the Groq-based manual tool-calling example.

Each section prints intermediate steps (tool calls, tool results) and the final model output.