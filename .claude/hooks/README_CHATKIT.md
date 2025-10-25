# ChatKit Validation Hooks Documentation

Complete hook system documentation for the ChatKit-OpenAI project.

## Overview

This documentation package provides comprehensive validation hooks specifically designed for the ChatKit-OpenAI architecture (FastAPI backend + React frontend). Hooks automate configuration validation, detect development issues early, and enforce code quality standards.

## What Are Hooks?

Hooks are scripts that run automatically at specific points in your development workflow:

- **Pre-Edit**: Before you start editing a file
- **Post-Edit**: After you save a file
- **Pre-Commit**: Before you create a git commit

## Why Use ChatKit Hooks?

1. **Catch Errors Early**: Validate configuration before they cause runtime errors
2. **Maintain Code Quality**: Auto-format and lint Python and TypeScript
3. **Manage Complexity**: Monitor ports, dependencies, and configuration
4. **Improve Workflow**: Automate repetitive validation tasks
5. **Team Consistency**: Enforce standards across the team

## Documentation Files

### Quick Start

- **[chatkit-quick-reference.md](./chatkit-quick-reference.md)**: 2-5 minute setup guide, common patterns, quick troubleshooting

Use this first if you want to get started immediately.

### Setup and Installation

- **[CHATKIT_SETUP.md](./CHATKIT_SETUP.md)**: Detailed setup instructions, configuration options by use case, troubleshooting guide

Use this to install and configure hooks properly.

### Comprehensive Reference

- **[chatkit-validation.md](./chatkit-validation.md)**: Complete documentation of all hooks, validation patterns, implementation details, error messages

Use this for detailed information about specific hooks and validations.

## Quick Start (2 Minutes)

### 1. Copy Configuration

```bash
# Choose one based on your needs:

# Solo developer (minimal)
cp .claude/hooks/examples/settings-chatkit-minimal.json .claude/settings.json

# Team project (comprehensive)
cp .claude/hooks/examples/settings-chatkit-comprehensive.json .claude/settings.json

# Or custom setup
touch .claude/settings.json  # and add configuration
```

### 2. Set Environment

```bash
export OPENAI_API_KEY=sk-proj-your-key
export VITE_CHATKIT_API_DOMAIN_KEY=dev-key-local
```

### 3. Test

```bash
# Verify environment
.claude/hooks/scripts/chatkit-env-validator.sh

# Expected: ✓ All validations passed
```

Done! Hooks will now run on your commits.

## Hook Types

### Environment Validation

**Validates**: OPENAI_API_KEY, ChatKit domain keys, and configuration

**When**: Before commits (blocking) - prevents invalid commits

**Impact**: 50-100ms

```bash
# Manually check
.claude/hooks/scripts/chatkit-env-validator.sh
```

### Port Conflict Detection

**Validates**: All ChatKit ports (8000-8003, 5170-5173) are available

**When**: Before commits (warning)

**Impact**: 100-200ms

```bash
# Manually check
.claude/hooks/scripts/port-conflict-detector.sh
```

### Configuration Validation

**Validates**: vite.config.ts, app/constants.py, endpoint configuration

**When**: Before editing configuration files

**Impact**: 100-150ms

```bash
# Manually check
.claude/hooks/scripts/chatkit-config-validator.sh
```

### Python Linting

**Validates**: Python syntax, style, potential errors with ruff

**When**: After saving Python files

**Impact**: 200-500ms per file

```bash
# Manually run
cd backend && uv run ruff check .
```

### Python Type Checking

**Validates**: Type annotations with mypy

**When**: After saving backend Python files

**Impact**: 1-2 seconds (incremental)

```bash
# Manually run
cd backend && uv run mypy app/
```

### TypeScript Linting

**Validates**: TypeScript syntax, React issues, style

**When**: After saving TypeScript files

**Impact**: 500ms-1.5s per project

```bash
# Manually run
cd frontend && npm run lint
```

## File Structure

```
.claude/hooks/
├── README_CHATKIT.md              # This file - overview and index
├── chatkit-validation.md           # Comprehensive reference
├── chatkit-quick-reference.md      # Quick lookup guide
├── CHATKIT_SETUP.md               # Setup instructions
├── scripts/                         # Hook implementation scripts
│   ├── chatkit-env-validator.sh
│   ├── chatkit-config-validator.sh
│   ├── port-conflict-detector.sh
│   ├── domain-allowlist-validator.sh
│   └── ... (other validators)
└── examples/                        # Example configurations
    ├── settings-chatkit-minimal.json
    ├── settings-chatkit-comprehensive.json
    ├── settings-chatkit-performance.json
    └── settings-chatkit-team.json
```

## Configuration Examples

### Minimal (2 hooks)

Essential validations only:

```json
{
  "hooks": {
    "pre-commit": {
      "chatkit-env": {
        "command": ".claude/hooks/scripts/chatkit-env-validator.sh",
        "blocking": true
      }
    }
  }
}
```

**Use for**: Quick feedback, minimal overhead

**File**: `settings-chatkit-minimal.json`

### Recommended (5 hooks)

Best balance of coverage and performance:

```json
{
  "hooks": {
    "post-edit": {
      "python-lint": { ... },
      "typescript-lint": { ... }
    },
    "pre-commit": {
      "chatkit-env": { "blocking": true },
      "port-check": { "blocking": false }
    }
  }
}
```

**Use for**: Most projects

**File**: `settings-chatkit-comprehensive.json`

### Comprehensive (8+ hooks)

Full validation and automation:

```json
{
  "hooks": {
    "pre-edit": {
      "config-check": { ... }
    },
    "post-edit": {
      "python-format": { ... },
      "python-lint": { ... },
      "python-type": { ... },
      "typescript-lint": { ... }
    },
    "pre-commit": {
      "chatkit-env": { ... },
      "port-check": { ... },
      "domain-check": { ... }
    }
  }
}
```

**Use for**: Teams, strict standards

**File**: `settings-chatkit-team.json`

## Common Workflows

### Development

```bash
# 1. Set environment
export OPENAI_API_KEY=sk-proj-...
export VITE_CHATKIT_API_DOMAIN_KEY=dev-key

# 2. Start development
npm start
# Backend at 8000, frontend at 5170
# Post-edit hooks run automatically as you save

# 3. Commit changes
git add .
git commit -m "feature: add new tool"
# Pre-commit hooks validate before commit
```

### Debugging Hook Issues

```bash
# 1. Check syntax
python3 -m json.tool .claude/settings.json

# 2. Run hook manually
.claude/hooks/scripts/chatkit-env-validator.sh

# 3. Check permissions
chmod +x .claude/hooks/scripts/*.sh

# 4. Test directly
FILE_PATH="backend/app/main.py" \
  .claude/hooks/scripts/python-lint.sh
```

### Disabling Hooks (Temporarily)

```bash
# For single commit
git commit --no-verify -m "message"

# Temporarily disable all hooks
# Edit .claude/settings.json and remove/comment hooks
```

## Common Issues and Fixes

| Issue | Quick Fix |
|-------|-----------|
| `OPENAI_API_KEY not set` | `export OPENAI_API_KEY=sk-proj-your-key` |
| Port 8000 in use | `lsof -i :8000 \| grep LISTEN \| awk '{print $2}' \| xargs kill` |
| JSON syntax error | `python3 -m json.tool .claude/settings.json` |
| Hooks not running | `chmod +x .claude/hooks/scripts/*.sh` |
| Linting too slow | Move checks to pre-commit or use `--fix` flags |

See [CHATKIT_SETUP.md](./CHATKIT_SETUP.md) Troubleshooting section for detailed fixes.

## Environment Variables Required

### Backend

```bash
OPENAI_API_KEY=sk-proj-...  # Required for OpenAI API
```

Format: Must start with `sk-proj-` followed by alphanumeric characters.

Get from: https://platform.openai.com/account/api-keys

### Frontend

```bash
VITE_CHATKIT_API_DOMAIN_KEY=your-domain-key  # ChatKit configuration
```

For development: Any non-empty string works (e.g., `dev-key-local`)

For production: Register domain at https://platform.openai.com/settings/organization/security/domain-allowlist and use generated key.

### Examples (Optional)

```bash
VITE_SUPPORT_CHATKIT_API_DOMAIN_KEY=...      # Customer support example
VITE_KNOWLEDGE_CHATKIT_API_DOMAIN_KEY=...    # Knowledge assistant example
VITE_MARKETING_CHATKIT_API_DOMAIN_KEY=...    # Marketing assets example
```

## Port Usage

ChatKit uses these ports (verify with hooks):

| Application | Backend | Frontend | Note |
|------------|---------|----------|------|
| Base | 8000 | 5170 | Main template |
| Customer Support | 8001 | 5171 | Example 1 |
| Knowledge Assistant | 8002 | 5172 | Example 2 |
| Marketing Assets | 8003 | 5173 | Example 3 |

Check availability: `.claude/hooks/scripts/port-conflict-detector.sh`

## Tools Required

- **Backend**: `uv` (Python package manager), `ruff` (linter), `mypy` (type checker)
- **Frontend**: `node`/`npm`, `eslint` (linter), `prettier` (formatter)

Install with:

```bash
cd backend && uv sync
cd frontend && npm install
```

## Code Quality Standards

### Python (Backend)

- **Formatter**: `ruff format`
- **Linter**: `ruff check`
- **Type Checker**: `mypy`

Configuration: `pyproject.toml` (backend root)

### TypeScript (Frontend)

- **Formatter**: `prettier`
- **Linter**: `eslint`

Configuration: `eslint.config.js`, `.prettierrc`

## Next Steps

1. **Quick Start**: Follow [chatkit-quick-reference.md](./chatkit-quick-reference.md) (2-5 min)
2. **Setup**: Follow [CHATKIT_SETUP.md](./CHATKIT_SETUP.md) for your use case
3. **Reference**: Use [chatkit-validation.md](./chatkit-validation.md) for detailed info
4. **Customize**: Modify hooks as needed for your project

## How to Navigate

### I want to...

**Get started quickly**
→ Go to [chatkit-quick-reference.md](./chatkit-quick-reference.md)

**Install hooks properly**
→ Go to [CHATKIT_SETUP.md](./CHATKIT_SETUP.md)

**Understand hook details**
→ Go to [chatkit-validation.md](./chatkit-validation.md)

**Find example configurations**
→ Look in `examples/` directory

**Fix a specific issue**
→ Check Troubleshooting in relevant doc

**See quick commands**
→ [chatkit-quick-reference.md](./chatkit-quick-reference.md) has a Commands section

**Understand architecture**
→ See [../../CLAUDE.md](../../CLAUDE.md)

## Integration with CI/CD

Hooks are local development tools. For CI/CD (GitHub Actions, etc.), mirror the same checks:

```yaml
- name: Lint Backend
  run: cd backend && uv run ruff check .

- name: Type Check Backend
  run: cd backend && uv run mypy app/

- name: Lint Frontend
  run: cd frontend && npm run lint
```

See [chatkit-validation.md](./chatkit-validation.md) "Integration with CI/CD" section for full examples.

## Performance

Typical execution times:

- Environment validation: 50-100ms
- Port detection: 100-200ms
- Python linting (single file): 200-300ms
- Python type checking: 1-2 seconds
- TypeScript linting: 500ms-1.5s

**Optimization**: Post-edit hooks run on file saves (non-blocking), pre-commit hooks run before commits (can be slow).

## Architecture

Hooks follow the official Claude Code format:

- **Input**: Via command-line arguments and environment variables
- **Output**: To stdout/stderr with clear status indicators
- **Exit Codes**:
  - `0`: Validation passed
  - `1`: Validation failed
  - `2`: Script execution error

See [chatkit-validation.md](./chatkit-validation.md) "Validation Scripts Reference" for implementation details.

## Support and Resources

- **Official Hooks Guide**: https://docs.anthropic.com/claude-code/hooks-guide
- **ChatKit Documentation**: https://docs.anthropic.com/chatkit
- **OpenAI Platform**: https://platform.openai.com
- **Project Repository**: [chatkit-openai](https://github.com/your-repo/chatkit-openai)

## Contributing

To add new validations:

1. Create script in `.claude/hooks/scripts/`
2. Document in [chatkit-validation.md](./chatkit-validation.md)
3. Add example configuration in `examples/`
4. Test thoroughly

## Related Documentation

- [CLAUDE.md](../../CLAUDE.md) - Full project documentation
- [svelte-hooks.md](./svelte-hooks.md) - General hooks patterns
- [Claude Code Hooks Docs](https://docs.anthropic.com/claude-code/hooks-guide)

## Summary

ChatKit hooks provide:

1. **Environment Validation** - Catch missing API keys before coding
2. **Port Management** - Avoid conflicts when running multiple services
3. **Configuration Checks** - Verify settings are correct
4. **Code Quality** - Auto-lint and type-check Python and TypeScript
5. **Developer Experience** - Quick feedback on issues

Start with the minimal setup and add hooks gradually based on your project needs.

---

For questions or issues, start with [CHATKIT_SETUP.md](./CHATKIT_SETUP.md) troubleshooting section.
