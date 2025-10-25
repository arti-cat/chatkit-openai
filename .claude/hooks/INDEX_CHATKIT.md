# ChatKit Hooks Documentation Index

Complete reference guide for all ChatKit validation hook documentation files.

## Quick Navigation

### I have 2 minutes
Start here: [chatkit-quick-reference.md](./chatkit-quick-reference.md)
- Quick setup instructions
- Common patterns
- Troubleshooting

### I have 10 minutes
Follow: [CHATKIT_SETUP.md](./CHATKIT_SETUP.md)
- Complete setup guide
- Configuration options by use case
- Detailed troubleshooting

### I need details
Read: [chatkit-validation.md](./chatkit-validation.md)
- Comprehensive documentation
- All hook types and validations
- Error messages and fixes
- Implementation patterns

### I'm creating validation scripts
Reference: [VALIDATION_SCRIPTS_SPEC.md](./VALIDATION_SCRIPTS_SPEC.md)
- Script interface specifications
- Input/output format
- Exit codes
- Performance requirements

## Documentation Files

### README_CHATKIT.md (Start here for overview)
**Purpose**: Main entry point, overview of all hooks

**Length**: ~4 minutes to read

**Contains**:
- What hooks are and why use them
- File structure overview
- Common workflows
- Quick troubleshooting
- How to navigate other docs

**Best for**: Getting oriented, understanding the system

---

### chatkit-validation.md (Comprehensive reference)
**Purpose**: Detailed documentation of all ChatKit-specific validations

**Length**: ~15 minutes to read, used as reference

**Contains**:
- Overview of validation system
- 6 validation hook categories with full configuration
- Hook configuration examples
- Validation patterns and implementation
- All error messages with fixes
- Integration with development workflow
- Best practices
- Related documentation

**Best for**: Understanding specific hooks, error resolution, implementation details

---

### chatkit-quick-reference.md (Lookup guide)
**Purpose**: Quick reference for common tasks and commands

**Length**: ~3 minutes to read, used as lookup

**Contains**:
- 1-minute and 5-minute setups
- Essential hook configurations
- Port and environment variable reference
- Common code patterns
- Error quick fixes table
- File type detection patterns
- Configuration examples
- Common tasks and commands

**Best for**: Quick lookup, solving problems fast, common patterns

---

### CHATKIT_SETUP.md (Installation guide)
**Purpose**: Step-by-step setup instructions and configuration

**Length**: ~10 minutes

**Contains**:
- Prerequisites and verification
- Step-by-step installation
- Environment setup
- Testing configuration
- Configuration options (minimal, recommended, comprehensive)
- Customization guide
- Detailed troubleshooting
- Performance tips

**Best for**: Setting up hooks correctly, debugging setup issues, customizing

---

### VALIDATION_SCRIPTS_SPEC.md (Script specification)
**Purpose**: Specification for validation script implementation

**Length**: ~10 minutes to read, used as reference

**Contains**:
- Script interface standards
- Detailed specification for each validation script:
  - chatkit-env-validator.sh
  - chatkit-config-validator.sh
  - port-conflict-detector.sh
  - domain-allowlist-validator.sh
- Input/output format specifications
- Exit code definitions
- Performance requirements
- Dependencies
- Testing patterns
- Future enhancements

**Best for**: Implementing validation scripts, understanding requirements

---

### INDEX_CHATKIT.md (This file)
**Purpose**: Navigation and file organization

**Contains**:
- File directory with descriptions
- Quick navigation guide
- Usage recommendations
- File relationships

---

## Example Configuration Files

All example files are valid JSON configurations ready to use.

### settings-chatkit-minimal.json
**Size**: ~1KB

**Use**: Solo developers, fast feedback, minimal overhead

**Features**:
- Pre-commit environment validation (blocking)
- Post-edit Python linting (non-blocking)
- Post-edit TypeScript linting (non-blocking)

**Hooks**: 3 total

**Setup time**: 2 minutes

---

### settings-chatkit-comprehensive.json
**Size**: ~2KB

**Use**: Most projects, balanced coverage

**Features**:
- Pre-edit configuration checks
- Post-edit Python formatting and linting
- Post-edit Python type checking
- Post-edit TypeScript linting
- Pre-commit environment validation (blocking)
- Pre-commit port conflict detection
- Pre-commit domain validation

**Hooks**: 7 total

**Setup time**: 5 minutes

---

### settings-chatkit-performance.json
**Size**: ~1.6KB

**Use**: Large projects, optimize for speed

**Features**:
- Quick post-edit linting on edited files only
- Comprehensive pre-commit checks
- Full Python and TypeScript validation on commit

**Hooks**: 5 total

**Setup time**: 3 minutes

---

### settings-chatkit-team.json
**Size**: ~2.7KB

**Use**: Team projects, strict standards

**Features**:
- Pre-edit configuration validation
- Auto-formatting on save (Python and TypeScript)
- Full linting on save
- Comprehensive pre-commit validation
- Domain allowlist checking

**Hooks**: 8 total

**Setup time**: 7 minutes

---

## Hook Scripts

Location: `.claude/hooks/scripts/`

The following scripts should exist and be executable:

### Core Validation Scripts

**chatkit-env-validator.sh**
- Validates environment variables
- Exit codes: 0 (valid), 1 (invalid), 2 (error)
- Performance: < 100ms
- Blocking: Yes (on pre-commit)

**chatkit-config-validator.sh**
- Validates ChatKit configuration files
- Exit codes: 0 (valid), 1 (invalid), 2 (error)
- Performance: 100-200ms
- Blocking: No

**port-conflict-detector.sh**
- Detects port conflicts
- Exit codes: 0 (available), 1 (in use), 2 (error)
- Performance: 100-300ms
- Blocking: No

**domain-allowlist-validator.sh**
- Validates domain configuration
- Exit codes: 0 (production), 1 (development), 2 (error)
- Performance: < 100ms
- Blocking: No

### Supporting Scripts

**format-and-lint.sh**, **typescript-check.py**, **bash-validator.py**, etc.
- Used for post-edit linting and formatting
- See [chatkit-validation.md](./chatkit-validation.md) for details

---

## Understanding Hook Timing

### Pre-Edit Hooks
**When**: Before you start editing a file

**Examples**: Show warnings, check prerequisites

**Use**: Quick checks, informational

### Post-Edit Hooks
**When**: After you save a file

**Examples**: Formatting, linting, type checking

**Use**: Real-time feedback, non-blocking checks

### Pre-Commit Hooks
**When**: Before git commit

**Examples**: Comprehensive validation, breaking changes

**Use**: Final validation, can be blocking

---

## File Organization Reference

```
.claude/hooks/
├── INDEX_CHATKIT.md                    # This file - navigation
├── README_CHATKIT.md                   # Overview and entry point
├── chatkit-validation.md               # Comprehensive reference (MAIN)
├── chatkit-quick-reference.md          # Quick lookup guide
├── CHATKIT_SETUP.md                    # Setup instructions
├── VALIDATION_SCRIPTS_SPEC.md          # Script specifications
│
├── scripts/                             # Validation script implementations
│   ├── chatkit-env-validator.sh        # Environment validation
│   ├── chatkit-config-validator.sh     # Configuration validation
│   ├── port-conflict-detector.sh       # Port conflict detection
│   ├── domain-allowlist-validator.sh   # Domain configuration
│   └── ... other scripts ...
│
└── examples/                            # Example configurations
    ├── settings-chatkit-minimal.json
    ├── settings-chatkit-comprehensive.json
    ├── settings-chatkit-performance.json
    └── settings-chatkit-team.json
```

---

## Common Workflows and Where to Go

### Workflow: Getting Started

1. Read [README_CHATKIT.md](./README_CHATKIT.md) (overview)
2. Copy config from `examples/settings-chatkit-minimal.json`
3. Follow [CHATKIT_SETUP.md](./CHATKIT_SETUP.md) steps
4. Reference [chatkit-quick-reference.md](./chatkit-quick-reference.md) for commands

**Time**: ~10 minutes

---

### Workflow: Fixing a Hook Issue

1. Check error message format in [chatkit-quick-reference.md](./chatkit-quick-reference.md)
2. Look up in "Error Quick Fixes" table
3. Read detailed fix in [chatkit-validation.md](./chatkit-validation.md) "Error Messages"
4. If still stuck, check [CHATKIT_SETUP.md](./CHATKIT_SETUP.md) Troubleshooting

**Time**: 2-5 minutes

---

### Workflow: Customizing Hooks

1. Choose base configuration from `examples/`
2. Read [CHATKIT_SETUP.md](./CHATKIT_SETUP.md) "Customization" section
3. Reference [chatkit-validation.md](./chatkit-validation.md) for hook details
4. Edit `.claude/settings.json`
5. Test with `python3 -m json.tool .claude/settings.json`

**Time**: 10-15 minutes

---

### Workflow: Implementing Validation Scripts

1. Read [VALIDATION_SCRIPTS_SPEC.md](./VALIDATION_SCRIPTS_SPEC.md)
2. Check desired script specification
3. Implement in `.claude/hooks/scripts/`
4. Make executable: `chmod +x script.sh`
5. Test manually before adding to hooks

**Time**: 30+ minutes per script

---

## Key Concepts

### Environment Variables
- `OPENAI_API_KEY`: Required for backend, format `sk-proj-...`
- `VITE_CHATKIT_API_DOMAIN_KEY`: Required for frontend

See [chatkit-quick-reference.md](./chatkit-quick-reference.md) for all variables.

### Ports
- Base: 8000 (backend), 5170 (frontend)
- Examples: 8001/5171, 8002/5172, 8003/5173

See [chatkit-quick-reference.md](./chatkit-quick-reference.md) port table.

### Exit Codes
- 0: Validation passed
- 1: Validation failed (not a script error)
- 2: Script execution error

See [VALIDATION_SCRIPTS_SPEC.md](./VALIDATION_SCRIPTS_SPEC.md) for details.

---

## Recommended Reading Order

1. **First time**: Start with [README_CHATKIT.md](./README_CHATKIT.md)
2. **Setting up**: Follow [CHATKIT_SETUP.md](./CHATKIT_SETUP.md)
3. **Daily use**: Reference [chatkit-quick-reference.md](./chatkit-quick-reference.md)
4. **Deep dive**: Read [chatkit-validation.md](./chatkit-validation.md)
5. **Implementation**: Refer to [VALIDATION_SCRIPTS_SPEC.md](./VALIDATION_SCRIPTS_SPEC.md)

---

## Related Documentation

- Project overview: [../../CLAUDE.md](../../CLAUDE.md)
- General hooks: [README.md](./README.md)
- Svelte hooks: [svelte-hooks.md](./svelte-hooks.md)
- React/Vite hooks: [react-vite-hooks.md](./react-vite-hooks.md)

---

## Quick Links

| Need | File | Section |
|------|------|---------|
| Get started | [README_CHATKIT.md](./README_CHATKIT.md) | Overview |
| Setup guide | [CHATKIT_SETUP.md](./CHATKIT_SETUP.md) | Installation Steps |
| Quick lookup | [chatkit-quick-reference.md](./chatkit-quick-reference.md) | Essential Setup |
| Full reference | [chatkit-validation.md](./chatkit-validation.md) | All Hooks |
| Script details | [VALIDATION_SCRIPTS_SPEC.md](./VALIDATION_SCRIPTS_SPEC.md) | Script Overview |
| Config examples | `examples/` | All JSON files |
| Project info | [../../CLAUDE.md](../../CLAUDE.md) | Overview |

---

## File Statistics

| File | Size | Read Time | Use |
|------|------|-----------|-----|
| README_CHATKIT.md | 13KB | 4 min | Overview |
| chatkit-validation.md | 23KB | 15 min | Reference |
| chatkit-quick-reference.md | 9.2KB | 3 min | Lookup |
| CHATKIT_SETUP.md | 15KB | 10 min | Setup |
| VALIDATION_SCRIPTS_SPEC.md | 14KB | 10 min | Implementation |
| Total documentation | 74KB | ~40 min | Complete learning |

---

## Support

For questions or issues:

1. Check [chatkit-quick-reference.md](./chatkit-quick-reference.md) "Troubleshooting" section
2. Read detailed fixes in [chatkit-validation.md](./chatkit-validation.md) "Error Messages"
3. Follow setup guide in [CHATKIT_SETUP.md](./CHATKIT_SETUP.md)
4. Review [README_CHATKIT.md](./README_CHATKIT.md) for overview

---

Generated with Claude Code
Last updated: October 25, 2024
