---
title: "OpenClaw: So, What the Hell Is This AI Assistant?"
description: "Complete setup guide with free models and real automation examples"
date: "02/10/2026"
draft: false
---

Most AI tools sit in a browser tab and wait for you to ask questions. OpenClaw is different. It runs on your machine, connects to your messaging apps, and can actually do stuff while you're offline.

Think of it as a personal AI that lives in your infrastructure instead of someone else's cloud. You run it on your own server or computer, connect it to Telegram/Discord/WhatsApp, and it operates like an assistant that never logs off.

The setup takes about 30 minutes. Here's how to do it.

## Installation

**Quick Install (Mac/Linux):**

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

This downloads the CLI, installs it globally via npm, and launches the onboarding wizard automatically.

**Manual Install (All Platforms):**

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

The `--install-daemon` flag registers OpenClaw as a background service. This ensures the agent survives reboots and stays always-on.

**Windows:**

Install Node.js first, then:

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

## Initial Setup Wizard

After installation, the onboarding wizard walks you through configuration.

**1. Security Warning**

You'll see a warning that OpenClaw works on your local machine and has significant permissions. Read it and accept to continue.

**2. Choose Setup Mode**

Select "Quick Start" for the easiest setup. This handles most configuration automatically.

**3. Pick Your AI Model**

This is where you decide which AI provider to use. You have three main options:

- Cloud providers (OpenAI, Anthropic, Google)
- OpenRouter (aggregates multiple providers, has free models)
- Local models (Ollama)

We'll cover the free and local options below.

**4. Connect a Messaging Platform**

OpenClaw asks you to set up a chat interface. Telegram is the fastest option.

**5. Skills Setup**

The wizard will ask if you want to configure skills (tools that give it capabilities). You can skip for now and add them later, or choose "Yes" and select from the included list.

**6. Choose Interface**

OpenClaw offers two interfaces:

- Control UI: Web-based dashboard at <http://127.0.0.1:18789/>
- TUI: Terminal-based interface (recommended for first-time setup)

Select "Hatch in TUI" to start. The bot will ask for its name and how to address you, then it's ready to chat.

## Connecting to Telegram (Recommended)

Telegram is the easiest messaging platform to set up with OpenClaw.

**Step-by-step:**

1. Open Telegram and search for `@BotFather`
2. Start a chat and send `/newbot`
3. BotFather asks for a name. Type anything: `My OpenClaw Bot`
4. BotFather asks for a username. Must end in `_bot`: `myclaw_bot`
5. BotFather responds with your bot token. Copy it.

Example token:

```txt
6234567890:AAHdqTcvCH1vGWJxfSeofSAs0K5PALDsaw
```

1. During OpenClaw setup, select Telegram and paste the token
2. Open Telegram and message your new bot
3. The bot responds with a pairing code like: `ABC123`
4. In your terminal, run:

```bash
openclaw pairing approve telegram ABC123
```

1. Done. Message your bot "hello" and it should respond.

**Example conversation:**

```
You: hello
Bot: Hi! I'm your OpenClaw assistant. I can help you with tasks, 
     manage your calendar, read emails, and much more. What would 
     you like to do?

You: what can you do?
Bot: I can help with:
     - Email and calendar management
     - File operations
     - Web searches
     - GitHub repositories
     - Smart home control
     - And more through installable skills
     
     Try asking me to "check my calendar" or "list my Gmail inbox"
```

## Alternative Messaging Platforms

### WhatsApp

1. During setup, select WhatsApp
2. OpenClaw shows a QR code in the terminal
3. Open WhatsApp on your phone
4. Go to Settings > Linked Devices > Link a Device
5. Scan the QR code
6. Connection established

**Note:** QR codes expire in 60 seconds. If it times out, run:

```bash
openclaw channels login
```

### Discord

1. Go to Discord Developer Portal: <https://discord.com/developers/applications>
2. Click "New Application"
3. Name it (e.g., "OpenClaw Bot")
4. Go to "Bot" section and click "Add Bot"
5. Under "Token", click "Copy"
6. During OpenClaw setup, select Discord and paste the token
7. Go to "OAuth2" > "URL Generator"
8. Select scopes: `bot`, `applications.commands`
9. Select permissions: `Send Messages`, `Read Message History`
10. Copy the generated URL and open it in browser
11. Select your server and authorize
12. Message the bot with `!hello`

**Example Discord setup:**

```bash
$ openclaw onboard
> Select messaging platform: Discord
> Enter Discord bot token: [paste token]
> Bot connected successfully
> Invite URL: https://discord.com/api/oauth2/authorize?client_id=...
```

You can run multiple channels simultaneously. Message from Telegram, get responses in Discord, whatever works.

## Using OpenRouter with Free Models

OpenRouter is the easiest way to run OpenClaw without spending money. It aggregates hundreds of AI models and offers several completely free options.

**Why OpenRouter?**

- One API key for hundreds of models
- Several free preview models (no credit card required)
- Automatic fallback if one model is down
- Easy to switch between models
- Pay-per-use pricing for non-free models

**Getting Your API Key:**

1. Go to <https://openrouter.ai>
2. Sign up (email or GitHub login)
3. Click your profile icon > "Keys"
4. Click "Create Key"
5. Name it "OpenClaw" and copy the key

Example key format:

```
sk-or-v1-abc123def456...
```

**Configure OpenClaw with OpenRouter:**

During onboarding, select "OpenRouter" as your provider and paste the API key.

Or configure it manually:

```bash
openclaw auth add openrouter --key sk-or-v1-abc123def456...
```

**Scanning for Free Models:**

OpenRouter has multiple free models. Let OpenClaw find them:

```bash
openclaw models scan --provider openrouter --free-only
```

This lists all available free models with tool support (required for OpenClaw).

**Output example:**

```
Scanning OpenRouter for free models...

Found 5 free models:
  ✓ openrouter/free (auto-routing)
  ✓ openrouter/google/gemini-2.0-flash-thinking-exp-01-21:free
  ✓ openrouter/deepseek/deepseek-r1:free
  ✓ openrouter/google/gemini-2.0-flash-exp:free
  ✓ openrouter/qwen/qwq-32b-preview:free

Set primary model? (Y/n): Y
Select model: openrouter/free
Primary model set to: openrouter/free
```

**Popular Free Models (February 2026):**

- `openrouter/free` - Auto-routing to available free models (recommended)
- `openrouter/google/gemini-2.0-flash-thinking-exp-01-21:free` - Google's thinking model
- `openrouter/deepseek/deepseek-r1:free` - Strong reasoning capabilities
- `openrouter/google/gemini-2.0-flash-exp:free` - Fast and capable
- `openrouter/qwen/qwq-32b-preview:free` - Good for code tasks

**Manual Configuration:**

Edit `~/.openclaw/openclaw.json`:

```json
{
  "models": {
    "providers": {
      "openrouter": {
        "baseUrl": "https://openrouter.ai/api/v1",
        "apiKey": "sk-or-v1-your-key-here"
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "openrouter/free",
        "fallbacks": [
          "openrouter/google/gemini-2.0-flash-exp:free",
          "openrouter/deepseek/deepseek-r1:free"
        ]
      }
    }
  }
}
```

After editing, restart:

```bash
openclaw gateway restart
```

**Switching Models During Conversation:**

You can change models on the fly via Telegram:

```
You: /model openrouter/deepseek/deepseek-r1:free
Bot: Model switched to deepseek-r1 (free)

You: explain quantum computing in simple terms
Bot: [responds using DeepSeek model]

You: /model openrouter/free
Bot: Model switched back to auto-routing
```

**Rate Limits on Free Models:**

Free preview models have limits:

- Usually 10-20 requests per minute
- May have daily caps
- Slower during peak hours
- Can be removed without notice

For production 24/7 use, consider cheap paid models like `openrouter/google/gemini-flash-1.5` (about $0.10 per million tokens).

**Cost-Saving Strategy:**

Use `openrouter/openrouter/auto` as your primary. It automatically routes simple tasks to cheap models and complex tasks to capable models.

Configuration:

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "openrouter/openrouter/auto"
      }
    }
  }
}
```

Typical costs with auto-routing: $0.50-2.00 per day for moderate usage.

## Using Local Models with Ollama

Running OpenClaw with local Ollama models means everything stays on your machine. No API costs, complete privacy, and offline operation once models are downloaded.

**Why Local Models?**

- Zero API costs
- Complete data privacy
- Works offline
- No rate limits
- Full control over your data

**Trade-offs:**

- Requires decent hardware
- Setup is more involved
- Performance depends on your GPU/CPU
- Not as capable as top cloud models (though getting close)

**Hardware Requirements:**

Minimum:

- 16 GB RAM
- 8 GB free disk space per model
- CPU with AVX2 support

Recommended:

- 32 GB RAM or more
- NVIDIA GPU with 8+ GB VRAM
- NVMe SSD for faster model loading

**Step 1: Install Ollama**

**Mac:**

```bash
brew install ollama
```

**Linux:**

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**Windows:**
Download the installer from <https://ollama.com> and run it.

**Step 2: Start Ollama**

Open a terminal and run:

```bash
ollama serve
```

Leave this running. Ollama operates as a local server on port 11434.

**Example output:**

```
2026/02/10 14:32:15 Listening on 127.0.0.1:11434
2026/02/10 14:32:15 GPU detected: NVIDIA GeForce RTX 4070
```

**Step 3: Pull a Model**

OpenClaw needs models with good tool-calling support. Recommended models:

```bash
# Best for OpenClaw (strong tool calling, 32B parameters)
ollama pull qwen2.5-coder:32b
```

This downloads about 19 GB. First pull takes 10-30 minutes depending on your connection.

**Alternative models:**

```bash
# Strong reasoning, 32B
ollama pull deepseek-r1:32b

# Most capable but needs powerful hardware (70B)
ollama pull llama3.3:70b

# Lightweight option (8B, faster but less capable)
ollama pull qwen3:8b

# Balanced option (7B, good for testing)
ollama pull mistral:7b
```

**Verify the model works:**

```bash
ollama run qwen2.5-coder:32b
```

**Example test:**

```
>>> Write a Python function to reverse a string
Here's a simple function:

def reverse_string(s):
    return s[::-1]

# Test it
print(reverse_string("hello"))  # Output: olleh

>>> /bye
```

**Step 4: Configure OpenClaw for Ollama**

**Automatic Method (Easiest):**

```bash
ollama launch openclaw
```

This opens an interactive selection screen showing all pulled models. Choose one and it configures everything automatically.

**Example:**

```
Available Ollama models:
  1. qwen2.5-coder:32b (19 GB)
  2. mistral:7b (4.1 GB)
  3. deepseek-r1:32b (19 GB)

Select model (1-3): 1
✓ Configured OpenClaw to use qwen2.5-coder:32b
✓ Gateway restarted
```

**Manual Configuration:**

Set the API key (any value works for local):

```bash
openclaw config set models.providers.ollama.apiKey "ollama-local"
```

OpenClaw auto-discovers models from your local Ollama instance.

**Advanced Manual Config:**

Edit `~/.openclaw/openclaw.json`:

```json
{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://127.0.0.1:11434/v1",
        "apiKey": "ollama-local",
        "api": "openai-completions",
        "models": [
          {
            "id": "qwen2.5-coder:32b",
            "name": "Qwen 2.5 Coder 32B (Local)",
            "reasoning": false,
            "input": ["text"],
            "cost": {
              "input": 0,
              "output": 0,
              "cacheRead": 0,
              "cacheWrite": 0
            },
            "contextWindow": 128000,
            "maxTokens": 8192
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/qwen2.5-coder:32b",
        "fallbacks": ["ollama/mistral:7b"]
      }
    }
  }
}
```

Restart the gateway:

```bash
openclaw gateway restart
```

**Step 5: Verify Everything Works**

Check gateway health:

```bash
openclaw gateway health
```

**Expected output:**

```
Gateway Status: ✓ Healthy
Primary Model: ollama/qwen2.5-coder:32b
Uptime: 2m 34s
Active Channels: telegram
```

List available models:

```bash
openclaw models list
```

**Output:**

```
Available models:
  ✓ ollama/qwen2.5-coder:32b (primary)
  ✓ ollama/mistral:7b (fallback)
```

**Test with your bot:**

```
You: hello, introduce yourself
Bot: Hi! I'm your local OpenClaw assistant running on Qwen 2.5 Coder. 
     I'm operating entirely on your machine, so your data stays private. 
     How can I help you today?
```

First response might be slower (5-10 seconds) as the model loads into memory. Subsequent responses are faster (1-3 seconds).

**Recommended Ollama Models for OpenClaw (February 2026):**

| Model | Size | VRAM | Use Case | Speed |
|-------|------|------|----------|-------|
| qwen2.5-coder:32b | 19 GB | 20 GB | Best tool calling | Medium |
| deepseek-r1:32b | 19 GB | 20 GB | Strong reasoning | Medium |
| llama3.3:70b | 40 GB | 48 GB | Most capable | Slow |
| qwen3:8b | 4.7 GB | 6 GB | Lightweight | Fast |
| mistral:7b | 4.1 GB | 5 GB | Good balance | Fast |

**Performance Tips:**

- Use models with 128k+ context windows for best results
- GPU acceleration is 5-10x faster than CPU
- Keep Ollama running in the background
- Use smaller models (7B-8B) for testing
- Use larger models (32B+) for production

**Troubleshooting:**

**Problem: "Gateway health check failed"**

Solution:

```bash
# Check if Ollama is running
ollama ps

# If empty, start it
ollama serve

# Restart OpenClaw gateway
openclaw gateway restart
```

**Problem: "Model not responding"**

Solution:

```bash
# Verify model is pulled
ollama list

# Should show your model
# If not, pull it again
ollama pull qwen2.5-coder:32b

# Check config matches pulled model
openclaw models list
```

**Problem: Slow responses (30+ seconds)**

Solution:

- Try a smaller model: `ollama pull qwen3:8b`
- Check if running on CPU (much slower than GPU)
- Close other applications to free RAM
- Verify GPU is detected: `ollama ps` shows GPU info

**Problem: "Error: model requires X GB VRAM, only Y GB available"**

Solution:

- Use a smaller model variant
- qwen2.5-coder:32b → qwen3:8b
- deepseek-r1:32b → mistral:7b
- Or add more VRAM (upgrade GPU)

**Setting Up Auto-Start:**

**Mac:**

Ollama installer includes a launchd service. Verify it's enabled:

```bash
launchctl list | grep ollama
```

If not listed:

```bash
brew services start ollama
```

**Linux (systemd):**

Create `/etc/systemd/system/ollama.service`:

```ini
[Unit]
Description=Ollama Service
After=network.target

[Service]
Type=simple
User=your-username
ExecStart=/usr/local/bin/ollama serve
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl enable ollama
sudo systemctl start ollama
```

**Windows:**

Use Task Scheduler:

1. Open Task Scheduler
2. Create Basic Task
3. Trigger: At log on
4. Action: Start a program
5. Program: `C:\Users\YourName\AppData\Local\Programs\Ollama\ollama.exe`
6. Arguments: `serve`

**Cost Reality:**

Local models are free to run but have electricity costs.

Typical power usage:

- 7B model on CPU: 50-100W
- 32B model on GPU: 150-250W
- 70B model on powerful GPU: 300-400W

At $0.12/kWh electricity rate:

- Light usage (2 hours/day): $0.05-0.15/day
- Heavy usage (24/7): $1.00-3.00/day

For light usage, OpenRouter's free tier is cheaper. For heavy 24/7 usage, local becomes cheaper after the first month.

## Key Features

### Persistent Memory

Unlike most AI chats that forget everything when you close the tab, OpenClaw remembers.

**Example:**

```
Day 1:
You: I prefer flights after 10 AM
Bot: Got it, I'll remember that you prefer flights after 10 AM.

Day 5:
You: find me a flight to New York next week
Bot: I'll search for flights after 10 AM based on your preference.
     [shows results filtered to afternoon flights]
```

The AI builds a profile over time:

- Your preferences
- How you work
- What you care about
- Common tasks you request

This changes how you interact with AI. You stop re-explaining context every time.

### Heartbeat System

OpenClaw can wake up every few hours and check things for you automatically. No manual triggers needed.

**How to enable:**

```bash
openclaw config set heartbeat.enabled true
openclaw config set heartbeat.interval "4h"
```

Or edit config:

```json
{
  "heartbeat": {
    "enabled": true,
    "interval": "4h",
    "tasks": [
      "check-email",
      "calendar-summary",
      "github-activity"
    ]
  }
}
```

**Real-world example: Morning Brief**

Configure Heartbeat to run at 6:47 AM daily:

```json
{
  "heartbeat": {
    "enabled": true,
    "schedule": "47 6 * * *",
    "tasks": [
      {
        "type": "email-scan",
        "params": {
          "unread_only": true,
          "urgent_keywords": ["asap", "urgent", "important"]
        }
      },
      {
        "type": "calendar-today"
      },
      {
        "type": "weather",
        "location": "auto"
      }
    ],
    "channel": "telegram"
  }
}
```

**Morning brief example output:**

```
Good morning! Here's your 6:47 AM brief:

📧 Email (3 urgent):
  • Client X needs approval on proposal (received 2h ago)
  • Meeting reschedule request from Sarah
  • Invoice reminder from vendor

📅 Today's Calendar:
  • 9:00 AM - Team standup (30 min)
  • 11:00 AM - Client call with Acme Corp (1 hour)
  • 2:00 PM - Product review (45 min)
  • 4:30 PM - 1-on-1 with manager (30 min)

🌤 Weather:
  • Currently 52°F, partly cloudy
  • High of 68°F today
  • No rain expected
```

This replaces checking five different apps before coffee.

### The Skills System

Skills are modular packages that give OpenClaw new capabilities. There are 700+ community-built skills available on ClawHub.

**Installing Skills:**

```bash
# Install a skill
openclaw skills install github

# Install multiple
openclaw skills install gmail calendar todoist

# List installed
openclaw skills list

# Search available
openclaw skills search "email"
```

**Or just ask OpenClaw:**

```
You: I need to automate my Todoist tasks
Bot: I can install the Todoist skill for that. Should I proceed?
You: yes
Bot: Installing todoist skill...
     ✓ Installed successfully
     Please provide your Todoist API token to connect.
```

The agent can write its own extensions if what you need doesn't exist.

### Google Workspace Integration (gog skill)

The gog skill connects your entire Google ecosystem: Gmail, Calendar, Drive, Docs, Sheets.

**Installation:**

```bash
openclaw skills install gog
```

**Setup (OAuth authentication):**

```bash
openclaw skills setup gog
```

This opens a browser for Google OAuth. Approve the permissions and you're done.

**Real conversation example:**

```
You: scan my Gmail, any urgent emails?
Bot: Found 2 urgent emails:
     1. From: sarah@company.com
        Subject: Q1 Report needs review
        Received: 2 hours ago
        
     2. From: billing@vendor.com
        Subject: Invoice #4532 due tomorrow
        Received: 5 hours ago

You: check today's calendar
Bot: Your schedule for Tuesday, Feb 10:
     • 9:00 AM - Team standup (30 min, Zoom link)
     • 11:00 AM - Client call with Acme Corp (1 hour, in-person)
     • 2:00 PM - Product review (45 min, Conference Room B)
     
     Next free slot: 12:00 PM - 2:00 PM

You: draft an email to Sarah saying I'll review the report by end of day
Bot: Draft created:
     
     To: sarah@company.com
     Subject: Re: Q1 Report needs review
     
     Hi Sarah,
     
     Thanks for sending this over. I'll have my review completed 
     and feedback to you by end of day today.
     
     Best,
     [Your name]
     
     Send this email? (yes/no/edit)

You: send
Bot: ✓ Email sent to sarah@company.com
```

**Automated morning email scan:**

Set up Heartbeat to scan Gmail every morning and send summaries:

```json
{
  "heartbeat": {
    "enabled": true,
    "schedule": "0 9 * * *",
    "tasks": [
      {
        "skill": "gog",
        "action": "gmail-scan",
        "params": {
          "unread_only": true,
          "from_hours": 24
        }
      }
    ]
  }
}
```

### GitHub Integration

Manage repositories, review pull requests, check CI/CD pipelines, all from your messaging app.

**Installation:**

```bash
openclaw skills install github
```

**Setup:**

```bash
openclaw skills setup github
```

Enter your GitHub personal access token (create one at <https://github.com/settings/tokens>).

**Example commands:**

```
You: list open issues in my repos
Bot: Found 7 open issues across your repositories:
     
     myapp (3 issues):
     • #42: Login page broken on mobile
     • #38: Add dark mode support
     • #35: API rate limiting needed
     
     website (2 issues):
     • #15: Update homepage copy
     • #12: Fix contact form validation
     
     ...

You: show details for PR #123 in myapp
Bot: Pull Request #123 in myapp/myapp
     Title: Add user authentication
     Author: @contributor
     Status: Open, needs review
     Checks: ✓ 3 passed, ✗ 1 failed (linter)
     Files changed: 12
     +245 -67 lines
     
     Recent comments:
     • @reviewer: "Looks good, just fix the linter errors"
     
     View on GitHub: https://github.com/myapp/myapp/pull/123

You: create a new issue in website: fix broken contact form
Bot: ✓ Created issue #16 in website
     Title: Fix broken contact form
     Labels: bug
     
     View: https://github.com/myuser/website/issues/16
```

**Monitor CI/CD automatically:**

```json
{
  "heartbeat": {
    "enabled": true,
    "interval": "1h",
    "tasks": [
      {
        "skill": "github",
        "action": "check-ci",
        "repos": ["myuser/myapp", "myuser/website"],
        "notify_on": ["failure", "success_after_failure"]
      }
    ]
  }
}
```

**Notification example:**

```
⚠️ CI/CD Alert

Repository: myapp
Branch: main
Commit: abc123 (Fix authentication bug)
Status: ✗ Failed

Failed checks:
  • Linter: 3 errors in auth.js
  • Unit tests: 2 tests failing

View logs: https://github.com/myapp/myapp/actions/runs/123456
```

### File Operations

OpenClaw can read, write, organize, batch rename, and search files on your system.

**Example commands:**

```
You: list all PDF files in my Documents folder
Bot: Found 47 PDF files in ~/Documents:
     • Q4_Report_2025.pdf (2.3 MB, modified 3 days ago)
     • Invoice_Jan2026.pdf (156 KB, modified 1 week ago)
     • Meeting_Notes_Feb.pdf (1.1 MB, modified yesterday)
     ...

You: organize my Downloads folder by file type
Bot: Analyzing ~/Downloads...
     Found 156 files:
     • 23 PDFs
     • 45 images (jpg, png)
     • 12 documents (docx, xlsx)
     • 67 archives (zip, tar.gz)
     • 9 other files
     
     Create subfolders and organize? (yes/no)

You: yes
Bot: ✓ Created folders: PDFs, Images, Documents, Archives, Other
     ✓ Moved 156 files
     ✓ Downloads organized

You: batch rename all files in ~/Photos/Vacation to "Paris2025_XX"
Bot: Found 84 images in ~/Photos/Vacation
     Will rename to: Paris2025_01.jpg, Paris2025_02.jpg, ...
     
     Preview:
     • IMG_3847.jpg → Paris2025_01.jpg
     • IMG_3848.jpg → Paris2025_02.jpg
     • IMG_3849.jpg → Paris2025_03.jpg
     ...
     
     Proceed? (yes/no)

You: yes
Bot: ✓ Renamed 84 files
```

### Browser Control

OpenClaw can open a Chrome instance, navigate websites, fill forms, extract data, and take screenshots.

**Example use cases:**

```
You: open Hacker News and get me the top 5 posts
Bot: Opening news.ycombinator.com...
     
     Top 5 posts right now:
     1. "Show HN: I built a local-first task manager" (287 points)
     2. "The cost of JavaScript in 2026" (421 points)
     3. "PostgreSQL 17 performance improvements" (156 points)
     4. "Why I switched from React to HTMX" (892 points)
     5. "Building a compiler from scratch" (234 points)

You: search for "quantum computing breakthroughs 2026" and summarize the first 3 results
Bot: Searching Google...
     
     Summary of top 3 results:
     
     1. Nature.com - "Quantum Error Correction Milestone"
        Researchers achieved 99.9% fidelity in quantum error 
        correction, a major step toward practical quantum computers.
     
     2. MIT News - "New Quantum Algorithm Solves NP-Hard Problems"
        MIT team developed algorithm that solves certain optimization 
        problems exponentially faster than classical methods.
     
     3. Quanta Magazine - "First Commercially Viable Quantum Computer"
        IBM announced a 1000-qubit system available for enterprise use.

You: take a screenshot of my portfolio website
Bot: Opening yourportfolio.com...
     ✓ Screenshot saved to ~/Screenshots/portfolio_20260210.png
     
     [Shows screenshot in chat]
```

**Automated price monitoring:**

```json
{
  "heartbeat": {
    "enabled": true,
    "interval": "6h",
    "tasks": [
      {
        "skill": "browser",
        "action": "check-price",
        "url": "https://example.com/product/laptop",
        "selector": ".price-tag",
        "notify_if_below": 999
      }
    ]
  }
}
```

**Price alert example:**

```
💰 Price Alert!

Product: Laptop Model X
Current price: $899
Previous: $1,199
Change: -25%

This is below your threshold of $999.

Link: https://example.com/product/laptop
```

### Smart Home Integration

Control lights, climate systems, cameras through natural language.

**Installation (Home Assistant required):**

```bash
openclaw skills install homeassistant
```

**Setup:**

```bash
openclaw skills setup homeassistant
```

Enter your Home Assistant URL and long-lived access token.

**Example commands:**

```
You: turn off all lights
Bot: ✓ Turned off 12 lights in your home

You: set living room temperature to 72°F
Bot: ✓ Set living room thermostat to 72°F
     Current temp: 68°F
     Heating to target...

You: show me the front door camera
Bot: [Displays camera feed image]
     Last motion: 5 minutes ago

You: what's the temperature in each room?
Bot: Current temperatures:
     • Living room: 68°F (target: 72°F)
     • Bedroom: 70°F (target: 70°F)
     • Office: 71°F (no target set)
     • Kitchen: 69°F (no target set)
```

**Automated routines:**

```json
{
  "heartbeat": {
    "enabled": true,
    "schedule": "0 22 * * *",
    "tasks": [
      {
        "skill": "homeassistant",
        "action": "night-routine",
        "steps": [
          {"action": "lights_off", "except": ["bedroom"]},
          {"action": "lock_doors"},
          {"action": "set_alarm", "mode": "home"}
        ]
      }
    ]
  }
}
```

**Notification:**

```
🌙 Night routine complete (10:00 PM)
✓ All lights off (except bedroom)
✓ Front door locked
✓ Security alarm armed (home mode)
```

### Productivity Tools

Built-in integrations for Notion, Obsidian, Todoist, Asana, Trello.

**Example: Todoist automation**

```bash
openclaw skills install todoist
openclaw skills setup todoist
```

**Usage:**

```
You: add task "Review Q1 report" due tomorrow priority 1
Bot: ✓ Added to Todoist
     Task: Review Q1 report
     Due: Feb 11, 2026
     Priority: P1

You: show my tasks for today
Bot: Today's tasks (4 items):
     • ✓ Team standup (completed)
     • Review Q1 report (P1, due today)
     • Send invoice to client (P2, due today)
     • Update project roadmap (P3, due today)

You: mark "Send invoice to client" as complete
Bot: ✓ Completed: Send invoice to client

You: what's left for this week?
Bot: This week (12 items, 4 completed):
     
     Today:
     • Review Q1 report (P1)
     • Update project roadmap (P3)
     
     Tomorrow:
     • Prepare presentation (P1)
     • Code review for PR #45 (P2)
     
     Thu Feb 12:
     • Client meeting prep (P2)
     ...
```

**Real-world automation: Weekly meal planning**

One user set up this workflow that saves an hour every week:

```json
{
  "heartbeat": {
    "enabled": true,
    "schedule": "0 18 * * 0",
    "tasks": [
      {
        "skill": "custom-meal-planner",
        "action": "generate-weekly-plan",
        "preferences": {
          "dietary": ["vegetarian", "gluten-free"],
          "servings": 4,
          "budget": 150
        },
        "output": "notion"
      }
    ]
  }
}
```

Every Sunday at 6 PM:

1. Bot generates meal plan for the week
2. Creates shopping list
3. Adds to Notion database
4. Sends summary to Telegram

**Output:**

```
🍽 Weekly Meal Plan (Feb 10-16)

Monday: Vegetarian stir-fry with tofu
Tuesday: Gluten-free pasta primavera
Wednesday: Lentil curry with rice
Thursday: Veggie burgers with sweet potato fries
Friday: Caprese quinoa salad
Weekend: Mushroom risotto

🛒 Shopping list added to Notion
Estimated cost: $142

Full plan: [Notion link]
```

## Real Automation Workflows

### Workflow 1: Email Triage

**Goal:** Stop constantly checking email. Get automated summaries twice daily.

**Setup:**

```json
{
  "heartbeat": {
    "enabled": true,
    "schedule": ["0 9 * * *", "0 14 * * *"],
    "tasks": [
      {
        "skill": "gog",
        "action": "email-triage",
        "params": {
          "inbox": "primary",
          "categorize": true,
          "since_hours": 12
        },
        "channel": "telegram"
      }
    ]
  }
}
```

**Result:**

9 AM and 2 PM daily, you get:

```
📧 Email Triage Summary

Urgent (2):
  • Client X: Proposal needs signature by EOD
  • Manager: Can you join 3 PM meeting?

Important (3):
  • Team lead: PR review requested
  • Finance: Expense report due Friday
  • Partner: Q&A about integration

Low priority (8):
  • Newsletters, promotions, automated updates

Action needed on 5 emails. View details? (yes/no)
```

No more email anxiety. Check at specific times, stay focused otherwise.

### Workflow 2: GitHub Monitoring for Teams

**Goal:** Track all activity across team repositories without manual checking.

**Setup:**

```json
{
  "heartbeat": {
    "enabled": true,
    "interval": "30m",
    "tasks": [
      {
        "skill": "github",
        "action": "monitor-activity",
        "repos": [
          "company/backend",
          "company/frontend",
          "company/mobile"
        ],
        "watch_for": [
          "new_prs",
          "pr_reviews_needed",
          "ci_failures",
          "new_issues"
        ],
        "channel": "discord"
      }
    ]
  }
}
```

**Result (example notification):**

```
🔔 GitHub Activity (last 30 min)

New PRs (2):
  • backend #234: "Add Redis caching layer" by @dev1
  • frontend #89: "Redesign dashboard UI" by @designer

Review needed (3):
  • backend #230: Waiting on your review
  • frontend #87: 2 approvals, needs 1 more
  • mobile #45: Merge conflicts need resolution

CI Failures (1):
  • backend #232: Unit tests failing on main branch

New Issues (1):
  • mobile #52: "App crashes on Android 13" by @user
```

Entire team stays synced without spam. Just relevant updates.

### Workflow 3: Medical Billing Automation

**Real example from an anesthesiologist:**

**Problem:** Spend 2-3 hours daily on medical billing paperwork.

**Solution:**

Custom skill that:

1. Reads procedure notes from EMR
2. Extracts CPT codes
3. Generates billing forms
4. Submits to insurance portals
5. Tracks claim status

**Code snippet (simplified):**

```javascript
// Custom skill: medical-billing
module.exports = {
  name: 'medical-billing',
  actions: {
    async processDailyProcedures(context) {
      // Fetch today's procedures from EMR
      const procedures = await this.emr.getProcedures({
        date: 'today',
        provider: context.user.npi
      });
      
      // Extract billing codes
      const claims = procedures.map(proc => ({
        patient: proc.patient_id,
        cpt_codes: this.extractCPT(proc.notes),
        diagnosis: proc.diagnosis_codes,
        amount: this.calculateBilling(proc)
      }));
      
      // Generate and submit claims
      for (const claim of claims) {
        await this.submitClaim(claim);
      }
      
      return {
        processed: claims.length,
        total_billed: claims.reduce((sum, c) => sum + c.amount, 0)
      };
    }
  }
};
```

**Heartbeat config:**

```json
{
  "heartbeat": {
    "schedule": "0 18 * * *",
    "tasks": [
      {
        "skill": "medical-billing",
        "action": "processDailyProcedures"
      }
    ]
  }
}
```

**Daily result:**

```
💼 Daily Billing Complete

Processed: 12 procedures
Total billed: $8,450
Claims submitted: 12
Success rate: 100%

Time saved: ~2.5 hours
```

Went from 3 hours of manual work to 5 minutes of review.

### Workflow 4: WHOOP Health Integration

**Goal:** Automatic health metric tracking and insights.

**Conversation setup:**

```
You: I want to track my WHOOP data automatically
Bot: I'll need access to the WHOOP API. Do you have your API credentials?
You: what do I need to set up?
Bot: You need:
     1. WHOOP API key (get from developer.whoop.com)
     2. Your user ID
     
     I can install the whoop skill and configure it. Ready?
You: yes
Bot: Installing whoop skill...
     ✓ Installed
     
     Please provide your WHOOP API key:
You: [paste API key]
Bot: ✓ Connected to WHOOP
     ✓ Fetched your profile
     
     I can now:
     • Get daily recovery scores
     • Track sleep metrics
     • Monitor strain
     • Analyze trends
     
     Want me to send daily summaries?
You: yes, every morning at 7 AM
Bot: ✓ Configured daily health brief at 7:00 AM
```

**Daily brief:**

```
🏃 Health Summary (Feb 10, 2026)

Recovery: 87% (Green)
  • HRV: 78 ms (↑ from yesterday)
  • Resting HR: 52 bpm (↓ from yesterday)
  • Sleep: 7h 42m (89% quality)

Strain: 12.4 (Moderate)
  • Activity: 45 min workout
  • Steps: 8,247

Recommendations:
  ✓ Recovery is high, good day for intense training
  ✓ Sleep quality excellent, keep current routine
  ✓ HRV trending up, stress management working

Full report: [link]
```

## Command Line Operations

OpenClaw has a comprehensive CLI for management:

**Skills:**

```bash
# List installed skills
openclaw skills list

# Install a skill
openclaw skills install github

# Install multiple
openclaw skills install gmail calendar todoist

# Remove a skill
openclaw skills remove github

# Update all skills
openclaw skills update --all

# Search for skills
openclaw skills search "email"

# Show skill details
openclaw skills info gmail
```

**System Management:**

```bash
# Check system health
openclaw doctor

# View real-time logs
openclaw logs --follow

# View logs for specific skill
openclaw logs --skill github

# Security audit
openclaw security audit --deep

# Restart gateway
openclaw gateway restart

# Stop gateway
openclaw gateway stop

# Check gateway status
openclaw gateway health
```

**Model Management:**

```bash
# List available models
openclaw models list

# Scan for new models
openclaw models scan --provider openrouter

# Set primary model
openclaw models set-primary openrouter/free

# Test a model
openclaw models test ollama/qwen2.5-coder:32b
```

**Configuration:**

```bash
# View current config
openclaw config show

# Set a config value
openclaw config set heartbeat.enabled true

# Get a config value
openclaw config get agents.defaults.model.primary

# Edit config in editor
openclaw config edit

# Reset to defaults
openclaw config reset
```

**Channels:**

```bash
# List connected channels
openclaw channels list

# Add new channel
openclaw channels add telegram

# Remove channel
openclaw channels remove discord

# Login to channel
openclaw channels login whatsapp
```

## Web Dashboard

OpenClaw includes a web interface at `http://127.0.0.1:18789/`

**Features:**

- Configure settings
- Monitor activity
- Manage skills
- View conversation history
- Check system health
- Modify Heartbeat tasks
- Review logs

Access it by running:

```bash
openclaw ui
```

This opens the dashboard in your default browser.

## Security Considerations

OpenClaw needs significant permissions to be useful. This creates risk.

**The Reality:**

- Access to files, email, calendar, browser
- Ability to execute commands
- Read/write to your filesystem
- Access to API keys and credentials

About 7% of skills on ClawHub mishandle secrets. Malicious skills exist that steal credentials. The fundamental problem is prompt injection: malicious instructions embedded in emails or websites can trick the AI into unauthorized actions.

**How to Use Safely:**

**1. Isolated Environment**

Run OpenClaw on a dedicated machine, VM, or container. Don't run it on your main laptop with production access.

**Example Docker setup:**

```dockerfile
FROM node:20
RUN npm install -g openclaw@latest
WORKDIR /openclaw
CMD ["openclaw", "gateway", "start"]
```

```bash
docker build -t openclaw .
docker run -d -p 18789:18789 -v ~/.openclaw:/root/.openclaw openclaw
```

**2. Principle of Least Privilege**

Only grant permissions needed for specific tasks.

Example: Gmail skill needs read access, not full Drive access.

**Config:**

```json
{
  "skills": {
    "gog": {
      "scopes": [
        "gmail.readonly",
        "calendar.readonly"
      ]
    }
  }
}
```

**3. Audit Skills Before Installation**

Read the code or trust community security assessments.

```bash
# View skill source before installing
openclaw skills inspect github

# Check security score
openclaw skills info github --security
```

**4. Monitor Activity**

Review what OpenClaw is doing:

```bash
# Real-time monitoring
openclaw logs --follow

# Filter by skill
openclaw logs --skill gog --level warn

# Security-specific logs
openclaw logs --security
```

**5. Regular Security Audits**

```bash
# Full security scan
openclaw security audit --deep

# Check for exposed credentials
openclaw security scan-secrets

# Review permissions
openclaw security review-permissions
```

**6. Start Small**

Test with limited access before expanding.

**Week 1:** Email reads only
**Week 2:** Add calendar
**Week 3:** Add file operations (in sandbox)
**Week 4:** Add command execution (carefully)

**OpenClaw Security Features:**

- Partnership with VirusTotal (scans all skills on ClawHub)
- Daily re-scanning of uploaded skills
- Sandboxed skill execution (optional)
- Permission system for skills
- Audit logs

**Still, be careful.** This is powerful infrastructure you control, which means you're responsible for securing it.

## Cost Reality

**OpenClaw itself:** Free and open-source

**Costs come from:**

1. **AI model API usage** (every message costs tokens)
2. **Optional cloud hosting** (if not running locally)
3. **Third-party API keys** (for specific skills)

**API Cost Examples:**

**OpenRouter (pay-per-use models):**

- Light usage (50 messages/day): $0.50-1.00/day
- Moderate usage (200 messages/day): $2.00-5.00/day
- Heavy usage (500+ messages/day): $5.00-15.00/day

**OpenRouter (free models):**

- Any usage level: $0.00/day
- Limited by rate limits (10-20 requests/minute)

**Claude API directly:**

- Moderate usage: $10-25/day
- Heavy usage: $25-50/day

**GPT-4 API:**

- Moderate usage: $15-30/day
- Heavy usage: $30-60/day

**Local Ollama:**

- API cost: $0.00
- Electricity: $1-3/day (24/7 operation)
- Hardware cost: One-time GPU expense

**Recommendation for beginners:**

Start with OpenRouter's free models. Once you understand your usage patterns, upgrade to cheap paid models or switch to local.

**Monitoring costs:**

```bash
# Check API usage stats
openclaw usage stats --week

# Set spending limits
openclaw config set limits.daily_spend 5.00

# Get alerts
openclaw config set alerts.spending_threshold 0.8
```

**Example alert:**

```
⚠️ Spending Alert

Daily limit: $5.00
Current spend: $4.20 (84%)
Estimated EOD: $4.75

Top consumers:
  • Heartbeat email scans: $1.80
  • GitHub monitoring: $1.20
  • Manual queries: $1.20

Approaching limit. Reduce usage or increase threshold.
```

## Why This Matters

OpenClaw represents a shift from "AI that answers questions" to "AI that operates systems."

It can:

- Manage your inbox
- Control your calendar
- Monitor repos
- Run automations
- Execute workflows

All through natural conversation.

**The difference:**

Traditional AI: "What should I do about this email?"
OpenClaw: [Already sorted your emails, sent responses, updated calendar, notified relevant people]

Traditional AI: "How do I deploy this code?"
OpenClaw: [Already ran tests, created PR, triggered CI/CD, posted in Slack]

Traditional AI: "What's my schedule today?"
OpenClaw: [Sent you a morning brief 3 hours ago with schedule, urgent emails, weather, and task priorities]

**But it requires:**

- Technical comfort
- Security awareness
- Willingness to iterate and troubleshoot
- Understanding of what you're granting access to

This isn't a polished consumer product. It's infrastructure you control, which means you're responsible for securing it.

For developers, tech enthusiasts, and people who want AI that genuinely operates in their environment rather than just talking about it, OpenClaw is worth exploring.

Just run it on a separate machine.

---

*Latest version: v2026.2.6*

**Resources:**

- Docs: <https://docs.openclaw.ai>
- Skills: <https://clawhub.com>
- GitHub: <https://github.com/openclaw/openclaw>
- Community: <https://discord.gg/openclaw>
- Security: <https://docs.openclaw.ai/security>
