# Lab 11–12: Secure REST API with JWT Authentication

## 🛡️ Overview
This project demonstrates a secure REST API built with **Spring Boot** and **Spring Security**, focusing on authentication, authorization, and access control for a multi-user environment.  

The lab objectives are based on **Spring Security foundations**:

- Implement session-based (MVC) or token-based (REST) authentication.
- Secure the data layer and restrict access to user-specific resources.
- Validate user input, prevent SQL injection, and enforce password policies.

> **Note:** This implementation follows the **REST track**. MVC (session-based) login/logout with Thymeleaf templates and Admin role endpoints are not included but have been documented.

---

## 🔑 Features Implemented

### Authentication & Authorization
- **JWT-based Authentication**
  - `/auth/login` returns a JWT token
  - Tokens validated on every request via `JwtAuthFilter`
  - Stateless session (`SessionCreationPolicy.STATELESS`)
- **Protected Routes**
  - `/notes/**` endpoints accessible only to authenticated users
  - Users can only access their own notes (ownership checks in `NoteService`)
- **Password Policy**
  - Minimum 8 characters
  - Requires uppercase, lowercase, digit, and special character
  - Enforced via `PasswordValidator.java`
- **Roles**
  - `ROLE_USER` is implemented
  - `ROLE_ADMIN` not implemented (no endpoints or assignment logic)

### Database & Security
- **Database Layer**
  - Flyway migrations for schema management (`V1__create_user.sql`, `V2__create_note.sql`, etc.)
  - `User` and `Note` entities with proper foreign key relationships
  - SQL Injection prevented via **JPA queries with named parameters**
- **CRUD Operations**
  - Full Create, Read, Update, Delete for notes
  - Access control ensures users cannot manipulate others’ data
- **Validation & Safety**
  - DTOs (`NoteDTO`, `LoginRequest`) use `@NotBlank`, `@Size`, and `@Valid`
  - Mass assignment prevented by manually mapping DTOs to entities
  - GlobalExceptionHandler ensures safe error messages without exposing stack traces

---

## ⚡ REST Endpoints

| Endpoint             | Method | Auth Required | Description                                |
|---------------------|--------|---------------|--------------------------------------------|
| `/auth/register`     | POST   | No            | Register new user                           |
| `/auth/login`        | POST   | No            | Login and receive JWT                        |
| `/notes`             | GET    | Yes           | Get all notes for current user              |
| `/notes`             | POST   | Yes           | Create a new note                            |
| `/notes/{id}`        | GET    | Yes           | Retrieve specific note (ownership enforced) |
| `/notes/{id}`        | PUT    | Yes           | Update note (ownership enforced)            |
| `/notes/{id}`        | DELETE | Yes           | Delete note (ownership enforced)            |

> **Note:** All endpoints return JSON responses. MVC/Thymeleaf endpoints are not implemented.

---

## ✅ Security Verification Results

| Test Case                                 | Result | Notes |
|-------------------------------------------|--------|-------|
| Weak Password Enforcement                  | PASS   | Attempt to register with "weakpass" blocked |
| SQL Injection Prevention                   | PASS   | Attempt to inject via login blocked          |
| Unauthorized Access                        | PASS   | `/notes` without token returns 403 Forbidden |
| Ownership Verification                      | PASS   | Users cannot access others' notes            |
| Token Validation                           | PASS   | JWT validated on every request               |
| Valid Registration & Login                 | PASS   | Normal flow works as expected                |

> All tests were executed using `verify_lab.ps1` (PowerShell script).

---

## 📝 Notes / Missing Items
- **Admin Role:** Not implemented. `ROLE_ADMIN` is missing, and no admin-specific endpoints exist.
- **MVC Track:** No Thymeleaf templates or session-based login/logout. Project is purely REST.
- **CSRF:** Disabled (correct for stateless REST API).

---

## 🚀 Getting Started

### Requirements
- Java 21+
- Maven 3.9+
- Optional: Postman or cURL for testing REST endpoints

### Running the Application
```bash
# Set JAVA_HOME
$env:JAVA_HOME="tools/jdk-21.0.9+10"

# Run using bundled Maven
tools\apache-maven-3.9.12\bin\mvn.cmd spring-boot:run
The server will start on: http://localhost:8082
