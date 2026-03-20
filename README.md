<div align="center">

<img src="assets/mascot.png" alt="Forge" width="250"/>

# Forge

**Make AI agents actually work together.**

![Experiments](https://img.shields.io/badge/experiments-400%2B-9b59b6)
![Quality](https://img.shields.io/badge/quality-+52.5%25-27ae60)
![License](https://img.shields.io/badge/license-MIT-2c3e50)

</div>

---

## What is Forge?

When multiple AI agents build something together, they fail. One writes `processPayment()`, another expects `handlePayment()`. Nothing fits.

**Forge gives every agent a shared contract before they start**, so their code works together on the first try.

---

## How it works

```mermaid
flowchart LR
    A["1. Analyze\n_______\nRead project,\nscore complexity"] --> B["2. Pick Mode\n_______\nParallel, Hybrid,\nor Sequential"]
    B --> C["3. Contract\n_______\nShared blueprint:\nnames, types, style"]
    C --> D["4. Dashboard\n_______\nLive monitor,\nzero dependencies"]
    D --> E["5. Execute\n_______\nAgents build\ntheir parts"]
    E --> F["6. Validate\n_______\nAuto-check\nsyntax & imports"]
    F --> G["7. Deliver\n_______\nMerge into\nclean code"]

    style A fill:#4a1942,stroke:#9b59b6,color:#fff
    style B fill:#1a3a5c,stroke:#3498db,color:#fff
    style C fill:#1a4731,stroke:#27ae60,color:#fff
    style D fill:#4a3728,stroke:#e67e22,color:#fff
    style E fill:#4a1942,stroke:#9b59b6,color:#fff
    style F fill:#1a3a5c,stroke:#3498db,color:#fff
    style G fill:#1a4731,stroke:#27ae60,color:#fff
```

---

## Three modes

Forge picks the right one based on how connected your modules are.

```mermaid
flowchart TB
    subgraph PARALLEL["Parallel Mode &nbsp; | &nbsp; Coupling < 0.4 &nbsp; | &nbsp; 5.2x faster"]
        direction LR
        PA1[Agent 1] ~~~ PA2[Agent 2] ~~~ PA3[Agent 3] ~~~ PA4[Agent 4]
    end

    subgraph HYBRID["Hybrid Mode &nbsp; | &nbsp; Coupling 0.4-0.7 &nbsp; | &nbsp; 2.8x faster"]
        direction LR
        HCore[Core Agent] --> HF1[Feature 1]
        HCore --> HF2[Feature 2]
        HCore --> HF3[Feature 3]
    end

    subgraph SEQUENTIAL["Sequential Mode &nbsp; | &nbsp; Coupling > 0.7 &nbsp; | &nbsp; Safest"]
        direction LR
        S1[Agent 1] --> S2[Agent 2] --> S3[Agent 3] --> S4[Agent 4]
    end

    style PARALLEL fill:#1a4731,stroke:#27ae60,color:#fff
    style HYBRID fill:#4a3728,stroke:#e67e22,color:#fff
    style SEQUENTIAL fill:#4a1942,stroke:#9b59b6,color:#fff
```

---

## Results

Tested across 400+ experiments.

```mermaid
xychart-beta
    title "Quality Score by Approach"
    x-axis ["Naive\nParallel", "Naive\nSequential", "Shared\nContext", "Self\nRepair", "Human\nContract", "LLM\nContract", "Forge"]
    y-axis "Quality Score" 0.4 --> 1.0
    bar [0.571, 0.614, 0.682, 0.728, 0.794, 0.803, 0.871]
```

| What we measured | Result |
|-----------------|--------|
| Quality improvement | **+52.5%** (0.571 to 0.871) |
| Speed in parallel mode | **5.2x** faster |
| Errors caught automatically | **97%** (vs 58% manual) |
| Scaling to 16 agents | **8%** error increase (vs 340% without contracts) |

---

## What's in the contract?

The contract is the key idea. Before any agent writes code, Forge generates this:

```mermaid
flowchart TB
    CONTRACT["Coordination Contract"]
    CONTRACT --> MM["Module Manifest\n_What each agent builds_"]
    CONTRACT --> ID["Interface Definitions\n_Function signatures & types_"]
    CONTRACT --> ST["Shared Types\n_Common data structures_"]
    CONTRACT --> SG["Style Guide\n_Naming, formatting rules_"]
    CONTRACT --> DM["Dependency Map\n_Who imports from where_"]
    CONTRACT --> SB["Section Boundaries\n_Clear code ownership_"]

    style CONTRACT fill:#4a1942,stroke:#9b59b6,color:#fff
    style MM fill:#1a3a5c,stroke:#3498db,color:#fff
    style ID fill:#1a3a5c,stroke:#3498db,color:#fff
    style ST fill:#1a3a5c,stroke:#3498db,color:#fff
    style SG fill:#1a3a5c,stroke:#3498db,color:#fff
    style DM fill:#1a3a5c,stroke:#3498db,color:#fff
    style SB fill:#1a3a5c,stroke:#3498db,color:#fff
```

---

## Quick start

```bash
forge build project.yaml
```

That's it. Forge handles everything.

---

## Docs

| Document | What's in it |
|----------|-------------|
| [SKILL.md](SKILL.md) | Full protocol spec |
| [Architecture](docs/ARCHITECTURE.md) | How the 7 phases work |
| [Experiments](docs/EXPERIMENTS.md) | All 400+ experiment data |
| [Agent Roles](docs/AGENT-ROLES.md) | 15+ agent types |
| [REST API Example](examples/rest-api-example.md) | Worked example |

---

## License

MIT

---

<div align="center">

<img src="assets/mascot.png" alt="Forge" width="80"/>

**HappyCapy Research**

</div>
