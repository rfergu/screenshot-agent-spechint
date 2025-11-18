# Screenshot Organizer - Technical Implementation Plan

## Overview
This plan details the technical implementation of the Screenshot Organizer with Local AI, following a modular architecture with MCP protocol integration, local-first processing, and Azure AI Foundry orchestration.

## Architecture Design

### System Architecture
```
┌─────────────────────────────────────────────────────┐
│                   User Terminal                     │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│              Chat Client (chat_client.py)           │
│  - Natural language interface                       │
│  - Azure AI Foundry orchestration                   │
│  - MCP protocol client                              │
└────────────────────┬────────────────────────────────┘
                     │ MCP Protocol
┌────────────────────▼────────────────────────────────┐
│         MCP Server (screenshot_mcp_server.py)       │
│  ┌─────────────────────────────────────────────┐    │
│  │ Tools:                                      │    │
│  │ - analyze_screenshot()                      │    │
│  │ - batch_process()                           │    │
│  │ - organize_file()                           │    │
│  └──────────────┬──────────────────────────────┘    │
└─────────────────┼───────────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────────┐
│          Processing Layer                           │
│  ┌─────────────────┐    ┌─────────────────────┐     │
│  │  OCR Module     │    │  Vision Module      │     │
│  │  (Tesseract)    │    │  (Azure GPT-4o Vision)    │
│  └─────────────────┘    └─────────────────────┘     │
└─────────────────────────────────────────────────────┘
```

### Data Flow
1. User input → Chat Client
2. Chat Client → Azure AI Foundry API (intent analysis)
3. Azure AI Foundry → MCP tool selection
4. MCP Server executes tool
5. Tool processes image (OCR → Vision if needed)
6. Results returned through MCP → Azure AI Foundry → User

## Technology Stack

### Core Dependencies

Only list packages you directly import. Agent Framework manages Azure integration as transitive dependencies.

```python
# requirements.txt

# Core Framework (handles Azure OpenAI/AI Foundry integration)
agent-framework>=1.0.0b251111

# MCP Protocol
mcp>=1.0.0

# Screenshot Processing
pytesseract==0.3.10
Pillow>=10.2.0

# Configuration & Utilities
python-dotenv>=1.0.0
pydantic>=2.7.2
PyYAML>=6.0

# CLI & Terminal UI
rich>=13.7.0
click>=8.1.7

# Testing
pytest>=7.4.0
```

**Note:** Agent Framework (v1.0.0b251111) is in beta. It manages Azure dependencies (`openai`, `azure-ai-inference`) internally. If you encounter missing Azure-related imports, the framework may not have properly declared all dependencies - install them explicitly as needed.

### Azure Configuration (OpenAI Service or AI Foundry)
```bash
# Environment variables (Azure OpenAI Service)
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com
AZURE_OPENAI_KEY=your-api-key
AZURE_OPENAI_DEPLOYMENT=gpt-4o

# OR (Azure AI Foundry)
AZURE_AI_CHAT_ENDPOINT=https://your-project.services.ai.azure.com
AZURE_AI_CHAT_KEY=your-api-key
AZURE_AI_MODEL_DEPLOYMENT=gpt-4o

# Agent Framework's AzureOpenAIChatClient works with both
```

### Directory Structure
```
screenshot-organizer/
├── src/
│   ├── __init__.py
│   ├── screenshot_mcp_server.py   # MCP server implementation
│   ├── chat_client.py              # Azure AI Foundry orchestrated interface
│   ├── processors/
│   │   ├── __init__.py
│   │   ├── ocr_processor.py       # Tesseract OCR wrapper
│   │   └── vision_processor.py    # Azure GPT-4o Vision wrapper
│   ├── classifiers/
│   │   ├── __init__.py
│   │   └── keyword_classifier.py  # Rule-based classification
│   ├── organizers/
│   │   ├── __init__.py
│   │   └── file_organizer.py      # File management
│   └── utils/
│       ├── __init__.py
│       ├── config.py               # Configuration management
│       └── logger.py               # Logging setup
├── tests/
│   ├── test_ocr_processor.py
│   ├── test_vision_processor.py
│   ├── test_classifier.py
│   └── test_mcp_server.py
├── config/
│   └── default_config.yaml
├── .env.example
├── requirements.txt
├── setup.py
└── README.md
```

## Component Specifications

The implementation consists of the following components:

### 1. MCP Server (`screenshot_mcp_server.py`)
MCP server providing screenshot processing tools

### 2. OCR Processor (`processors/ocr_processor.py`)
Handles OCR processing using Tesseract

### 3. Vision Processor (`processors/vision_processor.py`)
Handles vision model processing using Azure GPT-4o Vision

### 4. Keyword Classifier (`classifiers/keyword_classifier.py`)
Rule-based classification using keyword patterns

### 5. Agent Client (`agent_client.py`)
Microsoft Agent Framework wrapper with embedded MCP client
