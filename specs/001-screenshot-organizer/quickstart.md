# Screenshot Organizer - Quick Start Guide

## Prerequisites

Before you begin, ensure you have the following installed:

1. **Python 3.10+**
   ```bash
   python --version  # Should be 3.10 or higher
   ```

2. **Tesseract OCR**
   - macOS: `brew install tesseract`
   - Ubuntu/Debian: `sudo apt-get install tesseract-ocr`
   - Windows: Download from [GitHub releases](https://github.com/UB-Mannheim/tesseract/wiki)

3. **Git** (for version control)

## Installation

### 1. Clone the Repository
```bash
git clone <repository-url>
cd screenshot-organizer
```

### 2. Create Virtual Environment
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Set Up Azure AI Foundry Credentials

**Option A: Environment Variables (Recommended)**

Add to your shell profile (`~/.zshrc` or `~/.bashrc`):
```bash
# Azure AI Foundry Configuration
export AZURE_AI_CHAT_ENDPOINT="https://your-project.services.ai.azure.com/api/projects/your-id"
export AZURE_AI_CHAT_KEY="your-api-key"
export AZURE_AI_MODEL_DEPLOYMENT="gpt-4"

# Optional settings
export MCP_SERVER_PORT=8080
export LOG_LEVEL=INFO
```

Then reload: `source ~/.zshrc`

**Option B: .env File**
```bash
cp .env.example .env
# Edit .env and add your Azure credentials
```

**Getting Azure Credentials:**
1. Go to https://ai.azure.com
2. Sign in and create or select a project
3. Navigate to Settings → Project connection string (for endpoint)
4. Navigate to Keys and Endpoint (for API key)
5. Note your model deployment name from Deployments section

See `.env.example` for detailed setup instructions.

### 5. Download Azure GPT-4o Vision Model (Optional)
The model will auto-download on first use (~8GB), but you can pre-download:
```bash
python -c "from azure_vision import AzureVisionProcessor; model = AzureVisionProcessor()"
```

## Quick Test

### Test OCR Processing
```bash
python -m src.processors.ocr_processor test_image.png
```

### Test Vision Model
```bash
python -m src.processors.vision_processor test_image.png
```

## Running the Application

### Start MCP Server
```bash
python -m src.screenshot_mcp_server
```

### Launch Chat Interface (in another terminal)
```bash
python -m src.chat_client
```

## Usage Examples

### Interactive Chat Mode
```
$ python -m src.chat_client

Welcome to Screenshot Organizer! How can I help you?

You: Can you analyze the screenshot on my desktop?
Assistant: I'll analyze the screenshot for you. Let me check what's there...
[Analyzing /Users/you/Desktop/screenshot.png...]
This appears to be a code screenshot showing a Python function. I'll categorize it as "code" and suggest the filename "python_data_processing_function.png".

You: Great! Can you organize all screenshots in my Downloads folder?
Assistant: I'll process all screenshots in your Downloads folder...
[Processing 15 files...]
✓ Processed 15 files successfully:
  - 12 processed via OCR (0.4s total)
  - 3 processed via Vision (3.6s total)
  Categories: code(5), documentation(4), communication(3), design(2), other(1)
Files have been organized in ~/Screenshots/organized/
```

### Command Line Mode
```bash
# Analyze single file
python -m src analyze /path/to/screenshot.png

# Batch process folder
python -m src batch /path/to/screenshots/folder

# Process and organize
python -m src organize /path/to/screenshots/folder --auto-organize
```

## Project Structure After Setup
```
screenshot-organizer/
├── venv/                    # Virtual environment
├── .env                     # Your configuration
├── src/                     # Source code
├── tests/                   # Test files
├── logs/                    # Application logs
│   └── screenshot_organizer.log
└── ~/Screenshots/organized/ # Where organized files go
    ├── code/
    ├── errors/
    ├── documentation/
    ├── design/
    ├── communication/
    ├── memes/
    └── other/
```

## Common Commands

### Process Single Screenshot
```python
from src.screenshot_mcp_server import ScreenshotMCPServer

server = ScreenshotMCPServer()
result = await server.analyze_screenshot("/path/to/image.png")
print(f"Category: {result['category']}")
print(f"Suggested name: {result['suggested_filename']}")
```

### Batch Process with Organization
```python
result = await server.batch_process(
    folder="/path/to/screenshots",
    organize=True
)
print(f"Processed {result['total_files']} files")
```

## Troubleshooting

### Issue: "Tesseract not found"
**Solution:** Ensure Tesseract is installed and in PATH
```bash
which tesseract  # Should show path
# If not found, add to PATH or set TESSERACT_PATH in .env
```

### Issue: "Model download failed"
**Solution:** Check internet connection and disk space (need 8GB)
```bash
# Manual download
curl -L [model-url] -o ~/.cache/vision/model.bin
```

### Issue: "Azure AI Foundry credentials not configured"
**Solution:** Verify credentials are set correctly
```bash
# Check environment variables
echo $AZURE_AI_CHAT_ENDPOINT
echo $AZURE_AI_MODEL_DEPLOYMENT

# Test Azure connection
python -c "from azure.ai.inference import ChatCompletionsClient; from azure.core.credentials import AzureKeyCredential; import os; client = ChatCompletionsClient(endpoint=os.environ['AZURE_AI_CHAT_ENDPOINT'], credential=AzureKeyCredential(os.environ['AZURE_AI_CHAT_KEY'])); print('✅ Connection successful!')"
```

**Alternative:** Use Azure CLI authentication
```bash
az login
# Then comment out AZURE_AI_CHAT_KEY in your environment
```

### Issue: "MCP connection failed"
**Solution:** Ensure MCP server is running
```bash
# Check if port is in use
lsof -i :8080  # macOS/Linux
netstat -an | grep 8080  # Windows
```

## Performance Tips

1. **OCR Optimization**
   - Preprocess images (resize, denoise) for better OCR
   - Batch process during off-peak hours

2. **Memory Management**
   - Process large batches in chunks of 50 files
   - Restart vision model after 500 images

3. **Speed Improvements**
   - Use SSD for image storage
   - Disable vision model if not needed: `force_vision=False`

## Configuration Options

Edit `config/default_config.yaml`:

```yaml
processing:
  ocr_min_words: 10    # Adjust OCR threshold
  vision_timeout: 30    # Timeout for vision model
  
organization:
  categories:           # Add custom categories
    - code
    - your_custom_category
```

## Testing

Run the test suite:
```bash
# All tests
pytest

# Specific module
pytest tests/test_ocr_processor.py

# With coverage
pytest --cov=src --cov-report=html
```

## Getting Help

1. Check the logs: `tail -f logs/screenshot_organizer.log`
2. Run in debug mode: Set `LOG_LEVEL=DEBUG` in .env
3. View MCP server status: `http://localhost:8080/status`
4. Run diagnostics: `python -m src.utils.diagnostics`

## Next Steps

1. **Customize Categories:** Edit classification patterns in config
2. **Add Shortcuts:** Create shell aliases for common commands
3. **Schedule Batch Jobs:** Use cron/Task Scheduler for automation
4. **Extend Functionality:** Add new MCP tools for your needs

## Example Workflow

1. **Morning Cleanup:**
   ```bash
   # Process overnight screenshots
   python -m src organize ~/Desktop --auto-organize
   ```

2. **Project Documentation:**
   ```bash
   # Organize project screenshots
   python -m src batch ~/Projects/MyApp/screenshots --organize
   ```

3. **Weekly Archive:**
   ```bash
   # Archive and organize all screenshots
   python -m src batch ~/Screenshots --recursive --archive
   ```

## Support

- Documentation: `/docs`
- Issues: GitHub Issues
- Logs: `~/screenshot-organizer/logs/`

Happy organizing! 📸✨