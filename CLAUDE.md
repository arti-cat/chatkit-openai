# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 🎯 Template Overview

This is a **production-ready ChatKit-OpenAI template** for building conversational AI applications with OpenAI's Agents SDK and ChatKit framework.

**Use as template**: Clone this repository to start new client projects with a proven foundation.
**Setup time**: ~10 minutes for new project (see [TEMPLATE_SETUP.md](TEMPLATE_SETUP.md))

---

## Quick Template Setup

**For New Client Projects**:

1. Clone and customize project name
2. Set environment variables (OPENAI_API_KEY, VITE_CHATKIT_API_DOMAIN_KEY)
3. Run `npm start`
4. Validate with `/chatkit:validate-config` command
5. Start building with `/chatkit:add-tool` or `/chatkit:full-stack-feature`

**Detailed setup**: See [TEMPLATE_SETUP.md](TEMPLATE_SETUP.md)
**Customization guide**: See [TEMPLATE_CUSTOMIZATION.md](TEMPLATE_CUSTOMIZATION.md)

---

## Technology Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| Backend Framework | FastAPI | Latest |
| Backend AI | OpenAI Agents SDK | Latest |
| Backend Chat | ChatKit Python SDK | Latest |
| Frontend Framework | React | 19 |
| Frontend Build | Vite | 7 |
| Frontend Chat | ChatKit React SDK | Latest |
| Styling | Tailwind CSS | Latest |
| Python | 3.11+ | Required |
| Node.js | 20+ | Required |
| Package Management | uv (Python), npm (JS) | - |

---

## Project Structure

```
.
├── backend/              # Base template FastAPI backend
│   └── app/
│       ├── main.py       # FastAPI entrypoint with /chatkit endpoint
│       ├── chat.py       # ChatKit server integration with agent tools
│       ├── facts.py      # In-memory fact storage
│       ├── constants.py  # System instructions and model config ⚙️ CUSTOMIZE
│       ├── weather.py    # Weather API integration (example tool)
│       └── sample_widget.py  # Weather widget renderer (example)
├── frontend/             # Base template React frontend
│   └── src/
│       ├── main.tsx      # React entry point
│       ├── lib/config.ts # ChatKit configuration ⚙️ CUSTOMIZE
│       └── components/
│           └── ChatKitPanel.tsx  # ChatKit component with client tool handlers
├── .claude/              # Claude Code infrastructure (agents, commands, hooks)
│   ├── agents/chatkit/   # ChatKit-specific agents
│   ├── commands/chatkit/ # ChatKit workflow commands
│   └── hooks/            # React/Vite validation hooks
└── examples/             # Three complete reference applications
    ├── customer-support/     # Airline support workflow (port 8001/5171)
    ├── knowledge-assistant/  # File Search-backed assistant (port 8002/5172)
    └── marketing-assets/     # Marketing creative workflow (port 8003/5173)
```

**⚙️ CUSTOMIZE**: Files requiring customization for each client project

---

## Template Architecture

### Backend Pattern

```
FastAPI App → ChatKitServer → Agent with Tools
                                    ↓
                        @function_tool decorators
                                    ↓
                Widget streaming OR Client tool calls
```

**Key Components**:
- **FastAPI endpoint** (`/chatkit`): Receives chat requests
- **ChatKitServer**: Bridges OpenAI Agents SDK with ChatKit protocol
- **Agent**: OpenAI agent with model, instructions, and custom tools
- **Tools**: Python functions decorated with `@function_tool`
- **Widgets**: Rich UI components streamed to frontend
- **Client Tools**: Backend-triggered frontend actions

### Frontend Pattern

```
React App → <ChatKit> component → onClientToolCall handler
                                         ↓
                                   UI updates
```

**Key Components**:
- **ChatKit component**: OpenAI's React component for chat interface
- **Configuration**: API domain key, backend URL, theme
- **Client tool handlers**: Frontend functions triggered by backend
- **Vite proxy**: Routes `/chatkit` requests to backend

### Full Flow

```
User message → Frontend → /chatkit endpoint → Backend
                                                ↓
                                            Agent processes
                                                ↓
                                        Tool execution
                                                ↓
                    Widget (displays in chat) ← Tool returns widget
                                OR
                ClientToolCall → Frontend handler → UI update
```

---

## Development Commands

### Starting Development

```bash
# Both backend (8000) and frontend (5170)
npm start

# Backend only
npm run backend

# Frontend only
npm run frontend
```

### Running Example Applications

```bash
# Customer support example
cd examples/customer-support && npm start  # 8001/5171

# Knowledge assistant example
cd examples/knowledge-assistant && npm start  # 8002/5172

# Marketing assets example
cd examples/marketing-assets && npm start  # 8003/5173
```

### Backend Development

```bash
# Type checking
cd backend && uv run mypy app/

# Linting and formatting
cd backend && uv run ruff check .
cd backend && uv run ruff format .
```

### Frontend Development

```bash
# Linting
cd frontend && npm run lint

# Production build
cd frontend && npm run build
cd frontend && npm run preview
```

---

## Template Features

### Out-of-Box Tooling

**Specialized Agents** (`.claude/agents/chatkit/`):
- **chatkit-server-expert**: Backend tool implementation, widget rendering
- **chatkit-frontend-expert**: React integration, client tool handlers

**Workflow Commands** (`.claude/commands/chatkit/`):
- **`/chatkit:add-tool`**: Guided tool scaffolding (saves ~30-40 min/tool)
- **`/chatkit:validate-config`**: Environment and configuration validation
- **`/chatkit:full-stack-feature`**: Orchestrated end-to-end implementation (40-60% faster)

**Multi-Perspective Analysis** (`.claude/commands/`):
- **`/multi-role agent1,agent2 --agent`**: Parallel agent execution (50-65% time savings)

**Validation Hooks** (`.claude/hooks/`):
- TypeScript type checking (PostToolUse)
- Python linting with ruff (PostToolUse)
- Code formatting with Prettier (PostToolUse)
- Bash command validation (PreToolUse)

### Example Applications

Three complete reference implementations:

1. **customer-support**: Airline workflow with seat changes, cancellations, real-time UI sync
2. **knowledge-assistant**: File search integration, document-backed responses
3. **marketing-assets**: Creative workflow, asset management, approval flows

---

## Development Workflow

### Adding a New Agent Tool

```bash
/chatkit:add-tool
```

Claude will guide through:
1. Tool type (widget/client/data)
2. Backend `@function_tool` generation
3. Widget renderer (if needed)
4. Frontend handler (if client tool)
5. Registration and testing

**Time saved**: ~30-40 minutes per tool

### Adding Complete Feature

```bash
/chatkit:full-stack-feature
```

Orchestrates:
- Planning with `agent-organizer`
- Backend with `chatkit-server-expert`
- Frontend with `chatkit-frontend-expert`
- Testing with `test-engineer`
- Documentation with `documentation-expert`

**Time saved**: 40-60% faster than manual implementation

### Multi-Perspective Review

```bash
/multi-role python-pro,typescript-pro --agent
```

Get parallel analysis from multiple expert agents with synthesized recommendations.

**Effective combinations**:
- Full-stack: `chatkit-server-expert,chatkit-frontend-expert`
- Security: `security-auditor,performance-engineer`
- Backend: `python-pro,backend-architect`
- Frontend: `react-pro,typescript-pro`

**Time saved**: 50-65% for complex analysis

### Configuration Validation

```bash
/chatkit:validate-config
```

Validates:
- Environment variables (OPENAI_API_KEY, domain keys)
- Port configuration (no conflicts)
- Domain allowlisting (production)
- Proxy configuration
- Endpoint health

**Time saved**: ~15-20 minutes troubleshooting

---

## Template Customization Points

### Essential (Required for Each Client)

- [ ] **Project name**: Search/replace "chatkit-openai" → "{CLIENT_NAME}"
- [ ] **Environment variables**: Set OPENAI_API_KEY, VITE_CHATKIT_API_DOMAIN_KEY
- [ ] **System instructions**: Customize `backend/app/constants.py` (agent personality)
- [ ] **Domain allowlist**: Add production domains to `frontend/vite.config.ts`

### Common Customizations

- [ ] **Agent tools**: Add domain-specific tools in `backend/app/`
- [ ] **UI theme**: Modify `frontend/src/styles/` or ChatKit props
- [ ] **Port numbers**: Change if conflicts exist
- [ ] **Authentication**: Add auth layer as needed

### Optional (Template Includes Examples)

- [ ] **Widget types**: Extend `sample_widget.py` patterns
- [ ] **Client tools**: Add frontend interactions
- [ ] **Example apps**: Study `examples/` for patterns

**Detailed guide**: See [TEMPLATE_CUSTOMIZATION.md](TEMPLATE_CUSTOMIZATION.md)

---

## Key Template Files

### Backend (Customize for Each Client)

- **`backend/app/constants.py`**: System instructions, model config, agent personality
- **`backend/app/chat.py`**: ChatKitServer, agent initialization, tool registration
- **`backend/app/*.py`**: Custom tool implementations

**Tool Pattern**:
```python
@function_tool(description_override="Tool description")
async def my_tool(
    ctx: RunContextWrapper[FactAgentContext],
    param: str,
) -> dict[str, str]:
    # Tool logic
    result = perform_operation(param)

    # Stream widget (optional)
    await ctx.context.stream_widget(widget_data, copy_text="...")

    # OR trigger client action (optional)
    ctx.context.client_tool_call = ClientToolCall(
        name="client_action",
        arguments={"key": "value"}
    )

    return {"result": result}
```

### Frontend (Customize for Each Client)

- **`frontend/src/lib/config.ts`**: ChatKit configuration, API domain key
- **`frontend/src/components/ChatKitPanel.tsx`**: Client tool handlers
- **`frontend/vite.config.ts`**: Proxy configuration, domain allowlist

**Client Tool Pattern**:
```typescript
onClientToolCall={(call: ClientToolCall) => {
  if (call.name === "client_action") {
    const { key } = call.arguments;
    performAction(key);
  }
  return {
    type: "client_tool_call" as const,
    tool_call_id: call.tool_call_id,
    result: { success: true },
  };
}}
```

### Template Infrastructure (Reuse As-Is)

- **`.claude/agents/`**: 90 agents including ChatKit specialists
- **`.claude/commands/`**: 196 commands including ChatKit workflows
- **`.claude/hooks/`**: Validation hooks for React/Vite + ChatKit

---

## Environment Variables

### Backend

**Required**:
- `OPENAI_API_KEY`: OpenAI API key for agent responses

**Optional**:
- `BACKEND_PORT`: Override default 8000

### Frontend

**Required for Production**:
- `VITE_CHATKIT_API_DOMAIN_KEY`: Domain key from [OpenAI allowlist](https://platform.openai.com/settings/organization/security/domain-allowlist)

**Optional**:
- `VITE_BACKEND_URL`: Override backend URL (default: http://127.0.0.1:8000)

**Local Development**:
- Any non-empty string works for `VITE_CHATKIT_API_DOMAIN_KEY`

**Production**:
1. Register domain at OpenAI allowlist
2. Use generated domain key
3. Add domain to `server.allowedHosts` in `vite.config.ts`

---

## Claude Code Infrastructure

### ChatKit-Specific Agents (Use PROACTIVELY)

**chatkit-server-expert**:
- Use when: Implementing backend agent tools
- Use when: Creating widget renderers
- Use when: Debugging server-side ChatKit integration
- Expertise: @function_tool patterns, widget streaming, OpenAI Agents SDK

**chatkit-frontend-expert**:
- Use when: Adding client tool handlers
- Use when: Configuring ChatKit React component
- Use when: Setting up domain allowlisting
- Expertise: React integration, onClientToolCall, Vite proxy

### ChatKit Commands (Template Workflows)

**`/chatkit:add-tool`**: Guided tool scaffolding
- Handles widget/client/data tool types
- Auto-generates backend + frontend code
- Registers tools automatically
- Provides test instructions
- **Saves ~30-40 minutes per tool**

**`/chatkit:validate-config`**: Environment validation
- Checks API keys, domain allowlist, ports
- Validates proxy configuration
- Tests endpoint health
- **Saves ~15-20 minutes troubleshooting**

**`/chatkit:full-stack-feature`**: Orchestrated implementation
- Coordinates multiple specialized agents
- Implements backend + frontend + tests + docs
- Validates integration between components
- **40-60% faster than manual implementation**

### Multi-Role Analysis (Advanced)

**`/multi-role agent1,agent2 --agent`**: Multi-perspective review
- Parallel execution with `--agent` flag
- Synthesized recommendations from diverse expertise
- Explicit trade-off analysis
- **50-65% time reduction for complex decisions**

**Effective combinations**:
- ChatKit full-stack: `chatkit-server-expert,chatkit-frontend-expert`
- Security review: `security-auditor,performance-engineer`
- Backend quality: `python-pro,backend-architect,test-engineer`
- Frontend optimization: `react-pro,typescript-pro,performance-auditor`

### Complete Documentation

- **Agents**: `.claude/agents/README.md` - Complete agent reference with workflows
- **Commands**: `.claude/commands/README.md` - Command namespace guide
- **ChatKit Commands**: `.claude/commands/chatkit/README.md` - Workflow details
- **Hooks**: `.claude/hooks/react-vite-hooks.md` - Hook system documentation
- **Multi-Role**: `.claude/commands/multi-role.md` - Multi-perspective analysis guide

---

## Template Benefits

### For Client Projects

✅ **Fast setup**: 10 minutes to working ChatKit application
✅ **Proven patterns**: Backend tools, widgets, client tools pre-configured
✅ **Quality tooling**: Agents, commands, hooks included
✅ **Examples included**: 3 complete reference implementations

### For Development Teams

✅ **Consistent structure**: Same patterns across all client projects
✅ **Accelerated development**: ChatKit commands save 40-60% time
✅ **Built-in quality**: Validation hooks catch issues early
✅ **Multi-perspective reviews**: /multi-role for complex decisions

### For Maintenance

✅ **Self-documenting**: Claude Code infrastructure guides development
✅ **Clear patterns**: New team members onboard via examples
✅ **Testable**: Validation commands ensure consistency

---

## Template Testing

Before using this template for a client project:

1. **Validate configuration**: Run `/chatkit:validate-config`
2. **Test base template**: `npm start`, interact with chat
3. **Test tool addition**: `/chatkit:add-tool`, add simple tool
4. **Test examples**: Run each example application
5. **Review hooks**: Verify `.claude/hooks/` validation works

---

## Widget System

Widgets are rich UI components rendered in the ChatKit interface:

**Backend streaming**:
```python
await ctx.context.stream_widget(widget_data, copy_text="...")
```

**Widget structure** (see `backend/app/sample_widget.py`):
```python
{
    "type": "widget",
    "widget": {
        "type": "sections",
        "title": "Widget Title",
        "sections": [
            {
                "type": "table",
                "rows": [{"key": "Label", "value": "Value"}]
            }
        ]
    }
}
```

**Supported section types**: table, metadata, image

---

## Support & Documentation

### Template Questions

- **Architecture**: See "Template Architecture" section above
- **Setup issues**: Run `/chatkit:validate-config`
- **Tool patterns**: Study `examples/` directory
- **Agent usage**: See `.claude/agents/README.md`

### Development Help

- **Backend issues**: Use `chatkit-server-expert` agent
- **Frontend issues**: Use `chatkit-frontend-expert` agent
- **Complex decisions**: Use `/multi-role` command
- **Quick validation**: Use `/chatkit:validate-config`

---

## Template Versioning

**Current Version**: 1.0.0 (2025-10-25)

**Includes**:
- ChatKit Python SDK + OpenAI Agents SDK integration
- ChatKit React SDK + Vite configuration
- 2 ChatKit-specific agents (server + frontend)
- 3 ChatKit workflow commands (add-tool, validate-config, full-stack-feature)
- Multi-role analysis command
- React/Vite + ChatKit validation hooks
- 3 complete example applications
- 90 general-purpose agents (88 base + 2 ChatKit)
- 196 workflow commands (193 base + 3 ChatKit)

---

## Next Steps for New Client Project

1. **Clone this template repository**
2. **Customize** (see [TEMPLATE_CUSTOMIZATION.md](TEMPLATE_CUSTOMIZATION.md))
3. **Validate** with `/chatkit:validate-config`
4. **Start building** with `/chatkit:add-tool` or `/chatkit:full-stack-feature`
5. **Reference examples** for patterns
6. **Use agents/commands proactively** during development

---

**This template provides a production-ready foundation for conversational AI applications with ChatKit + OpenAI Agents SDK.**

For detailed setup instructions, see [TEMPLATE_SETUP.md](TEMPLATE_SETUP.md).
