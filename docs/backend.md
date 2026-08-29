# Mykira Backend


## Technology Stack

| Component | Technology | Purpose |
|---|---|---|
| Framework | FastAPI | REST API development |
| Programming Language | Python | Backend application development |
| Database | PostgreSQL | Persistent relational data storage |
| ORM | SQLAlchemy | Database models and queries |
| Authentication | JWT | Secure API authentication |
| JWT Algorithm | RS256 | Asymmetric token signing |
| Password Hashing | Argon2id | Secure password storage |
| MFA | TOTP / PyOTP | Multi-factor authentication |
| OAuth | Google and Microsoft OAuth 2.0 | Third-party authentication |
| Cache / Sessions | Redis | Token storage and rate limiting |
| Local Redis Alternative | FakeRedis | Local development fallback |
| AI Engine | Google Gemini | Pregnancy health AI assistant |
| Real-Time Communication | FastAPI WebSockets | Community chat |
| Email | SMTP | Verification and password reset emails |
| Deployment | Heroku | Cloud deployment |
| CI/CD | GitHub Actions | Automated deployment |

# Prerequisites

Before running the MyKira backend, install the following software.

| Tool | Recommended Version | Purpose |
|---|---|---|
| Python | 3.11+ / project-compatible version | Run the backend |
| PostgreSQL | Latest supported version | Database |
| Redis | Optional for local development | Cache and rate limiting |
| Git | Latest | Version control |
| pip | Latest | Python package installation |
| VS Code | Latest | Development environment |

---

# Setup and Installation

## Step 1: Open the Backend Project

Extract or clone the backend project and open it using Visual Studio Code.

The main backend directory contains important files such as:

```text
main.py
database.py
config.py
auth_dependency.py
gemini.py
requirements.txt
Procfile
mykira/
markdown_knowledge/
```

---

## Step 2: Open a Terminal

Open the VS Code terminal and navigate to the backend folder.

```bash
cd path/to/backend
```

---

## Step 3: Create a Virtual Environment

A virtual environment keeps the project's Python packages isolated from other Python projects.

### Linux/macOS

```bash
python -m venv venv
source venv/bin/activate
```

### Windows PowerShell

```powershell
python -m venv venv
venv\Scripts\Activate.ps1
```

### Windows Command Prompt

```cmd
python -m venv venv
venv\Scripts\activate
```

---

## Step 4: Install Dependencies

Install all required Python packages.

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

The backend uses packages for FastAPI, SQLAlchemy, PostgreSQL, JWT authentication, Argon2 password hashing, Redis, MFA, Google Gemini, OAuth, and WebSocket functionality.

---

# Running the Backend

The FastAPI application object is named `mykira`.

Run the development server using:

```bash
uvicorn main:mykira --reload --host 0.0.0.0 --port 8000
```

The `--reload` option automatically restarts the server when source code changes during development.

After starting the server, open:

```text
http://localhost:8000/docs
```

to access Swagger UI.

---
# Architecture & Folder Structure


The backend follows a strict layered architecture separating concerns across routers, services, repositories, and models.


```
Dataverse_Backend/
├── main.py                     # FastAPI app factory, CORS, router mounting
├── database.py                 # SQLAlchemy engine, SessionLocal, Base
├── config.py                   # Pydantic Settings (env var management)
├── auth_dependency.py          # JWT auth, password hashing, MFA, role guards
├── gemini.py                   # Google Gemini AI integration
├── oauth.py                    # OAuth 2.0 config (Google + Microsoft)
├── private.pem / public.pem    # RS256 JWT key pair
├── requirements.txt
├── .env
│
├── mykira/
│   ├── core/
│   │   ├── oauth.py            # OAuth provider URLs and env vars
│   │   └── redis_client.py     # Redis connection with FakeRedis fallback
│   │
│   ├── dependencies/
│   │   └── rate_limit.py       # API rate-limiting dependency
│   │
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py             # User account model
│   │   ├── risk_evaluation.py  # Maternal risk assessment model
│   │   ├── community_post.py   # Community forum posts
│   │   ├── ai_response.py      # AI conversation history
│   │   ├── challenges.py       # Challenge definitions
│   │   ├── user_challenges.py  # User challenge enrollments
│   │   └── enum.py             # All SQLAlchemy/Pydantic enums
│   │
│   ├── repositories/
│   │   ├── user.py             # User CRUD operations
│   │   ├── risk_evaluation.py  # Risk evaluation queries
│   │   ├── community_post.py   # Community post queries
│   │   ├── ai_response.py      # AI conversation queries
│   │   ├── challenges.py       # Challenge CRUD
│   │   └── user_challenges.py  # User challenge CRUD
│   │
│   ├── services/
│   │   ├── user.py             # User business logic
│   │   ├── risk_evaluation.py  # Risk scoring algorithm
│   │   ├── community_post.py   # Room assignment & access control
│   │   ├── ai_response.py      # Gemini chat orchestration
│   │   ├── challenges.py       # Challenge lifecycle
│   │   ├── user_challenges.py  # Challenge enrollment service
│   │   ├── stat.py             # Dashboard statistics
│   │   ├── email.py            # SMTP email sending
│   │   ├── security.py         # Login rate limiting & API quotas
│   │   └── pregnancy.py        # Gestational calculations
│   │
│   ├── routers/
│   │   ├── auth.py             # Authentication endpoints
│   │   ├── user.py             # User management endpoints
│   │   ├── risk_evaluation.py  # Risk assessment endpoints
│   │   ├── community_post.py   # Forum & WebSocket endpoints
│   │   ├── ai_response.py      # AI chat endpoints
│   │   ├── challenges.py       # Challenge management endpoints
│   │   ├── user_challenges.py  # User challenge endpoints
│   │   └── stat.py             # Statistics endpoints
│   │
│   └── schemas/
│       ├── user.py             # User Pydantic schemas
│       ├── risk_evaluation.py  # Risk evaluation schemas
│       ├── community_post.py   # Post schemas
│       ├── ai_response.py      # AI chat schemas
│       ├── challenges.py       # Challenge schemas
│       ├── user_challenges.py  # User challenge schemas
│       └── stat.py             # Statistics schemas
│
└── markdown_knowledge/         # Gemini knowledge base (Markdown files)
   └── gemini_registry.json    # File upload registry
```


---


## Request Flow


```mermaid
flowchart TD
   Client --> FastAPI_Router["FastAPI Router"]
   FastAPI_Router --> Auth["Authentication / Authorization (JWT, Role Checks)"]
   Auth --> Validation["Pydantic Schema Validation"]
   Validation --> Service["Service Layer (Business Logic)"]
   Service --> Repo["Repository Layer (Database Access)"]
   Repo --> DB[("PostgreSQL")]
   DB --> Service
   Service --> Response["API Response"]
```


### For AI-Enabled Operations:


```mermaid
graph TD
    Client --> API_Router["API Router"]
    API_Router --> Service["Service Layer"]
    Service --> Gemini["Gemini API + Knowledge Retrieval"]
    Gemini --> GenResponse["Generated Response"]
    GenResponse --> AIRepo["AI Response Repository"]
    AIRepo --> DB[("PostgreSQL")]
    AIRepo --> Response["API Response"]
```

## Database Architecture and Relationships

The following table describes the relationships between the main database models. These relationships are implemented using foreign keys and define how data is connected throughout the MyKira backend.

| Parent Model        | Related Model       | Relationship                 | Foreign Key                                                | Description                                                                                |
| :------------------ | :------------------ | :--------------------------- | :--------------------------------------------------------- | :----------------------------------------------------------------------------------------- |
| **User**            | **Risk Evaluation** | One-to-Many                  | `risk_evaluation.user_id` → `user.user_id`                 | A user can have multiple risk evaluations, while each risk evaluation belongs to one user. |
| **User**            | **AI Response**     | One-to-Many                  | `ai_response.user_id` → `user.user_id`                     | A user can have multiple AI conversations or responses.                                    |
| **User**            | **Community Post**  | One-to-Many                  | `community_post.user_id` → `user.user_id`                  | A user can create multiple community posts, while each post belongs to one user.           |
| **User**            | **Challenge**       | One-to-Many                  | `challenge.reviewed_by` → `user.user_id`                   | An authorized user or administrator can review multiple challenges.                        |
| **User**            | **User Challenge**  | One-to-Many                  | `user_challenge.user_id` → `user.user_id`                  | A user can participate in multiple challenges through the participation records.           |
| **Challenge**       | **User Challenge**  | One-to-Many                  | `user_challenge.challenge_id` → `challenge.challenge_id`   | A challenge can have multiple participating users.                                         |
| **Challenge**       | **Community Post**  | One-to-Many (Optional)       | `community_post.challenge_id` → `challenge.challenge_id`   | A community post may optionally be associated with a challenge.                            |
| **Risk Evaluation** | **AI Response**     | One-to-Many (Optional)       | `ai_response.risk_id` → `risk_evaluation.risk_id`          | An AI response may optionally use a risk evaluation as contextual information.             |
| **Community Post**  | **Community Post**  | Self-Referencing One-to-Many | `community_post.parent_post_id` → `community_post.post_id` | A post can have multiple replies, and each reply can optionally reference a parent post.   |


---

## Enumerations (`mykira/models/enum.py`)

The application uses enumerations to enforce consistent values across database records, validation schemas, and business logic.

```python
class RoleEnum(str, enum.Enum):
    MOTHER = "mother"
    ADMIN = "admin"


class RiskLevelEnum(str, enum.Enum):
    LOW = "low"
    MEDIUM = "medium"
    HIGH = "high"


class TrimesterEnum(str, enum.Enum):
    FIRST = "first"
    SECOND = "second"
    THIRD = "third"


class ChallengeCategoryEnum(str, enum.Enum):
    NUTRITION = "nutrition"
    MOVEMENT = "movement"
    SELF_CARE = "self_care"


class ChallengeApprovalStatusEnum(str, enum.Enum):
    PENDING = "pending"
    APPROVED = "approved"
    REJECTED = "rejected"


class UserChallengeStatusEnum(str, enum.Enum):
    NOT_STARTED = "NOT_STARTED"
    IN_PROGRESS = "IN_PROGRESS"
    COMPLETED = "COMPLETED"
    MISSED = "MISSED"


class TokenType(str, enum.Enum):
    ACCESS = "access"
    REFRESH = "refresh"
```

# Repository Layer

The repository layer abstracts database operations from business logic. Repositories provide a dedicated data-access interface, allowing services to work with database models without embedding database queries directly in API routers.

## Repository Layer Flow

```mermaid
%%{init: {"theme": "base", "themeVariables": {"primaryColor": "#7C3AED", "primaryTextColor": "#FFFFFF", "primaryBorderColor": "#5B21B6", "lineColor": "#7C3AED"}}}%%
graph LR
    Router["API Router"] --> Service["Service Layer"]
    Service --> UserRepo["User Repository"]
    Service --> RiskRepo["Risk Evaluation Repository"]
    Service --> CommunityRepo["Community Post Repository"]
    Service --> ChallengeRepo["Challenge Repository"]
    Service --> UserChallengeRepo["User Challenge Repository"]
    Service --> AIRepo["AI Response Repository"]
    UserRepo --> DB[("PostgreSQL")]
    RiskRepo --> DB
    CommunityRepo --> DB
    ChallengeRepo --> DB
    UserChallengeRepo --> DB
    AIRepo --> DB
```

# Service Layer

The service layer contains the core business logic of the MyKira backend. Services sit between the API routers and repositories and are responsible for validation, calculations, authorization decisions, coordination with external services, and database operations.

## Service Layer Architecture

```mermaid
%%{init: {"theme": "base", "themeVariables": {"primaryColor": "#7C3AED", "primaryTextColor": "#FFFFFF", "primaryBorderColor": "#5B21B6", "lineColor": "#7C3AED"}}}%%
graph TD
    Client["Client Application"] --> Router["API Router"]
    Router --> Service["Service Layer"]
    Service --> UserService["User Service"]
    Service --> RiskService["Risk Evaluation Service"]
    Service --> CommunityService["Community Post Service"]
    Service --> AIService["AI Response Service"]
    Service --> ChallengeService["Challenge Service"]
    Service --> SecurityService["Security Service"]
    UserService --> Repository["Repository Layer"]
    RiskService --> Repository
    CommunityService --> Repository
    ChallengeService --> Repository
    AIService --> Repository
    AIService --> Gemini["Gemini AI"]
    SecurityService --> Redis[("Redis")]
```
# API Layer (Routers)

The API layer exposes the backend functionality through FastAPI routers. Routers receive HTTP requests, apply authentication and validation, delegate business logic to the service layer, and return standardized API responses.

## Router Overview

| Router                     | Base Path           | Main Responsibility                                                                | Access                 |
| :------------------------- | :------------------ | :--------------------------------------------------------------------------------- | :--------------------- |
| **Authentication Router**  | `/auth`             | Registration, login, JWT management, password reset, MFA, and OAuth authentication | Public / Authenticated |
| **User Router**            | `/users`            | User profile management, account administration, and appointment information       | Authenticated          |
| **Risk Evaluation Router** | `/risk-evaluations` | Creating, managing, and retrieving pregnancy risk evaluations and statistics       | Authenticated / Admin  |
| **Community Post Router**  | `/community_posts`  | Community posts, room-based communication, and real-time WebSocket interaction     | Authenticated          |
| **AI Response Router**     | `/ai-responses`     | AI conversations and retrieval of user conversation history                        | Authenticated          |
| **Challenge Router**       | `/challenges`       | Creating, reviewing, approving, and managing wellness challenges                   | Authenticated / Admin  |
| **User Challenge Router**  | `/user-challenges`  | Challenge enrollment, progress tracking, and participation management              | Authenticated          |
| **Statistics Router**      | `/stats`            | Administrative dashboard, risk, community, and AI usage statistics                 | Admin                  |


# Schemas

The `schemas` directory contains Pydantic models.

Schemas are responsible for:

- Validating incoming API requests
- Defining expected data types
- Defining API response structures
- Preventing invalid data from reaching the business layer
- Documenting API data through FastAPI

The backend contains schemas for:

```text
users
risk evaluations
community posts
AI responses
challenges
user challenges
statistics
```

A typical data flow is:

```text
Client JSON
    ↓
Pydantic Schema Validation
    ↓
Service Layer
    ↓
Database
    ↓
Response Schema
    ↓
Client JSON Response
```

---

# Main Application Files

## `main.py`

`main.py` is the entry point of the FastAPI application.

Its main responsibilities include:

- Creating the FastAPI application
- Configuring CORS middleware
- Registering API routers
- Applying rate-limiting dependencies
- Creating database tables during startup
- Creating the default administrator when necessary

The application includes routers for:

- Authentication
- Users
- Risk evaluations
- AI responses
- Community posts
- Challenges
- User challenges
- Statistics

Authentication routes use their own login security controls, while other API routes use the general API quota system.

---

## `database.py`

The `database.py` file manages the PostgreSQL database connection.

Its responsibilities include:

- Reading the database URL
- Creating the SQLAlchemy engine
- Creating database sessions
- Providing the SQLAlchemy declarative base
- Providing the `get_db()` dependency

The database URL can be configured using:

```env
DATABASE_URL=postgresql://username:password@localhost:5432/mykira_db
```

A local development database configuration can use:

```text
postgresql://postgres:postgres@localhost:5432/mykira_db
```

The application also supports conversion of legacy Heroku URLs beginning with:

```text
postgres://
```

into the SQLAlchemy-compatible:

```text
postgresql://
```

The database session flow is:

```text
API Request
    ↓
get_db()
    ↓
Create Database Session
    ↓
Repository / Service Operations
    ↓
Close Session
```

---

## `config.py`

The configuration file centralizes application settings and environment variables.

Configuration values should be stored in environment variables rather than hard-coded in source files.

Important configuration areas include:

- Database settings
- Redis settings
- JWT settings
- OAuth credentials
- Gemini API configuration
- Email configuration
- MFA configuration
- Default administrator configuration

---

## `auth_dependency.py`

This file contains important authentication and authorization functionality.

It is responsible for features such as:

- JWT token creation and validation
- Password hashing and verification
- Current user extraction
- Role validation
- Resource ownership checks
- MFA secret generation
- TOTP verification
- Token revocation validation

Important authorization helpers include:

```text
get_current_user
require_role
require_admin
require_self_or_admin
check_resource_ownership
```

---

## `gemini.py`

The `gemini.py` file manages integration with Google Gemini.

Its responsibilities include:

- Creating the Gemini client
- Loading Markdown knowledge files
- Uploading knowledge files to Gemini
- Maintaining the Gemini file registry
- Generating AI responses
- Applying medical safety instructions
- Handling stale file references
- Providing fallback responses when Gemini is unavailable

---


## Authentication Header

Protected endpoints use Bearer authentication.

```http
Authorization: Bearer <access_token>
```

---

## Authorization Rules

The backend uses several authorization levels.

| Authorization | Description |
|---|---|
| Public | No authentication required |
| Bearer | Valid JWT required |
| Self | User can access their own resources |
| Admin | Administrator privileges required |
| Self/Admin | Resource owner or administrator |

---

# Authentication and Security

## JWT Authentication

The backend uses JWT tokens for authenticated API access.

JWT token information includes:

```json
{
  "sub": "<user_id>",
  "role": "mother or admin",
  "exp": "<expiration>",
  "iat": "<issued time>",
  "type": "access",
  "jti": "<unique token ID>"
}
```

The backend supports two primary token types:

- Access tokens
- Refresh tokens

---

## Token Revocation

Redis is used to manage revoked tokens.

```text
User Logs Out
      ↓
Token Identifier
      ↓
Redis Blacklist
      ↓
Future Requests Check Blacklist
      ↓
Revoked Token Rejected
```

Refresh tokens can also be rotated to improve security.

---

## Password Security

Passwords are stored as hashes rather than plain text.

The backend uses:

```text
Argon2id
```

Password security includes:

- Secure password hashing
- Password verification
- Support for migrating legacy password hashes
- Secure password reset tokens
- Expiring reset tokens

---

## Multi-Factor Authentication

MyKira supports TOTP-based MFA.

The setup process is:

```text
Generate Secret
      ↓
Generate QR Code
      ↓
User Scans with Authenticator App
      ↓
User Provides Verification Code
      ↓
Backend Verifies Code
      ↓
MFA Enabled
```

---

## OAuth Authentication

The backend supports OAuth authentication through:

- Google
- Microsoft

The general OAuth process is:

```text
User Selects OAuth Provider
        ↓
Redirect to Provider
        ↓
User Authenticates
        ↓
Provider Returns Authorization Code
        ↓
Backend Exchanges Code for Token
        ↓
Backend Retrieves User Information
        ↓
Create or Link MyKira Account
        ↓
Issue JWT Token
```

OAuth accounts can exist without a local password.

---

## Role-Based Access Control

The backend uses role-based authorization.

```text
Admin
  ↓
Full Administrative Access

Mother
  ↓
Personal Application Access
```

The backend checks both:

- User role
- Resource ownership

This prevents normal users from accessing another user's private information.

---

# Rate Limiting

The backend implements two important protection systems.

## Login Rate Limiting

Repeated failed login attempts are monitored.

The general security configuration is:

```text
Login Window:      60 seconds
Maximum Attempts:  5
Block Duration:    15 minutes
```

This helps protect accounts from repeated password guessing attempts.

---

## API Rate Limiting

Authenticated users are limited to a defined number of API requests.

```text
Window:        1 Hour
Maximum Calls: 100 per user
```

When the limit is exceeded, the backend returns HTTP `429`.

---

# CORS Configuration

The backend uses CORS middleware to allow frontend applications to communicate with the API.

During development, the configuration may allow broad origins.

For production, the allowed origins should be restricted to the official frontend domains.

Example production concept:

```text
Allowed:
https://mykira.example.com

Not recommended:
*
```


# Error Handling

The backend handles errors at multiple layers.

| Layer | Error Handling |
|---|---|
| Repository | Database rollback when required |
| Service | Business validation and exceptions |
| Router | HTTP exceptions and dependency validation |
| Authentication | Authentication and permission errors |
| AI | Safe fallback response |

Common HTTP status codes include:

| Status | Meaning |
|---|---|
| 200 | Request successful |
| 201 | Resource created |
| 204 | Resource deleted successfully |
| 400 | Invalid request |
| 401 | Authentication failed or required |
| 403 | Permission denied |
| 404 | Resource not found |
| 409 | Resource conflict |
| 422 | Validation error |
| 429 | Rate limit exceeded |
| 500 | Server error |

FastAPI automatically provides structured validation errors when request data does not match the expected schema.

---

# Deployment

## Heroku Deployment

The backend includes configuration for Heroku deployment.

The production server should run the FastAPI application using Uvicorn.

Because the application object is named `mykira`, the application command should use:

```bash
uvicorn main:mykira --host 0.0.0.0 --port $PORT
```

---

# Continuous Deployment

The backend project can use GitHub Actions for automated deployment.

The general deployment process is:

```text
Developer Pushes Code
       ↓
GitHub Repository
       ↓
GitHub Actions Workflow
       ↓
Install Dependencies
       ↓
Run Deployment Process
       ↓
Deploy to Hosting Platform
```

Production deployment credentials should be stored as GitHub secrets or hosting environment variables.

---

# Production Security Checklist

Before deploying the backend to production, verify:

- [ ] Debug mode is disabled.
- [ ] Production environment variables are configured.
- [ ] Real secrets are not stored in Git.
- [ ] CORS is restricted to trusted frontend domains.
- [ ] HTTPS is enabled.
- [ ] PostgreSQL production security is configured.
- [ ] Redis production service is configured.
- [ ] JWT keys are securely stored.
- [ ] Default administrator credentials are secure.
- [ ] Database backup strategy exists.
- [ ] Error logs do not expose sensitive information.
- [ ] Gemini API credentials are protected.

---

# Scaling Considerations

As the MyKira platform grows, the backend may require additional infrastructure.

## WebSocket Scaling

The current WebSocket connection management can require shared infrastructure for multiple backend instances.

A future distributed architecture may use:

```text
Multiple Backend Servers
          ↓
      Redis Pub/Sub
          ↓
Shared Real-Time Messaging
```

## AI Scaling

Google Gemini API usage may be affected by API quotas.

Future improvements may include:

- Response caching
- Usage monitoring
- Retry policies
- Request queues

## Database Scaling

As data grows, the project may require:

- Database indexing
- Query optimization
- Connection pooling
- Database backups
- Read replicas

---

# Troubleshooting

## Backend Does Not Start

Check that the virtual environment is activated and dependencies are installed.

```bash
pip install -r requirements.txt
```

---

## Database Connection Error

Check PostgreSQL and the database URL.

```env
DATABASE_URL=postgresql://username:password@localhost:5432/mykira_db
```

---

## Redis Connection Error

Ensure Redis is running or configure the project's supported local development fallback.

---

## Gemini API Error

Check:

```env
GEMINI_API_KEY=
GEMINI_MODEL_NAME=
```

Also verify that the configured model is available to the API account.

---

## Port Already in Use

Run the application on another port:

```bash
uvicorn main:mykira --reload --port 8001
```

---