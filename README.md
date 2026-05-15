<img width="1480" height="820" alt="image" src="https://github.com/user-attachments/assets/7486907d-e843-46e1-8e3a-3b7d8a344315" />




## Sliding Window Context Optimization

One of the core achievements of this agent is a **token-efficient context management system** that keeps long conversations affordable without losing important history.

### The Problem

LLMs have a fixed context window. In a long coding session, naively sending the full conversation history on every query means:
- Token usage grows unboundedly with every message
- API costs increase linearly with conversation length
- Eventually the context limit is hit and the agent breaks

### The Solution: Sliding Window + Summarization

Instead of sending the full history, the agent:

1. **Splits** the conversation into turns (each Human → AI exchange is one turn)
2. **Keeps only the last `N` turns** (configurable via `WINDOW_SIZE`) in full detail
3. **Summarizes all older turns** into a compact text block using the LLM itself — preserving key decisions, file names, and code changes

No matter how long the conversation gets, the token count per query stays roughly constant.

### Why This Matters

- **Saves tokens on every query** after the first `WINDOW_SIZE` turns
- **Never loses context** — important history is compressed, not discarded
- **Scales indefinitely** — a 100-turn session costs nearly the same per query as a 10-turn session
- **No external memory store needed** — no vector DB, no embeddings, pure in-process




# Coding Agent



An AI-powered coding assistant that runs locally and helps you read, write, refactor, debug, and execute code inside your project. Built with LangGraph, LangChain, it uses a custom MCP (Model Context Protocol) server to give the AI real filesystem and shell access.

## How it works

The agent uses a two-component architecture:

- **`agent.py`** — The brain. Manages conversation history, sliding window summarization, and a LangGraph state machine that routes messages to the LLM with tools attached.
- **`server.py`** — The hands. A FastMCP server that exposes tools (read/write files, run shell commands, search code, git operations) which the LLM can call.

When you type a query, the agent sends it to LLM along with the available tools.  Gemini decides to calls the MCP server which executes the action on your actual filesystem.


## Setup


Note : Make sure you have installed uv python package 


1. Install dependencies:
   ```bash
   uv sync
   ```

2. Create a `.env` file with your API key:
   ```
   GOOGLE_API_KEY=your_key_here
   ```

3. Run the agent:
   ```bash
   uv run agent.py
   ```

4. MCP Configuration
   ```bash 
   servers = {
    "server": {
        "transport": "stdio",
        "command": "/opt/homebrew/bin/uv",
        "args": [
            "run",
            "fastmcp",
            "run",
            os.path.join(PROJECT_DIR, "server.py")
        ]
    }
   }
 ```

Find the path of uv by command "which uv" ( Mac ) or "where uv" ( windows ) and put it in "command" 


Developed by **Prateek Khandelwal**

