# MyKira Backend Documentation

## Overview

The **MyKira Backend** is the server-side application for the **MyKira Pregnancy Health Companion** platform. It provides the APIs, business logic, database access, authentication, artificial intelligence integration, community communication, and administrative functionality required by the MyKira mobile and web applications.

The backend is built with **FastAPI**, a modern Python web framework for building APIs. It uses a layered architecture to keep the application organized, scalable, and maintainable.

### Main Responsibilities

The backend is responsible for:

* User registration and account management
* Secure authentication using JWT tokens
* Password hashing and account security
* Email verification and password recovery
* Multi-Factor Authentication (MFA)
* Google and Microsoft OAuth authentication
* Pregnancy risk evaluation and classification
* AI-powered pregnancy health assistance using Google Gemini
* AI conversation history management
* Community discussion rooms
* Real-time communication using WebSockets
* Pregnancy wellness challenges
* User challenge enrollment and progress tracking
* Administrative dashboards and statistics
* PostgreSQL database management
* Redis-based rate limiting and token management

---

# Technology Stack

| Layer                       | Technology                                       | Purpose                                             |
| --------------------------- | ------------------------------------------------ | --------------------------------------------------- |
| **Framework**               | FastAPI (Python 3.12+)                           | High-performance async web framework                |
| **ORM**                     | SQLAlchemy 2.0+                                  | Database abstraction and query building             |
| **Database**                | PostgreSQL                                       | Primary relational data store                       |
| **Cache / Session**         | Redis / FakeRedis                                | Token blacklisting, rate limiting, sessions         |
| **Authentication**          | JWT (RS256), OAuth 2.0, TOTP                     | Multi-layered authentication                        |
| **Password Hashing**        | Argon2id (passlib)                               | Secure password storage                             |
| **AI Engine**               | Google Gemini API (`gemini-3-flash-preview-001`) | Pregnancy health assistant                          |
| **HTTP Client**             | httpx (async)                                    | OAuth token exchange                                |
| **Real-Time Communication** | FastAPI WebSockets                               | Community chat rooms                                |
| **Email**                   | SMTP (Gmail)                                     | Verification and password reset emails              |
| **Deployment**              | Heroku                                           | Cloud platform with environment variable management |

---

# Hosted API

> **Note:** Replace `your-app.herokuapp.com` with the actual production domain when the backend is deployed.

| Environment        | URL                                           |
| ------------------ | --------------------------------------------- |
| **Production API** | `https://your-app.herokuapp.com`              |
| **Swagger UI**     | `https://your-app.herokuapp.com/docs`         |
| **OpenAPI JSON**   | `https://your-app.herokuapp.com/openapi.json` |

## Local Development URLs

```text
API:        http://localhost:8000
Swagger UI: http://localhost:8000/docs
ReDoc:      http://localhost:8000/redoc
OpenAPI:    http://localhost:8000/openapi.json
```

---

# Prerequisites and Setup

## Required Tools

| Tool       | Recommended Version            | Purpose                     |
| ---------- | ------------------------------ | --------------------------- |
| Python     | 3.11+                          | Run the backend             |
| PostgreSQL | Latest supported version       | Database                    |
| Redis      | Optional for local development | Cache and rate limiting     |
| Git        | Latest                         | Version control             |
| pip        | Latest                         | Python package installation |
| VS Code    | Latest                         | Development environment     |

## Installation

### 1. Open the Backend Project

Extract or clone the backend project and open it using **Visual Studio Code**.

### 2. Open a Terminal

Navigate to the backend project folder:

```bash
cd path/to/backend
```

### 3. Create a Virtual Environment

#### Linux/macOS

```bash
python -m venv venv
source venv/bin/activate
```

#### Windows PowerShell

```powershell
python -m venv venv
venv\Scripts\Activate.ps1
```

#### Windows Command Prompt

```cmd
python -m venv venv
venv\Scripts\activate
```

### 4. Install Dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

## Running the Backend

The FastAPI application object is named `mykira`.

Run the development server using:

```bash
uvicorn main:mykira --reload --host 0.0.0.0 --port 8000
```

The `--reload` option automatically restarts the server whenever source code changes during development.

---

# Architecture

The MyKira backend uses a layered architecture. This separates HTTP handling, business logic, database access, and data models.

## Standard Request Flow

```text
Client Application
       ↓
FastAPI Router
       ↓
Authentication and Authorization
       ↓
Rate Limiting
       ↓
Pydantic Schema Validation
       ↓
Service Layer
       ↓
Repository Layer
       ↓
SQLAlchemy Models
       ↓
PostgreSQL Database
       ↓
API Response
```

## AI-Enabled Request Flow

```text
Client Application
       ↓
API Router
       ↓
Service Layer
       ↓
Google Gemini API + Knowledge Retrieval
       ↓
Generated Response
       ↓
AI Response Repository
       ↓
PostgreSQL Database
       ↓
API Response
```

---

# Project Folder Structure

```text
backend/
├── main.py                     # FastAPI app factory, CORS, router mounting
├── database.py                 # SQLAlchemy engine, SessionLocal, Base
├── config.py                   # Pydantic Settings and environment variables
├── auth_dependency.py          # JWT auth, password hashing, MFA, role guards
├── gemini.py                   # Google Gemini AI integration
├── oauth.py                    # OAuth 2.0 configuration
├── private.pem                 # RS256 JWT private key
├── public.pem                  # RS256 JWT public key
├── requirements.txt            # Python dependencies
├── .env                        # Local environment variables
│
├── mykira/
│   ├── core/
│   │   ├── oauth.py            # OAuth provider URLs and configuration
│   │   └── redis_client.py     # Redis with FakeRedis fallback
│   │
│   ├── dependencies/
│   │   └── rate_limit.py       # API rate-limiting dependency
│   │
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── risk_evaluation.py
│   │   ├── community_post.py
│   │   ├── ai_response.py
│   │   ├── challenges.py
│   │   ├── user_challenges.py
│   │   └── enum.py
│   │
│   ├── repositories/
│   │   ├── user.py
│   │   ├── risk_evaluation.py
│   │   ├── community_post.py
│   │   ├── ai_response.py
│   │   ├── challenges.py
│   │   └── user_challenges.py
│   │
│   ├── services/
│   │   ├── user.py
│   │   ├── risk_evaluation.py
│   │   ├── community_post.py
│   │   ├── ai_response.py
│   │   ├── challenges.py
│   │   ├── user_challenges.py
│   │   ├── stat.py
│   │   ├── email.py
│   │   ├── security.py
│   │   └── pregnancy.py
│   │
│   ├── routers/
│   │   ├── auth.py
│   │   ├── user.py
│   │   ├── risk_evaluation.py
│   │   ├── community_post.py
│   │   ├── ai_response.py
│   │   ├── challenges.py
│   │   ├── user_challenges.py
│   │   └── stat.py
│   │
│   └── schemas/
│       ├── user.py
│       ├── risk_evaluation.py
│       ├── community_post.py
│       ├── ai_response.py
│       ├── challenges.py
│       ├── user_challenges.py
│       └── stat.py
│
└── markdown_knowledge/         # Gemini knowledge base
    └── gemini_registry.json    # Gemini file upload registry
```

---

# Main Application Files

| File                 | Responsibility                                                                                                                                                                                       |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `main.py`            | Creates the FastAPI application, configures CORS middleware, registers routers, applies rate limiting, creates database tables during startup, and creates the default administrator when necessary. |
| `database.py`        | Manages the PostgreSQL connection, SQLAlchemy engine, database sessions, declarative base, and the `get_db()` dependency.                                                                            |
| `config.py`          | Centralizes application settings and environment variables. Sensitive configuration should not be hard-coded.                                                                                        |
| `auth_dependency.py` | Handles JWT creation and validation, password hashing, current-user extraction, role validation, resource ownership checks, MFA, and token revocation.                                               |
| `gemini.py`          | Manages Google Gemini integration, knowledge files, file synchronization, AI response generation, safety instructions, stale references, and fallback responses.                                     |

---

# Database Layer

All models inherit from `database.Base`, which is the SQLAlchemy declarative base. The database uses PostgreSQL UUID primary keys with `uuid.uuid4` as the default generator.

## Entity Relationships

```text
User
 ├── Risk Evaluations
 ├── AI Responses
 ├── Community Posts
 └── User Challenges
          │
          ↓
      Challenges

Risk Evaluation
      │
      └── AI Responses

Community Post
      │
      └── Community Post Replies
```

| Parent Model     | Relationship          | Child Model      | Explanation                                                       |
| ---------------- | --------------------- | ---------------- | ----------------------------------------------------------------- |
| `User`           | One-to-Many           | `RiskEvaluation` | A mother can have multiple pregnancy risk assessments over time.  |
| `User`           | One-to-Many           | `AIResponse`     | A user can have multiple conversations with the AI assistant.     |
| `User`           | One-to-Many           | `CommunityPost`  | A user can create multiple community posts or replies.            |
| `User`           | One-to-Many           | `UserChallenge`  | A user can participate in multiple wellness challenges.           |
| `RiskEvaluation` | Optional Context      | `AIResponse`     | AI responses may optionally be associated with a risk evaluation. |
| `Challenge`      | One-to-Many           | `UserChallenge`  | A challenge can have multiple participating users.                |
| `Challenge`      | Optional Relationship | `CommunityPost`  | A challenge may optionally be linked to a community post.         |
| `User`           | One-to-Many           | `Challenge`      | An administrator can review multiple submitted challenges.        |
| `CommunityPost`  | Self-Referencing      | `CommunityPost`  | A post can have replies through `parent_post_id`.                 |

> **Note:** Foreign keys preserve referential integrity. Where configured with `CASCADE`, related records can be removed automatically when their parent record is deleted.

---

## Enumerations

**File:** `mykira/models/enum.py`

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

---

## 1. User Model (`users`)

| Column                       | Type             | Constraints         | Description                             |
| ---------------------------- | ---------------- | ------------------- | --------------------------------------- |
| `user_id`                    | `UUID`           | PK, default=`uuid4` | Unique identifier                       |
| `first_name`                 | `String(50)`     | NOT NULL            | First name                              |
| `last_name`                  | `String(50)`     | NOT NULL            | Last name                               |
| `email`                      | `String(255)`    | NOT NULL, UNIQUE    | Email address                           |
| `username`                   | `String(50)`     | NOT NULL, UNIQUE    | Auto-generated username                 |
| `password_hash`              | `String(255)`    | NULLABLE            | Argon2id hash; nullable for OAuth users |
| `lmp_date`                   | `Date`           | NULLABLE            | Last Menstrual Period date              |
| `due_date`                   | `Date`           | NULLABLE            | Estimated Due Date                      |
| `last_appointment`           | `Date`           | NULLABLE            | Last clinic appointment                 |
| `role`                       | `Enum(RoleEnum)` | NOT NULL            | `mother` or `admin`                     |
| `joined_at`                  | `DateTime(TZ)`   | Server default      | Registration timestamp                  |
| `is_verified`                | `Boolean`        | Default=`False`     | Email verification status               |
| `is_active`                  | `Boolean`        | Default=`True`      | Account activation status               |
| `verification_token_hash`    | `String(64)`     | NULLABLE            | SHA-256 verification token              |
| `verification_token_expires` | `DateTime(TZ)`   | NULLABLE            | Verification expiry                     |
| `reset_token_hash`           | `String(64)`     | NULLABLE            | SHA-256 password reset token            |
| `reset_token_expires`        | `DateTime(TZ)`   | NULLABLE            | Password reset expiry                   |
| `mfa_secret`                 | `String(255)`    | NULLABLE            | TOTP secret key                         |
| `mfa_enabled`                | `Boolean`        | Default=`False`     | MFA status                              |
| `age`                        | `Integer`        | NULLABLE            | User age                                |
| `previous_pregnancies`       | `Integer`        | Default=`0`         | Number of prior pregnancies             |
| `oauth_provider`             | `String(20)`     | NULLABLE            | OAuth provider                          |
| `oauth_id`                   | `String(255)`    | NULLABLE            | Provider user ID                        |

### Constraints and Relationships

* Email addresses must be unique.
* Usernames must be unique.
* A composite unique constraint on `(oauth_provider, oauth_id)` prevents duplicate OAuth accounts.
* A user can have multiple risk evaluations, AI responses, community posts, and challenge enrollments.
* Administrators can review submitted challenges.

---

## 2. RiskEvaluation Model (`risk_evaluations`)

| Column                 | Type                  | Constraints           | Description              |
| ---------------------- | --------------------- | --------------------- | ------------------------ |
| `risk_id`              | `UUID`                | PK, default=`uuid4`   | Unique identifier        |
| `user_id`              | `UUID`                | FK → `users`, CASCADE | Associated mother        |
| `lmp_date`             | `Date`                | NOT NULL              | Last Menstrual Period    |
| `pregnancy_week`       | `Integer`             | NOT NULL              | Current gestational week |
| `symptoms`             | `JSON`                | NOT NULL              | Reported symptoms        |
| `total_risk_score`     | `Integer`             | Default=`0`           | Aggregated risk score    |
| `risk_level`           | `Enum(RiskLevelEnum)` | Default=`LOW`         | Risk classification      |
| `existing_conditions`  | `JSON`                | NOT NULL              | Medical conditions       |
| `previous_pregnancies` | `Integer`             | NULLABLE              | Previous pregnancy count |
| `past_complications`   | `JSON`                | NOT NULL              | Historical complications |
| `is_flagged`           | `Boolean`             | Default=`False`       | High-priority flag       |
| `risk_assessment_date` | `DateTime`            | Default=`utcnow`      | Assessment timestamp     |

### Risk Scoring

The risk scoring service considers:

* **Danger signs:** Immediate high-risk classification
* **Medium-risk symptoms:** 20 points each
* **Low-risk symptoms:** 5 points each
* **High-risk medical conditions:** 20 points each
* **Moderate-risk conditions:** 10 points each
* **Pregnancy history and complications**

### Risk Classification

```text
LOW     → Score less than 30
MEDIUM  → Score from 30 to 99
HIGH    → Score of 100 or greater
```

Danger signs can bypass normal scoring and immediately classify a case as high risk.

---

## 3. CommunityPost Model (`community_posts`)

| Column           | Type           | Constraints                   | Description               |
| ---------------- | -------------- | ----------------------------- | ------------------------- |
| `post_id`        | `UUID`         | PK, default=`uuid4`           | Unique identifier         |
| `content_posted` | `Text`         | NOT NULL                      | Post content              |
| `created_at`     | `DateTime(TZ)` | Server default                | Creation timestamp        |
| `challenges_id`  | `UUID`         | FK → `challenges`, NULLABLE   | Optional linked challenge |
| `user_id`        | `UUID`         | FK → `users`, NOT NULL        | Author                    |
| `parent_post_id` | `UUID`         | Self-referencing FK, NULLABLE | Parent post for replies   |
| `room_id`        | `String`       | NOT NULL, INDEX               | Community room identifier |

### Room ID Format

```text
{trimester}_{risk_level}
```

Examples:

```text
tri1_low
tri2_medium
tri3_high
global_dashboard
```

---

## 4. AIResponse Model (`ai_responses`)

| Column           | Type           | Constraints                       | Description               |
| ---------------- | -------------- | --------------------------------- | ------------------------- |
| `ai_response_id` | `UUID`         | PK, default=`uuid4`               | Unique identifier         |
| `user_id`        | `UUID`         | FK → `users`, CASCADE             | Requesting user           |
| `risk_id`        | `UUID`         | FK → `risk_evaluations`, NULLABLE | Related risk assessment   |
| `user_message`   | `Text`         | NULLABLE                          | User's question           |
| `ai_response`    | `Text`         | NOT NULL                          | Gemini-generated response |
| `response_time`  | `DateTime(TZ)` | Server default                    | Response timestamp        |

---

## 5. Challenge Model (`challenges`)

| Column             | Type                                | Constraints            | Description           |
| ------------------ | ----------------------------------- | ---------------------- | --------------------- |
| `challenges_id`    | `UUID`                              | PK, default=`uuid4`    | Unique identifier     |
| `category`         | `Enum(ChallengeCategoryEnum)`       | NOT NULL               | Challenge category    |
| `trimester`        | `Enum(TrimesterEnum)`               | NOT NULL               | Pregnancy trimester   |
| `risk_level`       | `String(6)`                         | NOT NULL               | Target risk level     |
| `title`            | `String(120)`                       | NOT NULL               | Challenge title       |
| `challenge_text`   | `Text`                              | NOT NULL               | Challenge description |
| `challenge_type`   | `String(100)`                       | NOT NULL               | Challenge type        |
| `target_value`     | `Numeric(50,2)`                     | NOT NULL               | Target metric         |
| `unit`             | `String(100)`                       | NOT NULL               | Measurement unit      |
| `start_date`       | `DateTime`                          | NOT NULL               | Challenge start date  |
| `end_date`         | `DateTime`                          | NOT NULL               | Challenge end date    |
| `approval_status`  | `Enum(ChallengeApprovalStatusEnum)` | Default=`PENDING`      | Approval status       |
| `reviewed_by`      | `UUID`                              | FK → `users`, NULLABLE | Admin reviewer        |
| `reviewed_at`      | `DateTime`                          | NULLABLE               | Review timestamp      |
| `rejection_reason` | `Text`                              | NULLABLE               | Reason for rejection  |

---

## 6. UserChallenge Model (`user_challenges`)

| Column              | Type                            | Constraints                | Description            |
| ------------------- | ------------------------------- | -------------------------- | ---------------------- |
| `user_challenge_id` | `UUID`                          | PK, default=`uuid4`        | Unique identifier      |
| `user_id`           | `UUID`                          | FK → `users`, CASCADE      | Participating user     |
| `challenge_id`      | `UUID`                          | FK → `challenges`, CASCADE | Associated challenge   |
| `status`            | `Enum(UserChallengeStatusEnum)` | Default=`NOT_STARTED`      | Participation status   |
| `accepted_at`       | `DateTime(TZ)`                  | Default=`utcnow`           | Acceptance timestamp   |
| `completed_at`      | `DateTime(TZ)`                  | NULLABLE                   | Completion timestamp   |
| `progress_log`      | `JSON`                          | NULLABLE                   | Progress tracking data |

---

# Repository Layer

The repository pattern abstracts database operations from the business logic layer. Each repository is responsible for accessing and managing a specific database model.

## RiskEvaluationRepository

**File:** `mykira/repositories/risk_evaluation.py`

| Method                  | Description                            |
| ----------------------- | -------------------------------------- |
| `create()`              | Insert a new risk evaluation           |
| `get_by_id()`           | Fetch an evaluation by ID              |
| `get_by_user()`         | List a user's evaluations              |
| `get_all()`             | List all evaluations                   |
| `list_all_with_users()` | Join evaluations with user information |
| `get_stats()`           | Return risk statistics                 |
| `get_flagged()`         | Return flagged evaluations             |
| `update()`              | Partially update an evaluation         |
| `delete()`              | Delete an evaluation                   |

### Example Query

```python
stmt = (
    select(
        RiskEvaluation.risk_id,
        RiskEvaluation.user_id,
        User.first_name,
        User.last_name,
        RiskEvaluation.lmp_date,
        RiskEvaluation.pregnancy_week,
        RiskEvaluation.symptoms,
        RiskEvaluation.total_risk_score,
        RiskEvaluation.risk_level,
        RiskEvaluation.existing_conditions,
        RiskEvaluation.previous_pregnancies,
        RiskEvaluation.past_complications,
        RiskEvaluation.is_flagged,
        RiskEvaluation.risk_assessment_date,
    )
    .join(User, RiskEvaluation.user_id == User.user_id)
    .order_by(RiskEvaluation.risk_assessment_date.desc())
)
```

## UserRepository

**File:** `mykira/repositories/user.py`

Key responsibilities include:

* Creating users
* Finding users by ID, username, or email
* Finding OAuth-linked accounts
* Listing users
* Activating and deactivating accounts
* Deleting users
* Managing verification tokens
* Managing password reset tokens
* Updating passwords
* Enabling MFA

## CommunityPostRepository

**File:** `mykira/repositories/community_post.py`

Key responsibilities include:

* Fetching posts
* Filtering posts by room
* Creating and updating posts
* Deleting posts
* Fetching user information
* Fetching a user's latest risk evaluation
* Cleaning up old community messages

## ChallengeRepository and UserChallengeRepository

**Files:**

```text
mykira/repositories/challenges.py
mykira/repositories/user_challenges.py
```

These repositories manage:

* Challenge creation and retrieval
* Pending challenge review
* Challenge approval workflows
* User enrollment
* Progress updates
* Enrollment deletion

## AIResponseRepository

**File:** `mykira/repositories/ai_response.py`

Responsibilities:

* Save AI conversation records
* Retrieve conversation history for a user

---

# Service Layer

Services contain the main business rules of the application. They coordinate repositories, validation, calculations, and external services.

## User Service

**File:** `mykira/services/user.py`

The User Service handles:

* User registration
* Password authentication
* Password reset requests
* Password updates
* MFA activation
* OAuth account registration
* User profile updates
* Next appointment calculation
* Default administrator creation
* Unique username generation
* Due date calculation
* Pregnancy week calculation

### Clinic Visit Interval Logic

```python
VISIT_INTERVAL_WEEKS = {
    "low": 4,
    "medium": 2,
    "high": 1,
}

FALLBACK_INTERVAL_WEEKS = 4
```

## Risk Evaluation Service

**File:** `mykira/services/risk_evaluation.py`

The Risk Evaluation Service handles:

* Creating evaluation payloads
* Updating evaluation payloads
* Calculating pregnancy week
* Detecting danger signs
* Scoring symptoms
* Scoring medical conditions
* Scoring pregnancy history
* Calculating total risk score
* Classifying risk level

### Risk Classification Logic

```text
LOW     → Score < 30
MEDIUM  → Score < 100
HIGH    → Score >= 100
```

## Community Post Service

**File:** `mykira/services/community_post.py`

The Community Post Service handles:

* Listing community posts
* Room access verification
* Post creation
* Post updates and deletion
* Room assignment
* User room lookup
* Community chat cleanup
* Challenge-related posts

### Room Assignment

The user's room is determined by:

* Current pregnancy trimester
* Current pregnancy risk level

Example:

```text
Second trimester + medium risk
            ↓
       tri2_medium
```

## Other Services

| Service             | File                          | Main Responsibilities                               |
| ------------------- | ----------------------------- | --------------------------------------------------- |
| **AI Response**     | `services/ai_response.py`     | Gemini chat orchestration and conversation history  |
| **Challenges**      | `services/challenges.py`      | Challenge creation, review, approval, and rejection |
| **User Challenges** | `services/user_challenges.py` | Enrollment and progress tracking                    |
| **Statistics**      | `services/stat.py`            | Administrative dashboard analytics                  |
| **Email**           | `services/email.py`           | Verification and password reset emails              |
| **Security**        | `services/security.py`        | Login rate limiting and API quotas                  |
| **Pregnancy**       | `services/pregnancy.py`       | Pregnancy week and estimated delivery calculations  |

---

# API Layer

All routers are registered in `main.py`.

The authentication router uses a dedicated login rate limit, while the other routes use the general API quota.

## Authentication Router

**Base Path:** `/auth`

**File:** `mykira/routers/auth.py`

| Method  | Endpoint              | Authentication | Description                      |
| ------- | --------------------- | -------------- | -------------------------------- |
| `POST`  | `/register`           | Public         | Create a new account             |
| `POST`  | `/login`              | Public         | Authenticate and receive tokens  |
| `POST`  | `/refresh`            | Refresh Token  | Rotate access and refresh tokens |
| `POST`  | `/logout`             | Bearer         | Blacklist tokens                 |
| `POST`  | `/forgot-password`    | Public         | Request password reset           |
| `POST`  | `/reset-password`     | Public         | Reset password                   |
| `GET`   | `/reset-password`     | Public         | Password reset form              |
| `GET`   | `/verify-email`       | Public         | Verify email address             |
| `POST`  | `/mfa/enable`         | Bearer         | Enable MFA                       |
| `GET`   | `/me`                 | Bearer         | Get current user                 |
| `PATCH` | `/me`                 | Bearer         | Update current profile           |
| `GET`   | `/google`             | Public         | Start Google OAuth               |
| `GET`   | `/google/callback`    | Public         | Google OAuth callback            |
| `GET`   | `/microsoft`          | Public         | Start Microsoft OAuth            |
| `GET`   | `/microsoft/callback` | Public         | Microsoft OAuth callback         |

## User Router

**Base Path:** `/users`

| Method   | Endpoint                      | Authentication | Description                |
| -------- | ----------------------------- | -------------- | -------------------------- |
| `GET`    | `/me`                         | Bearer         | Current user profile       |
| `GET`    | `/{user_id}`                  | Self/Admin     | Get user by ID             |
| `GET`    | `/`                           | Admin          | List all users             |
| `PATCH`  | `/{user_id}`                  | Self/Admin     | Update user                |
| `DELETE` | `/{user_id}`                  | Admin          | Delete user                |
| `GET`    | `/{user_id}/next-appointment` | Self/Admin     | Calculate next appointment |

## Risk Evaluation Router

**Base Path:** `/risk-evaluations`

| Method   | Endpoint          | Authentication | Description             |
| -------- | ----------------- | -------------- | ----------------------- |
| `POST`   | `/`               | Self/Admin     | Create evaluation       |
| `GET`    | `/`               | Admin          | List all evaluations    |
| `GET`    | `/stats`          | Admin          | Risk statistics         |
| `GET`    | `/flagged/`       | Admin          | Flagged high-risk cases |
| `GET`    | `/user/{user_id}` | Self/Admin     | User evaluation history |
| `GET`    | `/{risk_id}`      | Self/Admin     | Get evaluation          |
| `PATCH`  | `/{risk_id}`      | Admin          | Update evaluation       |
| `DELETE` | `/{risk_id}`      | Admin          | Delete evaluation       |

## Community Post Router

**Base Path:** `/community_posts`

| Method   | Endpoint                      | Authentication  | Description         |
| -------- | ----------------------------- | --------------- | ------------------- |
| `GET`    | `/`                           | Bearer          | List posts          |
| `GET`    | `/room/{room_id}`             | Bearer          | List room posts     |
| `GET`    | `/{post_id}`                  | Bearer          | Get a post          |
| `POST`   | `/`                           | Bearer          | Create a post       |
| `PUT`    | `/{post_id}`                  | Bearer          | Update own post     |
| `DELETE` | `/{post_id}`                  | Bearer          | Delete own post     |
| `GET`    | `/my-assigned-room/{user_id}` | Public          | Get room assignment |
| `WS`     | `/ws/{room_id}`               | Authorized User | Real-time chat      |

## AI Responses, Challenges, and Statistics

| Router          | Method   | Endpoint                   | Authentication | Description              |
| --------------- | -------- | -------------------------- | -------------- | ------------------------ |
| AI Responses    | `POST`   | `/{user_id}`               | Self/Admin     | Send AI message          |
| AI Responses    | `GET`    | `/user/{user_id}`          | Self/Admin     | Get conversation history |
| Challenges      | `GET`    | `/pending`                 | Bearer         | List pending challenges  |
| Challenges      | `GET`    | `/{challenges_id}`         | Bearer         | Get challenge            |
| Challenges      | `POST`   | `/`                        | Bearer         | Create challenge         |
| Challenges      | `PATCH`  | `/{challenges_id}/approve` | Admin          | Approve challenge        |
| Challenges      | `PATCH`  | `/{challenges_id}/reject`  | Admin          | Reject challenge         |
| Challenges      | `PATCH`  | `/{challenges_id}`         | Bearer         | Update challenge         |
| Challenges      | `DELETE` | `/{challenges_id}`         | Bearer         | Delete challenge         |
| User Challenges | `POST`   | `/`                        | Bearer         | Enroll in challenge      |
| User Challenges | `GET`    | `/`                        | Bearer         | List enrollments         |
| User Challenges | `GET`    | `/{user_challenge_id}`     | Bearer         | Get enrollment           |
| User Challenges | `GET`    | `/user/{user_id}`          | Bearer         | User enrollments         |
| User Challenges | `PATCH`  | `/{user_challenge_id}`     | Bearer         | Update progress          |
| User Challenges | `DELETE` | `/{user_challenge_id}`     | Bearer         | Unenroll                 |
| Statistics      | `GET`    | `/dashboard`               | Admin          | Dashboard statistics     |
| Statistics      | `GET`    | `/risk`                    | Admin          | Risk statistics          |
| Statistics      | `GET`    | `/community`               | Admin          | Community statistics     |
| Statistics      | `GET`    | `/ai`                      | Admin          | AI usage statistics      |

---

# Authentication and Security

## JWT Implementation

The backend uses **RS256**, an asymmetric JWT signing algorithm.

### Key Files

```text
private.pem  → Used to sign tokens
public.pem   → Used to verify tokens
```

| Token Type    | Expiry                       | Purpose           |
| ------------- | ---------------------------- | ----------------- |
| Access Token  | Role-based and configurable  | API authorization |
| Refresh Token | Configurable, default 7 days | Token rotation    |

### Access Token Payload

```json
{
  "sub": "<user_id>",
  "role": "mother|admin",
  "exp": "<timestamp>",
  "iat": "<timestamp>",
  "type": "access",
  "jti": "<unique_token_id>"
}
```

### Refresh Token Payload

```json
{
  "sub": "<user_id>",
  "exp": "<timestamp>",
  "iat": "<timestamp>",
  "type": "refresh",
  "jti": "<unique_token_id>"
}
```

## Redis Token State

```text
refresh:{jti}   → valid
blacklist:{jti} → revoked
```

Redis keys use expiration times based on the remaining lifetime of their associated tokens.

---

## Password Security and MFA

### Password Hashing

* Passwords are hashed using **Argon2id**.
* Passwords are never stored as plain text.
* Legacy password hashes can be migrated after a successful login.
* Password reset tokens are stored as SHA-256 hashes.

### Password Reset

1. User requests a password reset.
2. The system generates a secure token.
3. A hash of the token is stored in the database.
4. The token expires after one hour.
5. The user submits a new password.
6. The password is securely hashed and saved.

### MFA Setup

1. Generate a random TOTP secret.
2. Generate a QR code containing the provisioning URI.
3. The user scans the QR code using an authenticator application.
4. The user enters a verification code.
5. The backend verifies the code.
6. MFA is enabled for the user.

The implementation uses `pyotp` with a verification window that supports minor clock differences.

---

## OAuth 2.0

The backend supports authentication through:

* Google
* Microsoft

### Google

Scopes:

```text
openid
email
profile
```

### Microsoft

Scopes:

```text
openid
email
profile
User.Read
```

OAuth accounts can have a `NULL` password hash because authentication is handled by the external provider.

---

## Role-Based Access Control

The application uses a role hierarchy:

```python
role_hierarchy = {
    "admin": 2,
    "mother": 1
}
```

### Authorization Dependencies

* `get_current_user` validates JWT tokens and retrieves the active user.
* `require_role()` checks the user's role.
* `require_admin` restricts access to administrators.
* `require_self_or_admin()` verifies ownership or administrator privileges.

---

# Rate Limiting

## Login Rate Limiting

| Setting                 | Value      |
| ----------------------- | ---------- |
| Maximum failed attempts | 5          |
| Time window             | 60 seconds |
| Block duration          | 15 minutes |

## API Rate Limiting

| Setting           | Value                                     |
| ----------------- | ----------------------------------------- |
| Maximum API calls | 100                                       |
| Time window       | 1 hour                                    |
| Scope             | General API routes outside authentication |

Redis is used to track these limits.

---

# AI Integration

**File:** `gemini.py`

The MyKira backend integrates Google Gemini to provide AI-powered pregnancy health assistance.

## Knowledge Base Architecture

```text
markdown_knowledge/
├── pregnancy_information.md
├── maternal_health_guidelines.md
├── ...
└── gemini_registry.json
```

The knowledge base uses Markdown files. The registry tracks uploaded Gemini file references.

The synchronization process:

1. Reads local knowledge files.
2. Checks the registry.
3. Verifies that existing Gemini file references are still valid.
4. Re-uploads missing or stale files.
5. Removes registry entries for deleted local files.

## AI Chat Flow

```text
Client Request
      ↓
Initialize Gemini Client
      ↓
Load Knowledge Files
      ↓
Build AI Request
      ↓
Apply Safety Instructions
      ↓
Generate Gemini Response
      ↓
Handle Errors and Retries
      ↓
Save Conversation
      ↓
Return Response
```

## Medical Safety Instructions

The AI assistant is designed to:

* Never provide a specific medication dosage.
* Never provide a definitive diagnosis.
* Describe possible concerns rather than claiming certainty.
* Encourage users with serious symptoms to contact a qualified healthcare professional.
* Include appropriate medical safety reminders.
* Use the available knowledge base for grounded responses.

### Fallback Response

If the AI service is unavailable, the backend can return:

> I'm having trouble connecting right now — please try again in a moment. If this is urgent, please contact your clinic directly.

---

# Configuration and Environment Variables

The application uses environment variables for configuration.

## Required Environment Variables

| Variable                      | Purpose                         | Example                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `DATABASE_URL`                | PostgreSQL connection           | `postgresql://user:pass@host:5432/db`  |
| `REDIS_URL`                   | Production Redis connection     | `redis://user:pass@host:6379`          |
| `REDIS_HOST`                  | Redis host                      | `localhost`                            |
| `REDIS_PORT`                  | Redis port                      | `6379`                                 |
| `GEMINI_API_KEY`              | Google AI API key               | `your-api-key`                         |
| `GEMINI_MODEL_NAME`           | Gemini model                    | `gemini-3-flash-preview-001`           |
| `GOOGLE_CLIENT_ID`            | Google OAuth ID                 | OAuth client ID                        |
| `GOOGLE_CLIENT_SECRET`        | Google OAuth secret             | OAuth secret                           |
| `GOOGLE_REDIRECT_URI`         | Google callback URL             | `https://host/auth/google/callback`    |
| `MICROSOFT_CLIENT_ID`         | Microsoft OAuth ID              | OAuth client ID                        |
| `MICROSOFT_CLIENT_SECRET`     | Microsoft OAuth secret          | OAuth secret                           |
| `MICROSOFT_REDIRECT_URI`      | Microsoft callback URL          | `https://host/auth/microsoft/callback` |
| `JWT_PRIVATE_KEY_PATH`        | JWT private key location        | `./private.pem`                        |
| `JWT_PUBLIC_KEY_PATH`         | JWT public key location         | `./public.pem`                         |
| `ALGORITHM`                   | JWT algorithm                   | `RS256`                                |
| `MOTHER_TOKEN_EXPIRE_MINUTES` | Mother token lifetime           | `60`                                   |
| `ADMIN_TOKEN_EXPIRE_MINUTES`  | Admin token lifetime            | `60`                                   |
| `REFRESH_TOKEN_EXPIRE_DAYS`   | Refresh token lifetime          | `7`                                    |
| `MFA_ISSUER_NAME`             | Authenticator application label | `MyKira`                               |
| `SMTP_HOST`                   | SMTP server                     | `smtp.gmail.com`                       |
| `SMTP_PORT`                   | SMTP port                       | `587`                                  |
| `SMTP_USERNAME`               | Email account                   | Application email                      |
| `SMTP_PASSWORD`               | Email application password      | Secret                                 |
| `SMTP_FROM_NAME`              | Sender name                     | `MyKira`                               |
| `APP_BASE_URL`                | Frontend URL                    | `https://mykira.app`                   |
| `DEFAULT_ADMIN_EMAIL`         | Default administrator email     | Admin email                            |
| `DEFAULT_ADMIN_USERNAME`      | Default administrator username  | `admin`                                |
| `DEFAULT_ADMIN_PASSWORD`      | Default administrator password  | Secure secret                          |

> **Security Warning:** Never commit real passwords, API keys, OAuth secrets, or private keys to Git. Use `.env` files locally and secure environment variables in production.

---

# Application Startup

The main FastAPI application is created in `main.py`.

```python
mykira = FastAPI(title="Mykira API")
```

## CORS Middleware

```python
mykira.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

> **Production Recommendation:** Replace `allow_origins=["*"]` with the official frontend domains before production deployment.

## Router Registration

```python
mykira.include_router(auth.router)

mykira.include_router(
    user.router,
    dependencies=[Depends(rate_limit_api)]
)

mykira.include_router(
    risk_evaluation.router,
    dependencies=[Depends(rate_limit_api)]
)

mykira.include_router(
    ai_response.router,
    dependencies=[Depends(rate_limit_api)]
)

mykira.include_router(
    community_post.router,
    dependencies=[Depends(rate_limit_api)]
)

mykira.include_router(
    challenges.router,
    dependencies=[Depends(rate_limit_api)]
)

mykira.include_router(
    user_challenges.router,
    dependencies=[Depends(rate_limit_api)]
)

mykira.include_router(
    stat.router,
    dependencies=[Depends(rate_limit_api)]
)
```

---

# Deployment

The backend can be deployed using Heroku or another cloud platform that supports Python applications.

## Production Server Command

```bash
uvicorn main:mykira --host 0.0.0.0 --port $PORT
```

Because the FastAPI application object is named `mykira`, the command must reference:

```text
main:mykira
```

## Continuous Deployment

A typical deployment workflow is:

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

Deployment credentials should be stored securely as GitHub Secrets or hosting platform environment variables.

---

# Scaling Considerations

## WebSocket Scaling

When multiple backend instances are deployed, real-time messages may need shared infrastructure.

A future architecture could use:

```text
Multiple Backend Servers
          ↓
      Redis Pub/Sub
          ↓
Shared Real-Time Messaging
```

## AI Scaling

Future AI improvements may include:

* Response caching
* Usage monitoring
* Retry policies
* Request queues
* API quota monitoring

## Database Scaling

As the application grows, consider:

* Additional database indexes
* Query optimization
* Connection pooling
* Automated backups
* Read replicas where necessary

---

# Error Handling

| Layer          | Error Handling Pattern                                                        |
| -------------- | ----------------------------------------------------------------------------- |
| Repository     | Roll back database transactions on `IntegrityError`                           |
| Service        | Validate business rules and raise appropriate HTTP exceptions                 |
| Router         | Use dependency injection and return meaningful HTTP errors                    |
| Authentication | Return `401` for authentication failures and `403` for authorization failures |
| AI             | Handle failures and return a safe fallback message                            |

## Common HTTP Status Codes

| Status Code | Meaning                                       |
| ----------- | --------------------------------------------- |
| `200`       | Request successful                            |
| `201`       | Resource created successfully                 |
| `204`       | Resource deleted successfully                 |
| `400`       | Invalid request                               |
| `401`       | Authentication required or invalid            |
| `403`       | Insufficient permissions                      |
| `404`       | Resource not found                            |
| `409`       | Conflict, such as duplicate email or username |
| `422`       | Request validation failed                     |
| `429`       | Rate limit exceeded                           |
| `500`       | Internal server error                         |

---

# Data Flow Examples

## User Registration

```text
POST /auth/register
       ↓
Validate Registration Data
       ↓
Check Existing Email
       ↓
Hash Password
       ↓
Generate Username
       ↓
Calculate Pregnancy Information
       ↓
Generate Verification Token
       ↓
Generate MFA Information
       ↓
Create User
       ↓
Send Verification Email
       ↓
Return Registration Result
```

## Risk Evaluation

```text
POST /risk-evaluations/
       ↓
Validate User Access
       ↓
Calculate Pregnancy Week
       ↓
Check Danger Signs
       ↓
Score Symptoms
       ↓
Score Medical Conditions
       ↓
Score Pregnancy History
       ↓
Classify Risk Level
       ↓
Flag High-Risk Cases
       ↓
Save Evaluation
       ↓
Return Result
```

## AI Conversation

```text
POST /ai-responses/{user_id}
       ↓
Verify User Access
       ↓
Receive User Message
       ↓
Load Pregnancy Knowledge
       ↓
Send Request to Gemini
       ↓
Apply Safety Instructions
       ↓
Receive AI Response
       ↓
Save Conversation
       ↓
Return Response
```

---

# Real-Time WebSocket Communication

The backend supports real-time community communication using WebSockets.

## WebSocket Endpoint

```text
ws://host/community_posts/ws/{room_id}
```

## Communication Flow

```text
User Connects
      ↓
Room Authorization
      ↓
WebSocket Accepted
      ↓
User Sends Message
      ↓
Verify Room Access
      ↓
Save Message
      ↓
Broadcast Message
      ↓
All Room Members Receive Message
```

A typical message can contain:

```json
{
  "user_id": "user-uuid",
  "content_posted": "Hello everyone!",
  "parent_post_id": null
}
```

The server can also use keepalive messages to maintain active connections.

---

# Schemas

The `schemas` directory contains Pydantic models responsible for:

* Validating incoming API requests
* Defining expected data types
* Defining API response structures
* Preventing invalid data from reaching the business layer
* Automatically documenting API data through FastAPI

The backend contains schemas for:

```text
Users
Risk Evaluations
Community Posts
AI Responses
Challenges
User Challenges
Statistics
```

## Typical Schema Data Flow

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

# Redis Configuration

Redis is used for:

* Token revocation
* Refresh token management
* Login rate limiting
* API quotas

For local development, the project can use a FakeRedis fallback when a real Redis server is unavailable.

For production, a real Redis service is recommended.

---

# Testing and Quality Assurance

## Recommended Testing Structure

```text
tests/
├── unit/
│   ├── test_user_service.py
│   ├── test_risk_evaluation_service.py
│   ├── test_security_service.py
│   └── test_pregnancy_service.py
│
├── integration/
│   ├── test_auth_api.py
│   ├── test_users_api.py
│   ├── test_risk_api.py
│   ├── test_ai_api.py
│   └── test_community_api.py
│
└── conftest.py
```

## Testing Coverage

Testing should cover:

* API endpoints
* Authentication and authorization
* User management
* Risk scoring logic
* AI response handling
* Community and WebSocket communication
* Challenge management
* Database relationships
* Rate limiting
* Error handling

## Recommended Testing Tools

* `pytest`
* FastAPI `TestClient`
* `httpx`
* Test PostgreSQL database
* Mocked Gemini responses
* Mocked Redis services

---

# Code Standards

## Naming Conventions

| Element   | Convention         | Example              |
| --------- | ------------------ | -------------------- |
| Variables | `snake_case`       | `user_id`            |
| Functions | `snake_case`       | `get_current_user()` |
| Classes   | `PascalCase`       | `RiskEvaluation`     |
| Constants | `UPPER_SNAKE_CASE` | `API_MAX_CALLS`      |
| Files     | `snake_case`       | `risk_evaluation.py` |

## Import Organization

Python imports should generally follow this order:

1. Python standard library
2. Third-party packages
3. Local application modules

Example:

```python
import uuid
from datetime import datetime

from fastapi import APIRouter, Depends
from sqlalchemy.orm import Session

from database import get_db
from mykira.models.user import User
```

## Development Principles

When adding new functionality:

* Keep routers focused on HTTP handling.
* Put business rules in services.
* Put database queries in repositories.
* Use Pydantic schemas for validation.
* Protect sensitive endpoints.
* Check ownership of private resources.
* Never place secrets directly in source code.
* Return meaningful HTTP status codes.

---

# Production Security Checklist

Before deploying the MyKira backend to production, verify the following:

* [ ] Debug mode is disabled.
* [ ] Production environment variables are configured.
* [ ] Real secrets are not stored in Git.
* [ ] CORS is restricted to trusted frontend domains.
* [ ] HTTPS is enabled.
* [ ] PostgreSQL production security is configured.
* [ ] A production Redis service is configured.
* [ ] JWT keys are securely stored.
* [ ] Default administrator credentials are secure.
* [ ] A database backup strategy exists.
* [ ] Error logs do not expose sensitive information.
* [ ] Gemini API credentials are protected.
* [ ] OAuth redirect URLs use secure HTTPS domains.
* [ ] Rate limiting is enabled.

---

# Troubleshooting

## Backend Does Not Start

Ensure the virtual environment is activated and dependencies are installed:

```bash
pip install -r requirements.txt
```

Then run:

```bash
uvicorn main:mykira --reload --host 0.0.0.0 --port 8000
```

## Database Connection Error

Check that PostgreSQL is running and verify the database URL:

```env
DATABASE_URL=postgresql://username:password@localhost:5432/mykira_db
```

## Redis Connection Error

Ensure Redis is running, or configure the project's supported local development fallback using FakeRedis.

## Gemini API Error

Check your environment variables:

```env
GEMINI_API_KEY=your_api_key
GEMINI_MODEL_NAME=gemini-3-flash-preview-001
```

Also verify that the configured Gemini model is available to your API account.

## Port Already in Use

Run the application on another port:

```bash
uvicorn main:mykira --reload --port 8001
```

---

# Conclusion

The **MyKira Backend** provides the core infrastructure for the MyKira Pregnancy Health Companion platform. Its layered architecture separates API handling, business logic, database access, and external integrations to improve maintainability and scalability.

The backend combines secure authentication, pregnancy risk evaluation, AI-powered assistance, community communication, wellness challenges, and administrative analytics into a unified API platform.

When extending the backend, developers should maintain the existing architecture:

```text
Router
  ↓
Schema Validation
  ↓
Service
  ↓
Repository
  ↓
Model
  ↓
Database
```

Following this structure helps ensure that the MyKira backend remains organized, secure, testable, and maintainable as the platform grows.
