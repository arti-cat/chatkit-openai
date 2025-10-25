# Validate ChatKit Configuration

## Description

Comprehensive validation of ChatKit application configuration including environment variables, domain allowlisting, port configuration, and endpoint health.

## Usage

```bash
/chatkit:validate-config
```

Claude will check:
1. Environment variables (OPENAI_API_KEY, VITE_CHATKIT_API_DOMAIN_KEY)
2. Domain allowlisting configuration
3. Backend/frontend port conflicts
4. Proxy configuration
5. Endpoint health (if servers running)

## What Gets Validated

### 1. Environment Variables

**Backend:**
- `OPENAI_API_KEY` - Required for OpenAI Agents SDK
  - ✅ Set and non-empty
  - ✅ Starts with `sk-` or `sk-proj-`
  - ⚠️  Format appears valid

**Frontend:**
- `VITE_CHATKIT_API_DOMAIN_KEY` - Required for ChatKit React SDK
  - ✅ Set and non-empty (required for production)
  - ℹ️  Can be any non-empty string for local development
  - ⚠️  Should be actual domain key from OpenAI allowlist for production

**Optional:**
- `BACKEND_URL` - Override backend target (default: http://127.0.0.1:8000)

### 2. Domain Allowlisting

**File:** `frontend/vite.config.ts`

Checks:
- `server.allowedHosts` includes expected domains
- Localhost and 127.0.0.1 included for development
- Production domains registered (if deploying)

**Production Requirements:**
1. Register domain at [OpenAI Domain Allowlist](https://platform.openai.com/settings/organization/security/domain-allowlist)
2. Add domain to `allowedHosts` in vite.config.ts
3. Set `VITE_CHATKIT_API_DOMAIN_KEY` to generated key

### 3. Port Configuration

**Base Template:**
- Backend: 8000
- Frontend: 5170

**Examples:**
- customer-support: Backend 8001, Frontend 5171
- knowledge-assistant: Backend 8002, Frontend 5172
- marketing-assets: Backend 8003, Frontend 5173

**Validation:**
- ✅ No port conflicts between apps
- ✅ Ports specified in package.json match vite.config.ts
- ✅ Backend URL in frontend config points to correct port
- ⚠️  Ports not already in use by other processes

### 4. Proxy Configuration

**File:** `frontend/vite.config.ts`

**Checks:**
- `/chatkit` endpoint proxies to backend
- `changeOrigin: true` set for proxy
- Target URL matches backend server

**Example Valid Config:**
```typescript
proxy: {
  '/chatkit': {
    target: 'http://127.0.0.1:8000',
    changeOrigin: true,
  },
}
```

### 5. Endpoint Health (Optional)

If servers are running, validates:
- Backend `/chatkit` endpoint responds
- Frontend dev server is accessible
- Proxy forwards requests correctly

## Validation Checklist

Claude performs these checks:

```markdown
### Environment Variables
- [ ] OPENAI_API_KEY set in backend environment
- [ ] OPENAI_API_KEY format valid (starts with sk-)
- [ ] VITE_CHATKIT_API_DOMAIN_KEY set (production)
- [ ] BACKEND_URL matches actual backend (if set)

### Domain Configuration
- [ ] allowedHosts includes localhost, 127.0.0.1
- [ ] Production domains added to allowedHosts
- [ ] Domain keys match OpenAI allowlist

### Port Configuration
- [ ] Backend port doesn't conflict
- [ ] Frontend port doesn't conflict
- [ ] package.json scripts use correct ports
- [ ] vite.config.ts server.port matches

### Proxy Configuration
- [ ] /chatkit proxy configured
- [ ] Proxy target matches backend URL
- [ ] changeOrigin set to true

### Endpoint Health (if running)
- [ ] Backend server responds on configured port
- [ ] Frontend server accessible
- [ ] /chatkit endpoint reachable via proxy
```

## Example Output

```
✅ ChatKit Configuration Validation

### Environment Variables
✅ OPENAI_API_KEY set and valid format
✅ VITE_CHATKIT_API_DOMAIN_KEY set (value: local-development)
ℹ️  Using local development key (not production)

### Domain Allowlisting
✅ localhost and 127.0.0.1 in allowedHosts
⚠️  No production domains configured
ℹ️  Add production domains before deploying

### Port Configuration
✅ Backend: 8000 (no conflicts)
✅ Frontend: 5170 (no conflicts)
✅ Example apps: 8001-8003, 5171-5173 (no conflicts)

### Proxy Configuration
✅ /chatkit proxy configured correctly
✅ Target: http://127.0.0.1:8000
✅ changeOrigin: true

### Endpoint Health
⚠️  Servers not running (cannot test endpoints)
ℹ️  Run `npm start` to start both servers

### Overall Status: ✅ Ready for development
```

## Common Issues & Fixes

### Issue: OPENAI_API_KEY not set

**Error:**
```
❌ OPENAI_API_KEY not found in environment
```

**Fix:**
```bash
# Add to backend environment
export OPENAI_API_KEY=sk-proj-your-key-here

# Or create .env file in backend/
echo "OPENAI_API_KEY=sk-proj-your-key-here" > backend/.env
```

### Issue: Invalid domain key format

**Error:**
```
❌ VITE_CHATKIT_API_DOMAIN_KEY format appears invalid
```

**Fix:**
- For local development: Any non-empty string works
- For production: Use key from [OpenAI Domain Allowlist](https://platform.openai.com/settings/organization/security/domain-allowlist)

```bash
# Development
export VITE_CHATKIT_API_DOMAIN_KEY=local-dev

# Production
export VITE_CHATKIT_API_DOMAIN_KEY=actual-key-from-openai
```

### Issue: Port conflict

**Error:**
```
❌ Port 8000 already in use
```

**Fix:**
```bash
# Find process using port
lsof -i :8000

# Kill process or use different port
kill -9 <PID>

# Or modify port in backend/app/main.py
uvicorn app.main:app --port 8001
```

### Issue: Proxy not forwarding requests

**Error:**
```
❌ /chatkit requests not reaching backend
```

**Fix:**
1. Check `BACKEND_URL` in vite.config.ts matches backend server
2. Restart Vite dev server after config changes
3. Verify backend is running on expected port
4. Check browser network tab for proxy errors

### Issue: Production domain not allowlisted

**Error:**
```
❌ Domain not in OpenAI allowlist
```

**Fix:**
1. Go to [OpenAI Domain Allowlist](https://platform.openai.com/settings/organization/security/domain-allowlist)
2. Click "Add Domain"
3. Enter your production domain (e.g., `app.yourcompany.com`)
4. Copy generated domain key
5. Set `VITE_CHATKIT_API_DOMAIN_KEY` to the key
6. Add domain to `allowedHosts` in vite.config.ts

## Files Checked

- `backend/.env` (if exists)
- `frontend/.env.local` (if exists)
- `frontend/vite.config.ts`
- `frontend/package.json`
- `backend/app/main.py` (port configuration)

## Usage Scenarios

### Scenario 1: Initial Setup Validation

```
User: Just cloned the repo, want to make sure everything is configured

/chatkit:validate-config

→ Identifies missing environment variables
→ Provides setup instructions
```

### Scenario 2: Pre-Deployment Check

```
User: About to deploy to production

/chatkit:validate-config

→ Checks domain allowlisting
→ Validates production environment variables
→ Confirms configuration is deployment-ready
```

### Scenario 3: Troubleshooting Connection Issues

```
User: Chat isn't connecting to backend

/chatkit:validate-config

→ Identifies proxy misconfiguration
→ Detects port conflicts
→ Suggests fixes
```

## Best Practices

1. **Run Before First Start**: Validate config before running `npm start`
2. **Check Before Deploying**: Always validate production config
3. **After Config Changes**: Re-validate after modifying vite.config.ts or .env files
4. **Troubleshooting First Step**: Run validation when encountering connection issues

## Related Commands

- `/chatkit:add-tool` - Add new ChatKit tool
- `/chatkit:full-stack-feature` - Add full-stack feature
- `/setup:logging-setup` - Configure logging
- `/deploy:prepare-release` - Prepare for deployment

## Related Documentation

- [CLAUDE.md](CLAUDE.md) - Project setup instructions
- [OpenAI Domain Allowlist](https://platform.openai.com/settings/organization/security/domain-allowlist)
- [Vite Proxy Documentation](https://vitejs.dev/config/server-options.html#server-proxy)

## See Also

- [chatkit-server-expert agent](.claude/agents/chatkit/chatkit-server-expert.md)
- [chatkit-frontend-expert agent](.claude/agents/chatkit/chatkit-frontend-expert.md)
