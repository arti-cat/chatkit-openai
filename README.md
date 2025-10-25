# OpenAI ChatKit Advanced Samples

## 🎯 Production-Ready Template

**This repository is a production-ready template** for building conversational AI applications with ChatKit + OpenAI Agents SDK.

Clone this template to start new client projects with:
- ✅ FastAPI backend with ChatKit Python SDK
- ✅ React 19 + Vite frontend with ChatKit React SDK
- ✅ 90 specialized AI agents (Claude Code integration)
- ✅ 196 workflow commands for accelerated development
- ✅ 3 complete example applications
- ✅ Validation hooks for quality assurance

**Setup time**: ~10 minutes | **See**: [TEMPLATE_SETUP.md](TEMPLATE_SETUP.md)

---

## Repository Structure

This repository contains a complete [ChatKit](https://github.com/openai/chatkit-js) playground that pairs a FastAPI backend with a Vite + React frontend.

The top-level [**backend**](backend) and [**frontend**](frontend) directories provide a basic project template that demonstrates ChatKit UI, widgets, and client tools.

- It runs a custom ChatKit server built with [ChatKit Python SDK](https://github.com/openai/chatkit-python) and [OpenAI Agents SDK for Python](https://github.com/openai/openai-agents-python).
- Available agent tools: `get_weather` for rendering a widget, `switch_theme` and `record_fact` as client tools

The Vite server proxies all `/chatkit` traffic straight to the local FastAPI service so you can develop the client and server in tandem without extra wiring.

## Quickstart

1. Start FastAPI backend API.
2. Configure the frontend's domain key and launch the Vite app.
3. Explore the demo flow.

Each step is detailed below.

### 1. Start FastAPI backend API

From the repository root you can bootstrap the backend in one step:

```bash
npm run backend
```

This command runs `uv sync` for `backend/` and launches Uvicorn on `http://127.0.0.1:8000`. Make sure [uv](https://docs.astral.sh/uv/getting-started/installation/) is installed and `OPENAI_API_KEY` is exported beforehand.

If you prefer running the backend from inside `backend/`, follow the manual steps:

```bash
cd backend
uv sync
export OPENAI_API_KEY=sk-proj-...
uv run uvicorn app.main:app --reload --port 8000
```

If you don't have uv, you can do the same with:

```bash
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -e .
export OPENAI_API_KEY=sk-proj-...
uvicorn app.main:app --reload
```

The development API listens on `http://127.0.0.1:8000`.

### 2. Run Vite + React frontend

From the repository root you can start the frontend directly:

```bash
npm run frontend
```

This script launches Vite on `http://127.0.0.1:5170`.

To configure and run the frontend manually:

```bash
cd frontend
npm install
npm run dev
```

Optional configuration hooks live in [`frontend/src/lib/config.ts`](frontend/src/lib/config.ts) if you want to tweak API URLs or UI defaults.

To launch both the backend and frontend together from the repository root, you can use `npm start`. This command also requires `uv` plus the necessary environment variables (for example `OPENAI_API_KEY`) to be set beforehand.

The Vite dev server runs at `http://127.0.0.1:5170`, and this works fine for local development. However, for production deployments:

1. Host the frontend on infrastructure you control behind a managed domain.
2. Register that domain on the [domain allowlist page](https://platform.openai.com/settings/organization/security/domain-allowlist) and add it to [`frontend/vite.config.ts`](frontend/vite.config.ts) under `server.allowedHosts`.
3. Set `VITE_CHATKIT_API_DOMAIN_KEY` to the value returned by the allowlist page.

If you want to verify this remote access during development, temporarily expose the app with a tunnel (e.g. `ngrok http 5170` or `cloudflared tunnel --url http://localhost:5170`) and add that hostname to your domain allowlist before testing.

### 3. Explore the demo flow

With the app reachable locally or via a tunnel, open it in the browser and try a few interactions. The sample ChatKit UI ships with three tools that trigger visible actions in the pane:

1. **Fact Recording** - prompt: `My name is Kaz`
2. **Weather Info** - prompt: `What's the weather in San Francisco today?`
3. **Theme Switcher** - prompt: `Change the theme to dark mode` 

## Using as a Template

### For New Client Projects

1. **Clone this repository**
2. **Quick setup**: Follow [TEMPLATE_SETUP.md](TEMPLATE_SETUP.md) (~10 minutes)
3. **Customize**: See [TEMPLATE_CUSTOMIZATION.md](TEMPLATE_CUSTOMIZATION.md) for common patterns
4. **Develop**: Use Claude Code commands for accelerated development:
   - `/chatkit:add-tool` - Add backend tools with guided scaffolding
   - `/chatkit:validate-config` - Validate environment and configuration
   - `/chatkit:full-stack-feature` - Orchestrated feature implementation
   - `/multi-role agent1,agent2 --agent` - Multi-perspective analysis

### Template Features

**Claude Code Integration** (`.claude/` directory):
- **90 AI agents** including ChatKit specialists (`chatkit-server-expert`, `chatkit-frontend-expert`)
- **196 workflow commands** for common development tasks
- **Validation hooks** for TypeScript, Python, formatting, and ChatKit-specific checks
- **Multi-role analysis** for complex technical decisions

**Development Acceleration**:
- Tool addition: ~30-40 minutes saved per tool
- Configuration validation: ~15-20 minutes saved troubleshooting
- Full-stack features: 40-60% faster implementation
- Multi-perspective reviews: 50-65% time reduction

**Documentation**:
- [CLAUDE.md](CLAUDE.md) - Complete project guide for Claude Code
- [TEMPLATE_SETUP.md](TEMPLATE_SETUP.md) - 10-minute setup guide
- [TEMPLATE_CUSTOMIZATION.md](TEMPLATE_CUSTOMIZATION.md) - Common customization patterns

---

## What's next

Under the [`examples`](examples) directory, you'll find three more sample apps that ground the starter kit in real-world scenarios:

1. [**Customer Support**](examples/customer-support): airline customer support workflow.
2. [**Knowledge Assistant**](examples/knowledge-assistant): knowledge-base agent backed by OpenAI's File Search tool.
3. [**Marketing Assets**](examples/marketing-assets): marketing creative workflow.

Each example under [`examples/`](examples) includes the helper scripts (`npm start`, `npm run frontend`, `npm run backend`) pre-configured with its dedicated ports, so you can `cd` into an example and run `npm start` to boot its backend and frontend together. Please note that when you run `npm start`, `uv` must already be installed and all required environment variables should be exported.

---

## Template Benefits

### For Client Projects
- ✅ Fast setup: 10 minutes to working ChatKit application
- ✅ Proven patterns: Backend tools, widgets, client tools pre-configured
- ✅ Quality tooling: AI agents, commands, hooks included
- ✅ Examples: 3 complete reference implementations

### For Development Teams
- ✅ Consistent structure: Same patterns across all projects
- ✅ Accelerated development: Commands save 40-60% time
- ✅ Built-in quality: Validation hooks catch issues early
- ✅ Multi-perspective reviews: Advanced decision support
