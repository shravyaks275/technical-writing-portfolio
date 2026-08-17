# Stray Shield — System Overview

> **A civic-tech platform for reporting, monitoring, and managing stray-dog cases through citizen–NGO collaboration and machine-learning-assisted health classification.**

## 1. Introduction

**Stray Shield** is a full-stack civic-technology web application that connects citizens with NGOs to report, monitor, and manage stray-dog cases in their communities.

The platform provides a centralized workflow for submitting reports, tracking case status, managing adoption-related activities, and coordinating responses. It also incorporates a machine-learning pipeline that performs basic dog-health classification from uploaded images to assist NGOs in prioritizing cases.

The application consists of three primary technical layers:

* **Next.js frontend** — provides the user interface and citizen/NGO workflows.
* **Express.js backend** — provides REST APIs, authentication, report management, and server-side processing.
* **TensorFlow.js ML pipeline** — performs image-based dog-health classification using MobileNet embeddings and a KNN classifier.

---

## 2. Problem Statement

Stray-dog cases are often reported and managed through fragmented or manual processes. Citizens may have difficulty communicating accurate information about a case, while NGOs need an efficient way to collect reports, review images, track case status, and coordinate responses.

Stray Shield addresses these challenges by providing a centralized platform where:

1. Citizens can submit reports containing images, location information, and contact details.
2. NGOs can review and filter submitted cases from a dedicated dashboard.
3. Report status can be tracked throughout the response lifecycle.
4. Citizens can monitor their submitted reports.
5. Adoption-related workflows can be managed through the platform.
6. Machine-learning-based health classification can provide an additional signal for prioritizing cases.

The goal is not to replace human assessment, but to provide an automated classification mechanism that can assist responders during the initial triage process.

---

## 3. Project Goals

Stray Shield is designed around the following goals:

### Citizen Reporting

Provide a simple reporting experience that allows citizens to submit:

* Multiple images
* Location information
* Contact details
* Relevant case information

### NGO Case Management

Provide NGOs with a centralized dashboard for:

* Viewing submitted reports
* Filtering cases
* Reviewing case information
* Updating report status
* Managing adoption requests
* Contacting reporters

### Machine-Learning Assistance

Provide an on-demand image classification workflow using TensorFlow.js to help identify broad health categories such as:

* Healthy
* Sick
* Injured

The classification is intended as an **assistive tool for prioritization**, rather than a replacement for professional veterinary assessment.

### Secure Access

Protect application functionality through:

* JWT-based authentication
* Password hashing using bcrypt
* Role-based access control
* Protected application routes

### Deployability

Keep the application compatible with commonly used deployment platforms, including:

* **Vercel** for the frontend
* **Railway / Render** for the backend

---

## 4. Key Features

### Citizen Features

* Citizen registration and authentication
* Stray-dog report submission
* Multi-image upload with preview
* Location information
* Personal dashboard
* **My Reports** view
* Report-status tracking
* Browse adoptable dogs
* Express adoption interest

### NGO Features

* NGO authentication
* NGO dashboard
* Report statistics
* Report filtering
* Case-status management
* Adoption-request management
* Reporter contact functionality

### Machine Learning

* Image-based dog-health classification
* MobileNet-based feature extraction
* KNN classification
* Training and prediction scripts
* Batch prediction support

### Security

* JWT authentication
* bcrypt password hashing
* Protected routes
* Role-based access control
* Centralized API communication helpers

### Data Storage

The backend supports:

* PostgreSQL for production-oriented database storage
* File-based JSON storage for lightweight local development and testing

---

## 5. User Roles

| Role        | Responsibilities                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------- |
| **Citizen** | Submit stray-dog reports, view submitted reports, browse adoptable dogs, and express adoption interest. |
| **NGO**     | Review reports, filter cases, update case status, manage adoption requests, and contact reporters.      |
| **Admin**   | Planned/future role for user management, moderation, and system-level administration.                   |

### Citizen

Citizens are the primary source of community reports. They can create an account, submit stray-dog cases, monitor their reports, and participate in adoption workflows.

### NGO

NGOs are responsible for reviewing and responding to submitted cases. Their dashboard provides tools for filtering reports, updating statuses, managing adoption requests, and coordinating with citizens.

### Admin

An administrative role is planned as a future capability for broader system management, moderation, and monitoring.

---

# 6. System Architecture

Stray Shield follows a full-stack architecture consisting of a Next.js frontend, Express.js API backend, database/storage layer, and machine-learning pipeline.

```text
┌───────────────────────────────┐
│           Citizen             │
│                               │
│  Reports / Dashboard /        │
│  Adoption                     │
└───────────────┬───────────────┘
                │
                │ HTTP / REST API
                ▼
┌───────────────────────────────┐
│       Next.js Frontend        │
│                               │
│  App Router                   │
│  UI Components                │
│  Protected Routes             │
│  API Client                   │
└───────────────┬───────────────┘
                │
                │ NEXT_PUBLIC_API_URL
                ▼
┌───────────────────────────────┐
│       Express.js Backend      │
│                               │
│  Authentication               │
│  Reports                      │
│  User Profiles                │
│  Classification               │
│  Health Checks                │
└───────┬───────────────┬───────┘
        │               │
        │               │
        ▼               ▼
┌───────────────┐  ┌──────────────────┐
│  PostgreSQL   │  │ TensorFlow.js    │
│               │  │                  │
│  Production   │  │ MobileNet        │
│  Database     │  │ + KNN Classifier │
└───────────────┘  └──────────────────┘
```

---

## 6.1 Frontend

The frontend is implemented using **Next.js 15** with the App Router.

During development, the frontend runs on:

```text
http://localhost:3000
```

The frontend provides the application's user-facing functionality, including:

* Landing page
* Authentication
* Reporting
* Citizen dashboard
* NGO dashboard
* My Reports
* Adoption workflows

API communication is configured through the:

```text
NEXT_PUBLIC_API_URL
```

environment variable.

---

## 6.2 Backend API

The backend is implemented using **Node.js and Express.js**.

During local development, the server runs on port:

```text
3001
```

The backend provides REST endpoints for:

* Authentication
* User profiles
* Reports
* Health classification
* Health checks

JWT tokens are used to authenticate protected API requests.

The main backend entry point is:

```text
server/index.js
```

---

## 6.3 Database Layer

The application is designed to use **PostgreSQL** for persistent database storage.

Database connectivity is handled using the `pg` package and a PostgreSQL connection pool configured in:

```text
server/config/database.js
```

A file-based JSON storage mechanism is also available for lightweight local development and testing.

The project also contains Prisma as a dependency, along with schema/migration-related files for development and potential future database management.

---

## 6.4 Machine Learning Layer

The ML pipeline is implemented using **TensorFlow.js**.

The classification workflow uses:

* **MobileNet** for image feature extraction
* **KNN Classifier** for classification

The ML components are located in the:

```text
ml/
```

directory.

The primary scripts include:

```text
ml/
├── predict.js
├── train.js
└── health_model/
```

The backend exposes an authenticated classification endpoint:

```text
/api/classify
```

This endpoint can integrate the trained classifier into the application workflow.

---

# 7. Data Flow

A typical reporting and classification workflow follows this sequence:

```text
Citizen
   │
   │ Submit report + images + location
   ▼
Next.js Frontend
   │
   │ REST API request
   ▼
Express.js Backend
   │
   ├──────────────► Database / File Storage
   │
   │
   ▼
ML Classification
   │
   │ MobileNet + KNN
   ▼
Health Classification
   │
   ▼
Report / Case Information
   │
   ▼
NGO Dashboard
   │
   ▼
Case Review & Response
```

### Workflow

1. A citizen creates a report through the frontend.
2. The report may include multiple images and location information.
3. The frontend sends the report information to the Express.js backend.
4. The backend stores the report and associated information.
5. Images can be processed through the ML classification pipeline.
6. The classification process produces a health-related label.
7. NGOs can review the resulting case information through their dashboard.
8. The NGO can update the case status and take appropriate action.

---

# 8. Technology Stack

| Layer                      | Technology       |
| -------------------------- | ---------------- |
| **Frontend Framework**     | Next.js 15       |
| **UI Library**             | React 18         |
| **Styling**                | Tailwind CSS 4   |
| **Backend Runtime**        | Node.js          |
| **Backend Framework**      | Express.js       |
| **Database**               | PostgreSQL       |
| **Database Client**        | `pg`             |
| **ORM / Database Tooling** | Prisma           |
| **Machine Learning**       | TensorFlow.js    |
| **Feature Extraction**     | MobileNet        |
| **Classification**         | KNN Classifier   |
| **Authentication**         | JSON Web Tokens  |
| **Password Security**      | bcryptjs         |
| **Frontend Deployment**    | Vercel           |
| **Backend Deployment**     | Railway / Render |

### Additional Libraries

The project also uses libraries including:

* `@tensorflow-models/mobilenet`
* `@tensorflow-models/knn-classifier`
* `pg`
* `prisma`
* `framer-motion`
* `lucide-react`
* `jsonwebtoken`
* `bcryptjs`

---

# 9. Major Components

The project is organized into several major directories.

```text
stray-shield/
│
├── app/
├── components/
├── server/
├── ml/
├── prisma/
├── public/
└── dataset/
```

## `app/`

Contains the Next.js App Router structure and application pages.

Key areas include:

* Landing page
* Login
* Signup
* Report submission
* Citizen dashboard
* NGO dashboard
* My Reports

---

## `components/`

Contains reusable UI components used throughout the frontend.

Examples include:

```text
Navbar
ReportForm
ReportCard
DogCard
ProtectedRoute
```

These components help keep the frontend modular and reusable.

---

## `server/`

Contains the Express.js backend implementation.

Key files and areas include:

```text
server/
├── index.js
├── package.json
├── config/
│   └── database.js
├── migration/
└── seed/
```

The backend is responsible for API handling, authentication, database communication, and server-side processing.

---

## `ml/`

Contains the machine-learning implementation.

```text
ml/
├── predict.js
├── train.js
└── health_model/
```

The directory contains scripts for training and prediction along with the model-related resources.

---

## `prisma/`

Contains Prisma-related database schema and migration resources.

Prisma is currently present as part of the project's database tooling and provides a foundation for future schema-management requirements.

---

## `public/`

Contains static frontend assets used by the application.

---

## `dataset/`

Contains labeled image data used by the machine-learning workflow.

The dataset is organized into health-related categories such as:

```text
dataset/
├── healthy/
├── sick/
└── injured/
```

---

# 10. User Workflows

## 10.1 Citizen Workflow

```text
Sign Up
   ↓
Log In
   ↓
Submit Report
   ↓
Upload Images + Location
   ↓
Report Created
   ↓
Track Report Status
   ↓
View / Participate in Adoption
```

### Typical Flow

1. Citizen creates an account.
2. Citizen logs into the application.
3. Citizen submits a stray-dog report.
4. Images and location information are provided.
5. The backend creates the report.
6. The citizen can view the report through **My Reports**.
7. The citizen can monitor the case status.
8. The citizen can browse available dogs and express adoption interest.

---

## 10.2 NGO Workflow

```text
Sign Up
   ↓
Log In
   ↓
NGO Dashboard
   ↓
Review Reports
   ↓
Filter / Prioritize Cases
   ↓
Update Status
   ↓
Coordinate Response
   ↓
Manage Adoption Requests
```

### Typical Flow

1. NGO user creates an account or accesses an existing account.
2. NGO user logs into the application.
3. The NGO dashboard displays available reports and statistics.
4. Reports can be filtered for easier case management.
5. The NGO reviews submitted case information.
6. Case status is updated as work progresses.
7. The NGO can contact the reporter when required.
8. Adoption requests can be reviewed and managed.

---

# 11. Report Status Lifecycle

Reports move through a basic status lifecycle:

```text
Pending
   │
   ▼
In Progress
   │
   ▼
Resolved
```

### Pending

The report has been submitted but has not yet been actively processed.

### In Progress

An NGO or responder is actively reviewing or handling the case.

### Resolved

The case has reached a completed or resolved state.

This lifecycle provides citizens and NGOs with a consistent way to track case progress.

---

# 12. Authentication and Authorization

Stray Shield uses **JWT-based authentication** to protect authenticated functionality.

The authentication workflow generally follows:

```text
User Login
    ↓
Credentials Validated
    ↓
JWT Generated
    ↓
Token Sent to Client
    ↓
Protected API Request
    ↓
JWT Validation
    ↓
Authorized Request
```

Passwords are protected using **bcryptjs** rather than being stored as plain text.

Protected frontend routes and backend endpoints ensure that functionality is accessible according to the authenticated user's role.

---

# 13. Deployment Architecture

The application is designed to separate frontend and backend deployment.

```text
                    Internet
                       │
            ┌──────────┴──────────┐
            │                     │
            ▼                     ▼
        Vercel                Railway / Render
            │                     │
            ▼                     ▼
       Next.js App          Express.js API
                                  │
                    ┌─────────────┴─────────────┐
                    │                           │
                    ▼                           ▼
                PostgreSQL                ML Pipeline
```

### Frontend

The Next.js frontend can be deployed using **Vercel**.

### Backend

The Express.js backend can be deployed using platforms such as **Railway** or **Render**.

### Database

Production deployments can use PostgreSQL for persistent application data.

### Machine Learning

The TensorFlow.js classification pipeline can be integrated with the backend to process submitted images.

---

# 14. Future Improvements

Several improvements can extend the current platform.

### Real-Time Notifications

Introduce WebSocket-based notifications so citizens and NGOs can receive updates when report statuses change.

### Cloud Image Storage

Move image storage to dedicated cloud storage services such as Amazon S3 or Cloudinary for improved scalability and reliability.

### ML Integration

Fully integrate health classification into the report creation workflow so predictions can be generated automatically as part of case processing.

### Mobile Application

Develop a React Native mobile application to make reporting more accessible from smartphones.

### Administrative Console

Introduce a dedicated administration interface for:

* User management
* Report moderation
* System monitoring
* Platform administration

### Scalable Infrastructure

For larger deployments, the system could be extended with infrastructure such as:

* Kubernetes
* Read replicas
* Distributed storage
* Caching
* Background job processing
* Horizontal API scaling

---

# 15. Summary

Stray Shield brings together **citizen reporting, NGO case management, adoption workflows, secure authentication, and machine-learning-assisted classification** within a single full-stack platform.

Its architecture separates the presentation, API, database, and machine-learning responsibilities, allowing each part of the system to evolve independently.

The current implementation provides a foundation for community-driven stray-dog case management while leaving room for future improvements in **real-time communication, cloud infrastructure, mobile accessibility, machine-learning integration, and system scalability**.

> **Stray Shield is designed to turn community reports into actionable cases by connecting citizens, NGOs, and technology through a single platform.**
