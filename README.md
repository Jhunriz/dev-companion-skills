# DevCompanion Skills 🛠️

A collection of reusable AI skills for **opencode**, **Zed**, **Claude Code**, **Cursor**, and other AI coding assistants. Skills provide specialized knowledge for specific tasks like architecture decisions, API design, database migrations, and more.

## 📦 Quick Install

### Option 1: zed-skills MCP Server (Recommended for Zed)
Add the `zed-skills` MCP server to Zed — it auto-discovers all skills from `~/.agents/skills/`, `.agents/skills/`, and other tool directories.

```json
// Add to ~/.config/zed/settings.json
{
  "context_servers": {
    "zed-skills": {
      "command": "npx",
      "args": ["-y", "zed-skills"]
    }
  }
}
```

Then clone the repo into your project:
```bash
git clone https://github.com/YOUR_USERNAME/dev-companion-skills.git .agents/skills/dev-companion-skills
# Or for project-level:
git clone https://github.com/YOUR_USERNAME/dev-companion-skills.git .agents/skills
```

Restart Zed — all skills will appear in the next conversation.

### Option 2: opencode (Auto-detected)
```bash
git clone https://github.com/YOUR_USERNAME/dev-companion-skills.git .opencode/skills/dev-companion-skills
ln -s .opencode/skills/dev-companion-skills/* .opencode/skills/
```
Make sure `opencode.json` includes `"paths": [".opencode/skills", ".agents/skills"]`.

## 🎯 Included Skills

| Skill | Description | Trigger Phrases |
|-------|-------------|-----------------|
| `project-architect` | Project structure, conventions, folder organization | "architecture", "project structure", "where does this go", "folder conventions" |
| `api-designer` | REST/tRPC API design, endpoints, request/response formats | "API endpoint", "REST design", "tRPC procedure", "request format" |
| `db-migrator` | Database migration workflows, schema conventions, naming | "migration", "schema", "create table", "add column", "rollback" |
| `skill-template` | Template for creating new custom skills | "create skill", "new skill", "make skill" |

## 🛠️ Adding Skills

`zed-skills` auto-discovers new skills dynamically — no restart needed. Just drop a folder with a `SKILL.md` in `.agents/skills/` or `~/.agents/skills/`.

### Create a new skill from template
```bash
cp -r .agents/skills/skill-template .agents/skills/my-new-skill
# Edit .agents/skills/my-new-skill/SKILL.md
```

### Create from scratch
```bash
mkdir -p .agents/skills/my-new-skill
# Create .agents/skills/my-new-skill/SKILL.md with:
# ---
# name: my-new-skill
# description: What it does and when to use it
# ---
```

### Add from another source
Clone or copy a skill directory into `.agents/skills/` or `~/.agents/skills/`. The skill must have a `SKILL.md` with at least `name` and `description` in its YAML frontmatter.

**Rules:**
- Each skill is a subdirectory containing a `SKILL.md` file
- `SKILL.md` must have YAML frontmatter with `name` and `description`
- No restart required — `zed-skills` picks up new skills on next conversation

## 📁 Repository Structure

```
dev-companion-skills/
├── .agents/skills/           # Zed & opencode skills (13 skills)
│   ├── project-architect/
│   ├── api-designer/
│   ├── db-migrator/
│   └── ...
├── .opencode/skills/         # opencode format (same content)
│   ├── project-architect/
│   ├── api-designer/
│   ├── db-migrator/
│   └── ...
├── opencode.json             # opencode config (registers both paths)
└── README.md
```

**For Zed:** Add `zed-skills` MCP server to `~/.config/zed/settings.json`:
```json
{ "context_servers": { "zed-skills": { "command": "npx", "args": ["-y", "zed-skills"] } } }
```

## 🔧 Customization

Each skill is a starting point - **customize for your stack**:

1. **project-architect**: Update stack table, folder structure, conventions
2. **api-designer**: Choose REST/tRPC/GraphQL, update base paths, error codes
3. **db-migrator**: Match your ORM (Prisma/Laravel/Drizzle/SQLAlchemy), naming conventions

## 🤝 Compatibility

| Tool | Skill Location | Auto-discovery |
|------|----------------|----------------|
| **Zed** | `.agents/skills/` | ✅ Via zed-skills MCP |
| **opencode** | `.opencode/skills/` or `.agents/skills/` | ✅ Native + zed-skills MCP |
| **Claude Code** | `~/.claude/skills/` | ✅ Via zed-skills MCP |
| **Codex** | `~/.codex/skills/` | ✅ Via zed-skills MCP |
| **Cursor** | `~/.cursor/skills/` | ✅ Via zed-skills MCP |
| **Generic** | `~/.agents/skills/` | ✅ Via zed-skills MCP |

## 📄 License

MIT - Feel free to fork, modify, and share.

---

**Tip**: Use `zed-skills` MCP server in Zed for zero-config skill discovery across all your AI tools simultaneously.