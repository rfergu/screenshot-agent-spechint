# Screenshot Organizer - Task Breakdown

## Overview
This document breaks down the implementation into executable tasks organized by user story, with clear dependencies and parallel execution markers.

## Phase 1: Core Infrastructure Setup

### Task 1.1: Project Structure [P]
**File:** `setup.py`, `requirements.txt`
**Description:** Initialize project structure and dependencies
**Actions:**
1. Create directory structure
2. Set up requirements.txt with all dependencies
3. Create setup.py for package installation
4. Initialize git repository
5. Create .env.example file

### Task 1.2: Configuration Module [P]
**File:** `src/utils/config.py`
**Description:** Implement configuration management
**Actions:**
1. Create config loader for YAML files
2. Implement environment variable override
3. Add validation for required settings
4. Create default configuration
**Tests:** `tests/test_config.py`

### Task 1.3: Logging Setup [P]
**File:** `src/utils/logger.py`
**Description:** Set up logging infrastructure
**Actions:**
1. Configure logging levels
2. Set up file and console handlers
3. Create logger factory function
4. Add performance logging decorators
**Tests:** `tests/test_logger.py`

## Phase 2: Image Processing Components

### Task 2.1: OCR Processor
**File:** `src/processors/ocr_processor.py`
**Description:** Implement Tesseract OCR wrapper
**Dependencies:** Task 1.2, Task 1.3
**Actions:**
1. Create OCRProcessor class
2. Implement text extraction method
3. Add word counting logic
4. Handle OCR errors gracefully
5. Add performance timing
**Tests:** `tests/test_ocr_processor.py`

### Task 2.2: Vision Processor
**File:** `src/processors/vision_processor.py`
**Description:** Implement Azure GPT-4o Vision integration
**Dependencies:** Task 1.2, Task 1.3
**Actions:**
1. Create VisionProcessor class
2. Implement lazy model loading
3. Add prompt template configuration
4. Parse JSON responses
5. Handle model failures
**Tests:** `tests/test_vision_processor.py`

### Task 2.3: Keyword Classifier
**File:** `src/classifiers/keyword_classifier.py`
**Description:** Build rule-based classification
**Dependencies:** Task 1.2
**Actions:**
1. Define keyword patterns for each category
2. Implement classification logic
3. Add confidence scoring
4. Support custom patterns from config
**Tests:** `tests/test_classifier.py`

## Phase 3: File Organization

### Task 3.1: File Organizer
**File:** `src/organizers/file_organizer.py`
**Description:** Implement file management system
**Dependencies:** Task 1.2, Task 1.3
**Actions:**
1. Create FileOrganizer class
2. Implement folder structure creation
3. Add file moving with conflict handling
4. Generate safe filenames
5. Optional original file archiving
**Tests:** `tests/test_file_organizer.py`

### Task 3.2: Batch Processing Support
**File:** `src/organizers/batch_processor.py`
**Description:** Add batch file processing
**Dependencies:** Task 3.1, Task 2.1, Task 2.2
**Actions:**
1. Implement folder scanning
2. Add progress tracking
3. Handle individual file failures
4. Collect and report statistics
**Tests:** `tests/test_batch_processor.py`

## Phase 4: MCP Server Implementation

### Task 4.1: MCP Server Base
**File:** `src/screenshot_mcp_server.py`
**Description:** Create MCP server with tool registration
**Dependencies:** Task 2.1, Task 2.2, Task 2.3, Task 3.1
**Actions:**
1. Initialize MCP server
2. Define tool schemas
3. Implement analyze_screenshot tool
4. Implement batch_process tool
5. Implement organize_file tool
**Tests:** `tests/test_mcp_server.py`

### Task 4.2: Tool Handlers
**File:** `src/mcp_tools.py`
**Description:** Implement MCP tool logic
**Dependencies:** Task 4.1
**Actions:**
1. Create analyze_screenshot handler
2. Implement tiered processing logic
3. Add force_vision parameter support
4. Return structured responses
**Tests:** `tests/test_mcp_tools.py`

### Task 4.3: MCP Protocol Integration
**File:** `src/mcp_protocol.py`
**Description:** Handle MCP protocol communication
**Dependencies:** Task 4.1
**Actions:**
1. Implement request parsing
2. Add response formatting
3. Handle protocol errors
4. Add connection management
**Tests:** `tests/test_mcp_protocol.py`

## Phase 5: Chat Interface

### Task 5.1: Azure AI Foundry Integration
**File:** `src/chat_client.py`
**Description:** Implement Azure AI Foundry orchestration
**Dependencies:** Task 4.1
**Actions:**
1. Set up Azure AI Inference client
2. Implement both API key and Azure CLI authentication
3. Create system prompt with tool definitions
4. Implement conversation management with Azure message types
5. Add MCP tool calling logic compatible with Azure format
**Tests:** `tests/test_chat_client.py`

### Task 5.2: Interactive CLI
**File:** `src/cli_interface.py`
**Description:** Build terminal interface
**Dependencies:** Task 5.1
**Actions:**
1. Create command-line parser
2. Implement chat loop
3. Add rich formatting
4. Handle user interrupts
**Tests:** `tests/test_cli.py`

### Task 5.3: Session Management
**File:** `src/session_manager.py`
**Description:** Manage conversation context
**Dependencies:** Task 5.1
**Actions:**
1. Implement session storage
2. Add context windowing
3. Handle session persistence
4. Clean up old sessions
**Tests:** `tests/test_session.py`

## Phase 6: Integration & Testing

### Task 6.1: End-to-End Integration [P]
**File:** `tests/test_integration.py`
**Description:** Integration testing
**Dependencies:** All previous tasks
**Actions:**
1. Test complete OCR workflow
2. Test vision model workflow
3. Test batch processing
4. Test error scenarios

### Task 6.2: Performance Testing [P]
**File:** `tests/test_performance.py`
**Description:** Validate performance targets
**Dependencies:** All previous tasks
**Actions:**
1. Test OCR <50ms target
2. Test vision <2s target
3. Test batch scaling
4. Memory usage profiling

### Task 6.3: Documentation [P]
**File:** `README.md`, `docs/`
**Description:** Create user documentation
**Dependencies:** All previous tasks
**Actions:**
1. Write installation guide
2. Create usage examples
3. Document configuration
4. Add troubleshooting guide

## Phase 7: Deployment & Polish

### Task 7.1: Docker Container
**File:** `Dockerfile`, `docker-compose.yml`
**Description:** Containerize application
**Dependencies:** Task 6.1
**Actions:**
1. Create Dockerfile
2. Set up docker-compose
3. Configure volumes for models
4. Add health checks

### Task 7.2: Model Management
**File:** `src/utils/model_manager.py`
**Description:** Handle model downloading/caching
**Dependencies:** Task 2.2
**Actions:**
1. Check for model presence
2. Download progress indication
3. Model version management
4. Cache management

### Task 7.3: CLI Entry Point
**File:** `src/__main__.py`
**Description:** Create main entry point
**Dependencies:** All tasks
**Actions:**
1. Parse command-line arguments
2. Initialize components
3. Start MCP server
4. Launch chat interface

## Execution Order Summary

```
Phase 1 (Parallel): Tasks 1.1, 1.2, 1.3
    ↓
Phase 2 (Sequential): Task 2.1 → Task 2.2 → Task 2.3
    ↓
Phase 3 (Sequential): Task 3.1 → Task 3.2
    ↓
Phase 4 (Sequential): Task 4.1 → Task 4.2 → Task 4.3
    ↓
Phase 5 (Sequential): Task 5.1 → Task 5.2 → Task 5.3
    ↓
Phase 6 (Parallel): Tasks 6.1, 6.2, 6.3
    ↓
Phase 7 (Sequential): Task 7.1 → Task 7.2 → Task 7.3
```

## Task Markers
- **[P]** - Can be executed in parallel with other [P] tasks in the same phase
- **Dependencies** - Must wait for listed tasks to complete
- **Tests** - Associated test files to create

## Validation Checkpoints

### Checkpoint 1: After Phase 2
- OCR successfully extracts text from test image
- Vision model loads and processes test image
- Classifier correctly categorizes sample texts

### Checkpoint 2: After Phase 4
- MCP server starts and registers tools
- Tools respond to test requests
- Proper error handling for invalid inputs

### Checkpoint 3: After Phase 5
- Azure AI Foundry models successfully call MCP tools
- Chat interface responds to natural language
- Session context maintained between messages

### Checkpoint 4: Final
- End-to-end screenshot processing works
- Performance targets met
- All tests passing

## Estimated Time
- Phase 1: 2 hours
- Phase 2: 4 hours
- Phase 3: 2 hours
- Phase 4: 3 hours
- Phase 5: 3 hours
- Phase 6: 2 hours
- Phase 7: 2 hours
- **Total: ~18 hours**

## Critical Path
The critical path for MVP functionality:
1. Task 1.2 (Config) → Task 2.1 (OCR) → Task 4.1 (MCP Server) → Task 5.1 (Azure AI Foundry) → Task 5.2 (CLI)

This allows basic single-file processing with OCR and chat interface.