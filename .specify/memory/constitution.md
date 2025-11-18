# Project Constitution

Constitution Version: 2.1.0

Ratification Date: 2025-11-11

Last Amended: 2025-11-12

## Article I: Core Principles
1. **Production-Ready Development**: Demonstrate the reality of AI agent development - use appropriate models for the task (small models for testing, large models for production)
2. **Honest Capability Communication**: Clearly distinguish between testing mode (limited capabilities) and production mode (full features)
3. **Privacy Options**: Provide options for privacy-conscious users (local Vision fallback) while maintaining production reliability
4. **Performance Transparency**: Always report which processing method was used and its performance metrics

## Article II: Technical Standards
1. **Python Best Practices**: Follow PEP 8, use type hints, document all functions
2. **Error Handling**: All operations must fail gracefully with clear error messages
3. **Modular Architecture**: Clean separation between MCP server, vision processing, and chat interface
4. **Protocol Compliance**: Strict adherence to MCP (Model Context Protocol) specifications

## Article III: Code Quality Requirements
1. **Test Coverage**: Minimum 80% test coverage for core functionality
2. **Documentation**: All modules, classes, and public functions must have docstrings
3. **Configuration**: All configurable parameters must be externalized to environment variables or config files
4. **Logging**: Comprehensive logging at appropriate levels (DEBUG, INFO, WARNING, ERROR)

## Article IV: User Experience
1. **Response Time**: Text-heavy screenshots must process in <50ms via OCR
2. **Batch Processing**: Support efficient batch processing with progress indicators
3. **Clear Feedback**: Always inform users about processing method, time, and results
4. **Intuitive Interface**: Terminal interface should be simple and conversational

## Article V: Development Workflow
1. **Incremental Development**: Build and test each component independently
2. **Test-Driven Development**: Write tests before implementation where applicable
3. **Version Control**: Commit frequently with clear, descriptive messages
4. **Dependency Management**: Use requirements.txt with pinned versions

## Article VI: Security & Privacy
1. **Privacy Options**: Remote mode can use local Phi-3 Vision MLX as fallback for image processing
2. **Image Processing Transparency**: Clearly document when images are sent to external services (GPT-4 Vision)
3. **Secure Configuration**: API keys stored in environment variables, never hardcoded
4. **Data Retention**: Process and organize files without creating unnecessary copies
5. **Local Testing Mode**: Local mode does not process images at all (testing only)

## Article VII: Performance Optimization
1. **Tiered Processing**: OCR first (fast), then local vision model (slower) only when needed
2. **Efficient Categorization**: Use keyword-based classification for OCR results
3. **Batch Optimization**: Process multiple files efficiently with parallel processing where safe
4. **Resource Management**: Properly manage memory and file handles

## Article VIII: Extensibility
1. **Plugin Architecture**: Design to allow easy addition of new categorization rules
2. **Configurable Categories**: Allow users to customize categories and classification rules
3. **Model Flexibility**: Support swapping vision models without major refactoring
4. **MCP Tool Extensibility**: Easy addition of new MCP tools

## Article IX: Maintenance
1. **Clear Logging**: Comprehensive logging for debugging and monitoring
2. **Error Recovery**: Graceful degradation when components fail
3. **Update Path**: Clear documentation for updating dependencies and models
4. **Backward Compatibility**: Maintain compatibility with existing organized file structures

## Article X: Required Technology Stack

These are non-negotiable technology requirements. Deviations MUST NOT be made without amending this constitution.

### 🔴 CRITICAL (MUST Use - No Exceptions)

1. **Programming Language**: Python 3.11 or higher
   - Rationale: Type hints, performance improvements, async features
   - Exception process: N/A - This is foundational

2. **AI Agent Framework**: Microsoft Agent Framework (agent-framework package)
   - Rationale: Demonstrates Agent Framework WITH MCP Client Integration architecture
   - Exception process: Requires constitutional amendment (this is the project's core purpose)

3. **Tool Protocol**: Model Context Protocol (MCP) via mcp package
   - Rationale: Standardized tool interface, demonstrates MCP integration pattern
   - Exception process: Requires constitutional amendment (this is the project's core purpose)

4. **Remote AI Provider**: Azure OpenAI Service or Azure AI Foundry
   - Both services work via Agent Framework's AzureOpenAIChatClient
   - Azure OpenAI Service: `https://*.openai.azure.com` endpoints
   - Azure AI Foundry: `https://*.services.ai.azure.com` endpoints
   - **Use Agent Framework client, NOT OpenAI package directly**
   - Rationale: Production-grade reliability for tool calling and GPT-4 capabilities
   - Exception process: May substitute equivalent Azure service with same API contract

### 🟡 IMPORTANT (SHOULD Use - Exceptions Require Justification)

5. **OCR Engine**: Tesseract OCR
   - Rationale: Fast, free, widely available
   - Exception: May substitute if alternative provides <50ms processing for text screenshots

6. **Local Testing Model**: Azure AI Foundry CLI with Phi-4-mini
   - Rationale: Free local testing, zero API costs
   - Exception: May substitute equivalent local model service

7. **Vision Fallback**: Phi-3 Vision MLX (macOS only)
   - Rationale: Privacy option for image analysis
   - Exception: Platform-specific, may be unavailable on non-macOS

### 🟢 RECOMMENDED (MAY Substitute)

8. **Configuration Format**: YAML via config files
9. **CLI Framework**: Click
10. **Terminal Output**: Rich library for formatting

### Technology Constraints

- **No JavaScript/TypeScript**: This is a Python demonstration project
- **No Alternative AI Frameworks**: LangChain, LlamaIndex, etc. would obscure Agent Framework patterns
- **No Custom MCP Protocol**: Must use official MCP SDK, not reimplementations

### Dependency Versions

Critical dependencies must be pinned in requirements.txt:
- `agent-framework>=1.0.0b251111`
- `mcp>=0.9.0`
- Python version specified in `.python-version` or documentation