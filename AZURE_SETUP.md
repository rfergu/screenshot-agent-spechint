# Azure AI Foundry Setup Guide

This guide walks you through setting up Azure AI Foundry to get the required credentials for building the Screenshot Agent project.

---

## Getting Started with Azure AI Foundry

### What You'll Get
- **$200 in free Azure credits** (for new Azure accounts)
- Access to **GPT-4o** and other Azure AI models
- Azure AI Foundry project with endpoints for agent development

### Prerequisites
- A Microsoft account (create one at https://account.microsoft.com if needed)
- A valid credit card (for identity verification - you won't be charged during free trial)

---

## Step 1: Create Azure Account and Get Free Credits

### Option A: New Azure Account (Recommended - Get $200 Free)

1. **Go to Azure Free Account Page**
   - Visit: https://azure.microsoft.com/free/
   - Click **"Start free"** button

2. **Sign in or Create Microsoft Account**
   - Use your existing Microsoft account, or
   - Create a new one

3. **Complete Azure Account Setup**
   - Enter your phone number for verification
   - Provide credit card information (for identity verification only)
   - Agree to terms and conditions
   - Complete the signup process

4. **Verify Your Free Credits**
   - You should see **$200 in credits** valid for 30 days
   - Plus 12 months of free services
   - After signup, you'll be taken to the Azure Portal

### Option B: Existing Azure Account

If you already have an Azure account:
- You may have already used your free credits
- You can still use Azure AI Foundry with pay-as-you-go pricing
- GPT-4o costs approximately $10-30/month for moderate usage

---

## Step 2: Access Azure AI Foundry

1. **Go to Azure AI Foundry**
   - Visit: https://ai.azure.com
   - Sign in with your Azure account

2. **Accept Terms of Service**
   - If this is your first time, you'll see a welcome screen
   - Review and accept the Azure AI terms

3. **You're now in Azure AI Foundry!**
   - This is where you'll create projects and deploy models

---

## Step 3: Create an Azure AI Foundry Project

1. **Create New Project**
   - Click **"+ New project"** button (top left or center of screen)
   - Or navigate to **"Build"** → **"Projects"** → **"+ New project"**

2. **Configure Project**
   - **Project name**: Choose a name (e.g., `screenshot-agent`)
   - **Hub**: Create new hub or select existing
     - If creating new hub:
       - **Hub name**: e.g., `screenshot-agent-hub`
       - **Subscription**: Select your Azure subscription
       - **Resource group**: Create new or select existing
         - Suggested name: `rg-screenshot-agent`
       - **Location**: Choose closest region (e.g., `East US`, `West Europe`)
   - Click **"Create"**

3. **Wait for Deployment**
   - This takes 2-5 minutes
   - Azure creates the hub, project, and associated resources

---

## Step 4: Deploy GPT-4o Model

1. **Navigate to Model Deployments**
   - In your project, go to **"Deployments"** (left sidebar)
   - Or click **"Model catalog"** → **"Deployments"**

2. **Create New Deployment**
   - Click **"+ Create deployment"** or **"+ Deploy model"**

3. **Select GPT-4o Model**
   - **Option A**: From Deployment screen
     - Click **"Deploy model"** → **"Deploy base model"**
     - Search for **"gpt-4o"**
     - Select **"gpt-4o"** (not gpt-4o-mini, not gpt-4)

   - **Option B**: From Model Catalog
     - Go to **"Model catalog"**
     - Search for **"gpt-4o"**
     - Click on **"gpt-4o"** tile
     - Click **"Deploy"**

4. **Configure Deployment**
   - **Deployment name**: `gpt-4o` (important - this is your AZURE_AI_MODEL_DEPLOYMENT value)
   - **Model version**: Select latest (auto-update enabled recommended)
   - **Deployment type**: Standard
   - **Tokens per Minute Rate Limit**:
     - Start with 10K-30K (you can increase later)
     - Higher = more concurrent requests but uses credits faster
   - Click **"Deploy"**

5. **Wait for Deployment**
   - Takes 1-2 minutes
   - Status will change from "Creating" to "Succeeded"

---

## Step 5: Get Your Environment Variables

Now you'll get the three critical values needed for the project.

### Get AZURE_AI_CHAT_ENDPOINT

1. **Go to Project Settings**
   - In your project, click **"Settings"** (gear icon, bottom left)
   - Or go to **"Project"** → **"Overview"**

2. **Find Project Connection String**
   - Look for section called **"Project details"** or **"Project connection"**
   - You'll see: **"Target URI"** or **"Project endpoint"**
   - It looks like: `https://[your-name].services.ai.azure.com/api/projects/[project-id]`

3. **Copy the Full Endpoint**
   ```bash
   # Example:
   https://eastus.api.azureml.ms/api/v1.0/subscriptions/abc-123/resourceGroups/rg-screenshot/providers/Microsoft.MachineLearningServices/workspaces/screenshot-agent/projects/screenshot-project
  ```

### Get AZURE_AI_MODEL_DEPLOYMENT

This is simply the deployment name you chose in Step 4:
```bash
gpt-4o
```

### Get AZURE_AI_CHAT_KEY

1. **Navigate to Keys and Endpoints**
   - Go to **"Settings"** → **"Keys and endpoints"**
   - Or go to **"Project"** → **"Keys and Endpoint"**

2. **Copy the API Key**
   - You'll see **"Key 1"** and **"Key 2"**
   - Copy either one (they both work)
   - Click the copy icon or manually select and copy

3. **Keep This Secret!**
   - Never commit this to git
   - Never share publicly
   - If exposed, regenerate it from this same screen

---

## Step 6: Set Environment Variables

### Option A: Export in Shell (Temporary - Good for Testing)

```bash
export AZURE_AI_CHAT_ENDPOINT="https://your-project.services.ai.azure.com/api/projects/your-project"
export AZURE_AI_MODEL_DEPLOYMENT="gpt-4o"
export AZURE_AI_CHAT_KEY="your_actual_api_key_here"

# Verify they're set
echo $AZURE_AI_CHAT_ENDPOINT
echo $AZURE_AI_MODEL_DEPLOYMENT
echo $AZURE_AI_CHAT_KEY
```

**Note**: These only last for your current terminal session.

### Option B: Add to Shell Profile (Permanent - Recommended)

**For macOS/Linux with zsh:**
```bash
# Open your shell config
nano ~/.zshrc

# Add these lines at the end:
export AZURE_AI_CHAT_ENDPOINT="https://your-project.services.ai.azure.com/api/projects/your-project"
export AZURE_AI_MODEL_DEPLOYMENT="gpt-4o"
export AZURE_AI_CHAT_KEY="your_actual_api_key_here"

# Save and exit (Ctrl+X, then Y, then Enter)

# Reload your shell config
source ~/.zshrc
```

**For macOS/Linux with bash:**
```bash
# Open your shell config
nano ~/.bashrc  # or ~/.bash_profile on macOS

# Add the same export lines above

# Reload
source ~/.bashrc
```

**For Windows (PowerShell):**
```powershell
# Set for current session
$env:AZURE_AI_CHAT_ENDPOINT="https://your-project.services.ai.azure.com/api/projects/your-project"
$env:AZURE_AI_MODEL_DEPLOYMENT="gpt-4o"
$env:AZURE_AI_CHAT_KEY="your_actual_api_key_here"

# Or set permanently (run PowerShell as Administrator)
[System.Environment]::SetEnvironmentVariable('AZURE_AI_CHAT_ENDPOINT', 'https://your-project.services.ai.azure.com/api/projects/your-project', 'User')
[System.Environment]::SetEnvironmentVariable('AZURE_AI_MODEL_DEPLOYMENT', 'gpt-4o', 'User')
[System.Environment]::SetEnvironmentVariable('AZURE_AI_CHAT_KEY', 'your_actual_api_key_here', 'User')
```

### Option C: Use .env File (Project-Specific)

Create `.env` file in project root:
```bash
cat > .env << 'EOF'
AZURE_AI_CHAT_ENDPOINT=https://your-project.services.ai.azure.com/api/projects/your-project
AZURE_AI_MODEL_DEPLOYMENT=gpt-4o
AZURE_AI_CHAT_KEY=your_actual_api_key_here
EOF

# Make sure .env is in .gitignore!
echo ".env" >> .gitignore
```

---

## Step 7: Verify Your Setup

Test that your credentials work:

```bash
# Install Agent Framework if not already installed
pip install agent-framework>=1.0.0b251111

# Test connection
python -c "
import asyncio
from agent_framework import AzureOpenAIChatClient
import os

async def test():
    endpoint = os.environ.get('AZURE_AI_CHAT_ENDPOINT') or os.environ.get('AZURE_OPENAI_ENDPOINT')
    key = os.environ.get('AZURE_AI_CHAT_KEY') or os.environ.get('AZURE_OPENAI_KEY')
    model = os.environ.get('AZURE_AI_MODEL_DEPLOYMENT') or os.environ.get('AZURE_OPENAI_DEPLOYMENT')

    client = AzureOpenAIChatClient(
        endpoint=endpoint,
        api_key=key,
        model=model
    )

    print('✅ Success! Azure credentials are configured!')
    print(f'Using model: {model}')

asyncio.run(test())
"
```

**Expected output:**
```
✅ Success! Azure credentials are configured!
Using model: gpt-4o
```

---

## Troubleshooting

### Error: "Invalid endpoint"
- **Check endpoint format**: Should be full URL including `/api/projects/[project-id]`
- **Verify project exists**: Go to https://ai.azure.com and confirm project is deployed
- **Check for typos**: Copy-paste the endpoint directly from Azure portal

### Error: "Authentication failed"
- **Check API key**: Make sure you copied the entire key (usually 32+ characters)
- **Key might be expired**: Regenerate from Azure portal if needed
- **Verify key is set**: Run `echo $AZURE_AI_CHAT_KEY` to confirm

### Error: "Model not found" or "Deployment not found"
- **Check deployment name**: Should match exactly what you named it (case-sensitive)
- **Verify deployment succeeded**: Go to Deployments page, check status is "Succeeded"
- **Wait a few minutes**: New deployments can take time to propagate

### Error: "Rate limit exceeded"
- **Increase rate limit**: Go to Deployments → Edit → Increase tokens per minute
- **Or wait**: Rate limits reset over time
- **Check credits**: Verify you still have Azure credits remaining

### Out of Azure Credits?
If you've used your $200 free credits:
1. **Check credit balance**: Azure Portal → Cost Management → Credits
2. **Upgrade to pay-as-you-go**: You'll only pay for what you use
3. **Alternative**: Use Azure OpenAI instead (same API, different pricing)
4. **Cost estimate**: GPT-4o costs ~$0.01-0.03 per 1K tokens
   - A typical conversation: 10-50 cents
   - Building this project: ~$5-20 depending on testing

---

## Alternative: Azure OpenAI (Instead of AI Foundry)

If you prefer Azure OpenAI service instead of Azure AI Foundry:

### Differences
- **Azure AI Foundry**: Newer, unified AI platform, simpler setup
- **Azure OpenAI**: Traditional Azure service, more enterprise features

### Setup for Azure OpenAI
1. Go to: https://portal.azure.com
2. Create resource → Search "OpenAI" → Create Azure OpenAI
3. Deploy GPT-4o model from Azure OpenAI Studio
4. Get endpoint from Keys and Endpoint page
5. Endpoint format: `https://[your-name].openai.azure.com/`

**Note**: The project code supports both! The `AzureOpenAIChatClient` auto-detects which type you're using.

---

## Cost Management Tips

### Stay Within Free Credits
- **Monitor usage**: Azure Portal → Cost Management → Cost Analysis
- **Set budget alerts**: Get notified when approaching credit limit
- **Use rate limits**: Keep tokens-per-minute low during development
- **Test locally first**: Use local mode (`--local` flag) for testing without API calls

### After Free Credits
- **Pay-as-you-go**: Only pay for what you use
- **Typical costs for this project**:
  - Development/testing: $5-20
  - Running demos: $1-5 per session
  - Production use: Depends on scale

---

## Quick Reference Card

Once set up, save this for reference:

```bash
# Your Azure AI Foundry credentials:
export AZURE_AI_CHAT_ENDPOINT="https://[your-hub].services.ai.azure.com/api/projects/[your-project]"
export AZURE_AI_MODEL_DEPLOYMENT="gpt-4o"
export AZURE_AI_CHAT_KEY="[your-api-key]"

# Test connection:
python -m src.cli_interface

# Or test with local mode (no API calls):
python -m src.cli_interface --local
```

---

## Next Steps

Once you have your environment variables set:
1. Return to **README.md** to continue with the project setup
2. Follow **quickstart.md** for installation instructions
3. Read **SPECHINT.md** for important tips before building

---

## Additional Resources

- **Azure AI Foundry Docs**: https://learn.microsoft.com/azure/ai-studio/
- **Azure Free Account**: https://azure.microsoft.com/free/
- **Azure AI Pricing**: https://azure.microsoft.com/pricing/details/cognitive-services/openai-service/
- **Azure Support**: https://portal.azure.com → Help + support

---

## Security Best Practices

✅ **DO:**
- Store API keys in environment variables
- Add `.env` to `.gitignore`
- Rotate keys periodically
- Use separate keys for dev/prod
- Set up budget alerts

❌ **DON'T:**
- Commit keys to git
- Share keys publicly
- Hardcode keys in source code
- Use production keys for testing
- Ignore cost alerts

---

## Which SDK to Use?

**Use Agent Framework, not OpenAI or Azure AI packages directly.**

```bash
# Install Agent Framework
pip install agent-framework>=1.0.0b251111
```

Agent Framework's `AzureOpenAIChatClient` works with both:
- Azure OpenAI Service (`*.openai.azure.com`)
- Azure AI Foundry (`*.services.ai.azure.com`)

You don't need to install `openai` or `azure-ai-inference` packages separately. The Agent Framework handles both Azure services seamlessly.

**For environment variables:**
- Azure OpenAI Service: Use `AZURE_OPENAI_ENDPOINT`, `AZURE_OPENAI_KEY`, `AZURE_OPENAI_DEPLOYMENT`
- Azure AI Foundry: Use `AZURE_AI_CHAT_ENDPOINT`, `AZURE_AI_CHAT_KEY`, `AZURE_AI_MODEL_DEPLOYMENT`

The project code will detect which service you're using automatically.

---

**You're all set!** 🚀

Once your environment variables are configured, you're ready to start building the Screenshot Agent project.
