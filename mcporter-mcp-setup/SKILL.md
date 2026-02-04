# MCPorter MCP Server Setup Skill

Set up and manage MCP servers with MCPorter CLI.

## When to Use

Use this skill when:
- Setting up MCP servers for AI assistants
- Configuring multiple MCP server instances
- Managing Jira, GitHub, or other MCP integrations
- Creating portable MCP server configurations

## What is MCPorter?

MCPorter is a CLI and runtime for Model Context Protocol (MCP) servers. It enables:
- Access to any MCP server without direct installation
- Auto-discovery of configured servers
- Ergonomic tool calling from command line
- OAuth support for hosted MCPs

## Setup Guide

### Installation

```bash
npm install -g mcporter
```

### Configuration File

MCPorter reads from `~/.mcporter/mcporter.json`:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "uvx",
      "args": ["package-name"],
      "env": {
        "VAR1": "value1",
        "VAR2": "value2"
      }
    }
  }
}
```

### Example: Jira MCP Setup

```json
{
  "mcpServers": {
    "jira-primary": {
      "command": "uvx",
      "args": ["mcp-atlassian"],
      "env": {
        "JIRA_URL": "https://company.atlassian.net",
        "JIRA_USERNAME": "your-email@company.com",
        "JIRA_API_TOKEN": "your-api-token"
      }
    },
    "jira-secondary": {
      "command": "uvx",
      "args": ["mcp-atlassian"],
      "env": {
        "JIRA_URL": "https://other.atlassian.net",
        "JIRA_USERNAME": "other-email@company.com",
        "JIRA_API_TOKEN": "other-api-token"
      }
    }
  }
}
```

## Common MCP Servers

### Context7 (Documentation)

```json
{
  "context7": {
    "command": "npx",
    "args": ["-y", "@upstash/context7-mcp"],
    "env": {}
  }
}
```

### Jira

```json
{
  "jira": {
    "command": "uvx",
    "args": ["mcp-atlassian"],
    "env": {
      "JIRA_URL": "https://your-domain.atlassian.net",
      "JIRA_USERNAME": "your-email@company.com",
      "JIRA_API_TOKEN": "your-api-token"
    }
  }
}
```

### Playwright (Browser Automation)

```json
{
  "playwright": {
    "command": "npx",
    "args": ["-y", "@playwright/mcp@latest"],
    "env": {}
  }
}
```

### GitHub

```json
{
  "github": {
    "command": "uvx",
    "args": ["mcp-github"],
    "env": {
      "GITHUB_TOKEN": "ghp_your-token"
    }
  }
}
```

## Commands

### List Configured Servers

```bash
mcporter list
```

Output:
```
context7 (2 tools, 2.5s)
jira (32 tools, 4.0s)
playwright (22 tools, 4.4s)
✔ Listed 3 servers (3 healthy)
```

### Call a Tool

Format: `mcporter call <server>.<tool>`

```bash
# Call Jira tool
mcporter call jira.jira_get_project_issues project_key:CW

# Call with multiple args
mcporter call context7.resolve-library-id query:"React hooks" libraryName:react
```

### Call Syntax Options

```bash
# Flag-style (shell-friendly)
mcporter call jira.create_issue project_key:CW summary:"New task" issue_type:Task

# Function-call style
mcporter call 'jira.create_issue(projectKey: "CW", summary: "New task")'
```

### List Server Tools

```bash
mcporter list jira --schema
```

Shows tool signatures with parameters.

### Ad-Hoc Servers (No Config)

```bash
# Run without adding to config
mcporter call --stdio "uvx mcp-atlassian" --env "JIRA_URL=..." jira_list_projects

# Persist after testing
mcporter call --stdio "uvx mcp-atlassian" --env "JIRA_URL=..." --persist --name my-jira
```

## Multiple Instances

Run the same MCP server with different configurations:

```json
{
  "mcpServers": {
    "jira-work": {
      "command": "uvx",
      "args": ["mcp-atlassian"],
      "env": {
        "JIRA_URL": "https://work.atlassian.net",
        "JIRA_USERNAME": "work@email.com",
        "JIRA_API_TOKEN": "work-token"
      }
    },
    "jira-personal": {
      "command": "uvx",
      "args": ["mcp-atlassian"],
      "env": {
        "JIRA_URL": "https://personal.atlassian.net",
        "JIRA_USERNAME": "personal@email.com",
        "JIRA_API_TOKEN": "personal-token"
      }
    }
  }
}
```

Usage:
```bash
mcporter call jira-work.jira_get_project_issues project_key:WORK
mcporter call jira-personal.jira_get_project_issues project_key:PERSONAL
```

## Environment Variables

Some MCP servers need credentials:

| Server | Variables |
|--------|------------|
| Jira | `JIRA_URL`, `JIRA_USERNAME`, `JIRA_API_TOKEN` |
| GitHub | `GITHUB_TOKEN` |
| Slack | `SLACK_BOT_TOKEN` |
| Google | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` |

Generate API tokens:
- **Jira**: https://id.atlassian.com/manage-profile/security/api-tokens
- **GitHub**: https://github.com/settings/tokens

## Configuration Locations

MCPorter searches in order:
1. `~/.mcporter/mcporter.json` (home config)
2. `./config/mcporter.json` (project config)
3. Claude/Codex/Cursor imports

## Tips

### Use Different SSH Keys

For multiple GitHub accounts:

```json
{
  "github-personal": {
    "command": "npx",
    "args": ["-y", "mcp-github"],
    "env": {
      "GITHUB_TOKEN": "ghp_personal-token"
    }
  }
}
```

### Headless vs Headed

For Playwright:
```json
{
  "playwright-headed": {
    "command": "npx",
    "args": ["-y", "@playwright/mcp@latest"],
    "env": { "HEADLESS": "false" }
  }
}
```

### Docker MCP Servers

```json
{
  "postgres": {
    "command": "docker",
    "args": ["run", "--rm", "-i", "mcp/postgres"],
    "env": {
      "DATABASE_URL": "postgresql://user:pass@localhost:5432/db"
    }
  }
}
```

## Troubleshooting

### Server Won't Start

```bash
# Check server logs
mcporter list --verbose

# Test manually
uvx mcp-atlassian
```

### Connection Timeout

Increase timeout:
```bash
mcporter list --timeout 60000
```

### Auth Errors

For OAuth servers:
```bash
mcporter auth server-name
```

## Example Workflow

1. **Install MCPorter**
   ```bash
   npm install -g mcporter
   ```

2. **Create config file**
   ```bash
   mkdir -p ~/.mcporter
   nano ~/.mcporter/mcporter.json
   ```

3. **Add server configuration**
   ```json
   {
     "mcpServers": {
       "context7": {
         "command": "npx",
         "args": ["-y", "@upstash/context7-mcp"],
         "env": {}
       }
     }
   }
   ```

4. **Test connection**
   ```bash
   mcporter list
   ```

5. **Call tools**
   ```bash
   mcporter call context7.resolve-library-id query:"React hooks" libraryName:react
   ```

## Resources

- **MCPorter GitHub**: [github.com/steipete/mcporter](https://github.com/steipete/mcporter)
- **MCP Registry**: [mcpmarket.com](https://mcpmarket.com)
- **Awesome MCP Servers**: [github.com/wong2/awesome-mcp-servers](https://github.com/wong2/awesome-mcp-servers)

---

*Last updated: February 3, 2026*
