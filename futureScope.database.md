# AI Career Mentor — Project Design Document

> **Version:** MVP v1.0
> **Status:** Future Project
> **Purpose:** AI-powered personalized career growth platform.

---

# Vision

AI Career Mentor is an intelligent career development platform that continuously helps users become more employable by analyzing their current skills, career goals, interests, learning pace, and market trends.

Unlike traditional learning platforms, AI Career Mentor creates adaptive roadmaps that evolve with the user's progress.

---

# Problem Statement

Current learning platforms recommend generic roadmaps such as:

* Learn React
* Learn Node.js
* Learn Docker

These recommendations ignore:

* Existing skills
* Career goals
* Learning speed
* Available time
* Market demand
* Personal interests
* Portfolio quality

The result is inefficient learning.

AI Career Mentor aims to solve this problem using personalized AI planning.

---

# Core Goals

* Understand the user's current profile.
* Identify skill gaps.
* Build personalized learning roadmaps.
* Track learning progress.
* Recommend projects.
* Suggest certifications.
* Analyze GitHub and LinkedIn profiles.
* Prepare users for interviews.
* Adapt recommendations as the user grows.

---

# High-Level Architecture

```text
User
    │
    ▼
Career Profile
    │
    ▼
AI Analysis Engine
    │
    ├──────────────┐
    │              │
    ▼              ▼
Skill Gap     Market Analysis
    │              │
    └──────┬───────┘
           ▼
   Personalized Roadmap
           │
           ▼
 Progress Tracking
           │
           ▼
 AI Mentor
```

---

# Core Modules

## 1. Career Profile

Represents the user's current professional state.

Contains

* Education
* Experience
* Current Skills
* Projects
* Certifications
* Interests
* Preferred Technologies
* Weekly Study Hours
* Career Goals
* Dream Companies
* Resume
* GitHub
* LinkedIn

This becomes the AI's primary input.

---

## 2. Skill Gap Analysis

Determines:

Current Skills

↓

Target Role

↓

Missing Skills

Example

Current

* React
* Node.js

Target

Backend Engineer

Missing

* Docker
* Redis
* PostgreSQL
* AWS
* CI/CD

---

## 3. Personalized Roadmap

Generates an adaptive learning plan.

Example

Month 1

* PostgreSQL
* Docker

Month 2

* Redis
* Authentication
* JWT

Month 3

* AWS
* CI/CD

Month 4

* System Design

Unlike static roadmaps, this roadmap evolves as the user progresses.

---

## 4. Learning Progress

Tracks

* Completed Topics
* Current Module
* Completion Percentage
* Weekly Hours
* Study Streak
* Notes
* Practice Score

---

## 5. Project Recommendation Engine

Suggests projects based on

* Current Skills
* Missing Skills
* Target Companies

Example

Current

React

↓

Recommendation

Build

* Chat Application
* Trello Clone
* Portfolio Website

After Docker

↓

Recommend

Containerized MERN Deployment

---

## 6. Certification Advisor

Suggests certifications based on

* Current level
* Target role
* Industry demand

Examples

* AWS Cloud Practitioner
* Oracle Cloud
* Google Cloud
* Kubernetes
* Azure Fundamentals

---

## 7. GitHub Analyzer

Analyzes

* Repository Quality
* Commit Frequency
* README Quality
* Project Diversity
* Technology Usage
* Testing
* Documentation

Outputs

* Strengths
* Weaknesses
* Improvement Suggestions

---

## 8. LinkedIn Analyzer

Evaluates

* Headline
* Summary
* Experience
* Projects
* Featured Section
* Skills
* Activity

Suggests improvements for recruiter visibility.

---

## 9. Resume Intelligence

Instead of ATS scoring alone,

the system identifies

* Missing achievements
* Weak bullet points
* Missing keywords
* Skill inconsistencies
* Career progression issues

---

## 10. Interview Preparation

Generates

* Technical Questions
* HR Questions
* Behavioral Questions
* Company-specific Questions
* Mock Interviews

Difficulty adapts based on user performance.

---

## 11. Market Intelligence

Continuously tracks

* Trending Skills
* Salary Trends
* Hiring Demand
* Popular Technologies
* Industry Reports

Example

Current Trend

Backend Engineers

↓

High demand for

* Docker
* Kubernetes
* AWS

The roadmap adapts automatically.

---

# Suggested Database Design

## Core Entities

```text
User

CareerProfile

Skill

Project

Certification

Roadmap

RoadmapStep

LearningProgress

InterviewSession

MockInterview

MarketTrend

GitHubProfile

LinkedInProfile

CareerGoal
```

---

# Relationships

```text
User
│
├───────────────┐
│               │
▼               ▼
CareerProfile   CareerGoal
│
├───────────────┐
│               │
▼               ▼
GitHubProfile   LinkedInProfile
│
▼
SkillGapAnalysis
│
▼
Roadmap
│
▼
RoadmapStep
│
▼
LearningProgress

CareerProfile
│
├───────────────┐
│               │
▼               ▼
Project       Certification

CareerProfile
│
▼
InterviewSession
│
▼
MockInterview

CareerProfile
│
▼
ResumeReview
```

---

# AI Components

## Input

* Career Profile
* Resume
* GitHub
* LinkedIn
* Current Skills
* Target Role
* Market Trends

---

## AI Pipelines

* Skill Gap Analysis
* Resume Intelligence
* Roadmap Generator
* Project Recommendation
* Interview Generator
* Certification Recommendation
* Weekly Progress Review

---

## Outputs

* Personalized Roadmap
* Weekly Goals
* Learning Tasks
* Projects
* Certifications
* Resume Suggestions
* Interview Questions

---

# Future Features

* Daily AI Mentor Chat
* Weekly Career Review
* AI Accountability Partner
* Coding Challenge Generator
* Company Preparation Packs
* Salary Negotiation Coach
* Networking Suggestions
* Open Source Contribution Planner
* Learning Calendar
* Achievement Badges

---

# Recommended Technology Stack

Frontend

* Next.js
* TypeScript
* Tailwind CSS
* shadcn/ui
* React Flow
* Recharts

Backend

* FastAPI
* SQLAlchemy
* PostgreSQL
* Redis
* Celery

AI

* Gemini
* Embedding Models
* pgvector
* LangGraph (future)

Infrastructure

* Docker
* MinIO
* Nginx
* GitHub Actions

---

# Long-Term Vision

Career Copilot helps users **apply for jobs**.

AI Career Mentor helps users **become the kind of candidate companies want to hire**.

Both products can eventually integrate:

```text
AI Career Mentor
        │
        ▼
Career Copilot
        │
        ▼
Interview Success
```

Together they create a complete AI-powered career ecosystem rather than a single resume optimization tool.
