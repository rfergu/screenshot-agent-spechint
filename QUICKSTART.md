# Quick Start: Rebuild with SpecKit

Get this screenshot agent running in ~15 minutes using AI-assisted development with SpecKit.

## What is SpecKit?

[SpecKit](https://github.com/github/spec-kit) is GitHub's official toolkit for specification-driven development. This project includes detailed specs that are comprehensive enough for an AI assistant to rebuild the entire application. You'll use the `/speckit.implement` command in your AI code assistant to trigger the rebuild.

## Prerequisites

Before you start, you'll need:

1. **Azure AI Foundry Account** - [Complete setup guide](AZURE_SETUP.md)
   - Sign up for Azure ($200 free credits available)
   - Deploy GPT-4o model
   - Get your Azure OpenAI credentials

2. **AI Code Assistant** - One of:
   - GitHub Copilot in VSCode (recommended)
   - Claude Code
   - Cursor
   - Any AI assistant that supports slash commands

3. **Development Environment**
   - Python 3.11 or higher
   - uv package manager
   - Node.js 18+ (for MCP server)
   - Git

4. **GitHub SpecKit** - Install the SpecKit CLI:
   ```bash
   uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
   ```
   Verify installation: `specify check`

## Step-by-Step Guide

### 1. Set Up Azure AI Foundry

Follow the [AZURE_SETUP.md](AZURE_SETUP.md) guide to configure your Azure environment. This is **required** - the agent needs GPT-4o to function.

### 2. Open the Project in Your Editor

```bash
cd screenshot-agent-spechint
code .  # or open in your preferred editor
```

### 3. Run the SpecKit Implementation Command

In your AI assistant's chat interface, run:

```
/speckit.implement
```

This command tells your AI assistant to:
- Read the specifications in `specs/001-screenshot-organizer/`
- Read the constitution in `.specify/memory/constitution.md`
- Implement the screenshot agent following these specifications
- Set up all dependencies, configuration, and file structure

### 4. Iterate and Fix Issues

The specifications are detailed enough that most AI assistants will get very close on the first try, but you may need to iterate:

- **Follow AI prompts** - The assistant may ask clarifying questions
- **Run suggested commands** - Install dependencies, configure environment variables
- **Fix errors as they appear** - The AI will help debug
- **Test the implementation** - Run the agent and verify it works

Depending on your AI model and editor, it might work on the first shot, or you might need 2-3 iterations. Either way, you'll be close.

### 5. Verify It Works

Once the AI completes the implementation:

```bash
# Install dependencies
uv pip install -e .

# Set your Azure credentials in .env
# (See AZURE_SETUP.md for getting these values)
AZURE_OPENAI_ENDPOINT=your-endpoint
AZURE_OPENAI_API_KEY=your-key
AZURE_OPENAI_DEPLOYMENT=your-deployment

# Run the agent
python -m screenshot_agent --help
```

## What Gets Built

The AI will build a complete screenshot organization agent with:

- **Agent Framework** - Conversational AI orchestration using Microsoft Agent Framework
- **MCP Integration** - 7 tools for file operations via Model Context Protocol
- **OCR Processing** - Fast local Tesseract OCR with GPT-4o Vision fallback
- **Smart Organization** - AI-powered categorization and filing

## Pro Tips

⚠️ **Read [SPECHINT.md](SPECHINT.md) first** - It contains critical gotchas and lessons learned:
- The correct `agent-framework` version is `>=1.0.0b251111` (beta versioning!)
- OCR-first strategy is 40x faster than Vision-only
- Common dependency and configuration issues

## Troubleshooting

**Command not recognized?**
- Make sure SpecKit is installed: `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git`
- Verify installation: `specify check`
- Make sure your AI assistant supports SpecKit slash commands (Claude Code, GitHub Copilot, Cursor, etc.)
- Try phrasing it naturally: "Please implement this project following the SpecKit methodology in the specs folder"

**Build fails?**
- Check [SPECHINT.md](SPECHINT.md) for known issues
- Verify Azure credentials are set correctly
- Ensure you're using Python 3.11+

**Agent runs but doesn't process screenshots?**
- You must use remote mode (default) - local mode has no tools
- Verify Azure OpenAI connection
- Check that Tesseract OCR is installed

## Next Steps

- Read [specs/001-screenshot-organizer/spec.md](specs/001-screenshot-organizer/spec.md) to understand the full requirements
- Review [SPECHINT.md](SPECHINT.md) for implementation insights
- Check the [README.md](README.md) for architecture details

## The Point of This Exercise

This project demonstrates that with detailed specifications, an AI assistant can rebuild a production-ready agent system. The screenshot organizer is just the use case - the real lesson is the **specification-driven development workflow** and the **"Brain vs Hands" architecture pattern** (Agent Framework + MCP).
