# Frontend Documentation — University Classroom Management System

This document provides a comprehensive technical overview of the frontend application built for the University Dashboard & Classroom Management platform.

---

## 🛠 Tech Stack Overview

* **Core Framework**: React 19, TypeScript, Vite 6
* **App Framework / Headless Engine**: [Refine v5](https://refine.dev/) (`@refinedev/core`, `@refinedev/react-router`, `@refinedev/rest`, `@refinedev/react-hook-form`)
* **Routing**: React Router v7 (`react-router`)
* **Styling**: Tailwind CSS v4, CSS Variables, `clsx`, `tailwind-merge`
* **UI Components & Icons**: Radix UI primitives, shadcn/ui, Lucide React (`lucide-react`)
* **Authentication**: [Better Auth](https://www.better-auth.com/) (`better-auth`) integrated into Refine's `authProvider`
* **Form & Validation**: `react-hook-form`, `zod`
* **Media Uploads**: Cloudinary Widget integration (`@cloudinary/react`, `@cloudinary/url-gen`)
* **Data Visualization & Analytics**: Recharts (`recharts`)
* **Notifications & UX**: Sonner (`sonner`), Refine Kbar command palette (`@refinedev/kbar`), Next Themes (`next-themes`)

---

## 📁 Directory Structure

```text
classroom/
├── public/                 # Static assets & public files
├── src/
│   ├── components/         # Reusable UI & Refine UI components
│   │   ├── refine-ui/      # Layout, buttons, table views, notification toasts, theme controls
│   │   ├── ui/             # shadcn/ui base primitives (accordion, dialog, card, sidebar, etc.)
│   │   └── upload-widget.tsx # Cloudinary media upload widget
│   ├── constants/          # Environment constants & global configuration
│   ├── hooks/              # Custom React hooks
│   ├── lib/                # Libraries & SDK configurations
│   │   ├── auth-client.ts  # Better-Auth client instance
│   │   ├── cloudinary.ts  # Cloudinary script loader & helpers
│   │   ├── schema.ts      # Zod validation schemas
│   │   └── utils.ts       # Tailwind class merger utility
│   ├── pages/              # Application views & route targets
│   │   ├── classes/        # Class listing, creation, and detailed show views
│   │   ├── departments/    # Department management views
│   │   ├── enrollments/    # Course enrollment creation & student join code views
│   │   ├── faculty/        # Faculty directory & detail views
│   │   ├── login/          # Sign-in page
│   │   ├── register/       # Multi-role user registration page
│   │   ├── subjects/       # Subject list, creation, and detail views
│   │   └── dashboard.tsx   # Analytics & key stats landing page
│   ├── providers/          # Refine core providers
│   │   ├── auth.ts         # Authentication provider (sign in, register, identity, permissions)
│   │   └── data.ts         # REST Data provider (custom filter & pagination query builder)
│   ├── types/              # TypeScript interface & type definitions
│   │   └── index.ts        # Data schemas (User, ClassDetails, Subject, Department, etc.)
│   ├── App.tsx             # Root component with routing, Refine provider, & layout configuration
│   ├── main.tsx            # Vite entrypoint
│   └── index.css           # Global Tailwind CSS definitions
├── package.json            # Project dependencies & scripts
├── vite.config.ts          # Vite build configuration
└── tsconfig.json           # TypeScript configuration
```

---

## 🔐 Core Architecture & Providers

### 1. Authentication System (`src/providers/auth.ts`)
The authentication is managed via Refine's `authProvider`, backed by Better Auth (`src/lib/auth-client.ts`):
* **`register`**: Registers a user with custom roles (`student`, `teacher`, `admin`), optional department, and avatar image uploaded via Cloudinary.
* **`login`**: Authenticates credentials using Better-Auth email provider and persists identity data in `localStorage`.
* **`check`**: Checks if active user identity exists; redirects unauthenticated users to `/login`.
* **`getIdentity`**: Returns user profile (ID, name, email, role, avatar public ID).
* **`getPermissions`**: Returns user role for Role-Based Access Control (RBAC).

### 2. REST Data Provider (`src/providers/data.ts`)
Uses `@refinedev/rest` connected to the Express API base URL:
* **Custom Query Param Construction**: Customizes search and filtering params for resources (`departments`, `users`, `subjects`, `classes`).
* **Response Normalization**: Normalizes backend JSON responses into standard Refine list (`ListResponse`), record detail (`GetOneResponse`), and creation payloads (`CreateResponse`).

---

## 🗺 Routing & Resource Definitions (`src/App.tsx`)

Refine resources map backend entities to frontend routes, icons, and titles:

| Resource Name | Navigation Label | Routes |
| :--- | :--- | :--- |
| **`dashboard`** | Home | `/` |
| **`subjects`** | Subjects | `/subjects`, `/subjects/create`, `/subjects/show/:id` |
| **`departments`** | Departments | `/departments`, `/departments/create`, `/departments/show/:id` |
| **`users`** | Faculty | `/faculty`, `/faculty/show/:id` |
| **`enrollments`** | Enrollments | `/enrollments/create`, `/enrollments/join`, `/enrollments/confirm` |
| **`classes`** | Classes | `/classes`, `/classes/create`, `/classes/show/:id` |

### Route Access Control
* **Public Routes** (`/login`, `/register`): Wrapped in `<Authenticated fallback={<Outlet />}>`. Logged-in users are automatically redirected to `/`.
* **Private Routes** (`/`, `/subjects/*`, `/departments/*`, `/faculty/*`, `/enrollments/*`, `/classes/*`): Wrapped in `<Authenticated fallback={<Login />}>` with global dashboard `<Layout>`.

---

## 📑 Feature Modules Overview

1. **Dashboard** (`src/pages/dashboard.tsx`)
   * Displays high-level institutional analytics (student count, active classes, department statistics, upcoming schedules).

2. **Subject Management** (`src/pages/subjects`)
   * Curricular management with filterable tables by department and search keyword. Supports creating new subjects and viewing assigned classes.

3. **Department Management** (`src/pages/departments`)
   * Department governance layer mapping faculty members, subjects, and active courses under specific departments.

4. **Faculty Directory** (`src/pages/faculty`)
   * Paginated directory of professors and instructors, with profile images hosted on Cloudinary, teaching schedules, and assigned courses.

5. **Classes & Enrollment System** (`src/pages/classes` & `src/pages/enrollments`)
   * Core scheduling and class management. Features unique join codes for Google Classroom-style student course enrollment.

---

## 🎨 UI Component Architecture

* **Layout & Navigation** (`src/components/refine-ui/layout/layout.tsx`): Responsive sidebar navigation, user profile header, dark/light theme toggle, and search trigger.
* **Cloudinary Upload Widget** (`src/components/upload-widget.tsx`): Managed Cloudinary upload button supporting image uploads and returning secure URLs and public IDs.
* **Form & Modal Primitives** (`src/components/ui`): Pre-styled primitives from shadcn/ui (`card.tsx`, `dialog.tsx`, `table.tsx`, `input.tsx`, `select.tsx`, etc.).

---

## 🚀 Development & Build Commands

Commands configured in `package.json`:

```bash
# Start dev server with Refine CLI
npm run dev

# TypeScript check & production build
npm run build

# Preview production build locally
npm run start
```

### Key Environment Variables (`.env`)
```env
VITE_BACKEND_BASE_URL="http://localhost:8000/api/"
VITE_CLOUDINARY_CLOUD_NAME="<your_cloud_name>"
VITE_CLOUDINARY_UPLOAD_PRESET="<your_upload_preset>"
VITE_CLOUDINARY_UPLOAD_URL="<your_upload_url>"
```
