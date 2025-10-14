# Contributing to MSSQL MCP Server

Thank you for your interest in contributing to the MSSQL MCP Server! This project extends the original Azure-Samples MCP server to support local SQL Server databases, and we welcome contributions that improve functionality, documentation, and user experience.

## 🚀 Quick Start for Contributors

### Prerequisites

- **Node.js 18+** (recommended) or Node.js 16+
- **Git** for version control
- **SQL Server** (local instance) or **Azure SQL Database** for testing
- **TypeScript** knowledge (project is written in TypeScript)

### Development Setup

1. **Fork and Clone**
   ```bash
   git clone https://github.com/USERNAME/mssql-mcp-server.git
   cd mssql-mcp-server
   ```

2. **Install Dependencies**
   ```bash
   npm install
   ```

3. **Set Up Environment**
   ```bash
   # Copy example environment file
   cp .env.example .env
   
   # Edit .env with your database credentials
   # For local SQL Server:
   SERVER_NAME=localhost
   DATABASE_NAME=TestDB
   SQL_USER=sa
   SQL_PASSWORD=YourPassword123!
   TRUST_SERVER_CERTIFICATE=true
   ```

4. **Build the Project**
   ```bash
   npm run build
   ```

5. **Test Your Setup**
   ```bash
   # Test the MCP server
   echo '{"jsonrpc": "2.0", "id": 1, "method": "tools/list"}' | node dist/index.js
   ```

## 🛠️ Development Workflow

### Code Standards

- **Language**: TypeScript with strict mode enabled
- **Style**: Follow existing code formatting (we use Prettier)
- **Linting**: ESLint configuration provided
- **Naming**: Use descriptive names, camelCase for variables/functions, PascalCase for classes

### Project Structure

```
src/
├── index.ts              # Main MCP server implementation
├── tools/               # Database operation tools
│   ├── ReadDataTool.ts   # SELECT queries with security validation
│   ├── InsertDataTool.ts # INSERT operations
│   ├── UpdateDataTool.ts # UPDATE operations with WHERE clause validation
│   ├── CreateTableTool.ts # Table creation
│   ├── CreateIndexTool.ts # Index management
│   ├── DropTableTool.ts  # Table deletion
│   ├── ListTableTool.ts  # Schema discovery
│   └── DescribeTableTool.ts # Table structure inspection
└── types/               # TypeScript type definitions
```

### Making Changes

1. **Create a Feature Branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make Your Changes**
   - Follow existing code patterns
   - Add appropriate error handling
   - Include security considerations
   - Update documentation if needed

3. **Test Your Changes**
   ```bash
   # Build and test
   npm run build
   npm test  # If tests exist
   
   # Manual testing
   node dist/index.js
   ```

4. **Commit Your Changes**
   ```bash
   git add .
   git commit -m "feat: add support for connection pooling"
   ```

## 📝 Contribution Types

### 🐛 Bug Fixes

- **Security Issues**: Report privately via email first
- **Connection Problems**: Include environment details and error messages
- **Tool Malfunctions**: Provide SQL queries that cause issues
- **Documentation Errors**: Fix typos, outdated information, or unclear instructions

### ✨ Feature Enhancements

- **New Database Tools**: Additional MCP tools for database operations
- **Authentication Methods**: Support for additional authentication types
- **Performance Improvements**: Query optimization, connection pooling enhancements
- **Developer Experience**: Better error messages, debugging tools, logging

### 📚 Documentation

- **README Improvements**: Clearer instructions, better examples
- **Code Comments**: Explain complex logic, security considerations
- **Troubleshooting**: Add solutions for common issues
- **Examples**: Real-world usage scenarios

### 🧪 Testing

- **Unit Tests**: Test individual tools and functions
- **Integration Tests**: Test with real databases
- **Security Tests**: Validate SQL injection prevention
- **Performance Tests**: Benchmark query execution

## 🔒 Security Guidelines

### Security-First Development

- **SQL Injection Prevention**: All user input must be validated and parameterized
- **Query Validation**: Implement comprehensive checks for dangerous patterns
- **Error Handling**: Don't expose sensitive information in error messages
- **Authentication**: Secure credential handling and storage

### Security Review Process

1. **Self-Review**: Check your code for security vulnerabilities
2. **Automated Checks**: Ensure all security validations pass
3. **Peer Review**: Security-sensitive changes require additional review
4. **Testing**: Verify security measures work as expected

## 📋 Pull Request Process

### Before Submitting

- [ ] Code builds successfully (`npm run build`)
- [ ] All existing functionality still works
- [ ] New features include appropriate documentation
- [ ] Security considerations addressed
- [ ] Commit messages follow conventional format

### PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Performance improvement
- [ ] Security enhancement

## Testing
- [ ] Tested with local SQL Server
- [ ] Tested with Azure SQL Database
- [ ] Manual testing completed
- [ ] No breaking changes

## Security Checklist
- [ ] Input validation implemented
- [ ] SQL injection prevention verified
- [ ] Error handling doesn't expose sensitive data
- [ ] Authentication/authorization considered
```

### Review Process

1. **Automated Checks**: CI/CD pipeline runs automatically
2. **Code Review**: Maintainers review for quality and security
3. **Testing**: Changes tested in multiple environments
4. **Approval**: At least one maintainer approval required
5. **Merge**: Squash and merge to main branch

## 🐛 Issue Reporting

### Bug Reports

Use this template for bug reports:

```markdown
**Bug Description**
Clear description of the issue

**Environment**
- OS: [Windows/macOS/Linux]
- Node.js version: [e.g., 18.17.0]
- SQL Server version: [e.g., SQL Server 2019, Azure SQL Database]
- Authentication method: [SQL Server/Azure AD/Windows]

**Steps to Reproduce**
1. Configure environment with...
2. Run command...
3. Observe error...

**Expected Behavior**
What should happen

**Actual Behavior**
What actually happens

**Error Messages**
```
Include full error messages and stack traces
```

**Configuration**
```json
{
  "SERVER_NAME": "localhost",
  "DATABASE_NAME": "TestDB",
  "READONLY": "false"
}
```
```

### Feature Requests

```markdown
**Feature Description**
Clear description of the proposed feature

**Use Case**
Why is this feature needed? What problem does it solve?

**Proposed Solution**
How should this feature work?

**Alternatives Considered**
Other approaches you've considered

**Additional Context**
Any other relevant information
```

## 🤝 Community Guidelines

### Code of Conduct

- **Be Respectful**: Treat all contributors with respect and kindness
- **Be Inclusive**: Welcome contributors of all backgrounds and skill levels
- **Be Constructive**: Provide helpful feedback and suggestions
- **Be Patient**: Remember that everyone is learning and contributing their time

### Communication

- **GitHub Issues**: For bug reports and feature requests
- **GitHub Discussions**: For questions and general discussion
- **Pull Requests**: For code contributions and reviews
- **Email**: For security issues or private matters

### Recognition

Contributors are recognized through:
- **GitHub Contributors**: Automatic recognition in repository
- **Release Notes**: Major contributions highlighted in releases
- **Documentation**: Contributors credited in relevant sections

## 📚 Resources

### Learning Resources

- **TypeScript**: [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- **Node.js**: [Node.js Documentation](https://nodejs.org/docs/)
- **SQL Server**: [SQL Server Documentation](https://docs.microsoft.com/sql/)
- **MCP Protocol**: [Model Context Protocol](https://modelcontextprotocol.io/)

### Development Tools

- **VS Code**: Recommended editor with TypeScript support
- **SQL Server Management Studio**: For database management
- **Azure Data Studio**: Cross-platform database tool
- **Postman**: For testing MCP server responses

## 🚀 Getting Started

Ready to contribute? Here's how to get started:

1. **Browse Issues**: Look for issues labeled `good first issue` or `help wanted`
2. **Join Discussions**: Participate in GitHub Discussions
3. **Read the Code**: Familiarize yourself with the codebase
4. **Start Small**: Begin with documentation or small bug fixes
5. **Ask Questions**: Don't hesitate to ask for help or clarification

Thank you for contributing to the MSSQL MCP Server! Your contributions help make database interaction more accessible for developers worldwide.
