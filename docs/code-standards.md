# MyKira — Engineering Standards & Conventions

## 1. Global Code Standards

### 1.1 What Technologies We Use

These are the tools and programming languages that power the MyKira platform:

| Technology | What It Is | Why We Use It | Analogy |
|------------|-----------|---------------|---------|
| **Python 3.12+** | The programming language we write in | Easy to read, great for web apps and AI, huge community | Like English - widely understood and expressive |
| **FastAPI** | A tool for building web APIs (how apps talk to servers) | Very fast, automatic documentation, easy to use | Like a well-organized restaurant kitchen - orders come in, food goes out efficiently |
| **SQLAlchemy 2.0+** | A tool for talking to databases using Python code | We can use Python objects instead of writing complex database commands | Like a translator - you speak Python, it speaks Database |
| **Pydantic v2** | A tool for checking that data is correct and complete | Catches mistakes before they cause problems | Like a form with required fields - it won't let you submit until everything is right |
| **PostgreSQL 15+** | Our database - where all user data lives | Reliable, fast, handles lots of data safely | Like a very organized filing cabinet that never loses papers |
| **Async Support** | A way to handle many things at once without waiting | One server can help thousands of users simultaneously | Like a chef who can cook multiple dishes at once instead of one at a time |

### 1.2 How the Code Is Organized

Think of our codebase like a hospital with different departments. Each department has a specific job, and they work together:

```
mykira/
├── models/              ← The "medical records" department
│                        ← Defines what data looks like (users, chats, risks)
│
├── schemas/             ← The "forms" department
│                        ← Defines what data comes in and goes out of the API
│
├── routers/             ← The "reception" department
│                        ← Handles incoming requests and routes them correctly
│
├── services/            ← The "doctors" department
│                        ← Where the real thinking and decision-making happens
│
├── repositories/        ← The "records clerks" department
│                        ← Finds, saves, and updates data in the database
│
├── dependencies/        ← The "security" department
                                            Checks IDs and permissions
```

**The Golden Rule:**
> Routers (reception) should NOT do the actual work. They should immediately hand off to Services (doctors), who then ask Repositories (clerks) to handle data. No department should skip the one below it.

**Why this matters:**
- If something breaks, we know exactly which department to fix
- Multiple people can work on different parts without breaking each other's code
- We can test each part separately
- If we need to change how the database works, we only change the repository layer

### 1.3 Naming Conventions

Names should tell you exactly what something is. Just like in a hospital, "Dr. Smith" is clearly different from "Room 302."

| What You're Naming | How to Name It | Good Example | Bad Example | Why |
|-------------------|---------------|-------------|------------|-----|
| **Classes** (blueprints for objects) | PascalCase (capitalize each word) | `AIResponse`, `RiskEvaluation` | `aiResponse`, `risk_evaluation` | Classes are important - they deserve capital letters |
| **Functions** (things that do work) | snake_case (lowercase with underscores) | `create_ai_response`, `get_user_by_id` | `CreateAIResponse`, `getuserbyid` | Functions do actions — underscores make them readable |
| **Variables** (containers for data) | snake_case | `user_id`, `reply_text` | `UserID`, `replyText` | Variables hold data - keep them simple |
| **Constants** (values that never change) | UPPER_SNAKE_CASE | `FALLBACK_MESSAGE`, `GEMINI_MODEL_NAME` | `fallbackMessage`, `gemini_model` | Constants are "loud and permanent" - all caps shows this |
| **Private things** (not for outside use) | Start with underscore | `_retry`, `_load_registry` | `retry`, `loadRegistry` | The underscore is like a "Staff Only" sign |
| **Database tables** | snake_case, plural | `ai_responses`, `risk_evaluations` | `AIResponse`, `risk` | Tables hold many things - plural makes sense |
| **Primary keys** | `{table}_id` | `ai_response_id`, `user_id` | `id`, `pk` | Clear naming prevents confusion |
| **Foreign keys** | Same as the referenced table's key | `user_id` (references `users.user_id`) | `uid`, `userID` | Consistency makes relationships obvious |
| **File names** | snake_case | `ai_response.py`, `risk_evaluation.py` | `AIResponse.py`, `risk-evaluation.py` | Matches the code inside |
| **Test files** | `test_{module}.py` | `test_ai_response.py` | `ai_response_test.py` | Easy to find all tests |

### 1.4 Type Annotations

**What are type annotations?**
They are like labels on boxes that tell you what's inside. In Python, they tell other programmers (and your code editor) what kind of data a function expects and returns.

**Why they matter:**
- Prevents bugs (the computer can catch type mismatches before running)
- Makes code easier to understand at a glance
- Enables better autocomplete in code editors
- Required by our standards - no exceptions

**Examples:**

```python
# ✅ GOOD - Clear and complete
def send_message(
    db: Session,           # "db" is a database session
    user_id: str,          # "user_id" is text (string)
    payload: AIResponseCreate  # "payload" is an AIResponseCreate object
) -> AIResponse:           # This function returns an AIResponse object
    ...

# ❌ BAD — What are these? What do they return?
def send_message(db, user_id, payload):
    ...
```

**Modern Python shortcuts we use:**

| Old Way | New Way | What It Means |
|---------|---------|--------------|
| `Optional[str]` | `str \| None` | This can be text OR empty |
| `List[str]` | `list[str]` | A list containing text items |
| `Dict[str, int]` | `dict[str, int]` | A dictionary with text keys and number values |
| `Union[str, int]` | `str \| int` | This can be text OR a number |

**Always use the new way** - it's cleaner and easier to read.

### 1.5 Import Ordering

**What are imports?**
Imports are like asking to borrow tools from different workshops. We need to organize them so it's clear where everything comes from.

**The order (enforced by our tools):**

1. **`__future__` imports** - Special Python features we want to enable
2. **Standard library** - Tools that come built into Python (like `uuid`, `datetime`, `json`)
3. **Third-party** - Tools we installed separately (like `fastapi`, `sqlalchemy`, `pydantic`)
4. **First-party / local** - Our own code (like `mykira.models...`, `database`, `gemini`)

**Example:**

```python
from __future__ import annotations  # Step 1

import uuid                         # Step 2 (standard library)
from datetime import datetime
import json
import os

from fastapi import APIRouter       # Step 3 (third-party)
from sqlalchemy import Column, Text
from pydantic import BaseModel
from google import genai

from mykira.models.ai_response import AIResponse  # Step 4 (our code)
from database import Base
from gemini import generate_chat_reply
```

**Why order matters:**
- It prevents circular imports (A imports B, B imports A — infinite loop!)
- It makes it obvious what depends on what
- Our tools (`ruff`, `isort`) can automatically fix the order

### 1.6 String Formatting

**How to write text in our code:**

| Rule | Good Example | Bad Example | Why |
|------|-------------|------------|-----|
| Use f-strings for variables | `f"Hello, {name}!"` | `"Hello, " + name + "!"` | Cleaner and faster |
| Use double quotes | `"Hello world"` | `'Hello world'` | Consistency across the project |
| Break long strings with parentheses | See below | Using backslashes (`\`) | Backslashes are easy to miss and break |

**Breaking long strings (the right way):**

```python
# ✅ GOOD - Parentheses allow clean line breaks
message = (
    "I'm having trouble connecting right now. "
    "Please try again in a moment. "
    "If this is urgent, please contact your clinic directly."
)

# ❌ BAD - Backslashes are fragile and hard to see
message = "I'm having trouble connecting right now. " \
          "Please try again in a moment. " \
          "If this is urgent, please contact your clinic directly."
```

### 1.7 Error Handling

**What is error handling?**
It's planning for when things go wrong. In a hospital, this is like having a backup generator — you hope you never need it, but you're glad it's there.

**Our rules:**

1. **Never ignore errors silently** - If something breaks, we MUST know about it
2. **Log errors properly** - Write down what went wrong with full details
3. **Use custom exceptions** - Define specific error types for our domain
4. **HTTP errors only in routers** - Don't raise web errors in business logic

**Good example:**

```python
# ✅ GOOD - We catch the error, log it, and give the user a friendly message
try:
    reply = generate_chat_reply(user_message)
except Exception as e:
    # Write to our error log with full details
    logger.error("Gemini generation failed", exc_info=e)
    # Give the user something helpful instead of crashing
    reply = FALLBACK_MESSAGE
```

**Bad examples:**

```python
# ❌ BAD - We pretend nothing happened
try:
    reply = generate_chat_reply(user_message)
except:
    pass  # The error disappears forever!

# ❌ BAD - We show technical errors to users (scary and unhelpful)
try:
    reply = generate_chat_reply(user_message)
except Exception as e:
    reply = str(e)  # User sees: "HTTPError: 503 Service Unavailable"
```

### 1.8 Environment Configuration

**What is environment configuration?**
These are settings that change depending on where the code runs:
- **Local development** on your laptop
- **Testing** environment
- **Staging** (a practice version of production)
- **Production** (the real app that users see)

**The rules:**

1. **Never hardcode secrets** - API keys, passwords, and database URLs must NEVER be written directly in the code
2. **Use environment variables** - Read settings from the computer's environment
3. **Validate settings** - If a required setting is missing, the app should refuse to start

**Good example:**

```python
# ✅ GOOD - Read from environment, validate it's there
GEMINI_API_KEY = os.getenv("GEMINI_API_KEY")
if not GEMINI_API_KEY:
    raise RuntimeError("GEMINI_API_KEY is not set. The app cannot start without it.")

# ✅ GOOD - Sensible default for non-sensitive settings
GEMINI_MODEL_NAME = os.getenv("GEMINI_MODEL_NAME", "gemini-3-flash-preview-001")
# If not set, use the default model name
```

**Bad example:**

```python
# ❌ BAD - Secret in code! Anyone who sees the code can steal it
GEMINI_API_KEY = "AIzaSyDxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# ❌ BAD - No validation. App might crash later mysteriously.
GEMINI_API_KEY = os.getenv("GEMINI_API_KEY")
```

**How to manage environment variables:**
- **Local development**: Create a `.env` file (never commit this to Git!)
- **Production**: Use Kubernetes secrets, AWS Parameter Store, or similar secure services

### 1.9 Docstrings

**What is a docstring?**
It's a special comment that explains what a function or class does. Unlike regular comments, docstrings can be read by tools to generate automatic documentation.

**Our standard: Google-style docstrings**

```python
def generate_ai_reply(user_message: str | None) -> str:
    """Generate a pregnancy health assistant reply.

    This function takes the user's question and asks Google's Gemini AI
    to provide a helpful, medically-grounded response. If the AI service
    is unavailable, a friendly fallback message is returned instead.

    Args:
        user_message: The user's raw input message. This may be None if
            the user sends an empty greeting or taps the chat button
            without typing anything.

    Returns:
        The AI-generated reply text. If the generative model is
        unavailable, returns a pre-defined fallback message directing
        the user to contact their clinic.

    Raises:
        RuntimeError: If the Gemini API key is not configured.
    """
    ...
```

**When to write docstrings:**
- **Always** for public functions and classes (things other files use)
- **Optional** for private helper functions (starting with `_`)
- **Always** for complex logic that isn't obvious

**Key sections:**
- **Args**: What inputs does this need? What type? What does each one mean?
- **Returns**: What does this give back? What type?
- **Raises**: What errors might this throw and when?

---

## 2. Testing Conventions

### 2.1 The Testing Pyramid

Imagine a pyramid. The base is wide (lots of tests) and the top is narrow (fewer tests).

```
        /\
       /  \     E2E Tests (10%)
      /    \    - Test the whole app like a real user
     /------\
    /        \   Integration Tests (20%)
   /          \  - Test how parts work together
  /------------\
 /              \ Unit Tests (70%)
/                \ - Test individual functions
------------------
```

| Layer | Percentage | What They Test | Speed | Cost |
|-------|-----------|----------------|-------|------|
| **Unit Tests** | 70% | One function at a time, with fake dependencies | Very fast (milliseconds) | Cheap |
| **Integration Tests** | 20% | Multiple parts together (e.g., router + service + database) | Medium (seconds) | Moderate |
| **E2E Tests** | 10% | The entire system, like a real user clicking around | Slow (minutes) | Expensive |

**Why this ratio?**
- Unit tests are fast and cheap - we can run thousands in seconds
- E2E tests are slow and fragile - we run fewer of them
- If a unit test fails, we know exactly which function is broken
- If an E2E test fails, we know something is wrong but need to investigate where

### 2.2 How Tests Are Organized

Tests should mirror the code they test:

```
tests/
├── unit/
│   ├── services/
│   │   └── test_ai_response.py      ← Tests for mykira/services/ai_response.py
│   ├── repositories/
│   │   └── test_ai_response.py      ← Tests for mykira/repositories/ai_response.py
│   └── utils/
│       └── test_gemini.py           ← Tests for gemini utility functions
│
├── integration/
│   └── routers/
│       └── test_ai_response.py      ← Tests for mykira/routers/ai_response.py
│
└── conftest.py                      ← Shared test setup (fixtures)
```

**Why mirror the structure?**
- Easy to find the test for any piece of code
- Easy to see if a file is missing tests
- Clear separation between testing layers

### 2.3 Naming Test Functions

Test names should be so descriptive that you know what failed without looking at the code.

**The formula:** `test_{action}_{condition}_{outcome}`

| Good Name | What It Tests |
|-----------|--------------|
| `test_send_message_empty_input_returns_greeting` | When user sends nothing, we get a greeting |
| `test_generate_ai_reply_gemini_failure_returns_fallback` | When Google is down, we show the fallback message |
| `test_get_conversation_orders_by_oldest_first` | Chat history is shown in the right order |
| `test_create_ai_response_saves_to_database` | Sending a message actually stores it |

**Bad names:**

| Bad Name | Why It's Bad |
|----------|-------------|
| `test1()` | Tells you nothing |
| `test_ai()` | Too vague - what about AI? |
| `test_message()` | Is it creating, reading, or deleting? |
| `test_stuff()` | Seriously? |

**Test classes** (when grouping related tests):
```python
class TestAIService:  # PascalCase, starts with "Test"
    def test_send_message_with_valid_input(self):
        ...

    def test_send_message_with_empty_input(self):
        ...
```

### 2.4 Fixtures (Shared Test Setup)

**What is a fixture?**
It's pre-made test data or setup that can be reused across many tests. Think of it like a prep station in a kitchen - everything is ready before you start cooking.

**Our database fixture:**

```python
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from database import Base

# The test database URL (separate from production!)
TEST_DATABASE_URL = "postgresql://test:test@localhost:5432/mykira_test"

@pytest.fixture(scope="session")
def engine():
    """Create the database engine once for all tests."""
    return create_engine(TEST_DATABASE_URL)

@pytest.fixture(scope="function")
def db(engine):
    """Provide a fresh, clean database for each individual test.

    This ensures tests don't interfere with each other. Each test gets
    a completely isolated database state.
    """
    # Connect to the database
    connection = engine.connect()
    # Start a transaction (a group of changes that can be undone)
    transaction = connection.begin()
    # Create a session (a workspace for database operations)
    session = sessionmaker(bind=connection)()
    # Create all tables fresh
    Base.metadata.create_all(bind=connection)

    # Give the session to the test
    yield session

    # Cleanup after the test (whether it passed or failed)
    session.close()           # Close the workspace
    transaction.rollback()    # Undo ALL changes (magic!)
    connection.close()        # Close the connection
```

**Why this is great:**
- Each test starts with a completely clean database
- Even if a test crashes, the rollback cleans up
- Tests run in parallel without stepping on each other
- We use a real PostgreSQL database, not SQLite, so we catch real database issues

### 2.5 Mocking (Faking External Services)

**What is mocking?**
It's creating a fake version of something so tests don't depend on it. Like using a flight simulator instead of a real plane for pilot training.

**What we mock:**

| Dependency | Why We Mock It | How We Mock It |
|-----------|---------------|---------------|
| **Gemini API** | We don't want to call Google (costs money, slow, unreliable for tests) | `respx` or `pytest-httpx` to intercept HTTP calls |
| **Database** | We use a real test database (not a mock) - but we reset it for each test | The fixture above handles this |
| **File System** | We don't want tests creating real files on the server | `tmp_path` fixture creates temporary folders |
| **Environment Variables** | Tests might need different settings than production | `monkeypatch.setenv` changes them temporarily |

**Example:**

```python
def test_load_knowledge_files_missing_folder_returns_empty(
    monkeypatch,      # Built-in pytest tool for changing things temporarily
    tmp_path          # Built-in pytest tool for temporary folders
):
    # Tell the code to look in a folder that doesn't exist
    monkeypatch.setattr(
        "gemini.KNOWLEDGE_BASE_DIRECTORY", 
        tmp_path / "nonexistent"
    )

    # Run the function
    result = load_knowledge_files()

    # Check it handled the missing folder gracefully
    assert result == []
```

### 2.6 Code Coverage Requirements

**What is code coverage?**
It's the percentage of your code that is executed during tests. If you have 100 lines and tests run 85 of them, your coverage is 85%.

**Our targets:**

| Test Type | Minimum Coverage | What This Means |
|-----------|-----------------|----------------|
| **Unit Tests** | 90% | Nearly every function should have a test |
| **Integration Tests** | 80% of endpoints | Every API route should be hit at least once |
| **Overall** | 85% | Combined, 85% of all code should be tested |

**How to check:**

```bash
# Run tests with coverage reporting
pytest --cov=mykira --cov-branch --cov-report=term-missing --cov-fail-under=85
```

**What the flags mean:**
- `--cov=mykira`: Measure coverage for our code (not libraries)
- `--cov-branch`: Also check if both "if" and "else" paths were tested
- `--cov-report=term-missing`: Show which lines were NOT tested
- `--cov-fail-under=85`: If coverage is below 85%, the test run fails

**What doesn't count toward coverage:**
- Auto-generated database migration files
- `__init__.py` files that just import things
- Configuration files

### 2.7 Test Data Factories

**What is a factory?**
It's a tool that automatically creates test objects with realistic fake data. Like a cookie cutter that makes perfect test cookies every time.

**Why use factories:**
- Don't write the same setup code over and over
- Create complex objects with one line
- Easy to override specific fields when needed
- Never use real production data in tests

**Example:**

```python
# factories.py
import factory
from mykira.models.ai_response import AIResponse

class AIResponseFactory(factory.Factory):
    """Creates fake AIResponse objects for testing."""

    class Meta:
        model = AIResponse  # Which class to create

    # Default fake values
    user_message = "What foods should I avoid during pregnancy?"
    ai_response = "You should avoid unpasteurized dairy products..."
    response_time = factory.Faker("date_time")  # Random realistic timestamp

# Usage in a test:
def test_something(db):
    # Create one fake chat message
    chat = AIResponseFactory()
    db.add(chat)
    db.commit()

    # Create 50 fake messages for load testing
    chats = AIResponseFactory.create_batch(50)

    # Create a message with a specific value
    chat = AIResponseFactory(user_message="Is exercise safe?")
```

---

## 3. Git Workflow

### 3.1 Branch Strategy (Simplified GitFlow)

Think of branches like parallel universes. Each universe has a different version of the code, and we can merge them together when ready.

```
main ─────────────────────────────────────────────
       \                                       /
        \    staging ─────────────────────────/
         \   /                              /
          \-/  develop ───────────────────/
           \  /    \    /    \    /
            \-      \--/      \--/
             feature/MYK-123    bugfix/MYK-145
```

| Branch | Purpose | Who Can Push Directly | Protection Level |
|--------|---------|----------------------|-----------------|
| `main` | The real app that users see | **Nobody** - only via pull request | Maximum — 2 approvals + all tests pass |
| `staging` | A practice version for testing | **Nobody** - only via pull request | High — 1 approval + tests pass |
| `develop` | Where active development merges | **Nobody** - only via pull request |  Medium - tests must pass |
| `feature/*` | New features being built | The developer working on it | 🟢 Low - deleted after merging |
| `bugfix/*` | Fixes for non-urgent bugs | The developer working on it | 🟢 Low - deleted after merging |
| `hotfix/*` | Emergency fixes for production | The developer working on it | 🟡 Medium - branched from `main` |

**The flow:**
1. Create a feature branch from `develop`
2. Write your code and tests
3. Open a Pull Request back to `develop`
4. Get reviews and pass all checks
5. Merge to `develop`
6. When ready for testing, merge `develop` to `staging`
7. When ready for release, merge `staging` to `main`

### 3.2 How to Name Branches

**The format:** `{type}/{ticket-id}-{short-description}`

| Type | When to Use It | Example |
|------|---------------|---------|
| `feature/` | Adding something new | `feature/MYK-123-conversation-context` |
| `bugfix/` | Fixing a bug that isn't breaking production | `bugfix/MYK-145-gemini-timeout` |
| `hotfix/` | Emergency fix for production | `hotfix/MYK-200-pii-leak-fix` |
| `refactor/` | Cleaning up code without changing behavior | `refactor/MYK-130-repository-cleanup` |
| `docs/` | Documentation changes only | `docs/MYK-110-api-docs-update` |
| `test/` | Adding or improving tests | `test/MYK-112-ai-response-coverage` |

**Rules:**
- Always include the ticket ID (e.g., `MYK-123`)
- Use kebab-case (hyphens, not spaces or underscores)
- Keep it short but descriptive (under 50 characters ideally)
- No punctuation except hyphens

### 3.3 Commit Messages

**What is a commit?**
It's a snapshot of your code at a specific moment, with a note explaining what changed. Like saving a version of a document with a description of what you did.

**We use Conventional Commits** - a standard format that makes history readable and enables automatic tools.

**The format:**

```
type(scope): short summary (under 50 characters)

Body: More detailed explanation of WHAT changed and WHY.
       Can be multiple lines. Wrap at 72 characters.

Footer: References to tickets, breaking changes, etc.
```

**The types:**

| Type | Use When | Example |
|------|----------|---------|
| `feat` | You added a new feature | `feat(ai): add conversation memory` |
| `fix` | You fixed a bug | `fix(auth): resolve login timeout` |
| `docs` | Only documentation changed | `docs(readme): update setup instructions` |
| `style` | Code formatting only (no logic change) | `style: format with ruff` |
| `refactor` | Code restructured, same behavior | `refactor(db): optimize query` |
| `test` | Tests added or fixed | `test(ai): add fallback message tests` |
| `chore` | Build tools, dependencies, config | `chore(deps): update sqlalchemy` |
| `perf` | Performance improvement | `perf(api): cache frequent queries` |
| `security` | Security fix | `security(auth): fix JWT validation` |

**Scopes** (the part in parentheses): `ai`, `auth`, `risk`, `db`, `deps`, `ci`, `api`

**Good examples:**

```text
feat(ai): add conversation context to Gemini prompts

Previously, each message was treated independently, making 
follow-up questions confusing. Now the last 5 turns of 
conversation are included so Gemini understands context.

Closes MYK-123

fix(ai): handle Gemini 429 rate limit with exponential backoff

When Google was busy, we immediately showed the fallback 
message. Now we retry 3 times with delays of 2s, 4s, and 8s.

Refs MYK-145
```

**Bad examples:**

```text
 "fix stuff"           - What stuff? What was broken?
 "update"            - Update what? Why?
 "WIP"                -  Work in progress? Is it done?
 "asdf"                - Not a word. Not helpful.
```

### 3.4 Pull Request (PR) Standards

A Pull Request is like asking permission to merge your changes. It's a chance for others to review your work before it becomes part of the main codebase.

**PR Requirements:**

| Field | Requirement | Why |
|-------|-------------|-----|
| **Title** | Same as commit format: `type(scope): description` | Consistency, enables automation |
| **Description** | Must include: What changed, Why it changed, How to test | Reviewers need context |
| **Linked Issues** | Must reference at least one ticket: `Closes MYK-123` | Traceability |
| **Size** | Prefer under 400 lines changed | Small PRs are easier to review thoroughly |
| **Reviewers** | 1 domain expert + 1 generalist for `main` merges | Catches different types of issues |
| **CI Checks** | All must pass: lint, type check, tests, coverage | Prevents broken code from merging |

**What makes a good PR description:**

```markdown
## What Changed
- Added conversation context to AI responses
- Modified `generate_chat_reply()` to accept previous messages
- Updated database schema to store message history

## Why
Users were frustrated when follow-up questions didn't make sense 
to the chatbot. Including context makes conversations feel natural.

## How to Test
1. Send message: "What foods should I avoid?"
2. Send follow-up: "What about spicy food?"
3. Verify the AI understands you're still talking about pregnancy diet

## Screenshots
[Include screenshots of the chat working]

Closes MYK-123
```

### 3.5 CI/CD Pipeline

**What is CI/CD?**
- **CI (Continuous Integration)**: Automatically check code every time someone pushes changes
- **CD (Continuous Deployment)**: Automatically deploy code that passes all checks

**Our pipeline runs on every Pull Request:**

```yaml
# Simplified version of what happens automatically
on: pull_request

jobs:
  lint:
    # Check code style and formatting
    - ruff check .           # Finds style issues
    - ruff format --check .  # Verifies formatting is correct

  typecheck:
    # Verify type annotations are correct
    - mypy mykira/

  test:
    # Run all tests with coverage
    - pytest --cov=mykira --cov-fail-under=85

  security:
    # Check for security issues
    - bandit -r mykira/      # Scans for common security mistakes
    - pip-audit              # Checks for vulnerable dependencies
```

**If ANY of these fail, the PR cannot be merged.** This protects the codebase from:
- Broken code
- Security vulnerabilities
- Untested features
- Messy formatting

---

## 4. Glossary

### 4.1 Technical Acronyms

| Term | Stands For | What It Means in Plain English |
|------|-----------|-------------------------------|
| **API** | Application Programming Interface | A set of rules that lets different software programs talk to each other. Like a menu in a restaurant — it tells you what you can order and how to order it. |
| **CI/CD** | Continuous Integration / Continuous Deployment | Automatically checking and deploying code. Like a factory assembly line that tests each part before adding it to the car. |
| **CRUD** | Create, Read, Update, Delete | The four basic things you can do with data. Like managing a contact list: add someone, look them up, edit their info, or remove them. |
| **DB** | Database | A structured place to store data. Like a super-organized filing cabinet that can find anything instantly. |
| **E2E** | End-to-End | Testing the entire system from start to finish. Like test-driving a car on a real road instead of just checking the engine in a garage. |
| **FHIR** | Fast Healthcare Interoperability Resources | A universal standard for sharing health data between different systems. Like a universal translator for medical records. |
| **ORM** | Object-Relational Mapping | A tool that lets you use programming objects instead of writing database commands. Like having a translator who speaks both "programmer" and "database." |
| **PII** | Personally Identifiable Information | Any data that could identify a specific person. Like names, phone numbers, addresses, or ID numbers. |
| **RAG** | Retrieval-Augmented Generation | An AI technique where the model looks up information before answering. Like an open-book exam — the AI reads the textbook first, then answers. |
| **REST** | Representational State Transfer | A common way to design web APIs using standard HTTP methods (GET, POST, etc.). Like using standard envelope sizes for mail. |
| **TTL** | Time To Live | How long something should be kept before being deleted. Like an expiration date on milk. |
| **UUID** | Universally Unique Identifier | A random string that is almost guaranteed to be unique across all computers everywhere. Like a snowflake — no two are the same. |

### 4.2 Domain Definitions (Pregnancy Health Terms)

| Term | What It Means |
|------|--------------|
| **AI Response** | One back-and-forth in the chat: the user's question AND the assistant's answer, saved together as one record. |
| **Conversation** | All the messages between one user and the chatbot, shown in order from oldest to newest. Like a text message thread. |
| **Fallback Message** | A pre-written message we show when the AI can't generate an answer. It's like a voicemail greeting — friendly and helpful even when the person isn't available. |
| **File-based RAG** | Our current approach: we give the AI entire documents to read. Like giving a student the whole textbook during an exam. |
| **Gemini Registry** | A JSON file that tracks which medical documents have been uploaded to Google and where they are. Like a library catalog. |
| **Knowledge Base** | Our collection of trusted pregnancy health documents. Like a medical library that the AI is allowed to read from. |
| **MyKira** | The digital health platform that helps pregnant women in Zambia. It includes the chatbot, risk assessments, and more. |
| **Risk Evaluation** | A health assessment that scores how risky a pregnancy might be. Stored in the `risk_evaluations` table. |
| **Risk ID** | A unique identifier that links a chat message to a specific health risk assessment. This lets the chatbot give advice relevant to that specific situation. |
| **System Instruction** | The rules we give Gemini before every conversation. Like briefing a new employee: "You are a pregnancy health assistant. Never give medication dosages. Always suggest seeing a doctor for serious concerns." |

### 4.3 Project-Specific Shortcuts

| Abbreviation | Full Form | Where to Find It |
|--------------|-----------|-----------------|
| `ai_repo` | `mykira.repositories.ai_response` | The database access code for chat messages |
| `ai_service` | `mykira.services.ai_response` | The business logic for handling chat messages |
| `KB` | Knowledge Base | The `markdown_knowledge/` folder containing medical documents |

---
