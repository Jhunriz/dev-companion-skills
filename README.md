# DevCompanion Skills 🛠️

A collection of reusable AI skills for **opencode**, **Zed**, **Claude Code**, **Cursor**, and other AI coding assistants. Skills provide specialized knowledge for specific tasks like architecture decisions, API design, database migrations, and more.

## 📦 Quick Install

### Option 1: Zed Native (Recommended)
```bash
# Global (all projects)
git clone https://github.com/YOUR_USERNAME/dev-companion-skills.git ~/.agents/skills/dev-companion-skills
ln -s ~/.agents/skills/dev-companion-skills/* ~/.agents/skills/

# Or project-local
git clone https://github.com/YOUR_USERNAME/dev-companion-skills.git .agents/skills/dev-companion-skills
ln -s .agents/skills/dev-companion-skills/* .agents/skills/
```

### Option 2: opencode (Auto-detected by zed-skills MCP)
```bash
# Global
git clone https://github.com/YOUR_USERNAME/dev-companion-skills.git ~/.config/opencode/skills/dev-companion-skills
ln -s ~/.config/opencode/skills/dev-companion-skills/* ~/.config/opencode/skills/

# Project-local (add to your project's opencode.json)
# { "skills": { "paths": [".opencode/skills"] } }
git clone https://github.com/YOUR_USERNAME/dev-companion-skills.git .opencode/skills/dev-companion-skills
ln -s .opencode/skills/dev-companion-skills/* .opencode/skills/
```

### Option 3: zed-skills MCP Server (Auto-discovers everything)
Install [zed-skills](https://github.com/Chenkeliang/zed-skills) MCP server in Zed - it automatically finds skills from opencode, Claude Code, Codex, Cursor, and more.

## 🎯 Included Skills

| Skill | Description | Trigger Phrases |
|-------|-------------|-----------------|
| `project-architect` | Project structure, conventions, folder organization | "architecture", "project structure", "where does this go", "folder conventions" |
| `api-designer` | REST/tRPC API design, endpoints, request/response formats | "API endpoint", "REST design", "tRPC procedure", "request format" |
| `db-migrator` | Database migration workflows, schema conventions, naming | "migration", "schema", "create table", "add column", "rollback" |
| `skill-template` | Template for creating new custom skills | "create skill", "new skill", "make skill" |

## 🛠️ Creating New Skills

```bash
# Copy template
cp -r .agents/skills/skill-template .agents/skills/my-new-skill

# Edit the skill
# 1. Rename folder to match skill name (lowercase, hyphens)
# 2. Edit SKILL.md - update name, description, and body
# 3. Restart Zed/opencode (or auto-reload in Zed)
```

## 📁 Repository Structure

```
dev-companion-skills/
├── .agents/skills/           # Zed native format
│   ├── project-architect/
│   ├── api-designer/
│   ├── db-migrator/
│   └── skill-template/
├── .opencode/skills/         # opencode format (same content)
│   ├── project-architect/
│   ├── api-designer/
│   ├── db-migrator/
│   └── skill-template/
├── opencode.json             # opencode config (registers .opencode/skills)
└── README.md
```

## 🔧 Customization

Each skill is a starting point - **customize for your stack**:

1. **project-architect**: Update stack table, folder structure, conventions
2. **api-designer**: Choose REST/tRPC/GraphQL, update base paths, error codes
3. **db-migrator**: Match your ORM (Prisma/Laravel/Drizzle/SQLAlchemy), naming conventions

## 🤝 Compatibility

| Tool | Skill Location | Auto-discovery |
|------|----------------|----------------|
| **Zed** | `~/.agents/skills/` or `.agents/skills/` | ✅ Native |
| **opencode** | `~/.config/opencode/skills/` or `.opencode/skills/` | ✅ Native |
| **Claude Code** | `~/.claude/skills/` | ✅ Via zed-skills MCP |
| **Codex** | `~/.codex/skills/` | ✅ Via zed-skills MCP |
| **Cursor** | `~/.cursor/skills/` | ✅ Via zed-skills MCP |
| **Generic** | `~/.agents/skills/` | ✅ Via zed-skills MCP |

## 📄 License

MIT - Feel free to fork, modify, and share.

---

**Tip**: Use `zed-skills` MCP server in Zed for zero-config skill discovery across all your AI tools simultaneously.