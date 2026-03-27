# Lineage — Genealogy AI & Data Platform

## Backend

- **Java + Spring Boot** — application framework
- **Maven** — build and dependency management
- **Spring Data JPA / Hibernate** — ORM and repository layer for persons, relationships, photos and notes
- **Spring AI** — integration layer for Ollama (chat model) and pgvector (vector store)

## Frontend

- **React + TypeScript + Vite** — UI framework and build tooling
- **Bun** — package manager and runtime
- **Tailwind CSS** — utility-first styling
- **shadcn/ui** — accessible component library built on Tailwind and Radix UI
- **React Flow** — interactive family tree graph visualization
- **Axios** — HTTP client for backend API calls

## Database

PostgreSQL is the primary store for all structured data. The pgvector extension adds
vector similarity search, enabling natural language queries without a separate vector database.

- Biographical fields are encrypted at rest using column-level encryption via JPA attribute
  converters (AES); encryption and decryption are handled transparently in the application layer
- Persons are stored as relational records with biographical fields:
    - `first_name`
    - `middle_name`
    - `last_name`
    - `place_of_birth`
    - `date_of_birth`
    - `place_of_death`
    - `date_of_death`

- Relationships are stored as directional typed edges (`parent_of`, `spouse_of`, `sibling_of`)
  in a join table; `child_of` is derived at query time as the inverse of `parent_of`
- Photos are tracked as records referencing filesystem paths, with a primary-photo flag per person
- Notes are stored as free-form text records associated with a person
- Basic search filters on family name, birth year and place of birth/death are served by standard
  relational queries
- Ancestor/descendant traversal uses PostgreSQL recursive CTEs — sufficient for personal
  genealogy scale without a dedicated graph database
- pgvector stores and queries embeddings for natural language search via Spring AI

## AI

All AI runs locally via **Ollama** — no data leaves the machine.

- **Biographical summary generation** — a person's notes are passed to the Ollama chat model
  to produce a structured summary; no embeddings required
- **Natural language search** — person fields and notes are embedded and stored in pgvector;
  queries are embedded at runtime and matched via similarity search (Spring AI vector store);
  queries are expressed in Hungarian

> Hungarian language support: choose an Ollama model with strong multilingual capability
> (e.g. `llama3`) and verify Hungarian-language output quality before
> committing to a specific model.

## File Storage

- **Local filesystem** — photos stored as flat files organized by person ID; file paths
  referenced in the database

## Deployment

- Local / self-hosted; all services run on the user's machine
- Single user — no authentication or multi-user roles required
- Full data ownership and privacy; no cloud dependency
