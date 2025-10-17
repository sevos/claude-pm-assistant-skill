---
name: linear-pm
description: Product Owner assistance for Linear ticket refinement, epic breakdown, dependency analysis, and backlog management. Use this skill when working with Linear tickets to create, analyze, propose amendments, or generate discussion questions. Best used alongside a Linear workspace with defined team prefixes and project names (e.g., team "AIA", project "Email parser").
---

# Linear PM Skill

## Overview

This skill enables Product Owner workflows within Linear:
- Create and refine tickets with proper structure and acceptance criteria
- Analyze tickets for gaps, clarity, completeness, and dependencies
- Break down epics into actionable sub-tickets
- Generate meaningful refinement session questions
- Propose amendments based on conversation context

The skill uses the connected Linear MCP server to directly query and mutate Linear data, and leverages LLM reasoning for analysis tasks.

## Getting Started: Team and Project Context

Before starting any Linear work, establish the workspace context:

### 1. Discover or Provide Context

The skill needs:
- **Linear Team Prefix**: e.g., "AIA"
- **Linear Project**: e.g., "Email parser"

These may come from:
- **CLAUDE.md file** in the project directory (recommended location)
- **User input** if not available in files
- **Linear discovery** if neither is explicitly provided

### 2. Linear MCP Server Discovery

If context is not provided, the skill will:
1. Use `mcp__linear-server__list_teams` to list available teams
2. Use `mcp__linear-server__list_projects` to find the project
3. Ask user to confirm the correct team/project before proceeding

This ensures all operations are scoped to the correct workspace.

## Core Capabilities

### 1. Create Tickets from Conversation

**Scenario**: "Create a ticket for this feature with acceptance criteria" or "Based on the conversation transcript create epic with tickets/ticket"

**Process**:
1. Extract requirements from conversation context
2. Use ticket template from `assets/ticket_template.md`
3. Structure as simple or complex ticket based on scope
4. Apply appropriate type labels (Feature, Bug, Enhancement, etc.)
5. Present proposal to user for review
6. **After user confirmation**: Create ticket using `mcp__linear-server__create_issue`
7. Report created ticket with ID and link

**Guidelines**:
- Refer to `references/ticket_structure_guide.md` for formatting standards
- Use `references/mcp_linear_tools.md` for API details
- Include acceptance criteria for complex work
- Flag open questions when scope is unclear
- Suggest dependencies if work relates to existing tickets

### 2. Propose Ticket Amendments

**Scenario**: "Are there any wrong assumptions in the ticket?" or "Based on the conversation transcript suggest adjustments to epic XXX-123"

**Process**:
1. Fetch existing ticket/epic using `mcp__linear-server__get_issue`
2. Analyze current state (description, acceptance criteria, scope)
3. Cross-reference with provided context (code, conversation, etc.)
4. Use patterns from `references/analysis_patterns.md` to identify:
   - Assumption mismatches
   - Scope gaps or overreach
   - Missing edge cases or error handling
   - Outdated requirements
5. Present proposed changes:
   - "Current state" (quote from ticket)
   - "Suggested changes" (with rationale)
   - "Questions for team" (if needed)
6. **After user confirmation**: Update ticket using `mcp__linear-server__update_issue`
7. Report changes applied

**Guidelines**:
- Be specific: quote the problematic text
- Explain the "why" behind each suggested change
- Distinguish between critical (must fix) and nice-to-have improvements
- Ask clarifying questions if context is ambiguous

### 3. Analyze Tickets for Quality

**Scenario**: "Review existing Linear tickets for completeness, clarity, dependencies, open questions" for range AIA-100 through AIA-110

**Process**:
1. Fetch all tickets in range using `mcp__linear-server__list_issues` with appropriate filters
2. For each ticket, evaluate against criteria:
   - **Clarity**: Title, description, acceptance criteria
   - **Completeness**: All required fields, edge cases covered
   - **Dependencies**: Blocks/Blocked-by relationships identified
   - **Open questions**: Uncertainties flagged
3. Use `references/analysis_patterns.md` Pattern 4 for systematic evaluation
4. Compile findings:
   - Strong tickets (ready for development)
   - Needs work (specific improvements recommended)
   - Needs breakdown (too large)
   - Blocked (waiting on decisions)
5. Present report with:
   - Summary per ticket
   - Recommended actions
   - Highest-priority refinements needed

**Guidelines**:
- Use consistent evaluation structure
- Highlight both strengths and issues
- Provide specific, actionable recommendations
- Flag patterns across multiple tickets (e.g., all missing error handling)

### 4. Identify Gaps in Epic Coverage

**Scenario**: "Identify gaps in the planned tickets for epic XXX-123"

**Process**:
1. Fetch epic using `mcp__linear-server__get_issue`
2. Query subtickets: `mcp__linear-server__list_issues` with filter `parent:"XXX-123"`
3. Analyze epic scope vs. ticket coverage using `references/analysis_patterns.md` Pattern 1:
   - Frontend/UI components
   - Backend services and APIs
   - Testing and QA work
   - Documentation and knowledge base
   - Deployment or infrastructure
   - Edge cases and error scenarios
4. Present findings:
   - Identified gaps with context
   - Suggested new tickets for each gap
   - Estimated scope per gap
5. Ask: "Would you like me to create tickets for these gaps?"
6. **After user confirmation**: Create tickets using `mcp__linear-server__create_issue`

**Guidelines**:
- Be thorough but realistic (not everything needs a separate ticket)
- Group related gaps (e.g., multiple API endpoints in one ticket)
- Consider team's estimation approach when scoping gaps
- Link new tickets to epic as subtickets

### 5. Analyze Dependencies and Suggest Parallelization

**Scenario**: User asks about dependencies between tickets or how to parallelize work

**Process**:
1. Fetch relevant tickets and analyze relationships
2. Use `references/analysis_patterns.md` Pattern 3:
   - Extract explicit Blocks/Blocked-by relationships
   - Identify implicit dependencies
   - Find critical path (work that must complete first)
   - Group by parallelizable tracks
3. Present parallelization strategy:
   - Work that can happen simultaneously
   - Critical path dependencies
   - Recommended team allocation
4. Suggest implementation sequence

**Guidelines**:
- Consider frontend/backend can often run in parallel if API contract is clear
- Testing can often run alongside implementation if setup is clear
- Documentation can start early with skeleton/outline
- Infrastructure work often critical path

### 6. Generate Refinement Session Questions

**Scenario**: "Generate questions for the next refinement session for tickets XXX-100 through XXX-110"

**Process**:
1. Fetch ticket range using `mcp__linear-server__list_issues` with filters
2. Analyze each ticket for uncertainty patterns:
   - Missing acceptance criteria
   - Ambiguous requirements
   - Unknown trade-offs
   - Implicit assumptions
   - Unclear edge cases
3. Use `references/refinement_session_guide.md` and `references/analysis_patterns.md` Pattern 5
4. Generate questions organized by:
   - **Critical blockers**: Must resolve first
   - **Design/Technical questions**: Needed before building
   - **Edge cases**: Clarify completeness
   - **Dependencies**: Identify coordination needs
   - **Success metrics**: Define what "done" means
5. Present as structured question set with:
   - Target audience (Product, Engineering, Design)
   - Why the question matters
   - Suggested answers or options

**Guidelines**:
- Prioritize high-impact questions first
- Frame as open-ended (not leading)
- Group related questions by theme
- Note interdependencies between questions
- Suggest time-boxing for discussion

## Analysis Patterns and Templates

### Reference Materials

**For ticket structure standards**:
- `references/ticket_structure_guide.md` - Detailed standards, templates, red flags
- `assets/ticket_template.md` - Practical templates for simple/complex/bug/epic tickets

**For analysis workflows**:
- `references/analysis_patterns.md` - Detailed patterns for gaps, assumptions, dependencies, clarity, refinement
- `references/mcp_linear_tools.md` - Complete Linear MCP tool reference

**For refinement sessions**:
- `references/refinement_session_guide.md` - Question generation, facilitation, templates

### When to Use Each Analysis Pattern

| Pattern | Trigger Question | Reference |
|---------|------------------|-----------|
| Gap Identification | "Identify gaps in epic XXX" | `analysis_patterns.md` Pattern 1 |
| Assumption Mismatch | "Are there wrong assumptions?" | `analysis_patterns.md` Pattern 2 |
| Dependency Analysis | "What are dependencies?" | `analysis_patterns.md` Pattern 3 |
| Quality Review | "Review tickets for completeness" | `analysis_patterns.md` Pattern 4 |
| Refinement Questions | "Generate questions for session" | `analysis_patterns.md` Pattern 5, `refinement_session_guide.md` |
| Epic Adjustment | "Suggest adjustments to epic" | `analysis_patterns.md` Pattern 6 |

## Workflow: Create and Propose

All creation and amendment operations follow this pattern:

### Step 1: Gather Context
- User provides requirements (conversation, transcript, existing ticket)
- Establish team/project scope if not already known
- Fetch existing data if modifying

### Step 2: Analyze and Draft
- Use appropriate patterns from `references/analysis_patterns.md`
- Follow structure from `references/ticket_structure_guide.md` or templates
- Draft ticket/amendment with complete context

### Step 3: Present for Review
- Show "current state" and "proposed state" for amendments
- Show "proposed ticket" for new tickets
- Include rationale for each decision
- Ask clarifying questions if needed

### Step 4: Wait for Confirmation
- **Do not proceed** until user explicitly confirms
- Offer to adjust draft if user suggests changes
- Re-present adjusted version for confirmation

### Step 5: Apply Changes
- Use Linear MCP tools to create/update
- `mcp__linear-server__create_issue` for new tickets
- `mcp__linear-server__update_issue` for amendments
- Include team/project context in all operations

### Step 6: Report Results
- Confirm what was created/updated
- Provide ticket ID and Link (if available)
- Ask: "What would you like to do next?"

## Best Practices

### Team and Project Scoping
- **Always verify** team prefix and project before operations
- **Check CLAUDE.md** in project directory for context
- **Discover from Linear** if not explicitly provided
- **Filter all queries** to correct team and project

### Ticket Quality
- Read `references/ticket_structure_guide.md` for quality standards
- Ensure titles are specific and action-oriented
- Include acceptance criteria for complex work
- Flag open questions when scope is unclear
- Link dependencies explicitly

### Analysis Completeness
- Quote specific text when identifying issues
- Explain the "why" behind recommendations
- Distinguish between facts (what's written) and inferences
- Flag assumptions clearly
- Suggest options with trade-offs when multiple paths exist

### User Confirmation Protocol
- Always show proposals before applying
- Wait for explicit user confirmation
- Never assume approval
- Offer revision if user suggests changes
- Re-present for approval after revisions

### Linear MCP Usage
- Refer to `references/mcp_linear_tools.md` for complete tool reference
- Use filters appropriately (team, project, status, etc.)
- Handle errors gracefully (e.g., "Project not found, please verify team/project")
- Respect rate limits and batch operations when possible
