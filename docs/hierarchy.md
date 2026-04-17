# SHiP Data Model Hierarchy

## Data Acquisition

Real data flows from data-taking runs through to global time frames. The detector is read out continuously over each spill (~1 s) without a trigger. Each subsystem produces adaptive-size time windows at irregular intervals, which are then combined across subsystems into global time frames.

```mermaid
flowchart TD
    Run["<b>Run</b><br/><i>data-taking period</i>"]
    Spill["<b>Spill</b><br/><i>~1 s beam extraction</i>"]
    TW["<b>TimeWindow</b><br/><i>per subsystem, adaptive size</i>"]
    TF["<b>TimeFrame</b><br/><i>global, combined across subsystems</i>"]

    Run -->|"1 : N"| Spill
    Spill -->|"1 : N per subsystem"| TW
    TW -->|"N : M"| TF

    style Run fill:#fff3cd,stroke:#d4a843,color:#333
    style Spill fill:#fff3cd,stroke:#d4a843,color:#333
    style TW fill:#d1ecf1,stroke:#0c5460,color:#333
    style TF fill:#c3e6cb,stroke:#28a745,color:#333
```

The time window to time frame relationship is N:M because the adaptive time windows from different subsystems do not necessarily align: a single time window may contribute to more than one time frame, and each time frame combines time windows from multiple subsystems.

## Below the Time Frame

The time frame is the convergence point between simulated and real data. Below it, two parallel trees exist: MC truth (present only in simulation) and reconstructed data.

Reconstruction breaks each time frame into algorithm-defined regions of interest, which are further resolved into individual events. On the MC truth side, each true collision fans out into its constituent particles and hits.

Several true collisions can contribute to a single reconstructed event (due to pile-up).

```mermaid
flowchart TD
    TF["<b>TimeFrame</b><br/><i>global</i>"]

    subgraph mc ["MC Truth (simulation only)"]
        direction TB
        Collision["<b>MC Collision</b><br/><i>true interaction</i>"]
        MCParticle["MCParticle"]
        SimHit["SimHit"]
        SimParticle["SimParticle"]
        Collision -->|"1 : N"| MCParticle
        Collision -->|"1 : N"| SimHit
        Collision -->|"1 : N"| SimParticle
    end

    subgraph reco ["Reconstructed"]
        direction TB
        ROI["<b>Region of Interest</b><br/><i>algorithm-defined</i>"]
        Event["<b>Reco Event</b>"]
        Track["Track"]
        Vertex["Vertex"]
        Digit["Digit"]
        ROI -->|"1 : N"| Event
        Event -->|"1 : N"| Track
        Event -->|"1 : N"| Vertex
        Event -->|"1 : N"| Digit
    end

    TF --> mc
    TF --> reco

    Collision -. "N : 1 association" .-> Event

    style TF fill:#c3e6cb,stroke:#28a745,color:#333
    style mc fill:#fce4ec,stroke:#c62828,color:#333
    style reco fill:#e8eaf6,stroke:#3f51b5,color:#333
    style Collision fill:#ef9a9a,stroke:#c62828,color:#333
    style MCParticle fill:#ffcdd2,stroke:#e57373,color:#333
    style SimHit fill:#ffcdd2,stroke:#e57373,color:#333
    style SimParticle fill:#ffcdd2,stroke:#e57373,color:#333
    style ROI fill:#c5cae9,stroke:#3f51b5,color:#333
    style Event fill:#9fa8da,stroke:#3f51b5,color:#333
    style Track fill:#e8eaf6,stroke:#7986cb,color:#333
    style Vertex fill:#e8eaf6,stroke:#7986cb,color:#333
    style Digit fill:#e8eaf6,stroke:#7986cb,color:#333
```

## Simulation Data Flow

In simulation, individual proton-on-target collisions are generated independently. Before digitisation, they are combined over the time window to model pile-up. After digitisation at the time frame level, the simulated data is indistinguishable from real global time frames and follows the same reconstruction path.

```mermaid
flowchart LR
    C1["Collision₁"]
    C2["Collision₂"]
    C3["Collision₃"]
    Cn["Collision_n"]

    Combine["combine /<br/>pile-up"]
    TF["<b>TimeFrame</b><br/><i>digitisation</i>"]

    C1 --> Combine
    C2 --> Combine
    C3 --> Combine
    Cn --> Combine
    Combine --> TF

    TF -->|"identical to<br/>real data"| Reco["Reconstruction"]

    style C1 fill:#ef9a9a,stroke:#c62828,color:#333
    style C2 fill:#ef9a9a,stroke:#c62828,color:#333
    style C3 fill:#ef9a9a,stroke:#c62828,color:#333
    style Cn fill:#ef9a9a,stroke:#c62828,color:#333
    style Combine fill:#ffe0b2,stroke:#e65100,color:#333
    style TF fill:#c3e6cb,stroke:#28a745,color:#333
    style Reco fill:#e8eaf6,stroke:#3f51b5,color:#333
```
