# Lineage — Genealogy AI & Data Platform

## Development Workflow

### Branching Strategy: GitHub Flow

`main` is always stable and deployable. All work happens on short-lived branches that merge back into `main` via pull
request.

### Branch Types

| Branch      | Lifetime  | Purpose                                           |
|-------------|-----------|---------------------------------------------------|
| `main`      | permanent | Always deployable — all PRs merge here            |
| `feature/*` | ephemeral | New functionality — merges into `main`            |
| `fix/*`     | ephemeral | Bug fixes — merges into `main`                    |
| `chore/*`   | ephemeral | Maintenance, config, tooling — merges into `main` |

### Naming Conventions

Branches follow the pattern `{type}/phase-{n}-{slug}` or `{type}/{slug}` for cross-cutting work:

```
feature/phase-1-project-scaffolding
feature/phase-2-person-crud
feature/phase-3-relationships
feature/phase-4-photos-and-notes
feature/phase-5-family-tree-visualization
feature/phase-6-biographical-summary
feature/phase-7-natural-language-search
fix/photo-upload
chore/update-dependencies
```

### Commit Messages: Conventional Commits

Prefix types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`, `build`, `revert`

---

## Phase 1 — Project Scaffolding

**Goal:** Runnable skeleton for both backend and frontend with local infrastructure in place.

### Backend

- Initialize Spring Boot project (Maven, Java 21)
- Dependencies: `spring-boot-starter-web`, `spring-boot-starter-data-jpa`, `postgresql`, `flyway-core`
- Configure `application.yaml`: datasource, JPA, Flyway

### Frontend

- Initialize React + TypeScript + Vite project with Bun (`bun create vite`)
- Install and configure Tailwind CSS and shadcn/ui
- Configure ESLint and Prettier
- Install Axios; create a pre-configured `axios` instance with `baseURL` pointing to the backend

### Infrastructure

- Run PostgreSQL locally (Docker or native)
- Enable `pgvector`: `CREATE EXTENSION IF NOT EXISTS vector;`
- Verify backend connects and Flyway baseline migration runs

---

## Phase 2 — Person CRUD

**Goal:** Full CRUD for person PII data via REST API, with all biographical fields encrypted at rest.

### Backend

- AES encryption key management (application property, never committed to source control)
- Generic JPA `AttributeConverter` — encrypts on write, decrypts on read; applied via `@Convert`
  on each biographical field
- Flyway migration: `persons` table (`id`, `first_name`, `middle_name`, `last_name`,
  `place_of_birth`, `date_of_birth`, `place_of_death`, `date_of_death`, `created_at`, `updated_at`)
  — all biographical columns store ciphertext
- `Person` JPA entity + repository
- `PersonService` + `PersonController`:
    - `GET /api/persons` — list / search (name, birth year, place)
    - `GET /api/persons/{id}`
    - `POST /api/persons`
    - `PUT /api/persons/{id}`
    - `DELETE /api/persons/{id}`

### Frontend

- Person list page with search/filter bar (name, birth year, place)
- Person detail page (view + edit form), create form, delete confirmation

---

## Phase 3 — Relationships

**Goal:** Store and query directional typed links between persons.

### Backend

- Flyway migration: `relationships` table (`id`, `from_person_id`, `to_person_id`, `type`)
- Relationship types: `PARENT_OF`, `SPOUSE_OF`, `SIBLING_OF`; `child_of` derived at query time
- `RelationshipService` + `RelationshipController`:
    - `GET /api/persons/{id}/relationships`
    - `POST /api/persons/{id}/relationships`
    - `DELETE /api/relationships/{id}`

### Frontend

- Relationship panel on the person detail page (person picker + type selector)

---

## Phase 4 — Photos & Notes

**Goal:** Attach photos and free-form notes to persons.

### Backend — Photos

- Flyway migration: `photos` table (`id`, `person_id`, `file_path`, `is_primary`, `uploaded_at`)
- `PhotoService`: store file to filesystem by person ID, enforce single primary flag
- `PhotoController`: `POST /api/persons/{id}/photos`, `DELETE /api/photos/{id}`,
  `PUT /api/photos/{id}/primary`

### Backend — Notes

- Flyway migration: `notes` table (`id`, `person_id`, `content`, `created_at`, `updated_at`)
- `NoteService` + `NoteController`: full CRUD on `/api/persons/{id}/notes` and `/api/notes/{id}`

### Frontend

- Photo gallery on person detail page (upload, delete, set primary)
- Notes section (add, edit, delete)

---

## Phase 5 — Family Tree Visualization

**Goal:** Render and navigate the connected person graph.

### Backend

- `GET /api/persons/{id}/tree` — subgraph (nodes + edges) via recursive CTE, configurable depth

### Frontend

- React Flow graph: person card nodes, labeled relationship edges
- Click node → navigate to person detail; zoom, pan, center controls

---

## Phase 6 — AI: Biographical Summary Generation

**Goal:** Generate a structured biographical summary from a person's notes via a local LLM.

### Infrastructure

- Install Ollama; pull a multilingual chat model (e.g. `mistral`, `llama3`, or `qwen2`)
- Verify Hungarian-language output quality before committing to a model

### Backend

- Add `spring-ai-ollama-spring-boot-starter`
- `POST /api/persons/{id}/summary` — decrypt and concatenate notes, pass to Ollama chat model,
  return summary; no embeddings required

### Frontend

- "Generate summary" action on person detail page

---

## Phase 7 — AI: Natural Language Search

**Goal:** Query the family tree in plain Hungarian via a local LLM.

### Infrastructure

- Pull an embedding model into Ollama (e.g. `nomic-embed-text`)

### Backend

- Flyway migration: add `embedding vector(768)` column to `persons`
- On person create/update: decrypt biographical fields, generate embedding, store in `pgvector`
- `GET /api/search?q=...` — embed query via Ollama, run `pgvector` similarity search,
  return ranked results

### Frontend

- Global natural language search bar with relevance-ranked results

---

## Milestones

| Phase | Deliverable                          | Release  |
|-------|--------------------------------------|----------|
| 1     | Runnable skeleton + DB connected     | `v1.0.0` |
| 2     | Person CRUD + encryption             | `v1.0.0` |
| 3     | Relationships                        | `v1.0.0` |
| 4     | Photos + notes                       | `v1.0.0` |
| 5     | Family tree visualization            | `v1.0.0` |
| 6     | Biographical summary generation (AI) | `v1.0.0` |
| 7     | Natural language search (AI)         | `v1.0.0` |
