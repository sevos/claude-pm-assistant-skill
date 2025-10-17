# Linear MCP Tools Reference

This guide explains how to use the Linear MCP server tools for querying and mutating Linear data.

## Discovery and Context

Before performing any operations, always discover the workspace context:

### 1. Get Current User and Team
```
mcp__linear-server__get_user
- No parameters required
- Returns: Current user information including team membership
```

### 2. List Teams
```
mcp__linear-server__list_teams
- Returns all available teams
- Use to verify team ID/prefix before operations
```

### 3. Get Specific Team
```
mcp__linear-server__get_team
- Parameters: teamId (e.g., "AIA")
- Returns team details including projects, settings, labels
```

### 4. List Projects
```
mcp__linear-server__list_projects
- Optional: Filter by team
- Returns all projects accessible to the user
- Use to find the correct project ID (e.g., "Email parser")
```

### 5. Get Specific Project
```
mcp__linear-server__get_project
- Parameters: projectId
- Returns project details, settings, team affiliation
```

## Querying Tickets

### Search and Retrieve Issues
```
mcp__linear-server__list_issues
- Parameters:
  - filter: Linear query filter (e.g., "team:AIA project:\"Email parser\" status:\"In Progress\"")
  - limit: Number of results (optional)
- Returns: List of issues matching criteria
- Example filters:
  - "team:AIA" - Filter by team
  - "project:\"Email parser\"" - Filter by project name
  - "status:\"In Progress\"" - Filter by status
  - "assignee:@me" - Assigned to current user
  - Combine with AND/OR operators
```

### Get Single Issue
```
mcp__linear-server__get_issue
- Parameters: issueId (e.g., "AIA-123")
- Returns: Full issue details including description, acceptance criteria, relationships
```

### Search Documentation
```
mcp__linear-server__search_documentation
- Parameters: query string
- Returns: Matching documentation from Linear workspace
```

## Querying Relationships and Metadata

### Get Issue Status
```
mcp__linear-server__get_issue_status
- Parameters: statusId
- Returns: Status details
```

### List Issue Statuses
```
mcp__linear-server__list_issue_statuses
- Returns: All available statuses in workspace
```

### List Issue Labels
```
mcp__linear-server__list_issue_labels
- Returns: All available labels with types (Type/Feature, Type/Bug, etc.)
```

### Create Issue Label
```
mcp__linear-server__create_issue_label
- Parameters: name, color, description (optional)
- Returns: New label ID
```

### List Project Labels
```
mcp__linear-server__list_project_labels
- Parameters: projectId
- Returns: Labels configured for this project
```

### List Cycles
```
mcp__linear-server__list_cycles
- Parameters: teamId (optional)
- Returns: All available cycles/sprints
```

## Creating and Updating Issues

### Create Issue
```
mcp__linear-server__create_issue
- Parameters:
  - teamId: Team prefix (e.g., "AIA")
  - projectId: Project identifier
  - title: Issue title (string, required)
  - description: Issue description (markdown, optional)
  - stateId: Status ID (optional)
  - assigneeId: User ID to assign (optional)
  - labelIds: Array of label IDs (optional)
  - cycleId: Sprint/cycle ID (optional)
  - parentId: Parent issue for sub-tickets (optional)
  - estimate: Story points (optional, numeric)
- Returns: Created issue object with ID
```

### Update Issue
```
mcp__linear-server__update_issue
- Parameters:
  - issueId: Issue identifier (e.g., "AIA-123")
  - title: New title (optional)
  - description: New description (optional)
  - stateId: New status (optional)
  - assigneeId: New assignee (optional)
  - labelIds: New labels (optional)
  - cycleId: New cycle (optional)
  - estimate: New estimate (optional)
- Returns: Updated issue object
```

## Managing Comments and Collaboration

### Create Comment
```
mcp__linear-server__create_comment
- Parameters:
  - issueId: Issue identifier
  - body: Comment text (markdown)
- Returns: Created comment object
```

### List Comments
```
mcp__linear-server__list_comments
- Parameters: issueId
- Returns: All comments on the issue
```

## Documents and Knowledge Base

### List Documents
```
mcp__linear-server__list_documents
- Returns: All documents in workspace
- Optional: Filter by project
```

### Get Document
```
mcp__linear-server__get_document
- Parameters: documentId
- Returns: Document content
```

## Common Workflows

### Workflow 1: Fetch Epic with Subtickets
```
1. Get issue (epic ID)
2. List issues with filter: "parent:\"EPIC-ID\""
3. Process each sub-ticket
```

### Workflow 2: Create Epic with Subtickets
```
1. Create parent issue (epic)
2. Create child issues with parentId pointing to epic
3. Optionally add labels, assignees, cycles
```

### Workflow 3: Search and Analyze Tickets
```
1. List issues with appropriate filters
2. Get each issue individually for full details
3. Analyze relationships (blocks, blocked-by)
4. Compile findings
```

### Workflow 4: Propose and Apply Changes
```
1. Analyze current state (fetch existing tickets)
2. Present proposal to user (structure, amendments, etc.)
3. Wait for user confirmation
4. Create/update issues after confirmation
5. Report what was created/updated
```

## Scoping to Team and Project

**Important:** Always scope operations to the correct team and project:

1. **From CLAUDE.md**: Project files may contain:
   - `Linear Team Prefix`: e.g., "AIA"
   - `Linear Project`: e.g., "Email parser"

2. **Discovery if not provided**:
   - Call `mcp__linear-server__list_projects` to find project ID
   - Call `mcp__linear-server__get_team` to verify team settings

3. **In queries**:
   - Use filters: `team:AIA project:"Email parser"`
   - Ticket IDs include team prefix: `AIA-123`

## Notes

- All timestamps are ISO 8601 format
- Descriptions support Markdown formatting
- Acceptance criteria should be formatted as checkboxes or Given-When-Then
- Always verify team/project before bulk operations
- For large operations, confirm with user before proceeding
