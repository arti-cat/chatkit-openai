# Template Customization Guide

**Common customization patterns for adapting the ChatKit-OpenAI template to client projects.**

This guide covers the most frequent customizations needed when starting a new client project.

---

## Table of Contents

1. [Agent Personality](#1-agent-personality)
2. [Brand & Theme](#2-brand--theme)
3. [Custom Tools](#3-custom-tools)
4. [Widget Customization](#4-widget-customization)
5. [Client Tool Actions](#5-client-tool-actions)
6. [Domain-Specific Data](#6-domain-specific-data)
7. [Authentication](#7-authentication)
8. [Port Configuration](#8-port-configuration)
9. [Production Setup](#9-production-setup)

---

## 1. Agent Personality

### Customize System Instructions

**File**: `backend/app/constants.py`

**Default**:
```python
SYSTEM_INSTRUCTIONS = """
You are a helpful assistant with access to tools...
"""
```

**Client Example - Customer Support**:
```python
SYSTEM_INSTRUCTIONS = """
You are Emma, a professional customer support agent for {CLIENT_COMPANY}.

Your personality:
- Friendly, empathetic, and patient
- Professional but approachable
- Solution-focused

Your role:
- Help customers with account issues, orders, and inquiries
- Provide accurate information using available tools
- Escalate complex issues when needed

Communication style:
- Use customer's name when known
- Acknowledge frustrations with empathy
- Provide clear, step-by-step guidance
- Confirm understanding before closing

Available tools:
- lookup_order: Find order details
- update_account: Modify account information
- create_ticket: Escalate to support team
"""
```

**Client Example - Sales Assistant**:
```python
SYSTEM_INSTRUCTIONS = """
You are Alex, an AI sales assistant for {CLIENT_COMPANY}.

Your goals:
- Understand customer needs through thoughtful questions
- Recommend appropriate products/services
- Guide customers through the buying process
- Build trust through expertise and honesty

Your approach:
- Ask clarifying questions before recommending
- Highlight benefits over features
- Be transparent about limitations
- Never pressure or oversell

Tone: Consultative, helpful, and genuine
"""
```

**Model Configuration**:
```python
# Default model
MODEL_NAME = "gpt-4o"

# For cost optimization
MODEL_NAME = "gpt-4o-mini"

# For complex reasoning
MODEL_NAME = "o1-preview"
```

---

## 2. Brand & Theme

### Frontend Theming

**File**: `frontend/src/components/ChatKitPanel.tsx`

**Custom Theme Colors**:
```typescript
<ChatKit
  {...chatkitConfig}
  theme={{
    colors: {
      primary: '#0066CC',        // Client brand primary
      secondary: '#6C757D',      // Client brand secondary
      background: '#FFFFFF',     // Background color
      text: '#212529',           // Text color
      border: '#DEE2E6',         // Border color
    }
  }}
/>
```

**Dark Mode Support**:
```typescript
const [theme, setTheme] = useState<'light' | 'dark'>('light');

<ChatKit
  {...chatkitConfig}
  theme={theme}
  // Add switch_theme client tool to toggle
/>
```

### Custom Styling

**File**: `frontend/src/styles/custom.css` (create if needed)

```css
/* Client brand colors */
:root {
  --client-primary: #0066CC;
  --client-secondary: #6C757D;
  --client-accent: #28A745;
}

/* Custom ChatKit overrides */
.chatkit-container {
  font-family: 'Your-Client-Font', sans-serif;
  border-radius: 12px;
}

.chatkit-message-user {
  background-color: var(--client-primary);
}

.chatkit-message-assistant {
  background-color: #F8F9FA;
  border-left: 3px solid var(--client-accent);
}
```

### Logo & Branding

**File**: `frontend/public/` (add logo files)

```typescript
// frontend/src/components/ChatKitPanel.tsx
<div className="chat-header">
  <img src="/logo.svg" alt="Client Logo" />
  <h1>Client Assistant</h1>
</div>
```

---

## 3. Custom Tools

### Adding Domain-Specific Tools

**Use Claude Code Command**:
```bash
/chatkit:add-tool
```

**Manual Example - Order Lookup**:

**File**: `backend/app/order_lookup.py` (create new file)

```python
from agents import function_tool, RunContextWrapper
from .constants import FactAgentContext
from .order_widget import create_order_widget

@function_tool(description_override="Look up order details by order ID")
async def lookup_order(
    ctx: RunContextWrapper[FactAgentContext],
    order_id: str,
) -> dict[str, str]:
    """
    Look up order details by order ID.

    Args:
        order_id: The order ID to look up

    Returns:
        Order details including status, items, and shipping info
    """
    # Replace with actual API call to your order system
    order_data = await fetch_order_from_api(order_id)

    if not order_data:
        return {"error": f"Order {order_id} not found"}

    # Create and stream widget
    widget = create_order_widget(order_data)
    await ctx.context.stream_widget(
        widget,
        copy_text=f"Order {order_id}: {order_data['status']}"
    )

    return {
        "order_id": order_id,
        "status": order_data["status"],
        "total": f"${order_data['total']:.2f}"
    }
```

**Register Tool** in `backend/app/chat.py`:
```python
from .order_lookup import lookup_order

class FactAssistantServer(ChatKitServer):
    def __init__(self):
        # ... existing code
        self.assistant = Agent(
            model=MODEL_NAME,
            instructions=SYSTEM_INSTRUCTIONS,
            tools=[
                # ... existing tools
                lookup_order,  # Add your custom tool
            ],
        )
```

---

## 4. Widget Customization

### Creating Custom Widgets

**File**: `backend/app/order_widget.py` (example)

```python
from typing import Any

def create_order_widget(order_data: dict[str, Any]) -> dict[str, Any]:
    """Create custom widget for order display."""
    return {
        "type": "widget",
        "widget": {
            "type": "sections",
            "title": f"Order #{order_data['id']}",
            "sections": [
                {
                    "type": "table",
                    "title": "Order Details",
                    "rows": [
                        {"key": "Order ID", "value": order_data["id"]},
                        {"key": "Status", "value": order_data["status"]},
                        {"key": "Order Date", "value": order_data["created_at"]},
                        {"key": "Total", "value": f"${order_data['total']:.2f}"},
                    ],
                },
                {
                    "type": "table",
                    "title": "Items",
                    "rows": [
                        {"key": item["name"], "value": f"${item['price']} x {item['quantity']}"}
                        for item in order_data["items"]
                    ],
                },
                {
                    "type": "metadata",
                    "items": [
                        {"key": "Shipping Address", "value": order_data["shipping_address"]},
                        {"key": "Tracking Number", "value": order_data["tracking"]},
                        {"key": "Estimated Delivery", "value": order_data["eta"]},
                    ],
                },
            ],
        },
    }
```

### Widget with Images

```python
{
    "type": "widget",
    "widget": {
        "type": "sections",
        "title": "Product Details",
        "sections": [
            {
                "type": "image",
                "url": "https://example.com/product.jpg",
                "alt": "Product image",
            },
            {
                "type": "table",
                "rows": [
                    {"key": "Price", "value": "$99.99"},
                    {"key": "In Stock", "value": "Yes"},
                ],
            },
        ],
    },
}
```

---

## 5. Client Tool Actions

### Adding Frontend Actions

**File**: `frontend/src/components/ChatKitPanel.tsx`

**Example - Open External Link**:

**Backend** (`backend/app/navigation.py`):
```python
@function_tool(description_override="Open external link in new tab")
async def open_link(
    ctx: RunContextWrapper[FactAgentContext],
    url: str,
    label: str = "Link",
) -> dict[str, str]:
    """Open external link in new browser tab."""
    ctx.context.client_tool_call = ClientToolCall(
        name="open_link",
        arguments={"url": url, "label": label}
    )
    return {"action": "open_link", "url": url}
```

**Frontend** (`frontend/src/components/ChatKitPanel.tsx`):
```typescript
const handleClientToolCall = (call: ClientToolCall) => {
  switch (call.name) {
    case "open_link": {
      const { url, label } = call.arguments;
      window.open(url, '_blank', 'noopener,noreferrer');
      toast.success(`Opening ${label}...`);
      break;
    }

    // ... other handlers

    default:
      console.warn('Unknown client tool:', call.name);
  }

  return {
    type: 'client_tool_call' as const,
    tool_call_id: call.tool_call_id,
    result: { success: true },
  };
};
```

**Example - Show Notification**:

**Backend**:
```python
ctx.context.client_tool_call = ClientToolCall(
    name="show_notification",
    arguments={
        "message": "Order updated successfully!",
        "type": "success"
    }
)
```

**Frontend**:
```typescript
case "show_notification": {
  const { message, type } = call.arguments;
  if (type === 'success') toast.success(message);
  else if (type === 'error') toast.error(message);
  else toast.info(message);
  break;
}
```

---

## 6. Domain-Specific Data

### Replace Example Data

**File**: `backend/app/facts.py` (example - replace with your data model)

**Default (Facts Storage)**:
```python
class FactsStore:
    def __init__(self):
        self.facts: list[dict] = []

    def add_fact(self, text: str, category: str) -> dict:
        fact = {
            "text": text,
            "category": category,
            "timestamp": datetime.now().isoformat()
        }
        self.facts.append(fact)
        return fact
```

**Client Example (Customer Data)**:
```python
# backend/app/customer_data.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

class CustomerStore:
    def __init__(self, database_url: str):
        self.engine = create_engine(database_url)
        self.Session = sessionmaker(bind=self.engine)

    def get_customer(self, customer_id: str) -> dict:
        """Fetch customer from database."""
        session = self.Session()
        try:
            customer = session.query(Customer).filter_by(id=customer_id).first()
            return customer.to_dict() if customer else None
        finally:
            session.close()

    def update_customer(self, customer_id: str, updates: dict) -> dict:
        """Update customer information."""
        session = self.Session()
        try:
            customer = session.query(Customer).filter_by(id=customer_id).first()
            if customer:
                for key, value in updates.items():
                    setattr(customer, key, value)
                session.commit()
            return customer.to_dict() if customer else None
        finally:
            session.close()
```

### External API Integration

```python
# backend/app/external_api.py
import httpx

async def fetch_from_client_api(endpoint: str, params: dict) -> dict:
    """Fetch data from client's external API."""
    async with httpx.AsyncClient() as client:
        response = await client.get(
            f"https://api.client.com/{endpoint}",
            params=params,
            headers={"Authorization": f"Bearer {API_KEY}"}
        )
        response.raise_for_status()
        return response.json()
```

---

## 7. Authentication

### Adding Authentication Layer

**Backend Middleware** (`backend/app/auth.py`):
```python
from fastapi import HTTPException, Header

async def verify_api_key(x_api_key: str = Header(None)):
    """Verify API key from request header."""
    if not x_api_key or x_api_key != EXPECTED_API_KEY:
        raise HTTPException(status_code=401, detail="Invalid API key")
    return x_api_key
```

**Apply to Endpoint** (`backend/app/main.py`):
```python
from .auth import verify_api_key

@app.post("/chatkit", dependencies=[Depends(verify_api_key)])
async def handle_chatkit(request: Request):
    # ... existing code
```

### Session-Based Auth

```python
# backend/app/session.py
from fastapi import Depends, HTTPException, Cookie

async def get_current_user(session_id: str = Cookie(None)):
    """Get current user from session."""
    if not session_id:
        raise HTTPException(status_code=401, detail="Not authenticated")

    user = await fetch_user_from_session(session_id)
    if not user:
        raise HTTPException(status_code=401, detail="Session expired")

    return user
```

---

## 8. Port Configuration

### Changing Default Ports

**Backend Port** (`backend/app/main.py`):
```python
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8001)  # Change from 8000
```

**Or via command line**:
```bash
uv run uvicorn app.main:app --reload --port 8001
```

**Frontend Port** (`frontend/vite.config.ts`):
```typescript
export default defineConfig({
  server: {
    port: 5171,  // Change from 5170
    proxy: {
      '/chatkit': {
        target: 'http://127.0.0.1:8001',  // Match backend port
        changeOrigin: true,
      },
    },
  },
});
```

**Update package.json**:
```json
{
  "scripts": {
    "backend": "cd backend && uv run uvicorn app.main:app --reload --port 8001",
    "frontend": "cd frontend && vite --port 5171"
  }
}
```

---

## 9. Production Setup

### Environment Configuration

**Backend** (`.env.production`):
```bash
OPENAI_API_KEY=sk-proj-production-key
DATABASE_URL=postgresql://user:pass@host:5432/dbname
REDIS_URL=redis://host:6379/0
LOG_LEVEL=INFO
ENVIRONMENT=production
```

**Frontend** (`.env.production`):
```bash
VITE_CHATKIT_API_DOMAIN_KEY=actual-domain-key-from-openai
VITE_BACKEND_URL=https://api.client.com
VITE_ENVIRONMENT=production
```

### Domain Allowlisting

**File**: `frontend/vite.config.ts`

```typescript
export default defineConfig({
  server: {
    allowedHosts: [
      'localhost',
      '127.0.0.1',
      'client-app.com',          // Production domain
      'staging.client-app.com',  // Staging domain
      'www.client-app.com',      // WWW subdomain
    ],
  },
});
```

### Production Optimizations

**Backend**:
```python
# backend/app/main.py
app = FastAPI(
    title="Client ChatKit API",
    docs_url="/docs" if ENVIRONMENT == "development" else None,  # Disable docs in prod
    redoc_url=None,
)

# Add CORS for production
app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://client-app.com"],
    allow_methods=["POST"],
    allow_headers=["*"],
)
```

**Frontend**:
```bash
# Build for production
cd frontend
npm run build

# Output in frontend/dist/
# Deploy to CDN or static hosting
```

---

## Quick Customization Checklist

For each new client project:

- [ ] Update agent personality (`backend/app/constants.py`)
- [ ] Set environment variables (`.env` files)
- [ ] Customize branding/theme (`frontend/src/styles/`)
- [ ] Replace example tools with client-specific tools
- [ ] Update widget designs for client data
- [ ] Add client tool actions for UI interactions
- [ ] Replace example data with client data models
- [ ] Configure authentication if needed
- [ ] Adjust ports if conflicts exist
- [ ] Set up production environment and domain allowlist

---

## Common Patterns by Industry

### E-Commerce
- Tools: `lookup_product`, `check_inventory`, `process_order`
- Widgets: Product cards, order status, shipping tracking
- Client tools: `add_to_cart`, `view_product_page`

### Healthcare
- Tools: `schedule_appointment`, `lookup_patient_info`, `check_availability`
- Widgets: Appointment cards, prescription details, test results
- Client tools: `book_appointment`, `download_report`

### Financial Services
- Tools: `check_balance`, `transaction_history`, `transfer_funds`
- Widgets: Account summaries, transaction tables, investment performance
- Client tools: `navigate_to_accounts`, `download_statement`

### SaaS/Tech Support
- Tools: `check_account_status`, `lookup_ticket`, `run_diagnostic`
- Widgets: System status, ticket details, diagnostic results
- Client tools: `open_documentation`, `schedule_demo`

---

## Getting Help

- **Claude Code commands**: Use `/chatkit:add-tool` for guided tool creation
- **Architecture questions**: See [CLAUDE.md](CLAUDE.md)
- **Setup issues**: See [TEMPLATE_SETUP.md](TEMPLATE_SETUP.md)
- **Agents**: Use `chatkit-server-expert` or `chatkit-frontend-expert`

---

**Need custom patterns?** Study the `examples/` directory for complete reference implementations across different domains.
