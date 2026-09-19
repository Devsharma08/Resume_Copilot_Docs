# Career Copilot — Frontend Architecture & Technology Stack

> This document details the frontend implementation, state management patterns, design system, and core dependency specifications for the Career Copilot application.

---

## 1. Core Technology Stack

| Library / Tool | Category | Role in Career Copilot |
| :--- | :--- | :--- |
| **Next.js 16 (App Router)** | Framework | Production React framework providing Server-Side Rendering (SSR), API routes, and optimized image/font delivery. |
| **React 19 & TypeScript 5** | Core | Strict type-safe UI component architecture. |
| **Tailwind CSS v4** | Styling | Utility-first CSS engine configured with modern dark/light CSS variables, glassmorphism, and responsive breakpoints. |
| **TanStack Query v5 (`@tanstack/react-query`)** | Server State | Manages asynchronous API data fetching, automatic background revalidation, query caching, and optimistic mutations. |
| **React Hook Form & Zod** | Form Management | High-performance, zero-rerender form engine with strict runtime schema validation via `@hookform/resolvers/zod`. |
| **Framer Motion v12** | Animation | Production micro-animations, page route transitions, modal popovers, and interactive dropzone feedback. |
| **Recharts v3** | Data Visualization | Interactive SVG charts for displaying ATS Score gauges, keyword matching distribution, and skill gap radar graphs. |
| **Lucide React** | Icons | SVG iconography across navigation, actions, and status indicators. |
| **Axios** | HTTP Client | Centralized API client configured with baseURL (`http://localhost:8000`), request interceptors, and error handlers. |
| **clsx & tailwind-merge** | UI Utility | Utility helpers for conditionally joining Tailwind CSS class names cleanly without specificity conflicts. |

---

## 2. Detailed Dependency Breakdown

### ⚡ `@tanstack/react-query`
* **Why it's essential**: Eliminates verbose `useEffect` data fetching logic. Provides built-in caching, background revalidation, loading states, retry logic, and mutation hooks for uploading resumes and generating cover letters.

### 📝 `react-hook-form` & `zod`
* **Why it's essential**: `react-hook-form` tracks form state natively without causing whole-page re-renders on every keystroke. `zod` guarantees strict validation schema rules for form inputs before sending payloads to FastAPI.

### 🎭 `framer-motion`
* **Why it's essential**: Delivers a fluid, desktop-app feel. Provides smooth layout animations when switching tabs, expanding ATS score cards, or displaying progress during AI parsing.

### 📊 `recharts`
* **Why it's essential**: Renders responsive, highly customizable charts for ATS scoring dashboards, keyword match percentage rings, and skill breakdown radar graphs.

### 🔌 `axios`
* **Why it's essential**: Provides a robust client instance (`lib/api.ts`) with request/response interceptors to easily consume our FastAPI endpoints.

---

## 3. Directory Structure

```text
frontend/
├── app/
│   ├── globals.css          # CSS Variables, Tailwind imports, Glassmorphism utilities
│   ├── layout.tsx           # Main RootLayout wrapping React Query Provider & Navbar
│   ├── page.tsx             # Main Dashboard Page
│   ├── resumes/             # Resume Version Management & Upload Views
│   ├── analysis/            # ATS Score & Feedback Analysis Dashboard
│   └── jobs/                # Job Match & Cover Letter Generator Views
├── components/
│   ├── ui/                  # Reusable Design System Components (Buttons, Cards, Modals)
│   ├── resume/              # Resume Upload Dropzone & Version Selectors
│   ├── analysis/            # ATS Score Gauges & Feedback Lists
│   └── layout/              # Navbar, Sidebar, and Header layouts
├── lib/
│   ├── api.ts               # Axios API client instance pointing to FastAPI
│   └── utils.ts             # Tailwind class merge helper (cn)
└── providers/
    └── QueryProvider.tsx    # TanStack Query Client Provider wrapper
```
