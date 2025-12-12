# System Architecture

This document describes the architectural structure of the Pokemon Type Network Graph project.
It explains how responsibilities are divided across the system and why this structure was chosen.

The design follows a backend first approach, with the frontend treated as a consumer of graph views
and metrics rather than a source of logic.

---

## High Level Architecture

The system is split into three major parts:

1. Static data layer
2. Backend graph and analysis service
3. Frontend visualization client

Each part has a clearly defined role and minimal coupling to the others.

---

## Data Layer

The data layer consists of static files describing Pokemon Generation 1 type interactions.

Characteristics:
- Read only
- Fully explicit (all attacker to defender pairs stored)
- No logic or computation

The backend treats this data as the single source of truth.

---

## Backend Architecture

The backend is implemented using FastAPI and is structured as a layered system.

### Core Principles

- The canonical graph is built once at startup
- The canonical graph is immutable
- All analysis is performed on derived graph views
- The API layer does not contain graph logic

---

### Backend Layers

#### 1. Data Module
Responsible for loading static Gen 1 type and interaction data from disk.

This layer:
- Knows nothing about graphs
- Knows nothing about APIs
- Only provides raw data structures

---

#### 2. Graph Construction Layer
Responsible for converting raw data into a canonical directed, weighted graph.

Responsibilities:
- Create one node per Pokemon type
- Create directed edges for all attacker to defender pairs
- Attach multipliers and immunity flags
- Store the graph in memory

The canonical graph is built once and cached.

---

#### 3. Graph View Layer
Responsible for producing filtered graph views from the canonical graph.

Supported filters:
- offense only
- defense only
- include neutral
- immunity only

This layer:
- Never mutates the canonical graph
- Produces lightweight derived views
- Contains no metric logic

---

#### 4. Metrics Layer
Responsible for computing network metrics on derived graph views.

Metrics include:
- in degree
- out degree
- weighted centrality

This layer:
- Operates only on graph views
- Contains no API or HTTP logic
- Can be tested independently

---

#### 5. API Layer
Responsible for exposing graph views and metrics via HTTP endpoints.

Responsibilities:
- Parse query parameters
- Select graph views
- Call metric functions
- Return structured JSON responses

The API layer does not know how graphs are built or analyzed.

---

## Frontend Architecture

The frontend is implemented using Next.js and runs entirely in the browser.

### Responsibilities

- Request graph views and metrics from the backend
- Render a 3D interactive network using a Three.js based force graph library
- Encode graph meaning using color, size, thickness, and direction
- Provide UI controls for the supported filters

### Non Responsibilities

- No graph construction
- No graph analysis
- No metric computation

---

## Visualization Strategy

The 3D layout is purely visual and physics based.
Graph meaning is conveyed through:
- edge direction
- edge thickness
- edge color
- node size
- interaction (hover, click, filtering)

No semantic meaning is encoded in node distance.

---

## Data Flow Summary

Startup:
1. Load static data
2. Build canonical graph
3. Cache graph in memory

Request handling:
1. Parse filters
2. Generate derived graph view
3. Compute metrics
4. Return response

---

## Scalability and Extensibility

This architecture supports:
- Adding new generations by extending the data module
- Adding new metrics without changing storage
- Adding Pokemon level nodes as a new graph layer
- Adding new visualizations without backend changes
