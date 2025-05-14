# Jira MCP Server

Speak to Jira in natural language to get information on and modify your project. Use it with Claude Desktop in combination with a custom README that you will create with project information, so that you can delegate PM tasks, (e.g. given yoou have a list of my team and their specialities, assign any new issue to the most relevant person).

Built using the [Model Context Protocol](https://github.com/modelcontextprotocol).

The server enables:

- Project creation and configuration
- Issue and subtask management
- Issue linking and dependencies
- Automated issue workflows

## Configuration

Required environment variables:

- `JIRA_HOST`: Your Jira instance hostname
- `JIRA_EMAIL`: Your Jira account email
- `JIRA_API_TOKEN`: API token from https://id.atlassian.com/manage-profile/security/api-tokens

## Available Tools

### 1. User Management

```typescript
// Get user's account ID by email
{
  email: "user@example.com";
}
```

### 2. Issue Type Management

```typescript
// List all available issue types
// Returns: id, name, description, subtask status
// No parameters required
```

### 3. Issue Link Types

```typescript
// List all available issue link types
// Returns: id, name, inward/outward descriptions
// No parameters required
```

### 4. Issue Management

#### Retrieving Issues

```typescript
// Get all issues in a project
{
  projectKey: "PROJECT"
}

// Get issues with JQL filtering
{
  projectKey: "PROJECT",
  jql: "status = 'In Progress' AND assignee = currentUser()"
}

// Get issues assigned to user
{
  projectKey: "PROJECT",
  jql: "assignee = 'user@example.com' ORDER BY created DESC"
}
```

#### Creating Issues

```typescript
// Create a standard issue
{
  projectKey: "PROJECT",
  summary: "Issue title",
  issueType: "Task",  // or "Story", "Bug", etc.
  description: "Detailed description",
  assignee: "accountId",  // from get_user tool
  labels: ["frontend", "urgent"],
  components: ["ui", "api"],
  priority: "High"
}

// Create a subtask
{
  parent: "PROJECT-123",
  projectKey: "PROJECT",
  summary: "Subtask title",
  issueType: "Subtask",
  description: "Subtask details",
  assignee: "accountId"
}
```

#### Updating Issues

```typescript
// Update issue fields
{
  issueKey: "PROJECT-123",
  summary: "Updated title",
  description: "New description",
  assignee: "accountId",
  status: "In Progress",
  priority: "High"
}
```

#### Issue Dependencies

```typescript
// Create issue link
{
  linkType: "Blocks",  // from list_link_types
  inwardIssueKey: "PROJECT-124",  // blocked issue
  outwardIssueKey: "PROJECT-123"  // blocking issue
}
```

#### Deleting Issues

```typescript
// Delete single issue
{
  issueKey: "PROJECT-123"
}

// Delete issue with subtasks
{
  issueKey: "PROJECT-123",
  deleteSubtasks: true
}

// Delete multiple issues
{
  issueKeys: ["PROJECT-123", "PROJECT-124"]
}
```

## Field Formatting

### Description Field

The description field supports markdown-style formatting:

- Use blank lines between paragraphs
- Use "- " for bullet points
- Use "1. " for numbered lists
- Use headers ending with ":" (followed by blank line)

Example:

```
Task Overview:

This task involves implementing new features:
- Feature A implementation
- Feature B testing

Steps:
1. Design component
2. Implement logic
3. Add tests

Acceptance Criteria:
- All tests passing
- Documentation updated
```

## Error Handling

The server provides detailed error messages for:

- Invalid issue keys
- Missing required fields
- Permission issues
- API rate limits

## Setup Instructions

1. Clone the repository:

   ```bash
   git clone https://github.com/George5562/Jira-MCP-Server.git
   cd Jira-MCP-Server
   ```

2. Install dependencies:

   ```bash
   npm install
   ```

3. Configure environment variables:
   Create a `.env` file in the root directory:

   ```bash
   JIRA_HOST=your-instance.atlassian.net
   JIRA_EMAIL=your-email@example.com
   JIRA_API_TOKEN=your-api-token
   ```

4. Build the project:

   ```bash
   npm run build
   ```

5. Start the server:
   ```bash
   npm start
   ```

## Configuring Claude Desktop

To use this MCP server with Claude Desktop:

1. Locate your Claude Desktop configuration file:

   - macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Windows: `%APPDATA%/Claude/claude_desktop_config.json`
   - Linux: `~/.config/Claude/claude_desktop_config.json`

2. Add the Jira MCP server to your configuration:

   ```json
   {
     "mcp_servers": [
       {
         "name": "jira-server",
         "command": "npm start",
         "cwd": "/path/to/jira-server",
         "env": {
           "JIRA_HOST": "your-instance.atlassian.net",
           "JIRA_EMAIL": "your-email@example.com",
           "JIRA_API_TOKEN": "your-api-token"
         }
       }
     ]
   }
   ```

   Replace `/path/to/jira-server` with the absolute path to your cloned repository.

3. Restart Claude Desktop to apply the changes.

## References

- [Model Context Protocol](https://github.com/modelcontextprotocol)
- [Jira REST API Documentation](https://docs.atlassian.com/software/jira/docs/api/REST/7.12.0)
- [Jira REST API Examples](https://developer.atlassian.com/server/jira/platform/jira-rest-api-examples/)
