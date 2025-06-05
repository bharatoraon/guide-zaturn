# Zaturn Documentation

[![License](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/version-1.0.0-green.svg)](https://github.com/yourusername/zaturn/releases)
[![Documentation](https://img.shields.io/badge/docs-latest-brightgreen.svg)](https://zaturn.ai/docs)

## Table of Contents
- [Introduction](#introduction)
- [Features](#features)
- [Installation](#installation)
  - [Prerequisites](#prerequisites)
  - [MacOS](#macos)
  - [Windows](#windows)
  - [Linux](#linux)
  - [Docker](#docker)
  - [Verification](#verification)
- [Configuration](#configuration)
  - [MCP Server Setup](#mcp-server-setup)
  - [Data Sources](#data-sources)
  - [Environment Variables](#environment-variables)
  - [Configuration File](#configuration-file)
- [Usage](#usage)
  - [Basic Commands](#basic-commands)
  - [Querying Data](#querying-data)
  - [Batch Processing](#batch-processing)
  - [Real-time Analysis](#real-time-analysis)
- [API Reference](#api-reference)
  - [Endpoints](#endpoints)
  - [Authentication](#authentication)
  - [Rate Limiting](#rate-limiting)
- [Troubleshooting](#troubleshooting)
  - [Common Issues](#common-issues)
  - [Debugging](#debugging)
  - [Getting Help](#getting-help)
- [Contributing](#contributing)
- [License](#license)
- [Support](#support)

## Introduction

Zaturn is a next-generation AI-powered data analytics platform that transforms how you interact with your data. By leveraging natural language processing and machine learning, Zaturn allows users to extract meaningful insights from various data sources using simple, intuitive queries.

### Features

- **Natural Language Processing**: Query your data using plain English
- **Multi-source Integration**: Connect to various data sources (CSV, JSON, SQL, NoSQL)
- **Real-time Analytics**: Get instant insights with streaming data support
- **Advanced Visualization**: Built-in tools for data exploration
- **Scalable Architecture**: Handles small to enterprise-level datasets
- **Secure**: Enterprise-grade security and access controls
- **Extensible**: Plugin system for custom functionality

## Installation

### Prerequisites

Before installing Zaturn, ensure your system meets these requirements:

- **Operating System**: macOS 10.15+, Windows 10/11, or Linux (x86_64)
- **Memory**: Minimum 8GB RAM (16GB recommended for large datasets)
- **Storage**: 2GB free disk space (more may be required for large datasets)
- **Dependencies**:
  - Python 3.8+
  - Node.js 16+ (for web interface)
  - Docker (optional, for containerized deployment)

### macOS

#### Using Homebrew (Recommended)

```bash
# Install Homebrew if you don't have it
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Zaturn
brew install zaturn/tap/zaturn

# Verify installation
zaturn --version
```

#### Manual Installation

```bash
# Install Python dependencies
pip3 install --upgrade pip
pip3 install zaturn

# Install system dependencies (if not already installed)
brew install libpq openssl

# Set up environment variables
export LDFLAGS="-L/usr/local/opt/openssl@3/lib"
export CPPFLAGS="-I/usr/local/opt/openssl@3/include"
```

### Windows

#### Using Chocolatey (Recommended)

```powershell
# Install Chocolatey if you don't have it
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

# Install Zaturn
choco install zaturn

# Add Zaturn to PATH (if not automatically added)
$env:Path += ";C:\Program Files\Zaturn"
```

#### Manual Installation

1. Download the latest Windows installer from [releases page](https://github.com/zaturn/zaturn/releases)
2. Run the installer and follow the on-screen instructions
3. Open a new Command Prompt and verify installation:
   ```
   zaturn --version
   ```

### Linux

#### Debian/Ubuntu

```bash
# Add the Zaturn repository
curl -s https://packages.zaturn.ai/gpg.key | sudo apt-key add -
echo "deb [arch=amd64] https://packages.zaturn.ai/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/zaturn.list

# Install Zaturn
sudo apt update
sudo apt install zaturn
```

#### RHEL/CentOS

```bash
# Add the Zaturn repository
sudo tee /etc/yum.repos.d/zaturn.repo <<EOF
[zaturn]
name=Zaturn Repository
baseurl=https://packages.zaturn.ai/rpm
enabled=1
gpgcheck=1
gpgkey=https://packages.zaturn.ai/gpg.key
EOF

# Install Zaturn
sudo yum install zaturn
```

### Docker

Zaturn is available as a Docker container for easy deployment:

```bash
# Pull the latest image
docker pull zaturn/zaturn:latest

# Run in development mode
docker run -p 8000:8000 -v $(pwd)/data:/data zaturn/zaturn

# Production deployment with persistent storage
docker run -d \
  --name zaturn \
  -p 8000:8000 \
  -v zaturn_data:/data \
  -e DATABASE_URL=postgresql://user:password@db:5432/zaturn \
  zaturn/zaturn:latest
```

### Verification

After installation, verify that Zaturn is working correctly:

```bash
# Check version
zaturn --version

# Display help
zaturn --help

# Run a test query
zaturn analyze "What's the current status?"
```

## Configuration

### MCP Server Setup

The MCP (Model Control Protocol) server is the backbone of Zaturn's analytics engine.

#### Basic Configuration

Create a configuration file at `~/.zaturn/config.yml`:

```yaml
mcp:
  host: 0.0.0.0
  port: 8080
  workers: 4
  log_level: info
  cache:
    enabled: true
    ttl: 3600
    max_size: 1GB
```

#### Advanced Configuration

```yaml
mcp:
  # Server settings
  host: 0.0.0.0
  port: 8080
  workers: 4
  log_level: debug
  
  # Authentication
  auth:
    enabled: true
    jwt_secret: your-secret-key-here
    token_expiry: 24h
  
  # Caching
  cache:
    enabled: true
    provider: redis  # or 'memory', 'memcached'
    ttl: 3600
    max_size: 1GB
    
  # Rate limiting
  rate_limit:
    enabled: true
    requests: 100
    window: 60  # seconds
    
  # Monitoring
  metrics:
    enabled: true
    port: 9090
    endpoint: /metrics
```

### Data Sources

Zaturn supports multiple data source types. Configure them in `~/.zaturn/sources.yml`:

```yaml
# SQL Database
- name: production_db
  type: postgresql
  connection:
    host: localhost
    port: 5432
    database: mydb
    username: user
    password: password
  
# CSV File
- name: sales_data
  type: csv
  path: /data/sales/2023.csv
  options:
    delimiter: ','
    has_header: true

# JSON API
- name: weather_api
  type: json
  endpoint: https://api.weather.com/v1/forecast
  auth:
    type: api_key
    key: your-api-key
    header: X-API-Key
```

### Environment Variables

Zaturn can be configured using environment variables:

```bash
# Core Settings
export ZATURN_ENV=production
export ZATURN_LOG_LEVEL=info

# Database
export DATABASE_URL=postgresql://user:password@localhost:5432/zaturn

# Cache
export REDIS_URL=redis://localhost:6379/0

# Authentication
export JWT_SECRET=your-secret-key
export TOKEN_EXPIRY=24h

# API Server
export PORT=8000
export HOST=0.0.0.0
```

## Usage

### Basic Commands

```bash
# Get help
zaturn --help

# View version
zaturn --version

# Start the server
zaturn server start

# Stop the server
zaturn server stop

# Check status
zaturn server status
```

### Querying Data

#### Basic Query

```bash
zaturn analyze "Show me total sales by region for Q1 2023"
```

#### Advanced Query with Options

```bash
zaturn analyze \
  "Analyze sales trends" \
  --source production_db \
  --output json \
  --timeout 30 \
  --limit 1000
```

#### Save Results to File

```bash
zaturn analyze "Generate monthly report" --output report.csv
```

### Batch Processing

Process multiple queries from a file:

```bash
# Create a queries file
echo "Show top 10 products by revenue" > queries.txt
echo "Analyze customer demographics" >> queries.txt

# Process in parallel
zaturn batch queries.txt --parallel 4 --output-dir reports
```

### Real-time Analysis

#### Stream Data

```bash
# Stream live data
zaturn stream "monitor server metrics" --interval 5s

# Follow a log file
zaturn tail /var/log/application.log --query "find errors"
```

## API Reference

### Endpoints

#### Analyze

```http
POST /api/v1/analyze
Content-Type: application/json
Authorization: Bearer YOUR_TOKEN

{
  "query": "Show me total sales by region",
  "source": "production_db",
  "format": "json"
}
```

#### Batch Process

```http
POST /api/v1/batch
Content-Type: application/json
Authorization: Bearer YOUR_TOKEN

{
  "queries": [
    "Query 1",
    "Query 2"
  ],
  "options": {
    "parallel": 4,
    "format": "csv"
  }
}
```

### Authentication

Zaturn uses JWT for authentication:

1. Get an access token:
   ```http
   POST /api/v1/auth/login
   Content-Type: application/json
   
   {
     "username": "admin",
     "password": "yourpassword"
   }
   ```

2. Use the token in subsequent requests:
   ```
   Authorization: Bearer YOUR_TOKEN
   ```

### Rate Limiting

- 100 requests per minute per IP for unauthenticated users
- 1000 requests per minute for authenticated users
- 10000 requests per minute for admin users

## Troubleshooting

### Common Issues

#### Connection Refused

```bash
# Check if the server is running
zaturn server status

# Check logs
journalctl -u zaturn -f
```

#### Performance Issues

```bash
# Check system resources
top

# Increase memory limit
export ZATURN_MEMORY_LIMIT=8G
zaturn analyze "query"
```

### Debugging

```bash
# Enable debug logging
export ZATURN_LOG_LEVEL=debug
zaturn analyze "query"

# Generate debug report
zaturn debug --report
```

### Getting Help

- [Documentation](https://docs.zaturn.ai)
- [GitHub Issues](https://github.com/zaturn/zaturn/issues)
- Community [Discord](https://discord.gg/zaturn)
- Email: support@zaturn.ai

## Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details.

## License

Zaturn is licensed under the [MIT License](LICENSE).

## Support

For enterprise support, contact sales@zaturn.ai

### Windows
```powershell
# Install Zaturn
uv tool install zaturn

# Note: Run PowerShell as Administrator
```

### Requirements
- Python 3.8 or higher
- uv tool (Universal Version Manager)
- At least 4GB RAM
- Internet connection for initial installation

## Configuration

### MCP Server
Configure the MCP server by creating a configuration file:
```json
{
    "mcpServers": {
        "zaturn": {
            "command": "zaturn_mcp",
            "args": []
        }
    }
}
```

Advanced configuration:
```json
{
    "mcpServers": {
        "zaturn": {
            "command": "zaturn_mcp",
            "args": [
                "--port=8080",
                "--log-level=debug"
            ],
            "env": {
                "ZATURN_API_KEY": "your-api-key"
            }
        }
    }
}
```

### Data Sources
Configure your data sources by creating a `sources.txt` file:
```bash
# Mac
echo "/path/to/your/data.csv" > ~/.local/share/zaturn/sources.txt

# Windows
echo "C:\path\to\your\data.csv" > %APPDATA%\zaturn\sources.txt
```

Each line in `sources.txt` represents a different data source.

### Environment Variables
- `ZATURN_API_KEY`: Your API key for authentication
- `ZATURN_LOG_LEVEL`: Logging verbosity (debug, info, warn, error)
- `ZATURN_CACHE_DIR`: Directory for caching data

## Usage

### Basic Commands
```bash
# Start Zaturn
zaturn

# Run analysis
zaturn analyze "What's the trend in sales?"

# List available commands
zaturn help
```

### Advanced Features

#### Real-time Analysis
```bash
zaturn analyze --stream "Show me live sales updates"
```

#### Batch Processing
```bash
zaturn batch queries.txt
```

### Troubleshooting
Common issues and solutions:
- Connection errors: Check your MCP server configuration
- Permission errors: Run with appropriate privileges
- Memory issues: Increase system resources

## API Reference

### Commands
- `analyze`: Analyze data with natural language
- `batch`: Process multiple queries
- `help`: Show command help
- `version`: Show version information

### Parameters
- `--stream`: Enable streaming output
- `--verbose`: Increase verbosity
- `--output`: Specify output format
- `--config`: Specify configuration file

### Examples
```bash
# Basic analysis
zaturn analyze "What's the average sales?"

# Advanced analysis with parameters
zaturn analyze --stream "Show me real-time sales updates" --output json
```

## FAQ

### Installation & Setup

#### How do I update Zaturn?
```bash
uv tool update zaturn
```

#### Can I use multiple data sources?
Yes, add each source on a new line in your `sources.txt` file.

#### What data formats are supported?
Currently supports:
- CSV files
- JSON files
- SQLite databases
- Excel files (.xlsx)
More formats coming soon.

#### How do I check the version?
```bash
zaturn version
```

### Configuration

#### How do I change the default configuration?
Create or modify `~/.zaturn/config.json`:
```json
{
    "mcpServers": {
        "zaturn": {
            "command": "zaturn_mcp",
            "args": [
                "--port=8080",
                "--log-level=debug"
            ]
        }
    },
    "dataSources": {
        "default": {
            "path": "/path/to/data.csv",
            "type": "csv",
            "encoding": "utf-8"
        }
    }
}
```

#### Can I override configuration per command?
Yes, use the `--config` parameter:
```bash
zaturn analyze "query" --config custom-config.json
```

### Performance

#### How can I improve analysis speed?
1. Use streaming output:
   ```bash
   zaturn analyze "query" --stream
   ```
2. Process data in batches:
   ```bash
   zaturn analyze "query" --limit 1000
   ```
3. Increase memory limit:
   ```bash
   export ZATURN_MAX_MEMORY=8192
   ```

#### What's the maximum data size I can analyze?
Depends on your system resources:
- Default: ~4GB
- Maximum: ~16GB (with increased memory limit)

### Troubleshooting

#### Common Error Messages

- "Unable to connect to MCP server"
  - Check if MCP server is running
  - Verify configuration file
  - Test connection

- "Permission denied"
  - Run with sudo (Mac/Linux)
  - Run as Administrator (Windows)
  - Check file permissions

- "Out of memory"
  - Increase memory limit
  - Process data in batches
  - Use streaming output

#### How do I debug issues?
1. Enable verbose logging:
   ```bash
   export ZATURN_LOG_LEVEL=debug
   zaturn analyze "query" --verbose 5
   ```

2. Check logs:
   ```bash
   cat ~/.zaturn/logs/zaturn.log
   ```

3. Run in debug mode:
   ```bash
   zaturn analyze "query" --debug
   ```

4. Validate configuration:
   ```bash
   zaturn validate-config
   ```

### Best Practices

#### Data Organization
1. Keep data files organized
2. Use descriptive filenames
3. Document data sources
4. Regularly validate data

#### Performance Optimization
1. Process data in batches
2. Use appropriate output formats
3. Optimize queries
4. Monitor resource usage

#### Security
1. Use environment variables for sensitive data
2. Regularly update Zaturn
3. Keep data encrypted
4. Use proper authentication

### Common Workflows

#### Data Analysis
1. Set up data sources:
   ```bash
   echo "/path/to/data.csv" > ~/.zaturn/sources.txt
   ```
2. Run analysis:
   ```bash
   zaturn analyze "What's the trend?"
   ```
3. Process results:
   ```bash
   zaturn analyze "query" --output json > results.json
   ```

#### Batch Processing
1. Create queries file:
   ```bash
   echo "What's the trend?" > queries.txt
   echo "What's the average?" >> queries.txt
   ```
2. Process in parallel:
   ```bash
   zaturn batch queries.txt --parallel 4
   ```

#### Real-time Analysis
1. Enable streaming:
   ```bash
   zaturn analyze "Show live updates" --stream
   ```
2. Set up monitoring:
   ```bash
   zaturn analyze "Monitor system" --stream --output json
   ```

### Known Issues

#### Memory Leaks
- Large datasets may cause memory issues
- Solution: Process in batches or increase memory limit

#### Slow Performance
- Complex queries may take longer
- Solution: Optimize queries or use streaming output

#### Data Corruption
- Invalid data may cause errors
- Solution: Validate data before processing

### Support

#### How do I get help?
1. Check documentation
2. Search issues
3. Contact support
4. Join community forums

#### What information should I provide when reporting issues?
1. Zaturn version
2. Operating system
3. Configuration file
4. Error messages
5. Steps to reproduce

### Future Features

#### Planned Updates
1. Additional data formats
2. Enhanced analysis capabilities
3. Improved performance
4. Better error handling

#### Community Requests
1. Feature suggestions
2. Bug reports
3. Documentation improvements
4. Plugin system

### Contributing

#### How can I contribute?
1. Report issues
2. Submit feature requests
3. Create documentation
4. Write tests
5. Submit code changes

#### Code of Conduct
1. Be respectful
2. Follow guidelines
3. Maintain quality
4. Document changes
5. Test thoroughly

### License

#### Usage Rights
1. Free for personal use
2. Commercial license required
3. Open source components
4. Attribution required
5. No warranty provided

#### Distribution
1. Share source code
2. Maintain copyright
3. Include license
4. No modifications without permission
5. No redistribution of binaries

### Contact

#### Support Channels
1. Email: support@zaturn.com
2. Discord: #zaturn-support
3. GitHub: Issues page
4. Twitter: @zaturn_ai
5. Website: zaturn.com/support

#### Response Times
1. Critical issues: Within 24 hours
2. Normal issues: Within 3 days
3. Feature requests: Within 1 week
4. Documentation: Within 2 weeks
5. General inquiries: Within 3 days
