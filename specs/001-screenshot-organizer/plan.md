# Screenshot Organizer - Technical Implementation Plan

## Overview
This plan details the technical implementation of the Screenshot Organizer with Local AI, following a modular architecture with MCP protocol integration, local-first processing, and Azure AI Foundry orchestration.

## Architecture Design

### System Architecture
```
┌─────────────────────────────────────────────────────┐
│                   User Terminal                      │
└────────────────────┬─────────────────────────────────┘
                     │
┌────────────────────▼─────────────────────────────────┐
│              Chat Client (chat_client.py)            │
│  - Natural language interface                        │
│  - Azure AI Foundry orchestration                   │
│  - MCP protocol client                              │
└────────────────────┬─────────────────────────────────┘
                     │ MCP Protocol
┌────────────────────▼─────────────────────────────────┐
│         MCP Server (screenshot_mcp_server.py)        │
│  ┌─────────────────────────────────────────────┐    │
│  │ Tools:                                      │    │
│  │ - analyze_screenshot()                      │    │
│  │ - batch_process()                          │    │
│  │ - organize_file()                          │    │
│  └──────────────┬──────────────────────────────┘    │
└─────────────────┼────────────────────────────────────┘
                  │
┌─────────────────▼────────────────────────────────────┐
│          Processing Layer                            │
│  ┌─────────────────┐    ┌─────────────────────┐    │
│  │  OCR Module     │    │  Vision Module      │    │
│  │  (Tesseract)    │    │  (Azure GPT-4o Vision)    │    │
│  └─────────────────┘    └─────────────────────┘    │
└───────────────────────────────────────────────────────┘
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
```python
# requirements.txt
azure-ai-inference>=1.0.0b9  # Azure AI Foundry SDK
azure-identity>=1.15.0        # Azure authentication
mcp>=1.0.0                    # Model Context Protocol
pytesseract==0.3.10           # OCR processing
Pillow>=10.2.0                # Image handling
azure-ai-inference>=0.0.3rc1    # Local vision model
python-dotenv>=1.0.0          # Environment management
rich>=13.7.0                  # Terminal UI formatting
click>=8.1.7                  # CLI framework
pydantic>=2.7.2               # Data validation
PyYAML>=6.0                   # Configuration parsing
pytest>=7.4.0                 # Testing framework
```

### Azure AI Foundry Configuration
```bash
# Environment variables
AZURE_AI_CHAT_ENDPOINT      # Your project endpoint URL
AZURE_AI_CHAT_KEY           # API key (or use az login)
AZURE_AI_MODEL_DEPLOYMENT   # Model deployment name (e.g., gpt-4, gpt-4o)
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

### 1. MCP Server (`screenshot_mcp_server.py`)

```python
from mcp import Server, Tool, Response
from typing import Dict, Any, List

class ScreenshotMCPServer:
    """MCP server for screenshot processing tools"""
    
    def __init__(self):
        self.server = Server()
        self.ocr_processor = OCRProcessor()
        self.vision_processor = VisionProcessor()
        self.classifier = KeywordClassifier()
        self.organizer = FileOrganizer()
        
    def register_tools(self):
        self.server.register_tool(
            Tool(
                name="analyze_screenshot",
                description="Analyze a screenshot using OCR or vision model",
                parameters={
                    "path": {"type": "string", "required": True},
                    "force_vision": {"type": "boolean", "default": False}
                },
                handler=self.analyze_screenshot
            )
        )
        # Additional tool registrations...
        
    async def analyze_screenshot(self, path: str, force_vision: bool = False) -> Dict[str, Any]:
        """Main screenshot analysis logic"""
        # Implementation follows tiered approach
```

### 2. OCR Processor (`processors/ocr_processor.py`)

```python
import pytesseract
from PIL import Image
import time
from typing import Optional, Dict

class OCRProcessor:
    """Handles OCR processing using Tesseract"""
    
    def __init__(self, min_words_threshold: int = 10):
        self.min_words_threshold = min_words_threshold
        
    def process(self, image_path: str) -> Dict[str, Any]:
        """
        Extract text from image using OCR
        Returns: {
            "text": extracted_text,
            "word_count": count,
            "processing_time": time_ms,
            "sufficient_text": bool
        }
        """
```

### 3. Vision Processor (`processors/vision_processor.py`)

```python
from azure_vision import AzureVisionProcessor
from PIL import Image
import json
from typing import Dict, Any

class VisionProcessor:
    """Handles vision model processing using Azure GPT-4o Vision"""
    
    def __init__(self):
        self.model = None
        self.prompt_template = """
        Analyze this screenshot and determine:
        1. Category: code, error, documentation, design, communication, meme, or other
        2. Main content description
        3. Suggested filename (descriptive, no spaces)
        Return as JSON: {"category": "", "description": "", "filename": ""}
        """
        
    def ensure_model_loaded(self):
        """Lazy load model on first use"""
        if self.model is None:
            self.model = AzureVisionProcessor()
            
    def process(self, image_path: str) -> Dict[str, Any]:
        """Process image using vision model"""
```

### 4. Keyword Classifier (`classifiers/keyword_classifier.py`)

```python
import re
from typing import Dict, List

class KeywordClassifier:
    """Rule-based classification using keyword patterns"""
    
    def __init__(self):
        self.patterns = {
            "code": ["def", "function", "import", "class", "const", "var", "return", "if", "for"],
            "errors": ["error", "exception", "failed", "traceback", "warning", "fatal"],
            "documentation": ["install", "usage", "api", "guide", "readme", "tutorial", "docs"],
            "communication": ["sent", "replied", "message", "@", "from:", "to:", "subject:"],
            "design": ["figma", "sketch", "wireframe", "mockup", "ui", "ux", "design"],
            "memes": ["meme", "funny", "lol", "😂", "🤣"]
        }
        
    def classify(self, text: str) -> str:
        """Classify text based on keyword patterns"""
```

### 5. Chat Client (`chat_client.py`)

```python
import os
from azure.ai.inference import ChatCompletionsClient
from azure.core.credentials import AzureKeyCredential
from azure.identity import DefaultAzureCredential
from rich.console import Console
from rich.prompt import Prompt

class ChatClient:
    """Azure AI Foundry orchestrated chat interface"""

    def __init__(self, endpoint: str = None, credential: str = None):
        # Get endpoint
        endpoint = endpoint or os.environ.get("AZURE_AI_CHAT_ENDPOINT")
        api_key = credential or os.environ.get("AZURE_AI_CHAT_KEY")

        # Initialize Azure client with API key or CLI auth
        if api_key:
            self.client = ChatCompletionsClient(
                endpoint=endpoint,
                credential=AzureKeyCredential(api_key)
            )
        else:
            # Fall back to Azure CLI authentication
            self.client = ChatCompletionsClient(
                endpoint=endpoint,
                credential=DefaultAzureCredential()
            )

        self.model = os.environ.get("AZURE_AI_MODEL_DEPLOYMENT", "gpt-4")
        self.console = Console()
        self.system_prompt = """
        You are a helpful assistant that organizes screenshots.
        You have access to MCP tools for analyzing and organizing screenshots.
        Always try OCR first for efficiency, only use vision when needed.
        Available tools:
        - analyze_screenshot(path, force_vision=False)
        - batch_process(folder)
        - organize_file(source, category, new_name)
        """

    def chat_loop(self):
        """Main chat interaction loop"""
```

## Implementation Details

### Configuration Management
```yaml
# config/default_config.yaml
processing:
  ocr_min_words: 10
  vision_timeout: 30
  batch_size: 50
  
organization:
  base_folder: "~/Screenshots/organized"
  categories:
    - code
    - errors
    - documentation
    - design
    - communication
    - memes
    - other
  keep_originals: true
  
models:
  vision:
    max_tokens: 200
    temperature: 0.3
  
api:
  azure_model_deployment: "gpt-4"  # Or gpt-4o, gpt-4-turbo, etc.
  max_context_length: 8000
```

### Error Handling Strategy
1. **OCR Failures**: Log warning, proceed to vision model
2. **Vision Model Failures**: Return "other" category with generic name
3. **File I/O Errors**: Skip file, log error, continue batch
4. **API Failures**: Implement exponential backoff with retry
5. **MCP Protocol Errors**: Graceful degradation to direct function calls

### Performance Optimizations
1. **Lazy Loading**: Load Azure GPT-4o Vision model only when needed
2. **Image Preprocessing**: Resize large images before processing
3. **Caching**: Cache classification results for duplicate images
4. **Batch Processing**: Process multiple OCR requests in parallel
5. **Memory Management**: Clear image buffers after processing

### Testing Strategy

#### Unit Tests
- OCR text extraction accuracy
- Keyword classification rules
- File organization logic
- MCP tool registration and execution

#### Integration Tests
- End-to-end screenshot processing
- Batch processing workflow
- Error handling scenarios
- Azure AI Foundry orchestration

#### Performance Tests
- OCR processing time (<50ms target)
- Vision processing time (<2s target)
- Batch processing scalability
- Memory usage under load

### Deployment Considerations

#### First-Time Setup
1. Install Tesseract OCR system dependency
2. Download Azure GPT-4o Vision model (~8GB)
3. Configure API keys in .env file
4. Create organized folder structure

#### Environment Variables
```bash
# .env.example or ~/.zshrc (recommended)
AZURE_AI_CHAT_ENDPOINT=https://your-project.services.ai.azure.com/api/projects/your-id
AZURE_AI_CHAT_KEY=your-api-key  # Or use 'az login' for CLI auth
AZURE_AI_MODEL_DEPLOYMENT=gpt-4
MCP_SERVER_PORT=8080
TESSERACT_PATH=/usr/local/bin/tesseract
VISION_MODEL_PATH=~/.cache/vision
LOG_LEVEL=INFO
```

### Security Considerations
1. **API Key Management**: Use environment variables, never hardcode
2. **File Access**: Validate paths, prevent directory traversal
3. **Input Validation**: Sanitize filenames and paths
4. **Process Isolation**: Run vision model in separate process
5. **Rate Limiting**: Implement rate limits for API calls

### Monitoring & Logging
```python
# Logging configuration
import logging

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('screenshot_organizer.log'),
        logging.StreamHandler()
    ]
)
```

### Future Enhancements
1. Web UI using FastAPI/Gradio
2. Custom model fine-tuning for better classification
3. Support for additional file types (PDFs, documents)
4. Cloud storage integration (S3, Google Drive)
5. Scheduled batch processing
6. Custom classification rules via configuration

## Success Metrics
- **OCR Processing**: <50ms for text-heavy images
- **Vision Processing**: <2s for visual content
- **Classification Accuracy**: >90% correct categorization
- **Batch Performance**: Linear scaling up to 1000 files
- **User Satisfaction**: Natural conversation flow with Azure AI Foundry models
- **Cost Savings**: $0.00 per image (all local processing)