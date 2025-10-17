# Local Markdown Connector - Quick Start Guide

## 5-Minute Setup

### Step 1: Install Dependencies
```bash
pip install pyyaml
```

### Step 2: Configure Your Project

Create `CLAUDE.md` in your project root:
```markdown
# CLAUDE.md

## Project Management
- **System**: Local-Markdown
- **Directory**: docs/tickets
```

### Step 3: Create Tickets Directory
```bash
mkdir -p docs/tickets
```

### Step 4: Test the Script
```bash
# Create first ticket
cat <<'EOF' | TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py create
{
  "title": "Setup authentication",
  "description": "Implement user authentication with JWT tokens",
  "type": "Feature",
  "estimate": 5
}
EOF
```

Expected output:
```json
{
  "id": "TICKET-001",
  "title": "Setup authentication",
  "type": "Feature",
  "status": "Backlog",
  "created_at": "2025-10-17T23:13:26.177589",
  "estimate": 5,
  "body": "Implement user authentication with JWT tokens"
}
```

### Step 5: View Your Ticket
```bash
cat docs/tickets/TICKET-001.md
```

Output:
```markdown
---
title: Setup authentication
type: Feature
status: Backlog
created_at: '2025-10-17T23:13:26.177589'
estimate: 5
---
Implement user authentication with JWT tokens
```

## Common Operations

### List All Tickets
```bash
TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py list
```

### Create a Ticket
```bash
cat <<'EOF' | TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py create
{
  "title": "Add password reset",
  "description": "Allow users to reset forgotten passwords",
  "type": "Feature",
  "estimate": 3
}
EOF
```

### Get Full Ticket Details
```bash
TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py get TICKET-001
```

### Update a Ticket
```bash
cat <<'EOF' | TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py update TICKET-001
{
  "status": "In Progress",
  "body": "Updated description with more details"
}
EOF
```

### Search Tickets
```bash
TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py search "authentication"
```

### List by Status
```bash
TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py list "In Progress"
```

### Create an Epic with Sub-tickets

**1. Create the epic:**
```bash
cat <<'EOF' | TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py create
{
  "title": "Email digest feature",
  "description": "Users can opt-in to weekly email digests",
  "type": "Feature"
}
EOF
# Returns: TICKET-002
```

**2. Create a sub-ticket:**
```bash
cat <<'EOF' | TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py create
{
  "title": "Design digest schema",
  "description": "Database schema for digest storage",
  "type": "Feature",
  "parent": "TICKET-002",
  "estimate": 2
}
EOF
# Returns: TICKET-003
```

**3. Create a dependent sub-ticket:**
```bash
cat <<'EOF' | TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py create
{
  "title": "Implement digest API",
  "description": "REST endpoint for digest operations",
  "type": "Feature",
  "parent": "TICKET-002",
  "blocked_by": "TICKET-003",
  "estimate": 5
}
EOF
# Returns: TICKET-004
```

**4. View sub-tickets:**
```bash
TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py get-subtickets TICKET-002
```

**5. Analyze dependencies:**
```bash
TICKETS_DIR=docs/tickets python scripts/markdown_tickets.py analyze-dependencies
```

Output shows:
- What tickets block what (critical path)
- Parent-child relationships
- Total ticket count

## Markdown Ticket Format

Every ticket is a markdown file with YAML frontmatter:

```markdown
---
title: "Ticket title"
type: "Feature|Bug|Enhancement|Documentation|Refactor|Testing|Infrastructure"
status: "Backlog|Ready|In Progress|In Review|Done"
created_at: "2025-10-17T23:13:26.177589"
estimate: 5
parent: "TICKET-001"
blocks: ["TICKET-003", "TICKET-004"]
blocked_by: "TICKET-002"
labels: ["critical", "backend"]
---

## Description
Your markdown content here.

Can include multiple sections:

## Requirements
- Requirement 1
- Requirement 2

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
```

## Using with PM Assistant Skill

Once configured, the pm-assistant skill automatically:

1. **Detects** your local-markdown setup
2. **Creates tickets** when you ask for it
3. **Analyzes dependencies** and gaps
4. **Generates refinement questions**
5. **Proposes amendments** to existing tickets

### Example: Ask PM Assistant to Create a Ticket

```
Use the pm-assistant skill to create a ticket for implementing
dark mode with acceptance criteria
```

The skill will:
1. Draft a ticket based on your description
2. Show you the proposal
3. Wait for your confirmation
4. Create TICKET-00X.md in docs/tickets/
5. Confirm with ID and location

### Example: Ask PM Assistant to Analyze Gaps

```
Use the pm-assistant skill to identify gaps in the email digest epic
```

The skill will:
1. Find the epic (TICKET-002)
2. List all sub-tickets
3. Identify missing areas (UI, tests, docs, etc.)
4. Suggest new tickets
5. Ask if you want them created

## Tips & Tricks

### Set TICKETS_DIR in .bashrc
```bash
export TICKETS_DIR=docs/tickets
```

Then use commands without the prefix:
```bash
python scripts/markdown_tickets.py list
```

### Create a wrapper script
```bash
#!/bin/bash
# tickets.sh
export TICKETS_DIR=docs/tickets
python $(dirname $0)/scripts/markdown_tickets.py "$@"
```

Usage:
```bash
./tickets.sh create < ticket.json
./tickets.sh list
./tickets.sh analyze-dependencies
```

### Version Control Integration
```bash
# Track ticket changes in git
git add docs/tickets/
git commit -m "docs(tickets): Add email digest epic and sub-tickets"

# View ticket history
git log --oneline docs/tickets/TICKET-001.md

# See who last edited a ticket
git blame docs/tickets/TICKET-001.md
```

### Bulk Import from CSV
Create Python helper to parse CSV and call the script for each row.

### Export to Other Formats
Parse JSON output and convert to Jira, Linear, or other systems.

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `ModuleNotFoundError: yaml` | Run `pip install pyyaml` |
| TICKETS_DIR not found | Check directory path, create with `mkdir -p docs/tickets` |
| IDs not sequential | IDs are managed by `.ticket_counter`; don't edit manually |
| Can't find CLAUDE.md | Create it in project root with System: Local-Markdown |
| Search not finding tickets | Search looks in title and body; ensure text is exact or partial match |

## Next Steps

1. ✅ Create a few test tickets
2. ✅ Try updating a ticket status
3. ✅ Create an epic with sub-tickets
4. ✅ Analyze dependencies
5. ✅ Use the pm-assistant skill to create/refine tickets

For full documentation, see `connectors/local-markdown.md`.
