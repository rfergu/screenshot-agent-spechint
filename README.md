# Screenshot Agent - SpecKit Rebuild Package

A spec to hopefully build an AI-powered screenshot organizer demonstrating Microsoft Agent Framework WITH MCP (Model Context Protocol) integration.

## What's in This Package

This repository contains a description of a Screenshot Agent from specifications.

```
.
├── .specify/memory/
│   └── constitution.md          # Non-negotiable principles (v2.1.0)
├── specs/001-screenshot-organizer/
│   ├── spec.md                  # Complete functional specification
│   ├── plan.md                  # Implementation plan
│   ├── quickstart.md            # Quick start guide
│   └── contracts/
│       └── mcp-api-spec.json    # MCP tool contracts
├── AZURE_SETUP.md               # Azure AI Foundry setup guide (get $200 free credits!)
├── SPECHINT.md                  # Tips & gotchas from original build
└── README.md                    # This file
```

## What You'll Build

An AI agent demonstrating production patterns:
- **Microsoft Agent Framework** for conversational orchestration
- **Model Context Protocol (MCP)** for standardized tool integration
- **OCR-first processing** with GPT-4o Vision fallback
- **Conversational UX**
- Unified architecture (Agent WITH embedded MCP, not separate)

**Use Case:** Screenshot organization (but the architecture is the real demonstration)

## How to Build

### Step 0: Set Up Azure AI Foundry (Required)

Before you start, you need Azure AI credentials to run the agent:

**📘 [AZURE_SETUP.md](./AZURE_SETUP.md)** - Complete setup guide:
- Get $200 in free Azure credits
- Create Azure AI Foundry project
- Deploy GPT-4o model
- Get your environment variables:
  ```bash
  export AZURE_AI_CHAT_ENDPOINT="https://your-project.services.ai.azure.com/api/projects/your-project"
  export AZURE_AI_MODEL_DEPLOYMENT="gpt-4o"
  export AZURE_AI_CHAT_KEY="your_api_key"
  ```

**⏱️ Time needed:** 15-20 minutes for first-time setup

### Step 1: Read the Foundation Documents

Start here, in this order:

1. **SPECHINT.md** - Read this FIRST! Tips that will save you hours:
   - Dependencies that actually work
   - Performance tricks (OCR-first = 40x faster!)
   - Gotchas & fixes
   - Architecture insights

2. **.specify/memory/constitution.md** - Non-negotiable constraints:
   - Required technology stack (Article X)
   - Core principles for rebuild

3. **specs/001-screenshot-organizer/spec.md** - What to build:
   - Project purpose (PRIMARY GOAL: Agent Framework + MCP demo)
   - User stories
   - Functional requirements

### Step 2: Plan Your Implementation

4. **specs/001-screenshot-organizer/plan.md** - How to structure it:
   - Architecture patterns
   - Component breakdown
   - Integration approach

5. **specs/001-screenshot-organizer/quickstart.md** - Getting started:
   - Setup instructions
   - Environment configuration
   - Testing procedures


## Key Technologies

From **constitution.md Article X** - Required stack:

- **Language:** Python 3.11+
- **AI Framework:** Microsoft Agent Framework (`agent-framework` package)
- **Tool Protocol:** Model Context Protocol (`mcp` package)
- **AI Provider:** Azure OpenAI or Azure AI Foundry
- **OCR:** Tesseract
- **Vision:** GPT-4o (via Azure)

## Architecture Pattern

From **spec.md** - The unified pattern:

```
Agent Framework (Brain 🧠)
    ↓ embeds
MCP Client Wrapper
    ↓ manages subprocess
MCP Server (Hands 🤲)
    ↓ operates on
File System
```

**Key Principle:** Agent Framework CONTAINS the MCP client. Not "Agent + MCP as separate services" but "Agent WITH embedded MCP client".

## Success Criteria

You've successfully rebuilt when:

- ✅ All user stories (US-001 through US-011) work
- ✅ Constitution principles satisfied
- ✅ All functional requirements (FR-001 through FR-016) implemented
- ✅ Agent demonstrates Framework + MCP integration
- ✅ OCR-first processing works with Vision fallback
- ✅ Conversational UX follows 7-phase flow

## SPECHINT.md Highlights

Before you start, know these gotchas:

**Dependencies:**
- agent-framework uses `1.0.0b*` versions, NOT `0.1.0`!

**Performance:**
- OCR-first strategy = 40x faster for text screenshots
- Try Tesseract OCR (~50ms) before GPT-4o Vision (~2s)

**Architecture:**
- MCP subprocess needs `.parent.parent.parent` to find project root
- Local mode = testing only (no tools)
- Remote mode = full demo (7 MCP tools)

**See SPECHINT.md for complete tips!**

## Project Purpose

From **spec.md**:

> **PRIMARY GOAL:** Demonstrate Microsoft Agent Framework WITH embedded MCP Client Integration

The screenshot organization is a demo!
It was chosen because it requires multi-step tool orchestration and clearly demonstrates the "Brain vs Hands" pattern.

## Files Explained

| File | Purpose | When to Read |
|------|---------|--------------|
| **AZURE_SETUP.md** | Azure AI Foundry setup & free credits | BEFORE starting - required credentials |
| **SPECHINT.md** | Practical tips & gotchas | Read FIRST - saves hours |
| **constitution.md** | Non-negotiable constraints | Before planning |
| **spec.md** | What to build | For requirements |
| **plan.md** | How to structure | Before coding |
| **quickstart.md** | Installation & setup | When ready to implement |

This is free and unencumbered "software" released into the public domain.

The software is provided "as is", without warranty of any kind.
For more information, please refer to <https://unlicense.org/>


## Built With SpecKit

This project demonstrates the **SpecKit workflow**:
- Constitution → Spec → Plan → Implementation
- All requirements traceable through user stories and functional requirements
- Rebuild-ready from specifications alone

Can you rebuild it? 🚀
