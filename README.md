# GitHub MCP Client Setup Guide

## Prerequisites
- Windows OS
- Docker Desktop
- Node.js and npm
- GitHub account with Personal Access Token (PAT)

## Installation Steps

### 1. Install MCP Client
```powershell
npm install -g @microsoft/mcp-client
```

### 2. Configure Environment
1. Create `.env` file in your project root:
```properties
GITHUB_PERSONAL_ACCESS_TOKEN=your_token_here
GITHUB_USERNAME=your_username
GITHUB_EMAIL=your_email
```

2. Add `.env` to `.gitignore`:
```plaintext
.env
*.env
.env.*
```

### 3. Configure MCP
Create `mcp.json` in your project root:
```json
{
  "mcp": {
    "inputs": [
      {
        "type": "promptString",
        "id": "github_token",
        "description": "GitHub Personal Access Token",
        "password": true
      }
    ],
    "servers": {
      "github": {
        "command": "docker",
        "args": [
          "run",
          "-i",
          "--rm",
          "-e",
          "GITHUB_PERSONAL_ACCESS_TOKEN",
          "ghcr.io/github/github-mcp-server"
        ],
        "env": {
          "GITHUB_PERSONAL_ACCESS_TOKEN": "${github_token}"
        }
      }
    }
  }
}
```

## Usage

### 1. Verify Installation
```powershell
mcp --version
docker --version
```

### 2. Start MCP Server
```powershell
# Pull the latest MCP server image
docker pull ghcr.io/github/github-mcp-server

# Start the server
docker run -i --rm -e GITHUB_PERSONAL_ACCESS_TOKEN=%GITHUB_PERSONAL_ACCESS_TOKEN% ghcr.io/github/github-mcp-server
```

### 3. Create Repository
In a new terminal:
```powershell
mcp github create-repository --name MCPNewRepo --private
```

## Common Commands

### Repository Management
```powershell
# Create new repository
mcp github create-repository --name RepoName --private

# List repositories
mcp github list-repositories

# Delete repository
mcp github delete-repository --name RepoName
```

## Troubleshooting

### Server Issues
Check Docker container status:
```powershell
docker ps | findstr "github-mcp-server"
```

View server logs:
```powershell
docker logs $(docker ps -q --filter ancestor=ghcr.io/github/github-mcp-server)
```

### Authentication Issues
1. Verify token is set:
```powershell
echo %GITHUB_PERSONAL_ACCESS_TOKEN%
```

2. Ensure Docker can access the token:
```powershell
docker run --rm -e GITHUB_PERSONAL_ACCESS_TOKEN=%GITHUB_PERSONAL_ACCESS_TOKEN% ghcr.io/github/github-mcp-server echo "Token configured"
```

## Security Notes
- Never commit your GitHub token to version control
- Use environment variables for sensitive data
- Regularly rotate your GitHub PAT
- Use tokens with minimum required permissions

## Additional Resources
- [GitHub CLI Documentation](https://cli.github.com/manual/)
- [Docker Documentation](https://docs.docker.com/)
- [GitHub PAT Documentation](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)