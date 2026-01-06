
**Lab 10 – HTTP Request Handling & Validation**

This project demonstrates secure HTTP request processing, input validation, and correct response handling using Spring Boot. It implements REST endpoints for user management, validating inputs, handling errors, and returning meaningful HTTP status codes.

---

## 📝 Objective

- Learn secure request handling and input validation.
- Implement controllers parsing headers, parameters, JSON bodies, and form data correctly.
- Return proper status codes for success, invalid input, and errors.
- Prevent common HTTP and data-related vulnerabilities.

---

## ✅ Features Implemented

### 1. API Documentation
- `API_DOCS.md` details all endpoints, methods, parameters, and expected responses.

### 2. DTO & Input Validation
- `UserDTO.java` with validation annotations:
  - `@NotBlank` and `@Size` for username
  - `@Email` for email
  - `@PasswordConstraint` for custom password validation
- Custom `PasswordValidator.java` ensures passwords contain:
  - At least one digit
  - One lowercase letter
  - One uppercase letter
  - One special character

### 3. Controllers & Request Handling
- `UserController.java` uses `@Valid UserDTO`.
- Endpoints:
  - `GET /users` – List all users
  - `POST /users` – Create user
  - `GET /users/{id}` – Retrieve user
  - `PUT /users/{id}` – Full update
  - `PATCH /users/{id}` – Partial update
  - `DELETE /users/{id}` – Delete user

### 4. Global Exception Handling
- `GlobalExceptionHandler.java` returns structured responses:
  - 400 Bad Request → validation errors
  - 409 Conflict → duplicate data
  - 500 Internal Server Error → generic exceptions

### 5. Security
- Spring Security configured to allow access to `/users/**` for testing.
- Basic authentication implemented.

### 6. Testing
- `test_endpoints.ps1` verifies all endpoints.
- Valid requests → 200/201
- Invalid requests → 400 with detailed errors
- CRUD operations fully functional
- Server runs on port 8081 to avoid conflicts

---

## ⚡ Getting Started

### Prerequisites
- Java 17+
- Maven
- PowerShell (Windows) or terminal
- curl or Postman for testing

### Clone the Repository
```bash
git clone https://github.com/aliev-ibra/Lab10Application.git
cd Lab10Application
Run the Application
powershell
Copy code
.\mvnw.cmd spring-boot:run
Test Endpoints
1. Hello Endpoint
bash
Copy code
curl.exe -v http://localhost:8081/hello
2. Create a User
bash
Copy code
curl.exe -v -X POST -H "Content-Type: application/json" -d "@user.json" http://localhost:8081/users
3. List Users (Basic Auth)
bash
Copy code
curl.exe -v -u test@example.com:password123 http://localhost:8081/users
4. Update, Patch, Delete Users
Use PUT, PATCH, and DELETE methods with JSON bodies as needed. See API_DOCS.md for full endpoint documentation.

✅ Verification
Endpoint	Expected Result
GET /users	Returns list of users
POST /users (valid)	Creates user, 201
POST /users (invalid)	Returns 400 with validation errors
PUT /users/{id}	Updates user details
PATCH /users/{id}	Partially updates user
DELETE /users/{id}	Deletes user
GET /users/{id} (after delete)	Returns 404 Not Found

📁 Project Structure
swift
Copy code
Lab10Application/
├─ src/main/java/com/example/lab10/
│  ├─ config/
│  ├─ controller/
│  ├─ model/
│  ├─ repository/
│  ├─ security/
│  ├─ service/
│  └─ Lab10Application.java
├─ src/main/resources/
│  ├─ db/migration/
│  └─ application.properties
├─ user.json
├─ user_invalid.json
├─ test_endpoints.ps1
├─ mvnw.cmd
├─ pom.xml
└─ README.md
🔒 Notes
Input validation is enforced via DTOs and custom validators.

Passwords are hashed before storage.

Basic authentication is used for user endpoints.

Errors return structured JSON for better API consumption.

