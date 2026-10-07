---
title: "Declarative SQL Refactoring: Designing a Reliable Inventory Calculation Engine"
layout: default
tags: [database-design, sql-server, refactoring, relational-algebra, sql-optimization, system-architecture, domain-modeling, t-sql]
---

## I. Background

1. **Business Changes Broke Legacy Calculations**: The original reserved inventory calculation worked as intended when first released. But as the business evolved, warehouse processes shifted and how the team used certain tables and fields drifted from their initial design. Over time, the legacy query began producing inaccurate inventory numbers and could no longer support daily operations.
    
2. **Team Leader's Suggestion**: While fixing the bug, my Team Leader suggested taking a step back to see if we could redesign the overall query to make it fundamentally cleaner and more reliable.
    
## II. Problems with the Legacy Implementation

### 1. Database Engine & Performance Bottlenecks

- **No Observability & Risk of Plan Drift**: The query relied heavily on dynamically concatenated SQL strings. Because runtime filters constantly changed the SQL text, the query had no stable Query Hash, making it almost impossible to aggregate performance telemetry in production. Furthermore, these dynamic query shapes created theoretical risks of Cardinality Estimation (CE) errors and unpredictable execution plan flipping.
    
- **Plan Cache Bloat**: Every unique combination of parameters generated a slightly different dynamic SQL string. This flooded the plan cache with single-use compiled plans and wasted memory.
    
- **Unpredictable Optimizer Behavior**: A massive, monolithic dynamic query turned the query optimizer into a black box, making execution behavior difficult to inspect or predict.
    
- **Buffer Cache Thrashing & Disk Read Spikes**: As data volume grew, uncontrolled query plans threatened to exhaust the buffer cache, creating risks of sudden spikes in physical disk reads and query timeouts.

### 2. Architecture & Maintainability Issues

- **Tangled Business Logic vs. Workarounds**: Successive maintainers could not tell which parts of the query were actual business rules and which were historical bug fixes or workarounds.
    
- **Fragmented Logic**: Monolithic SQL syntax forced single business rules to split across completely different parts of the query: some parts sat in `JOIN ... ON` conditions, others were buried in dynamic `WHERE` clauses, and others were tangled inside nested `SELECT ... CASE WHEN` statements. Keeping track of this fragmentation required excessive mental overhead during maintenance.
    
- **Blind Spots in Code Review & Hard to Test**: String concatenation and escaped quotes in dynamic SQL made code reviews error-prone—typos easily slipped through. It also prevented clean, deterministic unit testing across different business paths.

## III. Core Architecture & Design Principles

1. **Clarify Business Invariants First**: Before writing any SQL, extract all business rules into a complete, explicit list of constraints.
    
2. **Set-Based Joins Over Procedural Branching**: Relational database engines handle set operations (`JOIN`) far better than row-by-row procedural branching. Wherever possible, procedural `WHERE` conditions were rewritten into declarative joins against rule and driver tables.
    
3. **Clear Boundary Between Logic and Physical Execution**:
    - The engineer understands the overall business domain and actively designs the data pipeline and materialization boundaries.
    - The database optimizer does not understand business domain rules. Its search space is deliberately constrained to local subsets so it can focus strictly on what it does best: finding optimal physical access paths (e.g., index seek vs. scan).
        
4. **Unified Input Context (`@ctx`)**:
    - Collects all separate input parameters into a Single Source of Truth.
    - Shields the core engine from varying inputs while providing a single, clear inspection point for debugging and parameter validation.
        
5. **Staged Processing Pipeline (Three-Layer Table Variables)**:
    - Breaks the main calculation pipeline into three distinct stages using table variables (`@TableVariable`) as intermediate carriers. This decouples downstream consumption and provides clean inspection points:
        - **Layer 1: Line-item Filtering**: Filters valid order line items against `@ctx`.
        - **Layer 2: Master Attribute Enrichment**: Joins items against rule tables to attach master dimensional metadata.
        - **Layer 3: Cascading Stock Calculations**: Calculates on-hand, reserved, and available stock tiers using deterministic relational operations.

## IV. Mathematical Formulation & Algebraic Convergence

1. **Mental Model of Architectural Convergence**:
    - Architectural deduction does not stem from trial-and-error scaffolding; it functions by projecting all business invariants simultaneously into memory. Multi-dimensional constraints undergo implicit cross-boundary pruning, converging directly onto the optimal relational topology, which made the architectural synthesis exceptionally fast.
        
2. **Alignment with Relational Algebra**:
    The resulting execution topology strictly aligns with relational principles, eliminating procedural indeterminism:
    - **Composition of Relations Over Conditional Branching**: Dynamic branching paths are converted into Natural Joins ($\Join$), stabilizing execution plan geometry.
    - **Active Dimension Reduction (Pushdown Restrictions & Projections)**: Staged materialization boundaries intercept unbounded Cartesian spaces, pruning the search space to localized intersections ($\sigma$).
    - **Deterministic Cost Envelope**: By locking query topology, physical execution costs collapse into linear/logarithmic bounds ($O(N)$ / $O(\log N)$), eliminating latency variance under load.

## V. System Runtime Architecture

1. **Rule & Matrix Layer**: Pre-computed static business rules and routing matrices.
2. **Unified Request Context (`@ctx`)**: Standardized parameter parsing and boundary filtering.
3. **Three-Tier Calculation Pipeline**:
    - **Layer 1**: Line-item selection, predicate pushdown, and cardinality reduction.
    - **Layer 2**: Master data dimension enrichment and rule joins.
    - **Layer 3**: Cascading inventory allocation and reservation ledgering.
4. **Final Aggregation**: Resolves multi-warehouse settlement totals and outputs final domain fields.
5. **Post-Calculation Filter**: Checks derived flags on calculated results (e.g., flagging unfulfillable items).

## VI. Implementation Patterns & Observability

### 1. Rule & Matrix Model

- **Configuration Flattening for Warehouse Settlement Rules**:
    - **Table 1 (Human-Maintained Rules)**: Holds high-level business definitions, such as Warehouse Tags (e.g., Tag 1 = Primary WH Attribute A, Tag 2 = Primary WH Attribute B, Tag 3 = Vendor WH Attribute A, Tag 4 = Vendor WH Attribute B) and material compatibility rules.
    - **Table 2 (Flattened Mapping Table)**: Whenever configuration updates, the system automatically expands Table 1 into an exhaustive, flat lookup table. This turns potential procedural branching into simple, static lookups. Humans maintain Table 1; the system generates Table 2.
    - **Practical Benefits**:
        - Replaces procedural `IF/ELSE` checks with deterministic equi-joins.
        - Simplifies testing and auditing: The engine runs a full flattened calculation first, making it easy to inspect intermediate numbers across all branches. It then aggregates totals by Tag and combines them with `@ctx` to output the final stock.
            
- **Multi-Dimensional Range Matching via Intervals**:
    - Replaced cryptic magic flags (`-1 / 0 / 1`) with continuous, explicit intervals (`[min, max]`).
    - **Practical Benefits**:
        - Matches delivery rules using range containment against `@ctx`.
        - Enables efficient range joins with driver tables to filter eligible materials.

### 2. Calculation Layer

- **Priority Selection via `APPLY` Operators**:
    - Uses `CROSS APPLY` / `OUTER APPLY` over ranked rule sets to dynamically pick the highest-priority material attribute without cursor loops, offering high extensibility.
    - The same mechanism is reused in downstream validations, such as determining the exact reason why an order cannot be shipped.

### 3. Debugging & Observability

- **Empirical Tuning with `SET STATISTICS IO` and On-the-Fly AI Guidance**:
    - Once the initial draft was complete, I enabled `SET STATISTICS IO` to monitor logical reads across each query segment.
    - I knew the command existed as a profiling tool, but had not previously interpreted its detailed metrics in depth. I used an AI to learn the exact physical mechanics behind "Logical Reads" on the spot.
    - With a clear understanding of the numbers, I fine-tuned operators with abnormal overhead (such as reordering driving tables and eliminating redundant table scans), reducing logical reads and protecting against future scaling issues.
        
- **Global Error Handling with `TRY...CATCH` and Context Capture**:
    - Wrapped the execution logic in a structured `TRY...CATCH` block.
    - When an error occurs at runtime, the block captures the active SQL context and outputs the specific failing code block alongside the error message.
    - This solved the common SQL Server issue where native error line numbers are misleading or hard to track down, allowing immediate pinpointing of what failed and where.

## VII. Engineering Trade-offs & Team Alignment

### 1. Table Aliasing

- **The Trade-off**:
    - **Legacy Single-Letter Aliases (`a-z`)**: The monolithic query joined dozens of tables in a single massive scope, forcing developers to use single-letter aliases (`a, b, c...z`) simply to prevent naming collisions. This was essentially patching over scope bloat.
    - **Self-Documenting Scopes**: With modular stages, each step is short and touches very few entities. Using full domain table names makes the code self-documenting, eliminating the need to look up what `d` represents.
- **Outcome**:
    - At the time, my own naming conventions for the refactor were not fully settled. I followed the team's preference and kept the existing single-letter aliasing style.

### 2. Sorting Location

- **The Trade-off**:
    - I initially used temporary tables (`#TempTable`) in SQL to enforce deterministic sorting.
    - The team advised against temporary tables, believing they "remain trapped in cache indefinitely." I was unconvinced, but lacking the low-level data to challenge the claim on the spot, I went along with the team.
- **Outcome**:
    - I set aside the temporary table approach in SQL and adopted the team's suggestion, moving the sorting logic into the application layer.

### 3. Intermediate Storage Mechanism

- **The Trade-off**:
    - Team standards required table variables (`@TableVariable`).
    - I researched the underlying differences between table variables and temporary tables (such as how SQL Server handles statistics), but found no compelling evidence that temporary tables would perform significantly better in our specific workload.
- **Outcome**:
    - I adhered to team standards and kept table variables as the intermediate carrier across all pipeline stages.

## VIII. Technical Deep Dive: Eliminating `CASE WHEN` via `APPLY + VALUES`

To avoid deeply nested `CASE WHEN` statements, conditional logic was rewritten into set-oriented operations using `CROSS APPLY` / `OUTER APPLY` alongside row constructors (`VALUES`). This pattern was applied in three distinct scenarios:

### 1. Structural Layer: Dynamic Column-to-Row Inversion (Unpivot)

- **Mechanism & Implementation**:
    - Deconstructs horizontal multi-column layouts into normalized `(MatchTag, Val)` tuples on the fly.
    - Transforms runtime conditional column access into static equi-join predicates (`WHERE c.MatchTag = @ctx.RequiredTag`).
        
- **Pattern**:
    
    ```sql
    CROSS APPLY (
        SELECT c.Val
        FROM (
            VALUES 
                ('TAG_A', d.ColA),
                ('TAG_B', d.ColB)
        ) c(MatchTag, Val)
        WHERE c.MatchTag = @ctx.RequiredTag
    ) resolved
    ```
    
- **Relational Model & Algebraic Significance**:
    - **Eliminating Column-Level Coupling**: Relational optimizers are architected for row-level predicate evaluation, not runtime jumping across distinct column schemas. Local inversion normalizes ad-hoc column branching into standard First-Order restrictions ($\sigma$) and joins ($\Join$).
    - **Preserving Static Topology in Relational Closures**: Prevents schema-level polymorphism from leaking upward, maintaining a static topology across intermediate relational closures.

### 2. Control Flow Layer: Priority Fallbacks

- **Mechanism**:
    - **Layer 2 Tagging**: Derives business tags from material attributes and delivery modes earlier in the pipeline.
    - **Layer 3 Arbitration**: Builds an ordered candidate set `(Priority, SourceVal)` via `APPLY (VALUES ...)`. Grabbing the top candidate flattens nested procedural fallback chains:
        - *Priority 1*: Warehouse explicitly specified on the line item.
        - *Priority 2*: Target warehouse derived from Layer 2 rules.
            
- **Use Cases**:
    - Checks whether a reserved stock item counts toward the current ledger.
    - Flattens nested "use explicit value if available, otherwise fall back to rule default" checks into clean row selection, eliminating nested `IF...ELSE` logic from aggregation queries.

### 3. Domain Layer: Mapping Validation Status to Clear Reasons

- **Mechanism**:
    - Takes boolean flags computed during Layer 3 checks and maps them into an ordered list of human-readable failure reasons via `APPLY (VALUES ...)`:
        
- **Pattern**:
    
    ```sql
    OUTER APPLY (
        SELECT TOP 1 Reason
        FROM (
            VALUES 
                (IsStockShort,  'Insufficient Stock', 1),
                (IsInvalidAttr, 'Incompatible Material Attributes', 2)
        ) r(IsHit, Reason, Priority)
        WHERE r.IsHit = 1
        ORDER BY r.Priority
    ) unship_reason
    ```
    
- **Use Cases**:
    - When an item cannot be shipped, cleanly pulls the highest-priority root cause.
    - Completely decouples validation logic from human-readable text. Changing error messages or updating priorities requires no changes to the upstream calculation pipeline.