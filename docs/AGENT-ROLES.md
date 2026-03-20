# Agent Roles Catalog

> *"Every sword requires a different hand. Every task, a different agent."*

## Overview

Forge orchestrates specialized agents, each with a distinct role and expertise. The Role Selection algorithm analyzes project requirements and selects the optimal subset of agents to complete the work. This document catalogs all 15 available agent roles, their responsibilities, and selection criteria.

## Role Categories

Agents are organized into five categories:

1. **Core**: Essential architecture and integration agents
2. **Backend**: Server-side logic, data, and services
3. **Frontend**: Client-side UI and user experience
4. **Quality**: Testing, security, and documentation
5. **Specialty**: Platform-specific implementations (mobile, CLI, extensions)

## Core Roles

### Core Architect

**Responsibility**: Define system architecture, technology stack, and integration patterns.

**Deliverables**:
- Architecture documentation (`docs/ARCHITECTURE.md`)
- Technology stack decisions (`tech-stack.json`)
- Module structure and boundaries
- Design patterns and conventions

**When to Include**: **Always included** in every project (default role).

**Typical Contract Elements**:
- Exposed interfaces: Architecture diagrams, component relationships
- Dependencies: None (runs first in dependency graph)
- Validation: Architecture completeness, consistency checks

**Example Projects**: All projects require a Core Architect.

---

### Integration Lead

**Responsibility**: Ensure all agent deliverables integrate correctly. Resolve interface conflicts.

**Deliverables**:
- Integration layer code (`integration/`)
- Adapter patterns for mismatched interfaces
- Shared configuration files
- Integration tests

**When to Include**: Projects with complexity >= 10 or >= 5 agents.

**Typical Contract Elements**:
- Exposed interfaces: Integration functions, adapters
- Dependencies: All other agents (runs last in dependency graph)
- Validation: All imports resolve, no circular dependencies

**Example Projects**: Multi-service platforms, microservices architectures.

## Backend Roles

### Backend Engineer

**Responsibility**: Implement server-side application logic, API endpoints, and business rules.

**Deliverables**:
- API route handlers (`api/routes.py`, `controllers/`)
- Business logic services (`services/`)
- Request/response models
- Middleware and error handlers

**When to Include**: Projects requiring server-side logic or API endpoints.

**Keywords**: API, REST, GraphQL, server, backend, endpoints, routes.

**Typical Contract Elements**:
- Exposed interfaces: API endpoint functions, service classes
- Dependencies: Database Engineer (for data access), Auth Engineer (for authentication)
- Validation: Endpoint signatures match API spec, error handling present

**Example Projects**: REST APIs, web applications, microservices.

---

### Database Engineer

**Responsibility**: Design database schema, implement data models, and create data access layers.

**Deliverables**:
- Database schema definitions (`schema.sql`, `models.py`)
- ORM models and migrations
- Data access objects (DAOs) or repositories
- Database connection configuration

**When to Include**: Projects requiring persistent data storage.

**Keywords**: Database, SQL, NoSQL, PostgreSQL, MongoDB, schema, models, persistence.

**Typical Contract Elements**:
- Exposed interfaces: Model classes, CRUD functions
- Dependencies: None (typically runs early)
- Validation: Schema validity, model relationships consistent

**Example Projects**: Any project with data storage (CRUDs, dashboards, e-commerce).

---

### Auth Engineer

**Responsibility**: Implement authentication and authorization systems.

**Deliverables**:
- Authentication logic (`auth/login.py`, `auth/register.py`)
- Authorization middleware and decorators
- Session/token management
- Password hashing and security utilities

**When to Include**: Projects requiring user authentication or access control.

**Keywords**: Authentication, authorization, login, signup, JWT, OAuth, sessions, security.

**Typical Contract Elements**:
- Exposed interfaces: `authenticate()`, `authorize()`, `hash_password()` functions
- Dependencies: Database Engineer (for user models)
- Validation: Security best practices (bcrypt, JWT validation)

**Example Projects**: User-facing applications, admin panels, multi-tenant systems.

---

### Queue Engineer

**Responsibility**: Implement message queues, background jobs, and asynchronous task processing.

**Deliverables**:
- Queue setup and configuration (`queue/setup.py`)
- Task definitions and workers (`tasks/`)
- Job scheduling logic
- Dead-letter queue handling

**When to Include**: Projects with asynchronous processing, background jobs, or event-driven architecture.

**Keywords**: Queue, Celery, RabbitMQ, Redis, background jobs, async, workers, tasks.

**Typical Contract Elements**:
- Exposed interfaces: Task functions, queue publish/subscribe
- Dependencies: Backend Engineer (for task triggers)
- Validation: Task signatures, queue connectivity

**Example Projects**: Email processing systems, data pipelines, notification services.

## Frontend Roles

### Frontend Engineer

**Responsibility**: Implement client-side application logic, state management, and API integration.

**Deliverables**:
- React/Vue/Angular components (`components/`)
- State management setup (Redux, Zustand, Pinia)
- API client services (`services/api.js`)
- Routing configuration

**When to Include**: Projects with web-based user interfaces.

**Keywords**: Frontend, React, Vue, Angular, UI, client-side, SPA, web app.

**Typical Contract Elements**:
- Exposed interfaces: Component APIs, state selectors, API client functions
- Dependencies: Backend Engineer (for API endpoints), UI/UX Engineer (for design system)
- Validation: Component props match contracts, API calls use correct endpoints

**Example Projects**: Web dashboards, SPAs, admin panels.

---

### UI/UX Engineer

**Responsibility**: Create design system, reusable UI components, and styling framework.

**Deliverables**:
- Design system documentation (`docs/DESIGN.md`)
- Reusable UI components (`components/ui/`)
- Styling utilities and theme configuration
- Accessibility implementations

**When to Include**: Projects emphasizing user experience or requiring consistent design.

**Keywords**: UI, UX, design system, components, Tailwind, CSS, styling, accessibility.

**Typical Contract Elements**:
- Exposed interfaces: UI component library (Button, Input, Modal, etc.)
- Dependencies: None (typically runs early)
- Validation: Component accessibility (ARIA labels), responsive design

**Example Projects**: Consumer-facing applications, design-heavy dashboards.

## Quality Roles

### Security Engineer

**Responsibility**: Implement security measures, vulnerability scanning, and secure coding practices.

**Deliverables**:
- Security middleware (`middleware/security.py`)
- Input validation and sanitization
- Rate limiting and DDoS protection
- Security audit documentation

**When to Include**: Projects handling sensitive data, authentication, or requiring high security.

**Keywords**: Security, encryption, HTTPS, CORS, CSRF, XSS, SQL injection, vulnerabilities.

**Typical Contract Elements**:
- Exposed interfaces: Security utilities, validation functions
- Dependencies: Auth Engineer (for secure auth), Backend Engineer (for middleware)
- Validation: OWASP Top 10 checks, dependency vulnerability scan

**Example Projects**: Financial applications, healthcare systems, e-commerce.

---

### QA Engineer

**Responsibility**: Write automated tests, implement testing infrastructure, and ensure code quality.

**Deliverables**:
- Unit tests (`tests/unit/`)
- Integration tests (`tests/integration/`)
- Testing utilities and fixtures
- CI/CD pipeline configuration (`.github/workflows/test.yml`)

**When to Include**: Projects with complexity >= 12 or requiring high reliability.

**Keywords**: Testing, QA, pytest, Jest, unit tests, integration tests, CI/CD.

**Typical Contract Elements**:
- Exposed interfaces: Test suites, testing utilities
- Dependencies: All implementation agents (tests depend on code)
- Validation: Test coverage >= 80%, all tests pass

**Example Projects**: Production-grade applications, open-source libraries.

---

### Documentation Writer

**Responsibility**: Write user-facing documentation, API references, and developer guides.

**Deliverables**:
- README.md with setup instructions
- API documentation (`docs/API.md`)
- User guides and tutorials
- Code comments and docstrings

**When to Include**: Projects with complexity >= 10 or requiring external users/contributors.

**Keywords**: Documentation, README, API docs, guides, tutorials, examples.

**Typical Contract Elements**:
- Exposed interfaces: Documentation files
- Dependencies: All implementation agents (documents their work)
- Validation: Documentation completeness, code examples run successfully

**Example Projects**: Open-source projects, APIs, SDKs, libraries.

## Specialty Roles

### Mobile Engineer

**Responsibility**: Implement mobile applications for iOS and Android.

**Deliverables**:
- React Native / Flutter components
- Mobile-specific navigation
- Platform-specific integrations (camera, location, etc.)
- Mobile build configuration

**When to Include**: Projects explicitly requiring mobile applications.

**Keywords**: Mobile, iOS, Android, React Native, Flutter, mobile app.

**Typical Contract Elements**:
- Exposed interfaces: Mobile screens, navigation structure
- Dependencies: Backend Engineer (for API), UI/UX Engineer (for design)
- Validation: App builds successfully, platform-specific features work

**Example Projects**: Mobile-first applications, companion apps.

---

### CLI Engineer

**Responsibility**: Implement command-line interfaces and terminal applications.

**Deliverables**:
- CLI entry point (`cli.py`, `main.rs`)
- Command parsers and subcommands
- Terminal output formatting
- Configuration file handling

**When to Include**: Projects requiring command-line tools or developer utilities.

**Keywords**: CLI, command-line, terminal, console, tool, script.

**Typical Contract Elements**:
- Exposed interfaces: Command functions, argument parsers
- Dependencies: Backend Engineer (for core logic)
- Validation: Commands parse correctly, help text present, exit codes

**Example Projects**: Developer tools, automation scripts, system utilities.

---

### Extension Engineer

**Responsibility**: Implement browser extensions, IDE plugins, or platform extensions.

**Deliverables**:
- Extension manifest (`manifest.json`, `package.json`)
- Extension background scripts and content scripts
- Extension UI components
- Platform-specific APIs integration

**When to Include**: Projects explicitly requiring extensions.

**Keywords**: Extension, plugin, Chrome extension, VS Code extension, browser, addon.

**Typical Contract Elements**:
- Exposed interfaces: Extension APIs, message passing
- Dependencies: Frontend Engineer (for popup UI if applicable)
- Validation: Extension loads, permissions declared, manifest valid

**Example Projects**: Browser productivity tools, IDE enhancements.

## Role Selection Algorithm

Forge uses a rule-based algorithm to select agents:

```
function select_agents(specification, complexity):
    agents = []

    # Core roles (always included)
    agents.append("Core Architect")

    # Integration Lead (conditional)
    if complexity >= 10 or agent_count >= 5:
        agents.append("Integration Lead")

    # Backend roles
    if requires_backend(specification):
        agents.append("Backend Engineer")

        if requires_database(specification):
            agents.append("Database Engineer")

        if requires_auth(specification):
            agents.append("Auth Engineer")

        if requires_async_processing(specification):
            agents.append("Queue Engineer")

    # Frontend roles
    if requires_frontend(specification):
        agents.append("Frontend Engineer")

        if emphasizes_design(specification):
            agents.append("UI/UX Engineer")

    # Quality roles
    if requires_security(specification):
        agents.append("Security Engineer")

    if complexity >= 12:
        agents.append("QA Engineer")

    if complexity >= 10 or is_public_facing(specification):
        agents.append("Documentation Writer")

    # Specialty roles
    if requires_mobile(specification):
        agents.append("Mobile Engineer")

    if requires_cli(specification):
        agents.append("CLI Engineer")

    if requires_extension(specification):
        agents.append("Extension Engineer")

    return agents
```

## Role Selection Examples

### Example 1: Simple REST API

**Specification**: "Build a REST API for managing tasks with user authentication."

**Complexity**: 8

**Selected Agents**:
1. Core Architect (always)
2. Backend Engineer (API keyword)
3. Database Engineer (persistent tasks)
4. Auth Engineer (authentication keyword)

**Total**: 4 agents

---

### Example 2: Full-Stack Dashboard

**Specification**: "Create a web dashboard for monitoring system metrics with real-time updates and user roles."

**Complexity**: 14

**Selected Agents**:
1. Core Architect (always)
2. Integration Lead (complexity >= 10)
3. Backend Engineer (API for metrics)
4. Database Engineer (metrics storage)
5. Auth Engineer (user roles)
6. Queue Engineer (real-time updates suggest background processing)
7. Frontend Engineer (dashboard UI)
8. UI/UX Engineer (design emphasis for dashboard)
9. QA Engineer (complexity >= 12)
10. Documentation Writer (complexity >= 10)

**Total**: 10 agents

---

### Example 3: Mobile App with Backend

**Specification**: "Build a mobile task management app with cloud sync."

**Complexity**: 11

**Selected Agents**:
1. Core Architect (always)
2. Integration Lead (complexity >= 10)
3. Backend Engineer (cloud sync API)
4. Database Engineer (task storage)
5. Auth Engineer (user accounts)
6. Mobile Engineer (mobile app keyword)
7. Documentation Writer (complexity >= 10)

**Total**: 7 agents

## Summary

Forge's 15 agent roles cover the full spectrum of software development disciplines. By systematically analyzing project requirements and applying the selection algorithm, Forge assembles the optimal team for each project. No wasted agents, no missing expertise—just the right team for the job.

*Choose wisely. The team determines the blade's edge.*
