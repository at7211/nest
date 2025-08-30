# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is the official NestJS framework repository - a progressive Node.js framework for building efficient, reliable and scalable server-side applications. The repository contains the core framework packages and extensive integration tests.

## Architecture

### Monorepo Structure
- **packages/**: Core framework packages organized by functionality
  - **core/**: Core framework functionality (dependency injection, middleware, guards, etc.)
  - **common/**: Shared decorators, exceptions, interfaces, pipes, and utilities
  - **microservices/**: Microservices support (TCP, Redis, RabbitMQ, gRPC, Kafka, etc.)
  - **platform-express/**: Express.js platform adapter
  - **platform-fastify/**: Fastify platform adapter
  - **websockets/**: WebSocket support
  - **platform-socket.io/**: Socket.IO adapter
  - **platform-ws/**: WebSocket adapter
  - **testing/**: Testing utilities and mocking

### Key Architectural Concepts
- **Dependency Injection**: Core IoC container with module system
- **Decorators**: Heavy use of TypeScript decorators for metadata
- **Modular Architecture**: Application organized into modules with providers, controllers, and services
- **Platform Agnostic**: Abstraction layer over HTTP frameworks (Express/Fastify)
- **Microservices**: Built-in support for various transport layers

## Common Development Commands

### Building
```bash
npm run build           # Build all packages and move to sample directories
npm run build:prod      # Build packages without moving to samples
npm run build:dev       # Watch mode build
npm run clean           # Clean compiled output
```

### Testing
```bash
npm run test            # Run unit tests for all packages
npm run test:dev        # Watch mode for unit tests
npm run test:cov        # Run tests with coverage
npm run test:integration # Run integration tests (requires Docker)
sh scripts/run-integration.sh # Alternative integration test runner
```

### Linting & Formatting
```bash
npm run lint            # Run ESLint on all packages
npm run lint:fix        # Fix ESLint issues automatically
npm run format          # Format code with Prettier
```

### Development Setup
```bash
npm ci --legacy-peer-deps  # Install dependencies
sh scripts/prepare.sh      # Compile packages and move to sample directories
```

### Integration Tests
Integration tests require Docker for external services (Redis, MongoDB, etc.). Use:
```bash
npm run test:docker:up     # Start Docker services
npm run test:integration   # Run integration tests
npm run test:docker:down   # Stop Docker services
```

## Testing Guidelines

- All features and bug fixes **must be tested**
- Unit tests go in `packages/*/test/` directories
- Integration tests are in `integration/` directory
- Use Mocha as the test framework
- Mock external dependencies appropriately
- Test coverage is tracked with nyc

## Code Style & Standards

- Follow Google's JavaScript Style Guide with 100-character line limit
- Use TypeScript decorators extensively
- Follow existing patterns in similar modules
- All public API methods should be properly typed
- Use consistent naming conventions across packages

## Commit Message Format

Follow conventional commits with specific scopes:
```
<type>(<scope>): <subject>

Types: feat, fix, docs, style, refactor, perf, test, build, ci, chore, sample
Scopes: common, core, microservices, express, fastify, socket.io, ws, testing, websockets
```

## Working with Samples

The `sample/` directory contains example applications demonstrating various features. After building, packages are automatically moved to sample directories for testing.

## Important Notes

- Node.js >= 20 is required
- This is a TypeScript-first codebase
- Extensive use of reflection and decorators
- Platform adapters provide abstraction over HTTP frameworks
- Integration tests require Docker for external services
- All changes should include appropriate tests