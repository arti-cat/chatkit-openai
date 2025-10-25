# ChatKit Commands Namespace

Specialized commands for ChatKit-OpenAI project development, focusing on rapid feature implementation, configuration validation, and full-stack workflows.

## Available Commands

### `/chatkit:add-tool`
**Purpose:** Guided scaffolding for adding new ChatKit agent tools

**Use when:**
- Adding a new backend tool with @function_tool decorator
- Creating widget-based features
- Implementing client tool calls
- Need step-by-step guidance through the process

**What it does:**
1. Identifies tool type (widget/client/data)
2. Generates backend tool implementation
3. Creates widget renderer (if needed)
4. Adds frontend handler (if client tool)
5. Registers tool in chat.py
6. Provides testing instructions

**Time saved:** ~30-40 minutes per tool

**See:** [add-tool.md](add-tool.md)

---

### `/chatkit:validate-config`
**Purpose:** Comprehensive validation of ChatKit application configuration

**Use when:**
- Initial project setup
- Before deployment
- Troubleshooting connection issues
- After configuration changes

**What it validates:**
1. Environment variables (OPENAI_API_KEY, VITE_CHATKIT_API_DOMAIN_KEY)
2. Domain allowlisting configuration
3. Port conflicts (8000/5170 + examples)
4. Proxy configuration
5. Endpoint health (if servers running)

**Time saved:** ~15-20 minutes of manual checking

**See:** [validate-config.md](validate-config.md)

---

### `/chatkit:full-stack-feature`
**Purpose:** Orchestrated end-to-end feature implementation using specialized agents

**Use when:**
- Building complex features spanning multiple components
- Need comprehensive testing and documentation
- Want structured agent coordination
- Implementing novel functionality

**What it does:**
1. Uses agent-organizer to create implementation plan
2. Coordinates chatkit-server-expert for backend
3. Coordinates chatkit-frontend-expert for frontend
4. Coordinates test-engineer for testing
5. Coordinates documentation-expert for docs
6. Validates integration between all components

**Time saved:** ~40-60% faster than manual implementation

**See:** [full-stack-feature.md](full-stack-feature.md)

---

## Quick Start

### Adding a Simple Tool

```bash
/chatkit:add-tool
```

Example conversation:
```
Claude: What type of tool would you like to add?
User: A weather lookup tool that displays a widget

Claude: [Creates backend/app/weather.py with @function_tool]
       [Creates backend/app/weather_widget.py]
       [Registers in backend/app/chat.py]
       [Provides test instructions]
```

### Validating Configuration

```bash
/chatkit:validate-config
```

Output example:
```
✅ ChatKit Configuration Validation

✅ OPENAI_API_KEY set and valid
✅ VITE_CHATKIT_API_DOMAIN_KEY set
✅ Ports: Backend 8000, Frontend 5170 (no conflicts)
✅ Proxy configured correctly
⚠️  Servers not running (start with: npm start)

Overall: ✅ Ready for development
```

### Building a Complete Feature

```bash
/chatkit:full-stack-feature
```

Example conversation:
```
User: Add a user profile lookup feature with avatar and recent activity

Claude: [agent-organizer creates plan]
        [chatkit-server-expert implements backend]
        [chatkit-server-expert creates widget renderer]
        [test-engineer generates tests]
        [documentation-expert updates README]

        ✅ Feature complete with full test coverage
```

---

## Command Selection Guide

### Decision Tree

```
What do you need?
├─ Quick tool addition
│  └─ Similar to existing tools → /chatkit:add-tool
│
├─ Configuration check
│  ├─ Initial setup → /chatkit:validate-config
│  ├─ Pre-deployment → /chatkit:validate-config
│  └─ Troubleshooting → /chatkit:validate-config
│
└─ Complete feature
   ├─ Complex, multi-component → /chatkit:full-stack-feature
   ├─ Novel functionality → /chatkit:full-stack-feature
   └─ Need tests + docs → /chatkit:full-stack-feature
```

### By Task Type

| Task | Command | Agents Used | Time |
|------|---------|-------------|------|
| Add weather widget | `/chatkit:add-tool` | chatkit-server-expert | ~5 min |
| Add theme switcher | `/chatkit:add-tool` | chatkit-server-expert, chatkit-frontend-expert | ~7 min |
| Validate env vars | `/chatkit:validate-config` | None (validation only) | ~2 min |
| User profile feature | `/chatkit:full-stack-feature` | 4-5 specialized agents | ~15 min |

---

## Integration with Agents

These commands work seamlessly with project-specific agents:

### ChatKit Agents
- **chatkit-server-expert** - Backend tool and widget implementation
- **chatkit-frontend-expert** - Frontend handler and React integration

### Supporting Agents
- **agent-organizer** - Feature planning and agent coordination
- **test-engineer** - Test generation and coverage
- **documentation-expert** - Documentation updates

**Usage Pattern:**
```
/chatkit:full-stack-feature
  ↓
agent-organizer (creates plan)
  ↓
Agent Routing Map
  ├─ chatkit-server-expert (backend)
  ├─ chatkit-frontend-expert (frontend)
  ├─ test-engineer (tests)
  └─ documentation-expert (docs)
```

---

## Best Practices

### 1. Start with Validation

Always validate configuration before starting development:
```bash
/chatkit:validate-config
```

### 2. Use the Right Command

- **Simple tools**: Use `/chatkit:add-tool` for speed
- **Complex features**: Use `/chatkit:full-stack-feature` for completeness

### 3. Follow the Workflow

Each command provides step-by-step guidance:
- Read the instructions
- Verify each step completes
- Test before moving forward

### 4. Leverage Agents

Commands automatically invoke specialized agents:
- Trust the agent assignments
- Don't override with generic agents
- Let the workflow complete

### 5. Test Continuously

- Test after backend implementation
- Test after frontend integration
- Test end-to-end before completion

---

## File Organization

Commands create/modify files following project structure:

```
backend/app/
├── {tool_name}.py          # Tool implementation
├── {tool_name}_widget.py   # Widget renderer (if widget tool)
└── chat.py                 # Tool registration

frontend/src/
├── components/
│   └── ChatKitPanel.tsx    # Client tool handlers
└── lib/
    └── config.ts           # ChatKit configuration

backend/tests/
└── test_{tool_name}.py     # Backend tests

frontend/tests/
└── {tool_name}.test.tsx    # Frontend tests (if client tool)
```

---

## Environment Variables

Commands respect these environment variables:

**Backend:**
- `OPENAI_API_KEY` - Required for OpenAI Agents SDK
- `BACKEND_PORT` - Override default 8000

**Frontend:**
- `VITE_CHATKIT_API_DOMAIN_KEY` - Required for ChatKit React SDK
- `VITE_BACKEND_URL` - Override default http://127.0.0.1:8000

**Validation:**
Use `/chatkit:validate-config` to check all environment variables.

---

## Troubleshooting

### Command Not Found

**Issue:** `/chatkit:add-tool` doesn't work

**Fix:**
- Ensure you're in the project root
- Check `.claude/commands/chatkit/` directory exists
- Restart Claude Code if recently added

### Tool Not Registering

**Issue:** New tool doesn't appear in agent responses

**Fix:**
- Verify tool added to `self.assistant.tools` list
- Restart backend server: `cd backend && uv run uvicorn app.main:app --reload`
- Check backend logs for import errors

### Widget Not Rendering

**Issue:** Widget doesn't display in chat

**Fix:**
- Verify widget schema matches ChatKit spec
- Check browser console for errors
- Inspect network tab for widget data
- Use `/chatkit:validate-config` to check configuration

### Client Tool Not Triggering

**Issue:** Frontend handler not executing

**Fix:**
- Ensure `ctx.context.client_tool_call` set in backend
- Verify frontend handler name matches backend
- Check frontend console for errors
- Restart frontend dev server

---

## Examples

### Example 1: Weather Widget

```bash
/chatkit:add-tool
```

**Conversation:**
```
Claude: What type of tool?
User: Weather lookup with widget showing temperature, conditions, and forecast

Claude: Creating weather tool...
        ✅ backend/app/weather.py (tool)
        ✅ backend/app/weather_widget.py (widget)
        ✅ Updated backend/app/chat.py

Test: "What's the weather in San Francisco?"
```

### Example 2: Theme Switcher

```bash
/chatkit:add-tool
```

**Conversation:**
```
Claude: What type of tool?
User: Theme switcher for light/dark mode

Claude: Creating theme tool...
        ✅ backend/app/theme_switcher.py (client tool)
        ✅ Updated frontend/src/components/ChatKitPanel.tsx
        ✅ Updated backend/app/chat.py

Test: "Switch to dark mode"
```

### Example 3: User Profile Feature

```bash
/chatkit:full-stack-feature
```

**Conversation:**
```
User: Add user profile lookup with avatar, bio, and activity

Claude: [agent-organizer] Creating implementation plan...

        Plan:
        1. Backend tool: get_user_profile(user_id)
        2. Widget renderer: UserProfileWidget
        3. Tests: Unit + integration
        4. Docs: README update

        [chatkit-server-expert] Implementing backend...
        ✅ backend/app/user_profile.py
        ✅ backend/app/user_profile_widget.py

        [test-engineer] Generating tests...
        ✅ backend/tests/test_user_profile.py

        [documentation-expert] Updating docs...
        ✅ README.md updated

        ✅ Feature complete!
```

---

## Related Documentation

- [chatkit-server-expert agent](../.claude/agents/chatkit/chatkit-server-expert.md)
- [chatkit-frontend-expert agent](../.claude/agents/chatkit/chatkit-frontend-expert.md)
- [agent-organizer agent](../.claude/agents/agent-organizer.md)
- [Project README](../../CLAUDE.md)
- [Hooks Documentation](../.claude/hooks/)

---

## Contributing

When adding new ChatKit commands:

1. **Follow naming convention**: `/chatkit:{command-name}`
2. **Include comprehensive docs**: Examples, troubleshooting, best practices
3. **Integrate with agents**: Use chatkit-server-expert and chatkit-frontend-expert
4. **Update this README**: Add to command list and examples

---

## Feedback

Issues or suggestions for ChatKit commands?
- Open an issue describing the workflow
- Propose new commands for common patterns
- Share successful usage examples

---

**Last Updated:** 2025-10-25
**Maintained by:** ChatKit-OpenAI Development Team
