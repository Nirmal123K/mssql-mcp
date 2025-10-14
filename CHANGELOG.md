# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Initial release preparation
- Comprehensive documentation
- Community contribution guidelines

## [1.0.0] - 2024-12-XX

### Added
- **Dual Authentication System**: Support for both local SQL Server (username/password) and Azure SQL Database (Azure AD) authentication
- **8 Comprehensive MCP Tools**:
  - `read_data`: Secure SELECT queries with SQL injection prevention
  - `insert_data`: Single and bulk record insertion
  - `update_data`: Data updates with mandatory WHERE clauses
  - `create_table`: Table creation with full column specification
  - `create_index`: Index creation for performance optimization
  - `drop_table`: Safe table deletion with validation
  - `list_tables`: Schema discovery and table listing
  - `describe_table`: Detailed table structure inspection
- **Enterprise Security Features**:
  - Advanced SQL injection prevention with pattern detection
  - Parameterized query execution
  - Dangerous keyword filtering
  - Query validation and sanitization
  - Read-only mode support
- **Comprehensive Documentation**:
  - Professional README with clear value proposition
  - Step-by-step installation instructions
  - Configuration examples for multiple environments
  - Troubleshooting guide and FAQ
  - Usage examples and integration patterns
- **Developer Experience**:
  - TypeScript implementation with strict mode
  - Environment variable configuration
  - Connection pooling and token management
  - Detailed error messages and logging
- **Community Support**:
  - Contribution guidelines (CONTRIBUTING.md)
  - Issue and pull request templates
  - Code of conduct and community standards
  - Professional package.json for npm distribution

### Enhanced from Original
- **Extended Authentication**: Added local SQL Server support to original Azure-only implementation
- **Broader Compatibility**: Support for SQL Server Express, Docker containers, and on-premises deployments
- **Improved Documentation**: Comprehensive guides for all supported environments
- **Enhanced Security**: Additional validation layers and security features
- **Better Developer Experience**: Clearer setup instructions and troubleshooting guides

### Technical Details
- **Node.js**: 16+ support with ES2020 modules
- **TypeScript**: Strict mode with comprehensive type definitions
- **Dependencies**: 
  - `@azure/identity`: ^4.8.0 for Azure AD authentication
  - `@modelcontextprotocol/sdk`: ^1.0.0 for MCP framework
  - `mssql`: ^11.0.1 for SQL Server connectivity
  - `dotenv`: ^10.0.0 for environment configuration
- **Build System**: TypeScript compiler with source maps and declarations
- **Security**: Comprehensive SQL injection prevention and query validation

### Attribution
This project is a fork of [Azure-Samples/SQL-AI-samples](https://github.com/Azure-Samples/SQL-AI-samples), specifically enhancing the MssqlMcp implementation to support local SQL Server databases while maintaining full compatibility with Azure SQL Database.

### Breaking Changes
None - this is the initial release of the enhanced fork.

### Migration Guide
For users of the original Azure-Samples implementation:
1. Update package name from `mssql-mcp` to `mssql-mcp-server`
2. Add `SQL_USER` and `SQL_PASSWORD` environment variables for local SQL Server
3. All existing Azure SQL Database configurations continue to work unchanged

### Known Issues
- Stored procedure execution is intentionally disabled for security
- Very large result sets may be truncated
- Windows Authentication requires running on Windows systems

### Future Roadmap
- [ ] Unit and integration test suite
- [ ] Performance benchmarking and optimization
- [ ] Additional database operation tools
- [ ] Enhanced logging and monitoring
- [ ] Docker container support
- [ ] CI/CD pipeline implementation

---

## Release Notes Format

### Added
- New features and capabilities

### Changed
- Changes in existing functionality

### Deprecated
- Soon-to-be removed features

### Removed
- Now removed features

### Fixed
- Bug fixes

### Security
- Security improvements and vulnerability fixes

---

For more information about releases, see the [GitHub Releases](https://github.com/USERNAME/mssql-mcp-server/releases) page.
