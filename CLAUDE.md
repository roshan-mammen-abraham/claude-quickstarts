# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a monorepo containing independent Claude API quickstart projects. Each subdirectory is a standalone project with its own stack and dependencies. Projects do not share code between each other.

## Legal

When changes are made to files that have a copyright notice, add them to that subdirectory's CHANGELOG.md file.

## Projects

### computer-use-demo (Python/Docker)

Desktop control via Claude with VNC interface.

```bash
cd computer-use-demo
./setup.sh                                    # Setup environment
docker build . -t computer-use-demo:local     # Build
docker run -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  -v $(pwd)/computer_use_demo:/home/computeruse/computer_use_demo/ \
  -v $HOME/.anthropic:/home/computeruse/.anthropic \
  -p 5900:5900 -p 8501:8501 -p 6080:6080 -p 8080:8080 \
  -it computer-use-demo:local                 # Run
ruff check . && ruff format .                 # Lint/format
pyright                                       # Typecheck
pytest                                        # Test all
pytest tests/path_to_test.py::test_name -v   # Test single
```

Architecture: `loop.py` (API sampling loop) → `tools/` (computer, bash, edit tools) → `streamlit.py` (chat UI)

### browser-use-demo (Python/Docker/Playwright)

Browser automation with DOM-based element targeting.

```bash
cd browser-use-demo
cp .env.example .env                          # Configure API key
docker-compose up --build                     # Run (production)
docker-compose up --build --watch             # Run (dev with file watching)
```

Ports: 8080 (Streamlit), 6080 (NoVNC), 5900 (VNC)

Architecture: `browser_use_demo/tools/browser.py` (main tool) → `loop.py` (API loop) → `streamlit.py` (UI). JavaScript utilities in `browser_tool_utils/` handle DOM extraction and element refs.

### customer-support-agent (Next.js/TypeScript)

RAG-powered support chat with Amazon Bedrock Knowledge Base.

```bash
cd customer-support-agent
npm install
npm run dev                                   # Full UI
npm run dev:left                              # Left sidebar only
npm run dev:right                             # Right sidebar only
npm run dev:chat                              # Chat only
npm run lint
npm run build
```

### financial-data-analyst (Next.js/TypeScript)

Chat-based data analysis with dynamic chart generation using Recharts.

```bash
cd financial-data-analyst
npm install
npm run dev
npm run lint
npm run build
```

### agents (Python)

Minimal educational agent implementation (<300 lines). Demonstrates tool loops and MCP integration.

```bash
cd agents
# Requires: anthropic, mcp libraries
python -c "from agents.agent import Agent; ..."
```

Architecture: `agent.py` (core loop) → `tools/` (ThinkTool, file tools, web search, MCP tools) → `utils/` (history, MCP connections)

### autonomous-coding (Python/Claude Agent SDK)

Multi-session autonomous coding with two-agent pattern.

```bash
cd autonomous-coding
npm install -g @anthropic-ai/claude-code      # Install Claude Code CLI
pip install -r requirements.txt
python autonomous_agent_demo.py --project-dir ./my_project
python autonomous_agent_demo.py --project-dir ./my_project --max-iterations 3  # Limited run
```

Architecture: Two-agent pattern where initializer creates `feature_list.json`, then coding agent implements features across sessions. Progress persisted via git. Security enforced in `security.py` with bash command allowlist.

## Code Style

**Python projects**: snake_case functions/variables, PascalCase classes, type annotations required, custom ToolError for tool errors, dataclasses and abstract base classes

**TypeScript projects**: Strict mode, function components with React hooks, shadcn/ui components, ESLint Next.js configuration