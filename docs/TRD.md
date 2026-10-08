# Technical Requirements Document (TRD)

**Project Name:** SecureWeb Pro (Modernized Spring Security App)
**Document Version:** 1.0
**Status:** Draft

## 1. System Architecture

The application follows a decoupled Client-Server architecture.

* **Client:** React-based Single Page Application (SPA) responsible for routing, state management, and rendering the UI.
* **Server:** Spring Boot REST API responsible for business logic, database transactions, and issuing/validating JWTs.
* **Database:** PostgreSQL relational database.

## 2. Technology Stack

| Layer | Technology | Purpose | 
| ----- | ----- | ----- | 
| **Frontend** | React, TypeScript, Vite | Core framework and build tool | 
| **Styling** | Tailwind CSS, Shadcn UI | Responsive styling and accessible UI components | 
| **State & Fetching** | Zustand, Axios | Auth state management and API communication | 
| **Backend** | Java 17+, Spring Boot 3.x | Core API framework | 
| **Security** | Spring Security 6, JWT, BCrypt | Endpoint protection, stateless auth, password hashing | 
| **Data Layer** | Spring Data JPA, Hibernate, PostgreSQL | ORM and persistent data storage | 
| **Infrastructure** | Docker, Docker Compose | Containerization for local development | 

## 3. Security Architecture

* **Authentication Flow:**
  1. Client submits email/password to `/api/auth/login`.
  2. Backend validates credentials via `AuthenticationManager`.
  3. Backend generates a short-lived **Access Token (JWT)** (e.g., 15 mins) and a long-lived **Refresh Token** (e.g., 7 days).
  4. Access token is returned in the JSON payload; Refresh token is set as a secure, `HttpOnly` cookie.

* **Authorization:** The Access Token is sent by the client in the `Authorization: Bearer <token>` header for all protected API requests. A custom `JwtAuthenticationFilter` intercepts and validates this token.
* **CORS:** Configured on the backend to accept requests *only* from the explicit frontend origin (e.g., `http://localhost:5173`).
* **CSRF:** Disabled for the API endpoints since JWTs are not susceptible to traditional CSRF attacks (when stored in memory/local storage).

## 4. Database Schema (Draft)

**Table: `users`**

| Column | Type | Constraints | 
| ----- | ----- | ----- | 
| `id` | UUID | Primary Key | 
| `email` | VARCHAR(255) | Unique, Not Null | 
| `password` | VARCHAR(255) | Not Null (BCrypt Hash) | 
| `created_at` | TIMESTAMP | Not Null | 
| `is_enabled` | BOOLEAN | Default TRUE | 

**Table: `roles`**

| Column | Type | Constraints | 
| ----- | ----- | ----- | 
| `id` | BIGINT | Primary Key, Auto Increment | 
| `name` | VARCHAR(50) | Unique, Not Null (e.g., ROLE_USER) | 

**Table: `user_roles` (Join Table)**

| Column | Type | Constraints | 
| ----- | ----- | ----- | 
| `user_id` | UUID | Foreign Key -> users(id) | 
| `role_id` | BIGINT | Foreign Key -> roles(id) | 

## 5. API Interface Design

| Endpoint | Method | Auth Required | Description | 
| ----- | ----- | ----- | ----- | 
| `/api/auth/register` | POST | No | Creates a new user account | 
| `/api/auth/login` | POST | No | Authenticates user, returns JWT and sets Refresh cookie | 
| `/api/auth/refresh` | POST | No (Requires Cookie) | Issues a new Access Token using the HttpOnly Refresh Token | 
| `/api/users/me` | GET | Yes (Any Role) | Returns profile data for the authenticated user | 
| `/api/admin/users` | GET | Yes (ROLE_ADMIN) | Returns a paginated list of all users | 

## 6. Implementation Phases

1. **Phase 1: Backend Scaffolding & Database**
   * Initialize Spring Boot project.
   * Set up PostgreSQL connection and define JPA Entities (`User`, `Role`).
   * Implement basic CRUD repositories.

2. **Phase 2: Security & JWT Implementation**
   * Configure Spring Security `SecurityFilterChain`.
   * Implement `JwtService` for generating and parsing tokens.
   * Create Auth Controllers and the `JwtAuthenticationFilter`.

3. **Phase 3: Frontend Scaffolding & API Integration**
   * Initialize React + Vite project with Tailwind.
   * Configure Axios interceptors to automatically attach the JWT and handle 401 (Unauthorized) responses to trigger a token refresh.
   * Build Login, Register, and Dashboard views.

4. **Phase 4: Containerization**
   * Write `Dockerfile` for backend.
   * Write `Dockerfile` for frontend.
   * Create `docker-compose.yml` to orchestrate Backend, Frontend, and PostgreSQL locally.