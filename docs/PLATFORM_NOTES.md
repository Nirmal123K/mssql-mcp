# Platform-Specific Notes

This guide covers platform-specific installation steps, considerations, and troubleshooting for Windows, macOS, Linux, and Docker environments.

## 🪟 Windows

### Installation Considerations

**SQL Server Instances:**
```bash
# Use double backslashes for named instances
SERVER_NAME=localhost\\SQLEXPRESS
SERVER_NAME=MYCOMPUTER\\SQLSERVER2019
```

**Windows Authentication:**
```bash
# Windows Authentication is available when omitting SQL credentials
# Remove SQL_USER and SQL_PASSWORD to use Windows auth
SERVER_NAME=localhost
DATABASE_NAME=MyDatabase
TRUST_SERVER_CERTIFICATE=true
# SQL_USER and SQL_PASSWORD are omitted
```

**PowerShell Environment Setup:**
```powershell
# Set environment variables in PowerShell
$env:SERVER_NAME="localhost\SQLEXPRESS"
$env:DATABASE_NAME="MyDatabase"
$env:SQL_USER="sa"
$env:SQL_PASSWORD="MyPassword123!"
$env:TRUST_SERVER_CERTIFICATE="true"

# Or use PowerShell profile for persistence
Add-Content $PROFILE '$env:SERVER_NAME="localhost\SQLEXPRESS"'
```

### Windows-Specific Issues

**Firewall Configuration:**
```cmd
# Add firewall rule for SQL Server
netsh advfirewall firewall add rule name="SQL Server" dir=in action=allow protocol=TCP localport=1433

# For named instances, also allow SQL Browser
netsh advfirewall firewall add rule name="SQL Browser" dir=in action=allow protocol=UDP localport=1434
```

**Service Management:**
```cmd
# Start SQL Server service
net start MSSQLSERVER

# Start SQL Server Express
net start MSSQL$SQLEXPRESS

# Check service status
sc query MSSQLSERVER
```

**Path Considerations:**
```json
{
  "mcpServers": {
    "mssql": {
      "command": "C:\\Program Files\\nodejs\\node.exe",
      "args": ["C:\\path\\to\\mssql-mcp-server\\dist\\index.js"]
    }
  }
}
```

## 🍎 macOS

### Installation Steps

**Using Homebrew:**
```bash
# Install Node.js
brew install node

# Clone and setup project
git clone https://github.com/USERNAME/mssql-mcp-server.git
cd mssql-mcp-server
npm install
npm run build
```

**SQL Server via Docker:**
```bash
# Run SQL Server in Docker (recommended for macOS)
docker run -e "ACCEPT_EULA=Y" \
  -e "SA_PASSWORD=YourPassword123!" \
  -p 1433:1433 \
  --name sql-server \
  -d mcr.microsoft.com/mssql/server:2019-latest

# Connect to Docker SQL Server
export SERVER_NAME="localhost"
export DATABASE_NAME="master"
export SQL_USER="sa"
export SQL_PASSWORD="YourPassword123!"
export TRUST_SERVER_CERTIFICATE="true"
```

### macOS-Specific Considerations

**Environment Variables:**
```bash
# Add to ~/.zshrc or ~/.bash_profile
export SERVER_NAME="localhost"
export DATABASE_NAME="MyDatabase"
export SQL_USER="sa"
export SQL_PASSWORD="MyPassword123!"
export TRUST_SERVER_CERTIFICATE="true"

# Reload shell configuration
source ~/.zshrc
```

**Claude Desktop Configuration Path:**
```bash
# Claude Desktop config location on macOS
~/Library/Application Support/Claude/claude_desktop_config.json
```

**Firewall Settings:**
```bash
# macOS firewall typically doesn't block outbound connections
# If using Docker, ensure port 1433 is accessible
docker port sql-server
```

## 🐧 Linux

### Distribution-Specific Installation

**Ubuntu/Debian:**
```bash
# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install SQL Server (optional)
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/20.04/mssql-server-2019.list)"
sudo apt-get update
sudo apt-get install -y mssql-server
```

**CentOS/RHEL/Fedora:**
```bash
# Install Node.js
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo dnf install -y nodejs

# Install SQL Server (optional)
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo dnf install -y mssql-server
```

**Arch Linux:**
```bash
# Install Node.js
sudo pacman -S nodejs npm

# SQL Server via AUR or Docker (recommended)
yay -S mssql-server  # AUR package
```

### Linux-Specific Configuration

**Environment Variables:**
```bash
# Add to ~/.bashrc or ~/.zshrc
export SERVER_NAME="localhost"
export DATABASE_NAME="MyDatabase"
export SQL_USER="sa"
export SQL_PASSWORD="MyPassword123!"
export TRUST_SERVER_CERTIFICATE="true"

# Reload configuration
source ~/.bashrc
```

**Firewall Configuration:**
```bash
# UFW (Ubuntu)
sudo ufw allow 1433/tcp

# Firewalld (CentOS/RHEL/Fedora)
sudo firewall-cmd --permanent --add-port=1433/tcp
sudo firewall-cmd --reload

# iptables (manual)
sudo iptables -A INPUT -p tcp --dport 1433 -j ACCEPT
```

**Service Management:**
```bash
# Start SQL Server service
sudo systemctl start mssql-server
sudo systemctl enable mssql-server

# Check service status
sudo systemctl status mssql-server

# View logs
sudo journalctl -u mssql-server
```

**SQL Server via Docker:**
```bash
# Run SQL Server in Docker
docker run -e "ACCEPT_EULA=Y" \
  -e "SA_PASSWORD=YourPassword123!" \
  -p 1433:1433 \
  --name sql-server \
  --restart unless-stopped \
  -d mcr.microsoft.com/mssql/server:2019-latest

# Create systemd service for Docker container
sudo tee /etc/systemd/system/sql-server-docker.service > /dev/null <<EOF
[Unit]
Description=SQL Server Docker Container
Requires=docker.service
After=docker.service

[Service]
Restart=always
ExecStart=/usr/bin/docker start -a sql-server
ExecStop=/usr/bin/docker stop -t 2 sql-server

[Install]
WantedBy=default.target
EOF

sudo systemctl enable sql-server-docker
```

## 🐳 Docker Deployment

### Docker Compose Setup

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  sql-server:
    image: mcr.microsoft.com/mssql/server:2019-latest
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=YourPassword123!
      - MSSQL_PID=Express
    ports:
      - "1433:1433"
    volumes:
      - sql-data:/var/opt/mssql
    restart: unless-stopped

  mcp-server:
    build: .
    environment:
      - SERVER_NAME=sql-server
      - DATABASE_NAME=MyDatabase
      - SQL_USER=sa
      - SQL_PASSWORD=YourPassword123!
      - TRUST_SERVER_CERTIFICATE=true
    depends_on:
      - sql-server
    restart: unless-stopped

volumes:
  sql-data:
```

**Dockerfile for MCP Server:**
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY dist/ ./dist/

EXPOSE 3000

CMD ["node", "dist/index.js"]
```

### Container Networking

**Network Configuration:**
```bash
# Create custom network
docker network create mcp-network

# Run SQL Server with custom network
docker run -e "ACCEPT_EULA=Y" \
  -e "SA_PASSWORD=YourPassword123!" \
  -p 1433:1433 \
  --network mcp-network \
  --name sql-server \
  -d mcr.microsoft.com/mssql/server:2019-latest

# Use container name as server name
export SERVER_NAME="sql-server"
```

**Volume Mounting:**
```bash
# Mount configuration as volume
docker run -v $(pwd)/.env:/app/.env \
  -v $(pwd)/dist:/app/dist \
  --network mcp-network \
  node:18-alpine node /app/dist/index.js
```

## 🔧 Cross-Platform Considerations

### Path Separators

**Windows:**
```json
{
  "command": "C:\\Program Files\\nodejs\\node.exe",
  "args": ["C:\\path\\to\\project\\dist\\index.js"]
}
```

**Unix-like (macOS/Linux):**
```json
{
  "command": "/usr/bin/node",
  "args": ["/path/to/project/dist/index.js"]
}
```

### Environment Variable Syntax

**Windows (Command Prompt):**
```cmd
set SERVER_NAME=localhost
set DATABASE_NAME=MyDatabase
```

**Windows (PowerShell):**
```powershell
$env:SERVER_NAME="localhost"
$env:DATABASE_NAME="MyDatabase"
```

**Unix-like (Bash/Zsh):**
```bash
export SERVER_NAME="localhost"
export DATABASE_NAME="MyDatabase"
```

### Node.js Path Resolution

**Find Node.js Path:**
```bash
# Unix-like systems
which node

# Windows
where node
```

**Use Full Paths in Configuration:**
```json
{
  "command": "/usr/local/bin/node",  // macOS with Homebrew
  "command": "/usr/bin/node",        // Linux package manager
  "command": "C:\\Program Files\\nodejs\\node.exe"  // Windows
}
```

## 🚨 Common Platform Issues

### Windows Issues

**Named Instance Connection:**
```bash
# Ensure SQL Browser service is running
net start SQLBrowser

# Use correct instance name format
SERVER_NAME=localhost\\SQLEXPRESS
```

**Windows Authentication:**
```bash
# Run Node.js process with appropriate Windows user
# Ensure user has database access permissions
```

### macOS Issues

**Docker Desktop:**
```bash
# Ensure Docker Desktop is running
docker --version

# Check Docker daemon status
docker info
```

### Linux Issues

**Permission Denied:**
```bash
# Ensure user has permission to access SQL Server
sudo usermod -a -G mssql $USER

# Or run with appropriate permissions
sudo -u mssql node dist/index.js
```

**SELinux (CentOS/RHEL):**
```bash
# Check SELinux status
sestatus

# Allow network connections if needed
sudo setsebool -P httpd_can_network_connect 1
```

---

**Related Documentation:**
- [Advanced Configuration](ADVANCED_CONFIGURATION.md) - Complex setup scenarios
- [Best Practices](BEST_PRACTICES.md) - Performance and security guidance
- [Troubleshooting](TROUBLESHOOTING.md) - Common issues and solutions
- [Usage Examples](USAGE_EXAMPLES.md) - Practical examples and patterns
