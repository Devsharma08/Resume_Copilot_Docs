# Career Copilot — Database Design

> **Version:** MVP v1.0
> **Database:** PostgreSQL
> **ORM:** SQLAlchemy 2.x
> **Migration Tool:** Alembic

---

# Overview

Career Copilot is an AI-powered career assistance platform focused on helping users:

* Build and manage multiple resumes
* Track resume versions
* Analyze resumes using AI
* Match resumes against job descriptions
* Generate AI-powered cover letters
* Track job applications

The database is designed using **normalized relational modeling**, while storing AI-generated structured content in **JSONB** where flexibility is required.

---

# Design Principles

The schema follows these principles:

* Separate **core business entities** from **AI-generated outputs**.
* Store **immutable resume versions** rather than overwriting existing data.
* Preserve history whenever possible.
* Keep AI-generated data independent from user-generated data.
* Use PostgreSQL `JSONB` for parsed AI content to avoid unnecessary normalization.
* Design for future scalability without overengineering the MVP.

---

# Entity Classification

## Core Entities

These represent business objects created or owned by the user.

* User
* Resume
* ResumeVersion
* Job
* Application

---

## AI Generated Entities

These are outputs produced by AI models.

* ResumeAnalysis
* CompatibilityReport
* CoverLetter

---

# Entity Relationship Diagram

```text
User
│
├──────────────────────┐
│                      │
▼                      ▼
Resume             Application
│                      │
▼                      │
ResumeVersion───────────┘
│
├───────────────┐
│               │
▼               ▼
ResumeAnalysis  CompatibilityReport
                    ▲
                    │
                   Job
                    │
                    ▼
              CoverLetter
```

---

# Database Models

---

# 1. User

## Purpose

Represents a registered user of the platform.

Authentication is delegated to an external authentication provider (Clerk/Auth.js/OAuth). The application stores only user-related business information.

## Fields

| Field | Type | Description |
| ----------- | ----------- | ---------------------------------------- |
| id | Integer | Internal primary key |
| provider_id | String | External authentication provider user ID (Unique, Indexed) |
| name | String(100) | User display name |
| email | String(255) | User email (Unique, Indexed) |
| avatar_url | String(255) | Profile picture URL |
| created_at | DateTime | Account creation timestamp |
| updated_at | DateTime | Last update timestamp |

## Relationships

* One User → Many Resumes (`resumes`)
* One User → Many Applications (`applications`)

---

# 2. Resume

## Purpose

Represents a logical resume.

A Resume is **not** an uploaded file.

Instead, it represents a resume identity such as:

* Backend Resume
* Frontend Resume
* Research Resume

Each resume can have multiple versions over time.

## Fields

| Field | Type | Description |
| ----------- | ----------- | ----------------------- |
| id | Integer | Primary key |
| user_id | Integer | Owner (ForeignKey to `users.id`) |
| title | String(255) | Resume title |
| description | String | Optional description |
| created_at | DateTime | Creation timestamp |
| updated_at | DateTime | Last modified timestamp |

## Relationships

* Belongs to one User
* Has many ResumeVersions (`versions`)

---

# 3. ResumeVersion

## Purpose

Stores immutable snapshots of a resume.

Every upload or accepted optimization creates a new version.

Example

```text
Backend Resume

↓

Version 1

↓

Version 2

↓

Version 3
```

No version is overwritten.

## Fields

| Field | Type | Description |
| --------------------- | ------------------- | ----------------------------- |
| id | Integer | Primary key |
| resume_id | Integer | Parent Resume (ForeignKey to `resumes.id`) |
| version_number | Integer | Version number |
| storage_key | String(255) | File location (local/S3/etc., Unique) |
| original_filename | String(255) | Uploaded filename |
| raw_text | String | Extracted plain text |
| parsed_resume | JSONB | Structured parsed resume |
| status | String(50) | Draft/Active/Archived |
| created_at | DateTime | Upload timestamp |

## Relationships

* Belongs to one Resume
* Has one ResumeAnalysis (`analysis`)
* Has many CompatibilityReports (`compatibility_reports`)
* Has many CoverLetters (`cover_letters`)

---

# 4. ResumeAnalysis

## Purpose

Stores AI analysis of a specific resume version.

Keeping analysis separate prevents ResumeVersion from becoming a large table.

## Fields

| Field | Type | Description |
| ----------------- | -------------------- | -------------------- |
| id | Integer | Primary key |
| resume_version_id | Integer | Resume Version (ForeignKey to `resume_versions.id`, Unique) |
| ats_score | Integer | ATS score |
| grammar_score | Integer | Grammar quality |
| keyword_score | Integer | Keyword optimization |
| formatting_score | Integer | Formatting quality |
| overall_score | Integer | Final score |
| feedback | JSONB | AI suggestions and feedback details |
| created_at | DateTime | Generated timestamp |

## Relationships

* One ResumeVersion ↔ One ResumeAnalysis

---

# 5. Job

## Purpose

Represents a job posting.

The table stores more than just the job description.

## Fields

| Field | Type | Description |
| --------------------------- | --------------------------------- | --------------------------------- |
| id | Integer | Primary key |
| company | String(255) | Company name |
| title | String(255) | Job title |
| location | String(255) | Job location |
| employment_type | Enum | Internship / Full-Time / Contract / Part-Time / Other |
| source | Enum | LinkedIn / Internshala / Indeed / Naukri / TimesJob / Whatsapp / Other |
| url | String(255) | Original posting URL |
| description | String | Raw job description |
| application_status | Enum | Not Started / In Progress / Submitted / Rejected / Withdrawn *(User Custom Change)* |
| parsed_requirements | JSONB | AI parsed requirements |
| created_at | DateTime | Created timestamp |
| updated_at | DateTime | Last modified timestamp |

## Relationships

* Has many CompatibilityReports (`compatibility_reports`)
* Has many CoverLetters (`cover_letters`)
* Has many Applications (`applications`)

---

# 6. CompatibilityReport

## Purpose

Represents the AI comparison between:

* one ResumeVersion
* one Job

This is not simply a junction table.

It stores valuable business information generated by AI.

## Fields

| Field | Type | Description |
| ----------------------- | --------------------- | --------------------- |
| id | Integer | Primary key |
| resume_version_id | Integer | Resume used (ForeignKey to `resume_versions.id`) |
| job_id | Integer | Target job (ForeignKey to `jobs.id`) |
| compatibility_score | Integer | Overall compatibility |
| matched_skills | JSONB | Matching skills list |
| missing_skills | JSONB | Missing skills list |
| keyword_matches | JSONB | Keyword analysis details |
| recommendations | JSONB | AI suggestions and roadmap |
| created_at | DateTime | Generated timestamp |

## Relationships

* Belongs to one ResumeVersion
* Belongs to one Job

---

# 7. CoverLetter

## Purpose

Stores AI-generated cover letters.

Although generated using the same inputs as CompatibilityReport, CoverLetter remains an independent feature because users may generate it directly without viewing compatibility results.

Input

```text
ResumeVersion

+

Job
```

Output

```text
Cover Letter
```

## Fields

| Field | Type | Description |
| ------------- | -------------------------- | -------------------------- |
| id | Integer | Primary key |
| resume_version_id | Integer | Resume version used (ForeignKey to `resume_versions.id`) |
| job_id | Integer | Target job (ForeignKey to `jobs.id`) |
| content | String | Generated cover letter text |
| tone | String(50) | Tone selection (e.g. Professional, Friendly) |
| prompt_version | String(50) | AI prompt version code |
| created_at | DateTime | Generated timestamp |

## Relationships

* Belongs to one ResumeVersion
* Belongs to one Job

---

# 8. Application

## Purpose

Tracks real job applications.

This entity represents an actual application submitted by the user.

## Fields

| Field | Type | Description |
| ------------- | ------------------------------------------------- | ------------------------------------------------- |
| id | Integer | Primary key |
| user_id | Integer | Applicant (ForeignKey to `users.id`) |
| resume_version_id | Integer | Resume version submitted (ForeignKey to `resume_versions.id`) |
| job_id | Integer | Applied job (ForeignKey to `jobs.id`) |
| status | String(50) | Application status (e.g., Applied, Interview, Rejected, Offer, Accepted) |
| notes | String | Personal notes and tracking details |
| applied_at | DateTime | Submission date |
| updated_at | DateTime | Last status change date |

## Relationships

* Belongs to one User
* Uses one ResumeVersion
* References one Job

---

# JSONB Usage

The project intentionally stores AI-generated structured content in PostgreSQL JSONB.

Examples include:

* Parsed Resume
* Resume Feedback
* Parsed Job Requirements
* Matched Skills
* Missing Skills
* AI Recommendations

## Benefits

* Flexible schema
* Easy AI integration
* No unnecessary normalization
* Supports PostgreSQL indexing
* Future-proof for changing AI outputs

---

# Relationship Summary

| Parent | Child | Type |
| ------------- | ------------------- | ----------- |
| User | Resume | One-to-Many |
| User | Application | One-to-Many |
| Resume | ResumeVersion | One-to-Many |
| ResumeVersion | ResumeAnalysis | One-to-One |
| ResumeVersion | CompatibilityReport | One-to-Many |
| ResumeVersion | CoverLetter | One-to-Many |
| Job | CompatibilityReport | One-to-Many |
| Job | CoverLetter | One-to-Many |
| Job | Application | One-to-Many |

---

# Future Modules (Not Part of MVP)

The following features have intentionally been excluded from the MVP database because they represent a separate product domain.

## AI Career Mentor

Future entities may include:

* SkillProfile
* SkillRoadmap
* LearningProgress
* WeeklyGoals
* RecommendedProjects
* InterviewPreparation
* GitHubAnalysis
* LinkedInAnalysis

These will be designed as an independent module to keep the Career Copilot core focused and maintainable.

---

# Architecture Philosophy

The database is designed around three categories:

**Core Business Data**

* User
* Resume
* ResumeVersion
* Job
* Application

**AI Inputs**

* ResumeVersion
* Job

**AI Outputs**

* ResumeAnalysis
* CompatibilityReport
* CoverLetter

This separation ensures that user-owned data remains independent from AI-generated artifacts, making the system easier to maintain, regenerate, and extend over time.
