# Add Full-Stack ChatKit Feature

## Description

Orchestrates complete end-to-end feature implementation spanning backend tools, widgets, frontend handlers, tests, and documentation using specialized agents.

## Usage

```bash
/chatkit:full-stack-feature
```

Claude will:
1. Use `agent-organizer` to plan the feature
2. Coordinate specialized agents (chatkit-server-expert, chatkit-frontend-expert, test-engineer, documentation-expert)
3. Implement each phase with proper validation
4. Ensure all components integrate correctly

## Workflow Phases

### Phase 1: Planning & Analysis

**Agent:** `agent-organizer`

Creates Agent Routing Map with:
- Feature requirements analysis
- Component breakdown (backend tool, widget, frontend handler)
- Agent assignment for each component
- Implementation order
- Testing strategy

**Output:** Detailed implementation plan with specific agent assignments

### Phase 2: Backend Tool Implementation

**Agent:** `chatkit-server-expert`

Implements:
1. Create `@function_tool` in `backend/app/{feature}.py`
2. Parameter validation with Pydantic
3. Tool logic and business rules
4. Error handling
5. Tool registration in `backend/app/chat.py`

**For Widget Features:**
- Create widget renderer in `backend/app/{feature}_widget.py`
- Implement `stream_widget()` call

**For Client Tool Features:**
- Implement `ClientToolCall` in tool
- Define arguments schema

**Validation:**
- Backend server restarts successfully
- Tool imports without errors
- Tool appears in agent's tool list

### Phase 3: Widget Renderer (Widget Features Only)

**Agent:** `chatkit-server-expert`

Creates:
1. Widget structure following ChatKit schema
2. Multi-section layout (tables, metadata, images)
3. Data transformation from tool output to widget format
4. Copy text for clipboard functionality

**Example Structure:**
```python
def create_feature_widget(data: dict) -> dict:
    return {
        "type": "widget",
        "widget": {
            "type": "sections",
            "title": "Feature Title",
            "sections": [
                {
                    "type": "table",
                    "title": "Details",
                    "rows": [
                        {"key": "Key", "value": "Value"},
                    ],
                },
            ],
        },
    }
```

**Validation:**
- Widget schema is valid
- Data transformation works correctly
- Widget renders in chat interface

### Phase 4: Frontend Integration (Client Tool Features Only)

**Agent:** `chatkit-frontend-expert`

Updates `frontend/src/components/ChatKitPanel.tsx`:
1. Add handler in `onClientToolCall`
2. Type-safe argument handling
3. State updates
4. UI changes
5. Return completion item

**Example:**
```typescript
if (call.name === 'feature_action') {
  const { param } = call.arguments;
  performAction(param);
  updateState(param);
}

return {
  type: 'client_tool_call' as const,
  tool_call_id: call.tool_call_id,
  result: { success: true },
};
```

**Validation:**
- Frontend compiles without errors
- Client tool triggers correctly
- State updates work as expected

### Phase 5: Testing

**Agent:** `test-engineer`

Generates:
1. Backend unit tests for tool logic
2. Widget rendering tests
3. Frontend component tests (client tools)
4. Integration tests for full flow

**Test Coverage:**
- ✅ Tool executes successfully with valid inputs
- ✅ Tool handles invalid inputs gracefully
- ✅ Widget renders with correct data
- ✅ Client tool triggers frontend action
- ✅ Error cases handled properly

**Files Created:**
- `backend/tests/test_{feature}.py`
- `frontend/tests/{feature}.test.tsx` (client tools)

### Phase 6: Documentation

**Agent:** `documentation-expert`

Updates:
1. README with feature description
2. Usage examples
3. API documentation (if applicable)
4. CHANGELOG entry

**Documentation Includes:**
- Feature overview
- How to use the tool
- Example requests and responses
- Screenshots (widget features)
- Troubleshooting guide

## Example: User Profile Lookup Feature

### User Request

```
User: Add a user profile lookup tool that shows a widget with user details including photo, name, email, and recent activity
```

### Execution Flow

**Phase 1: Planning**
```
Agent: agent-organizer

Plan:
1. Backend tool: get_user_profile(user_id: str)
2. Widget renderer: UserProfileWidget
3. No client tool needed (widget-only feature)
4. Tests: Unit test for profile fetch, widget rendering test
5. Docs: Update README with usage example

Agent Routing Map:
- Backend: chatkit-server-expert
- Widget: chatkit-server-expert
- Tests: test-engineer
- Docs: documentation-expert
```

**Phase 2: Backend Tool**
```
Agent: chatkit-server-expert

Creates: backend/app/user_profile.py

@function_tool(description_override="Look up user profile by ID")
async def get_user_profile(
    ctx: RunContextWrapper[FactAgentContext],
    user_id: str,
) -> dict[str, str]:
    # Fetch user data
    user = fetch_user(user_id)

    # Create widget
    widget = create_user_profile_widget(user)

    # Stream to frontend
    await ctx.context.stream_widget(
        widget,
        copy_text=f"User profile for {user['name']}"
    )

    return {"user_id": user_id, "status": "displayed"}
```

**Phase 3: Widget Renderer**
```
Agent: chatkit-server-expert

Creates: backend/app/user_profile_widget.py

def create_user_profile_widget(user: dict) -> dict:
    return {
        "type": "widget",
        "widget": {
            "type": "sections",
            "title": f"User Profile: {user['name']}",
            "sections": [
                {
                    "type": "table",
                    "title": "Details",
                    "rows": [
                        {"key": "Name", "value": user['name']},
                        {"key": "Email", "value": user['email']},
                        {"key": "Joined", "value": user['joined_date']},
                    ],
                },
                {
                    "type": "table",
                    "title": "Recent Activity",
                    "rows": [
                        {"key": "Last login", "value": user['last_login']},
                        {"key": "Actions", "value": str(user['action_count'])},
                    ],
                },
            ],
        },
    }
```

**Phase 4: Frontend** (Skipped - widget-only)

**Phase 5: Testing**
```
Agent: test-engineer

Creates: backend/tests/test_user_profile.py

def test_get_user_profile_valid():
    result = get_user_profile("user-123")
    assert result["status"] == "displayed"

def test_get_user_profile_invalid():
    with pytest.raises(ValueError):
        get_user_profile("invalid<>id")
```

**Phase 6: Documentation**
```
Agent: documentation-expert

Updates: README.md

## Features

### User Profile Lookup

Look up user profiles by ID and display detailed information including:
- User name and email
- Join date and status
- Recent activity summary

**Usage:**
"Show me the profile for user-123"

**Example Response:**
[Widget displaying user profile card]
```

## Agent Coordination Pattern

```
User Request
    ↓
agent-organizer (creates plan)
    ↓
Agent Routing Map
    ├─ Backend: chatkit-server-expert
    │   ├─ Tool implementation
    │   └─ Widget renderer
    ├─ Frontend: chatkit-frontend-expert (if client tool)
    │   └─ Client tool handler
    ├─ Tests: test-engineer
    │   ├─ Backend tests
    │   └─ Frontend tests
    └─ Docs: documentation-expert
        └─ README, examples, CHANGELOG
```

## Invariants

1. **Phase Order**: Follow plan phases sequentially
2. **Validation**: Test each phase before proceeding
3. **Agent Assignment**: Use ONLY agents specified in routing map
4. **Integration**: Ensure components integrate correctly
5. **Documentation**: Update docs before marking complete

## Best Practices

1. **Start with Planning**: Always use agent-organizer first
2. **Follow the Plan**: Implement exactly as planned, adjust plan if needed
3. **Validate Each Phase**: Test components before moving forward
4. **Use Specialized Agents**: Don't use generic agents when specialists exist
5. **Complete Documentation**: Update all relevant docs

## Common Patterns

### Widget-Only Feature
```
Plan → Backend Tool → Widget Renderer → Tests → Docs
Agents: agent-organizer, chatkit-server-expert, test-engineer, documentation-expert
```

### Client Tool Feature
```
Plan → Backend Tool → Frontend Handler → Tests → Docs
Agents: agent-organizer, chatkit-server-expert, chatkit-frontend-expert, test-engineer, documentation-expert
```

### Data-Only Feature
```
Plan → Backend Tool → Tests → Docs
Agents: agent-organizer, chatkit-server-expert, test-engineer, documentation-expert
```

### Complex Feature (Widget + Client Tool)
```
Plan → Backend Tool → Widget Renderer → Frontend Handler → Tests → Docs
Agents: All five specialists
```

## Verification Checklist

After feature completion:
- [ ] All planned components implemented
- [ ] Backend tool registered and working
- [ ] Widget renders correctly (if widget feature)
- [ ] Client tool triggers (if client feature)
- [ ] Tests pass
- [ ] Documentation updated
- [ ] No errors in backend logs
- [ ] No errors in browser console
- [ ] Feature works end-to-end

## Related Commands

- `/chatkit:add-tool` - Quick tool addition (simpler, less orchestration)
- `/chatkit:validate-config` - Validate configuration before starting
- `/project:create-feature` - Generic feature creation workflow
- `/orchestration:start` - Start complex multi-agent task

## Related Agents

- `agent-organizer` - Creates Agent Routing Map
- `chatkit-server-expert` - Backend implementation
- `chatkit-frontend-expert` - Frontend integration
- `test-engineer` - Test generation
- `documentation-expert` - Documentation updates

## When to Use This vs /chatkit:add-tool

**Use /chatkit:full-stack-feature when:**
- Feature spans multiple components
- Need comprehensive testing
- Requires detailed planning
- Want full documentation
- Feature is complex or novel

**Use /chatkit:add-tool when:**
- Simple, straightforward tool
- Similar to existing tools
- Quick iteration needed
- Don't need extensive testing initially

## Success Metrics

- ⏱️ **Time Efficiency**: 40-60% faster than manual implementation
- 🎯 **Completeness**: All components (backend, frontend, tests, docs) included
- ✅ **Quality**: Tests ensure functionality works correctly
- 📖 **Maintainability**: Documentation enables future modifications

## See Also

- [agent-organizer agent](.claude/agents/agent-organizer.md)
- [chatkit-server-expert agent](.claude/agents/chatkit/chatkit-server-expert.md)
- [chatkit-frontend-expert agent](.claude/agents/chatkit/chatkit-frontend-expert.md)
- [WORKFLOW_EXAMPLES.md](.claude/agents/WORKFLOW_EXAMPLES.md)
