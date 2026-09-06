---
name: skill-template
description: Template for creating new custom skills. Copy this folder and customize. Trigger for "create skill", "new skill", "make skill", "skill template".
---

# Skill Template

**Copy this folder to create a new skill:**

```bash
cp -r .opencode/skills/skill-template .opencode/skills/my-new-skill
# Then edit SKILL.md
```

## Frontmatter (Required)

```yaml
---
name: my-new-skill              # Must match folder name (lowercase, hyphens)
description: One sentence covering what this skill does AND when to trigger it. Front-load keywords like "write tests", "fix migration", "build component", "deploy", "debug". Use "Trigger for..." to list exact phrases.
disable-model-invocation: false  # Optional: true to prevent model from invoking this skill automatically
---
```

## Skill Body Structure

```markdown
# Skill Title

Brief 1-2 sentence overview of what this skill covers.

## When to Use

- **Trigger phrases**: "exact phrases users say"
- **File patterns**: `*.test.ts`, `migration/*.sql`
- **Tasks**: "refactor", "add feature", "debug"

## Core Concepts

Key principles, patterns, or conventions this skill enforces.

## Patterns & Examples

### Pattern Name

```language
// Runnable code example
// Include imports, types, complete implementation
```

**When to use**: Condition
**Avoid when**: Condition

## Reference

### Commands
```bash
# Common commands
command --flag
```

### Files
| Path | Purpose |
|------|---------|
| `src/...` | Description |

### Quick Decisions
| Question | Answer |
|----------|--------|
| How to X? | Do Y |
| Where to put Z? | `path/` |

## Related Skills

- `other-skill` - For related tasks
- `another-skill` - For complementary work
```

## Description Writing Tips

**Good descriptions** (specific, keyword-rich):
- "React component patterns for this project. Trigger for 'build component', 'create component', 'component structure', 'React patterns'."
- "Database migration workflows for PostgreSQL/Prisma. Trigger for 'migration', 'schema', 'create table', 'add column', 'rollback'."

**Bad descriptions** (vague, generic):
- "Helps with React components"
- "Database stuff"

## Testing Your Skill

1. **Restart opencode** after creating/editing
2. **Trigger it**: Say the exact phrases in `description`
3. **Verify**: Check the skill loads (you'll see it in agent context)
4. **Iterate**: Refine description if it triggers too much/too little

## Publishing (Optional)

To share across projects:
1. Move to `~/.config/opencode/skills/` (global)
2. Or add to `skills.paths` in `opencode.json`
3. Or publish as external skill repo

---

**Delete this template section** when creating your actual skill. Keep only the structure you need.