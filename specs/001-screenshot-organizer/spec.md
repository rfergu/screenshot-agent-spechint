# Screenshot Agent: Microsoft Agent Framework + MCP Demonstration

> 💡 **Rebuilding this from spec?**
> See [SPECHINT.md](../../SPECHINT.md) for dependencies, gotchas, and tips from the original build!

## Project Purpose

**PRIMARY GOAL:** Demonstrate Microsoft Agent Framework WITH embedded MCP Client Integration

This project shows how to build production AI agents using:
1. **Microsoft Agent Framework** - Conversational intelligence and agent orchestration
2. **Azure AI Foundry / Azure OpenAI** - Production-grade GPT-4o for reliable tool calling
3. **Model Context Protocol (MCP)** - Standardized tool interface for file operations

**The screenshot organization use case is SECONDARY** - it was chosen because it requires multiple tool calls and clearly demonstrates the "Brain (Agent Framework) vs Hands (MCP Server)" architectural pattern. Any multi-step file operation would serve the same demonstration purpose.

## Demo & Training Context

**This is a demonstration project**, not a production application. Key implications:

1. **Architecture Choices Are Pedagogical:**
   - Separate `src/agent/` and `src/screenshot_mcp/` directories make the integration pattern visually obvious
   - Individual tool files in `src/screenshot_mcp/tools/` show exactly what MCP provides
   - Code structure prioritizes clarity and teachability over optimization

2. **Documentation Is Extensive:**
   - This spec explains the "Brain vs Hands" pattern throughout (see "Dual-Mode Architecture" and FR-009)
   - User stories and functional requirements provide clear traceability
   - Implementation should include code comments explaining WHY architectural decisions were made, not just WHAT the code does

3. **Use Case (Screenshots) Is Secondary:**
   - Screenshot organization was chosen to demonstrate multi-step tool orchestration
   - The important part is HOW Agent Framework coordinates with MCP tools
   - Focus is on the integration pattern, not screenshot-specific features

4. **Emphasis on Honest Capability Communication:**
   - Local mode explicitly states its limitations (testing only, no tools)
   - Constitution documents the reality of small vs large model tradeoffs
   - Shows production AI agent development honestly, including what doesn't work

## Dual-Mode Architecture

**Remote Mode (Default)** - Production demonstration of Agent Framework + MCP
- Uses Azure GPT-4o with 7 MCP tools for full screenshot organization
- Demonstrates reliable tool calling and multi-step workflows
- Run: `python -m src.cli_interface`

**Local Mode** - Testing only
- Uses Phi-4-mini for basic chat without tools or file operations
- For testing Agent Framework setup and prompts locally without API costs
- Run: `python -m src.cli_interface --local`
- Note: Small models have unreliable tool calling, so local mode has no MCP tools

## Feature Description

A conversational AI agent that intelligently organizes screenshots through natural language interaction. Built with **Microsoft Agent Framework and MCP (Model Context Protocol)**, the agent proactively guides users through discovery, analysis, and organization phases with context-aware intelligence.

The agent adapts its behavior based on collection size, extracts meaningful content from screenshots, suggests smart categorization, offers multiple naming strategies, and provides real-time progress with intelligent summaries.

**Core demonstration:** How Agent Framework WITH embedded MCP Client Integration enables production AI agents to combine conversational intelligence with structured tool execution.

## User Stories

### US-001: Proactive Agent Introduction and Discovery ✅ IMPLEMENTED
**As a** user starting the agent
**I want** the agent to introduce itself and proactively guide me
**So that** I understand what it can do and how to begin

**Acceptance Criteria:**
- Agent introduces itself on startup with clear capabilities description ✅
- Proactively asks where user keeps screenshots ✅
- Uses MCP tools to list files in provided directory ✅
- Reports file count with contextual description (small/moderate/large collection) ✅
- Suggests appropriate approach based on collection size: ✅
  - Small (1-10): Offer manual review option
  - Moderate (11-30): Suggest preview sampling
  - Large (30+): Recommend direct batch processing
- Maintains conversational, helpful tone throughout ✅

**Implementation:** See `src/agent_client.py` lines 59-164 (REMOTE_SYSTEM_PROMPT)

### US-002: Intelligent Content Preview and Pattern Recognition
**As a** user with a moderate screenshot collection
**I want** the agent to preview samples and identify patterns
**So that** I understand what types of content I have before organizing

**Acceptance Criteria:**
- Agent samples 3-4 files with varied naming patterns
- Analyzes each using Azure GPT-4o Vision via MCP tools
- Identifies specific content types (invoices, errors, designs, etc.)
- Recognizes patterns across samples
- Suggests relevant categories based on actual content found
- Extracts meaningful details (dates, amounts, error types, company names)
- Asks user if suggested categories make sense
- Allows user to add or modify categories
- Shows [MCP Tool Call] annotations during analysis

### US-003: Flexible Naming Strategy Selection
**As a** user organizing screenshots
**I want** to choose how files should be named
**So that** they're organized in a way that works for my workflow

**Acceptance Criteria:**
- Agent offers three naming strategies:
  1. **Smart Naming**: Extracts meaningful content (e.g., "invoice_acme_corp_2024_11_1234usd.png")
  2. **Date-Based**: Uses dates and sequence (e.g., "2024-11-12_invoice_1.png")
  3. **Keep Originals**: Maintains original names, only organizes into folders
- Explains tradeoffs of each approach
- Confirms user's choice before proceeding
- Remembers choice throughout session

### US-004: Real-Time Progress with Contextual Updates
**As a** user during file organization
**I want** to see what's happening in real-time
**So that** I know the agent is working and understand its progress

**Acceptance Criteria:**
- Shows progress for each file: `original_name → identified_content → new_location`
- Displays MCP tool calls naturally: `[MCP Tool Call: move_screenshot(...)]`
- Provides progress summaries every 5-6 files
- Adapts verbosity based on collection size (less detail for large batches)
- Shows specific details extracted from content (amounts, dates, error types)
- Indicates when files are being created, analyzed, or moved

### US-005: Intelligent Summary with Content Understanding
**As a** user after organization completes
**I want** an intelligent summary that shows real understanding
**So that** I know the agent truly analyzed my content, not just moved files

**Acceptance Criteria:**
- Provides specific counts by category
- Shows extracted insights (e.g., "6 invoices totaling $8,422", "5 error screenshots: 3 Azure, 2 Python")
- Demonstrates understanding of actual content
- Asks if user wants help finding anything specific
- Maintains memory of what was organized

### US-006: Contextual Follow-Up Questions
**As a** user after organization
**I want** to ask follow-up questions about the content
**So that** I can find or understand specific files

**Acceptance Criteria:**
- Agent recalls specific files and their content
- Uses MCP tools to re-read files if needed for detailed questions
- Provides contextual analysis (e.g., "Those Azure errors were about connection timeouts to...")
- References earlier analysis without re-explaining everything
- Can locate specific files by description or content
- Maintains conversational context across questions

### US-007: Analyze Single Screenshot
**As a** user with many unorganized screenshots  
**I want to** analyze a single screenshot file  
**So that** I can understand its content and get it properly categorized  

**Acceptance Criteria:**
- System attempts OCR first for text extraction
- If OCR fails or yields insufficient text, falls back to Azure GPT-4o Vision
- Returns category (code/errors/documentation/design/communication/memes/other)
- Suggests descriptive filename based on content
- Reports processing method used and time taken
- Uses Azure GPT-4o Vision for reliable image understanding

### US-008: Batch Process Screenshots
**As a** user with a folder full of screenshots  
**I want to** process all screenshots in a folder at once  
**So that** I can organize my entire screenshot collection efficiently  

**Acceptance Criteria:**
- Processes all image files in specified folder
- Shows progress indicator during processing
- Reports count of files processed by each method (OCR vs Vision)
- Displays total processing time and per-file average
- Handles errors gracefully without stopping batch
- Creates organized folder structure if it doesn't exist

### US-009: Interactive Chat Interface
**As a** user
**I want to** interact with the tool through natural language
**So that** I can easily organize screenshots without memorizing commands

**Acceptance Criteria:**
- Supports dual-mode operation (see "Dual-Mode Architecture" section above for details)
- Remote mode (production): Microsoft Agent Framework with full tool access and GPT-4o
- Local mode (testing only): Basic chat for testing conversation flow (see architecture section for limitations)
- Natural language understanding for user requests (remote mode)
- Provides helpful suggestions and clarifications
- Explains what the tool is doing at each step
- Offers to process individual files or batches (remote mode only)
- Remembers context within a session
- Clear error messages when operations fail
- Displays current mode indicator to prevent confusion

### US-010: Organize and Rename Files
**As a** user  
**I want to** have my screenshots automatically moved and renamed  
**So that** I can easily find them later  

**Acceptance Criteria:**
- Creates organized folder structure (code/errors/documentation/design/communication/memes/other)
- Moves files to appropriate category folders
- Renames files with descriptive names based on content
- Handles filename conflicts (appends numbers if needed)
- Preserves original file metadata
- Optionally keeps originals in an archive folder

### US-011: View Processing Metrics
**As a** user  
**I want to** see performance metrics for processing  
**So that** I understand how the tool is performing  

**Acceptance Criteria:**
- Shows processing method (OCR or Azure Vision) for each file
- Displays processing time per file
- Shows batch statistics (files per method, total time)
- Reports when Azure GPT-4o Vision is used (cloud API call)
- Clear indication of OCR vs cloud vision processing for cost awareness

## Functional Requirements

### FR-001: OCR Processing
- Must use Tesseract OCR as first processing attempt
- Extract text from screenshots
- Count words to determine if sufficient for classification
- Process text-heavy screenshots in <50ms

### FR-002: Vision Model Processing
- Use Azure GPT-4o Vision for visual analysis (remote mode)
- Triggered when OCR fails or yields insufficient text
- Process visual content via Azure OpenAI API
- Images encoded as base64 and sent to GPT-4o vision endpoint
- Returns structured JSON with category, description, and suggested filename

### FR-003: Classification Logic
- Implement keyword-based classification for OCR results
- Categories: code, errors, documentation, design, communication, memes, other
- Classification rules:
  - code: Contains "def", "function", "import", "class", "const", "var"
  - errors: Contains "error", "exception", "failed", "traceback"
  - documentation: Contains "install", "usage", "api", "guide", "readme"
  - communication: Contains "sent", "replied", "message", "@"
  - other: Default if no patterns match

### FR-004: MCP Tool Implementation (Remote Mode Only)
- Implement 5 essential file operation tools via MCP server:
  1. `list_screenshots(directory, recursive, max_files)` - List screenshot files
  2. `analyze_screenshot(file_path, force_vision)` - Extract text/description using OCR or Azure GPT-4o Vision
  3. `get_categories()` - Get available categories with descriptions
  4. `create_category_folder(category, base_dir)` - Create category folder
  5. `move_screenshot(source_path, dest_folder, new_filename, keep_original)` - Move/copy/rename file
- MCP server runs as subprocess, accessed via stdio transport
- MCP client embedded inside Agent Framework (MCPClientWrapper)
- **Critical**: MCP server subprocess must receive parent's environment variables for Azure credentials
- **Critical**: Tool wrapper functions must be async (Agent Framework calls them from async context)
- **Critical**: Path normalization needed to handle shell-escaped spaces in file paths
- Agent Framework (GPT-4o) makes intelligent decisions (categories, filenames, orchestration)
- Tools return facts; Agent provides intelligence
- Local mode has NO tools (see "Dual-Mode Architecture" section for details)

### FR-005: File Management
- Create organized folder structure
- Support common image formats (PNG, JPG, JPEG, GIF, BMP)
- Generate descriptive filenames (no spaces, filesystem-safe)
- Handle duplicate filenames gracefully

### FR-006: Azure AI Integration (Foundry or Azure OpenAI)
- Use **Microsoft Agent Framework** for AI orchestration
- Support **either** Azure AI Foundry serverless endpoints **or** Azure OpenAI resource endpoints
- Automatically detect endpoint type via `AzureOpenAIChatClient`:
  - AI Foundry: `https://xxx.services.ai.azure.com/api/projects/xxx`
  - Azure OpenAI: `https://xxx.cognitiveservices.azure.com`
- Use Azure models (GPT-4, GPT-4o, etc.) for natural language understanding
- Agent Framework automatically orchestrates tool calls based on user intent
- Provide conversational responses via `ChatAgent`
- Maintain session context using `AgentThread` serialization
- Authentication support:
  - Azure OpenAI: API key required
  - AI Foundry: API key or Azure CLI authentication (DefaultAzureCredential)

### FR-007: Error Handling
- Gracefully handle OCR failures
- Provide fallback when vision model fails
- Report clear error messages to users
- Continue batch processing despite individual failures

### FR-008: Performance Monitoring
- Track processing time per file
- Count files processed by each method
- Calculate and display aggregate statistics
- Show cost savings from local processing

### FR-009: Microsoft Agent Framework WITH MCP Client Integration
- Use `agent-framework` Python package (>=0.1.0)
- **Unified Architecture**: Agent Framework WITH embedded MCP Client
  - `MCPClientWrapper` embedded inside `AgentClient`
  - MCP client starts MCP server subprocess on initialization
  - Tools accessed via MCP protocol (stdio transport)
  - All file system operations mediated through MCP
- Create `ChatAgent` with tools provided by MCP client wrapper (remote mode only)
- Use `AzureOpenAIChatClient` for dual endpoint support (Foundry + Azure OpenAI)
- Implement async conversation pattern with `await agent.run()`
- Use `AgentThread` for conversation state management:
  - `agent.get_new_thread()` for new conversations
  - `await thread.serialize()` for session persistence
  - `await agent.deserialize_thread()` for session restoration
- Lifecycle management:
  - `await agent_client.async_init()` starts MCP client (remote mode)
  - `await agent_client.cleanup()` stops MCP server subprocess
- Agent Framework handles:
  - Intelligent decision making (GPT-4)
  - Tool call orchestration
  - Message formatting and API calls
  - Thread state management
- MCP Server provides:
  - Low-level file operation tools
  - Facts and data (not decisions)
  - Standardized tool interface via MCP protocol

### FR-010: Conversational Agent Intelligence and Proactive Guidance ✅ IMPLEMENTED
- **Proactive Introduction**: Agent introduces itself on startup with clear capability description ✅
- **Guided Discovery**: Automatically asks for screenshot directory location ✅
- **Adaptive Behavior**: Adjusts strategy based on file count (small/moderate/large) ✅
- **Context-Aware Suggestions**: Recommends preview sampling, manual review, or batch processing based on collection size ✅
- **Natural Language Flow**: Maintains conversational tone throughout all interactions ✅
- **User Confirmation**: Always confirms plans before executing operations ✅

**Implementation:** See `src/agent_client.py` lines 59-164 (REMOTE_SYSTEM_PROMPT) and `src/cli_interface.py` lines 152-169 (proactive introduction trigger)

### FR-011: Intelligent Content Analysis and Pattern Recognition ✅ IMPLEMENTED
- **Strategic Sampling**: For moderate collections (11-30 files), sample 3-4 files with varied naming patterns ✅
- **Visual Analysis**: Use Azure GPT-4o Vision to analyze sampled screenshots via MCP tools ✅
- **Content Extraction**: Extract specific details: ✅
  - Invoice: company name, amount, date
  - Error: error type, service name, message excerpt
  - Design: design type, tool used
  - Documentation: topic, format
- **Pattern Recognition**: Identify common content types across samples ✅
- **Category Suggestions**: Suggest categories based on actual content found (not generic defaults) ✅
- **User Validation**: Ask user if suggested categories make sense and allow modifications ✅
- **MCP Tool Visibility**: Show [MCP Tool Call] annotations during analysis ✅

**Implementation:** See `src/agent_client.py` lines 59-164 (REMOTE_SYSTEM_PROMPT, Phase 3: Preview & Pattern Recognition)

### FR-012: Flexible Naming Strategies ✅ IMPLEMENTED
- **Three Strategy Options**: ✅
  1. **Smart Naming**: Extract meaningful content from screenshots
     - Example: `invoice_acme_corp_2024_11_1234usd.png`
     - Example: `error_azure_connection_timeout.png`
     - Example: `design_mobile_checkout_flow.png`
  2. **Date-Based Naming**: Use date + category + sequence
     - Example: `2024-11-12_invoice_1.png`
     - Example: `2024-11-12_error_1.png`
  3. **Keep Originals**: Maintain original names, organize into category folders only
- **Strategy Explanation**: Explain tradeoffs of each approach ✅
- **User Choice**: Allow user to select preferred strategy ✅
- **Persistent Memory**: Remember chosen strategy throughout session ✅

**Implementation:** See `src/agent_client.py` lines 59-164 (REMOTE_SYSTEM_PROMPT, Phase 4: Choose Naming Strategy)

### FR-013: Real-Time Progress and Contextual Updates ✅ IMPLEMENTED
- **Per-File Updates**: Show format `original_name → identified_content → new_location/name` ✅
- **MCP Tool Annotations**: Display tool calls: `[MCP Tool Call: create_category_folder(...)]` ✅
- **Progress Summaries**: Provide summary every 5-6 files processed ✅
- **Adaptive Verbosity**: Less detail for large batches, more detail for small collections ✅
- **Content-Specific Details**: Show extracted information (amounts, dates, error types) ✅
- **Operation Indicators**: Clearly indicate when creating folders, analyzing files, moving files ✅

**Implementation:** See `src/agent_client.py` lines 59-164 (REMOTE_SYSTEM_PROMPT, Phase 5: Execute Organization)

### FR-014: Intelligent Summary with Content Understanding ✅ IMPLEMENTED
- **Category Counts**: Report specific counts for each category ✅
- **Extracted Insights**: Show aggregate understanding: ✅
  - Invoices: "6 invoices totaling $8,422"
  - Errors: "5 error screenshots: 3 Azure, 2 Python"
  - Designs: "4 mobile UI designs"
- **Content Demonstration**: Prove agent understood content, not just moved files ✅
- **Proactive Help**: Ask if user wants help finding anything specific ✅
- **Context Maintenance**: Maintain memory of organized files for follow-up questions ✅

**Implementation:** See `src/agent_client.py` lines 59-164 (REMOTE_SYSTEM_PROMPT, Phase 6: Intelligent Summary)

### FR-015: Contextual Memory and Follow-Up Questions ✅ IMPLEMENTED
- **Content Recall**: Remember specific files and their analyzed content ✅
- **Intelligent Re-Analysis**: Use MCP tools to re-read files for detailed follow-up questions ✅
- **Contextual Responses**: Provide specific answers (e.g., "The Azure errors were connection timeouts to storage account...") ✅
- **Location Assistance**: Help locate files by description or content ✅
- **Multi-Turn Context**: Maintain conversation state across multiple questions ✅
- **Efficient References**: Don't re-explain everything, reference prior analysis ✅

**Implementation:** See `src/agent_client.py` lines 59-164 (REMOTE_SYSTEM_PROMPT, Phase 7: Contextual Follow-Up) and `src/session_manager.py` (thread persistence)

### FR-016: Dual-Mode Support - Testing vs Production Architecture ✅ IMPLEMENTED

**Overview:** See "Dual-Mode Architecture" section at the top of this spec for the complete description of local vs remote modes.

**Key Implementation Details:**
- **Mode Selection**: CLI flag `--local` for local mode (testing), no flag = remote mode (production default)
- **Mode-Specific Configuration**:
  - Same `AgentClient` wrapper for both modes
  - Different tool lists: Empty list for local, full MCP tools for remote
  - Different system prompts: Testing guidance for local, full 7-phase workflow for remote
  - Configuration via `config/config.yaml`
  - CLI shows mode indicator to prevent user confusion
- **Technical Requirements**:
  - Local: AI Foundry CLI, Phi-4-mini model, inference server running
  - Remote: Azure OpenAI/AI Foundry credentials, internet connectivity
- **Architecture Reality Demonstrated**:
  - Local small models (Phi-4-mini) do NOT reliably support function calling
  - Remote large models (GPT-4o) have robust tool calling support
  - This shows production AI agent development reality: use appropriate models for each task

**Implementation:**
- CLI interface: `src/cli_interface.py` lines 234-238 (`--local` flag)
- Local mode system prompt: `src/agent_client.py` lines 167-197 (LOCAL_SYSTEM_PROMPT)
- Remote mode system prompt: `src/agent_client.py` lines 59-164 (REMOTE_SYSTEM_PROMPT)
- Mode-specific agent initialization: `src/agent_client.py` lines 76-150
- Local chat client: `src/local_foundry_chat_client.py`

## Non-Functional Requirements

### NFR-001: Performance
- OCR processing: <100ms per image (local tesseract)
- Azure GPT-4o Vision processing: <3s per image (includes API latency)
- Batch processing: Linear scaling with file count
- Network-dependent for vision fallback (requires internet connectivity)

### NFR-002: Privacy
- Image data sent to Azure OpenAI GPT-4o Vision when OCR fails (remote mode)
- Images encoded as base64 and transmitted over HTTPS
- OCR processing happens locally when possible (tesseract)
- Local mode does not process images (see "Dual-Mode Architecture" section)
- Azure data processing complies with Microsoft's data handling policies

### NFR-003: Usability
- Clear command-line interface
- Helpful error messages
- Progress indicators for long operations
- Natural language interaction

### NFR-004: Reliability
- 90%+ successful categorization rate
- Graceful degradation on failures
- Robust error handling
- Consistent file organization

## Review & Acceptance Checklist
- [x] All user stories have clear acceptance criteria
- [x] Functional requirements are testable
- [x] Non-functional requirements are measurable
- [x] Privacy requirements are explicitly stated
- [x] Performance targets are defined
- [x] Error handling is comprehensive
- [x] File organization structure is clear
- [x] Integration points are well-defined
- [x] Microsoft Agent Framework integration specified
- [x] Tool function architecture defined
- [x] Dual Azure endpoint support documented
- [x] Hybrid local/remote architecture documented (FR-016)
- [x] Mode selection with `--local` flag implemented (FR-016)
- [x] Proactive conversational UX implemented (US-001, FR-010 through FR-015)
- [x] Setup instructions documented in README.md for both modes
- [x] Demo comparison utility specified

## Open Questions & Clarifications

### Clarified Items
1. **Q: Should we keep original files after organizing?**
   A: Optional - user can choose to archive originals

2. **Q: How to handle non-image files in batch processing?**
   A: Skip with warning message, continue processing

3. **Q: How to handle very large images?**
   A: Resize for processing while maintaining aspect ratio

5. **Q: Why use MCP architecture (Agent Framework WITH MCP Client)?**
   A: This unified architecture demonstrates modern AI agent development:
   - **Separation of Concerns**: Brain (Agent Framework/GPT-4o) vs Hands (MCP Server)
   - **Protocol Standardization**: All file operations through standardized MCP protocol
   - **Clear Boundaries**: Tools return facts, Agent makes decisions
   - **Educational Value**: Shows how to integrate MCP with Agent Framework
   - **Production Pattern**: Demonstrates protocol-mediated tool access
   - **Flexibility**: MCP tools can be reused by other MCP clients
   - **Demonstrations**: Shows both Agent Framework capabilities AND MCP protocol integration
   - Agent provides intelligence (categorization, naming), MCP provides operations (file access)
   - **Key Implementation Lessons**:
     - Tool wrappers must be async (Agent Framework calls from async context)
     - MCP subprocess needs parent environment variables for Azure credentials
     - Path normalization required for shell-escaped file paths
     - OCR failures should gracefully fall back to vision processing

6. **Q: Why support both local and remote modes?**
   A: Demonstrates the reality of production AI agent development: local small models are good for testing conversation flow but unreliable for tool calling. Remote large models (GPT-4) provide production-ready capabilities. This shows developers the tradeoffs between cost/privacy (local testing) vs reliability/capability (remote production).

7. **Q: Why doesn't local mode have tools?**
   A: Local mode is for testing Agent Framework conversation flow only, not for actual screenshot organization. Small models like Phi-4-mini have unreliable function calling (ignores tools, hallucinates responses, garbled JSON). See "Dual-Mode Architecture" section for complete explanation of local vs remote mode purposes.

8. **Q: Why use Azure GPT-4o Vision instead of local vision models?**
   A: This project demonstrates PRODUCTION AI agent patterns, which require reliable tool calling and robust vision capabilities - both available in GPT-4o but not in small local models. Local vision models had reliability issues during prototyping. Azure GPT-4o Vision provides production-ready image understanding while Tesseract OCR handles text-heavy screenshots locally for performance.

### Pending Clarifications
- Exact format for renamed files (timestamp prefix?)
- Maximum batch size limitations
- Configuration file format and location
- Logging verbosity levels and output destination

## Implementation Lessons Learned

### Key Technical Discoveries

1. **MCP Subprocess Environment Variables**
   - **Issue**: MCP server subprocess had `env=None`, causing Azure credentials to be unavailable
   - **Solution**: Pass `env=dict(os.environ)` to subprocess so it inherits parent environment
   - **Impact**: Critical for cloud service integration (Azure OpenAI credentials)

2. **Async Tool Wrapper Requirements**
   - **Issue**: Tool wrappers were synchronous, but Agent Framework calls them from async context
   - **Symptom**: `RuntimeWarning: coroutine 'call_tool_async' was never awaited`
   - **Solution**: Make all tool wrapper functions `async def` and directly `await` MCP calls
   - **Root Cause**: Agent Framework's `agent.run()` is async and calls tools from running event loop

3. **Path Normalization for Shell Escaping**
   - **Issue**: File paths with spaces received as `path\ with\ spaces` (shell-escaped)
   - **Solution**: Add `.replace('\\ ', ' ')` normalization in all path-handling tools
   - **Impact**: Prevents "file not found" errors when users paste shell-escaped paths

4. **OCR Fallback to Vision Processing**
   - **Issue**: Tesseract has Unicode decoding bugs with certain images
   - **Solution**: Catch OCR exceptions in `mcp_tools.py` and automatically fall back to Azure Vision
   - **Pattern**: Try local processing first, gracefully degrade to cloud when needed

5. **Vision Model Selection: Production-Ready over Local**
   - **Decision**: Use Azure GPT-4o Vision for robust, production-ready image understanding
   - **Rationale**: Demonstrates production AI agent patterns requiring reliable tool calling
   - **Architecture**: Local OCR (tesseract) for speed, Azure Vision for accuracy

6. **Agent Framework Tool Integration**
   - **Discovery**: Tools are registered once at agent initialization, called automatically
   - **Pattern**: Agent Framework handles tool orchestration, no manual tool routing needed
   - **Benefit**: GPT-4o intelligently decides when and how to use tools

### Architecture Validation

The final working architecture validates the MCP integration approach:
- **Brain (Agent Framework + GPT-4o)**: Intelligent orchestration, decision-making
- **Hands (MCP Server)**: Low-level file operations, fact gathering
- **Bridge (MCP Client Wrapper)**: Async tool wrappers, environment propagation
- **Vision (Azure GPT-4o)**: Reliable image understanding when OCR insufficient

This demonstrates that production AI agents require:
- Robust cloud models for tool calling (local small models unreliable)
- Proper async patterns throughout the stack
- Graceful fallback strategies for local processing
- Environment configuration passed to all subprocess components
