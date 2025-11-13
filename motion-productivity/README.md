# Motion Alternative: Productivity Skills

> **Self-hosted, AI-powered productivity assistant using Claude Code Skills and FOSS tools**

Collection of Skills that replicate [Motion's](https://usemotion.com) AI-powered productivity features.

## 🎯 Included Skills

**Install all at once:**
```bash
/plugin install motion-productivity@drews-skills
```

**Or install individually:**
- `priority-assistant` - Intelligent task prioritization
- `task-scheduler` - Automatic calendar scheduling
- `deadline-guardian` - Proactive deadline warnings
- `project-planner` - AI project decomposition
- `meeting-optimizer` - Optimal meeting times
- `context-assistant` - Context-aware task filtering

## 📖 Full Documentation

See `/docs` directory for complete specs:
- [Motion Feature Spec](../docs/motion-ai-feature-spec.md)
- [Architecture](../docs/architecture.md)
- [MCP Integration Guide](../docs/mcp-integration-guide.md)
- [Parity Checklist](../docs/parity-checklist.md)

## Prerequisites

Requires MCP servers:
- Calendar MCP (CalDAV) - [caldav-mcp](https://github.com/dominik1001/caldav-mcp)
- Tasks MCP (Vikunja) - see docs for build instructions
- Projects MCP (Plane) - see docs for build instructions
