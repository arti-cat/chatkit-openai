# ChatKit Validation Scripts Specification

This document specifies the interface and behavior of validation scripts used in ChatKit hooks. These specifications define what each script should do without providing implementation details.

## Overview

Validation scripts are executable shell scripts or Python scripts that run automatically during development. They follow the official Claude Code hooks format:

- **Standard Input**: JSON-encoded hook information (optional)
- **Standard Output**: Human-readable status messages
- **Standard Error**: Error messages and warnings
- **Exit Codes**: 0 (success), 1 (validation failed), 2 (script error)

## Script: chatkit-env-validator.sh

### Purpose
Validate that all required ChatKit environment variables are set and have valid formats.

### Invocation
```bash
.claude/hooks/scripts/chatkit-env-validator.sh
```

### Environment Variables Checked
1. **OPENAI_API_KEY**
   - Required: Yes
   - Format: Must start with `sk-proj-` followed by alphanumeric characters
   - Minimum length: 20 characters
   - Error: Missing or invalid format

2. **VITE_CHATKIT_API_DOMAIN_KEY**
   - Required: Yes (warning if missing)
   - Format: Non-empty string, not a placeholder
   - Error: Placeholder or obviously invalid value

3. **VITE_SUPPORT_CHATKIT_API_DOMAIN_KEY** (optional)
   - Required: Only if `examples/customer-support` directory exists
   - Format: Non-empty string
   - Error: Missing when example directory exists

4. **VITE_KNOWLEDGE_CHATKIT_API_DOMAIN_KEY** (optional)
   - Required: Only if `examples/knowledge-assistant` directory exists
   - Format: Non-empty string
   - Error: Missing when example directory exists

5. **VITE_MARKETING_CHATKIT_API_DOMAIN_KEY** (optional)
   - Required: Only if `examples/marketing-assets` directory exists
   - Format: Non-empty string
   - Error: Missing when example directory exists

### Output Format

**Success** (exit code 0):
```
Running ChatKit environment validation...
✓ OPENAI_API_KEY is set and valid
✓ VITE_CHATKIT_API_DOMAIN_KEY is set
✓ All required variables present
```

**Partial Success** (exit code 0, with warnings):
```
Running ChatKit environment validation...
✓ OPENAI_API_KEY is set and valid
✓ VITE_CHATKIT_API_DOMAIN_KEY is set
⚠ VITE_SUPPORT_CHATKIT_API_DOMAIN_KEY not set
  (Not required unless running customer-support example)
✓ All required variables valid
```

**Failure** (exit code 1):
```
Running ChatKit environment validation...
✓ OPENAI_API_KEY is set and valid
✗ VITE_CHATKIT_API_DOMAIN_KEY not set
  Set: export VITE_CHATKIT_API_DOMAIN_KEY=local-dev-key
✗ Validation failed: 1 required variable missing
Fix: Set missing environment variables before proceeding.
```

### Exit Codes
- `0`: All required variables valid (or all missing are optional)
- `1`: One or more required variables missing or invalid
- `2`: Script execution error (can't read environment)

### Performance Requirement
< 100ms

### Dependencies
- bash/sh
- Standard utilities: grep, test, echo

### Error Messages
- "OPENAI_API_KEY not set" - Not found in environment
- "OPENAI_API_KEY has invalid format" - Doesn't match pattern
- "VITE_CHATKIT_API_DOMAIN_KEY not set" - Not found in environment
- "Variable contains placeholder value" - Set to 'TODO' or similar

---

## Script: chatkit-config-validator.sh

### Purpose
Validate ChatKit configuration files before or after editing them.

### Invocation
```bash
.claude/hooks/scripts/chatkit-config-validator.sh [FILE_PATH]
```

### Parameters
- `FILE_PATH` (optional): Path to file being edited. If provided, only validate that file.

### Files Validated (if no FILE_PATH provided)

1. **frontend/vite.config.ts**
   - Proxy `/chatkit` targets correct backend port (8000 for base, 8001-8003 for examples)
   - Proxy `/facts` targets correct backend port
   - `server.allowedHosts` configured (if production domain set)

2. **frontend/src/lib/config.ts**
   - `CHATKIT_API_DOMAIN_KEY` configuration present
   - API endpoint URLs configured
   - Backend URL matches proxy configuration

3. **backend/app/main.py**
   - `/chatkit` POST endpoint defined
   - `/facts` GET endpoint defined (if used)
   - Port configuration matches expected (8000 for base)

4. **backend/app/constants.py**
   - `MODEL` variable set (e.g., "gpt-4")
   - `SYSTEM_INSTRUCTIONS` defined
   - Required imports present

5. **Examples**: Same checks for each example with correct port numbers
   - customer-support: ports 8001/5171
   - knowledge-assistant: ports 8002/5172
   - marketing-assets: ports 8003/5173

### Output Format

**Success** (exit code 0):
```
Validating ChatKit configuration...
✓ frontend/vite.config.ts: Proxy configuration valid
✓ frontend/src/lib/config.ts: ChatKit config present
✓ backend/app/main.py: Endpoints configured correctly
✓ backend/app/constants.py: Model and instructions defined
Configuration validation passed
```

**Warning** (exit code 0):
```
Validating ChatKit configuration...
✓ frontend/vite.config.ts: Proxy configuration valid
⚠ backend/app/constants.py: Using model 'gpt-3.5-turbo'
  (Consider upgrading to 'gpt-4' for better results)
Configuration validation passed with warnings
```

**Failure** (exit code 1):
```
Validating ChatKit configuration...
✓ frontend/vite.config.ts: Proxy configuration valid
✗ frontend/src/lib/config.ts: CHATKIT_API_DOMAIN_KEY not configured
  Fix: Set VITE_CHATKIT_API_DOMAIN_KEY environment variable
✗ Configuration validation failed
Fix: Check errors above and update configuration files
```

### Exit Codes
- `0`: All configuration valid
- `1`: Configuration issues detected
- `2`: File read error or permission denied

### Performance Requirement
100-200ms

### Dependencies
- bash/sh
- grep, test, echo
- Basic file reading

### Checks to Perform

For **vite.config.ts**:
- Search for `proxy` configuration
- Verify `/chatkit` target matches backend port
- Verify `/facts` target matches backend port (if present)
- Check `allowedHosts` configuration for production domains
- Warn if using localhost for production

For **src/lib/config.ts**:
- Check for `CHATKIT_API_DOMAIN_KEY` variable
- Verify not hardcoded to placeholder
- Check API endpoint configuration
- Verify backend URL configuration

For **app/main.py**:
- Check for ChatKit server initialization
- Verify `/chatkit` endpoint defined
- Check FastAPI app initialization
- Verify port configuration in uvicorn command

For **app/constants.py**:
- Check `MODEL` variable defined
- Verify `SYSTEM_INSTRUCTIONS` present
- Check for import statements

---

## Script: port-conflict-detector.sh

### Purpose
Detect when ports required by ChatKit applications are already in use.

### Invocation
```bash
.claude/hooks/scripts/port-conflict-detector.sh
```

### Ports to Check

**Base Template**:
- 8000 (FastAPI backend)
- 5170 (React frontend)

**Examples**:
- 8001, 5171 (customer-support)
- 8002, 5172 (knowledge-assistant)
- 8003, 5173 (marketing-assets)

### Output Format

**Success - All Available** (exit code 0):
```
Checking port availability for ChatKit applications...
✓ Port 8000 (base backend) is available
✓ Port 5170 (base frontend) is available
✓ Port 8001 (customer-support backend) is available
✓ Port 5171 (customer-support frontend) is available
✓ Port 8002 (knowledge-assistant backend) is available
✓ Port 5172 (knowledge-assistant frontend) is available
✓ Port 8003 (marketing-assets backend) is available
✓ Port 5173 (marketing-assets frontend) is available
All required ports are available
```

**Partial - Some In Use** (exit code 1):
```
Checking port availability for ChatKit applications...
✓ Port 8000 (base backend) is available
✗ Port 5170 (base frontend) is in use
  Process: node (PID 12345)
  Running: react-scripts start
  Command: Kill process with: kill 12345
✓ Port 8001 (customer-support backend) is available
✓ Port 5171 (customer-support frontend) is available
...

⚠ 1 port conflict detected
Suggestions:
1. Stop the existing process: kill 12345
2. Or run example on different port: VITE_PORT=5200 npm run dev
3. Or wait for existing process to finish
```

### Exit Codes
- `0`: All ports available
- `1`: One or more ports in use
- `2`: Cannot determine port status (lsof/netstat not available)

### Performance Requirement
100-300ms

### Dependencies
- bash/sh
- Either `lsof` OR `netstat` (for port checking)
- grep, awk, sed for parsing output
- echo for output

### Detection Method

Use `lsof` if available (preferred):
```bash
lsof -i :PORT_NUMBER
```

Fallback to `netstat`:
```bash
netstat -tuln | grep ":PORT_NUMBER"
```

### Information to Capture

For each port in use:
- Process name
- Process ID (PID)
- Command line (if available)
- Suggestion to kill or use different port

---

## Script: domain-allowlist-validator.sh

### Purpose
Validate domain allowlisting configuration for production deployments.

### Invocation
```bash
.claude/hooks/scripts/domain-allowlist-validator.sh
```

### Checks to Perform

1. **Frontend Domain Configuration**
   - Look in `frontend/vite.config.ts` for `server.allowedHosts`
   - Check if using localhost (development) or domain (production)

2. **Domain Key Format**
   - Verify `VITE_CHATKIT_API_DOMAIN_KEY` is not a placeholder
   - Check for obvious test values like "test-key", "TODO", etc.

3. **Domain Registration Status** (informational)
   - Not actually checking with OpenAI API
   - But provide guidance on registration process

### Output Format

**Development Setup** (exit code 1 - informational):
```
Domain allowlist validation...
Configuration: localhost (development mode)
✓ Acceptable for local development

When ready for production:
1. Register domain at: https://platform.openai.com/settings/organization/security/domain-allowlist
2. Set VITE_CHATKIT_API_DOMAIN_KEY to the generated key
3. Update server.allowedHosts in frontend/vite.config.ts
4. Deploy frontend to registered domain
```

**Production Setup** (exit code 0):
```
Domain allowlist validation...
Configuration: my-chatapp.com (production)
✓ Domain configured for production
✓ Domain key appears valid (not placeholder)

Reminder: Ensure domain is registered at:
https://platform.openai.com/settings/organization/security/domain-allowlist
```

**Invalid Configuration** (exit code 2):
```
Domain allowlist validation...
Configuration: my-chatapp.com (production)
✗ Domain key is placeholder or invalid
✗ Invalid configuration for production

Fix:
1. Register domain at: https://platform.openai.com/settings/organization/security/domain-allowlist
2. Copy generated key to VITE_CHATKIT_API_DOMAIN_KEY
3. Update frontend/vite.config.ts with registered domain
```

### Exit Codes
- `0`: Production-ready configuration
- `1`: Development setup (acceptable)
- `2`: Invalid configuration for production

### Performance Requirement
50-100ms

### Dependencies
- bash/sh
- grep, test, echo
- Basic file reading

---

## Script Interface Standard

All scripts should:

### Input
- Accept optional FILE_PATH as first argument
- Read from environment variables as needed
- No stdin required (hooks don't pipe data)

### Output
- Use stdout for informational messages
- Use stderr for errors
- Prefix messages with symbols:
  - `✓` (U+2713): Validation passed
  - `✗` (U+2717): Validation failed
  - `⚠` (U+26A0): Warning

### Exit Codes
- `0`: Validation passed
- `1`: Validation failed (not a script error)
- `2`: Script error (execution problem)

### Error Handling
- All scripts should handle missing files gracefully
- Provide actionable error messages
- Suggest fixes or next steps
- Don't output stack traces or debug info

### Performance
- Keep execution < 500ms for post-edit hooks
- Keep execution < 2 seconds for pre-commit hooks
- Cache results where possible

### Portability
- Use POSIX shell where possible
- If Python, require only standard library
- Support macOS and Linux
- Provide fallback methods when tools not available

---

## Testing Validation Scripts

### Unit Testing Pattern

```bash
# Test environment validation
OPENAI_API_KEY=sk-proj-test \
VITE_CHATKIT_API_DOMAIN_KEY=test \
.claude/hooks/scripts/chatkit-env-validator.sh

# Should exit with 0

# Test missing variable
unset OPENAI_API_KEY && \
VITE_CHATKIT_API_DOMAIN_KEY=test \
.claude/hooks/scripts/chatkit-env-validator.sh

# Should exit with 1
```

### Integration Testing

1. Create test environment with various configurations
2. Run each script with different states
3. Verify correct exit codes
4. Check output format matches specification
5. Test on both macOS and Linux
6. Test with and without optional tools (lsof, netstat)

---

## Script Organization

All scripts should be:

1. **Executable**: `chmod +x script.sh`
2. **Documented**: Include brief comment explaining purpose
3. **Self-contained**: No external imports beyond standard utilities
4. **Consistent**: Follow naming and output conventions
5. **Maintainable**: Clear variable names and logic flow
6. **Robust**: Handle edge cases and errors gracefully

Example structure:

```bash
#!/bin/bash
# Purpose: Validate ChatKit environment configuration
# Usage: ./chatkit-env-validator.sh
# Exit codes: 0 = success, 1 = validation failed, 2 = script error

set -e  # Exit on error (optional, for strict mode)

# Configuration
REQUIRED_VARS=("OPENAI_API_KEY" "VITE_CHATKIT_API_DOMAIN_KEY")

# Main logic
# ... validation code ...

# Exit with appropriate code
```

---

## Future Enhancements

Potential additions (not yet implemented):

1. **Backend Health Check**: Verify FastAPI server is responding
2. **Frontend Build Check**: Verify frontend builds successfully
3. **API Compatibility**: Check OpenAI API version compatibility
4. **File Synchronization**: Warn if files are out of sync across examples
5. **Security Scan**: Basic security checks on configuration files
6. **Performance Metrics**: Monitor hook execution times

---

## Related Documentation

- Main validation docs: [chatkit-validation.md](./chatkit-validation.md)
- Implementation guide: [CHATKIT_SETUP.md](./CHATKIT_SETUP.md)
- Quick reference: [chatkit-quick-reference.md](./chatkit-quick-reference.md)
