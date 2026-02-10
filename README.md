# URL Shortener

A simple, fast, and secure URL shortener service built with Go. This service allows you to shorten long URLs into manageable aliases and redirect users to the original URLs. It integrates with a separate **SSO gRPC service** for authentication and authorization (e.g. admin checks).

## Features

- **Shorten URLs**: Create short aliases for long URLs
- **Auto-generate aliases**: Automatically generate random aliases if not provided
- **Redirect**: Seamlessly redirect users to original URLs
- **Delete URLs**: Remove shortened URLs by alias
- **Basic Authentication**: Secure endpoints with HTTP Basic Auth
- **SSO gRPC integration**: Connects to an SSO (auth) service over gRPC for user/admin checks (retries and logging built-in)
- **SQLite Storage**: Lightweight database for URL storage
- **Structured Logging**: Comprehensive logging with different formats for different environments
- **RESTful API**: Clean REST API design

## Requirements

- Go 1.24.0 or higher
- SQLite3 (included with Go SQLite driver)
- **SSO gRPC service** running and reachable (see [SSO integration](#sso-integration) below)

## Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd URL-shortener
```

2. Install dependencies:
```bash
go mod download
```

3. Build the application:
```bash
go build -o url-shortener ./cmd/url-shortener
```

## Configuration

The application uses YAML configuration files. Configuration can be set via the `CONFIG_PATH` environment variable, or it defaults to `config/local.yaml`.

### Configuration Files

- **`config/local.yaml`**: Local development configuration
- **`config/prod.yaml`**: Production configuration

### Configuration Structure

```yaml
env: "local"  # Environment: local, dev, or prod
storage_path: "./storage/storage.db"  # Path to SQLite database
http_server:
  address: "localhost:8082"  # Server address
  timeout: 4s  # Request timeout
  idle_timeout: 60s  # Idle connection timeout
  user: "myuser"  # Basic Auth username
  password: "mypass"  # Basic Auth password (can be set via HTTP_SERVER_PASSWORD env var)
client:
  sso:
    address: "localhost:44044"  # SSO gRPC server address
    timeout: 3s  # gRPC call timeout
    retriesCount: 3  # Retries on retryable errors
app_secret: "your-app-secret"  # Can be set via APP_SECRET env var (required)
```

### Environment Variables

- `CONFIG_PATH`: Path to configuration file (default: `config/local.yaml`)
- `HTTP_SERVER_PASSWORD`: Override password from config file
- `APP_SECRET`: Application secret (required if not set in config)

## SSO integration

The URL shortener talks to a separate **SSO gRPC service** for authentication and authorization. The SSO service must be running and registered in config under `client.sso` (address, timeout, retriesCount). The client uses the SSO Auth API (e.g. `IsAdmin`) with retries and structured logging. Ensure the SSO server is started first and that `client.sso.address` matches your SSO gRPC listen address.

## Usage

### Start the Server

```bash
./url-shortener
```

Or with custom config:
```bash
CONFIG_PATH=config/prod.yaml ./url-shortener
```

### API Endpoints

#### 1. Shorten a URL

**POST** `/url`

Create a new shortened URL. Requires Basic Authentication.

**Request Body:**
```json
{
  "url": "https://example.com/very/long/url/path",
  "alias": "example"  // Optional: if not provided, a random 10-character alias will be generated
}
```

**Response:**
```json
{
  "status": "OK",
  "alias": "example"
}
```

**Example:**
```bash
curl -X POST http://localhost:8082/url \
  -u myuser:mypass \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com", "alias": "ex"}'
```

#### 2. Redirect to Original URL

**GET** `/{alias}`

Redirect to the original URL associated with the alias.

**Example:**
```bash
curl -L http://localhost:8082/ex
```

#### 3. Delete a URL

**DELETE** `/url/{alias}`

Delete a shortened URL by its alias. Requires Basic Authentication.

**Response:**
```json
{
  "status": "OK",
  "alias": "ex"
}
```

**Example:**
```bash
curl -X DELETE http://localhost:8082/url/ex \
  -u myuser:mypass
```

## Project Structure

```
URL-shortener/
├── cmd/
│   └── url-shortener/          # Application entry point
│       └── main.go
├── internal/
│   ├── clients/
│   │   └── sso/
│   │       └── grpc/           # SSO gRPC client (Auth API, retries, logging)
│   ├── config/                  # Configuration management
│   ├── http-server/
│   │   ├── handlers/            # HTTP handlers
│   │   │   ├── redirect/        # Redirect handler
│   │   │   └── url/
│   │   │       ├── save/        # Save URL handler
│   │   │       └── delete/      # Delete URL handler
│   │   └── middleware/          # HTTP middleware
│   │       └── logger/          # Request logging
│   ├── lib/                     # Shared libraries
│   │   ├── api/                 # API utilities
│   │   ├── logger/              # Logging utilities
│   │   └── random/              # Random string generation
│   └── storage/                 # Storage layer
│       ├── storage.go           # Storage interface
│       └── sqlite/              # SQLite implementation
├── config/                      # Configuration files
│   ├── local.yaml
│   └── prod.yaml
├── tests/                       # Integration tests
│   └── url_shortener_test.go
├── deployment/                  # Deployment files
│   └── url-shortener.service   # Systemd service file
└── .github/workflows/           # CI/CD workflows
    └── deploy.yaml
```

## Testing

### Run Unit Tests

```bash
go test ./...
```

### Run Integration Tests

```bash
go test ./tests/...
```

Make sure the server is running on `localhost:8082` with the credentials from `config/local.yaml` before running integration tests.

## Logging

The application uses structured logging with different formats based on the environment:

- **Local**: Pretty-printed colored logs with debug level
- **Dev**: JSON format with debug level
- **Prod**: JSON format with info level

## Deployment

### Systemd Service

The project includes a systemd service file for deployment. See `deployment/url-shortener.service` for configuration.

### GitHub Actions

The project includes a GitHub Actions workflow for automated deployment. Configure the following secrets in GitHub:

- `DEPLOY_SSH_KEY`: Private SSH key for server access
- `AUTH_PASS`: HTTP server password for production
- `APP_SECRET`: Application secret (used with SSO)

Update the `HOST` environment variable in `.github/workflows/deploy.yaml` with your server IP address. Ensure the SSO gRPC service is deployed and that `client.sso.address` in your config points to it.
