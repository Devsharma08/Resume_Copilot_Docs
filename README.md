# Career Copilot Documentation

This folder contains the product, architecture, backend, database, future-scope, and research documentation for Career Copilot.

## Documentation Map

### 1. Product and Architecture

Start with [Project Context](projectContext.md). It is the current source of truth for the MVP vision, core features, technology stack, application architecture, project structure, database concepts, milestones, and architecture decisions.

### 2. Backend Implementation

Read [Backend Services](backend.md) for the backend module layout, technical stack, database-model ownership, and implementation boundaries.

### 3. MVP Data Model

Read [Database Design](database.md) for the normalized PostgreSQL model, entity relationships, fields, JSONB usage, and MVP database boundaries.

### 4. Future Product Scope

Read [Future Scope: AI Career Mentor](futureScope.database.md) after the MVP documents. It describes the separate long-term career-growth product, its modules, AI pipelines, and possible database entities.

### 5. Research and Governance

The [Research Index](research_list/README.md) organizes the research behind governed candidate selection, evidence provenance, deterministic policy enforcement, uncertainty routing, and closeness metrics.

## Recommended Reading Order

1. [Project Context](projectContext.md)
2. [Backend Services](backend.md)
3. [Database Design](database.md)
4. [Future Scope: AI Career Mentor](futureScope.database.md)
5. [Research Index](research_list/README.md)

## Scope Boundaries

- **Career Copilot MVP:** resume management, resume analysis, job matching, cover letters, and application tracking.
- **AI Career Mentor:** a future product domain for skill development, roadmaps, progress, market intelligence, and interview preparation.
- **Governed AI research:** a research track that can inform future decision and candidate-selection capabilities; it is not currently part of the MVP schema.

## Maintenance Rules

- Update [Project Context](projectContext.md) when a major product or architecture decision changes.
- Update [Database Design](database.md) when an MVP entity, field, relationship, or persistence rule changes.
- Update [Backend Services](backend.md) when backend modules or implementation ownership changes.
- Keep future ideas in [Future Scope: AI Career Mentor](futureScope.database.md) until they are explicitly brought into the MVP.
- Keep experiments and research notes under [research_list](research_list/).
