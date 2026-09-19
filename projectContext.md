# Career Copilot - Project Context

> This document serves as the single source of truth for the project's goals, architecture, technology stack, and core database design. It should be updated whenever major architectural decisions are made.

---

# 1. Project Goals

## Vision

Build an AI-powered Career Copilot that helps students and professionals improve their resumes, optimize them for specific jobs, identify skill gaps, and prepare for job applications.

---

## Core Features (Phase 1)

* User Authentication
* Resume Upload (PDF/DOCX)
* Resume Parsing
* ATS Score Analysis
* AI Resume Review
* Resume Versioning
* Resume Optimization
* Job Description Upload
* Resume ↔ Job Compatibility Analysis
* AI Cover Letter Generation

---

## Planned Features (Future)

* Personalized Skill Roadmaps
* LinkedIn Profile Analysis
* GitHub Profile Analysis
* Portfolio Review
* Interview Question Generator
* Application Tracker
* AI Career Coach
* Recruiter Feedback Mode
* Analytics Dashboard

---

# 2. High-Level Architecture

```text
Next.js
    │
    ▼
FastAPI
    │
    ▼
SQLAlchemy ORM
    │
    ▼
PostgreSQL
```

Supporting Services

* Docker
* DBeaver CE
* Gemini API
* Alembic
* pgvector (future)

---

# 3. Technology Stack

## Frontend

* Next.js
* TypeScript
* Tailwind CSS
* shadcn/ui
* TanStack Query
* React Hook Form
* Zod
* Framer Motion
* Recharts

---

## Backend

* Python
* FastAPI
* SQLAlchemy 2.x
* Alembic
* Pydantic
* python-dotenv
* psycopg2

---

## Database

* PostgreSQL
* JSONB
* pgvector (planned)

---

## AI

* Google Gemini API

Planned

* Embeddings
* Resume Analysis
* Cover Letter Generation
* Skill Gap Analysis
* Career Roadmap Generation

---

## Infrastructure

* Docker Compose
* PostgreSQL Container
* DBeaver CE

Future

* Redis
* MinIO
* Celery

---

# 4. Project Structure

```text
career-copilot/

├── frontend/
├── backend/
├── docs/
├── infra/
├── scripts/
└── docker-compose.yml
```

Backend

```text
backend/

app/

    api/

    ai/

    core/

    database/

    middleware/

    models/

    repositories/

    schemas/

    services/

    utils/

    workers/
```

---

# 5. Database Design

## User

Purpose

Stores user account information.

Relationship

* One User → Many Resumes
* One User → Many Applications
* One User → Many Skill Roadmaps

---

## Resume

Purpose

Represents a logical resume owned by a user.

Relationship

* Belongs to one User
* Has many Resume Versions

---

## ResumeVersion

Purpose

Stores immutable snapshots of resumes.

Contains

* Parsed Resume (JSONB)
* Generated PDF
* Version Number

Relationship

* Belongs to one Resume
* Has one Resume Analysis
* Has many Compatibility Reports
* Has many Cover Letters

---

## ResumeAnalysis

Purpose

Stores ATS and AI review results.

Contains

* Overall Score
* ATS Score
* Grammar Score
* Formatting Score
* AI Feedback (JSONB)

Relationship

* One ResumeVersion → One ResumeAnalysis

---

## JobDescription

Purpose

Stores uploaded or pasted job descriptions.

Contains

* Company
* Role
* Parsed Data (JSONB)

Relationship

* One Job Description → Many Compatibility Reports
* One Job Description → Many Cover Letters

---

## CompatibilityReport

Purpose

Stores comparison results between a Resume Version and a Job Description.

Contains

* Compatibility Score
* Matched Skills
* Missing Skills
* AI Recommendations

Relationship

* Belongs to one ResumeVersion
* Belongs to one JobDescription

---

## CoverLetter

Purpose

Stores AI-generated cover letters.

Relationship

* Belongs to one ResumeVersion
* Belongs to one JobDescription

---

## Application

Purpose

Tracks submitted job applications.

Contains

* Company
* Role
* Status
* Notes
* Applied Date

Relationship

* Belongs to one User
* Uses one ResumeVersion

---

## SkillRoadmap

Purpose

Stores AI-generated personalized learning plans.

Relationship

* Belongs to one User

---

# 6. Entity Relationship Summary

```text
User
│
├───────────────┐
│               │
▼               ▼
Resume      SkillRoadmap
│
▼
ResumeVersion
│
├───────────────┐
│               │
▼               ▼
ResumeAnalysis  CompatibilityReport
                    ▲
                    │
             JobDescription
                    │
                    ▼
               CoverLetter

User
│
▼
Application
│
▼
ResumeVersion
```

---

# 7. Current Progress

## Completed

* Project planning & Architecture design
* Technology stack finalized
* Dockerized PostgreSQL & DBeaver setup
* SQLAlchemy ORM & Alembic database migrations
* Database schema & models (User, Resume, ResumeVersion, ResumeAnalysis, Job, CompatibilityReport, CoverLetter, Application)
* Full CRUD Repositories & FastAPI Router endpoints
* Local AI Integration via Ollama (`qwen2.5:1.5b`) & Structured JSON System Prompts
* Document Parsing Service (PDF & DOCX plain text extraction)
* Next.js 16 Frontend initialization with pnpm & core production packages (`@tanstack/react-query`, `framer-motion`, `recharts`, `react-hook-form`, `zod`, `axios`, `lucide-react`)

---

## Next Milestone

* Configure Frontend Providers (`QueryClientProvider`) & Utility Client (`lib/api.ts`)
* Build UI Design System & Reusable Components (Buttons, Cards, Score Badges)
* Implement Interactive Resume Upload Dropzone Component
* Build ATS Score Analysis & Feedback Dashboard Page
* Build Job Description Matching & AI Cover Letter Generator Views


---

# 8. Architecture Decisions

* PostgreSQL is the primary database.
* SQLAlchemy is the ORM.
* Alembic manages schema migrations.
* Resume parsing results are stored in JSONB.
* Resume versions are immutable.
* AI-generated data should be stored separately from core entities.
* Use absolute imports throughout the backend.
* Docker is the standard local development environment.
