# Project Structure

This document outlines the organization and structure of the MSSQL MCP Server project.

## 📁 Repository Layout

```
mssql-mcp-server/
├── 📄 README.md                 # Main project documentation
├── 📄 LICENSE                   # MIT License with proper attribution
├── 📄 CONTRIBUTING.md           # Contribution guidelines
├── 📄 CHANGELOG.md              # Version history and release notes
├── 📄 package.json              # Node.js package configuration
├── 📄 package-lock.json         # Dependency lock file
├── 📄 tsconfig.json             # TypeScript configuration
├── 📄 .gitignore                # Git ignore patterns
├── 📄 .env.example              # Environment configuration template
├── 📁 src/                      # Source code
│   ├── 📄 index.ts              # Main MCP server implementation
│   └── 📁 tools/                # Database operation tools
│       ├── 📄 ReadDataTool.ts   # SELECT queries with security validation
│       ├── 📄 InsertDataTool.ts # INSERT operations
│       ├── 📄 UpdateDataTool.ts # UPDATE operations with WHERE validation
│       ├── 📄 CreateTableTool.ts # Table creation
│       ├── 📄 CreateIndexTool.ts # Index management
│       ├── 📄 DropTableTool.ts  # Table deletion
│       ├── 📄 ListTableTool.ts  # Schema discovery
│       └── 📄 DescribeTableTool.ts # Table structure inspection
├── 📁 dist/                     # Compiled JavaScript output (generated)
├── 📁 docs/                     # Additional documentation
│   └── 📄 PROJECT_STRUCTURE.md  # This file
└── 📁 node_modules/             # Dependencies (generated)
```

## 🏗️ Architecture Overview

### Core Components

#### `src/index.ts`
- **Main MCP Server**: Entry point and server implementation
- **Dual Authentication**: Logic for SQL Server vs Azure AD authentication
- **Connection Management**: Database connection pooling and token handling
- **Tool Registration**: Registration of all 8 database operation tools
- **Error Handling**: Centralized error management and logging

#### `src/tools/`
Database operation tools implementing the MCP Tool interface:

| Tool | Purpose | Security Level | Operations |
|------|---------|----------------|------------|
| **ReadDataTool** | Execute SELECT queries | 🔒🔒🔒 High | Read data with injection prevention |
| **InsertDataTool** | Add new records | 🔒🔒 Medium | Single/bulk inserts with validation |
| **UpdateDataTool** | Modify existing data | 🔒🔒🔒 High | Updates with mandatory WHERE clauses |
| **CreateTableTool** | Create new tables | 🔒🔒 Medium | Table creation with column definitions |
| **CreateIndexTool** | Optimize performance | 🔒🔒 Medium | Index creation and management |
| **DropTableTool** | Remove tables | 🔒🔒🔒 High | Safe table deletion with validation |
| **ListTableTool** | Browse schema | 🔒 Low | List tables and metadata |
| **DescribeTableTool** | Inspect structure | 🔒 Low | Table schema and column details |

## 🔧 Configuration Files

### `package.json`
- **Metadata**: Project name, description, keywords for npm
- **Scripts**: Build, development, and utility commands
- **Dependencies**: Runtime and development dependencies
- **Publishing**: Configuration for npm distribution

### `tsconfig.json`
- **Target**: ES2020 for modern JavaScript features
- **Module**: ES2020 modules for compatibility
- **Strict Mode**: Enabled for type safety
- **Output**: Compiled to `dist/` directory with source maps

### `.env.example`
- **Template**: Example environment configuration
- **Documentation**: Inline comments explaining each variable
- **Examples**: Multiple configuration scenarios
- **Security**: Guidelines for credential management

## 📚 Documentation Structure

### Primary Documentation
- **README.md**: Complete user guide and reference
- **CONTRIBUTING.md**: Developer contribution guidelines
- **LICENSE**: MIT License with proper attribution
- **CHANGELOG.md**: Version history and release notes

### Additional Documentation
- **docs/PROJECT_STRUCTURE.md**: This architectural overview
- **Inline Comments**: Comprehensive code documentation
- **JSDoc**: TypeScript documentation for APIs

## 🔄 Build Process

### Development Workflow
```bash
npm install          # Install dependencies
npm run build        # Compile TypeScript to JavaScript
npm run watch        # Watch mode for development
npm run dev          # Build and start server
npm run clean        # Clean build artifacts
```

### Output Structure
```
dist/
├── index.js         # Compiled main server
├── index.d.ts       # TypeScript declarations
├── index.js.map     # Source map for debugging
└── tools/           # Compiled tool implementations
    ├── ReadDataTool.js
    ├── InsertDataTool.js
    └── ... (other tools)
```

## 🛡️ Security Architecture

### Input Validation
- **SQL Injection Prevention**: Multi-layer protection in ReadDataTool
- **Query Validation**: Syntax and semantic analysis
- **Parameter Binding**: Automatic parameterization for all queries
- **Keyword Filtering**: Dangerous SQL keyword detection

### Authentication Flow
```
Environment Check
├── SQL_USER + SQL_PASSWORD present?
│   ├── Yes → SQL Server Authentication
│   └── No → Azure AD Authentication
└── Connection established with appropriate method
```

### Access Control
- **Read-Only Mode**: Environment variable to restrict operations
- **Tool-Level Security**: Each tool implements appropriate validation
- **Connection Pooling**: Secure token management and expiration

## 🤝 Contribution Guidelines

### Code Organization
- **Single Responsibility**: Each tool has one clear purpose
- **Consistent Patterns**: All tools follow the same interface
- **Error Handling**: Comprehensive error management
- **Type Safety**: Full TypeScript coverage

### File Naming Conventions
- **PascalCase**: Class files (e.g., `ReadDataTool.ts`)
- **camelCase**: Function and variable names
- **UPPER_CASE**: Constants and environment variables
- **kebab-case**: Configuration files (e.g., `package.json`)

### Development Standards
- **TypeScript**: Strict mode with comprehensive types
- **ES2020**: Modern JavaScript features
- **Modular Design**: Clear separation of concerns
- **Documentation**: Inline comments and JSDoc

## 🚀 Deployment Considerations

### Package Distribution
- **npm Registry**: Configured for npm publication
- **Binary**: Executable via `mssql-mcp-server` command
- **Files**: Only essential files included in package
- **Dependencies**: Minimal runtime dependencies

### Environment Support
- **Node.js**: 16+ compatibility
- **Platforms**: Windows, macOS, Linux
- **Databases**: SQL Server 2016+, Azure SQL Database
- **AI Assistants**: Claude Desktop, VS Code Agent, MCP-compatible clients

This structure ensures maintainability, security, and ease of contribution while providing a professional foundation for the open-source project.
