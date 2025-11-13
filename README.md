# Screenshot Agent - SpecKit Rebuild Package

Minimum files to build an AI-powered screenshot organizer demonstrating Microsoft Agent Framework WITH MCP (Model Context Protocol) integration.

## What's in This Package

This repository contains **everything you need to build the Screenshot Agent from specifications** - no code included:

```
.
├── .specify/memory/
│   └── constitution.md          # Non-negotiable principles (v2.1.0)
├── specs/001-screenshot-organizer/
│   ├── spec.md                  # Complete functional specification
│   ├── plan.md                  # Implementation plan
│   ├── tasks.md                 # Detailed task breakdown
│   ├── data-model.md            # Data structures
│   ├── research.md              # Technology choices & rationale
│   ├── quickstart.md            # Quick start guide
│   └── contracts/
│       └── mcp-api-spec.json    # MCP tool contracts
├── HANDOFF.md                   # 11-step rebuild guide
├── TRACEABILITY.md              # Requirements → code mapping
├── MAINTAINERS.md               # Governance & decision authority
├── SPECHINT.md                  # Tips & gotchas from original build
└── README.md                    # This file
```

## What You'll Build

An AI agent demonstrating production patterns:
- **Microsoft Agent Framework** for conversational orchestration
- **Model Context Protocol (MCP)** for standardized tool integration
- **OCR-first processing** with GPT-4o Vision fallback
- **7-phase conversational UX**
- Unified architecture (Agent WITH embedded MCP, not separate)

**Use Case:** Screenshot organization (but the architecture is the real demonstration)

## How to Build

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

5. **specs/001-screenshot-organizer/tasks.md** - Task breakdown:
   - Detailed implementation tasks
   - Acceptance criteria

### Step 3: Build From Spec

6. **Follow HANDOFF.md** - 11-step rebuild process:
   - Prerequisites
   - Step-by-step implementation
   - Validation checkpoints

7. **Validate with TRACEABILITY.md**:
   - Verify each requirement is implemented
   - Check spec → code mappings

## Quick Start

```bash
# 1. Read these in order:
cat SPECHINT.md                                    # Tips first!
cat .specify/memory/constitution.md                # Constraints
cat specs/001-screenshot-organizer/spec.md         # Requirements

# 2. Follow the rebuild process:
cat HANDOFF.md                                     # 11-step guide

# 3. Validate your work:
cat TRACEABILITY.md                                # Check mappings
```

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

- ✅ All user stories (US-001 through US-009) work
- ✅ Constitution principles satisfied
- ✅ TRACEABILITY.md requirements map to your code
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

The screenshot organization use case is SECONDARY - chosen because it requires multi-step tool orchestration and clearly demonstrates the "Brain vs Hands" pattern.

## Files Explained

| File | Purpose | When to Read |
|------|---------|--------------|
| **SPECHINT.md** | Practical tips & gotchas | Read FIRST - saves hours |
| **constitution.md** | Non-negotiable constraints | Before planning |
| **spec.md** | What to build | For requirements |
| **plan.md** | How to structure | Before coding |
| **tasks.md** | Task breakdown | During implementation |
| **HANDOFF.md** | Step-by-step rebuild | When building |
| **TRACEABILITY.md** | Requirements → code map | For validation |

## License

MIT License - See LICENSE file

## Built With SpecKit

This project demonstrates the **SpecKit workflow**:
- Constitution → Spec → Plan → Tasks → Implementation
- Traceability mapping throughout
- Rebuild-ready from specifications alone

Can you rebuild it? 🚀
