# React/Vite Hooks for Claude Code

This document provides comprehensive hook configurations for React 19 + Vite + TypeScript projects using Claude Code. These hooks help maintain code quality, ensure type safety, catch errors early, and automate common development tasks in the ChatKit-OpenAI development workflow.

## Table of Contents

1. [Overview](#overview)
2. [Hook Types & Events](#hook-types--events)
3. [Exit Codes & Behavior](#exit-codes--behavior)
4. [Core Development Hooks](#core-development-hooks)
5. [Hook Scripts Reference](#hook-scripts-reference)
6. [Configuration Examples](#configuration-examples)
7. [Integration Points](#integration-points)
8. [Best Practices](#best-practices)
9. [Troubleshooting](#troubleshooting)

## Overview

This project uses hooks to enforce code quality standards across React/Vite development. Hooks run at different points in your development workflow:

- **Pre-edit hooks**: Validate state before editing
- **Post-edit hooks**: Validate changes after editing
- **Pre-commit hooks**: Verify code before Git commits
- **Post-tool-use hooks**: Run automated checks and fixes

### Tech Stack

- **Frontend**: React 19 + Vite 7 + TypeScript 5
- **Testing**: Vitest
- **Linting**: ESLint with TypeScript support
- **Formatting**: Prettier
- **CSS**: Tailwind CSS + PostCSS
- **Build Tool**: Vite

## Hook Types & Events

Claude Code supports multiple hook events that run at different stages of development:

### Hook Event Categories

#### PreToolUse Hooks
Run **before** a tool is executed. Useful for:
- Validation checks that prevent tool execution
- Pre-flight checks
- Bash command safety validation

Exit codes:
- **0**: Allow tool to proceed
- **1**: Warning, allow tool to proceed with caution
- **2**: Block tool execution

#### PostToolUse Hooks
Run **after** a tool completes. Useful for:
- Formatting and linting
- Type checking
- Test execution
- Build validation

Exit codes:
- **0**: Success, continue
- **1**: Warning logged, continue
- **2**: Block with error message

#### UserPromptSubmit Hooks
Run when user submits a prompt. Useful for:
- Prompt validation
- Context enhancement
- Security checks

#### Stop Hooks
Run when Claude Code session ends. Useful for:
- Session cleanup
- Summary generation
- Report writing

### Tool Matchers

Use matchers to target specific Claude Code tools:

```json
{
  "matcher": "Write|Edit|MultiEdit|Bash|ReadFile|CreateNewFile"
}
```

Common matchers for React/Vite work:
- `Write|Edit|MultiEdit`: File editing operations
- `Bash`: Shell command execution
- `ReadFile`: File reading

## Exit Codes & Behavior

Hooks follow a standard exit code convention:

### Exit Code 0: Pass
- Hook validation succeeded
- Tool execution proceeds normally
- Used by: All successful checks

### Exit Code 1: Warning
- Non-critical issue detected
- Tool execution continues
- Warning is logged and shown to user
- Used by: Optional linting, non-blocking checks

### Exit Code 2: Block
- Critical issue detected
- Tool execution is blocked
- Error message shown to user
- Used by: Type errors, syntax errors, failed builds

### Special JSON Responses

Hooks can output JSON to control behavior:

```json
{
  "decision": "block",
  "reason": "Error message to show user"
}
```

```json
{
  "suppressOutput": true
}
```

## Core Development Hooks

### 1. TypeScript Type Checking (Post-Edit)

**Purpose**: Validates TypeScript syntax and type safety after file edits

**Script**: `.claude/hooks/scripts/typescript-check.py`

**Triggers on**: `Write`, `Edit`, `MultiEdit` on `.ts`, `.tsx` files

**Behavior**:
- Runs incremental TypeScript checking
- Shows first 5 errors per file
- Blocks on type errors
- Skips check if `tsconfig.json` not found

**Configuration**:
```json
{
  "type": "command",
  "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/typescript-check.py",
  "timeout": 30
}
```

**Performance**: 200-500ms for incremental checks

### 2. Code Formatting & Linting (Post-Edit)

**Purpose**: Auto-formats code with Prettier and lints with ESLint

**Script**: `.claude/hooks/scripts/format-and-lint.sh`

**Triggers on**: `Write`, `Edit`, `MultiEdit` on `.ts`, `.tsx`, `.js`, `.jsx` files

**Behavior**:
- Runs Prettier for code formatting
- Runs ESLint with auto-fix
- Blocks on unfixable ESLint errors
- Logs warnings but continues on style warnings

**Configuration**:
```json
{
  "type": "command",
  "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/format-and-lint.sh",
  "timeout": 10
}
```

**Performance**: 100-300ms per file

**What gets formatted**:
- JavaScript/TypeScript files
- Indentation and spacing
- Quote style
- Semicolon usage
- Import ordering (if configured)

### 3. Test Runner (Post-Edit & Pre-Commit)

**Purpose**: Automatically runs tests for modified components

**Script**: `.claude/hooks/scripts/test-runner.sh`

**Triggers on**:
- `Write`, `Edit`, `MultiEdit` (Post-edit with warning)
- Pre-commit (Full test suite)

**Behavior**:
- Pre-edit: Warns if existing tests are failing
- Post-edit: Finds and runs related tests
- Pre-commit: Runs full test suite
- Reminds to create tests for new components

**Configuration**:
```json
{
  "type": "command",
  "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/test-runner.sh",
  "timeout": 60
}
```

**Performance**:
- Component tests: 100-500ms
- Full suite: 1-5 seconds

**Test file patterns detected**:
- `ComponentName.test.ts`
- `ComponentName.spec.ts`
- `tests/ComponentName.test.ts`
- `__tests__/ComponentName.test.ts`

### 4. Component Complexity Analysis (Post-Edit)

**Purpose**: Analyzes component complexity and suggests improvements

**Script**: `.claude/hooks/scripts/component-analyzer.py`

**Triggers on**: `Write`, `Edit`, `MultiEdit` on `.tsx` files

**Behavior**:
- Checks component size (warn if > 200 lines)
- Counts props, state variables, effects
- Detects nested loop complexity
- Warns about memory leak patterns
- Suggests refactoring

**Configuration**:
```json
{
  "type": "command",
  "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/component-analyzer.py",
  "timeout": 10
}
```

**Thresholds**:
- **Max lines**: 200 (refactor warning)
- **Max props**: 10 (interface warning)
- **Max state vars**: 8 (state management warning)
- **Max effects**: 5 (effect warning)

### 5. Bundle Size Monitoring (Post-Edit)

**Purpose**: Tracks bundle size impact of changes

**Script**: `.claude/hooks/scripts/bundle-size-check.py`

**Triggers on**: `Write`, `Edit`, `MultiEdit` on `.ts`, `.tsx` files

**Behavior**:
- Triggers build check on component changes
- Monitors build size impact
- Warns if bundle increases > 50KB
- Blocks if total bundle > 500KB

**Configuration**:
```json
{
  "type": "command",
  "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/bundle-size-check.py",
  "timeout": 120
}
```

**Thresholds**:
- **Max bundle size**: 500KB
- **Max increase per change**: 50KB

### 6. Bash Command Validation (Pre-Tool)

**Purpose**: Validates bash commands for safety and best practices

**Script**: `.claude/hooks/scripts/bash-validator.py`

**Triggers on**: `Bash` tool execution

**Behavior**:
- Detects dangerous commands (rm -rf, etc.)
- Suggests safer alternatives (ripgrep over grep)
- Warns about unquoted variables
- Suggests use of specific tools over general ones

**Configuration**:
```json
{
  "type": "command",
  "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/bash-validator.py",
  "timeout": 5
}
```

**Performance**: < 50ms

**Common validations**:
- `grep` → suggest `ripgrep` (faster)
- `find` → suggest `fd` (faster)
- `cat` → suggest `Read` tool
- Dangerous patterns like `rm -rf /`

## Hook Scripts Reference

All hook scripts read JSON from stdin and follow the Claude Code hook protocol:

### Input Format (stdin)

```json
{
  "tool_name": "Write|Edit|MultiEdit|Bash|etc",
  "tool_input": {
    "file_path": "/absolute/path/to/file.ts",
    "content": "file content (for some tools)"
  },
  "hook_event_name": "PreToolUse|PostToolUse|etc",
  "context": {
    "project_dir": "/path/to/project"
  }
}
```

### Output Format (stdout)

**Success (exit 0)**:
```json
{
  "suppressOutput": true
}
```

**Block with error (exit 0 for post-tools)**:
```json
{
  "decision": "block",
  "reason": "Error message explaining why it was blocked"
}
```

**Warning (exit 1)**:
```json
{
  "decision": "warn",
  "message": "Warning message to display"
}
```

### Script Location

All scripts are in `.claude/hooks/scripts/`:

- `typescript-check.py` - TypeScript type validation
- `format-and-lint.sh` - Prettier + ESLint formatting
- `test-runner.sh` - Vitest test execution
- `component-analyzer.py` - Component complexity analysis
- `bundle-size-check.py` - Bundle size monitoring
- `bash-validator.py` - Bash command validation
- `prompt-enhancer.py` - Prompt validation/enhancement

## Configuration Examples

### Minimal Setup

For quick setup with essential checks:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/format-and-lint.sh",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**What you get**:
- Auto-formatting with Prettier
- Auto-fixing ESLint issues
- Non-blocking warnings

### Recommended Setup

Balanced for most React/Vite projects:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/format-and-lint.sh",
            "timeout": 10
          },
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/typescript-check.py",
            "timeout": 30
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/bash-validator.py",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

**What you get**:
- Auto-formatting and linting
- Type safety validation
- Bash command safety checks

### Comprehensive Setup

Full-featured for teams and performance-critical apps:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/test-runner.sh",
            "timeout": 30
          }
        ]
      },
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/bash-validator.py",
            "timeout": 5
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/format-and-lint.sh",
            "timeout": 10
          },
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/typescript-check.py",
            "timeout": 30
          },
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/component-analyzer.py",
            "timeout": 10
          }
        ]
      },
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/bundle-size-check.py",
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

**What you get**:
- Pre-edit test warnings
- Post-edit formatting and type checking
- Component complexity analysis
- Bundle size monitoring
- Bash safety validation

### Frontend-Only Setup

For projects running only the frontend:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/format-and-lint.sh",
            "timeout": 10
          },
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/typescript-check.py",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

### CI/CD-Ready Setup

For production workflows:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/bash-validator.py",
            "timeout": 5
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/format-and-lint.sh",
            "timeout": 10
          },
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/typescript-check.py",
            "timeout": 30
          },
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/scripts/component-analyzer.py",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

## Integration Points

### Where Hooks Fit in Your Workflow

```
User edits file
    ↓
PreToolUse hooks (validation, warnings)
    ↓
Claude Code tool executes (Write/Edit/MultiEdit)
    ↓
PostToolUse hooks (formatting, checks, fixes)
    ↓
File is updated + feedback shown
```

### Hook Execution Timeline

1. **User initiates action**: Edits a file, runs Bash, etc.
2. **PreToolUse hooks run**: Validate preconditions, show warnings
3. **Tool executes**: Claude Code tool (Write, Edit, etc.) runs
4. **PostToolUse hooks run**: Validation, formatting, testing
5. **Results displayed**: Errors block, warnings shown, success logged

### Environment Variables Available to Hooks

```bash
CLAUDE_PROJECT_DIR    # Root directory of the project
FILE_PATH             # Absolute path to file being edited
TOOL_NAME             # Name of the tool being run (Write, Edit, etc)
```

### When Hooks Trigger

| Hook | Trigger | Tools | Purpose |
|------|---------|-------|---------|
| PreToolUse | Before tool runs | Write, Edit, MultiEdit, Bash | Validation, warnings |
| PostToolUse | After tool completes | Write, Edit, MultiEdit | Formatting, checks |
| UserPromptSubmit | User submits prompt | All | Validation, enhancement |
| Stop | Session ends | N/A | Cleanup, summary |

### Debugging Failing Hooks

**Enable debug logging**:
```bash
export CLAUDE_DEBUG=1
```

**Test hook directly**:
```bash
# Create test input
echo '{
  "tool_name": "Write",
  "tool_input": {"file_path": "src/App.tsx"},
  "hook_event_name": "PostToolUse"
}' | .claude/hooks/scripts/format-and-lint.sh
```

**Check script permissions**:
```bash
ls -la .claude/hooks/scripts/
# Should show -rwxr-xr-x permissions
```

**Run with timing**:
```bash
time .claude/hooks/scripts/typescript-check.py < input.json
```

**View hook output**:
- Claude Code displays hook results in the UI
- Check `.claude/logs/` if available
- Review any error messages in stderr

## Best Practices

### Performance Optimization

**Keep hooks fast** (< 200ms target):
- Aim for instant execution for simple checks
- Use incremental compilation where possible
- Cache results between runs
- Run expensive checks only on commit

**Performance tiers**:

| Speed | Examples | Acceptable? |
|-------|----------|------------|
| < 50ms | Echo, grep, simple checks | Excellent |
| 50-200ms | Prettier, single file lint | Good |
| 200-500ms | TypeScript checking, small tests | Acceptable |
| > 500ms | Full builds, test suites | Consider moving to pre-commit |

**Optimization tips**:

1. **Use incremental builds**: TypeScript supports `--incremental`
2. **Cache when possible**: Store compilation info between runs
3. **Filter by file type**: Skip hooks for irrelevant files
4. **Use `|| true`**: Prevent non-blocking hooks from failing
5. **Parallelize**: Run independent checks together

### Clear Error Messages

**Good error messages explain**:
- What went wrong
- Why it matters
- How to fix it

```
Example:
TypeScript error TS2339 in ChatKitPanel.tsx:
Property 'unknown' does not exist on type 'ClientToolCall'
at line 45: ctx.context.client_tool_call.unknown = value

Fix: Use defined properties like 'name' or 'arguments'
```

### Proper Exit Codes

**Use exit codes correctly**:
- **Exit 0**: Always on success
- **Exit 1**: For warnings (continues execution)
- **Exit 2**: For blocking errors (in PreToolUse)
- **Exit 0 with JSON**: For PostToolUse blocks (decode from JSON)

### File Type Filtering

**Only run hooks on relevant files**:

```bash
# Only TypeScript/React files
if [[ "$FILE_PATH" =~ \.(ts|tsx)$ ]]; then
    # Run check
fi

# Skip node_modules and .venv
if [[ "$FILE_PATH" =~ (node_modules|\.venv)/ ]]; then
    exit 0
fi
```

### Hook Naming Conventions

**Name hooks clearly**:
- `typescript-check`: What it does, not "type-validator"
- `format-and-lint`: Combined purpose clear
- `bundle-size-check`: Specific metric being checked

### Documentation

**Document custom hooks with**:
- What they validate
- When they trigger
- How to disable them
- Performance impact
- Common failures and solutions

### Testing Hooks

**Before adding to team config**:
1. Test in isolation
2. Measure performance
3. Check error messages
4. Verify file path handling
5. Test with edge cases (empty files, large files, etc.)

## Troubleshooting

### Hooks Not Running

**Check if hooks are registered**:
```bash
# Verify .claude/settings.json exists
test -f .claude/settings.json && echo "Config found"

# Validate JSON syntax
jq . .claude/settings.json
```

**Verify scripts are executable**:
```bash
ls -la .claude/hooks/scripts/
# Should show -rwxr-xr-x or -rwxrwxr-x
chmod +x .claude/hooks/scripts/*.py
chmod +x .claude/hooks/scripts/*.sh
```

**Check Claude Code logs**:
```bash
tail -f ~/.claude/logs/claude.log
# Look for hook execution messages
```

**Test hook manually**:
```bash
echo '{"tool_name":"Write","tool_input":{"file_path":"test.tsx"}}' | \
  .claude/hooks/scripts/typescript-check.py
```

### Hooks Too Slow

**Identify the slow hook**:
```bash
time .claude/hooks/scripts/typescript-check.py < input.json
```

**Solutions**:
1. Increase timeout in settings.json
2. Use incremental compilation
3. Cache results
4. Move to pre-commit instead of post-edit
5. Run in parallel with other hooks

### Hooks Failing

**Common issues**:

| Error | Cause | Solution |
|-------|-------|----------|
| `ModuleNotFoundError` | Python dependency missing | `npm install` in project |
| `command not found: npx` | Node.js not in PATH | Check `npm --version` |
| `jq: command not found` | jq not installed | Install jq or use Python fallback |
| Timeout | Hook takes too long | Increase timeout or optimize hook |
| Permission denied | Script not executable | `chmod +x scripts/*.sh` |

**Debug a failing hook**:

1. Run script manually with test input
2. Check stderr output
3. Verify dependencies installed
4. Check file permissions
5. Review timeout settings

### TypeScript Errors Not Caught

**Ensure TypeScript is configured**:
```bash
# Check tsconfig.json exists
test -f tsconfig.json && echo "TypeScript configured"
```

**Verify tsconfig.json includes your files**:
```json
{
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

**Run TypeScript manually**:
```bash
npx tsc --noEmit
# Should show same errors as hook
```

### Linting Errors Not Fixed

**Check ESLint configuration**:
```bash
test -f .eslintrc.json && echo "ESLint configured"
npx eslint --version
```

**Run ESLint manually**:
```bash
npx eslint src/App.tsx --fix
# Should apply fixes same as hook
```

**Some rules can't be auto-fixed**:
- Manual fixes may be required
- Check error output for specific rule
- Update ESLint config if rule is too strict

### Tests Not Running

**Ensure test files exist**:
```bash
# Look for test files
find . -name "*.test.ts" -o -name "*.spec.ts" | head -5
```

**Verify Vitest is configured**:
```bash
test -f vitest.config.ts && echo "Vitest configured"
npx vitest --version
```

**Run tests manually**:
```bash
cd frontend
npm test  # or npx vitest
```

## Performance Benchmarks

Typical hook execution times on modern hardware:

| Hook | File Type | Time | Notes |
|------|-----------|------|-------|
| Format-and-lint | .tsx | 100-200ms | Depends on file size |
| TypeScript-check | .tsx | 200-500ms | First run slower (build cache) |
| Component-analyzer | .tsx | 50-100ms | Regex-based, very fast |
| Bundle-size-check | Any | 5-60s | Requires full build |
| Bash-validator | N/A | < 50ms | Instant |
| Test-runner | .tsx | 100-2000ms | Depends on test count |

## Configuration File Locations

**Global Claude Code settings** (all projects):
```
~/.config/claude/settings.json
```

**Project-specific settings** (this project only):
```
.claude/settings.json
```

Project settings override global settings.

## Related Documentation

- **CLAUDE.md**: Project-specific architecture and commands
- **svelte-hooks.md**: Reference for Svelte hook patterns (some overlap)
- **IMPLEMENTATION.md**: Hook system implementation details
- [Claude Code Hooks Guide](https://docs.anthropic.com/claude-code/hooks-guide)
- [Vite Documentation](https://vitejs.dev/guide/)
- [React Documentation](https://react.dev/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)

## Summary

React/Vite hooks in Claude Code provide automated validation, formatting, and testing to maintain code quality. Start with the recommended setup and customize based on your team's needs. Keep hooks fast, provide clear feedback, and integrate them into your development workflow for maximum benefit.

For questions or issues, refer to the troubleshooting section or consult the Claude Code documentation.
