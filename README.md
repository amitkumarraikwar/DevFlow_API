# DevFlow API

**A Secure Project & Task Management REST API**

DevFlow API is a backend-focused project management system built with Node.js, Express.js, JavaScript, and MongoDB. It provides a structured REST API for managing projects, team members, tasks, comments, and project activity.

The primary goal of this project is to demonstrate practical backend development skills, including REST API design, authentication, authorization, database modelling, validation, error handling, security, and automated testing.

> **Project Status:** Initial Setup Completed — Development Not Started

---

## 1. Project Overview

DevFlow API allows authenticated users to create and manage projects, collaborate with team members, assign tasks, track progress, and maintain an activity history.

### Primary Goals

- Build a secure and scalable REST API.
- Implement authentication using JWT.
- Implement role-based access control.
- Design a structured MongoDB database using Mongoose.
- Follow modular and maintainable backend architecture.
- Implement request validation and centralized error handling.
- Write automated unit and integration tests.
- Provide API documentation using Swagger/OpenAPI.

### Planned Features

| Feature | Description |
|---|---|
| Authentication | Registration, login, logout and token refresh |
| User Management | User profiles and account management |
| Project Management | Create, read, update and delete projects |
| Team Management | Add, remove and manage project members |
| Role Management | Owner, Manager and Member permissions |
| Task Management | Create, assign and track tasks |
| Comments | Add and view task comments |
| Activity Logs | Track project activities |
| API Documentation | Interactive Swagger documentation |

### Out of Scope for Version 1

- Frontend application
- Mobile application
- Payment integration
- Real-time chat
- File uploads
- Microservices
- Redis and BullMQ
- Kubernetes

---

## 2. Technology Stack

| Technology | Purpose |
|---|---|
| Node.js | JavaScript runtime |
| Express.js | Backend framework |
| JavaScript (ES6+) | Programming language |
| MongoDB | NoSQL database |
| Mongoose | MongoDB ODM |
| JWT | Authentication |
| bcrypt | Password hashing |
| Zod | Request validation |
| dotenv | Environment variables |
| Helmet | HTTP security headers |
| CORS | Cross-origin resource sharing |
| express-rate-limit | API rate limiting |
| Vitest | Automated testing |
| Supertest | HTTP integration testing |
| Swagger / OpenAPI | API documentation |
| Git & GitHub | Version control |

### Development Dependencies

- Nodemon — Automatic server restarting during development.

---

## 3. Project Architecture

The application follows a modular architecture with separate responsibilities for routes, controllers, services, middleware, and database models.

### Planned Folder Structure

```text
devflow-api/
│
├── src/
│   ├── app.js
│   ├── server.js
│   │
│   ├── config/
│   │   ├── env.js
│   │   └── database.js
│   │
│   ├── controllers/
│   │
│   ├── middleware/
│   │   ├── authenticate.js
│   │   ├── require-role.js
│   │   ├── validate.js
│   │   ├── error-handler.js
│   │   └── not-found.js
│   │
│   ├── modules/
│   │   ├── auth/
│   │   ├── users/
│   │   ├── projects/
│   │   ├── members/
│   │   ├── tasks/
│   │   ├── comments/
│   │   └── activity/
│   │
│   ├── services/
│   │
│   ├── lib/
│   │   ├── mongoose.js
│   │   └── logger.js
│   │
│   └── utils/
│       ├── api-error.js
│       ├── async-handler.js
│       └── generate-tokens.js
│
├── tests/
│   ├── integration/
│   └── unit/
│
├── .env
├── .env.example
├── .gitignore
├── package.json
├── package-lock.json
└── README.md
```

*Note: This is the target architecture. The directory structure may evolve during implementation.*

### Layer Responsibilities

| Layer | Responsibility |
|---|---|
| Routes | Define API endpoints |
| Controllers | Handle HTTP requests and responses |
| Services | Contain business logic |
| Models | Define MongoDB schemas |
| Middleware | Authentication, authorization and validation |
| Config | Environment and database configuration |
| Utils | Reusable helper functions |
| Tests | Verify API functionality and security |

---

## 4. Database Design

MongoDB will be used as the primary database, with Mongoose for schema definition, relationships, validation, and queries.

### Planned Collections

**1. Users**

- _id
- name
- email (unique)
- password
- createdAt
- updatedAt

**2. RefreshSessions**

- _id
- userId
- tokenHash
- expiresAt
- revokedAt
- createdAt

**3. Projects**

- _id
- name
- description
- ownerId
- createdAt
- updatedAt

**4. ProjectMembers**

- _id
- projectId
- userId
- role
- joinedAt

**5. Tasks**

- _id
- projectId
- title
- description
- status
- priority
- assigneeId
- createdById
- dueAt
- createdAt
- updatedAt

**6. Comments**

- _id
- taskId
- authorId
- content
- createdAt
- updatedAt

**7. ActivityLogs**

- _id
- projectId
- actorId
- action
- entityType
- entityId
- metadata
- createdAt

### Recommended Enums

**Project Roles**
- OWNER
- MANAGER
- MEMBER

**Task Status**
- TODO
- IN_PROGRESS
- DONE

**Task Priority**
- LOW
- MEDIUM
- HIGH
- URGENT

### Data Integrity Rules

- Every project must have an owner.
- A user cannot be added to the same project multiple times.
- Task assignees must belong to the respective project.
- Only project members can access project tasks and comments.
- Database indexes should be created for frequently queried fields.
- Use MongoDB transactions where multiple related operations must succeed or fail together.

---

## 5. Authentication & Authorization

Authentication verifies the identity of a user, while authorization determines what that user is allowed to do.

### Authentication Requirements

- Register and login using email and password.
- Hash passwords using bcrypt.
- Issue short-lived JWT access tokens.
- Implement refresh token rotation.
- Store only hashed refresh tokens in the database.
- Revoke refresh sessions during logout.
- Allow users to view and revoke their active sessions.
- Never expose passwords, password hashes, or tokens in API responses.

### Role-Based Access Control

| Action | Owner | Manager | Member |
|---|---|---|---|
| View project and tasks | Yes | Yes | Yes |
| Update project details | Yes | Yes | No |
| Invite members | Yes | Yes | No |
| Remove members | Yes | Yes* | No |
| Create tasks | Yes | Yes | Yes |
| Assign/update tasks | Yes | Yes | Yes** |
| Delete project | Yes | No | No |

*Subject to owner and role restrictions.*

**Within permitted project rules.*

Managers must not be able to promote themselves to owners or remove the final project owner.

---

## 6. API Contract

All API endpoints will use the following base path:

```text
/api/v1
```

### Authentication APIs

| Method | Endpoint | Purpose |
|---|---|---|
| POST | /auth/register | Register a new user |
| POST | /auth/login | Authenticate user |
| POST | /auth/refresh | Refresh access token |
| POST | /auth/logout | Revoke current session |
| GET | /auth/sessions | List user sessions |
| DELETE | /auth/sessions/:id | Revoke a session |

### User APIs

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /users/me | Get current profile |
| PATCH | /users/me | Update current profile |

### Project APIs

| Method | Endpoint | Purpose |
|---|---|---|
| POST | /projects | Create project |
| GET | /projects | List accessible projects |
| GET | /projects/:projectId | Get project details |
| PATCH | /projects/:projectId | Update project |
| DELETE | /projects/:projectId | Delete project |

### Member APIs

| Method | Endpoint | Purpose |
|---|---|---|
| POST | /projects/:projectId/members | Add member |
| GET | /projects/:projectId/members | List members |
| PATCH | /projects/:projectId/members/:userId | Change member role |
| DELETE | /projects/:projectId/members/:userId | Remove member |

### Task APIs

| Method | Endpoint | Purpose |
|---|---|---|
| POST | /projects/:projectId/tasks | Create task |
| GET | /projects/:projectId/tasks | List project tasks |
| GET | /tasks/:taskId | Get task details |
| PATCH | /tasks/:taskId | Update task |
| DELETE | /tasks/:taskId | Delete task |

### Comment APIs

| Method | Endpoint | Purpose |
|---|---|---|
| POST | /tasks/:taskId/comments | Add comment |
| GET | /tasks/:taskId/comments | List comments |

### Activity APIs

| Method | Endpoint | Purpose |
|---|---|---|
| GET | /projects/:projectId/activity | Get project activity |

All endpoints are planned contracts and will be implemented and verified during development.

---

## 7. Request & Response Standards

The API will use consistent JSON responses and appropriate HTTP status codes.

### HTTP Status Codes

| Code | Meaning |
|---|---|
| 200 | Successful request |
| 201 | Resource created |
| 204 | Successful deletion |
| 400 | Invalid request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Resource not found |
| 409 | Resource conflict |
| 500 | Internal server error |

### Success Response

```json
{
  "success": true,
  "data": {
    "id": "resource-id",
    "name": "Website Refresh"
  }
}
```

### Error Response

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": []
  }
}
```

### Validation Requirements

- Validate request bodies, route parameters, and query parameters.
- Reject unauthorized or protected fields.
- Set reasonable maximum lengths for inputs.
- Implement pagination for collection endpoints.
- Allowlist sorting fields.
- Use generic login errors.
- Never expose internal database errors or stack traces.

---

## 8. Testing Requirements

Automated tests are a required part of the project.

**Testing Stack:** Vitest + Supertest

### Planned Test Cases

| Area | Test Cases |
|---|---|
| Registration | Valid registration, invalid email, weak password, duplicate email |
| Login | Valid login, incorrect password, unknown email |
| Sessions | Token rotation, logout, revoked session |
| Projects | CRUD, unauthorized access |
| Membership | Duplicate members, invalid role, owner restrictions |
| Tasks | CRUD, invalid status, invalid assignee |
| Comments | Authorized access, invalid content |
| Errors | Validation errors, 404 routes, internal errors |

Use a separate test database and clean up test records between tests.

Test authorization with at least two users and two projects.

---

## 9. Development Roadmap

The project will be implemented in the following milestones.

| Milestone | Deliverable |
|---|---|
| M1: Foundation | Project configuration, Express server, MongoDB connection, error handling |
| M2: Authentication | Registration, login, JWT, refresh sessions |
| M3: Projects | Project CRUD and ownership permissions |
| M4: Membership | Team management and role-based permissions |
| M5: Tasks | Task CRUD, assignments, comments and activity logs |
| M6: Documentation | Swagger, README and API examples |
| M7: Testing & Release | Integration tests, security review and deployment |

### Suggested 10-Day Schedule

| Day | Focus |
|---|---|
| 1 | Project setup and MongoDB connection |
| 2 | Database models and relationships |
| 3 | Registration, login and password hashing |
| 4 | JWT, refresh sessions and logout |
| 5 | Project CRUD |
| 6 | Membership and role permissions |
| 7 | Task CRUD and comments |
| 8 | Activity logs, filters and pagination |
| 9 | Swagger and automated testing |
| 10 | README, final verification and deployment |

The schedule is a planning target. Security and testing must not be skipped to meet deadlines.

---

## 10. Git Workflow & Developer Guidelines

### Git Commit Convention

Use small, descriptive commits.

Examples:

```text
chore: initialize project structure
feat(auth): add user registration
feat(auth): implement login
feat(projects): add project CRUD
feat(tasks): implement task assignment
test(auth): add authentication tests
docs: update API documentation
```

### Development Rules

- Never commit `.env` files or secrets.
- Keep business logic inside service files.
- Keep controllers focused on HTTP request and response handling.
- Keep routes thin.
- Use centralized error handling.
- Use reusable middleware.
- Follow consistent naming conventions.
- Write tests alongside features.
- Document architectural decisions and known limitations.

### Required README Documentation

- Project overview
- Technology stack
- Architecture
- Database models
- Environment variables
- Installation instructions
- API documentation
- Authentication and permissions
- Testing instructions
- Security considerations
- Known limitations

---

## 11. Getting Started

### Prerequisites

- Node.js (LTS)
- npm
- MongoDB (Local or MongoDB Atlas)
- Git

### Installation

Clone the repository:

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
```

Navigate into the project:

```bash
cd devflow-api
```

Install dependencies:

```bash
npm install
```

### Environment Variables

Create a `.env` file in the project root.

```env
NODE_ENV=development
PORT=3000

MONGODB_URI=mongodb://127.0.0.1:27017/devflow

JWT_ACCESS_SECRET=your_long_random_secret
JWT_REFRESH_SECRET=your_refresh_secret

ACCESS_TOKEN_TTL=15m
REFRESH_TOKEN_TTL_DAYS=30

APP_ORIGIN=http://localhost:3000
```

Never commit real environment secrets.

### Run Development Server

```bash
npm run dev
```

### Run Production Server

```bash
npm start
```

*Note: These commands reflect the initial package scripts. The entry point and scripts will be aligned with the final server implementation.*

---

## 12. Final Acceptance Criteria

The project will be considered complete when:

- [ ] User registration and login work correctly.
- [ ] Passwords are securely hashed.
- [ ] JWT authentication works correctly.
- [ ] Refresh token rotation and revocation work.
- [ ] Project CRUD operations work.
- [ ] Role-based authorization is enforced.
- [ ] Users cannot access unauthorized project data.
- [ ] Tasks and assignments work correctly.
- [ ] Comments and activity history work.
- [ ] MongoDB indexes and data integrity rules are implemented.
- [ ] Input validation is implemented.
- [ ] Centralized error handling works.
- [ ] Automated tests pass.
- [ ] Swagger documentation matches the implementation.
- [ ] README setup instructions work from a clean clone.

---

## Project Objective

DevFlow API is designed to demonstrate practical backend engineering skills through a real-world project management system.

The focus is on writing clean, secure, maintainable, and well-tested backend code rather than simply implementing basic CRUD operations.

**Build the fundamentals first. Prioritize security, data integrity, and testing.**
