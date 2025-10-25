# Multi-Role Command

## Description

Enable multi-perspective analysis through parallel or sequential agent execution. Combines insights from multiple specialized agents to provide comprehensive, balanced assessments of complex technical decisions.

## Syntax

```bash
/multi-role role1,role2[,role3[,role4]] [--agent]
```

**Parameters:**
- `role1,role2,...` - Comma-separated list of 2-4 agent names (no spaces)
- `--agent` (optional) - Enable parallel subagent execution for faster results

**Without `--agent`**: Sequential prompting from each role's perspective
**With `--agent`**: Parallel Task tool invocation with synthesized results

## Usage Examples

### Basic Multi-Role Analysis

```bash
/multi-role python-pro,typescript-pro
```

**Prompt after command:**
```
Analyze this API endpoint for type safety and best practices
```

**Claude behavior:**
- Analyzes from Python backend perspective
- Analyzes from TypeScript frontend perspective
- Synthesizes combined recommendations

### Parallel Execution (Faster)

```bash
/multi-role security-auditor,performance-engineer --agent
```

**Prompt after command:**
```
Review this authentication implementation
```

**Claude behavior:**
- Launches security-auditor subagent (security analysis)
- Launches performance-engineer subagent (performance analysis)
- Waits for both to complete
- Synthesizes findings into unified report

### Three-Way Analysis

```bash
/multi-role backend-architect,frontend-developer,test-engineer --agent
```

**Prompt after command:**
```
Design a user profile feature with caching
```

**Claude behavior:**
- backend-architect: API design, data model, caching strategy
- frontend-developer: UI components, state management, API integration
- test-engineer: Test strategy, coverage requirements, integration tests
- Synthesized output with coordinated plan

### Four-Way Comprehensive Review

```bash
/multi-role architect,security-auditor,performance-engineer,test-engineer --agent
```

**Prompt after command:**
```
Evaluate this microservices architecture
```

**Claude behavior:**
- Parallel execution of 4 specialized reviews
- Comprehensive assessment from all perspectives
- Unified recommendations with trade-off analysis

## Effective Role Combinations for ChatKit-OpenAI

### Backend Development

| Combination | Use Case | Time Savings |
|-------------|----------|--------------|
| `python-pro,backend-architect` | API design review | ~50% |
| `python-pro,security-auditor` | Secure backend development | ~60% |
| `backend-architect,performance-engineer` | Scalable API design | ~55% |
| `python-pro,test-engineer` | Testable backend code | ~45% |

### Frontend Development

| Combination | Use Case | Time Savings |
|-------------|----------|--------------|
| `react-pro,typescript-pro` | Type-safe React development | ~50% |
| `frontend-developer,performance-auditor` | Optimized UI implementation | ~55% |
| `react-pro,security-auditor` | Secure frontend patterns | ~60% |
| `typescript-pro,test-engineer` | Well-tested TypeScript code | ~45% |

### Full-Stack Features

| Combination | Use Case | Time Savings |
|-------------|----------|--------------|
| `backend-architect,frontend-developer` | End-to-end architecture | ~55% |
| `python-pro,typescript-pro` | Full-stack type safety | ~50% |
| `full-stack-developer,security-auditor` | Secure full-stack implementation | ~60% |
| `chatkit-server-expert,chatkit-frontend-expert` | ChatKit feature implementation | ~65% |

### Production Readiness

| Combination | Use Case | Time Savings |
|-------------|----------|--------------|
| `security-auditor,performance-engineer` | Production validation | ~60% |
| `deployment-engineer,security-auditor` | Secure deployment pipeline | ~55% |
| `code-auditor,performance-auditor` | Quality + performance review | ~50% |
| `security-auditor,performance-engineer,test-engineer` | Comprehensive pre-launch review | ~65% |

### Testing & Quality

| Combination | Use Case | Time Savings |
|-------------|----------|--------------|
| `test-engineer,code-auditor` | Test coverage + code quality | ~50% |
| `test-engineer,security-auditor` | Security test strategy | ~55% |
| `architecture-auditor,test-engineer` | Design validation with tests | ~60% |

### ChatKit-Specific

| Combination | Use Case | Time Savings |
|-------------|----------|--------------|
| `chatkit-server-expert,chatkit-frontend-expert` | Full-stack ChatKit feature | ~65% |
| `chatkit-server-expert,test-engineer` | Backend tool with tests | ~50% |
| `chatkit-frontend-expert,performance-auditor` | Optimized ChatKit UI | ~55% |
| `chatkit-server-expert,security-auditor` | Secure agent tools | ~60% |

## When to Use Multi-Role vs Single Agent

### Use Multi-Role When:
- ✅ Decision involves trade-offs between multiple concerns
- ✅ Need comprehensive analysis from different perspectives
- ✅ Feature spans multiple domains (backend + frontend)
- ✅ Security + performance + architecture must all be considered
- ✅ Want to identify blind spots or unconsidered issues

### Use Single Agent When:
- ✅ Task is clearly within one domain
- ✅ Speed is more important than comprehensiveness
- ✅ Straightforward implementation with no trade-offs
- ✅ Already know which perspective is needed

## Output Format

### Sequential Mode (without --agent)

```markdown
# Multi-Role Analysis: [role1, role2, ...]

## [Role 1 Name] Perspective

[Analysis from first role's perspective]
- Key considerations
- Recommendations
- Potential issues

## [Role 2 Name] Perspective

[Analysis from second role's perspective]
- Key considerations
- Recommendations
- Potential issues

## Synthesis

[Combined recommendations considering all perspectives]
- Unified approach
- Trade-off analysis
- Prioritized action items
```

### Parallel Mode (with --agent)

```markdown
# Multi-Role Analysis: [role1, role2, ...] (Parallel Execution)

## Executive Summary

[High-level synthesis of all agent findings]

## Detailed Findings

### [Role 1 Name]
[Comprehensive analysis from subagent 1]

### [Role 2 Name]
[Comprehensive analysis from subagent 2]

## Recommendations

### High Priority
- [Critical items from any agent]

### Medium Priority
- [Important but not urgent items]

### Low Priority
- [Nice-to-have improvements]

## Trade-Off Analysis

[Discussion of conflicts between different perspectives]
- How to balance competing concerns
- Recommended approach with rationale
```

## Best Practices

### 1. Choose Complementary Roles

**Good Combinations:**
- `security-auditor,performance-engineer` - Often have trade-offs
- `backend-architect,frontend-developer` - Full-stack coordination
- `python-pro,test-engineer` - Implementation + testing

**Less Effective:**
- `python-pro,golang-pro` - Different languages, little overlap
- `react-pro,nextjs-pro` - Too similar, redundant perspectives

### 2. Use --agent Flag for Complex Analysis

**Use `--agent` when:**
- Analysis will take >5 minutes
- Need deep dive from each perspective
- Roles are independent (parallel execution beneficial)

**Skip `--agent` when:**
- Quick opinion needed
- Roles build on each other sequentially
- Simple, straightforward analysis

### 3. Limit to 2-4 Roles

**Optimal**: 2-3 roles
- Clear, focused perspectives
- Manageable synthesis
- Faster completion

**Maximum**: 4 roles
- Comprehensive but complex
- Longer synthesis time
- Use only when truly necessary

### 4. Ask Specific Questions

**Good Prompts:**
- "Evaluate this caching strategy"
- "Review this authentication implementation"
- "Design a real-time notification system"

**Less Effective:**
- "What do you think?" (too vague)
- "Is this good?" (not actionable)
- "Review everything" (too broad)

### 5. Act on Synthesis

The synthesis is the most valuable output:
- Read conflicting recommendations carefully
- Understand trade-offs explicitly
- Make informed decisions based on project priorities

## Limitations

1. **Not all agents are compatible** - Some combinations provide redundant insights
2. **Synthesis may oversimplify** - Complex trade-offs might need follow-up discussion
3. **Parallel mode has overhead** - For very quick tasks, single agent is faster
4. **Maximum 4 roles** - More than 4 creates cognitive overload in synthesis

## Examples

### Example 1: API Security Review

```bash
/multi-role security-auditor,backend-architect --agent
```

**Prompt:**
```
Review this user authentication endpoint:

POST /api/auth/login
- Email/password validation
- JWT token generation
- Rate limiting: 5 requests/minute
- Password hashing: bcrypt with 12 rounds
```

**Output:**
```markdown
# Multi-Role Analysis: security-auditor, backend-architect

## Executive Summary
The authentication endpoint has solid security fundamentals but needs
improvements in token management and error handling.

## Detailed Findings

### security-auditor
✅ Good: bcrypt with 12 rounds is appropriate
✅ Good: Rate limiting helps prevent brute force
⚠️  Issue: No mention of token expiration strategy
⚠️  Issue: Error messages may leak information about valid emails
🔴 Critical: No mention of HTTPS enforcement
🔴 Critical: Missing CSRF protection for web clients

Recommendations:
1. Implement short-lived access tokens (15min) + refresh tokens
2. Generic error messages for failed login attempts
3. Enforce HTTPS at infrastructure level
4. Add CSRF tokens for cookie-based auth

### backend-architect
✅ Good: POST verb appropriate for authentication
✅ Good: Clear endpoint naming
⚠️  Issue: No discussion of horizontal scaling for rate limiting
⚠️  Issue: Session management strategy unclear
💡 Suggestion: Consider distributed rate limiting (Redis)
💡 Suggestion: Document token refresh flow

Recommendations:
1. Use Redis for distributed rate limiting
2. Implement refresh token rotation
3. Add monitoring for failed login attempts
4. Document complete auth flow in API docs

## Recommendations

### High Priority
1. Add token expiration + refresh strategy (security + architecture)
2. Enforce HTTPS (security)
3. Implement CSRF protection (security)
4. Use distributed rate limiting with Redis (architecture)

### Medium Priority
5. Generic error messages (security)
6. Add authentication monitoring (architecture)
7. Document complete auth flow (architecture)

### Low Priority
8. Consider OAuth2/OpenID Connect for future (architecture)

## Trade-Off Analysis

**Security vs Performance:**
- bcrypt rounds (12) balances security and response time
- Rate limiting protects security but may impact UX during peak times
- Recommend: Keep current settings, monitor for abuse patterns

**Token Expiration vs UX:**
- Short access tokens (15min) improve security
- Refresh tokens reduce re-authentication friction
- Recommend: 15min access + 7-day refresh with rotation
```

### Example 2: React Component Optimization

```bash
/multi-role react-pro,performance-auditor
```

**Prompt:**
```
Optimize this user list component:
- Renders 1000+ users
- Each user has avatar, name, email, status
- Updates in real-time
- Currently experiencing lag
```

**Output:**
```markdown
# Multi-Role Analysis: react-pro, performance-auditor

## react-pro Perspective

**Current Issues:**
- Likely rendering all 1000 items on mount
- No memoization of user items
- Real-time updates may trigger full list re-render

**Recommendations:**
1. Implement virtualized list (react-window or react-virtual)
2. Memoize UserItem component with React.memo()
3. Use useCallback for event handlers
4. Consider pagination or infinite scroll as alternative
5. Optimize real-time updates with targeted re-renders

**Implementation:**
```tsx
import { FixedSizeList } from 'react-window';

const UserItem = React.memo(({ user }) => (
  <div>
    <Avatar src={user.avatar} />
    <span>{user.name}</span>
    <span>{user.email}</span>
    <Badge status={user.status} />
  </div>
));

const UserList = ({ users }) => (
  <FixedSizeList
    height={600}
    itemCount={users.length}
    itemSize={72}
  >
    {({ index, style }) => (
      <UserItem user={users[index]} style={style} />
    )}
  </FixedSizeList>
);
```

## performance-auditor Perspective

**Performance Analysis:**
- Rendering 1000 DOM nodes causes 500-1000ms initial render
- Real-time updates likely causing full reconciliation
- Avatar loading may block rendering

**Recommendations:**
1. Virtualization reduces render to ~20 items (98% reduction)
2. Lazy load avatars with loading placeholder
3. Debounce real-time updates (max 1 update/100ms)
4. Use production build for 2-3x performance gain
5. Profile with React DevTools to identify bottlenecks

**Performance Targets:**
- Initial render: <100ms (from ~800ms)
- Update latency: <50ms
- Memory usage: <50MB (from ~200MB with all DOM nodes)

## Synthesis

**Unified Approach:**
1. **Virtualization (Critical)**: Implement react-window
   - Reduces initial render by 98%
   - Solves lag immediately

2. **Memoization (High Priority)**: React.memo() on UserItem
   - Prevents unnecessary re-renders
   - Complements virtualization

3. **Lazy Loading (Medium Priority)**: Defer avatar loading
   - Improves perceived performance
   - Reduces network pressure

4. **Update Optimization (Medium Priority)**: Debounce real-time updates
   - Reduces render frequency
   - Maintains responsiveness

**Expected Results:**
- Initial render: 800ms → 80ms (90% improvement)
- Smooth scrolling at 60fps
- Memory usage: 200MB → 40MB (80% reduction)
```

### Example 3: ChatKit Feature Planning

```bash
/multi-role chatkit-server-expert,chatkit-frontend-expert,test-engineer --agent
```

**Prompt:**
```
Design a user search feature for ChatKit:
- Search users by name or email
- Display results in a widget
- Allow clicking to view full profile
- Should work with 10,000+ users
```

**Output:** _(See full example in command documentation)_

## Related Commands

- `/chatkit:full-stack-feature` - Orchestrated ChatKit feature implementation
- `/chatkit:add-tool` - Quick tool addition
- `/role-debate` - Debate opposing perspectives (if implemented)
- `/role-help` - Get role selection suggestions (if implemented)

## Related Agents

All 88+ agents can be used in multi-role combinations. Most effective combinations listed above.

**ChatKit-Specific:**
- chatkit-server-expert
- chatkit-frontend-expert

**Core Development:**
- python-pro, typescript-pro, react-pro
- backend-architect, frontend-developer
- full-stack-developer

**Quality & Security:**
- test-engineer, code-auditor, security-auditor
- performance-auditor, performance-engineer
- architecture-auditor

**Infrastructure:**
- deployment-engineer, cloud-architect
- incident-responder

## Troubleshooting

### Issue: Too Many Conflicting Recommendations

**Solution:**
- Reduce to 2 roles for clearer guidance
- Focus on specific aspect (security OR performance)
- Ask follow-up question to resolve conflict

### Issue: Analysis Takes Too Long

**Solution:**
- Remove `--agent` flag for faster sequential mode
- Reduce number of roles (2 instead of 3-4)
- Ask more specific question to scope analysis

### Issue: Redundant Perspectives

**Solution:**
- Choose more complementary roles
- Avoid roles from same domain (e.g., react-pro + nextjs-pro)
- Consult role combination table above

### Issue: Synthesis Misses Key Point

**Solution:**
- Ask follow-up question highlighting the concern
- Review individual role outputs for details
- Consider if additional role needed

## See Also

- [Agent Coordination Patterns](.claude/agents/WORKFLOW_EXAMPLES.md)
- [Best Practices Analysis](docs/command-suite/BEST_PRACTICES_ANALYSIS.md)
- [Agent README](.claude/agents/README.md)

---

**Performance Impact**: 50-65% time reduction for multi-perspective analysis (based on Claude Code Cookbook best practices)

**When in doubt**: Start with 2 complementary roles, add more only if needed.
