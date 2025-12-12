# Architectural Decisions and Trade Offs

This document records key design decisions made during the project,
along with the reasoning behind them.

The goal is to make trade offs explicit and defensible.

---

## Use a Graph Model

Decision:
Model Pokemon type interactions as a directed graph.

Reasoning:
- Naturally represents attacker to defender relationships
- Supports formal graph analysis
- Aligns with network theory learning goals

Alternative:
Matrix only representation.

Why rejected:
- Harder to extend
- Less expressive for analysis and visualization

---

## One Node per Type

Decision:
Use one node per Pokemon type.

Reasoning:
- Types are single entities
- Offense and defense are roles, not entities
- Direction belongs on edges, not nodes

Alternative:
Separate attacker and defender nodes.

Why rejected:
- Artificially doubles node count
- Breaks interpretation of metrics
- Adds complexity without benefit

---

## Directed Edges for All Interactions

Decision:
Store every attacker to defender interaction explicitly as a directed edge.

Reasoning:
- Ensures complete and explicit data
- Avoids implicit assumptions
- Enables flexible filtering later

Trade Off:
Creates a dense canonical graph.

Mitigation:
Use filtered graph views for analysis and visualization.

---

## Include Neutral Interactions in Canonical Data

Decision:
Store neutral (1.0) interactions explicitly.

Reasoning:
- Preserves complete domain knowledge
- Avoids special casing neutral logic
- Enables experiments with and without neutral edges

Trade Off:
Including neutrals in analysis reduces structural signal.

Mitigation:
Make neutral inclusion a filter choice.

---

## Layered Graph Views

Decision:
Separate canonical graph storage from derived graph views.

Reasoning:
- Prevents accidental mutation
- Keeps storage simple
- Makes analysis configurable per request

Alternative:
Store multiple graphs for each view.

Why rejected:
- Data duplication
- Harder to maintain
- Unnecessary for small graphs

---

## Weighted Centrality Using Multipliers

Decision:
Use effectiveness multipliers as edge weights for centrality.

Reasoning:
- Reflects strength of interactions
- Aligns with domain intuition
- More informative than unweighted metrics

Trade Off:
Requires careful interpretation and normalization.

Mitigation:
Document metric meaning per filter configuration.

---

## Immunity as a Special Case

Decision:
Treat immunity (multiplier 0) as a special flagged edge.

Reasoning:
- Zero weights do not behave well in weighted centrality
- Immunity represents hard constraints, not weak influence

Mitigation:
Expose immunity only as its own filtered view.

---

## Build Graph Once and Cache in Memory

Decision:
Build the canonical graph once at application startup.

Reasoning:
- Data is static
- Simplifies request handling
- Reflects real world backend patterns

Alternative:
Rebuild graph per request.

Why rejected:
- Repeated work
- Less realistic architecture

---

## No Database for MVP

Decision:
Use in memory data only.

Reasoning:
- Dataset is small and static
- Focus is on graph theory, not persistence
- Reduces complexity

Extension Path:
Add persistence only if dynamic data is introduced.

---

## Use Existing 3D Graph Libraries

Decision:
Use Three.js based graph libraries for visualization.

Reasoning:
- Avoids reinventing rendering and physics
- Keeps focus on graph modeling and analysis
- Reflects practical engineering choices

Trade Off:
Less control over layout internals.

Accepted because:
Layout is visual only and not semantically meaningful.

---

## Backend First Development

Decision:
Design and implement backend before frontend.

Reasoning:
- Forces clear domain modeling
- Avoids UI driven shortcuts
- Produces a reusable analysis service

---

## Summary

These decisions prioritize:
- conceptual clarity
- learning value
- extensibility
- realistic engineering practices

They intentionally trade raw performance and feature breadth
for correctness, explainability, and maintainability.
