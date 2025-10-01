# Spring Boot REST APIs – Full Course Projects & Final Todo App

This repository documents my journey through the **[Spring Boot REST APIs](https://www.udemy.com/course/spring-boot-rest-apis/)** course on Udemy.  
The course was an immersive, hands-on experience that took me from the basics of REST API development in Spring Boot to building a fully functional, production-ready **Todo Management Application** as the final project.

I have included all key learnings, project details, database setup instructions, and reflections below.

---

## 🚀 Key Learnings from the Course

This course was very practical and structured in a way that each section built up toward the final capstone project. Here’s what I mastered:

- **Spring Boot Basics**

  - How to set up a Spring Boot project with Maven.
  - Auto-configuration, starters, and application properties.

- **REST API Development**

  - Creating endpoints with `@RestController` and `@RequestMapping`.
  - Handling GET, POST, PUT, and DELETE requests.
  - Request and response DTOs with `@RequestBody` and `@PathVariable`.

- **Data Persistence with JPA & Hibernate**

  - Connecting Spring Boot to relational databases.
  - Using `Spring Data JPA` repositories for CRUD operations.
  - Entity relationships (One-to-Many, Many-to-One).

- **Validation & Exception Handling**

  - `@Valid`, `@NotNull`, `@Size` annotations for request validation.
  - Custom exception handling with `@ControllerAdvice`.

- **API Documentation with Swagger**

  - Integrated Swagger UI for exploring and testing APIs.
  - Added annotations for better API readability.

- **Testing and Deployment**
  - Using **Postman** to test endpoints.
  - Running applications locally and with **Docker**.

---

## 📌 Final Project: Todo Management Application

The highlight of this course was building the **Todo App**, which applied everything I learned in a real-world scenario.

### ✅ Features

- **User Management**

  - Register new users with username, email, and password.
  - Secure password storage (bcrypt).

- **Todo Management**

  - Add, update, delete, and list todos.
  - Todos have `title`, `description`, `status`, and `due_date`.
  - Users can only manage their own todos.

- **Validation & Error Handling**

  - Example: Todo title cannot be empty, due date must be in the future.
  - Custom error messages with JSON response format.

- **Swagger Integration**
  - API endpoints well-documented and testable via Swagger UI.

---

### 🛣️ REST Endpoints Overview

#### User Endpoints

```http
POST /api/users/register
GET  /api/users/{id}
```

#### Todo Endpoints

```http
GET    /api/todos               → List all todos for current user
POST   /api/todos               → Create a new todo
PUT    /api/todos/{id}          → Update a todo by ID
DELETE /api/todos/{id}          → Delete a todo by ID
```

#### Example JSON (Create Todo Request)

```json
{
  "title": "Finish Spring Boot course",
  "description": "Complete the final project and prepare README",
  "status": "Pending",
  "due_date": "2025-10-15"
}
```

#### Example JSON (Response)

```json
{
  "id": 1,
  "title": "Finish Spring Boot course",
  "description": "Complete the final project and prepare README",
  "status": "Pending",
  "due_date": "2025-10-15",
  "user_id": 101
}
```

---

## 🗄️ Database Schema

The database is simple yet effective, with a clear **one-to-many relationship** between `User` and `Todo`.

### Tables

**User Table**

- `id` (Primary Key)
- `username` (Unique)
- `password`
- `email`

**Todo Table**

- `id` (Primary Key)
- `title`
- `description`
- `status` (`Pending`, `In Progress`, `Completed`)
- `due_date`
- `user_id` (Foreign Key referencing `User.id`)

📌 _Schema Diagram :_  
![Database Schema](/images/hello.png)

This schema allows multiple todos per user and enforces ownership through foreign keys.

---

## 🐳 Running MySQL with Docker

During the project, we used Docker to simplify database setup. Here’s the full process:

### Step 1: Pull MySQL Image

```bash
docker pull mysql:8.0
```

### Step 2: Run MySQL Container

```bash
docker run -d -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=tododb -- name mysqldb -p 3307:3306 mysql:8.0
```

### Step 3: Verify Container

```bash
docker ps
```

### Step 4: Connect to MySQL

```bash
docker exec -it todo-mysql mysql -u todo_user -p
```

### Step 5: Configure Spring Boot

`src/main/resources/application.properties`

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/todo_db
spring.datasource.username=todo_user
spring.datasource.password=todo_pass
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

## 🛠️ Running the Todo App

1. Clone the repository:

   ```bash
   git clone https://github.com/ibrahimali00/Spring-Boot-REST-APIs.git
   cd todo-app-springboot
   ```

2. Start the MySQL database (via Docker command above).

3. Run the Spring Boot application:

   ```bash
   mvn spring-boot:run
   ```

4. Access Swagger UI:
   [http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)

5. Test the endpoints with Postman or Swagger.

---

## 🔒 Security: Authentication & Authorization

Although the course focused mainly on building REST APIs and the Todo App, I extended the project with basic security concepts to better simulate real-world applications.

### Authentication

- Implemented **JWT (JSON Web Tokens)** for stateless authentication.
- On login, the server generates a signed JWT that contains user details.
- The token is included in the `Authorization: Bearer <token>` header for all protected requests.

### Authorization

- Configured role-based access control (`ROLE_USER`, `ROLE_ADMIN`).
- **Users** can only manage their own todos.
- **Admins** can view and manage all todos across users.

### Spring Security Setup

In `SecurityConfig.java`:

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.csrf().disable()
        .authorizeRequests()
        .antMatchers("/api/users/register", "/api/auth/login").permitAll()
        .antMatchers("/api/admin/**").hasRole("ADMIN")
        .anyRequest().authenticated()
        .and()
        .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);

    http.addFilterBefore(jwtAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
}
```

### Example Flow

1. **Register a new user** with `/api/users/register`.
2. **Login** with `/api/auth/login` → receive a JWT token.
3. **Access secured endpoints** by including the JWT token in the request header.

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

This ensures only authenticated users can interact with the API, and authorization rules decide who can access what resources.

---

## 📖 Reflections as a Student

Completing this course has been transformative for me as a Java developer. Some key reflections:

- **Practical Learning**: Instead of just theory, every concept was backed by real coding exercises.
- **Confidence with APIs**: I can now design and implement APIs for real-world projects.
- **Best Practices**: The instructor emphasized code structure, validation, exception handling, and API documentation.
- **Final Project Impact**: The Todo App consolidated everything. I now understand how to integrate Spring Boot with MySQL, run services with Docker, and expose APIs with Swagger.
- **Next Steps**: I plan to extend this Todo App by adding JWT authentication, user roles, and front-end integration with React.

---

## 🙌 Acknowledgments

Special thanks to the instructor for creating such a clear, structured, and practical course. I recommend it to any Java developer who wants to step into API development with Spring Boot.
