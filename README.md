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


## 🧪 Testing & Verification

This section describes how the application was tested to verify security, authentication, and access control requirements.

### 1️⃣ Application Startup Test

The application was started using local Maven:

```bash
tools\apache-maven-3.9.12\bin\mvn.cmd spring-boot:run
✔ Server started successfully
✔ Application available at http://localhost:8082
✔ No runtime errors during startup

2️⃣ Registration & Password Policy Test
Test: Register user with weak password

json
Copy code
{
  "username": "testuser",
  "password": "123456"
}
Result: ❌ Rejected
✔ Password validation correctly enforced

Test: Register user with strong password

json
Copy code
{
  "username": "testuser",
  "password": "StrongP@ssw0rd!"
}
Result: ✅ Accepted
✔ User stored in database with encrypted password

3️⃣ Authentication (Login) Test
Endpoint: POST /auth/login

Test: Login with valid credentials
✔ JWT token returned in response

Test: Login with invalid password
✔ Authentication rejected
✔ No sensitive error information leaked

4️⃣ Unauthorized Access Test
Test: Access protected endpoint without JWT

http
Copy code
GET /notes
Result:
❌ 403 Forbidden
✔ Security filter correctly blocks unauthenticated access

5️⃣ Authorized Access Test
Test: Access /notes with valid JWT in Authorization header

http
Copy code
Authorization: Bearer <JWT_TOKEN>
Result:
✔ Access granted
✔ User-specific notes returned

6️⃣ Ownership Enforcement Test
Test: User A attempts to access User B’s note

http
Copy code
GET /notes/{otherUserNoteId}
Result:
❌ Access denied
✔ Ownership verification works correctly

7️⃣ SQL Injection Test
Test Input:

json
Copy code
{
  "username": "' OR '1'='1",
  "password": "test"
}
Result:
❌ Login failed
✔ SQL injection prevented by JPA parameterized queries

8️⃣ CRUD Functionality Test
Operation	Result
Create Note	✅ Success
Read Note	✅ Success
Update Note	✅ Success
Delete Note	✅ Success

✔ All operations restricted to authenticated user only

9️⃣ Error Handling Test
Test: Access non-existing resource
✔ Safe error message returned
✔ No stack trace or internal details exposed

✅ Test Conclusion
All security and functional requirements for the REST-based lab implementation were successfully tested:

Authentication and authorization function correctly

Access control and ownership enforcement work as expected

Input validation and SQL injection protection are effective

Application behaves securely under invalid and unauthorized requests
