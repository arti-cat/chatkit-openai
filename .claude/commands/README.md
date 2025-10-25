# Claude Command Suite - Namespace Organization

Welcome to the Claude Command Suite! This collection of commands is organized into logical namespaces to help you quickly find the right tool for your task.

## Command Namespaces

### 🚀 `/project:*` - Project Management
Initialize, configure, and manage projects. Includes commands for creating new projects, adding packages, tracking milestones, and monitoring project health.

### 💻 `/dev:*` - Development Tools
Essential development utilities including code review, debugging, refactoring, and specialized AI modes (prime, sentient, ultra-think) for enhanced assistance.

### 🧪 `/test:*` - Testing Suite
Comprehensive testing tools covering unit tests, integration tests, E2E tests, coverage analysis, mutation testing, and visual regression testing.

### 🔒 `/security:*` - Security & Compliance
Security auditing, dependency scanning, authentication implementation, and security hardening tools to keep your codebase secure.

### ⚡ `/performance:*` - Performance Optimization
Tools for optimizing build times, bundle sizes, database queries, caching strategies, and overall application performance.

### 🔄 `/sync:*` - Integration & Synchronization
Bidirectional sync between GitHub Issues and Linear, PR tracking, conflict resolution, and cross-platform task management.

### 📦 `/deploy:*` - Deployment & Release
Release preparation, automated deployments, rollback capabilities, containerization, and Kubernetes deployment management.

### 📚 `/docs:*` - Documentation Generation
Automated documentation tools for APIs, architecture diagrams, onboarding guides, and troubleshooting documentation.

### 🔧 `/setup:*` - Configuration & Setup
Initial setup commands for development environments, linting, formatting, monitoring, database schemas, and API design.

### 👥 `/team:*` - Team Collaboration
Team workflow tools including standup reports, sprint planning, retrospectives, workload balancing, and knowledge capture.

### 🎯 `/simulation:*` - AI Reality Simulators
Advanced simulation and modeling tools for exponential decision value. Transform from linear execution gains to exponential strategic advantage through systematic scenario exploration, digital twins, and timeline compression.

### 🤝 `/multi-role` - Multi-Perspective Analysis
Combine insights from multiple specialized agents for comprehensive, balanced assessments. Enables parallel or sequential execution of 2-4 agents with synthesized recommendations. Ideal for complex technical decisions requiring multiple viewpoints.

**Example:** `/multi-role security-auditor,performance-engineer --agent`
- 50-65% time reduction for multi-perspective analysis
- Balances trade-offs explicitly
- Unified recommendations from diverse expertise

### 💬 `/chatkit:*` - ChatKit Development Tools
Specialized commands for ChatKit-OpenAI project development. Accelerates feature implementation, configuration validation, and full-stack workflows specific to ChatKit + FastAPI + React architecture.

**Key Commands:**
- `/chatkit:add-tool` - Guided tool scaffolding (saves ~30-40 min)
- `/chatkit:validate-config` - Configuration validation (saves ~15-20 min)
- `/chatkit:full-stack-feature` - Orchestrated implementation (40-60% faster)

## Instructions

This is a README file providing an overview of the Claude Command Suite namespace organization. To use any specific command, navigate to the appropriate namespace directory and select the command you need. Each command file contains detailed instructions for its use.

## Usage

Commands follow the namespace pattern: `/<namespace>:<command>`

For example:
- `/project:init-project` - Initialize a new project
- `/dev:code-review` - Perform a code review
- `/test:generate-test-cases` - Generate test cases
- `/security:security-audit` - Run a security audit

## Quick Start

1. **Starting a new project?** Begin with `/project:init-project`
2. **Need to debug?** Try `/dev:debug-error`
3. **Writing tests?** Use `/test:generate-test-cases`
4. **Security concerns?** Run `/security:security-audit`
5. **Performance issues?** Check `/performance:performance-audit`
6. **Major decisions?** Explore `/simulation:business-scenario-explorer`
7. **ChatKit development?** Start with `/chatkit:validate-config`
8. **Need multiple perspectives?** Use `/multi-role agent1,agent2 --agent`

## Finding Commands

Each namespace directory contains a README.md with detailed descriptions of available commands. Navigate to any namespace folder to see its specific commands and their purposes.

## Command Availability

Commands are loaded from:
- **Project-level**: `.claude/commands/` (this directory)
- **User-level**: `~/.claude/commands/` (your personal commands)

Project-level commands take precedence when there are naming conflicts.