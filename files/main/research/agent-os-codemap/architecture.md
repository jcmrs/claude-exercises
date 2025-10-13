# Agent OS: Architecture Diagrams and Refactoring Guidance

## Overview

This document provides comprehensive architecture diagrams, component relationships, and refactoring guidance for Agent OS. It visualizes both the current architecture and proposed future states.

**Related Documents:**
- [Project Roadmap](./roadmap.md)
- [Code Map](./codemap.md)
- [Workflows Analysis](./workflows.md)

---

## Current Architecture

### High-Level System Architecture

```mermaid
graph TB
    subgraph "GitHub Repository"
        A[agent-os repo]
    end
    
    subgraph "User Environment"
        B[~/agent-os/]
        B1[config.yml]
        B2[profiles/]
        B3[scripts/]
        B4[standards/]
    end
    
    subgraph "Project 1"
        C1[agent-os/]
        C2[.claude/]
    end
    
    subgraph "Project 2"
        D1[agent-os/]
        D2[.claude/]
    end
    
    A -->|base-install.sh| B
    B -->|project-install.sh| C1
    B -->|project-install.sh| D1
    B -->|project-install.sh| C2
    B -->|project-install.sh| D2
    
    B1 --> C1
    B2 --> C1
    B2 --> C2
    B2 --> D1
    B2 --> D2
    
    style A fill:#e1f5ff
    style B fill:#fff3e0
    style C1 fill:#f3e5f5
    style C2 fill:#f3e5f5
    style D1 fill:#f3e5f5
    style D2 fill:#f3e5f5
```

### Component Layer Architecture

```mermaid
graph TD
    subgraph "Delivery Layer"
        A[GitHub Repository]
        B[base-install.sh]
        C[GitHub API]
    end
    
    subgraph "Installation Layer"
        D[Base Installation ~/agent-os/]
        E[Project Installation Scripts]
        F[Template Engine]
        G[File Copy Engine]
    end
    
    subgraph "Configuration Layer"
        H[Base Config]
        I[Project Config]
        J[CLI Arguments]
        K[Config Resolver]
    end
    
    subgraph "Content Layer"
        L[Profiles]
        M[Standards]
        N[Workflows]
        O[Roles]
    end
    
    subgraph "Integration Layer"
        P[Claude Code Integration]
        Q[Generic Tool Integration]
        R[Agent Generator]
        S[Command Compiler]
    end
    
    subgraph "Project Layer"
        T[agent-os/]
        U[.claude/agents/]
        V[.claude/commands/]
    end
    
    A --> B
    B --> D
    D --> E
    E --> G
    E --> F
    
    H --> K
    I --> K
    J --> K
    K --> E
    
    L --> F
    M --> F
    N --> F
    O --> R
    
    F --> S
    R --> P
    S --> P
    S --> Q
    
    G --> T
    P --> U
    P --> V
    
    style A fill:#e3f2fd
    style D fill:#fff8e1
    style K fill:#f3e5f5
    style P fill:#e8f5e9
    style T fill:#fce4ec
```

### Data Flow Architecture

```mermaid
flowchart LR
    subgraph "Source"
        A[GitHub Repo]
    end
    
    subgraph "Base"
        B[~/agent-os/]
    end
    
    subgraph "Processing"
        C[YAML Parser]
        D[Template Compiler]
        E[Standards Processor]
    end
    
    subgraph "Output"
        F[Project Files]
        G[Agent Definitions]
        H[Command Files]
    end
    
    A -->|Download| B
    B -->|Read| C
    B -->|Load| D
    B -->|Include| E
    
    C -->|Parse roles.yml| D
    E -->|Embed standards| D
    D -->|Generate| G
    D -->|Compile| H
    
    B -->|Copy| F
    G --> F
    H --> F
    
    style A fill:#bbdefb
    style B fill:#fff9c4
    style C fill:#f8bbd0
    style D fill:#f8bbd0
    style E fill:#f8bbd0
    style F fill:#c8e6c9
    style G fill:#c8e6c9
    style H fill:#c8e6c9
```

---

## Current Architecture Pain Points

### 1. Dual Installation Model

```mermaid
graph TD
    A[User] -->|Step 1| B[Install ~/agent-os/]
    B -->|Step 2| C[Install project 1]
    B -->|Step 3| D[Install project 2]
    B -->|Step 4| E[Install project 3]
    
    F[Update Base] -->|Requires| G[Update all projects]
    
    H[Customize Standards] -->|At base level| I[Affects all projects]
    H -->|At project level| J[Diverges from base]
    
    style B fill:#ffcdd2
    style F fill:#ffcdd2
    style H fill:#ffcdd2
```

**Issues:**
- Two-step installation complexity
- Version sync challenges
- Customization conflicts
- Dependency on user-level directory

### 2. File Duplication Pattern

```mermaid
graph LR
    A[~/agent-os/standards/] --> B[Project 1 standards/]
    A --> C[Project 2 standards/]
    A --> D[Project 3 standards/]
    
    E[Update Standards] -->|Must propagate| F[All Projects]
    
    style A fill:#fff9c4
    style B fill:#ffcdd2
    style C fill:#ffcdd2
    style D fill:#ffcdd2
    style E fill:#ffcdd2
```

**Issues:**
- Storage overhead
- Sync complexity
- Version fragmentation
- Update propagation

### 3. Tight Coupling

```mermaid
graph TD
    A[Installation Script] --> B[YAML Parsing]
    A --> C[Template Compilation]
    A --> D[File Operations]
    A --> E[Claude Code Paths]
    A --> F[Standards Processing]
    
    style A fill:#ffcdd2
    style E fill:#ffcdd2
```

**Issues:**
- Monolithic scripts
- Tool-specific code embedded
- Difficult to test
- Hard to extend

---

## Proposed Architecture: Phase 1 (Project-Only)

### Simplified Installation Model

```mermaid
graph TB
    A[GitHub Repository] -->|Single Command| B[Project Installation]
    B --> C[agent-os/]
    B --> D[.claude/]
    
    E[All Resources Bundled] --> B
    
    style A fill:#e1f5ff
    style B fill:#c8e6c9
    style C fill:#c8e6c9
    style D fill:#c8e6c9
```

**Benefits:**
- Single installation step
- No user-level dependencies
- Self-contained projects
- Simplified updates

### Embedded Resources Model

```mermaid
graph LR
    A[Project Install Script] --> B[Embedded Profiles]
    A --> C[Embedded Standards]
    A --> D[Embedded Templates]
    
    B --> E[agent-os/ directory]
    C --> E
    D --> E
    
    F[Single Config] --> E
    
    style A fill:#fff8e1
    style E fill:#c8e6c9
```

**Changes:**
- Bundle all resources in installer
- Remove `~/agent-os/` dependency
- Single configuration file
- Simplified path resolution

---

## Proposed Architecture: Phase 3 (API-Driven)

### Modern API Architecture

```mermaid
graph TB
    subgraph "API Layer"
        A[Profile Service]
        B[Standards Service]
        C[Template Service]
        D[Installation Service]
    end
    
    subgraph "Storage Layer"
        E[(Profile Database)]
        F[(Standards Repository)]
        G[CDN / Cache]
    end
    
    subgraph "Client Layer"
        H[CLI Tool]
        I[Web Interface]
        J[IDE Plugin]
    end
    
    subgraph "Project Layer"
        K[Local Cache]
        L[Project Config]
        M[Generated Files]
    end
    
    H --> A
    H --> B
    H --> C
    H --> D
    
    I --> A
    I --> B
    
    J --> A
    J --> B
    
    A --> E
    B --> F
    B --> G
    C --> F
    
    H --> K
    K --> L
    D --> M
    
    style A fill:#81c784
    style B fill:#81c784
    style C fill:#81c784
    style D fill:#81c784
    style H fill:#64b5f6
    style K fill:#ffb74d
```

### API Service Architecture

```mermaid
graph LR
    subgraph "Clients"
        A[CLI]
        B[Web UI]
        C[IDE Extension]
    end
    
    subgraph "API Gateway"
        D[Authentication]
        E[Rate Limiting]
        F[Routing]
    end
    
    subgraph "Services"
        G[Profile Manager]
        H[Standards Manager]
        I[Template Engine]
        J[Version Manager]
    end
    
    subgraph "Data"
        K[Profiles DB]
        L[Standards Repo]
        M[Template Cache]
        N[Version Store]
    end
    
    A --> D
    B --> D
    C --> D
    
    D --> F
    E --> F
    
    F --> G
    F --> H
    F --> I
    F --> J
    
    G --> K
    H --> L
    I --> M
    J --> N
    
    style D fill:#ce93d8
    style E fill:#ce93d8
    style F fill:#ce93d8
    style G fill:#81c784
    style H fill:#81c784
    style I fill:#81c784
    style J fill:#81c784
```

### Dynamic Resource Loading

```mermaid
sequenceDiagram
    participant CLI
    participant API
    participant Cache
    participant CDN
    participant DB
    
    CLI->>Cache: Check local cache
    Cache-->>CLI: Cache miss
    
    CLI->>API: Request profile
    API->>DB: Query profile
    DB-->>API: Profile data
    
    API->>CDN: Get standards
    CDN-->>API: Standards content
    
    API->>API: Compile template
    API-->>CLI: Compiled resources
    
    CLI->>Cache: Store in cache
    CLI->>CLI: Generate project files
```

**Benefits:**
- No file copying
- Real-time updates
- Version management
- Centralized maintenance
- Reduced storage
- Faster installations

---

## Component Modularity Analysis

### Current Modularity

```mermaid
graph TD
    A[Monolithic Scripts] --> B[common-functions.sh]
    A --> C[base-install.sh]
    A --> D[project-install.sh]
    A --> E[project-update.sh]
    
    B --> F[YAML Parsing]
    B --> G[File Operations]
    B --> H[Template Compilation]
    B --> I[Config Management]
    B --> J[Validation]
    
    style A fill:#ffcdd2
    style B fill:#fff9c4
```

**Modularity Score: 4/10**
- Some separation in common-functions.sh
- Still tightly coupled
- Difficult to test individually
- Hard to reuse components

### Proposed Modularity (Phase 2)

```mermaid
graph TD
    A[Core Library] --> B[Config Module]
    A --> C[Template Module]
    A --> D[Install Module]
    A --> E[Validation Module]
    
    B --> F[YAML Parser]
    B --> G[Config Merger]
    B --> H[Config Writer]
    
    C --> I[Template Loader]
    C --> J[Variable Substitution]
    C --> K[Standards Processor]
    
    D --> L[File Manager]
    D --> M[Directory Creator]
    D --> N[Permission Setter]
    
    E --> O[Schema Validator]
    E --> P[Path Validator]
    E --> Q[Dependency Checker]
    
    style A fill:#c8e6c9
    style B fill:#81c784
    style C fill:#81c784
    style D fill:#81c784
    style E fill:#81c784
```

**Target Modularity Score: 8/10**
- Clear separation of concerns
- Independently testable
- Reusable components
- Pluggable architecture

---

## Integration Points

### Tool Integration Architecture

```mermaid
graph TD
    subgraph "Agent OS Core"
        A[Core Engine]
        B[Profile Manager]
        C[Standards Manager]
    end
    
    subgraph "Tool Adapters"
        D[Claude Code Adapter]
        E[Cursor Adapter]
        F[Generic Adapter]
        G[Future Tool Adapter]
    end
    
    subgraph "Tool Integrations"
        H[Claude Code]
        I[Cursor]
        J[Generic Tool]
        K[Future Tool]
    end
    
    A --> B
    A --> C
    B --> D
    B --> E
    B --> F
    B --> G
    
    D --> H
    E --> I
    F --> J
    G --> K
    
    style A fill:#90caf9
    style B fill:#90caf9
    style C fill:#90caf9
    style D fill:#a5d6a7
    style E fill:#a5d6a7
    style F fill:#a5d6a7
    style G fill:#a5d6a7
```

**Current State:**
- Claude Code integration hardcoded
- Generic mode limited
- Difficult to add new tools

**Target State:**
- Adapter pattern for tools
- Pluggable integrations
- Unified core interface
- Easy tool additions

### External Service Dependencies

```mermaid
graph LR
    A[Agent OS] --> B[GitHub API]
    A --> C[GitHub Raw Content]
    A --> D[curl/HTTP]
    
    E[Future API] --> F[CDN]
    E --> G[Database]
    E --> H[Authentication Service]
    E --> I[Analytics Service]
    
    style A fill:#ffcc80
    style E fill:#a5d6a7
```

---

## Refactoring Strategy

### Phase 1: Project-Only Installation

**Goal:** Remove `~/agent-os/` dependency

```mermaid
graph LR
    A[Current: Dual Install] --> B[Phase 1: Project-Only]
    B --> C[Bundle resources in installer]
    B --> D[Single config file]
    B --> E[Self-contained projects]
    
    style A fill:#ffcdd2
    style B fill:#fff9c4
    style C fill:#c8e6c9
    style D fill:#c8e6c9
    style E fill:#c8e6c9
```

**Key Changes:**
- Embed profiles in installation script
- Remove base directory dependency
- Simplify configuration
- Update path resolution

### Phase 2: Improved Modularity

**Goal:** Better separation and testability

```mermaid
graph LR
    A[Monolithic Scripts] --> B[Modular Libraries]
    B --> C[Config Module]
    B --> D[Template Module]
    B --> E[Install Module]
    B --> F[Validation Module]
    
    style A fill:#ffcdd2
    style B fill:#fff9c4
    style C fill:#c8e6c9
    style D fill:#c8e6c9
    style E fill:#c8e6c9
    style F fill:#c8e6c9
```

**Key Changes:**
- Extract functions to modules
- Add unit tests
- Standardize interfaces
- Enable reuse

### Phase 3: API Architecture

**Goal:** Modern, serverless architecture

```mermaid
graph LR
    A[Script-Based] --> B[API-Driven]
    B --> C[Profile Service]
    B --> D[Standards Service]
    B --> E[Template Service]
    B --> F[CLI Client]
    
    style A fill:#ffcdd2
    style B fill:#fff9c4
    style C fill:#81c784
    style D fill:#81c784
    style E fill:#81c784
    style F fill:#64b5f6
```

**Key Changes:**
- Build REST/GraphQL API
- Create CLI client
- Dynamic resource loading
- Cloud-based storage

---

## Scalability Analysis

### Current Scalability Limits

```mermaid
graph TD
    A[Scalability Factors] --> B[Number of Projects]
    A --> C[Number of Users]
    A --> D[Profile Complexity]
    A --> E[Standards Size]
    
    B -->|Limited by| F[Storage Duplication]
    C -->|Limited by| G[Manual Updates]
    D -->|Limited by| H[Script Complexity]
    E -->|Limited by| I[Copy Performance]
    
    style F fill:#ffcdd2
    style G fill:#ffcdd2
    style H fill:#ffcdd2
    style I fill:#ffcdd2
```

**Bottlenecks:**
- File copying overhead (O(n) per project)
- Storage grows linearly with projects
- Update propagation manual
- Script execution time

### Target Scalability

```mermaid
graph TD
    A[API Architecture] --> B[O(1) Installation]
    A --> C[Centralized Storage]
    A --> D[Cached Resources]
    A --> E[Automatic Updates]
    
    B --> F[Fast]
    C --> F
    D --> F
    E --> F
    
    style A fill:#c8e6c9
    style F fill:#81c784
```

**Improvements:**
- Constant-time operations
- Minimal storage per project
- CDN-cached resources
- Real-time updates

---

## Security Considerations

### Current Security Model

```mermaid
graph LR
    A[Download Scripts] -->|curl| B[GitHub]
    C[Execute Scripts] -->|bash| D[User Permissions]
    E[File Operations] -->|write| F[User Directories]
    
    style A fill:#fff9c4
    style C fill:#fff9c4
    style E fill:#fff9c4
```

**Security Posture:**
- Trust GitHub content
- Execute downloaded scripts
- User-level permissions
- No authentication
- No authorization

### Target Security Model

```mermaid
graph LR
    A[CLI Client] -->|HTTPS| B[API]
    B -->|Auth| C[Authentication Service]
    B -->|Verify| D[Signature Validation]
    E[Resources] -->|Signed| F[CDN]
    
    style B fill:#c8e6c9
    style C fill:#81c784
    style D fill:#81c784
    style F fill:#81c784
```

**Security Improvements:**
- API authentication
- Resource signing
- Version verification
- Access control
- Audit logging

---

## Migration Path

### Backward Compatibility Strategy

```mermaid
graph TD
    A[Current Users] --> B{Choose Mode}
    B -->|Legacy| C[Keep ~/agent-os/]
    B -->|New| D[Project-Only Mode]
    
    C --> E[Continue with scripts]
    D --> F[Use new installer]
    
    E --> G[Gradual Migration]
    G --> F
    
    style C fill:#fff9c4
    style D fill:#c8e6c9
    style F fill:#c8e6c9
```

**Migration Strategy:**
1. Support both modes simultaneously
2. Provide migration tool
3. Deprecate old mode gradually
4. Sunset old mode after transition period

### Version Management

```mermaid
graph LR
    A[v2.0.3 Current] --> B[v2.1.0 Project-Only]
    B --> C[v2.2.0 Modular]
    C --> D[v3.0.0 API]
    
    A -.->|Migration Tool| B
    B -.->|Gradual Upgrade| C
    C -.->|Dual Mode| D
    
    style A fill:#fff9c4
    style B fill:#c8e6c9
    style C fill:#c8e6c9
    style D fill:#81c784
```

---

## Related Documentation

- [Project Roadmap](./roadmap.md) - Overall strategy
- [Code Map](./codemap.md) - Component details
- [Workflows Analysis](./workflows.md) - Process flows
- [Configuration Guide](./config.md) - Config details
- [Refactoring Notes](./refactoring-notes.md) - Action items

---

**Last Updated:** 2025-10-13  
**Analysis Version:** 1.0  
**Source Repository:** https://github.com/buildermethods/agent-os
