# Career Copilot — Backend Services Overview

This document provides a high-level overview of the Career Copilot backend services, architecture, and module organization.

For detailed database schemas, field definitions, and entity relationship diagrams, please refer directly to the [Database Design Documentation](file:///home/devsharma08/code/Resume_App/docs/database.md).

---

## Technical Stack

* **Web Framework**: FastAPI
* **Database ORM**: SQLAlchemy 2.x
* **Migration Manager**: Alembic
* **Data Validation**: Pydantic v2
* **Language**: Python 3.14+

---

## Directory Structure

```text
backend/
├── app/
│   ├── ai/            # Prompt engineering, AI model wrappers and providers
│   ├── api/           # Router groups and API endpoint handlers
│   ├── core/          # Security, auth, configuration setup
│   ├── database/      # Session setup and declarative Base config
│   ├── middleware/    # Cors, logger, rate limiting middlewares
│   ├── models/        # SQLAlchemy database model definitions
│   ├── repositories/  # Database access layer pattern (CRUD operations)
│   ├── schemas/       # Pydantic validation schemas
│   ├── services/      # Business logic services (AI integration, PDF parsing)
│   ├── utils/         # Helper functions (hashing, file helpers)
│   └── main.py        # Application entry point
├── tests/             # Pytest suite
└── requirements.txt   # Backend python dependencies
```

---

## Database Models

The database models are declared under `backend/app/models/` and extend the shared declarative `Base` from `app.database.base`. 

The models are:
1. **[User](file:///home/devsharma08/code/Resume_App/backend/app/models/user.py)**: Tracks user profiles and authentication identities.
2. **[Resume](file:///home/devsharma08/code/Resume_App/backend/app/models/resume.py)**: Represents a user's resume container.
3. **[ResumeVersion](file:///home/devsharma08/code/Resume_App/backend/app/models/resumeVersion.py)**: Holds immutable snapshots of resumes (documents/raw text/parsed data).
4. **[ResumeAnalysis](file:///home/devsharma08/code/Resume_App/backend/app/models/resumeAnalysis.py)**: Stores AI-generated general scorecards and feedback for a resume version.
5. **[Job](file:///home/devsharma08/code/Resume_App/backend/app/models/job.py)**: Stores job descriptions and contains an `application_status` tracking enum.
6. **[CompatibilityReport](file:///home/devsharma08/code/Resume_App/backend/app/models/compatibilityReport.py)**: Stores match reports between a resume version and a job description.
7. **[CoverLetter](file:///home/devsharma08/code/Resume_App/backend/app/models/coverLetter.py)**: Contains AI-generated cover letter texts.
8. **[Application](file:///home/devsharma08/code/Resume_App/backend/app/models/application.py)**: Records specific user job application history.
