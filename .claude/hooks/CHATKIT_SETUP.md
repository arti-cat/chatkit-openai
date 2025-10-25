# ChatKit Hooks Setup Guide

Complete setup instructions for ChatKit-specific validation hooks in Claude Code.

## Prerequisites

Before setting up hooks, ensure you have:

- **Claude Code**: Latest version (hooks support)
- **Project**: ChatKit-OpenAI repository cloned
- **Python**: 3.11+ with `uv` package manager
- **Node.js**: 20+ with `npm`
- **Tools Installed**:
  - Backend: Run `cd backend && uv sync`
  - Frontend: Run `cd frontend && npm install`

### Verify Installation

```bash
# Check Python tools
uv version          # Should be >= 0.4.0
python --version    # Should be >= 3.11

# Check Node tools
node --version      # Should be >= 20
npm --version       # Should be >= 10

# Test ruff
cd backend && uv run ruff --version

# Test ESLint
cd frontend && npx eslint --version
```

## Installation Steps

### Step 1: Copy Hook Scripts

Hook scripts should already exist in `.claude/hooks/scripts/`. Verify they are executable:

```bash
chmod +x /home/bch/dev/projects/bch_openai/chatkit-openai/.claude/hooks/scripts/*.sh
chmod +x /home/bch/dev/projects/bch_openai/chatkit-openai/.claude/hooks/scripts/*.py
```

Verify critical scripts exist:

```bash
ls -la .claude/hooks/scripts/
# Should include:
# - chatkit-env-validator.sh
# - chatkit-config-validator.sh
# - port-conflict-detector.sh
# - domain-allowlist-validator.sh
```

### Step 2: Create Claude Code Settings File

Create or edit `.claude/settings.json` in project root:

```bash
# Option A: Start with minimal setup
cp .claude/hooks/examples/settings-chatkit-minimal.json .claude/settings.json

# Option B: Start with comprehensive setup
cp .claude/hooks/examples/settings-chatkit-comprehensive.json .claude/settings.json

# Option C: Create custom setup
touch .claude/settings.json
# Then add configuration (see Step 3)
```

### Step 3: Add Hook Configuration

Edit `.claude/settings.json` and add appropriate hooks (see examples below).

### Step 4: Set Environment Variables

**For Local Development**:

```bash
# Set required variables
export OPENAI_API_KEY=sk-proj-your-actual-key
export VITE_CHATKIT_API_DOMAIN_KEY=local-dev-key

# Optional for examples
export VITE_SUPPORT_CHATKIT_API_DOMAIN_KEY=local-dev-key
export VITE_KNOWLEDGE_CHATKIT_API_DOMAIN_KEY=local-dev-key
export VITE_MARKETING_CHATKIT_API_DOMAIN_KEY=local-dev-key
```

**For Permanent Setup** (add to shell profile):

```bash
# For bash (~/.bashrc or ~/.bash_profile)
cat >> ~/.bashrc << 'EOF'
# ChatKit development
export OPENAI_API_KEY=sk-proj-your-key
export VITE_CHATKIT_API_DOMAIN_KEY=local-dev-key
EOF

source ~/.bashrc

# For zsh (~/.zshrc)
cat >> ~/.zshrc << 'EOF'
# ChatKit development
export OPENAI_API_KEY=sk-proj-your-key
export VITE_CHATKIT_API_DOMAIN_KEY=local-dev-key
EOF

source ~/.zshrc
```

### Step 5: Test Hook Configuration

**Validate JSON syntax**:

```bash
python3 -m json.tool .claude/settings.json
# or
jq . .claude/settings.json
```

**Run environment validation**:

```bash
.claude/hooks/scripts/chatkit-env-validator.sh
```

Expected output:

```
Running ChatKit environment validation...
✓ OPENAI_API_KEY is set and valid
✓ VITE_CHATKIT_API_DOMAIN_KEY is set
✓ All required variables present
Exit code: 0
```

**Test port detection**:

```bash
.claude/hooks/scripts/port-conflict-detector.sh
```

Expected output (if ports are available):

```
Checking port availability for ChatKit applications...
✓ Port 8000 (base backend) is available
✓ Port 5170 (base frontend) is available
✓ Port 8001 (customer-support backend) is available
✓ Port 5171 (customer-support frontend) is available
...
Exit code: 0
```

## Configuration Options

### Option 1: Minimal Setup (Recommended Starting Point)

Focuses on critical validations only:

```json
{
  "hooks": {
    "pre-commit": {
      "chatkit-env": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "description": "Validate ChatKit environment variables",
        "blocking": true
      }
    }
  }
}
```

**Features**:
- Environment variable validation on commit
- Quick feedback, minimal overhead
- No automated fixes

**Setup time**: 2 minutes

### Option 2: Recommended Setup

Balances automation with speed:

```json
{
  "hooks": {
    "post-edit": {
      "python-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.py$ ]] && [[ \"$FILE_PATH\" =~ ^(backend|examples).*/.*\\.py$ ]]; then (cd \"${FILE_PATH%/*/*}\" && uv run ruff check . --fix) || echo '⚠ Linting completed'; fi",
        "description": "Lint Python files",
        "blocking": false
      },
      "typescript-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.(ts|tsx)$ ]] && [[ \"$FILE_PATH\" =~ ^(frontend|examples).*/src.*\\.(ts|tsx)$ ]]; then (cd \"${FILE_PATH%/*/*}\" && npm run lint 2>/dev/null) || echo '⚠ Linting completed'; fi",
        "description": "Lint TypeScript files",
        "blocking": false
      }
    },
    "pre-commit": {
      "chatkit-env": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "description": "Validate ChatKit environment",
        "blocking": true
      },
      "port-check": {
        "command": ".claude/hooks/scripts/port-conflict-detector.sh",
        "description": "Check for port conflicts",
        "blocking": false
      }
    }
  }
}
```

**Features**:
- Real-time linting feedback on saves
- Environment validation before commits
- Port conflict warnings
- Non-blocking checks

**Setup time**: 5 minutes

### Option 3: Comprehensive Setup (Teams)

Full validation and standards enforcement:

```json
{
  "hooks": {
    "pre-edit": {
      "config-check": {
        "command": ".claude/hooks/scripts/chatkit-config-validator.sh \"$FILE_PATH\"",
        "description": "Validate configuration",
        "blocking": false
      }
    },
    "post-edit": {
      "python-format": {
        "command": "if [[ \"$FILE_PATH\" =~ ^(backend|examples).*/app/.*\\.py$ ]]; then (cd \"${FILE_PATH%/*/*}\" && uv run ruff format \"$FILE_PATH\") || true; fi",
        "description": "Format Python code",
        "blocking": false
      },
      "python-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ ^(backend|examples).*/app/.*\\.py$ ]]; then (cd \"${FILE_PATH%/*/*}\" && uv run ruff check \"$FILE_PATH\" --fix) || echo '⚠ Linting completed'; fi",
        "description": "Lint Python code",
        "blocking": false
      },
      "python-type": {
        "command": "if [[ \"$FILE_PATH\" =~ ^backend/app/.*\\.py$ ]]; then (cd backend && uv run mypy app/ --incremental) || echo '⚠ Type errors found'; fi",
        "description": "Type check Python",
        "blocking": false
      },
      "typescript-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ ^(frontend|examples).*/src.*\\.(ts|tsx)$ ]]; then (cd \"${FILE_PATH%/*/*}\" && npm run lint 2>/dev/null) || echo '⚠ Linting completed'; fi",
        "description": "Lint TypeScript",
        "blocking": false
      }
    },
    "pre-commit": {
      "chatkit-env": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "description": "Validate environment",
        "blocking": true
      },
      "port-check": {
        "command": ".claude/hooks/scripts/port-conflict-detector.sh",
        "description": "Check ports",
        "blocking": false
      },
      "domain-check": {
        "command": ".claude/hooks/scripts/domain-allowlist-validator.sh",
        "description": "Validate domain config",
        "blocking": false
      }
    }
  }
}
```

**Features**:
- Auto-formatting on save
- Full linting and type checking
- Configuration validation
- Comprehensive pre-commit checks
- All blocking/non-blocking options

**Setup time**: 10 minutes

## Configuration by Use Case

### Solo Developer

```bash
cp .claude/hooks/examples/settings-chatkit-minimal.json .claude/settings.json
```

Then add post-edit for quick feedback:

```json
{
  "hooks": {
    "post-edit": {
      "quick-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.py$ ]]; then cd backend && uv run ruff check \"$FILE_PATH\" --fix; elif [[ \"$FILE_PATH\" =~ \\.(ts|tsx)$ ]]; then cd frontend && npm run lint 2>/dev/null; fi",
        "blocking": false
      }
    },
    "pre-commit": {
      "validate": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "blocking": true
      }
    }
  }
}
```

### Small Team

```bash
cp .claude/hooks/examples/settings-chatkit-team.json .claude/settings.json
```

Key features:
- Auto-formatting for consistency
- Full linting before commit
- Configuration validation
- Standards enforcement

### Large Project

```bash
cp .claude/hooks/examples/settings-chatkit-comprehensive.json .claude/settings.json
```

Then customize thresholds in hook scripts as needed.

### CI/CD Integration

Mirror hooks in GitHub Actions:

```yaml
name: ChatKit Validation

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install uv
        run: curl -LsSf https://astral.sh/uv/install.sh | sh

      - name: Install backend dependencies
        run: cd backend && uv sync

      - name: Python linting
        run: cd backend && uv run ruff check . && uv run mypy app/

      - name: Set up Node
        uses: actions/setup-node@v3
        with:
          node-version: '20'

      - name: Install frontend dependencies
        run: cd frontend && npm ci

      - name: TypeScript linting
        run: cd frontend && npm run lint
```

## Customization

### Modifying Hook Behavior

Edit hook scripts in `.claude/hooks/scripts/`:

```bash
# Example: Modify chatkit-env-validator.sh
nano .claude/hooks/scripts/chatkit-env-validator.sh

# Change required variables
REQUIRED_VARS=(
  "OPENAI_API_KEY"
  "VITE_CHATKIT_API_DOMAIN_KEY"
  # Add more as needed
)
```

### Adding Custom Hooks

Create new script:

```bash
# Create hook script
cat > .claude/hooks/scripts/my-validator.sh << 'EOF'
#!/bin/bash

# Your validation logic
if ! my_check; then
  echo "❌ Validation failed"
  exit 1
fi

echo "✓ Validation passed"
exit 0
EOF

chmod +x .claude/hooks/scripts/my-validator.sh
```

Add to `.claude/settings.json`:

```json
{
  "hooks": {
    "pre-commit": {
      "my-validator": {
        "command": ".claude/hooks/scripts/my-validator.sh",
        "description": "My custom validation",
        "blocking": true
      }
    }
  }
}
```

### Adjusting Thresholds

Most scripts have configuration at the top:

```bash
# In port-conflict-detector.sh
BACKEND_PORTS=(8000 8001 8002 8003)
FRONTEND_PORTS=(5170 5171 5172 5173)

# In chatkit-env-validator.sh
REQUIRED_VARS=(...)
KEY_PREFIX="sk-proj-"
```

## Troubleshooting

### Hooks Not Executing

**Problem**: Hooks configured but not running

**Solutions**:

1. Check Claude Code version supports hooks
2. Verify `.claude/settings.json` is valid JSON:
   ```bash
   python3 -m json.tool .claude/settings.json
   ```
3. Check script permissions:
   ```bash
   chmod +x .claude/hooks/scripts/*.sh
   ```
4. Test hook directly:
   ```bash
   FILE_PATH="backend/app/main.py" .claude/hooks/scripts/chatkit-env-validator.sh
   ```

### Hooks Running Too Slow

**Problem**: Post-edit hooks taking > 2 seconds

**Solutions**:

1. Move expensive checks to pre-commit
2. Add file filtering to limit scope:
   ```bash
   if [[ "$FILE_PATH" =~ \.py$ ]]; then
     # Only run for Python files
   fi
   ```
3. Use incremental mode:
   ```bash
   cd backend && uv run mypy app/ --incremental
   ```
4. Disable slow hooks temporarily:
   ```bash
   # Remove from .claude/settings.json or use --no-verify
   git commit --no-verify
   ```

### Scripts Failing

**Problem**: Hook scripts fail with errors

**Solutions**:

1. Run script directly to see error:
   ```bash
   .claude/hooks/scripts/chatkit-env-validator.sh
   ```
2. Check dependencies installed:
   ```bash
   which uv npm node
   ```
3. Verify environment variables set:
   ```bash
   echo $OPENAI_API_KEY
   ```
4. Check script permissions:
   ```bash
   ls -la .claude/hooks/scripts/chatkit-env-validator.sh
   # Should start with -rwxr-xr-x
   ```

### Environment Variable Errors

**Problem**: "OPENAI_API_KEY not set"

**Solution**:
```bash
# Check if set
echo $OPENAI_API_KEY

# Set temporarily
export OPENAI_API_KEY=sk-proj-your-key

# Set permanently
echo 'export OPENAI_API_KEY=sk-proj-your-key' >> ~/.bashrc
source ~/.bashrc
```

### Port Conflict Errors

**Problem**: "Port 8000 is in use"

**Solutions**:

```bash
# Find what's using the port
lsof -i :8000

# Kill the process (if safe)
kill -9 <PID>

# Use different port
cd backend && uv run uvicorn app.main:app --port 8100

# Or wait for existing process to finish
```

## Next Steps

1. **Run test development session**:
   ```bash
   npm start
   # Base backend at 8000, frontend at 5170
   ```

2. **Edit a Python file** to trigger post-edit hooks:
   ```bash
   # Make a small change to backend/app/main.py
   # Observe lint/format hooks running
   ```

3. **Try a commit** to trigger pre-commit hooks:
   ```bash
   git add .
   git commit -m "test: hook configuration"
   # Should run chatkit-env-validator.sh
   ```

4. **Check hook logs**:
   ```bash
   # Hooks output appears in Claude Code logs
   # Check Claude Code console for hook execution details
   ```

5. **Customize as needed**:
   - Disable slow hooks for faster iteration
   - Add team-specific rules
   - Create custom validations

## Performance Tips

1. **File filtering**: Only run tools on relevant files
2. **Incremental builds**: Enable caching in tools
3. **Async execution**: Run non-critical checks asynchronously
4. **Parallel checks**: Run independent tools in parallel
5. **Smart defaults**: Start minimal, add hooks gradually

## Getting Help

- **Hooks Documentation**: See [chatkit-validation.md](./chatkit-validation.md)
- **Quick Reference**: See [chatkit-quick-reference.md](./chatkit-quick-reference.md)
- **Project Documentation**: See [../../CLAUDE.md](../../CLAUDE.md)
- **Claude Code Hooks**: https://docs.anthropic.com/claude-code/hooks-guide
- **OpenAI API Keys**: https://platform.openai.com/account/api-keys
- **Domain Allowlist**: https://platform.openai.com/settings/organization/security/domain-allowlist

## Quick Reference Commands

```bash
# Validate environment
.claude/hooks/scripts/chatkit-env-validator.sh

# Check ports
.claude/hooks/scripts/port-conflict-detector.sh

# Validate config
.claude/hooks/scripts/chatkit-config-validator.sh

# Lint backend
cd backend && uv run ruff check . --fix

# Type check backend
cd backend && uv run mypy app/

# Lint frontend
cd frontend && npm run lint

# Run everything
npm start
```

---

For questions or issues, see Troubleshooting section above or check project documentation.
