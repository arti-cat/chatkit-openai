# Template Setup Guide

**Quick-start guide for setting up new client projects from this ChatKit-OpenAI template.**

**Estimated time**: 10 minutes

---

## Prerequisites

Before starting, ensure you have:

- ✅ **Git** installed
- ✅ **Python 3.11+** installed
- ✅ **Node.js 20+** installed
- ✅ **uv** installed (Python package manager): `pip install uv`
- ✅ **OpenAI API key** (from [platform.openai.com](https://platform.openai.com))
- ✅ **ChatKit domain key** (for production, from [OpenAI domain allowlist](https://platform.openai.com/settings/organization/security/domain-allowlist))

---

## 10-Minute Setup

### Step 1: Clone Template (1 minute)

```bash
# Clone the template repository
git clone <template-repo-url> my-client-project
cd my-client-project

# Remove template git history (optional)
rm -rf .git
git init
git add .
git commit -m "Initial commit from ChatKit-OpenAI template"
```

---

### Step 2: Customize Project Name (2 minutes)

**Option A: Manual Search/Replace**

Search and replace `chatkit-openai` with your client project name in:
- `package.json`
- `backend/pyproject.toml`
- `README.md`
- `CLAUDE.md`

**Option B: Automated (Linux/Mac)**

```bash
# Replace with your client project name
CLIENT_NAME="my-client-project"

# Find and replace in all files
find . -type f \( -name "*.json" -o -name "*.toml" -o -name "*.md" \) \
  -not -path "./.git/*" \
  -exec sed -i "s/chatkit-openai/$CLIENT_NAME/g" {} +

echo "✅ Project name updated to: $CLIENT_NAME"
```

**Option C: Automated (Windows)**

```powershell
$ClientName = "my-client-project"
Get-ChildItem -Recurse -Include *.json,*.toml,*.md |
  ForEach-Object {
    (Get-Content $_.FullName) -replace 'chatkit-openai', $ClientName |
    Set-Content $_.FullName
  }
```

---

### Step 3: Configure Environment Variables (3 minutes)

**Backend Environment**:

```bash
# Create backend/.env file
cat > backend/.env << 'EOF'
OPENAI_API_KEY=sk-proj-YOUR_OPENAI_API_KEY_HERE
EOF

echo "✅ Backend environment configured"
```

**Frontend Environment (Local Development)**:

```bash
# Create frontend/.env.local file
cat > frontend/.env.local << 'EOF'
VITE_CHATKIT_API_DOMAIN_KEY=local-development
EOF

echo "✅ Frontend environment configured for local dev"
```

**Frontend Environment (Production)**:

For production deployments, replace `local-development` with actual domain key:

1. Register your domain at [OpenAI domain allowlist](https://platform.openai.com/settings/organization/security/domain-allowlist)
2. Copy the generated domain key
3. Update `frontend/.env.local` (or production environment variables):

```bash
VITE_CHATKIT_API_DOMAIN_KEY=your-actual-domain-key-here
```

---

### Step 4: Install Dependencies (3 minutes)

**Install all dependencies**:

```bash
# From project root
npm install

# This installs:
# - Root package dependencies (npm scripts)
# - Backend dependencies (via uv)
# - Frontend dependencies (via npm)
```

**Verify installation**:

```bash
# Check backend
cd backend && uv pip list | head -5
cd ..

# Check frontend
cd frontend && npm list --depth=0 | head -10
cd ..

echo "✅ Dependencies installed"
```

---

### Step 5: Start Development Servers (1 minute)

**Start both backend and frontend**:

```bash
npm start
```

**Expected output**:
```
Starting backend on http://127.0.0.1:8000...
Starting frontend on http://127.0.0.1:5170...

✓ Backend ready
✓ Frontend ready
```

**Verify servers are running**:
- Backend: [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) (FastAPI docs)
- Frontend: [http://127.0.0.1:5170](http://127.0.0.1:5170) (React app)

---

### Step 6: Validate Configuration (Optional, recommended)

**Using Claude Code** (if available):

```bash
/chatkit:validate-config
```

**Manual validation**:

```bash
# Test backend health
curl http://127.0.0.1:8000/health

# Test frontend
curl http://127.0.0.1:5170

echo "✅ Configuration validated"
```

---

## Post-Setup Checklist

After completing setup, verify:

- [ ] Backend runs without errors
- [ ] Frontend displays ChatKit interface
- [ ] Can send messages in chat
- [ ] No console errors in browser
- [ ] Environment variables loaded correctly

---

## Troubleshooting

### Issue: `uv` not found

**Solution**:
```bash
pip install uv
# or
brew install uv  # macOS
```

### Issue: Backend fails to start

**Error**: `ModuleNotFoundError: No module named 'agents'`

**Solution**:
```bash
cd backend
uv sync
uv run uvicorn app.main:app --reload
```

### Issue: Frontend proxy not working

**Error**: `GET http://127.0.0.1:5170/chatkit 502`

**Solution**:
1. Ensure backend is running on port 8000
2. Check `frontend/vite.config.ts` proxy configuration
3. Restart frontend: `npm run frontend`

### Issue: Missing OPENAI_API_KEY

**Error**: `ValueError: OPENAI_API_KEY environment variable not set`

**Solution**:
```bash
# Verify .env file exists
ls backend/.env

# Check content
cat backend/.env

# If missing, create it
echo "OPENAI_API_KEY=sk-proj-YOUR_KEY" > backend/.env
```

### Issue: Port already in use

**Error**: `OSError: [Errno 48] Address already in use`

**Solution**:

```bash
# Find process using port 8000
lsof -i :8000
kill -9 <PID>

# Or change port
cd backend
uv run uvicorn app.main:app --reload --port 8001

# Update frontend proxy in vite.config.ts
```

---

## Next Steps

After successful setup:

1. **Customize agent personality**: Edit `backend/app/constants.py`
2. **Add your first tool**: Run `/chatkit:add-tool` in Claude Code
3. **Study examples**: Explore `examples/` directory for patterns
4. **Review architecture**: Read [CLAUDE.md](CLAUDE.md) for detailed docs
5. **Customize UI**: Modify `frontend/src/styles/` for branding

---

## Alternative Setup Methods

### Without `uv` (Using pip/venv)

```bash
cd backend
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install -e .
export OPENAI_API_KEY=sk-proj-...
uvicorn app.main:app --reload
```

### Running Examples

Each example has the same setup process:

```bash
cd examples/customer-support
npm install
# Set environment variables in .env files
npm start  # Backend on 8001, frontend on 5171
```

---

## Production Deployment Setup

For production deployments, additional steps required:

### 1. Domain Allowlisting

```bash
# Register domain at OpenAI
# https://platform.openai.com/settings/organization/security/domain-allowlist

# Update frontend/vite.config.ts
server: {
  allowedHosts: [
    'localhost',
    '127.0.0.1',
    'your-production-domain.com',  # Add your domain
  ],
}
```

### 2. Environment Variables

**Backend (Production)**:
```bash
export OPENAI_API_KEY=sk-proj-production-key
export BACKEND_PORT=8000
```

**Frontend (Production)**:
```bash
export VITE_CHATKIT_API_DOMAIN_KEY=actual-domain-key-from-openai
export VITE_BACKEND_URL=https://api.your-domain.com
```

### 3. Build for Production

```bash
# Frontend build
cd frontend
npm run build
npm run preview  # Test production build locally

# Backend (no build needed, but verify)
cd backend
uv run mypy app/  # Type checking
uv run ruff check .  # Linting
```

---

## Template Testing

Before starting development, test the template:

```bash
# 1. Validate configuration
/chatkit:validate-config  # In Claude Code

# 2. Test base chat
npm start
# Open http://127.0.0.1:5170
# Send message: "What's the weather in San Francisco?"

# 3. Test tool addition
/chatkit:add-tool  # In Claude Code
# Follow prompts to add a simple tool

# 4. Test examples
cd examples/customer-support && npm start
# Interact with airline support agent
```

---

## Quick Reference

| Task | Command |
|------|---------|
| Start dev servers | `npm start` |
| Backend only | `npm run backend` |
| Frontend only | `npm run frontend` |
| Validate config | `/chatkit:validate-config` |
| Add tool | `/chatkit:add-tool` |
| Backend tests | `cd backend && uv run pytest` |
| Frontend tests | `cd frontend && npm test` |
| Lint backend | `cd backend && uv run ruff check .` |
| Lint frontend | `cd frontend && npm run lint` |

---

## Support

- **Setup issues**: Check [Troubleshooting](#troubleshooting) section
- **Architecture questions**: See [CLAUDE.md](CLAUDE.md)
- **Customization**: See [TEMPLATE_CUSTOMIZATION.md](TEMPLATE_CUSTOMIZATION.md)
- **Claude Code help**: Use `/chatkit:validate-config` command

---

**Setup complete!** 🎉

You now have a working ChatKit application. Start building with:
- `/chatkit:add-tool` - Add your first custom tool
- `/chatkit:full-stack-feature` - Build complete features
- Study `examples/` for patterns and best practices
