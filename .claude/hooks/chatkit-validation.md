# ChatKit-Specific Validation Hooks for Claude Code

This document provides comprehensive hook configurations for ChatKit-OpenAI projects. These hooks validate ChatKit-specific configurations, manage environment variables, detect port conflicts, and enforce code quality standards across the full-stack FastAPI + React architecture.

## Overview

ChatKit validation hooks address the unique requirements of the ChatKit framework:

- **Environment Configuration**: Validate required API keys and domain settings
- **Port Management**: Detect and warn about port conflicts across base template and examples
- **Configuration Validation**: Ensure ChatKit configuration is correct for development and production
- **Code Quality**: Run linting and type checking on backend (Python) and frontend (TypeScript)
- **Integration Testing**: Verify ChatKit endpoint health and configuration

## Quick Start

### Minimal Setup

Add to your Claude Code settings (`.claude/settings.json` or `~/.config/claude/settings.json`):

```json
{
  "hooks": {
    "pre-commit": {
      "chatkit-env-check": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "description": "Validate ChatKit environment variables before commit",
        "blocking": true
      }
    },
    "post-edit": {
      "python-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.py$ ]] && [[ \"$FILE_PATH\" =~ ^(backend|examples).*/.*\\.py$ ]]; then cd \"${FILE_PATH%/*}/..\" && uv run ruff check . || true; fi",
        "description": "Lint Python files in backend",
        "blocking": false
      },
      "typescript-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.(ts|tsx)$ ]] && [[ \"$FILE_PATH\" =~ ^(frontend|examples).*/.*\\.(ts|tsx)$ ]]; then cd \"${FILE_PATH%/*}/../..\" && npm run lint 2>/dev/null || true; fi",
        "description": "Lint TypeScript files in frontend",
        "blocking": false
      }
    }
  }
}
```

### Recommended Comprehensive Setup

```json
{
  "hooks": {
    "pre-edit": {
      "chatkit-config-check": {
        "command": ".claude/hooks/scripts/chatkit-config-validator.sh",
        "description": "Check ChatKit configuration before editing",
        "blocking": false
      }
    },
    "post-edit": {
      "python-format-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.py$ ]] && [[ \"$FILE_PATH\" =~ ^(backend|examples).*/.*\\.py$ ]]; then (cd \"${FILE_PATH%/*/*}\" && uv run ruff format . && uv run ruff check .) || true; fi",
        "description": "Format and lint Python files",
        "blocking": false
      },
      "python-type-check": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.py$ ]] && [[ \"$FILE_PATH\" =~ ^backend/.*\\.py$ ]]; then cd backend && uv run mypy app/ || true; fi",
        "description": "Type check Python backend",
        "blocking": false
      },
      "typescript-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.(ts|tsx)$ ]] && [[ \"$FILE_PATH\" =~ ^(frontend|examples).*/src.*\\.(ts|tsx)$ ]]; then cd \"${FILE_PATH%/*/*}\" && npm run lint 2>/dev/null || true; fi",
        "description": "Lint TypeScript files",
        "blocking": false
      }
    },
    "pre-commit": {
      "chatkit-env-validate": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "description": "Validate ChatKit environment variables",
        "blocking": true
      },
      "port-conflict-check": {
        "command": ".claude/hooks/scripts/port-conflict-detector.sh",
        "description": "Detect port conflicts before commit",
        "blocking": false
      }
    }
  }
}
```

## Validation Hook Categories

### 1. Environment Variable Validation

#### Hook: Pre-Commit Environment Check

Validates that required environment variables are set with correct format before committing.

**Purpose**: Prevent commits that would break development workflow due to missing API keys or configuration.

**Configuration**:

```json
{
  "hooks": {
    "pre-commit": {
      "chatkit-env-validate": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "description": "Validate ChatKit environment variables before commit",
        "blocking": true,
        "blockingMessage": "Missing or invalid ChatKit environment variables. See errors above."
      }
    }
  }
}
```

**Validates**:
- `OPENAI_API_KEY` presence and format (must start with `sk-proj-`)
- `VITE_CHATKIT_API_DOMAIN_KEY` presence (for frontend)
- Environment-specific keys for examples:
  - `VITE_SUPPORT_CHATKIT_API_DOMAIN_KEY`
  - `VITE_KNOWLEDGE_CHATKIT_API_DOMAIN_KEY`
  - `VITE_MARKETING_CHATKIT_API_DOMAIN_KEY`

**Exit Codes**:
- `0`: All validations passed
- `1`: Missing or invalid environment variables
- `2`: Validation script error

**Example Output**:

```
Running ChatKit environment validation...
✓ OPENAI_API_KEY is set and valid
✓ VITE_CHATKIT_API_DOMAIN_KEY is set
⚠ VITE_SUPPORT_CHATKIT_API_DOMAIN_KEY not set (required for examples/customer-support)
✗ Validation failed: 1 required variable missing
Exit code: 1
```

### 2. Port Conflict Detection

#### Hook: Pre-Commit Port Conflict Check

Detects when ports required by ChatKit applications are already in use.

**Purpose**: Prevent commit if local development ports are unavailable.

**Configuration**:

```json
{
  "hooks": {
    "pre-commit": {
      "port-conflict-check": {
        "command": ".claude/hooks/scripts/port-conflict-detector.sh",
        "description": "Detect port conflicts for ChatKit applications",
        "blocking": false
      }
    }
  }
}
```

**Monitored Ports**:
- Base template: `8000` (backend), `5170` (frontend)
- Customer support example: `8001` (backend), `5171` (frontend)
- Knowledge assistant example: `8002` (backend), `5172` (frontend)
- Marketing assets example: `8003` (backend), `5173` (frontend)

**Exit Codes**:
- `0`: All ports available
- `1`: One or more ports in use
- `2`: Script error (lsof/netstat not available)

**Example Output**:

```
Checking port availability for ChatKit applications...
✓ Port 8000 (base backend) is available
✓ Port 5170 (base frontend) is available
✗ Port 8001 (customer-support backend) is in use
  Process: uvicorn (PID 12345)
  Suggestion: Kill process or change port in vite.config.ts

⚠ 1 port conflict detected. Start a different example or stop existing processes.
```

### 3. ChatKit Configuration Validation

#### Hook: Pre-Edit Configuration Check

Validates ChatKit configuration files before editing them.

**Purpose**: Catch configuration errors early and provide guidance on valid settings.

**Configuration**:

```json
{
  "hooks": {
    "pre-edit": {
      "chatkit-config-check": {
        "command": ".claude/hooks/scripts/chatkit-config-validator.sh \"$FILE_PATH\"",
        "description": "Validate ChatKit configuration before editing",
        "blocking": false
      }
    }
  }
}
```

**Validates**:
- Frontend `vite.config.ts`: Proxy configuration points to correct backend
- Frontend `src/lib/config.ts`: ChatKit domain key configuration
- Backend `app/constants.py`: Model name and system instructions
- Backend `app/main.py`: Endpoint configuration
- Example-specific configurations match their port assignments

**Applicable Files**:
- `frontend/vite.config.ts`
- `frontend/src/lib/config.ts`
- `backend/app/constants.py`
- `backend/app/main.py`
- `examples/*/vite.config.ts`
- `examples/*/src/lib/config.ts`

**Exit Codes**:
- `0`: Configuration valid
- `1`: Configuration issues detected
- `2`: File not found or unreadable

### 4. Domain Allowlist Validation

#### Hook: Pre-Deploy Domain Check

Validates that frontend domains are properly registered with OpenAI for production deployments.

**Purpose**: Prevent deployment without proper domain allowlisting.

**Configuration**:

```json
{
  "hooks": {
    "pre-commit": {
      "domain-allowlist-check": {
        "command": ".claude/hooks/scripts/domain-allowlist-validator.sh",
        "description": "Check domain allowlist configuration for production",
        "blocking": false
      }
    }
  }
}
```

**Validates**:
- Domain configured in `vite.config.ts` `server.allowedHosts`
- Domain not using localhost or 127.0.0.1
- Domain key format is valid (not placeholder)
- Guidance for domain registration process

**Exit Codes**:
- `0`: Domain configuration looks good for production
- `1`: Development setup detected (localhost) - acceptable
- `2`: Invalid configuration that would fail in production

**Example Output**:

```
Domain allowlist validation...
Configuration: localhost (development mode)
✓ Acceptable for local development

For production deployment:
1. Register domain at: https://platform.openai.com/settings/organization/security/domain-allowlist
2. Set VITE_CHATKIT_API_DOMAIN_KEY to the generated key
3. Update server.allowedHosts in vite.config.ts
4. Deploy frontend to registered domain
```

### 5. Python Backend Validation

#### Hook: Post-Edit Python Linting

Runs `ruff` linter and formatter on backend Python files.

**Purpose**: Maintain code quality and consistency in Python backend.

**Configuration**:

```json
{
  "hooks": {
    "post-edit": {
      "python-lint-format": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.py$ ]] && [[ \"$FILE_PATH\" =~ ^(backend|examples).*/.*\\.py$ ]]; then (cd \"${FILE_PATH%/*/*}\" && uv run ruff format . && uv run ruff check . --fix) || echo '⚠ Linting completed with warnings'; fi",
        "description": "Format and lint Python code with ruff",
        "blocking": false
      }
    }
  }
}
```

**Tools**:
- **ruff format**: Auto-formats Python code
- **ruff check**: Lints for style and error issues

**Applicable Files**:
- `backend/app/**/*.py`
- `examples/*/backend/app/**/*.py`

**Performance**: ~500ms for typical file edits

#### Hook: Post-Edit Python Type Checking

Runs `mypy` type checker on backend Python files.

**Purpose**: Catch type errors before runtime.

**Configuration**:

```json
{
  "hooks": {
    "post-edit": {
      "python-type-check": {
        "command": "if [[ \"$FILE_PATH\" =~ ^backend/app/.*\\.py$ ]]; then (cd backend && uv run mypy app/ --incremental) || echo '⚠ Type checking completed with errors'; fi",
        "description": "Type check Python backend with mypy",
        "blocking": false
      }
    }
  }
}
```

**Tools**:
- **mypy**: Static type checker for Python

**Applicable Files**:
- `backend/app/**/*.py` only (examples have separate mypy configs)

**Performance**: ~1-2 seconds (uses incremental mode for speed)

### 6. TypeScript Frontend Validation

#### Hook: Post-Edit TypeScript Linting

Runs ESLint on frontend TypeScript/React files.

**Purpose**: Maintain code quality and catch React/TypeScript issues.

**Configuration**:

```json
{
  "hooks": {
    "post-edit": {
      "typescript-lint": {
        "command": "if [[ \"$FILE_PATH\" =~ \\.(ts|tsx)$ ]] && [[ \"$FILE_PATH\" =~ ^(frontend|examples).*/src.*\\.(ts|tsx)$ ]]; then (cd \"${FILE_PATH%/*/*}\" && npm run lint 2>/dev/null) || echo '⚠ Linting completed with warnings'; fi",
        "description": "Lint TypeScript files with ESLint",
        "blocking": false
      }
    }
  }
}
```

**Tools**:
- **ESLint**: Linter for TypeScript/JavaScript
- **@typescript-eslint**: TypeScript-specific rules
- **eslint-plugin-react**: React-specific rules

**Applicable Files**:
- `frontend/src/**/*.ts`
- `frontend/src/**/*.tsx`
- `examples/*/src/**/*.ts`
- `examples/*/src/**/*.tsx`

**Performance**: ~800ms-1.5s depending on project size

## Hook Implementation Patterns

### Pattern 1: File Type Detection

Detect which type of file is being edited:

```bash
# Python file in backend/examples
if [[ "$FILE_PATH" =~ ^(backend|examples)/.*\.py$ ]]; then
  # Run Python tools
fi

# TypeScript file in frontend/examples
if [[ "$FILE_PATH" =~ ^(frontend|examples)/.*/src.*\.(ts|tsx)$ ]]; then
  # Run TypeScript tools
fi
```

### Pattern 2: Conditional Backend Selection

Run tools from the correct directory:

```bash
# Navigate to project root for tools
cd "${FILE_PATH%/*/*}"  # Go up two directories from file
npm run lint

# Or for nested examples
cd "${FILE_PATH%/*/*/*}"  # Go up three directories
npm run lint
```

### Pattern 3: Environment Variable Validation

Check variable presence and format:

```bash
if [ -z "$OPENAI_API_KEY" ]; then
  echo "❌ OPENAI_API_KEY not set"
  exit 1
fi

if [[ ! "$OPENAI_API_KEY" =~ ^sk-proj- ]]; then
  echo "❌ OPENAI_API_KEY has invalid format"
  exit 1
fi
```

### Pattern 4: Port Availability Check

Check if a port is in use:

```bash
# Using lsof (macOS/Linux)
if lsof -i :8000 > /dev/null 2>&1; then
  echo "✗ Port 8000 is in use"
  exit 1
fi

# Fallback using netstat
if netstat -tuln 2>/dev/null | grep -q ":8000 "; then
  echo "✗ Port 8000 is in use"
  exit 1
fi
```

## Validation Scripts Reference

### chatkit-env-validator.sh

Validates environment variables required for ChatKit development.

**Input**: Standard input (unused)

**Output**: Validation results with clear status indicators

**Exit Codes**:
- `0`: All required variables valid
- `1`: One or more variables missing or invalid
- `2`: Script execution error

**Checks**:
1. OPENAI_API_KEY exists
2. OPENAI_API_KEY matches format `sk-proj-*`
3. VITE_CHATKIT_API_DOMAIN_KEY exists (warn if missing)
4. Domain key is not a placeholder value
5. Validate example-specific keys if example directories exist

### chatkit-config-validator.sh

Validates ChatKit configuration across frontend and backend.

**Input**: File path to validate (optional)

**Output**: Configuration validation status

**Exit Codes**:
- `0`: Configuration valid
- `1`: Configuration issues detected
- `2`: File read error

**Checks**:
1. Proxy targets in `vite.config.ts` match backend ports
2. ChatKit domain key configured in frontend config
3. Backend model and instructions configured
4. Endpoints match expected paths (/chatkit, /facts)
5. Example configurations match their assigned ports

### port-conflict-detector.sh

Detects port conflicts for all ChatKit applications.

**Input**: Standard input (unused)

**Output**: Port availability status with process information

**Exit Codes**:
- `0`: All ports available
- `1`: One or more ports in use
- `2`: Cannot determine (lsof/netstat unavailable)

**Ports Checked**:
- 8000, 8001, 8002, 8003 (backends)
- 5170, 5171, 5172, 5173 (frontends)

### domain-allowlist-validator.sh

Validates domain configuration for production.

**Input**: Standard input (unused)

**Output**: Domain configuration status and guidance

**Exit Codes**:
- `0`: Production-ready configuration
- `1`: Development setup (localhost - acceptable)
- `2`: Invalid configuration for production

## Error Messages and Fixes

### Environment Variable Errors

**Error**: `OPENAI_API_KEY not set`

**Fix**:
```bash
export OPENAI_API_KEY=sk-proj-your-actual-key-here
# Or for permanent setup, add to ~/.bashrc or ~/.zshrc
```

**Error**: `OPENAI_API_KEY has invalid format`

**Fix**: Ensure key starts with `sk-proj-`. Get a valid key from [platform.openai.com/account/api-keys](https://platform.openai.com/account/api-keys)

**Error**: `VITE_CHATKIT_API_DOMAIN_KEY not set`

**Fix**:
```bash
# For development, use any non-empty string
export VITE_CHATKIT_API_DOMAIN_KEY=dev-key-local

# For production, register domain and use generated key
# See: https://platform.openai.com/settings/organization/security/domain-allowlist
```

### Port Conflict Errors

**Error**: `Port 8000 (base backend) is in use`

**Fix Options**:
1. Stop existing process: `lsof -i :8000 | grep -v COMMAND | awk '{print $2}' | xargs kill`
2. Use different port: `cd backend && uv run uvicorn app.main:app --port 8100`
3. Check what's running: `lsof -i :8000` or `netstat -tuln | grep 8000`

**Error**: `Port 5170 (base frontend) is in use`

**Fix Options**:
1. Stop existing process using the port
2. Use different frontend port by setting `VITE_PORT=5200 npm run dev`
3. Wait for existing process to free the port

### Configuration Errors

**Error**: `vite.config.ts proxy target doesn't match backend port`

**Fix**: Update proxy configuration in `vite.config.ts`:
```typescript
server: {
  proxy: {
    '/chatkit': {
      target: 'http://127.0.0.1:8000',  // Match backend port
      changeOrigin: true,
    },
  },
},
```

**Error**: `ChatKit domain key is not set or is placeholder`

**Fix**: For local development, set any non-empty string:
```bash
export VITE_CHATKIT_API_DOMAIN_KEY=local-dev-key
```

For production, register domain and get key from OpenAI dashboard.

## Performance Guidelines

### Hook Execution Times

**Instant (< 100ms)**:
- Environment variable checks
- Simple file existence checks
- Port availability checks

**Fast (100-500ms)**:
- ruff formatting of single file
- ESLint linting of single file
- Basic configuration validation

**Acceptable (500ms-2s)**:
- mypy type checking (incremental)
- Full ruff check across backend
- Complete ESLint run on frontend

**Slow (> 2s)**:
- Full mypy run on all files
- Building/compiling
- Running full test suites

### Optimization Tips

1. **Use file filtering**: Only run linting on edited file types
2. **Enable incremental checking**: mypy uses `--incremental` flag
3. **Run expensive checks on pre-commit**: Not on every file edit
4. **Parallelize where possible**: Run multiple checks simultaneously
5. **Cache results**: Many tools support caching

## Development Workflow Integration

### Recommended Hook Execution Points

**Pre-Edit Hooks** (run before editing a file):
- Quick configuration checks
- Warnings about critical files
- Setup validation

**Post-Edit Hooks** (run after file save):
- Format checking
- Linting
- Type checking
- Warn about style issues

**Pre-Commit Hooks** (run before git commit):
- Environment validation
- Port conflict detection
- Breaking change detection
- Comprehensive checks

### Running Specific Validations Manually

```bash
# Validate environment
.claude/hooks/scripts/chatkit-env-validator.sh

# Check for port conflicts
.claude/hooks/scripts/port-conflict-detector.sh

# Validate ChatKit config
.claude/hooks/scripts/chatkit-config-validator.sh

# Run Python linting on backend
cd backend && uv run ruff format . && uv run ruff check .

# Run TypeScript linting on frontend
cd frontend && npm run lint

# Type check backend
cd backend && uv run mypy app/
```

## Disabling Hooks Temporarily

### Disable Single Hook for a Commit

```bash
# Commit without running pre-commit hooks
git commit --no-verify -m "message"
```

### Disable All Hooks in Settings

Temporarily remove hooks from `.claude/settings.json` or comment them out:

```json
{
  "hooks": {
    // "pre-commit": { ... }  // Commented out
  }
}
```

### Disable Specific Hook Type

Remove only problematic hooks from settings file.

## Troubleshooting

### Hooks Not Running

**Check 1**: Verify settings file syntax
```bash
# Validate JSON
jq . .claude/settings.json

# Or Python
python3 -m json.tool .claude/settings.json
```

**Check 2**: Verify scripts are executable
```bash
ls -la .claude/hooks/scripts/
# Should show -rwxr-xr-x permissions
chmod +x .claude/hooks/scripts/*.sh
```

**Check 3**: Test hook directly
```bash
# Simulate hook environment
FILE_PATH="backend/app/main.py" .claude/hooks/scripts/python-lint.sh
```

### Hooks Running Too Slow

**Issue**: Post-edit hooks taking > 2 seconds

**Solutions**:
1. Move to pre-commit instead of post-edit
2. Use file filtering to limit scope
3. Enable incremental/cache modes in tools
4. Run full checks asynchronously on CI instead

### Hooks Failing Inconsistently

**Issue**: Hook fails sometimes but not always

**Debugging**:
1. Run hook script directly with same file
2. Check environment variables are set
3. Verify dependencies installed (uv, npm packages)
4. Look for race conditions with other processes

## Best Practices

### 1. Validate Early, Provide Clear Feedback

Validate at pre-commit stage to catch issues before code changes. Provide specific, actionable error messages:

```bash
# Bad: Generic error
echo "Error"
exit 1

# Good: Specific error with fix
echo "❌ OPENAI_API_KEY not set"
echo "Fix: export OPENAI_API_KEY=sk-proj-your-key"
exit 1
```

### 2. Use Clear Visual Indicators

```bash
✓ Passed check
✗ Failed critical check
⚠ Warning - non-critical issue
→ Information/action item
```

### 3. Keep Hooks Fast

- Use file filtering to limit scope
- Enable incremental/cache modes
- Run expensive checks asynchronously on CI

### 4. Document Dependencies

Clearly document which tools must be installed:

```bash
# Hook requires:
# - uv >= 0.4.0
# - Python >= 3.11
# - Backend dependencies: uv sync
```

### 5. Provide Actionable Guidance

When validation fails, guide users to fix it:

```bash
if [ ! -d "backend" ]; then
  echo "❌ 'backend' directory not found"
  echo "→ Run from repository root"
  exit 2
fi
```

## Integration with CI/CD

Hooks are local development tools. For CI/CD, use equivalent commands:

```yaml
# GitHub Actions example
- name: Validate ChatKit Configuration
  run: .claude/hooks/scripts/chatkit-env-validator.sh

- name: Lint Python
  run: |
    cd backend
    uv run ruff format . --check
    uv run ruff check .

- name: Type Check Python
  run: |
    cd backend
    uv run mypy app/

- name: Lint TypeScript
  run: |
    cd frontend
    npm run lint
```

## Advanced Customization

### Adding Custom Validations

Create a new script in `.claude/hooks/scripts/`:

```bash
#!/bin/bash
# .claude/hooks/scripts/my-custom-validator.sh

# Read from stdin
read -r hook_input

# Validate
if ! my_check "$hook_input"; then
  echo "❌ Custom validation failed"
  exit 1
fi

echo "✓ Custom validation passed"
exit 0
```

Add to settings:

```json
{
  "hooks": {
    "pre-commit": {
      "custom-validator": {
        "command": ".claude/hooks/scripts/my-custom-validator.sh",
        "description": "My custom validation",
        "blocking": true
      }
    }
  }
}
```

### Modifying Validation Rules

Edit validation scripts to customize thresholds and rules. Example variables:

```bash
# Port conflict detector - modify monitored ports
BACKEND_PORTS=(8000 8001 8002 8003)
FRONTEND_PORTS=(5170 5171 5172 5173)

# Environment validator - add/remove required variables
REQUIRED_VARS=(
  "OPENAI_API_KEY"
  "VITE_CHATKIT_API_DOMAIN_KEY"
)
```

## Related Documentation

- [CLAUDE.md](../../CLAUDE.md) - ChatKit-OpenAI project overview
- [Svelte Hooks Documentation](./ svelte-hooks.md) - General hooks patterns
- [Claude Code Hooks Guide](https://docs.anthropic.com/claude-code/hooks-guide)
- [OpenAI Domain Allowlist](https://platform.openai.com/settings/organization/security/domain-allowlist)
- [OpenAI API Keys](https://platform.openai.com/account/api-keys)

## Contributing

To add new ChatKit-specific validations:

1. Create validation script in `.claude/hooks/scripts/`
2. Make it executable: `chmod +x script.sh`
3. Document in this file under appropriate section
4. Test thoroughly across different environments
5. Add example configuration

## Support

For issues with hooks:

1. Check [Troubleshooting](#troubleshooting) section
2. Run hook script directly to debug
3. Check hook script logs
4. Verify environment setup
5. Review [Error Messages and Fixes](#error-messages-and-fixes)
