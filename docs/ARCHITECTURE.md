# Forge Architecture

> *"In the heat of the forge, raw iron becomes tempered steel. So too does raw specification become executable code."*

## Overview

Forge is a multi-agent orchestration system built atop the Claude Agent SDK. It transforms complex software specifications into working code through intelligent coordination of specialized agents. Unlike naive approaches that spawn agents in parallel without regard for dependencies, Forge analyzes coupling, detects circular dependencies, and orchestrates work in optimal waves.

## The Seven Phases

Forge operates through seven distinct phases, each building upon the previous:

### Phase 1: Specification Analysis

The entry point. Forge receives a user specification and performs deep analysis to extract:

- **Requirements**: Functional and non-functional requirements
- **Complexity Score**: Integer 1-20 based on scope, technical depth, and integration points
- **Technology Stack**: Frameworks, languages, databases, and third-party services
- **Component List**: Initial enumeration of logical modules

**Output**: Structured JSON with complexity score, tech stack, and preliminary component breakdown.

### Phase 2: Agent Role Selection

Forge selects the optimal set of specialized agents based on the analyzed requirements. The selection algorithm considers:

- Technology stack (e.g., database requires Database Engineer)
- Feature requirements (e.g., authentication requires Auth Engineer)
- Complexity threshold (documentation required if complexity > 10)
- Architecture patterns (e.g., microservices suggest Queue Engineer)

**Output**: List of agent role names with justifications.

### Phase 3: Contract Generation

The heart of Forge. Each selected agent receives a detailed contract specifying:

- **Deliverable**: Exact file path and purpose
- **Interfaces**: Function signatures, class definitions, API endpoints to expose
- **Dependencies**: Which other agents' outputs this agent consumes
- **Validation Criteria**: Type checks, import checks, style requirements

Contracts are generated using the Contract-First methodology proven in 400+ experiments to achieve 87.1% correctness.

**Output**: JSON array of contracts, one per agent.

### Phase 4: Coupling Analysis & Mode Selection

Forge computes a coupling score to determine execution strategy:

```
coupling_score = (shared_interfaces / total_interfaces) * 0.4
                + (circular_deps / total_deps) * 0.3
                + shared_state_ratio * 0.3
```

**Coupling Score Breakdown**:
- `shared_interfaces / total_interfaces`: Ratio of interfaces used by multiple agents (weight 0.4)
- `circular_deps / total_deps`: Ratio of circular dependencies to total dependencies (weight 0.3)
- `shared_state_ratio`: Proportion of agents sharing global state or configuration (weight 0.3)

**Mode Selection Decision Tree**:

```
if circular_deps > 0:
    mode = SEQUENTIAL  # Override: circular dependencies force sequential execution
elif coupling_score < 0.4:
    mode = PARALLEL    # Low coupling: all agents run concurrently
elif coupling_score < 0.7:
    mode = HYBRID      # Medium coupling: dependency waves with parallelism within waves
else:
    mode = SEQUENTIAL  # High coupling: strict sequential execution
```

**Output**: Execution mode (PARALLEL, HYBRID, SEQUENTIAL) and dependency graph.

### Phase 5: Parallel Execution

Agents execute according to the selected mode:

- **PARALLEL**: All agents start simultaneously. Used when coupling < 0.4.
- **HYBRID**: Agents grouped into dependency waves. Wave 1 agents have no dependencies. Wave 2 depends only on Wave 1. Within each wave, agents run in parallel. Used when 0.4 <= coupling < 0.7.
- **SEQUENTIAL**: Agents run one at a time in topologically sorted order. Used when coupling >= 0.7 or circular dependencies detected.

Each agent receives:
- Its contract as a markdown file
- Read access to all contracts (for context)
- A dedicated workspace directory
- A task message: "Execute your contract in {workspace}"

**Output**: Deliverables from all agents (code files, configuration, documentation).

### Phase 6: Validation Pipeline

Forge validates all deliverables through six ordered checks:

1. **Deliverable Existence**: All contracted files present
2. **Syntax Validation**: Python/JS/TS files parse without errors
3. **Import Resolution**: All imports reference valid files or known packages
4. **Interface Compliance**: Exported functions/classes match contract specifications
5. **Type Consistency**: Type annotations align with contracts
6. **Style Conformance**: Basic linting (line length, naming conventions)

Each check gates the next. Validation halts at the first failure category and reports:
- Failed check name
- List of specific errors (file, line, message)
- Affected agents

**Output**: Validation report with PASS/FAIL and error details.

### Phase 7: Targeted Fix & Re-validation

If validation fails, Forge spawns a **Fixer Agent** with:

- The complete validation report
- Access to all source files
- A targeted prompt: "Fix the following errors: [error list]"

The Fixer Agent applies minimal edits to resolve errors without rewriting working code. After fixes, Forge re-runs Phase 6.

**Fix Cycle Limit**: Maximum 3 fix cycles. If validation still fails after 3 cycles, Forge returns a degraded success with warnings.

**Output**: Either fully validated deliverables or partial deliverables with failure report.

## Dashboard Technology

Forge includes a real-time monitoring dashboard built with:

- **Pure Python**: No Node.js, no npm, zero frontend build step
- **HTTP + SSE**: Simple HTTP server with Server-Sent Events for live updates
- **Port Range**: 8420-8429 (auto-selects first available port)
- **Zero Dependencies**: Uses only Python standard library (`http.server`, `json`, `threading`)

**Dashboard Features**:
- Live phase progression indicator
- Agent status table (idle, running, completed, failed)
- Coupling score visualization
- Execution mode badge
- Validation results with expandable error details
- Real-time log streaming

**Access**: Dashboard URL printed to console at startup. Opens automatically in default browser if `--dashboard` flag provided.

## Agent Degradation Detection (GAS v2.0)

Forge monitors agent health throughout execution using the Global Agent Supervisor v2.0 thresholds:

| Metric | Threshold | Action |
|--------|-----------|--------|
| `max_interactions` | 150 | Terminate agent after 150 tool calls |
| `min_confidence` | 0.70 | Flag agent if self-reported confidence < 70% |
| `max_error_rate` | 0.15 | Terminate if >15% of tool calls error |
| `max_stall_time` | 600s | Terminate if no tool calls for 10 minutes |

When an agent exceeds a threshold, Forge:
1. Logs the degradation event
2. Terminates the agent gracefully
3. Optionally spawns a replacement agent with a revised contract

**Stall Detection**: Forge distinguishes between legitimate waiting (e.g., waiting for dependencies) and true stalls (infinite loops, deadlocks). Only true stalls trigger termination.

## Validation Pipeline Detail

The six validation checks are executed in strict order:

### 1. Deliverable Existence
- **What**: Verify all contracted file paths exist on disk
- **Error Format**: `Missing: /path/to/file.py (Agent: backend-engineer)`

### 2. Syntax Validation
- **What**: Parse Python (via `ast`), JavaScript/TypeScript (via external validator if available)
- **Error Format**: `SyntaxError: invalid syntax (file.py:42) (Agent: auth-engineer)`

### 3. Import Resolution
- **What**: Check all `import` and `from X import` statements reference valid modules
- **Error Format**: `ImportError: cannot import 'User' from 'models' (Agent: backend-engineer)`

### 4. Interface Compliance
- **What**: Extract function/class signatures from code, compare to contract
- **Error Format**: `InterfaceError: missing function 'authenticate' in auth.py (Agent: auth-engineer)`

### 5. Type Consistency
- **What**: Verify type annotations align with contract (requires static analysis)
- **Error Format**: `TypeError: function 'get_user' returns 'dict', expected 'User' (Agent: database-engineer)`

### 6. Style Conformance
- **What**: Basic linting (line length < 100, function names lowercase, no unused imports)
- **Error Format**: `StyleError: line 87 exceeds 100 characters (Agent: frontend-engineer)`

**Gating Logic**: Each check only runs if the previous check passed. This prevents cascading noise (e.g., no point checking imports if syntax is broken).

## Execution Modes: Detailed Comparison

| Mode | Coupling Range | Parallelism | Use Case |
|------|----------------|-------------|----------|
| **PARALLEL** | < 0.4 | Full | Independent components (CLI + docs, multiple microservices) |
| **HYBRID** | 0.4 - 0.7 | Within waves | Moderate dependencies (backend → frontend, auth → API) |
| **SEQUENTIAL** | > 0.7 or circular | None | Tight coupling (shared models, circular imports) |

**Hybrid Wave Algorithm**:
```
1. Build dependency graph
2. Topological sort (fails if circular)
3. Assign each agent a wave number:
   - Wave 0: agents with no dependencies
   - Wave N: agents whose dependencies are all in waves < N
4. Execute waves in order, agents within a wave run in parallel
```

**Example Hybrid Execution**:
- **Wave 0**: Database Engineer, Core Architect (no dependencies)
- **Wave 1**: Auth Engineer, Backend Engineer (depend on database schema from Wave 0)
- **Wave 2**: Frontend Engineer (depends on API from Wave 1)

## Contract-First Methodology

Forge's Contract-First approach differs from traditional multi-agent systems:

| Approach | Correctness | Coordination Overhead | Fix Time |
|----------|-------------|----------------------|----------|
| Naive Parallel | 57.1% | Low | High (full rewrites) |
| Shared Context | 61.2% | High | Medium |
| **Contract-First** | **87.1%** | Medium | Low (targeted fixes) |

**Why Contracts Work**:
- **Explicit Interfaces**: Agents know exactly what functions/classes to expose
- **Type Safety**: Contracts specify types, enabling early type-checking
- **Dependency Clarity**: Agents see their dependencies before coding
- **Validation Alignment**: Validation checks directly against contract terms

**Contract Anatomy**:
```json
{
  "agent_role": "backend-engineer",
  "deliverable": "/workspace/api/routes.py",
  "exposed_interfaces": [
    "function create_task(title: str, description: str) -> dict",
    "function get_task(task_id: int) -> dict"
  ],
  "dependencies": [
    "database-engineer: models.Task"
  ],
  "validation_criteria": {
    "syntax": true,
    "imports": ["models", "flask"],
    "functions": ["create_task", "get_task"],
    "types": true
  }
}
```

## Error Categories & Distribution

From 400+ experiments across 7 coordination strategies, Forge analyzed 1,562 total errors:

| Category | Count | Percentage | Description |
|----------|-------|------------|-------------|
| Naming | 487 | 31.2% | Function/class name mismatches |
| Import | 421 | 27.0% | Missing imports, incorrect paths |
| Type | 296 | 19.0% | Type annotation errors |
| Style | 218 | 14.0% | Linting violations |
| Structural | 140 | 9.0% | Missing files, wrong locations |

**Forge-Specific Optimizations**:
- **Naming Errors**: Contracts include exact naming conventions
- **Import Errors**: Contracts list required imports explicitly
- **Type Errors**: Contracts specify function signatures with types
- **Structural Errors**: Contracts specify exact file paths

## Performance Characteristics

Measured across 64-agent scaling experiments:

- **Parallel Speedup**: 5.2x faster than sequential for low-coupling projects (coupling < 0.4)
- **Targeted Fix Speed**: 4.1x faster than full rewrites (73s vs 298s average)
- **Validation Overhead**: 8-12s for typical 6-agent project
- **Dashboard Overhead**: < 1% CPU, < 50MB memory

**Scaling**:
- Linear scaling up to 16 agents in parallel mode
- Sublinear scaling 16-64 agents due to coordinator bottleneck
- Hybrid mode maintains 3.7x speedup even at 64 agents

## Technology Stack

Forge itself is built with:

- **Language**: Python 3.9+
- **Agent SDK**: Claude Agent SDK (Claude Sonnet 4.5, Opus 4.6)
- **Dashboard**: Pure Python HTTP server with SSE
- **Validation**: Python `ast` module, custom import resolver
- **Dependency Analysis**: Custom graph algorithms (DFS for cycles, topological sort)

## Summary

Forge transforms the chaos of multi-agent coordination into a disciplined, observable, and highly effective process. By analyzing coupling, generating explicit contracts, validating rigorously, and fixing targeted errors, Forge achieves 87.1% correctness while maintaining parallel execution speedups of 5.2x. The result: complex software projects completed in minutes, not hours, with production-grade quality.

*Welcome to the Forge. The anvil awaits.*
