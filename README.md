# Claude Linear PM Skill

AI-powered Product Owner assistance for Linear ticket management and refinement.

## What It Does

When you work with Linear tickets, this Skill automatically activates to help you:

- **Create tickets** — Draft well-structured tickets with acceptance criteria from conversations
- **Analyze tickets** — Review for completeness, clarity, gaps, and dependencies
- **Propose amendments** — Suggest improvements based on code context or new information
- **Identify gaps** — Find missing coverage when breaking down epics
- **Generate questions** — Create structured refinement session discussion points
- **Plan work** — Suggest parallelization strategies and dependency analysis

## Setup

### 1. Install the Skill
Add to your Claude Code configuration at `~/.claude/config.json`:

```json
{
  "skills": [
    {
      "path": "/path/to/claude-linear-pm-skill"
    }
  ]
}
```

### 2. Configure Linear MCP Server
Follow the [Linear MCP documentation](https://linear.app/docs/mcp) to set up the MCP server and authenticate with Linear.

### 3. Add Project Context (Optional)
Create `CLAUDE.md` in your project root to establish default team and project context:

```markdown
# CLAUDE.md

## Project Management

### Linear Team
- **Team Prefix**: PROD

### Linear Projects
- **Backend Services** - https://linear.app/yourworkspace/project/backend-services-abc123
- **Frontend UI** - https://linear.app/yourworkspace/project/frontend-ui-def456
```

The skill will use these settings for all Linear operations in the project.

## How to Use

Simply describe what you need with Linear tickets. The Skill activates automatically:

```
Review the tickets for this sprint and identify any gaps

Create a ticket for implementing dark mode with acceptance criteria

What are the dependencies between PROD-100 and PROD-110?

Generate questions for our refinement session on the payment feature

Suggest improvements to this epic based on the code review
```

The Skill will:
1. Fetch relevant tickets from Linear
2. Analyze them using proven patterns
3. Present findings or proposals for review
4. **Wait for your explicit confirmation** before making changes
5. Apply updates and report results

## Documentation

- **SKILL.md** — Complete workflow guide and patterns
- **assets/ticket_template.md** — Ready-to-use templates
- **references/ticket_structure_guide.md** — Quality standards
- **references/analysis_patterns.md** — Six analysis workflows with examples
- **references/mcp_linear_tools.md** — Linear MCP API reference
- **references/refinement_session_guide.md** — Refinement best practices

## Key Principles

✅ **Always proposes before acting** — Shows changes for your review
✅ **Requires explicit confirmation** — Never assumes approval
✅ **Respects scope** — Filters to correct team and project
✅ **Specific and quoted** — References exact text when identifying issues
✅ **Explains rationale** — Shows why changes matter

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Skill not activating | Explicitly ask to use the linear-pm skill: "Use the linear-pm skill to review these tickets..." |
| Team/project not found | Add a `CLAUDE.md` file to your project with team prefix and project links (see Setup step 3) |

---

See **SKILL.md** for comprehensive documentation and workflow examples.
