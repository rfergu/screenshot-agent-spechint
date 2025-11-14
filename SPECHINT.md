# 💡 Spec Hints - Tips from the Trenches

Rebuilding this from the spec? Here are some things I learned the hard way so you don't have to.

## 🎯 Dependencies That Actually Work

**Python Packages (Direct Imports Only):**
```bash
# THE BIG ONE - agent-framework uses beta versioning!
agent-framework>=1.0.0b251111   # NOT 0.1.0! It'll fail with "version not found"

# MCP and Processing
mcp>=1.0.0
pytesseract==0.3.10
Pillow>=10.2.0

# Utilities
python-dotenv>=1.0.0
pydantic>=2.7.2
rich>=13.7.0
click>=8.1.7
```

**Note:** Agent Framework manages Azure packages (`openai`, `azure-ai-inference`, `azure-identity`) as dependencies. If you get import errors, install them explicitly - the beta version may not declare them properly.

**System Stuff:**
- Python 3.11+ (tested on 3.11.5)
- Tesseract OCR (Homebrew: `brew install tesseract`)
- Azure AI Foundry CLI for local mode (optional, testing only)

## ⚡ Performance Tricks I Discovered

**OCR-First Strategy:**
- Try Tesseract OCR first (~50ms) before hitting GPT-4o Vision (~2s)
- OCR handles most text-heavy screenshots just fine
- Only fall back to Vision for images/memes/low-text stuff
- This made processing **40x faster** for code screenshots!

**Batch Processing:**
- Process screenshots in batches for large collections
- Show progress every 5-6 files to keep user engaged
- Be less verbose for 30+ files (they don't want a novel)

**Logging Levels:**
- Use `DEBUG` for operational logs (tool calls, processing steps)
- Use `INFO` only for high-level progress
- Otherwise your console gets flooded!

## 🐛 Gotchas & Fixes

### 1. MCP Subprocess Can't Find Module
**Problem:** `ModuleNotFoundError: No module named 'src'`

**Fix:** In `src/screenshot_mcp/client_wrapper.py`, the project root path needs **three** `.parent` calls:
```python
# WRONG - only goes up to src/
project_root = Path(__file__).parent.parent

# RIGHT - goes to actual project root
project_root = Path(__file__).parent.parent.parent
```
Path goes: `client_wrapper.py → screenshot_mcp → src → project_root`

### 2. Local Mode ≠ Production Mode
**Remember:**
- **Local mode** = Testing Agent Framework conversation flow ONLY (no tools, no file ops)
- **Remote mode** = The actual demo (GPT-4o + 7 MCP tools)
- Don't expect local mode to organize screenshots - it can't!

### 3. Agent Framework Tool Integration
The Agent Framework automatically handles tool calling. Just:
1. Create async functions with proper type hints
2. Pass them to the agent via `tools=` parameter
3. Agent Framework does the rest!

**Don't** try to manually invoke tools or parse responses - let the framework do it.

### 4. Processing Method Transparency
Users want to know if OCR or Vision was used. Tell the agent in the system prompt:
```python
# In prompts.py
"ALWAYS indicate processing method after analyze_screenshot:
 - '✅ Analyzed via local OCR' or '🔍 Analyzed via cloud Vision'"
```

## 🏗️ Architecture Insights

**Unified Pattern (not separate components):**
```
Agent Framework (Brain)
  ↓ embeds
MCP Client Wrapper
  ↓ manages subprocess
MCP Server (stdio)
  ↓ operates on
File System
```

**Key Principle:** The Agent Framework *contains* the MCP client. It's not "Agent + MCP as separate services" - it's "Agent WITH embedded MCP client".

**Why this matters:** All file operations go through MCP protocol as a demonstration of the unified architecture pattern.

## 🎨 System Prompt Tips

**7-Phase Conversational Flow:**
The agent guides users through:
1. Introduction & directory discovery
2. Analysis (inspect screenshots)
3. Category proposal (suggest based on content)
4. Naming strategy (ask user preference)
5. Execution (do the work)
6. Intelligent summary (prove understanding)
7. Follow-up (remember everything)

**Make it conversational, not robotic.** The agent should feel like a helpful coworker, not a command-line tool.

## 📊 Testing Tips

**Quick Smoke Tests:**
```bash
# Test local mode (conversation only)
python -m src.cli_interface --local

# Test remote mode (full demo)
export AZURE_AI_CHAT_ENDPOINT="..."
export AZURE_AI_CHAT_KEY="..."
export AZURE_AI_MODEL_DEPLOYMENT="gpt-4o"
python -m src.cli_interface
```

**What to test:**
- ✅ MCP server subprocess starts correctly
- ✅ OCR processes text screenshots fast
- ✅ Vision kicks in for images/low-text screenshots
- ✅ Tool results show processing method (OCR vs Vision)
- ✅ Agent makes intelligent categorization decisions

## 🎓 Lessons Learned

1. **Start with OCR, not Vision** - It's 40x faster and handles 80% of screenshots
2. **Log levels matter** - DEBUG for operational, INFO for progress, not the other way around
3. **Local mode is for testing framework integration** - Not for actual screenshot organization
4. **The unified architecture is the point** - Agent Framework WITH MCP, not separate
5. **Users want transparency** - Show them when local vs cloud processing happens

## 🤝 Final Thoughts

This project is a **demo of production AI agent patterns**, not a screenshot app. The screenshot organization use case is just a vehicle to demonstrate:
- Microsoft Agent Framework orchestration
- Model Context Protocol (MCP) integration
- OCR-first processing with Vision fallback
- Conversational UX with GPT-4o

Keep that context in mind when rebuilding. The choices we made (like no tools in local mode) make sense when you remember the goal is demonstrating reliable production patterns.

Good luck! You got this. 🚀

---

## 🚨 Critical Lessons from First Implementation Attempt

### Lesson 1: You MUST Use Agent Framework (Not OpenAI SDK Directly)

**❌ WRONG - What the first implementation did:**
```python
from openai import AzureOpenAI

client = AzureOpenAI(azure_endpoint=endpoint, api_key=api_key, ...)
response = client.chat.completions.create(model="gpt-4o", messages=..., tools=...)
```

**✅ CORRECT - What you should do:**
```python
from agent_framework import ChatAgent, AzureOpenAIChatClient

# Use Agent Framework's Azure client
azure_client = AzureOpenAIChatClient(
    endpoint=endpoint,
    api_key=api_key,
    model="gpt-4o"
)

# Create agent with MCP tools
agent = ChatAgent(
    name="Screenshot Organizer",
    model=azure_client,
    tools=mcp_tools,  # From MCPClientWrapper
    instructions=system_prompt
)

# Let Agent Framework orchestrate everything
thread = agent.get_new_thread()
response = await agent.run(thread=thread, input=user_message)
```

**Why this matters:**
- Constitution Article X requires Agent Framework (non-negotiable)
- Agent Framework handles tool calling orchestration automatically
- Agent Framework manages conversation state and thread persistence
- This is the architectural pattern being demonstrated

---

### Lesson 2: Azure Service Disambiguation

**There are TWO different Azure AI services. Check your endpoint URL:**

| Endpoint Contains | Service | Agent Framework Usage |
|-------------------|---------|----------------------|
| `openai.azure.com` | Azure OpenAI Service | `AzureOpenAIChatClient` |
| `services.ai.azure.com` | Azure AI Foundry | `AzureOpenAIChatClient` |

**Both work with Agent Framework the same way** - `AzureOpenAIChatClient` handles both.

**Environment Variables:**
```bash
# Azure OpenAI Service
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com
AZURE_OPENAI_KEY=your-key
AZURE_OPENAI_DEPLOYMENT=gpt-4o

# OR Azure AI Foundry
AZURE_AI_CHAT_ENDPOINT=https://your-project.services.ai.azure.com
AZURE_AI_CHAT_KEY=your-key
AZURE_AI_MODEL_DEPLOYMENT=gpt-4o
```

**Common Mistake:** Using `azure-ai-inference` package directly. Don't - Agent Framework's `AzureOpenAIChatClient` abstracts this.

---

### Lesson 3: Python Import Strategy

**Problem:** `ImportError: attempted relative import beyond top-level package`

**Solution:** Add to top of each module:
```python
import sys
from pathlib import Path

# Add project root to path
project_root = Path(__file__).parent.parent  # Adjust as needed
sys.path.insert(0, str(project_root))

# Now use absolute imports
from src.utils.config import load_config
```

**Why:** Allows running scripts directly (`python src/chat_client.py`) without package installation.

---

### Lesson 4: Agent Framework Handles Tool Calling

**Don't manually parse tool calls.** Agent Framework does it automatically.

**❌ WRONG:**
```python
if response.choices[0].message.tool_calls:
    for tool_call in response.choices[0].message.tool_calls:
        # Manual execution...
```

**✅ CORRECT:**
```python
# Agent Framework handles tool calling automatically in agent.run()
response = await agent.run(thread=thread, input=user_message)
```

The agent will call MCP tools when needed, format responses, and manage conversation flow.