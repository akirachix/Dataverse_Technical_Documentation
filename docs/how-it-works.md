## Architecture Overview
MyKira uses a client-server architecture. The mobile application and administrative dashboard communicate with the MyKira backend through APIs, while the backend handles communication with the PostgreSQL database and external AI services.

[Click Here to view the system architecture diagram](https://lucid.app/lucidchart/d0e46d6c-5eac-477f-ab97-f40201cb2838/edit?invitationId=inv_df5a2610-daed-4544-af72-d30a89cdc08f&page=0_0#)

The MyKira platform consists of:

*   MyKira Mobile Application
*   MyKira Administrative Dashboard
*   MyKira Backend API
*   PostgreSQL Database
*   Google Gemini Flash 3.5 Preview
*   Gemini Files Service

The mobile application is used by pregnant mothers to check risk scores, chat with an AI assistant, and engage with the community. The administrative dashboard is used by platform managers to review and publish weekly challenges.

---

## System Components

#### Mobile Application 
The MyKira mobile application provides functionality for pregnant mothers. The application communicates with the backend through the API and includes functionality such as:

*   User authentication
*   Risk score assessment
*   AI chatbot interaction
*   Peer engagement and community features
*   Weekly challenge participation
*   Group joining and updates

#### Administrative Dashboard
The administrative dashboard is the web interface used by MyKira platform administrators. The dashboard communicates with the backend API for reviewing AI-generated weekly challenges, editing them, and publishing to the community.

#### Backend API
The MyKira backend is built with **FastAPI** and provides the API layer for the mobile application and administrative dashboard.

The backend handles:

*   API requests
*   Authentication and authorization
*   Risk scoring logic
*   AI chat routing
*   Community data management
*   Challenge management
*   Database operations
*   Integration with Google Gemini Flash 3.5 preview
*   File reference management via Gemini Files Service
*   Security and request handling

The backend communicates with PostgreSQL for persistent data storage and with Google Gemini services for AI-generated content.

#### PostgreSQL Database
MyKira uses PostgreSQL for persistent data storage. The database contains information used by the platform, including:

*   Users
*   Risk scores
*   Chat history
*   Community posts
*   Challenge records

The backend is responsible for database access. The mobile application and administrative dashboard do not access PostgreSQL directly.

#### Google Gemini Flash 3.5 Preview
MyKira integrates Google Gemini Flash 3.5 as the AI model for generating personalized pregnancy guidance and weekly challenge ideas. The AI model is invoked only via the backend (never directly from the client) for security. It receives prompts and file references from the backend and returns generated content.

#### Gemini Files Service
The Gemini Files Service provides cloud file storage for markdown reference documents used by the AI to ground its responses. These reference files are uploaded to the Gemini Files Service and referenced by file ID when making AI generation requests through the backend.

---

## Key Integration Points
*   **The Hub:** The MyKira API is the hub where every module routes through it.
*   **AI Security:** Google Gemini is invoked only via the backend (never directly from the client) for security.
*   **Content Flow:** The Admin dashboard controls challenge content that then flows to the User's Peer Engagement module.
*   **Data Grounding:** The Gemini Files Service provides grounded reference documents to improve AI response quality.

---

##  Architecture Principles

*   **Separation of Responsibilities:** The mobile application, dashboard, backend, database, and AI services each have their own responsibilities.
*   **Centralized API:** The mobile application and dashboard communicate with the backend through APIs rather than connecting directly to the database or AI services.
*   **Role-Based Access:** MyKira provides different functionality for pregnant mothers and administrators.
*   **Centralized Data Storage:** Application data is stored in PostgreSQL and accessed through the backend.
*   **Secure AI Integration:** AI services are accessed only through the backend to protect API keys and ensure controlled prompt engineering.
*   **Modular Backend:** The backend separates routers, services, repositories, schemas, and models so that different parts of the application can be maintained independently.
