# Complete AI-Powered Development Environment Setup

A comprehensive guide for setting up a modern Mac development environment with AI integration and multi-window workflow.

## Prerequisites

- Fresh Mac installation
- Administrator access
- Internet connection

---

## Step 1: Install Homebrew (Package Manager)

Open Terminal (Command + Space, type "Terminal") and run:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the prompts. After installation, it may ask you to run commands to add Homebrew to your PATH. Copy and run those commands.

---

## Step 2: Install iTerm2

```bash
brew install --cask iterm2
```

Launch iTerm2 from Applications or Spotlight. You can make it your default terminal.

### Quick iTerm2 Setup

- Preferences (Cmd + ,) → Profiles → Window → Set transparency to ~10% for style
- Profiles → Keys → Key Mappings → Add "Natural Text Editing" preset for better text navigation

---

## Step 3: Install tmux

```bash
brew install tmux
```

Create a basic config file:

```bash
cat > ~/.tmux.conf << 'EOF'
# Improve colors
set -g default-terminal "screen-256color"

# Change prefix from Ctrl-b to Ctrl-a (easier to reach)
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Split panes using | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# Enable mouse mode
set -g mouse on

# Start window numbering at 1
set -g base-index 1
set -g pane-base-index 1
EOF
```

---

## Step 4: Install Git

```bash
brew install git
```

Configure Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

---

## Step 5: Install Node.js (for our demo project)

```bash
brew install node
```

---

## Step 6: Install Cursor (AI-Powered IDE)

Download from: https://cursor.sh

Or via Homebrew:

```bash
brew install --cask cursor
```

Launch Cursor and sign up for an account. The free tier includes AI features.

### Cursor Setup

1. Open Cursor → Settings (Cmd + ,)
2. Go to "Cursor Settings" → "Models"
3. You'll see options for different models (Claude, GPT-4, etc.)
4. Add your API keys if you have them, or use Cursor's included credits

---

## Step 7: Install Continue.dev Extension (for multi-LLM flexibility)

In Cursor:

1. Click Extensions icon (Cmd + Shift + X)
2. Search for "Continue"
3. Click Install

### Configure Continue

1. After installation, click the Continue icon in the sidebar
2. Click the gear icon to open config
3. Edit `~/.continue/config.json` to add multiple providers:

```json
{
  "models": [
    {
      "title": "Claude Sonnet 4",
      "provider": "anthropic",
      "model": "claude-sonnet-4-20250514",
      "apiKey": "YOUR_ANTHROPIC_KEY"
    },
    {
      "title": "GPT-4",
      "provider": "openai",
      "model": "gpt-4",
      "apiKey": "YOUR_OPENAI_KEY"
    }
  ],
  "tabAutocompleteModel": {
    "title": "Cursor Tab",
    "provider": "cursor",
    "model": "cursor-small"
  }
}
```

**Note:** Don't have API keys yet? Skip this for now - Cursor's built-in models work great to start.

---

## Step 8: Install Ollama (for local AI models)

```bash
brew install ollama
```

Start Ollama and download a model:

```bash
ollama serve &
ollama pull codellama
```

This gives you a local model that doesn't require internet or API keys.

---

## Step 9: Install Useful CLI Tools

```bash
# Modern git UI
brew install lazygit

# Better find
brew install fd

# Better grep
brew install ripgrep

# JSON processor (useful for working with APIs)
brew install jq
```

---

## Step 10: Create Your Workspace Directory

```bash
mkdir -p ~/Projects
cd ~/Projects
```

---

# Demo Project: AI-Powered Task Manager API

Now let's build a simple REST API to test everything!

## Project Setup

```bash
cd ~/Projects
mkdir ai-task-manager
cd ai-task-manager
npm init -y
npm install express
mkdir src tests
```

## Open Project in Cursor

```bash
cursor .
```

This opens Cursor in your project directory.

---

## Setting Up Your Multi-Window Workflow

### iTerm2 Window Layout

1. Open iTerm2
2. Create a new window (Cmd + T) 
3. Split vertically (Cmd + D)
4. Select top pane, split horizontally (Cmd + Shift + D)

You should now have 3 panes:

- **Top-left**: Development server
- **Top-right**: Tests
- **Bottom**: Git operations

---

## Building the API with AI Assistance

### Create src/app.js

In Cursor:

1. Press `Cmd + L` to open AI chat
2. Type: "Create a simple Express API with endpoints for creating, reading, updating, and deleting tasks. Each task should have an id, title, description, and completed status. Use an in-memory array for storage."

The AI will generate code similar to:

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.use(express.json());

let tasks = [];
let currentId = 1;

// Get all tasks
app.get('/tasks', (req, res) => {
  res.json(tasks);
});

// Get single task
app.get('/tasks/:id', (req, res) => {
  const task = tasks.find(t => t.id === parseInt(req.params.id));
  if (!task) return res.status(404).json({ error: 'Task not found' });
  res.json(task);
});

// Create task
app.post('/tasks', (req, res) => {
  const { title, description } = req.body;
  const task = {
    id: currentId++,
    title,
    description,
    completed: false
  };
  tasks.push(task);
  res.status(201).json(task);
});

// Update task
app.put('/tasks/:id', (req, res) => {
  const task = tasks.find(t => t.id === parseInt(req.params.id));
  if (!task) return res.status(404).json({ error: 'Task not found' });
  
  const { title, description, completed } = req.body;
  if (title !== undefined) task.title = title;
  if (description !== undefined) task.description = description;
  if (completed !== undefined) task.completed = completed;
  
  res.json(task);
});

// Delete task
app.delete('/tasks/:id', (req, res) => {
  const index = tasks.findIndex(t => t.id === parseInt(req.params.id));
  if (index === -1) return res.status(404).json({ error: 'Task not found' });
  
  tasks.splice(index, 1);
  res.status(204).send();
});

app.listen(port, () => {
  console.log(`Task API listening at http://localhost:${port}`);
});

module.exports = app;
```

---

### Create Tests with AI

Install Jest dependencies:

```bash
npm install --save-dev jest supertest
```

Update package.json scripts section:

```json
{
  "scripts": {
    "start": "node src/app.js",
    "test": "jest",
    "test:watch": "jest --watch"
  }
}
```

In Cursor, press `Cmd + L` and type: "Create Jest tests for this API covering all endpoints"

Create **tests/app.test.js** with the generated tests:

```javascript
const request = require('supertest');
const app = require('../src/app');

describe('Task API', () => {
  beforeEach(() => {
    // Reset tasks before each test
    app._router.stack.forEach((middleware) => {
      if (middleware.name === 'router') {
        middleware.handle.stack = middleware.handle.stack.filter(
          (layer) => layer.route
        );
      }
    });
  });

  test('GET /tasks returns empty array initially', async () => {
    const response = await request(app).get('/tasks');
    expect(response.status).toBe(200);
    expect(Array.isArray(response.body)).toBe(true);
  });

  test('POST /tasks creates a new task', async () => {
    const newTask = {
      title: 'Test Task',
      description: 'This is a test'
    };
    
    const response = await request(app)
      .post('/tasks')
      .send(newTask);
    
    expect(response.status).toBe(201);
    expect(response.body.title).toBe(newTask.title);
    expect(response.body.completed).toBe(false);
  });

  test('GET /tasks/:id returns specific task', async () => {
    // Create a task first
    const task = await request(app)
      .post('/tasks')
      .send({ title: 'Find Me', description: 'Test' });
    
    const response = await request(app).get(`/tasks/${task.body.id}`);
    expect(response.status).toBe(200);
    expect(response.body.title).toBe('Find Me');
  });
});
```

---

## Testing Your Multi-Window Workflow

### iTerm2 - Top-left pane (Development Server)

```bash
cd ~/Projects/ai-task-manager
npm start
```

### iTerm2 - Top-right pane (Test Watcher)

```bash
cd ~/Projects/ai-task-manager
npm test:watch
```

### iTerm2 - Bottom pane (Git & Deployment)

```bash
cd ~/Projects/ai-task-manager
git init
git add .
```

Now ask Cursor AI to write your commit message:

- Select all files in Cursor
- Press `Cmd + L`
- Type: "Write a conventional commit message for this initial project setup"
- It will suggest something like: `feat: initial task manager API with CRUD operations`

Complete the commit:

```bash
git commit -m "feat: initial task manager API with CRUD operations

- Express server with REST endpoints
- In-memory task storage
- Jest test suite with full coverage"
```

---

## Test the API Manually

In a new terminal or use the bottom pane:

```bash
# Create a task
curl -X POST http://localhost:3000/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Learn AI Development","description":"Set up environment"}'

# Get all tasks
curl http://localhost:3000/tasks

# Update a task
curl -X PUT http://localhost:3000/tasks/1 \
  -H "Content-Type: application/json" \
  -d '{"completed":true}'

# Delete a task
curl -X DELETE http://localhost:3000/tasks/1
```

---

## Using AI Throughout Development

### Cursor AI Workflows

1. **Inline editing** - Highlight code, press `Cmd + K`, ask: "Add input validation"

2. **Chat for refactoring** - Press `Cmd + L`, ask: "How can I add database persistence to this?"

3. **Generate deployment script** - Create new file `deploy.sh`, press `Cmd + K`, ask: "Create a deployment script for AWS EC2"

4. **Documentation** - Select your app.js, press `Cmd + L`: "Generate API documentation in Markdown"

---

## tmux Bonus: Save Your Layout

Create a tmux session with your preferred layout:

```bash
# Start tmux
tmux new -s dev

# Split into 3 panes
# Ctrl-a | (vertical split)
# Ctrl-a - (horizontal split on right)

# In each pane, navigate to your project
cd ~/Projects/ai-task-manager

# Pane 1: npm start
# Pane 2: npm test:watch
# Pane 3: git status
```

### Create Auto-Launch Script

Save this session as a reusable script:

```bash
cat > ~/start-dev.sh << 'EOF'
#!/bin/bash
tmux new-session -d -s dev -n main
tmux send-keys -t dev:main 'cd ~/Projects/ai-task-manager && npm start' C-m
tmux split-window -h -t dev:main
tmux send-keys -t dev:main 'cd ~/Projects/ai-task-manager && npm test:watch' C-m
tmux split-window -v -t dev:main.0
tmux send-keys -t dev:main 'cd ~/Projects/ai-task-manager && git status' C-m
tmux attach -t dev
EOF

chmod +x ~/start-dev.sh
```

Now anytime you want to start development:

```bash
~/start-dev.sh
```

---

## Recommended Window Layout Diagram

```
┌─────────────────────────┬──────────────────────┐
│                         │                      │
│  Cursor IDE             │  AI Chat Panel       │
│  (Code Editor)          │  (Cmd + L)           │
│                         │                      │
│  - Main editing         │  - Ask questions     │
│  - File explorer        │  - Code generation   │
│  - Inline completions   │  - Refactoring help  │
│                         │                      │
└─────────────────────────┴──────────────────────┘

┌─────────────────────────┬──────────────────────┐
│  iTerm2 - Server        │  iTerm2 - Tests      │
│  npm start              │  npm test:watch      │
│                         │                      │
├─────────────────────────┴──────────────────────┤
│  iTerm2 - Git/Deploy                           │
│  git status, commits, deployment               │
└────────────────────────────────────────────────┘
```

---

## Quick Reference: Keyboard Shortcuts

### Cursor

- `Cmd + L` - Open AI chat
- `Cmd + K` - Inline AI edit
- `Cmd + Shift + P` - Command palette
- `Cmd + P` - Quick file open
- `Cmd + B` - Toggle sidebar

### iTerm2

- `Cmd + D` - Split pane vertically
- `Cmd + Shift + D` - Split pane horizontally
- `Cmd + [` or `Cmd + ]` - Navigate panes
- `Cmd + T` - New tab
- `Cmd + W` - Close pane

### tmux (with custom config)

- `Ctrl + A` then `|` - Split vertically
- `Ctrl + A` then `-` - Split horizontally
- `Ctrl + A` then arrow keys - Navigate panes
- `Ctrl + A` then `d` - Detach session
- `tmux attach` - Reattach to session

---

## Multi-LLM Strategy

### When to Use Each Model

- **Claude Sonnet 4** - Complex reasoning, refactoring, architecture decisions
- **GPT-4o** - Fast responses, quick questions, general coding
- **Local models (Ollama)** - Privacy-sensitive code, offline work
- **Claude Opus 4** - Hardest problems (costs more, use sparingly)

### Switching Models in Cursor

1. Open Settings (Cmd + ,)
2. Go to "Cursor Settings" → "Models"
3. Select your preferred model for chat and autocomplete

### Using Continue.dev for Multi-LLM

1. Click Continue icon in sidebar
2. Use dropdown to switch between configured models
3. Compare responses from different models for complex problems

---

## What You Now Have

- ✅ AI-powered IDE (Cursor)
- ✅ Multiple terminal panes (iTerm2 + tmux)
- ✅ Multi-LLM capability (Continue.dev + Cursor)
- ✅ Working demo project
- ✅ Automated workflow setup
- ✅ Git integration
- ✅ Test-driven development workflow

---

## Next Steps & Experiments

1. **Switch AI models** - Try different models in Cursor settings and compare results
2. **Use Continue.dev** - Install and configure multiple LLM providers
3. **Create complex tmux layouts** - Design custom layouts for different project types
4. **Add database integration** - Ask AI to help add PostgreSQL or MongoDB
5. **Dockerize** - Have AI generate Dockerfile and docker-compose.yml
6. **CI/CD Pipeline** - Generate GitHub Actions workflows with AI
7. **API Documentation** - Use AI to generate OpenAPI/Swagger specs

---

## Troubleshooting

### Homebrew Installation Issues

If Homebrew commands aren't found after installation, add to your PATH:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

### Cursor AI Not Responding

- Check internet connection
- Verify you're signed in to Cursor account
- Check if you have API credits remaining
- Try restarting Cursor

### tmux Not Working

If tmux keybindings don't work, ensure your config loaded:

```bash
tmux source-file ~/.tmux.conf
```

### Port Already in Use

If port 3000 is taken:

```bash
# Find process using port 3000
lsof -i :3000

# Kill the process
kill -9 <PID>
```

---

## Additional Resources

- [Cursor Documentation](https://cursor.sh/docs)
- [Continue.dev Docs](https://continue.dev/docs)
- [tmux Cheat Sheet](https://tmuxcheatsheet.com/)
- [Ollama Model Library](https://ollama.ai/library)
- [Anthropic Claude API](https://docs.anthropic.com/)

---

## Support

For issues or questions:

- Cursor: https://cursor.sh/support
- Continue.dev: https://github.com/continuedev/continue
- tmux: https://github.com/tmux/tmux/wiki

---

*Last Updated: December 2025*