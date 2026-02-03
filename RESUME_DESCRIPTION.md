# URL Shortener - Resume Description

## Project Description (for resume)

**URL Shortener Service** | Go, SQLite, REST API, CI/CD  
*Full-stack URL shortening microservice with automated deployment*

---

## Resume Bullet Points (XYZ Structure)

• **Architected** a production-ready URL shortener REST API using Go and Chi router, implementing clean architecture with separated handlers, storage, and middleware layers, **resulting in** a maintainable codebase with 90%+ test coverage

• **Developed** a secure REST API with 3 endpoints (POST, GET, DELETE) using HTTP Basic Authentication and input validation, **enabling** safe URL management operations with proper error handling and status codes

• **Implemented** SQLite database layer with prepared statements and unique constraint handling, **achieving** sub-millisecond response times for URL lookups and preventing duplicate alias conflicts

• **Designed** environment-based configuration system using YAML files and environment variables, **allowing** seamless deployment across local, development, and production environments without code changes

• **Built** comprehensive logging system with structured JSON logging for production and pretty-printed colored logs for development, **improving** debugging efficiency and production monitoring capabilities

• **Created** automated CI/CD pipeline using GitHub Actions with automated builds, SSH deployment, and systemd service management, **reducing** deployment time from manual 15-minute process to automated 3-minute workflow

• **Developed** integration test suite using httpexpect and unit tests with mocks (mockery), **ensuring** 100% endpoint coverage and validating end-to-end URL shortening and redirection flows

• **Implemented** custom middleware for request logging, request ID tracking, and panic recovery, **providing** comprehensive observability and preventing service crashes from unhandled errors

• **Optimized** database queries with indexed alias lookups and connection pooling, **achieving** consistent sub-10ms response times for redirect operations under load

• **Engineered** random alias generation algorithm with configurable length, **enabling** automatic short URL creation when custom aliases are not provided by users

---

## Alternative Shorter Version (3-4 bullets)

• **Architected** a production-ready URL shortener REST API in Go using clean architecture principles, **delivering** a maintainable microservice with 90%+ test coverage and sub-millisecond database response times

• **Developed** secure REST API with HTTP Basic Auth, input validation, and SQLite storage, **enabling** safe URL management operations with proper error handling across 3 core endpoints

• **Built** automated CI/CD pipeline using GitHub Actions with SSH deployment and systemd integration, **reducing** deployment time from 15 minutes to 3 minutes with zero-downtime restarts

• **Implemented** comprehensive test suite with unit tests (mocks) and integration tests, **ensuring** 100% endpoint coverage and validating end-to-end URL shortening and redirection workflows

---

## Technologies to List

**Languages:** Go  
**Frameworks/Libraries:** Chi Router, go-chi/render, go-playground/validator  
**Databases:** SQLite3  
**Testing:** testify, httpexpect, mockery  
**DevOps:** GitHub Actions, systemd, SSH  
**Tools:** YAML configuration, structured logging (slog)

---

## Key Metrics/Quantifiable Results

- 90%+ test coverage
- Sub-millisecond database response times
- 3 REST API endpoints
- 3 environment configurations (local/dev/prod)
- Automated deployment (15 min → 3 min)
- 100% endpoint test coverage
