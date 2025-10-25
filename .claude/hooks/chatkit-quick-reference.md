# ChatKit Hooks Quick Reference

Quick lookup guide for ChatKit-specific validation hooks configuration.

## Essential Setup

### 1-Minute Setup (Minimal)

Copy to `.claude/settings.json`:

```json
{
  "hooks": {
    "pre-commit": {
      "chatkit-env": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "description": "Validate environment variables",
        "blocking": true
      }
    }
  }
}
```

### 5-Minute Setup (Recommended)

Use example configuration:

```bash
cp .claude/hooks/examples/settings-chatkit-comprehensive.json .claude/settings.json
```

## Hook Execution Points

| Hook Type | When | Use Case |
|-----------|------|----------|
| `pre-edit` | Before editing a file | Show warnings, check prerequisites |
| `post-edit` | After file save | Linting, formatting, type checking |
| `pre-commit` | Before git commit | Validation, comprehensive checks |

## ChatKit-Specific Validations

### Environment Variables

Check before developing:

```bash
OPENAI_API_KEY=sk-proj-...              # Required for backend
VITE_CHATKIT_API_DOMAIN_KEY=dev-key     # Frontend config

# For examples (optional)
VITE_SUPPORT_CHATKIT_API_DOMAIN_KEY=...
VITE_KNOWLEDGE_CHATKIT_API_DOMAIN_KEY=...
VITE_MARKETING_CHATKIT_API_DOMAIN_KEY=...
```

**Validate with**: `.claude/hooks/scripts/chatkit-env-validator.sh`

### Port Availability

ChatKit uses these ports:

| Service | Port | Note |
|---------|------|------|
| Base Backend | 8000 | FastAPI server |
| Base Frontend | 5170 | React dev server |
| Customer Support Backend | 8001 | Example backend |
| Customer Support Frontend | 5171 | Example frontend |
| Knowledge Assistant Backend | 8002 | Example backend |
| Knowledge Assistant Frontend | 5172 | Example frontend |
| Marketing Assets Backend | 8003 | Example backend |
| Marketing Assets Frontend | 5173 | Example frontend |

**Check with**: `.claude/hooks/scripts/port-conflict-detector.sh`

### Configuration Files

Critical ChatKit configuration:

| File | What It Controls |
|------|-----------------|
| `frontend/vite.config.ts` | Proxy targets, domain allowlist |
| `frontend/src/lib/config.ts` | ChatKit domain key, API endpoints |
| `backend/app/constants.py` | Model name, system instructions |
| `backend/app/main.py` | /chatkit endpoint configuration |

**Validate with**: `.claude/hooks/scripts/chatkit-config-validator.sh`

## Common Hook Patterns

### Python Backend Linting

```bash
# Single file formatting
uv run ruff format backend/app/main.py

# Full backend linting
cd backend && uv run ruff check . --fix

# Type checking
cd backend && uv run mypy app/
```

### TypeScript Frontend Linting

```bash
# Single file linting
npx eslint frontend/src/main.tsx --fix

# Full frontend linting
cd frontend && npm run lint

# Format with Prettier
npx prettier --write frontend/src/main.tsx
```

### Environment Validation

```bash
# Check OPENAI_API_KEY
[[ "$OPENAI_API_KEY" =~ ^sk-proj- ]] && echo "✓ Valid" || echo "✗ Invalid"

# Check domain key
[ -n "$VITE_CHATKIT_API_DOMAIN_KEY" ] && echo "✓ Set" || echo "✗ Not set"
```

## File Type Detection

Use in hooks to run appropriate tools:

```bash
# Python files
[[ "$FILE_PATH" =~ \.py$ ]] && python_tools

# TypeScript/TSX files
[[ "$FILE_PATH" =~ \.(ts|tsx)$ ]] && typescript_tools

# Backend files
[[ "$FILE_PATH" =~ ^backend/ ]] && backend_tools

# Frontend files
[[ "$FILE_PATH" =~ ^frontend/ ]] && frontend_tools

# Example files
[[ "$FILE_PATH" =~ ^examples/ ]] && example_tools
```

## Example Configurations

### For Solo Developers

Quick feedback on code quality:

```json
{
  "hooks": {
    "post-edit": {
      "format-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.py$ ]]; then cd backend && uv run ruff format . && uv run ruff check .; else if [[ \"$FILE_PATH\" =~ \\.(ts|tsx)$ ]]; then cd frontend && npm run lint; fi; fi",
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

### For Teams

Standards enforcement and communication:

```json
{
  "hooks": {
    "post-edit": {
      "auto-format": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.py$ ]]; then cd backend && uv run ruff format \"$FILE_PATH\"; elif [[ \"$FILE_PATH\" =~ \\.(ts|tsx)$ ]]; then cd frontend && npx prettier --write \"$FILE_PATH\"; fi",
        "blocking": false
      }
    },
    "pre-commit": {
      "full-check": {
        "command": "cd backend && uv run ruff check . && uv run mypy app/ && cd ../frontend && npm run lint",
        "blocking": true
      },
      "config-check": {
        "command": ".claude/hooks/scripts/chatkit-config-validator.sh",
        "blocking": false
      }
    }
  }
}
```

### For Performance-Critical

Optimize for development speed:

```json
{
  "hooks": {
    "post-edit": {
      "quick-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.py$ ]]; then cd backend && uv run ruff check \"$FILE_PATH\" --fix; elif [[ \"$FILE_PATH\" =~ \\.(ts|tsx)$ ]]; then cd frontend && npx eslint \"$FILE_PATH\" --fix; fi",
        "blocking": false
      }
    },
    "pre-commit": {
      "env-check": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "blocking": true
      }
    }
  }
}
```

## Common Tasks

### Validate Everything Before Commit

```bash
# Environment
.claude/hooks/scripts/chatkit-env-validator.sh

# Ports
.claude/hooks/scripts/port-conflict-detector.sh

# Configuration
.claude/hooks/scripts/chatkit-config-validator.sh

# Python
cd backend && uv run ruff format . && uv run ruff check . && uv run mypy app/

# TypeScript
cd frontend && npm run lint
```

### Run Linting on Specific File

```bash
# Python
cd backend && uv run ruff check app/chat.py --fix

# TypeScript
cd frontend && npx eslint src/components/ChatKitPanel.tsx --fix
```

### Check Port Status

```bash
# List all processes using ports 8000-8003, 5170-5173
lsof -i :8000 -i :8001 -i :8002 -i :8003 -i :5170 -i :5171 -i :5172 -i :5173

# Or using netstat
netstat -tuln | grep -E ':(8000|8001|8002|8003|5170|5171|5172|5173)'
```

### Set Environment Variables

```bash
# Temporarily for current session
export OPENAI_API_KEY=sk-proj-your-key
export VITE_CHATKIT_API_DOMAIN_KEY=local-dev

# Permanently (bash)
echo 'export OPENAI_API_KEY=sk-proj-your-key' >> ~/.bashrc
source ~/.bashrc

# Permanently (zsh)
echo 'export OPENAI_API_KEY=sk-proj-your-key' >> ~/.zshrc
source ~/.zshrc
```

## Error Quick Fixes

| Error | Fix Command |
|-------|-------------|
| Port 8000 in use | `lsof -i :8000 \| grep LISTEN \| awk '{print $2}' \| xargs kill` |
| OPENAI_API_KEY not set | `export OPENAI_API_KEY=sk-proj-...` |
| Python linting fails | `cd backend && uv run ruff format .` |
| TypeScript linting fails | `cd frontend && npm run lint -- --fix` |
| mypy errors | `cd backend && uv run mypy app/` |
| Type errors | Read mypy output and fix type annotations |

## Troubleshooting

### Hook Not Running

1. Check JSON syntax: `python3 -m json.tool .claude/settings.json`
2. Verify script exists: `ls -la .claude/hooks/scripts/`
3. Make executable: `chmod +x .claude/hooks/scripts/*.sh`

### Hook Too Slow

1. Move to `pre-commit` instead of `post-edit`
2. Use file filtering: `[[ "$FILE_PATH" =~ \\.py$ ]]`
3. Run on single file instead of entire directory

### Validation Failing

1. Run script directly: `./.claude/hooks/scripts/chatkit-env-validator.sh`
2. Check output for specific errors
3. Verify dependencies: `uv version`, `npm -v`, `node -v`

## Configuration Files

### Minimal (Fast)
- **File**: `settings-chatkit-minimal.json`
- **Features**: Pre-commit env check, basic linting
- **Use**: Solo developers, quick feedback

### Comprehensive (Full Featured)
- **File**: `settings-chatkit-comprehensive.json`
- **Features**: All validations, type checking, config validation
- **Use**: Teams, strict quality requirements

### Performance (Balanced)
- **File**: `settings-chatkit-performance.json`
- **Features**: Quick post-edit checks, comprehensive pre-commit
- **Use**: Large projects, optimize for dev speed

### Team (Collaboration Focused)
- **File**: `settings-chatkit-team.json`
- **Features**: Auto-formatting, full validation, standards enforcement
- **Use**: Team projects, consistent code style

## Related Files

- Main documentation: [chatkit-validation.md](./chatkit-validation.md)
- Project overview: [../../CLAUDE.md](../../CLAUDE.md)
- General hooks: [svelte-hooks.md](./svelte-hooks.md)

## Tips

1. **Start minimal**: Begin with environment validation only
2. **Add gradually**: Enable more hooks as needed
3. **Use --fix flags**: Let tools auto-fix issues
4. **Test manually**: Run hook commands directly to debug
5. **Check CI/CD**: Mirror hook checks in GitHub Actions
6. **Cache results**: Enable incremental modes in tools
7. **Use blocking wisely**: Only block on critical failures

## Keyboard Shortcuts

For git operations with hooks:

```bash
# Bypass hooks (use carefully!)
git commit --no-verify -m "message"

# Force push (NEVER on main!)
git push --force-with-lease

# Amend last commit
git commit --amend --no-edit
```

---

For detailed documentation, see [chatkit-validation.md](./chatkit-validation.md)
