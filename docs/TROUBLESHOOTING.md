# Troubleshooting Guide

This guide helps you diagnose and resolve common issues with the MSSQL MCP Server.

## 🚨 Common Issues and Solutions

### Connection Problems

#### ❌ Error: "Login failed for user 'sa'"

**Diagnosis:**
1. Check SQL Server authentication mode
2. Verify user credentials
3. Confirm SQL Server is running

**Solutions:**
```bash
# Enable SQL Server authentication
sqlcmd -S localhost -E -Q "ALTER LOGIN sa ENABLE"
sqlcmd -S localhost -E -Q "ALTER LOGIN sa WITH PASSWORD = 'NewPassword123!'"

# Or use Windows authentication (remove SQL_USER/SQL_PASSWORD)
unset SQL_USER SQL_PASSWORD
```

#### ❌ Error: "Cannot connect to server"

**Diagnosis:**
```bash
netstat -an | grep 1433  # Check if SQL Server is listening
telnet localhost 1433    # Test connectivity
```

**Solutions:**
```bash
# Start SQL Server service
sudo systemctl start mssql-server  # Linux
net start MSSQLSERVER              # Windows

# Check firewall
sudo ufw allow 1433                # Linux
netsh advfirewall firewall add rule name="SQL Server" dir=in action=allow protocol=TCP localport=1433  # Windows
```

#### ❌ Error: "Certificate verification failed"

**Solutions:**
```bash
# For development environments
export TRUST_SERVER_CERTIFICATE=true

# For production (get proper certificate)
export TRUST_SERVER_CERTIFICATE=false
export ENCRYPT=true
```

### Azure SQL Database Issues

#### ❌ Error: "Azure AD authentication failed"

**Diagnosis:**
```bash
az account show  # Check if logged in to Azure
```

**Solutions:**
```bash
az login  # Login to Azure
az account set --subscription "Your-Subscription-ID"

# Verify database access
az sql db show --server myserver --name mydatabase --resource-group mygroup
```

#### ❌ Error: "Firewall blocked connection"

**Solution:**
```bash
# Add your IP to Azure SQL firewall
az sql server firewall-rule create \
  --server myserver \
  --resource-group mygroup \
  --name AllowMyIP \
  --start-ip-address YOUR_IP \
  --end-ip-address YOUR_IP
```

### MCP Server Issues

#### ❌ Error: "MCP server not found"

**Solutions:**
```bash
# Check if server is built
npm run build
ls -la dist/index.js

# Verify path in configuration
which node  # Use full path in config
```

#### ❌ Error: "Tools not available"

**Solutions:**
```bash
# Test MCP server directly
echo '{"jsonrpc": "2.0", "id": 1, "method": "tools/list"}' | node dist/index.js

# Check environment variables
env | grep -E "(SERVER_NAME|DATABASE_NAME|SQL_)"
```

## 🔍 Debugging Guide

### Enable Detailed Logging

```bash
# Add debug environment variable
export DEBUG=mcp:*
export NODE_ENV=development

# Run with verbose output
node dist/index.js 2>&1 | tee debug.log
```

### Test Connection Manually

Create a test file to verify your connection:

```javascript
// test-connection.js
const sql = require('mssql');

const config = {
  server: process.env.SERVER_NAME,
  database: process.env.DATABASE_NAME,
  user: process.env.SQL_USER,
  password: process.env.SQL_PASSWORD,
  options: {
    trustServerCertificate: true,
    encrypt: true
  }
};

sql.connect(config).then(() => {
  console.log('✅ Connection successful');
  return sql.query('SELECT @@VERSION');
}).then(result => {
  console.log('SQL Server version:', result.recordset[0]);
}).catch(err => {
  console.error('❌ Connection failed:', err.message);
});
```

### Validate Environment

Use this script to check your environment setup:

```bash
#!/bin/bash
# validate-env.sh

echo "🔍 Environment Validation"
echo "========================"

# Required variables
for var in SERVER_NAME DATABASE_NAME; do
  if [ -z "${!var}" ]; then
    echo "❌ Missing required variable: $var"
  else
    echo "✅ $var: ${!var}"
  fi
done

# Optional variables
for var in SQL_USER SQL_PASSWORD READONLY TRUST_SERVER_CERTIFICATE; do
  if [ -n "${!var}" ]; then
    echo "✅ $var: ${!var}"
  fi
done

# Test Node.js
node --version || echo "❌ Node.js not found"

# Test build
if [ -f "dist/index.js" ]; then
  echo "✅ Build found"
else
  echo "❌ Build missing - run 'npm run build'"
fi
```

## ❓ Frequently Asked Questions

### General Questions

**Q: What's the difference between this and the original Azure-Samples repository?**
A: This fork adds support for local SQL Server databases using username/password authentication, while the original only supports Azure SQL Database with Azure AD authentication.

**Q: Can I use this with SQL Server Express?**
A: Yes! This works perfectly with SQL Server Express. Use `localhost\SQLEXPRESS` as your SERVER_NAME.

**Q: Is this secure for production use?**
A: Yes, when properly configured. Use `READONLY=true` for production, enable encryption, and follow security best practices for credential management.

**Q: What SQL Server versions are supported?**
A: SQL Server 2016+ and Azure SQL Database. Older versions may work but are not officially tested.

### Authentication Questions

**Q: How do I know which authentication method is being used?**
A: If you provide `SQL_USER` and `SQL_PASSWORD`, it uses SQL Server authentication. Otherwise, it uses Azure AD authentication.

**Q: Can I use Windows Authentication?**
A: Yes, on Windows systems. Simply omit `SQL_USER` and `SQL_PASSWORD` environment variables.

**Q: How do I authenticate with Azure SQL Database?**
A: Run `az login` first, then omit `SQL_USER` and `SQL_PASSWORD` from your configuration.

### Configuration Questions

**Q: Where should I put my configuration files?**
A:
- **Claude Desktop**: `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS)
- **VS Code Agent**: `.vscode/mcp.json` in your workspace
- **Environment**: `.env` file or system environment variables

**Q: Can I connect to multiple databases?**
A: Currently, each MCP server instance connects to one database. You can configure multiple server instances for different databases.

**Q: What's the difference between READONLY=true and false?**
A: `READONLY=true` only allows SELECT, DESCRIBE, and LIST operations. `READONLY=false` allows all operations including INSERT, UPDATE, DELETE, and schema changes.

### Performance Questions

**Q: How do I optimize query performance?**
A:
- Create appropriate indexes
- Use specific WHERE clauses
- Avoid SELECT * on large tables
- Use the `describe_table` tool to understand table structure

**Q: Are there query size limits?**
A: Yes, queries are limited to prevent abuse. Very large result sets may be truncated.

**Q: Can I use stored procedures?**
A: No, stored procedure execution is blocked for security reasons. Use direct SQL queries instead.

### Error Resolution

**Q: Why am I getting "WHERE clause required" errors?**
A: For security, UPDATE operations require explicit WHERE clauses to prevent accidental mass updates.

**Q: What does "dangerous keyword detected" mean?**
A: The security system detected potentially harmful SQL keywords. Use the appropriate tool for your operation (e.g., `update_data` for updates).

**Q: How do I fix "connection timeout" errors?**
A: Increase `CONNECTION_TIMEOUT` environment variable or check network connectivity to your SQL Server.

## 🆘 Getting Help

### Self-Help Resources
1. **Check this troubleshooting guide** for common issues
2. **Review configuration examples** for your specific setup
3. **Test with minimal configuration** to isolate problems
4. **Check SQL Server logs** for detailed error information

### Community Support
- **GitHub Issues**: Report bugs and request features
- **Discussions**: Ask questions and share experiences
- **Documentation**: Comprehensive guides and examples

### Professional Support
For enterprise deployments or complex issues:
- Review the original [Azure-Samples repository](https://github.com/Azure-Samples/SQL-AI-samples)
- Consult SQL Server documentation
- Consider professional database administration services

---

**Need more help?** Check out our other documentation:
- [Usage Examples](USAGE_EXAMPLES.md) - Practical examples and patterns
- [Advanced Configuration](ADVANCED_CONFIGURATION.md) - Complex setup scenarios
- [Best Practices](BEST_PRACTICES.md) - Performance and security guidance
- [Platform Notes](PLATFORM_NOTES.md) - Platform-specific considerations
