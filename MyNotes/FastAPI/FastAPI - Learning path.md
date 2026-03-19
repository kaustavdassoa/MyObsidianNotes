
### **Phase 1: The Core Foundation**
- **Routing and Endpoints 🛣️**
    - Path operations (`GET`, `POST`, `PUT`, `DELETE`).
    - Extracting data: Path parameters vs. Query parameters.
    - Receiving data: Request bodies and Form data.
    - Handling File Uploads.
    - Setting custom HTTP Status Codes.
        
- **Data Validation with Pydantic 🛡️**
    - Building schemas with `BaseModel`.
    - Defining field constraints (e.g., string length, number ranges).
    - Creating nested models for complex JSON payloads.
    - Writing custom validation logic (using `@field_validator`).
    - Defining distinct response models to filter output data.
        
- **Concurrency and Async/Await ⚡**
    - Understanding the difference between `def` and `async def`.
    - Handling non-blocking I/O (like database calls or external API requests).
    - Running background tasks after returning a response.
#####  **Phase 1: The Core Foundation Learning Reference ** 🧱
- **FastAPI First Steps:** The official starting point for routing and endpoints.
    - [FastAPI Tutorial - First Steps](https://fastapi.tiangolo.com/tutorial/first-steps/)
- **Data Validation:** Deep dive into how Pydantic handles schemas.
    - [Pydantic Official Documentation](https://docs.pydantic.dev/latest/)
- **Concurrency:** A clear explanation of `async` and `await` in the context of the framework.
    - [FastAPI - Concurrency and async / await](https://fastapi.tiangolo.com/async/)

### **Phase 2: Intermediate Architecture**

- **Dependency Injection 💉**
    - Using `Depends()` to share logic across endpoints.
    - Managing database sessions cleanly.
    - Injecting authentication checks.
        
- **Databases and ORMs 💾**
    - Integrating relational databases (e.g., PostgreSQL, SQLite) using SQLAlchemy or SQLModel.
    - Executing asynchronous database queries.
    - Managing database schema migrations with Alembic.

### **Phase 3: Security and Polish**
- **Security and Authentication 🔐**
    - Implementing OAuth2 with Password (and hashing).
    - Generating and validating JWTs (JSON Web Tokens) for user sessions.
    - Handling CORS (Cross-Origin Resource Sharing) for frontend integration.
        
- **Middleware and Exceptions 🚦**
    - Writing custom middleware (e.g., to log request processing time).
    - Creating global, custom error handlers to standardize error responses.

##### **Phase 2 & 3: Architecture and Security Learning Reference 
- **Dependency Injection:** How to share logic cleanly across your app.
    - [FastAPI Tutorial - Dependencies](https://fastapi.tiangolo.com/tutorial/dependencies/)
- **Databases:** Integrating relational databases (like PostgreSQL or SQLite).
    - [FastAPI Tutorial - SQL (Relational) Databases](https://fastapi.tiangolo.com/tutorial/sql-databases/)
- **Security:** Implementing OAuth2 and handling user authentication.
    - [FastAPI Tutorial - Security](https://fastapi.tiangolo.com/tutorial/security/)

### **Phase 4: Production Readiness**

- **Testing 🧪**
    - Writing unit and integration tests with `pytest`.
    - Using FastAPI's `TestClient` to simulate requests.
    - Overriding dependencies (like swapping a real database for a test database).
        
- **Deployment 🚢**
    - Managing environment variables and secrets using Pydantic Settings.
    - Containerizing the application with Docker.
    - Running the app using Uvicorn (ASGI server) paired with Gunicorn for process management.
##### **Phase 4: Production Readiness 🚀Learning Reference **
- **Testing:** Simulating requests to ensure your endpoints work correctly.
    - [FastAPI Tutorial - Testing](https://fastapi.tiangolo.com/tutorial/testing/)
- **Deployment:** Concepts for running your app on a server (Uvicorn, Gunicorn, Docker).
    - [FastAPI - Deployment](https://fastapi.tiangolo.com/deployment/)

#### Reference Link 
https://www.youtube.com/playlist?list=PL-osiE80TeTsak-c-QsVeg0YYG_0TeyXI