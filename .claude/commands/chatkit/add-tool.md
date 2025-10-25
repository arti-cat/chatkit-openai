# Add ChatKit Agent Tool

## Description

Guided scaffolding for adding new ChatKit agent tools with backend @function_tool, optional widget renderer, and optional frontend client tool handler.

## Usage

```bash
/chatkit:add-tool
```

Claude will guide you through:
1. Identifying tool type (widget/client/data)
2. Generating backend tool implementation
3. Creating widget renderer (if needed)
4. Adding frontend handler (if client tool)
5. Registering tool in chat.py
6. Testing the implementation

## Tool Types

### Widget Tool
Displays rich UI components in the chat interface.

**Use cases:**
- Weather display
- User profile cards
- Data tables
- Charts and visualizations
- Multi-section information displays

**Components:**
- Backend @function_tool
- Widget renderer module
- Widget schema following ChatKit spec

### Client Tool
Triggers frontend actions without displaying widgets.

**Use cases:**
- Theme switching
- Navigation
- Local state updates
- UI component interactions

**Components:**
- Backend @function_tool with ClientToolCall
- Frontend onClientToolCall handler

### Data Tool
Returns JSON data only, no UI rendering.

**Use cases:**
- API queries
- Database lookups
- Calculations
- Data transformations

**Components:**
- Backend @function_tool returning dict

## Step-by-Step Workflow

### Step 1: Determine Tool Type

Claude will ask:
- What does this tool do?
- Does it need to display UI? (→ Widget tool)
- Does it need to trigger frontend actions? (→ Client tool)
- Is it data-only? (→ Data tool)

### Step 2: Generate Backend Tool

Creates `backend/app/{tool_name}.py`:

```python
from agents import function_tool, RunContextWrapper
from .constants import FactAgentContext

@function_tool(description_override="Tool description for agent")
async def tool_name(
    ctx: RunContextWrapper[FactAgentContext],
    param1: str,
    param2: int = 10,
) -> dict[str, str]:
    """
    Tool docstring explaining functionality.

    Args:
        param1: Parameter description
        param2: Optional parameter with default

    Returns:
        Dictionary with tool results
    """
    # Tool implementation
    result = perform_operation(param1, param2)

    # For widget tools:
    # await ctx.context.stream_widget(widget, copy_text="...")

    # For client tools:
    # ctx.context.client_tool_call = ClientToolCall(
    #     name="client_action",
    #     arguments={"key": "value"}
    # )

    return {"result": result}
```

### Step 3: Create Widget Renderer (Widget Tools Only)

Creates `backend/app/{tool_name}_widget.py`:

```python
from typing import Any

def create_tool_name_widget(data: dict[str, Any]) -> dict[str, Any]:
    """Create ChatKit widget for tool data."""
    return {
        "type": "widget",
        "widget": {
            "type": "sections",
            "title": "Widget Title",
            "sections": [
                {
                    "type": "table",
                    "title": "Section Title",
                    "rows": [
                        {"key": "Label", "value": data.get("value")},
                    ],
                },
            ],
        },
    }
```

### Step 4: Register Tool

Updates `backend/app/chat.py`:

```python
from .tool_name import tool_name

class FactAssistantServer(ChatKitServer):
    def __init__(self):
        # ... existing code

        self.assistant = Agent(
            model=MODEL_NAME,
            instructions=SYSTEM_INSTRUCTIONS,
            tools=[
                # ... existing tools
                tool_name,  # ← Add new tool here
            ],
        )
```

### Step 5: Add Frontend Handler (Client Tools Only)

Updates `frontend/src/components/ChatKitPanel.tsx`:

```typescript
const handleClientToolCall = (call: ClientToolCall) => {
  // Add new handler
  if (call.name === 'client_action') {
    const { key } = call.arguments;
    // Handle the client action
    performFrontendAction(key);
  }

  // ... existing handlers

  return {
    type: 'client_tool_call' as const,
    tool_call_id: call.tool_call_id,
    result: { success: true },
  };
};
```

### Step 6: Test Implementation

Claude will:
1. Restart backend server (if needed)
2. Test tool invocation via chat interface
3. Verify widget rendering (widget tools)
4. Verify client action triggers (client tools)
5. Check for errors in console

## Examples

### Example 1: Widget Tool

```
User: /chatkit:add-tool

Claude: What type of tool would you like to add?
User: A weather lookup tool that shows a widget with weather data

Claude: [Generates:]
- backend/app/weather.py (@function_tool)
- backend/app/weather_widget.py (widget renderer)
- Updates backend/app/chat.py (tool registration)
- Test instructions
```

### Example 2: Client Tool

```
User: /chatkit:add-tool

Claude: What type of tool would you like to add?
User: A tool to switch between light and dark themes

Claude: [Generates:]
- backend/app/theme_switcher.py (with ClientToolCall)
- Updates frontend/src/components/ChatKitPanel.tsx (handler)
- Test instructions
```

### Example 3: Data Tool

```
User: /chatkit:add-tool

Claude: What type of tool would you like to add?
User: A calculator tool that returns computation results

Claude: [Generates:]
- backend/app/calculator.py (returns dict)
- Updates backend/app/chat.py (registration)
- Test instructions
```

## Best Practices

1. **Naming Convention**: Use snake_case for tool names (e.g., `get_user_profile`, `switch_theme`)
2. **Type Safety**: Use Pydantic for parameter validation
3. **Async**: Declare tools as `async def` for I/O operations
4. **Documentation**: Include clear docstrings
5. **Error Handling**: Handle errors gracefully, return meaningful messages
6. **Widget Copy Text**: Provide useful copy text for widgets
7. **Testing**: Test both success and error cases

## Prerequisites

- ChatKit backend running (FastAPI server)
- ChatKit frontend running (Vite dev server)
- OPENAI_API_KEY environment variable set

## Files Modified

- `backend/app/{tool_name}.py` (created)
- `backend/app/{tool_name}_widget.py` (created, widget tools only)
- `backend/app/chat.py` (modified, tool registration)
- `frontend/src/components/ChatKitPanel.tsx` (modified, client tools only)

## Verification Checklist

After tool creation:
- [ ] Backend server restarts without errors
- [ ] Tool appears in agent's available tools
- [ ] Tool executes successfully when invoked
- [ ] Widget renders correctly (widget tools)
- [ ] Client action triggers (client tools)
- [ ] Error handling works as expected
- [ ] Tests added (optional but recommended)

## Related Commands

- `/chatkit:validate-config` - Validate ChatKit configuration
- `/chatkit:full-stack-feature` - Add complete full-stack feature
- `/test:generate-tests` - Generate tests for new tool

## Related Agents

- `chatkit-server-expert` - Backend tool implementation
- `chatkit-frontend-expert` - Frontend handler implementation
- `test-engineer` - Test generation

## Troubleshooting

### Tool not showing in agent responses
- Verify tool is in `self.assistant.tools` list
- Restart backend server
- Check server logs for import errors

### Widget not rendering
- Inspect browser console for errors
- Verify widget schema matches ChatKit spec
- Check network tab for widget data

### Client tool not triggering
- Ensure `ctx.context.client_tool_call` is set
- Verify frontend handler name matches backend
- Check frontend console for errors

## See Also

- [chatkit-server-expert agent](.claude/agents/chatkit/chatkit-server-expert.md)
- [chatkit-frontend-expert agent](.claude/agents/chatkit/chatkit-frontend-expert.md)
- [backend/app/chat.py](backend/app/chat.py) - Tool registration
- [backend/app/sample_widget.py](backend/app/sample_widget.py) - Widget example
