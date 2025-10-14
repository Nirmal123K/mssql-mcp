# Advanced Configuration

This guide covers complex configuration scenarios and enterprise deployment considerations for the MSSQL MCP Server.

## 🏠 Local SQL Server Configurations

### Development Environment

```bash
# .env file for local development
SERVER_NAME=localhost
DATABASE_NAME=MyDevDatabase
SQL_USER=sa
SQL_PASSWORD=MyStrongPassword123!
TRUST_SERVER_CERTIFICATE=true
READONLY=false
CONNECTION_TIMEOUT=30
```

### Docker SQL Server

```bash
# For SQL Server running in Docker
SERVER_NAME=localhost,1433
DATABASE_NAME=TestDB
SQL_USER=sa
SQL_PASSWORD=YourStrong@Passw0rd
TRUST_SERVER_CERTIFICATE=true
READONLY=false
```

### Named Instance Configuration

```bash
# For SQL Server named instances
SERVER_NAME=localhost\\SQLEXPRESS
DATABASE_NAME=MyDatabase
SQL_USER=myuser
SQL_PASSWORD=mypassword
TRUST_SERVER_CERTIFICATE=true
READONLY=false
```

### Windows Authentication

```bash
# Use Windows Authentication (no SQL credentials needed)
SERVER_NAME=localhost
DATABASE_NAME=MyDatabase
TRUST_SERVER_CERTIFICATE=true
READONLY=false
# Note: SQL_USER and SQL_PASSWORD are omitted
```

## ☁️ Azure SQL Database Configurations

### Production Azure SQL

```bash
# Azure SQL Database with Azure AD
SERVER_NAME=myserver.database.windows.net
DATABASE_NAME=MyProdDatabase
READONLY=true  # Recommended for production
CONNECTION_TIMEOUT=60
ENCRYPT=true
```

### Azure SQL with Specific User

```bash
# Azure SQL with specific Azure AD user
SERVER_NAME=mycompany-sql.database.windows.net
DATABASE_NAME=CompanyDB
READONLY=false
CONNECTION_TIMEOUT=45
```

### Multi-Tenant Azure SQL

```bash
# For multi-tenant applications
SERVER_NAME=tenant1-sql.database.windows.net
DATABASE_NAME=Tenant1DB
READONLY=true
CONNECTION_TIMEOUT=30
```

## 🔐 Security Best Practices

### Environment Variable Management

**✅ Recommended:**
```bash
# Use environment files (not committed to git)
echo "SQL_PASSWORD=MySecretPassword" >> .env.local

# Use system environment variables
export SQL_PASSWORD="MySecretPassword"

# Use Azure Key Vault for production
az keyvault secret set --vault-name MyVault --name sql-password --value "MySecretPassword"
```

**❌ Avoid:**
```bash
# Never hardcode credentials in configuration files
# Never commit .env files with real credentials
# Never use weak passwords
```

### Connection Security

**Local SQL Server:**
```bash
# Enable encryption for local connections
ENCRYPT=true
TRUST_SERVER_CERTIFICATE=false  # Only true for development

# Use Windows Authentication when possible
# (Remove SQL_USER and SQL_PASSWORD to use Windows auth)
```

**Azure SQL Database:**
```bash
# Always use encryption (default for Azure SQL)
ENCRYPT=true
# Azure AD authentication is preferred over SQL authentication
```

### Production Security Hardening

```bash
# Production environment variables
READONLY=true                    # Restrict to read-only operations
CONNECTION_TIMEOUT=30           # Reasonable timeout
ENCRYPT=true                    # Always encrypt connections
TRUST_SERVER_CERTIFICATE=false # Use proper certificates
```

## 📁 Sample Configuration Files

### Claude Desktop Configuration

**Local SQL Server:**
```json
{
  "mcpServers": {
    "mssql": {
      "command": "node",
      "args": ["/path/to/mssql-mcp-server/dist/index.js"],
      "env": {
        "SERVER_NAME": "localhost",
        "DATABASE_NAME": "MyDatabase",
        "SQL_USER": "sa",
        "SQL_PASSWORD": "MyPassword123!",
        "TRUST_SERVER_CERTIFICATE": "true",
        "READONLY": "false"
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
        "SERVER_NAME": "myserver.database.windows.net",
        "DATABASE_NAME": "MyDatabase",
        "READONLY": "true"
      }
    }
  }
}
```

### VS Code Agent Configuration

**Local SQL Server (.vscode/mcp.json):**
```json
{
  "servers": {
    "mssql": {
      "type": "stdio",
      "command": "node",
      "args": ["/path/to/mssql-mcp-server/dist/index.js"],
      "env": {
        "SERVER_NAME": "localhost\\SQLEXPRESS",
        "DATABASE_NAME": "TestDB",
        "SQL_USER": "testuser",
        "SQL_PASSWORD": "testpass123",
        "TRUST_SERVER_CERTIFICATE": "true",
        "READONLY": "false"
      }
    }
  }
}
```

**Azure SQL Database (.vscode/mcp.json):**
```json
{
  "servers": {
    "mssql": {
      "type": "stdio",
      "command": "node",
      "args": ["/path/to/mssql-mcp-server/dist/index.js"],
      "env": {
        "SERVER_NAME": "company-sql.database.windows.net",
        "DATABASE_NAME": "ProductionDB",
        "READONLY": "true",
        "CONNECTION_TIMEOUT": "60"
      }
    }
  }
}
```

## 🔄 Dual Authentication Architecture

This MCP server automatically detects and uses the appropriate authentication method:

```typescript
// Authentication Decision Flow
if (SQL_USER && SQL_PASSWORD) {
  // Use SQL Server Authentication
  connectionConfig = {
    user: SQL_USER,
    password: SQL_PASSWORD,
    server: SERVER_NAME,
    database: DATABASE_NAME,
    authentication: {
      type: 'default'
    }
  };
} else {
  // Use Azure AD Authentication
  connectionConfig = {
    server: SERVER_NAME,
    database: DATABASE_NAME,
    authentication: {
      type: 'azure-active-directory-default'
    }
  };
}
```

**Authentication Priority:**
1. **SQL Server Auth**: If `SQL_USER` and `SQL_PASSWORD` are provided
2. **Azure AD Auth**: If no SQL credentials are provided
3. **Windows Auth**: If running on Windows without SQL credentials (local only)

## 🏢 Enterprise Deployment Scenarios

### High Availability Setup

```bash
# Always On Availability Group
SERVER_NAME=sql-cluster.company.com
DATABASE_NAME=ProductionDB
READONLY=true
CONNECTION_TIMEOUT=60
ENCRYPT=true
```

### Load Balancer Configuration

```bash
# Behind load balancer
SERVER_NAME=sql-lb.internal.company.com,1433
DATABASE_NAME=AppDB
CONNECTION_TIMEOUT=45
ENCRYPT=true
```

### Multi-Environment Setup

**Development:**
```bash
SERVER_NAME=dev-sql.company.com
DATABASE_NAME=DevDB
READONLY=false
```

**Staging:**
```bash
SERVER_NAME=staging-sql.company.com
DATABASE_NAME=StagingDB
READONLY=true
```

**Production:**
```bash
SERVER_NAME=prod-sql.company.com
DATABASE_NAME=ProdDB
READONLY=true
CONNECTION_TIMEOUT=30
```

## 🔧 Custom Connection Parameters

### Advanced Connection Options

```bash
# Custom connection parameters
SERVER_NAME=myserver.com
DATABASE_NAME=MyDB
CONNECTION_TIMEOUT=60
REQUEST_TIMEOUT=30
CANCEL_TIMEOUT=5
CONNECT_TIMEOUT=15
ENCRYPT=true
TRUST_SERVER_CERTIFICATE=false
```

### Connection Pool Settings

```bash
# Connection pooling (handled automatically)
# These are internal settings - no configuration needed
# Pool size: 10 connections
# Pool timeout: 30 seconds
# Idle timeout: 30 minutes
```

## 🌐 Network Configuration

### Firewall Rules

**Local SQL Server:**
```bash
# Windows Firewall
netsh advfirewall firewall add rule name="SQL Server" dir=in action=allow protocol=TCP localport=1433

# Linux UFW
sudo ufw allow 1433/tcp
```

**Azure SQL Database:**
```bash
# Add IP to Azure SQL firewall
az sql server firewall-rule create \
  --server myserver \
  --resource-group mygroup \
  --name AllowMyIP \
  --start-ip-address YOUR_IP \
  --end-ip-address YOUR_IP
```

### VPN/Private Network

```bash
# For VPN connections
SERVER_NAME=10.0.1.100  # Private IP
DATABASE_NAME=InternalDB
CONNECTION_TIMEOUT=60
```

## 📊 Monitoring and Logging

### Enable Debug Logging

```bash
# Environment variables for debugging
DEBUG=mcp:*
NODE_ENV=development
LOG_LEVEL=debug
```

### Connection Monitoring

```bash
# Monitor connection health
CONNECTION_TIMEOUT=30
REQUEST_TIMEOUT=15
# Automatic retry on connection failure
```

---

**Related Documentation:**
- [Usage Examples](USAGE_EXAMPLES.md) - Practical examples and patterns
- [Best Practices](BEST_PRACTICES.md) - Performance and security guidance
- [Troubleshooting](TROUBLESHOOTING.md) - Common issues and solutions
- [Platform Notes](PLATFORM_NOTES.md) - Platform-specific considerations
