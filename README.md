# MSSQL MCP Server

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Node.js](https://img.shields.io/badge/Node.js-339933?logo=node.js&logoColor=white)](https://nodejs.org/)

> **A Model Context Protocol (MCP) server that enables AI assistants to interact with both local SQL Server and Azure SQL Database through natural language.**

<a href="https://glama.ai/mcp/servers/@Nirmal123K/mssql-mcp">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/@Nirmal123K/mssql-mcp/badge" alt="MSSQL Server MCP server" />
</a>

## 🚀 What Makes This Special?

This MCP server **solves a critical limitation** of existing solutions by supporting **both local SQL Server and Azure SQL Database** connections. While the original Azure-Samples implementation only works with Azure SQL Database, this enhanced version enables:

- **🏠 Local Development**: Connect to local SQL Server instances using username/password authentication
- **☁️ Azure Cloud**: Full Azure SQL Database support with Azure AD authentication
- **🔄 Seamless Switching**: Use the same tools for both environments

**Quick Example:**
```text
You: "Show me all customers from New York"
AI: *securely queries your database and returns results in plain English*

You: "Create a table for storing product reviews"
AI: *generates and executes the appropriate CREATE TABLE statement*
```

## 📦 Quick Start

### Prerequisites
- **Node.js 18+**
- **SQL Server** (local) or **Azure SQL Database**
- **AI Assistant**: Claude Desktop or VS Code Agent

### Installation
```bash
git clone https://github.com/Nirmal123K/mssql-mcp.git
cd mssql-mcp-server
npm install && npm run build
```

### Configuration

**Local SQL Server:**
```bash
export SERVER_NAME="localhost"
export DATABASE_NAME="your_database"
export SQL_USER="your_username"
export SQL_PASSWORD="your_password"
export TRUST_SERVER_CERTIFICATE="true"
```

**Azure SQL Database:**
```bash
export SERVER_NAME="your-server.database.windows.net"
export DATABASE_NAME="your_database"
# Uses Azure AD authentication (run 'az login' first)
```

## ⚙️ AI Assistant Setup

### Claude Desktop
Add to `claude_desktop_config.json`:

**Local SQL Server:**
```json
{
  "mcpServers": {
    "mssql": {
      "command": "node",
      "args": ["/path/to/mssql-mcp-server/dist/index.js"],
      "env": {
        "SERVER_NAME": "localhost",
        "DATABASE_NAME": "your_database",
        "SQL_USER": "your_username",
        "SQL_PASSWORD": "your_password",
        "TRUST_SERVER_CERTIFICATE": "true"
      }
    }
  }
}
```

**Azure SQL Database:**
```json
{
  "mcpServers": {
    "mssql": {
      "command": "node",
      "args": ["/path/to/mssql-mcp-server/dist/index.js"],
      "env": {
        "SERVER_NAME": "your-server.database.windows.net",
        "DATABASE_NAME": "your_database",
        "READONLY": "true"
      }
    }
  }
}
```

### VS Code Agent
Create `.vscode/mcp.json` with similar configuration using `"type": "stdio"`.

**Config Locations:**
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

## 📋 Environment Variables

| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| `SERVER_NAME` | ✅ | SQL Server hostname | `localhost` or `server.database.windows.net` |
| `DATABASE_NAME` | ✅ | Target database name | `MyDatabase` |
| `SQL_USER` | 🔄 | Username (local SQL Server only) | `sa` or `myuser` |
| `SQL_PASSWORD` | 🔄 | Password (local SQL Server only) | `MyPassword123!` |
| `READONLY` | ❌ | Restrict to read-only operations | `true` or `false` (default) |
| `TRUST_SERVER_CERTIFICATE` | ❌ | Trust self-signed certificates | `true` or `false` (default) |

> **🔄 = Required for local SQL Server, not needed for Azure SQL Database**

## ✅ Verification

```bash
# Test the MCP server
echo '{"jsonrpc": "2.0", "id": 1, "method": "tools/list"}' | node dist/index.js
```

## 🛠️ Available Tools

| Tool | Purpose | Example |
|------|---------|---------|
| **`read_data`** | Query data with SELECT | "Show customers from New York" |
| **`insert_data`** | Add new records | "Insert new product with price $99" |
| **`update_data`** | Modify existing data | "Update order status to shipped" |
| **`create_table`** | Create new tables | "Create table for reviews" |
| **`create_index`** | Add database indexes | "Create index on customer email" |
| **`drop_table`** | Remove tables | "Drop temporary table" |
| **`list_tables`** | Show all tables | "What tables exist?" |
| **`describe_table`** | Show table structure | "Describe customers table" |

**Security Features:**
- ✅ SQL injection prevention
- ✅ Query validation and sanitization
- ✅ Required WHERE clauses for updates
- ✅ Read-only mode support

## 📚 Documentation

For detailed information, see our comprehensive guides:

- **[Usage Examples](docs/USAGE_EXAMPLES.md)** - Practical examples and real-world scenarios
- **[Advanced Configuration](docs/ADVANCED_CONFIGURATION.md)** - Complex setups and enterprise deployment
- **[Best Practices](docs/BEST_PRACTICES.md)** - Performance optimization and security guidance
- **[Troubleshooting](docs/TROUBLESHOOTING.md)** - Common issues and solutions
- **[Platform Notes](docs/PLATFORM_NOTES.md)** - Windows, macOS, Linux, and Docker specifics

## � Credits and Attribution

### Original Work

This project is a **fork** of the [Azure-Samples/SQL-AI-samples](https://github.com/Azure-Samples/SQL-AI-samples) repository, specifically building upon the **MssqlMcp** implementation created by Microsoft and the Azure team.

- **Original Repository**: [Azure-Samples/SQL-AI-samples](https://github.com/Azure-Samples/SQL-AI-samples)
- **Original Authors**: Microsoft Corporation and the Azure team
- **Original License**: MIT License
- **Original Focus**: Azure SQL Database with Azure AD authentication

### Key Enhancement

This fork **extends the original Azure-only MCP server** to support **local SQL Server databases** with username/password authentication, making it accessible for:

- 🏠 **Local Development**: Connect to local SQL Server instances
- 🏢 **On-Premises Deployments**: Support enterprise SQL Server installations
- 🔄 **Hybrid Environments**: Seamlessly switch between local and Azure databases
- 🚀 **Broader Accessibility**: Remove Azure dependency for local development

### What We Added

| Feature | Original Repository | This Fork |
|---------|-------------------|-----------|
| **Azure SQL Database** | ✅ Azure AD only | ✅ Azure AD support |
| **Local SQL Server** | ❌ Not supported | ✅ Username/password auth |
| **Authentication Methods** | 1 (Azure AD) | 3 (Azure AD, SQL Server, Windows) |
| **Target Audience** | Azure users only | Local + Azure + On-premises |
| **Development Setup** | Requires Azure account | Works with local SQL Server |

### Acknowledgments

We are **deeply grateful** to Microsoft and the Azure team for creating the foundational architecture, security implementation, and tool design that made this enhanced version possible.

**This enhanced version would not exist without their excellent foundational work.**

---

> **License**: MIT License - see [LICENSE](LICENSE) file for details.