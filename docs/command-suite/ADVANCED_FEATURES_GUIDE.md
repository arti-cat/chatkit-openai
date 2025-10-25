# Claude Code Advanced Features Guide

**Document Version:** 1.0
**Last Updated:** 2025-10-24
**Verified Against:** Official Anthropic/Claude Code documentation
**Status:** ✅ Accuracy Verified

---

## About This Document

This guide covers advanced Claude Code features that are not included in the main [BEST_PRACTICES_ANALYSIS.md](BEST_PRACTICES_ANALYSIS.md). All information is sourced from official Anthropic documentation and verified for accuracy.

**What's Covered:**
1. Planning Workflow (Explore → Plan → Code → Commit)
2. Headless Mode & Automation
3. MCP (Model Context Protocol) Integration
4. Extended Thinking
5. Checkpointing & Session Recovery
6. Output Styles
7. Skills System Deep Dive

---

## 1. Planning Workflow: Explore → Plan → Code → Commit

### Overview

The official workflow recommended by Anthropic for complex development tasks. **This workflow significantly enhances performance for tasks requiring upfront analytical thinking.**

### The Four-Step Process

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ EXPLORE  │ → │   PLAN   │ → │   CODE   │ → │  COMMIT  │
└──────────┘    └──────────┘    └──────────┘    └──────────┘
```

#### Step 1: Explore (Research Without Coding)

**Purpose:** Gather context before making changes

**How to Use:**
```bash
# Request Claude read files WITHOUT writing code
"Read the ChatKit backend implementation and understand the widget system. Don't write any code yet."

# Have Claude explore multiple files
"Examine backend/app/chat.py, backend/app/sample_widget.py, and frontend/src/components/ChatKitPanel.tsx to understand the current architecture."

# Use images for context
"Look at this screenshot of the current UI [drag image]. Understand the layout before suggesting changes."
```

**Best Practices:**
- ✅ Explicitly instruct: "Don't write code yet"
- ✅ Use subagents for verification: "Use the Explore agent to understand the codebase structure"
- ✅ Leverage images, URLs, and documentation
- ❌ Don't skip exploration for complex tasks

**Official Guidance:**
> "Steps #1-#2 are crucial—without them, Claude tends to jump straight to coding a solution."

---

#### Step 2: Plan (Strategic Design Before Implementation)

**Purpose:** Develop a detailed strategy using extended thinking

**How to Trigger Planning:**
```bash
# Basic planning request
"Create a plan for adding user profile lookup to ChatKit"

# Extended thinking for complex plans
"think harder about the best architecture for real-time notifications in ChatKit"

# Explicit plan documentation
"Develop a detailed implementation plan and save it as a GitHub issue so we can reference it later"
```

**Planning Output Example:**
```markdown
## Implementation Plan: User Profile Widget

### Phase 1: Backend Tool
1. Create @function_tool get_user_profile(user_id: str)
2. Add OpenAI API integration for profile data
3. Define UserProfileWidget schema

### Phase 2: Widget Renderer
1. Create backend/app/user_profile_widget.py
2. Implement multi-section layout (similar to weather widget)
3. Add profile image, bio, and metadata sections

### Phase 3: Frontend Integration
1. No client tool needed (widget-only)
2. Verify widget rendering in ChatKitPanel.tsx

### Phase 4: Testing
1. Unit test for get_user_profile()
2. Integration test for widget rendering
3. Manual testing in chat interface

### Estimated Complexity: Medium
### Estimated Time: 4-6 hours
```

**Creating Persistent Plans:**
```bash
# Save plan as document
"Create a document with this plan so we can reset to this spot if implementation isn't what we want"

# Save plan as GitHub issue
"Create a GitHub issue with this implementation plan, including all phases and acceptance criteria"
```

**Best Practices:**
- ✅ Request plans for features spanning 3+ files
- ✅ Save plans before starting implementation
- ✅ Use extended thinking ("think harder") for architectural decisions
- ✅ Break plans into phases with clear milestones
- ❌ Don't plan for trivial single-file changes

---

#### Step 3: Code (Implementation with Verification)

**Purpose:** Implement the solution while continuously verifying

**How to Use:**
```bash
# Request implementation with verification
"Implement the user profile widget according to the plan. Explicitly verify the reasonableness of your solution as you implement each piece."

# Step-by-step implementation
"Implement Phase 1 of the plan (backend tool only). Verify it works before moving to Phase 2."

# Test-driven approach
"Write tests first for the user profile tool, then implement the code to pass those tests."
```

**Verification During Implementation:**
Claude should:
- ✅ Verify each component works before proceeding
- ✅ Run tests after each significant change
- ✅ Check for regressions in related code
- ✅ Validate configuration and environment setup

**Example Implementation Flow:**
```bash
1. Claude: "Implementing backend tool get_user_profile()..."
   [Creates backend/app/user_profile.py]

2. Claude: "Verifying tool works with test user ID..."
   [Runs: uv run python -c "from app.user_profile import get_user_profile; print(get_user_profile('test-123'))"]

3. Claude: "Tool verified. Moving to widget renderer..."
   [Creates backend/app/user_profile_widget.py]

4. Claude: "Testing widget rendering..."
   [Manual test via chat interface]
```

**Best Practices:**
- ✅ Implement in phases matching the plan
- ✅ Test each phase before proceeding
- ✅ Use TodoWrite to track implementation progress
- ✅ Request verification at each step
- ❌ Don't implement everything at once without testing

---

#### Step 4: Commit (Finalize and Document)

**Purpose:** Create clean commits and update documentation

**How to Use:**
```bash
# Request commit with descriptive message
"Stage these changes and create a commit with a descriptive message following this repository's commit style"

# Update documentation
"Create a commit for the user profile feature, and update the README with usage examples"

# Create pull request
"Create a pull request for this feature with a description of what was implemented and why"
```

**Best Practices:**
- ✅ Review changes before committing (git diff)
- ✅ Follow repository commit message conventions
- ✅ Update CHANGELOG or release notes
- ✅ Include usage examples in documentation
- ✅ Add the standard commit footer

---

### Complete Workflow Example: Adding ChatKit Feature

```bash
# STEP 1: EXPLORE
User: "Read backend/app/chat.py and understand how tools are registered.
       Also examine backend/app/sample_widget.py to see widget structure.
       Don't write any code yet."

Claude: [Reads files, analyzes structure]
        "I understand the architecture. Tools use @function_tool decorator,
        widgets follow the ChatKit schema with sections and tables."

# STEP 2: PLAN
User: "think harder about adding a user profile lookup feature with a widget"

Claude: [Extended thinking activated]
        [Creates detailed 4-phase plan]
        "I've created a comprehensive plan. Should I save this as a document?"

User: "Yes, save it as docs/USER_PROFILE_PLAN.md"

# STEP 3: CODE
User: "Implement Phase 1 according to the plan. Verify each component works."

Claude: [Implements backend tool]
        [Tests with sample user ID]
        "Phase 1 complete and verified. Ready for Phase 2?"

User: "Yes, continue"

Claude: [Implements widget renderer]
        [Tests widget structure]
        "Phase 2 complete. Phase 3 (frontend) doesn't require changes.
        Moving to Phase 4 (testing)."

# STEP 4: COMMIT
User: "Create a commit for this feature and update the README"

Claude: [Stages changes]
        [Creates descriptive commit]
        [Updates README with usage example]
        "Committed feature. Updated documentation in README."
```

---

### When to Use This Workflow

✅ **Use Explore → Plan → Code → Commit for:**
- New features spanning multiple files
- Architecture refactoring
- Complex bug fixes requiring investigation
- Integration with new services or APIs
- Security-sensitive changes
- Performance optimizations

❌ **Skip for simpler tasks:**
- Single-file bug fixes
- Documentation updates
- Formatting changes
- Adding tests to existing code

---

## 2. Headless Mode & Automation

### Overview

Headless mode enables **programmatic execution of Claude Code without interactive UI**, suitable for automation, scripting, and CI/CD integration.

### Basic Usage

**Command:** `claude -p` (or `--print`)

**Example:**
```bash
# Single-turn execution
claude -p "Review the latest commit for security issues"

# With permission settings
claude -p "Stage my changes and write commits" \
  --allowedTools "Bash,Read,Write,Edit" \
  --permission-mode acceptEdits
```

---

### Output Formats

#### 1. Text Output (Default)

Returns plain text responses suitable for direct consumption.

```bash
claude -p "How does the ChatKit widget system work?"
# Output: Plain text explanation
```

#### 2. JSON Output

Returns structured metadata including cost, duration, turn count, and session ID.

```bash
claude -p "Analyze backend/app/chat.py" --output-format json

# Output:
{
  "response": "Analysis text...",
  "metadata": {
    "cost": 0.024,
    "duration_ms": 3421,
    "turn_count": 1,
    "session_id": "550e8400-e29b-41d4..."
  }
}
```

**Parsing JSON:**
```bash
# Extract just the response
claude -p "Explain this code" --output-format json | jq -r '.response'

# Get session ID for resumption
SESSION_ID=$(claude -p "Start analysis" --output-format json | jq -r '.metadata.session_id')
```

#### 3. Streaming JSON Output (JSONL)

Emits each message as a separate JSON object, useful for real-time processing.

```bash
claude -p "Build a user authentication system" --output-format stream-json

# Output (one JSON object per line):
{"type":"assistant","message":{"role":"assistant","content":[{"type":"text","text":"I'll build..."}]}}
{"type":"tool_use","name":"Write","input":{"file_path":"auth.py",...}}
{"type":"tool_result","tool_use_id":"...","content":"File written"}
...
```

**Processing Streaming Output:**
```bash
# Real-time monitoring
claude -p "Complex task" --output-format stream-json | while IFS= read -r line; do
  echo "$line" | jq '.type' # Show each event type
done
```

---

### Multi-Turn Conversations

#### Resume Most Recent Session

```bash
# Continue the last conversation
claude --continue "Refactor for performance"

# With headless mode
claude --continue "Fix linting issues" --no-interactive
```

#### Resume Specific Session

```bash
# Resume by session ID
claude --resume 550e8400-e29b-41d4... "Add error handling" --no-interactive
```

#### Streaming JSON Input for Multi-Turn

```bash
# Send multi-turn conversation without relaunching
echo '{"type":"user","message":{"role":"user","content":[{"type":"text","text":"Explain this code"}]}}' | \
  claude -p --output-format=stream-json --input-format=stream-json
```

---

### Configuration Flags

| Flag | Purpose | Example |
|------|---------|---------|
| `-p, --print` | Enable headless mode | `claude -p "task"` |
| `--output-format` | Specify text, json, or stream-json | `--output-format json` |
| `--input-format` | Accept stream-json input | `--input-format stream-json` |
| `--resume <id>` | Continue specific session | `--resume abc123...` |
| `--continue` | Resume most recent session | `--continue "next task"` |
| `--allowedTools` | Whitelist permitted tools | `--allowedTools "Bash,Read"` |
| `--permission-mode` | Set permission handling | `--permission-mode acceptEdits` |
| `--mcp-config` | Load MCP server definitions | `--mcp-config .mcp.json` |
| `--verbose` | Enable detailed logging | `--verbose` |
| `--no-interactive` | Force non-interactive mode | `--no-interactive` |

---

### CI/CD Integration Examples

#### GitHub Actions: Automated Code Review

```yaml
# .github/workflows/claude-review.yml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0 # Get full history for diff

      - name: Install Claude Code
        run: |
          npm install -g @anthropic-ai/claude-code

      - name: Run Security Review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude -p "Review this PR for security vulnerabilities. Focus on authentication, input validation, and data handling." \
            --output-format json > review.json

      - name: Parse and Comment
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const review = JSON.parse(fs.readFileSync('review.json', 'utf8'));

            await github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `## 🤖 Claude Code Review\n\n${review.response}`
            });

      - name: Check for Critical Issues
        run: |
          # Fail if critical security issues found
          if grep -i "critical\|severe" review.json; then
            echo "Critical security issues found!"
            exit 1
          fi
```

#### GitLab CI: Test Runner with Coverage

```yaml
# .gitlab-ci.yml
claude-test-coverage:
  stage: test
  image: node:20
  before_script:
    - npm install -g @anthropic-ai/claude-code
  script:
    - |
      claude -p "Run all tests and generate a coverage report.
                 Identify any files with <80% coverage." \
        --allowedTools "Bash" \
        --permission-mode acceptEdits \
        --output-format json > coverage-report.json

    - cat coverage-report.json | jq -r '.response'

  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

  only:
    - merge_requests
    - main
```

#### ChatKit Deployment Automation

```bash
#!/bin/bash
# scripts/deploy-chatkit.sh

set -e

echo "Starting ChatKit deployment..."

# Step 1: Validate configuration
echo "Validating ChatKit configuration..."
claude -p "Validate that OPENAI_API_KEY and VITE_CHATKIT_API_DOMAIN_KEY are set.
          Check backend/frontend port configuration for conflicts." \
  --output-format json > validation.json

if jq -e '.response | test("error|failed|invalid"; "i")' validation.json; then
  echo "❌ Configuration validation failed"
  cat validation.json | jq -r '.response'
  exit 1
fi

echo "✅ Configuration validated"

# Step 2: Run tests
echo "Running tests..."
claude -p "Run backend tests with: cd backend && uv run pytest
          Run frontend tests with: cd frontend && npm test
          Report any failures." \
  --allowedTools "Bash" \
  --permission-mode acceptEdits \
  --output-format json > test-results.json

if jq -e '.response | test("failed|error"; "i")' test-results.json; then
  echo "❌ Tests failed"
  cat test-results.json | jq -r '.response'
  exit 1
fi

echo "✅ All tests passed"

# Step 3: Build frontend
echo "Building frontend..."
cd frontend
npm run build

# Step 4: Deploy services
echo "Starting services..."
cd ../backend
uv run uvicorn app.main:app --host 0.0.0.0 --port 8000 &
BACKEND_PID=$!

cd ../frontend
npm run preview &
FRONTEND_PID=$!

# Step 5: Health check
sleep 5
echo "Running health check..."
claude -p "Verify ChatKit endpoints:
          - Backend: http://localhost:8000/chatkit (should respond)
          - Frontend: http://localhost:5170 (should load)
          Report any connectivity issues." \
  --output-format json > health-check.json

if jq -e '.response | test("error|failed|unavailable"; "i")' health-check.json; then
  echo "❌ Health check failed"
  kill $BACKEND_PID $FRONTEND_PID
  exit 1
fi

echo "✅ Deployment successful!"
echo "Backend PID: $BACKEND_PID"
echo "Frontend PID: $FRONTEND_PID"
```

---

### Best Practices for Headless Mode

1. **Set Timeouts**
   ```bash
   # Prevent hanging in CI/CD
   timeout 300 claude -p "Long-running task"
   ```

2. **Capture Output for Parsing**
   ```bash
   # Use stream-json for programmatic processing
   claude -p "task" --output-format stream-json | jq '.type'
   ```

3. **Handle Errors**
   ```bash
   # Check exit codes
   if ! claude -p "validate config" --output-format json > result.json; then
     echo "Claude failed with exit code: $?"
     exit 1
   fi
   ```

4. **Use Specific Prompts**
   ```bash
   # ❌ Ambiguous
   claude -p "check the code"

   # ✅ Specific
   claude -p "Review backend/app/chat.py for security issues in authentication"
   ```

5. **Limit Scope**
   ```bash
   # Focus on specific files or concerns
   claude -p "Analyze only the authentication module for SQL injection vulnerabilities" \
     --allowedTools "Read,Grep"
   ```

6. **Respect Rate Limits**
   ```bash
   # Add delays between requests
   for file in *.py; do
     claude -p "Review $file for bugs"
     sleep 2
   done
   ```

---

### Error Handling

**Check Exit Codes:**
```bash
claude -p "task" --output-format json
EXIT_CODE=$?

if [ $EXIT_CODE -ne 0 ]; then
  echo "Claude failed with exit code: $EXIT_CODE"
  # Handle error
fi
```

**Parse JSON Errors:**
```bash
RESULT=$(claude -p "task" --output-format json)

if echo "$RESULT" | jq -e '.is_error' > /dev/null; then
  echo "Error occurred:"
  echo "$RESULT" | jq -r '.error_message'
fi
```

---

## 3. MCP (Model Context Protocol) Integration

### What is MCP?

**Model Context Protocol (MCP)** is an open-source standard enabling Claude Code to integrate with external tools and services. MCP allows access to:
- Databases (PostgreSQL, MongoDB)
- APIs (GitHub, Linear, Stripe)
- Cloud services (AWS, Azure, GCP)
- Development tools (Figma, Sentry)

### Current Project MCP Servers

Your `.claude/` configuration uses these MCP servers:

#### 1. **context7** - Library Documentation
```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@context7/mcp-server"]
    }
  }
}
```

**Available Tools:**
- `mcp__context7__resolve-library-id` - Find library documentation
- `mcp__context7__get-library-docs` - Retrieve docs for a library

**Usage in Agents:**
```yaml
---
name: chatkit-server-expert
tools: mcp__context7__resolve-library-id, mcp__context7__get-library-docs
---

Before implementing, always fetch latest docs:
```bash
# Resolve library
mcp__context7__resolve-library-id("chatkit-python")

# Get documentation
mcp__context7__get-library-docs("/openai/chatkit-python", topic="widgets")
```
```

#### 2. **azure** - Azure Cloud Operations
```json
{
  "mcpServers": {
    "azure": {
      "command": "npx",
      "args": ["-y", "@azure/mcp-server"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "${AZURE_SUBSCRIPTION_ID}"
      }
    }
  }
}
```

**Available Tools:**
- `mcp__azure__list_resources`
- `mcp__azure__execute_cli_command`
- `mcp__azure__create_resource`

#### 3. **sequential-thinking** - Extended Reasoning
```json
{
  "mcpServers": {
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@anthropic/sequential-thinking-mcp"]
    }
  }
}
```

**Used by:** WFGY reasoning agents for multi-step logic validation

#### 4. **playwright** - Browser Automation
```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp-server"]
    }
  }
}
```

**Available Tools:**
- `mcp__playwright__browser_navigate`
- `mcp__playwright__browser_snapshot`
- `mcp__playwright__browser_click`
- `mcp__playwright__browser_type`

---

### Adding MCP Servers

#### Method 1: Command Line (Recommended for Cloud Services)

```bash
# Add remote HTTP server
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Add local stdio server with environment variables
claude mcp add --transport stdio airtable \
  --env AIRTABLE_API_KEY=YOUR_KEY \
  -- npx -y airtable-mcp-server

# Add GitHub integration
claude mcp add --transport http github https://mcp.github.com/mcp
```

#### Method 2: Configuration Files

**Project-Level (`.claude/settings.json`):**
```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@postgres/mcp-server"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

**Team-Shared (`.mcp.json`):**
```json
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}"
      }
    }
  }
}
```

**User-Global (`~/.claude/settings.json`):**
```json
{
  "mcpServers": {
    "linear": {
      "command": "npx",
      "args": ["-y", "@linear/mcp-server"],
      "env": {
        "LINEAR_API_KEY": "${LINEAR_API_KEY}"
      }
    }
  }
}
```

---

### MCP Server Management

```bash
# List all configured servers
claude mcp list

# Get details about a server
claude mcp get github

# Remove a server
claude mcp remove notion

# Check server status (in Claude Code)
/mcp
```

---

### Tool Naming Convention

MCP tools follow the pattern: **`mcp__[servername]__[toolname]`**

Examples:
- `mcp__github__list_prs` - List pull requests
- `mcp__postgres__execute_query` - Run SQL query
- `mcp__linear__create_issue` - Create Linear issue

**Using in Slash Commands:**
```bash
# Invoke MCP tool as slash command
/mcp__github__list_prs owner=anthropics repo=claude-code
```

---

### Authentication

#### OAuth 2.0 Flow

```bash
# Trigger authentication (in Claude Code)
/mcp

# Browser opens for OAuth flow
# Tokens stored securely and auto-refresh
```

#### API Key Environment Variables

```json
{
  "mcpServers": {
    "stripe": {
      "command": "npx",
      "args": ["-y", "@stripe/mcp-server"],
      "env": {
        "STRIPE_API_KEY": "${STRIPE_API_KEY}"
      }
    }
  }
}
```

**Environment Variable Expansion:**
- `${VAR}` - Required variable (error if missing)
- `${VAR:-default}` - Optional with default value

---

### ChatKit-Specific MCP Recommendations

#### OpenAI MCP Server (if available)

```json
{
  "mcpServers": {
    "openai": {
      "command": "npx",
      "args": ["-y", "@openai/mcp-server"],
      "env": {
        "OPENAI_API_KEY": "${OPENAI_API_KEY}"
      }
    }
  }
}
```

**Use for:**
- Validating ChatKit configurations
- Testing agent responses
- Debugging tool calls

#### Postgres for Session Storage

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@postgres/mcp-server"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    }
  }
}
```

**Use for:**
- Querying ChatKit thread history
- Analyzing conversation patterns
- Debugging memory store issues

---

### Output Management

**Token Limits:**
- Warning threshold: 10,000 tokens
- Default maximum: 25,000 tokens

**Configure via environment:**
```bash
export MAX_MCP_OUTPUT_TOKENS=50000
```

---

### Best Practices

1. **Document Required MCP Servers in CLAUDE.md**
   ```markdown
   ## Required MCP Servers

   This project requires:
   - context7 (for library documentation)
   - azure (for cloud deployments)

   Install with:
   ```bash
   claude mcp add --transport stdio context7 -- npx -y @context7/mcp-server
   ```
   ```

2. **Use Official Servers from Verified Sources**
   - Prefer `@official/mcp-server` packages
   - Verify publisher on npm
   - Check GitHub stars and activity

3. **Test MCP Connectivity Before Relying on Tools**
   ```bash
   # Test in Claude Code
   /mcp
   # Verify all servers show "connected"
   ```

4. **Handle MCP Failures Gracefully in Agents**
   ```yaml
   ---
   name: data-analyst
   description: Uses postgres MCP. Falls back to manual queries if MCP unavailable.
   ---

   Try using mcp__postgres__execute_query first.
   If unavailable, ask user to run query manually.
   ```

5. **Prefer Built-in Tools Over MCP When Available**
   ```yaml
   # ❌ Unnecessary MCP usage
   tools: mcp__bash__execute

   # ✅ Use built-in tool
   tools: Bash
   ```

---

### Enterprise Features

**Managed MCP Configuration:**

Admins can deploy organization-wide configs:

**macOS:** `/Library/Application Support/ClaudeCode/managed-mcp.json`
**Windows:** `C:\ProgramData\ClaudeCode\managed-mcp.json`

**With allowlists/denylists:**
```json
{
  "allowedServers": ["github", "linear", "notion"],
  "deniedServers": ["experimental-*"],
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://mcp.github.com/enterprise"
    }
  }
}
```

---

## 4. Extended Thinking

### What is Extended Thinking?

Extended thinking enables Claude to **show its step-by-step reasoning process** before delivering final answers. This provides enhanced reasoning capabilities for complex tasks with transparency into the thought process.

**Key Quote from Official Docs:**
> "Extended thinking gives Claude enhanced reasoning capabilities for complex tasks, while providing varying levels of transparency into its step-by-step thought process before it delivers its final answer."

---

### When to Use Extended Thinking

✅ **Best for:**
- Complex mathematics and logic problems
- Detailed code analysis and debugging
- Architectural decision-making
- Multi-step reasoning tasks
- Trade-off analysis
- Security vulnerability assessment

❌ **Not needed for:**
- Simple code formatting
- Straightforward bug fixes
- Documentation updates
- Basic questions

**Works with tool use:** Claude can reason about which tools to call and how to interpret results.

---

### How to Trigger Extended Thinking

#### In Claude Code: Natural Language

```bash
# Basic extended thinking
"think about the best way to implement user authentication"

# More computation
"think hard about optimizing the ChatKit widget rendering performance"

# Maximum computation
"think harder about whether to use microservices or monolith architecture"
```

**Thinking Levels:**
- `"think"` → Extended thinking (~4,000 tokens)
- `"think hard"` or `"megathink"` → Deep thinking (~10,000 tokens)
- `"think harder"` or `"ultrathink"` → Maximum thinking (~32,000 tokens)

**Official Guidance:**
> "think" < "think hard" < "think harder" < "ultrathink" map to increasing levels of computational budget

---

#### Via API: Thinking Object

```json
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 4096,
  "thinking": {
    "type": "enabled",
    "budget_tokens": 16000
  },
  "messages": [
    {
      "role": "user",
      "content": "Analyze this code for security vulnerabilities"
    }
  ]
}
```

**Budget Tokens:**
- Minimum: 1,024 tokens
- Recommended for complex tasks: 16,000+ tokens
- Maximum: Approximately 32,000 tokens

---

### Token Costs

**Pricing:**
Extended thinking uses standard pricing for:
- **Thinking process:** Output token pricing
- **Thinking in subsequent requests:** Input token pricing (from previous turn)
- **Final answer:** Standard output token pricing

**You're billed for the FULL thinking tokens Claude generates internally**, not just summaries shown in responses.

**Cost Example:**
```
Prompt: "think harder about database schema design"

Thinking tokens used: 25,000 (output pricing)
Final response tokens: 1,500 (output pricing)
Next turn includes thinking: 25,000 (input pricing)

Total cost = 25,000 * output_rate + 1,500 * output_rate + (next turn: 25,000 * input_rate)
```

---

### Model Differences

| Model | Thinking Output | Interleaved Thinking |
|-------|----------------|---------------------|
| **Claude Sonnet 3.7** | Full thinking output | ❌ Not supported |
| **Claude 4 Opus** | Summarized thinking | ✅ Supported with beta header |
| **Claude 4 Sonnet** | Summarized thinking | ✅ Supported with beta header |

**Interleaved Thinking:** Thinking between tool calls (Claude 4 only with `interleaved-thinking-2025-05-14` beta header)

---

### Examples

#### Architecture Decision

```bash
User: "think harder about whether we should use microservices or monolith for the ChatKit backend"

Claude (Thinking): [
  Considering team size (2-3 developers):
  - Microservices add operational complexity
  - Monolith faster to develop initially

  Analyzing scaling requirements:
  - ChatKit expected load: <1000 concurrent users
  - Monolith can handle this scale easily

  Evaluating deployment complexity:
  - Microservices: Multiple containers, orchestration
  - Monolith: Single deployment, simpler CI/CD

  Assessing development velocity:
  - Early stage: Monolith enables faster iteration
  - Can extract microservices later if needed

  Conclusion: Recommend monolith initially with modular architecture
  to enable future extraction if scaling requires it.
]

Claude (Final Answer): "Based on my analysis, I recommend starting with a **well-structured monolith** for the ChatKit backend. Here's why:

1. **Team Size**: With 2-3 developers, microservices would add unnecessary operational overhead
2. **Scale**: Expected load (<1000 users) is well within monolith capacity
3. **Development Speed**: Monolith enables faster iteration in early stages
4. **Future-Proofing**: Design with clear module boundaries to enable extraction later

**Implementation:**
- Use FastAPI with clear module separation
- Keep services loosely coupled (dependency injection)
- Design for potential future extraction

This approach maximizes development velocity now while preserving optionality for scaling later."
```

#### Security Vulnerability Analysis

```bash
User: "think about security issues in backend/app/chat.py"

Claude (Thinking): [
  Analyzing authentication flow:
  - Tool calls don't verify user permissions
  - No rate limiting on tool execution

  Checking input validation:
  - @function_tool parameters lack validation
  - User-provided tool arguments passed directly

  Examining data handling:
  - Widget data may contain user PII
  - No sanitization before streaming to frontend

  Identifying risks:
  - HIGH: Lack of input validation enables injection
  - MEDIUM: No rate limiting allows abuse
  - MEDIUM: PII in widgets without sanitization

  Prioritizing fixes:
  1. Add input validation to all tools
  2. Implement rate limiting
  3. Sanitize widget data
]

Claude (Final Answer): "I've identified **3 security concerns** in the ChatKit implementation:

### 🔴 HIGH: Input Validation Missing
**Issue:** `@function_tool` parameters lack validation
**Risk:** Injection attacks, malformed data crashes
**Fix:**
```python
from pydantic import BaseModel, validator

class UserProfileInput(BaseModel):
    user_id: str

    @validator('user_id')
    def validate_user_id(cls, v):
        if not v.isalnum() or len(v) > 100:
            raise ValueError('Invalid user_id format')
        return v
```

[Additional recommendations...]"
```

---

### Integration with MCP Sequential Thinking

The `mcp__sequential-thinking__sequentialthinking` tool provides:
- Multi-step reasoning chains
- Explicit logic validation
- Hypothesis testing
- Contradiction detection

**Used by:** WFGY reasoning agents (decision-navigator, logic-synthesizer)

```yaml
---
name: logic-synthesizer
tools: mcp__sequential-thinking__sequentialthinking
---

For complex multi-path reasoning:
1. Use sequential-thinking MCP for step-by-step validation
2. Explore parallel solution paths
3. Synthesize optimal solution from validated paths
```

---

### Best Practices

1. **Be Specific About What to Think About**
   ```bash
   # ❌ Vague
   "think about this"

   # ✅ Specific
   "think harder about the security implications of exposing this API endpoint publicly"
   ```

2. **Use for Decisions, Not Routine Implementation**
   ```bash
   # ❌ Waste of tokens
   "think harder about how to format this code"

   # ✅ Good use
   "think harder about the trade-offs between PostgreSQL and MongoDB for ChatKit thread storage"
   ```

3. **Review Thinking Output, Verify Conclusions**
   - Extended thinking shows reasoning, but conclusions still need verification
   - Test assumptions with small experiments
   - Validate architectural decisions with prototypes

4. **Budget Tokens for High-Value Problems**
   ```bash
   # Routine: No extended thinking
   "Fix the typo in README"

   # Complex: Basic thinking
   "think about optimizing this database query"

   # Critical decision: Maximum thinking
   "think harder about migrating from FastAPI to Django for this project"
   ```

5. **Combine with Explore → Plan Workflow**
   ```bash
   # EXPLORE
   "Read the codebase and understand the current architecture"

   # PLAN (with extended thinking)
   "think harder about the best approach to add real-time features to ChatKit"

   # CODE
   "Implement the plan we developed"
   ```

---

## 5. Checkpointing & Session Recovery

### Overview

**Checkpointing** is an automatic safety feature that tracks Claude's file edits, enabling quick recovery from unwanted changes without manual intervention.

**Official Description:**
> "Every user prompt creates a new checkpoint. Sessions persist across conversations for 30 days (configurable)."

---

### How Checkpoints Work

#### Automatic Creation
- ✅ Every user prompt creates a checkpoint
- ✅ File edits tracked automatically via Write, Edit, MultiEdit tools
- ✅ Sessions persist for 30 days (default)
- ❌ Bash commands (rm, mv, cp) **NOT** tracked
- ❌ External manual file edits **NOT** captured

#### What's Tracked
```
User Prompt 1 → Checkpoint 1 (before edits)
  Claude edits file A, B
User Prompt 2 → Checkpoint 2 (after A,B; before C)
  Claude edits file C
User Prompt 3 → Checkpoint 3 (after C; before D,E)
  Claude edits file D, E
```

---

### Accessing Checkpoints

**Two Methods:**

1. **Keyboard:** Press **Esc twice**
2. **Command:** Type `/rewind`

Both open the checkpoint menu showing:
- Timestamp of each checkpoint
- User prompt associated with checkpoint
- Files modified since checkpoint

---

### Rewind Options

When you select a checkpoint, choose restoration scope:

#### 1. Conversation Only
- ✅ Return conversation to earlier point
- ❌ Keep all code changes
- **Use when:** You want to explore different instructions while keeping recent code

**Example:**
```bash
Checkpoint 1: "Add user authentication"
  [Files: auth.py, routes.py modified]
Checkpoint 2: "Add password reset"
  [Files: reset.py, email.py modified]

→ Rewind conversation to Checkpoint 1
→ Keep code: auth.py, routes.py, reset.py, email.py all stay modified
→ Conversation: Back to "Add user authentication" state
```

#### 2. Code Only
- ✅ Revert files to checkpoint state
- ❌ Preserve full conversation history
- **Use when:** Recent code broke something, but conversation context is valuable

**Example:**
```bash
Checkpoint 2: Working state
Checkpoint 3: Attempted refactoring (introduced bug)

→ Rewind code to Checkpoint 2
→ Conversation: Keep entire history (including failed refactor discussion)
→ Code: Reverted to working state before bug
```

#### 3. Both
- ✅ Restore code AND conversation
- **Use when:** Exploring alternative implementations from scratch

**Example:**
```bash
Checkpoint 1: "Try React for frontend"
  [Implemented React components]
Checkpoint 2: "Actually, try Vue instead"
  [Started Vue components]

→ Rewind both to Checkpoint 1
→ Everything back to React state
→ Can now try different approach (e.g., Svelte)
```

---

### Use Cases

#### Safe Experimentation

```bash
User: "Add real-time updates to ChatKit"
# Checkpoint created automatically

Claude: [Implements WebSocket approach]

User: "This adds too much complexity. Rewind and try polling instead."
# Rewind code to previous checkpoint
# Keep conversation history (Claude knows WebSocket was complex)

Claude: [Implements simpler polling approach]
```

#### Recovering from Bugs

```bash
User: "Refactor the widget rendering system"
# Checkpoint created

Claude: [Refactors, introduces performance regression]

User: "Performance got worse. Rewind code to before refactoring."
# Rewind code only
# Conversation preserved (Claude aware of performance issue)

Claude: "I see the performance regression. Let me try a different refactoring approach..."
```

#### Exploring Alternatives

```bash
User: "Design a caching layer for ChatKit"
# Checkpoint 1

Claude: [Implements Redis cache]

User: [Tests, not ideal]
User: "Rewind both. Let's try in-memory cache instead."
# Rewind to Checkpoint 1 (both conversation and code)

Claude: [Implements in-memory LRU cache]

User: [Tests, works better]
User: "Great! Now optimize it."
# Checkpoint 2 created

Claude: [Optimizes cache]
```

---

### Best Practices

1. **Create Manual Checkpoints for Milestones**
   ```bash
   # While checkpoints auto-create, you can force new checkpoints by sending any message
   User: "Checkpoint: Backend authentication complete"
   # Creates checkpoint with clear label for finding later
   ```

2. **Use Descriptive Prompts for Easier Checkpoint Identification**
   ```bash
   # ❌ Hard to identify later
   "Make changes"

   # ✅ Clear checkpoint label
   "Add user profile widget to ChatKit (Phase 1: Backend tool)"
   ```

3. **Checkpoint Before Risky Operations**
   ```bash
   User: "Checkpoint: Before database schema migration"
   # Creates explicit checkpoint

   User: "Now migrate the database schema to add user_profiles table"
   # If migration breaks, easy to rewind
   ```

4. **Combine with Git for Permanent History**
   ```bash
   # Checkpoints are session-level (30 days)
   # Git is permanent version control

   User: "Implement feature"
   Claude: [Implements]

   User: "This works. Create a git commit."
   Claude: [Creates commit]
   # Now safe in git, checkpoint still available for experimentation
   ```

5. **Understand Bash Limitations**
   ```bash
   # ❌ NOT tracked by checkpoints
   User: "Run: rm -rf old-code/"
   Claude: [Executes via Bash]
   # If you need to restore old-code/, checkpoints WON'T help

   # ✅ Use git for Bash file operations
   User: "Use git to remove old-code/ directory"
   Claude: [Uses: git rm -r old-code/]
   # Recoverable via git if needed
   ```

---

### Checkpoint Limitations

**What Checkpoints Are NOT:**
- ❌ Not a replacement for version control (use Git)
- ❌ Not permanent (30-day retention)
- ❌ Not collaborative (session-specific)
- ❌ Don't track Bash file operations
- ❌ Don't track external manual edits

**What Checkpoints ARE:**
- ✅ Safety net for Claude Code sessions
- ✅ Quick undo for AI-generated changes
- ✅ Exploration tool for trying alternatives
- ✅ Recovery mechanism for bugs/regressions

---

### ChatKit-Specific Checkpoint Strategy

```bash
# Phase 1: Backend Tool
User: "Checkpoint: Starting backend user profile tool"
Claude: [Implements @function_tool]

# Phase 2: Widget Renderer
User: "Checkpoint: Backend tool works, starting widget renderer"
Claude: [Implements widget]

# Phase 3: Testing
User: "Checkpoint: Widget rendering works, starting integration tests"
Claude: [Adds tests]

# If tests reveal issues with widget:
User: "Rewind code to 'Backend tool works' checkpoint"
# Try different widget approach while keeping working backend tool
```

---

## 6. Output Styles

### Overview

**Output styles** allow Claude Code to function as different types of agents by modifying the system prompt. This changes how Claude communicates and works while preserving core capabilities.

**Official Description:**
> "Output styles directly modify Claude Code's system prompt. Non-default styles exclude standard code generation instructions and replace them with custom directives tailored to specific use cases."

---

### Built-in Output Styles

#### 1. Default (Standard)
Standard software engineering assistance focused on efficient code completion.

**Characteristics:**
- Concise, direct responses
- Focus on implementation
- Minimal explanation
- Code-first approach

**Example:**
```bash
User: "Add input validation to the user profile tool"

Claude (Default): [Immediately shows code]
```python
from pydantic import BaseModel, validator

class UserProfileInput(BaseModel):
    user_id: str

    @validator('user_id')
    def validate_user_id(cls, v):
        if not v.isalnum():
            raise ValueError('user_id must be alphanumeric')
        return v
```
Applied to backend/app/user_profile.py:
...
```
```

#### 2. Explanatory
Provides educational "Insights" explaining implementation choices and codebase patterns.

**Characteristics:**
- Detailed explanations before code
- "Insights" sections explaining why
- Educational tone
- Pattern documentation

**Example:**
```bash
User: "Add input validation to the user profile tool"

Claude (Explanatory):
## Insights

Input validation is crucial for ChatKit tools because:
1. **Security**: Prevents injection attacks via malformed tool arguments
2. **Reliability**: Catches errors early before database queries
3. **Developer Experience**: Clear error messages for debugging

I'll use Pydantic for validation because:
- Already used in ChatKit for widget schemas
- Type-safe with editor support
- Integrates with FastAPI's validation

## Implementation

```python
from pydantic import BaseModel, validator
...
```

This pattern should be applied to all ChatKit tools that accept user input.
```
```

#### 3. Learning (Collaborative)
Collaborative mode where Claude shares insights AND requests user contributions, marked with `TODO(human)` annotations.

**Characteristics:**
- Interactive dialogue
- Asks for user input on design decisions
- `TODO(human)` annotations for user tasks
- Shared learning experience

**Example:**
```bash
User: "Add input validation to the user profile tool"

Claude (Learning):
Let's implement input validation together! I have some questions first:

**Design Decision Needed:**
1. Should we allow special characters in user_id? (e.g., "user-123" vs "user123")
2. What's the maximum length for user_id? I'll suggest 100 chars unless you prefer different.

I'll start with a basic implementation you can refine:

```python
from pydantic import BaseModel, validator

class UserProfileInput(BaseModel):
    user_id: str

    @validator('user_id')
    def validate_user_id(cls, v):
        # TODO(human): Define regex pattern based on user_id format requirements
        if len(v) > 100:
            raise ValueError('user_id too long')
        return v
```

TODO(human): Review and specify:
- Allowed characters in user_id
- Min/max length constraints
- Any domain-specific validation rules
```

---

### Changing Output Styles

#### Via Menu
```bash
/output-style
# Opens interactive menu
# Select: Default, Explanatory, or Learning
```

#### Direct Switch
```bash
/output-style explanatory
# Immediately switches to Explanatory style

/output-style default
# Back to default
```

**Persistence:**
Changes saved to `.claude/settings.local.json` (project-level).

---

### Creating Custom Output Styles

#### Using the Generator

```bash
/output-style:new I want an output style that focuses on test-driven development. Always write tests first, then implementation.
```

Claude generates a custom style file:

**`.claude/output-styles/test-first.md`:**
```yaml
---
name: Test-First TDD
description: Test-driven development focused style. Always writes tests before implementation.
---

# Test-First Development Style

You are a test-driven development expert. Your primary focus is writing tests BEFORE implementation.

## Core Principles

1. **Tests First**: Always write tests before any implementation code
2. **Red-Green-Refactor**: Follow the TDD cycle strictly
3. **Incremental**: Build functionality one test at a time
4. **Coverage**: Aim for 100% coverage of new code

## Workflow

For every new feature or change:

1. **Write failing test** demonstrating desired behavior
2. **Run test** to confirm it fails (Red)
3. **Implement minimal code** to pass test (Green)
4. **Refactor** while keeping tests green
5. **Repeat** for next requirement

## Output Format

```markdown
## Test Case: [Description]

```python
# Test first
def test_user_profile_validation():
    with pytest.raises(ValueError):
        get_user_profile("invalid<>id")
```

**Expected:** Test fails (function doesn't validate yet)

## Implementation

```python
def get_user_profile(user_id: str):
    if not user_id.isalnum():
        raise ValueError("Invalid user_id")
    # ...
```

**Verify:** Test now passes
```

## Example Usage

User: "Add email validation to user profiles"

Response:
### Test Case: Email Validation

First, the failing test:
```python
def test_user_profile_email_validation():
    # Valid emails should pass
    assert validate_email("user@example.com") == True

    # Invalid emails should fail
    with pytest.raises(ValueError):
        validate_email("not-an-email")
```

Running this test now would fail because validate_email() doesn't exist.

### Implementation

Minimal code to pass:
```python
import re

def validate_email(email: str) -> bool:
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    if not re.match(pattern, email):
        raise ValueError("Invalid email format")
    return True
```

**Test Status:** ✅ Passes

### Next Steps
Now that tests pass, we can refactor for edge cases...
```
```

#### Manual Creation

Create `.claude/output-styles/your-style.md`:

```yaml
---
name: Security-Focused
description: Security-first code review and implementation style
---

# Security-Focused Development

You are a security-conscious developer. Every suggestion prioritizes security.

## Security Checklist

For every code change, verify:

1. **Input Validation** - All user input sanitized
2. **Authentication** - Proper auth checks
3. **Authorization** - Correct permission levels
4. **Data Protection** - Sensitive data encrypted
5. **Logging** - Security events logged
6. **Error Handling** - No sensitive info in errors

## Output Format

Always include security analysis:

```markdown
## Security Analysis

**Risks Identified:**
- [List security risks in current/proposed code]

**Mitigations:**
- [Security measures implemented]

**Recommendations:**
- [Additional security improvements]

## Implementation
[Code with security measures]
```
```

---

### Storage Locations

**User-Level:**
```bash
~/.claude/output-styles/my-style.md
# Available across all projects
```

**Project-Level:**
```bash
.claude/output-styles/project-specific.md
# Only available in this project
```

---

### Key Distinctions

#### Output Styles vs. CLAUDE.md

| Aspect | Output Styles | CLAUDE.md |
|--------|--------------|-----------|
| **Purpose** | Replace entire system prompt | Supplement system prompt |
| **Scope** | How Claude communicates | Project context/conventions |
| **Switchable** | Yes, via `/output-style` | No, always loaded |
| **Use for** | Agent behavior/tone | Project-specific info |

**Example:**
```markdown
# CLAUDE.md (Always active)
This is a ChatKit project using FastAPI + React.
Run with: npm start

# Output Style (Switchable)
/output-style explanatory → Educational tone with insights
/output-style default → Concise, code-first
```

#### Output Styles vs. Agents

| Aspect | Output Styles | Agents |
|--------|--------------|--------|
| **Purpose** | Change main agent behavior | Specialized task executors |
| **Tools** | Inherit all tools | Configurable tool list |
| **Invocation** | Manual switch (`/output-style`) | Auto-triggered by description |
| **Scope** | Entire conversation | Specific task |

**Example:**
```bash
# Output Style: Changes how ALL responses work
/output-style test-first
→ Every response now follows TDD workflow

# Agent: Specialized for specific task
"Use code-reviewer to analyze security"
→ Code-reviewer agent handles just this task
→ Returns to normal mode after
```

#### Output Styles vs. Custom Slash Commands

| Aspect | Output Styles | Slash Commands |
|--------|--------------|----------------|
| **What they are** | Stored system prompts | Stored user prompts |
| **Apply to** | All responses | Single invocation |
| **Persistence** | Until switched | One-time execution |

**Example:**
```bash
# Output Style (persists)
/output-style explanatory
→ All responses now explanatory until switched back

# Slash Command (one-time)
/chatkit:validate-config
→ Runs validation once
→ Next response back to normal
```

---

### ChatKit-Specific Output Styles

#### 1. Widget-First Style

**`.claude/output-styles/widget-first.md`:**
```yaml
---
name: Widget-First ChatKit
description: Prioritizes ChatKit widget design and user experience in all responses
---

# Widget-First ChatKit Development

For every ChatKit feature, design the widget UI first, then implement backend.

## Workflow

1. **Design Widget**: Sketch widget layout with sections, tables, metadata
2. **Define Schema**: Create widget TypeScript/Python types
3. **Implement Backend**: Build tool that generates widget data
4. **Test Rendering**: Verify widget displays correctly

## Output Format

```markdown
## Widget Design

**Widget Name:** [Name]
**Sections:**
1. [Section 1]: [Purpose]
2. [Section 2]: [Purpose]

**Visual Mockup:**
┌─────────────────────────┐
│ Title: User Profile     │
├─────────────────────────┤
│ Photo  │ Name: John     │
│        │ Email: john@.. │
├─────────────────────────┤
│ Recent Activity Table   │
└─────────────────────────┘

## Widget Schema
```python
# Widget structure
```

## Backend Implementation
```python
# Tool that generates widget
```
```
```

#### 2. API-First Style

**`.claude/output-styles/api-first.md`:**
```yaml
---
name: API-First ChatKit
description: Designs backend APIs before frontend, with OpenAPI specs
---

# API-First ChatKit Development

Design and document APIs using OpenAPI before implementation.

## Workflow

1. **Define API Contract**: OpenAPI specification
2. **Generate Types**: TypeScript/Python from spec
3. **Implement Backend**: Following contract
4. **Implement Frontend**: Against contract
5. **Validate**: Schema validation on both ends

## Output Format

```markdown
## API Contract

```yaml
# OpenAPI 3.0 specification
paths:
  /api/user-profile:
    post:
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                user_id:
                  type: string
```

## Implementation
[Backend code following contract]

## Frontend Usage
[TypeScript client code]
```
```

---

### Best Practices

1. **Choose Style Based on Context**
   ```bash
   # Learning new codebase → Explanatory
   /output-style explanatory

   # Fast feature development → Default
   /output-style default

   # Pair programming with junior dev → Learning
   /output-style learning
   ```

2. **Create Project-Specific Styles**
   ```bash
   # For ChatKit: Widget-first approach
   /output-style:new Create a ChatKit style that always designs widgets first

   # Save to project
   → .claude/output-styles/chatkit-widget-first.md
   ```

3. **Document Your Styles**
   ```markdown
   # .claude/README.md

   ## Available Output Styles

   - **widget-first**: Design widget UI before backend (for ChatKit features)
   - **test-first**: TDD workflow (write tests before code)
   - **api-first**: OpenAPI contract before implementation

   Switch with: `/output-style widget-first`
   ```

4. **Combine with CLAUDE.md**
   ```markdown
   # CLAUDE.md
   ## Preferred Output Style

   For ChatKit features, use `/output-style widget-first`
   For backend refactoring, use `/output-style test-first`
   ```

---

## 7. Skills System Deep Dive

### Overview

Skills are **reusable, model-invoked workflows** stored as SKILL.md files. Claude automatically decides when to use them based on your request and the skill's description.

**Key Characteristics:**
- **Model-invoked**: Claude activates automatically (not manual like slash commands)
- **Reusable**: One skill, many uses across projects
- **Self-contained**: SKILL.md + optional supporting files (scripts, templates)
- **Discoverable**: Description field helps Claude find relevant skills

---

### Anatomy of a SKILL.md File

**Required Structure:**

```yaml
---
name: skill-name-lowercase-with-hyphens
description: Brief description of what this Skill does and when Claude should use it (max 1024 characters)
---

# Skill Name (Human-Readable)

## Instructions

Clear, step-by-step guidance for Claude to follow when executing this skill.

## Examples

Concrete examples showing how to use this skill.

## Supporting Files (optional)

Reference to scripts, templates, or documentation in this skill directory.
```

**Field Requirements:**
- **name**: Lowercase letters, numbers, hyphens only (max 64 characters)
- **description**: Triggers skill invocation (max 1024 characters, CRITICAL for discovery)

---

### Example: ChatKit Tool Generator Skill

**`.claude/skills/chatkit-tool-generator/SKILL.md`:**

```yaml
---
name: chatkit-tool-generator
description: Generates complete ChatKit agent tools with backend @function_tool, widget renderer, and optional frontend client tool handler. Use when adding new tools to ChatKit applications, especially for tools that display widgets or trigger client actions.
---

# ChatKit Tool Generator

Automatically scaffolds complete ChatKit tools following project conventions.

## Instructions

When a user requests a new ChatKit tool:

1. **Determine Tool Type**:
   - Widget tool: Returns data for UI display
   - Client tool: Triggers frontend action (theme switch, navigation)
   - Data tool: Returns JSON only (no UI)

2. **Generate Backend Tool**:
   ```python
   # backend/app/{tool_name}.py

   from agents import function_tool, RunContextWrapper
   from .constants import FactAgentContext

   @function_tool(description_override="Tool description")
   async def {tool_name}(
       ctx: RunContextWrapper[FactAgentContext],
       param1: str,
       param2: int,
   ) -> dict[str, str]:
       """
       Tool docstring explaining functionality.

       Args:
           param1: Description
           param2: Description

       Returns:
           Dictionary with tool results
       """
       # For widget tools:
       await ctx.context.stream_widget(widget_data, copy_text="...")

       # For client tools:
       ctx.context.client_tool_call = ClientToolCall(
           name="client_tool_name",
           arguments={"key": "value"}
       )

       return {"result": "data"}
   ```

3. **Create Widget Renderer (if widget tool)**:
   ```python
   # backend/app/{tool_name}_widget.py

   from typing import Any

   def create_{tool_name}_widget(data: dict[str, Any]) -> dict[str, Any]:
       """Create ChatKit widget for {tool_name} data."""
       return {
           "type": "widget",
           "widget": {
               "type": "sections",
               "title": "Widget Title",
               "sections": [
                   {
                       "type": "table",
                       "title": "Section Title",
                       "rows": [...],
                   }
               ],
           },
       }
   ```

4. **Register Tool**:
   Add to `backend/app/chat.py`:
   ```python
   from .{tool_name} import {tool_name}

   # In FactAssistantServer.__init__():
   self.assistant = Agent(
       ...,
       tools=[
           ...,
           {tool_name},
       ],
   )
   ```

5. **Add Frontend Handler (if client tool)**:
   Update `frontend/src/components/ChatKitPanel.tsx`:
   ```typescript
   onClientToolCall={(call: ClientToolCall) => {
     if (call.name === "client_tool_name") {
       // Handle client action
     }
     return { type: "client_tool_call", ... };
   }}
   ```

6. **Test**:
   ```bash
   # Start backend
   cd backend && uv run uvicorn app.main:app --reload

   # Test in chat
   "Use the new {tool_name} tool with param1='test'"
   ```

## Supporting Files

- `templates/widget_template.py` - Widget boilerplate
- `templates/tool_template.py` - Tool boilerplate
- `scripts/validate_tool.py` - Validation script

## Examples

**Example 1: Weather Widget Tool**
```bash
User: "Add a weather lookup tool that shows a widget"

Claude (using chatkit-tool-generator skill):
[Generates complete weather tool with:]
- backend/app/weather.py (@function_tool)
- backend/app/weather_widget.py (renderer)
- Registration in chat.py
- Test instructions
```

**Example 2: Theme Switch Client Tool**
```bash
User: "Add a tool to switch between light/dark themes"

Claude (using chatkit-tool-generator skill):
[Generates:]
- backend/app/theme.py (client tool call)
- frontend update for onClientToolCall
- Theme application logic
```
```

---

### Skill Discovery: The Description Field

**Critical:** The description field is how Claude discovers when to use your skill.

**❌ Poor Description:**
```yaml
description: Generates ChatKit tools
```
Claude doesn't know WHEN to use this.

**✅ Good Description:**
```yaml
description: Generates complete ChatKit agent tools with backend @function_tool, widget renderer, and optional frontend client tool handler. Use when adding new tools to ChatKit applications, especially for tools that display widgets or trigger client actions.
```
Claude knows to use this when:
- User asks to "add a tool"
- ChatKit application context
- Widget or client tool mentioned

**Best Practices for Descriptions:**
- ✅ Explain WHAT the skill does
- ✅ Explain WHEN to use it
- ✅ Include trigger keywords (e.g., "ChatKit tool", "widget", "agent tool")
- ✅ Mention specific scenarios ("when adding new tools")
- ✅ Keep under 1024 characters

---

### Storage Locations

#### Personal Skills (All Projects)
```bash
~/.claude/skills/
├── chatkit-tool-generator/
│   └── SKILL.md
└── test-suite-generator/
    └── SKILL.md
```

#### Project Skills (This Project Only)
```bash
.claude/skills/
├── cloudflare-manager/
│   ├── SKILL.md
│   ├── scripts/
│   │   └── deploy.ts
│   └── templates/
│       └── worker.ts
└── linear-todo-sync/
    ├── SKILL.md
    └── sync.py
```

---

### Supporting Files

Skills can include additional files:

```bash
.claude/skills/chatkit-tool-generator/
├── SKILL.md                    # Required: Main skill file
├── reference.md                # Optional: Documentation
├── examples.md                 # Optional: Usage examples
├── templates/
│   ├── tool_template.py
│   └── widget_template.py
└── scripts/
    └── validate_tool.py
```

**Reference in SKILL.md:**
```markdown
## Templates

See `templates/tool_template.py` for boilerplate.

## Validation

Run `scripts/validate_tool.py` after generation.
```

---

### Skills vs. Commands vs. Agents

| Feature | Skills | Commands | Agents |
|---------|--------|----------|--------|
| **Invocation** | Auto (model-invoked) | Manual (`/command`) | Auto (description-based) |
| **Scope** | Multi-step workflows | Single tasks | Domain expertise |
| **Can include scripts** | ✅ Yes | ✅ Yes | ❌ No (uses tools) |
| **Reusable across projects** | ✅ Yes (personal) | ❌ No (project-specific) | ✅ Yes |
| **Best for** | Repeatable workflows | Project commands | Specialized tasks |

**Example Decision Tree:**
```
Need to automate a workflow?
  ├─ Same steps every time → Skill
  ├─ Project-specific → Command
  └─ Needs AI reasoning → Agent
```

---

### Creating Your First Skill

#### Option 1: Using Skill Generator Command

```bash
/skills:templates:enhanced-simple-skill-template

# Or for multi-file skills:
/skills:templates:enhanced-multi-file-skill-template
```

#### Option 2: Manual Creation

**1. Create Directory:**
```bash
mkdir -p .claude/skills/my-skill
```

**2. Create SKILL.md:**
```bash
cat > .claude/skills/my-skill/SKILL.md << 'EOF'
---
name: my-skill
description: What this skill does and when to use it
---

# My Skill

## Instructions

Step-by-step instructions for Claude.

## Examples

Concrete usage examples.
EOF
```

**3. Test:**
```bash
# In Claude Code, trigger the skill
"[Request that matches skill description]"

# Claude should automatically use the skill
```

---

### Advanced: Skill with Scripts

**Example: Test Coverage Analyzer Skill**

**`.claude/skills/test-coverage/SKILL.md`:**
```yaml
---
name: test-coverage-analyzer
description: Analyzes test coverage and identifies untested code paths. Use when reviewing code quality, before releases, or when test coverage is a concern.
---

# Test Coverage Analyzer

Generates comprehensive test coverage report with actionable recommendations.

## Instructions

1. Run coverage analysis script:
   ```bash
   python .claude/skills/test-coverage/scripts/analyze_coverage.py
   ```

2. Parse JSON output to identify:
   - Files with <80% coverage
   - Uncovered critical functions
   - Missing test categories (unit/integration/e2e)

3. Generate report with:
   - Coverage summary by file
   - Priority-ranked missing tests
   - Specific line numbers to test
   - Suggested test cases

4. If requested, generate test templates using `templates/test_template.py`

## Supporting Scripts

- `scripts/analyze_coverage.py` - Coverage analysis
- `templates/test_template.py` - Test boilerplate generator
```

**`.claude/skills/test-coverage/scripts/analyze_coverage.py`:**
```python
#!/usr/bin/env python3
"""Test coverage analyzer for Claude Code skills."""

import json
import subprocess
import sys
from pathlib import Path

def run_coverage():
    """Run coverage and return JSON report."""
    result = subprocess.run(
        ["pytest", "--cov=.", "--cov-report=json"],
        capture_output=True,
        text=True
    )

    if result.returncode != 0:
        print(json.dumps({"error": "Coverage failed", "stderr": result.stderr}))
        sys.exit(1)

    # Load coverage.json
    with open("coverage.json") as f:
        coverage_data = json.load(f)

    # Analyze
    low_coverage_files = []
    for file_path, file_data in coverage_data["files"].items():
        coverage_pct = file_data["summary"]["percent_covered"]
        if coverage_pct < 80:
            low_coverage_files.append({
                "file": file_path,
                "coverage": coverage_pct,
                "missing_lines": file_data["missing_lines"],
            })

    print(json.dumps({
        "total_coverage": coverage_data["totals"]["percent_covered"],
        "low_coverage_files": low_coverage_files,
    }, indent=2))

if __name__ == "__main__":
    run_coverage()
```

**Usage:**
```bash
User: "Analyze test coverage and suggest improvements"

Claude (using test-coverage-analyzer skill):
[Runs script]
[Parses output]

## Test Coverage Report

**Overall Coverage:** 67.3%
**Target:** 80%

### Files Below Target

1. **backend/app/chat.py** (42% coverage)
   - Missing lines: 45-67, 89-102
   - Critical functions uncovered:
     - `register_tool()` (lines 45-67)
     - `handle_error()` (lines 89-102)

2. **backend/app/user_profile.py** (58% coverage)
   - Missing lines: 23-34
   - Uncovered: Input validation logic

### Recommended Tests

**Priority 1: Critical Functions**
```python
# tests/test_chat.py
def test_register_tool():
    """Test tool registration with valid/invalid inputs."""
    # Test valid tool
    result = register_tool(valid_tool)
    assert result.success

    # Test duplicate registration
    with pytest.raises(ValueError):
        register_tool(valid_tool)  # Already registered
```

[Additional recommendations...]
```

---

### Best Practices

1. **Make Skills Discoverable**
   ```yaml
   # ❌ Vague
   description: Helps with ChatKit

   # ✅ Specific, trigger-rich
   description: Generates complete ChatKit agent tools including @function_tool backend, widget renderers, and frontend handlers. Use when adding new ChatKit tools, especially for widgets or client actions.
   ```

2. **Include Concrete Examples**
   ```markdown
   ## Examples

   **Example 1: Widget Tool**
   User: "Add a weather widget tool"
   → Generates weather.py, weather_widget.py, frontend handler

   **Example 2: Client Tool**
   User: "Add theme switching"
   → Generates theme.py with client_tool_call
   ```

3. **Make Instructions Actionable**
   ```markdown
   # ❌ Vague
   "Generate the tool"

   # ✅ Step-by-step
   1. Create backend/app/{tool_name}.py with @function_tool
   2. Add to tools list in chat.py
   3. Test with: "Use {tool_name} tool"
   ```

4. **Include Validation**
   ```markdown
   ## Validation

   After generating tool:
   1. Verify imports resolve
   2. Check @function_tool decorator syntax
   3. Test with sample input
   4. Verify widget renders (if widget tool)
   ```

5. **Provide Templates for Consistency**
   ```bash
   .claude/skills/my-skill/
   ├── SKILL.md
   └── templates/
       ├── backend_tool.py.template
       ├── widget.py.template
       └── test.py.template
   ```

---

### ChatKit Project Skills Recommendations

Based on your ChatKit project, consider creating these skills:

#### 1. **chatkit-full-stack-feature**
```yaml
name: chatkit-full-stack-feature
description: Scaffolds complete full-stack ChatKit features including backend tool, widget, frontend handler, tests, and documentation. Use when adding end-to-end features to ChatKit apps.
```

#### 2. **chatkit-widget-validator**
```yaml
name: chatkit-widget-validator
description: Validates ChatKit widget schemas, tests rendering, and checks for common issues. Use after creating or modifying ChatKit widgets to ensure they render correctly.
```

#### 3. **chatkit-deployment-checker**
```yaml
name: chatkit-deployment-checker
description: Validates ChatKit deployment configuration including environment variables, domain allowlisting, port configuration, and endpoint health. Use before deploying ChatKit applications.
```

#### 4. **example-app-generator**
```yaml
name: example-app-generator
description: Generates new ChatKit example applications following the structure of customer-support, knowledge-assistant, and marketing-assets examples. Use when creating new ChatKit demo applications with custom ports and configurations.
```

---

## Summary

This guide covers the advanced Claude Code features missing from the main best practices document:

1. **✅ Planning Workflow** - Explore → Plan → Code → Commit for complex tasks
2. **✅ Headless Mode** - `-p` flag for CI/CD automation
3. **✅ MCP Integration** - External tool integration via Model Context Protocol
4. **✅ Extended Thinking** - "think harder" for deep reasoning
5. **✅ Checkpointing** - Automatic session recovery and experimentation
6. **✅ Output Styles** - Customizable agent behavior and communication
7. **✅ Skills Deep Dive** - Creating model-invoked reusable workflows

**All information verified against official Anthropic documentation.**

---

**Related Documents:**
- [BEST_PRACTICES_ANALYSIS.md](BEST_PRACTICES_ANALYSIS.md) - Core best practices (agents, hooks, commands)
- [BEST_PRACTICES_VERIFICATION_REPORT.md](BEST_PRACTICES_VERIFICATION_REPORT.md) - Accuracy verification
- [COVERAGE_GAP_ANALYSIS.md](COVERAGE_GAP_ANALYSIS.md) - What was missing before this document

---

**Document Maintenance:**
- ✅ Verified against official docs: 2025-10-24
- 🔄 Next review: When Claude Code documentation updates
- 📝 Update trigger: New official features announced
