# MyKira Mobile Frontend Documentation

The **MyKira Mobile Frontend** is the Flutter-based client application of the MyKira maternal health platform. It provides a mobile user interface through which users can securely access MyKira services and interact with the backend system.

The application communicates with the **MyKira FastAPI Backend** through HTTP-based REST API requests. The frontend is responsible for presenting information to users, managing application state, handling authentication sessions, collecting user input, and displaying responses received from the backend.

The mobile application is built using **Flutter** and **Dart**, allowing the project to support multiple platforms from a shared codebase.

The main features supported by the frontend architecture include:

- User registration
- User login and logout
- Authentication session management
- Email verification status handling
- Password recovery and password reset
- Google sign-in integration
- Microsoft sign-in integration
- User profile updates
- Pregnancy-related information management
- Pregnancy risk evaluation
- Risk evaluation status tracking
- Appointment information retrieval
- Light and dark theme support
- Secure API communication
- Local authentication token management

The mobile frontend acts as the presentation and client-side state management layer, while the backend handles business logic, authentication validation, database operations, and other server-side responsibilities.

The general platform relationship is:

```text
MyKira User

     |

Flutter Mobile Application

     |

Providers / Application State

     |

Services / API Layer

     |

MyKira FastAPI Backend

     |

Business Logic and Database

     |

JSON Response

     |

Provider State Update

     |

Updated Mobile User Interface
```

---

# 2. Technology Stack

The MyKira Mobile Frontend uses the following technologies and architectural components.

| Component | Technology | Purpose |
| --- | --- | --- |
| Mobile Framework | Flutter | Builds the cross-platform mobile user interface |
| Programming Language | Dart | Application logic and development |
| State Management | Provider / ChangeNotifier | Manages authentication and theme state |
| HTTP Client | http package | Communicates with the backend REST API |
| Environment Configuration | flutter_dotenv | Loads environment variables such as the API URL |
| Typography | Google Fonts | Provides Roboto-based application typography |
| Local Storage | StorageService | Stores and retrieves authentication tokens |
| Backend API | FastAPI | Provides backend services and business logic |
| Data Format | JSON | Transfers data between frontend and backend |
| Authentication | Bearer Token / JWT | Secures authenticated API requests |
| UI Framework | Material Design 3 | Provides Flutter UI components and design system |

The architecture is designed so that individual responsibilities remain separated.

For example:

```text
Screen
  |
Provider
  |
Service
  |
API Service
  |
FastAPI Backend
```

This separation helps make the application easier to maintain, test, and expand.

---

# 3. Prerequisites

Before running the MyKira Mobile Frontend, the development environment should contain the required Flutter development tools.

| Tool | Purpose |
| --- | --- |
| Flutter SDK | Builds and runs the Flutter application |
| Dart SDK | Programming language used by Flutter |
| VS Code or Android Studio | Development environment |
| Android SDK | Required for Android development |
| Android Emulator or Physical Device | Runs and tests the application |
| Git | Version control |
| MyKira Backend | Provides API services during development |

Verify the Flutter installation by running:

```bash
flutter --version
```

Check whether the development environment is correctly configured:

```bash
flutter doctor
```

The `flutter doctor` command checks important development dependencies and reports missing requirements.

---

# 4. Setup and Installation

## Step 1: Open the Mobile Frontend Project

Open the MyKira mobile frontend project in Visual Studio Code.

The project should contain the Flutter project files, including:

```text
pubspec.yaml
lib/
android/
ios/
.env
```

The main Flutter application source code is typically located inside the `lib/` directory.

---

## Step 2: Open a Terminal

Open the integrated terminal in VS Code.

Navigate to the root directory of the Flutter project:

```bash
cd path/to/mykira
```

Make sure that the current directory contains the `pubspec.yaml` file.

---

## Step 3: Install Dependencies

Install all Flutter packages required by the project:

```bash
flutter pub get
```

This command reads the `pubspec.yaml` file and downloads the required dependencies.

The project uses packages and functionality such as:

- Flutter Material
- Provider
- HTTP
- flutter_dotenv
- Google Fonts

If dependencies are changed, run:

```bash
flutter pub get
```

again to update the local package installation.

---

## Step 4: Configure Environment Variables

The MyKira mobile application uses environment variables to configure the backend API address.

The application loads the `.env` file during startup.

Example:

```text
API_BASE_URL=http://YOUR_SERVER_ADDRESS:8000
```

The API URL is accessed through:

```dart
dotenv.env['API_BASE_URL']
```

The application checks the environment variable first before using development fallback addresses.

> **Important:** Do not use a production secret inside a publicly distributed mobile application. Environment files in frontend applications should be treated as configuration, not as a secure place for server secrets.

---

## Step 5: Verify Flutter Configuration

Run:

```bash
flutter doctor
```

Resolve any important issues before running the application.

To view connected devices or available emulators, use:

```bash
flutter devices
```

---

# 5. Running the Application

After installing dependencies and configuring the API URL, run the Flutter application with:

```bash
flutter run
```

The general startup process is:

```text
Start Flutter Application

        |

Initialize Flutter Bindings

        |

Load .env Configuration

        |

Create Providers

        |

Run MyApp

        |

Load Theme Configuration

        |

Open AuthWrapper

        |

Check Authentication Status

        |

Display Appropriate Screen
```

The application entry point ensures that Flutter is initialized before loading environment configuration.

---

# 6. Frontend Architecture Overview

The MyKira Mobile Frontend follows a layered and modular architecture.

The major layers are:

```text
+---------------------------------+
|         Presentation Layer      |
|     Screens and UI Widgets      |
+----------------+----------------+
                 |
                 |
+---------------------------------+
|       State Management Layer    |
|   Providers and ChangeNotifier  |
+----------------+----------------+
                 |
                 |
+---------------------------------+
|          Service Layer          |
| AuthService and Other Services  |
+----------------+----------------+
                 |
                 |
+---------------------------------+
|       API Communication Layer   |
|          ApiService             |
+----------------+----------------+
                 |
                 |
+---------------------------------+
|        MyKira FastAPI Backend   |
+---------------------------------+
```

## Architecture Layers

| Layer | Responsibility |
| --- | --- |
| Presentation Layer | Displays screens and collects user interaction |
| Provider Layer | Manages application state and notifies the UI |
| Model Layer | Represents application data such as users |
| Service Layer | Implements feature-specific backend communication |
| API Layer | Builds HTTP requests and handles common networking logic |
| Storage Layer | Stores authentication tokens locally |
| Backend Integration Layer | Connects the application to the MyKira API |

This separation prevents screens from needing to directly manage low-level HTTP communication.

---

# 7. Application Flow

The MyKira frontend follows a predictable data flow between the user interface, application state, services, and backend.

## Standard Data Flow

```text
User Action

    |

Flutter Screen / Widget

    |

Provider Method

    |

Service Method

    |

ApiService HTTP Request

    |

MyKira Backend

    |

JSON Response

    |

Service Processes Response

    |

Provider Updates State

    |

notifyListeners()

    |

User Interface Rebuilds
```

For example, during login:

```text
User Enters Email and Password

            |

Login Screen

            |

AuthProvider.login()

            |

AuthService.login()

            |

ApiService.post('/auth/login')

            |

MyKira FastAPI Backend

            |

Access Token Returned

            |

StorageService Saves Token

            |

AuthService.getCurrentUser()

            |

UserModel Created

            |

AuthProvider Updates Status

            |

Authenticated UI Displayed
```

---

# 8. Project Structure

Based on the application architecture and the code currently implemented, the MyKira Flutter project follows a structure similar to the following:

```text
mykira/

+-- android/                         # Android platform configuration
+-- ios/                             # iOS platform configuration
+-- web/                             # Web platform configuration when enabled
+-- assets/                          # Images, icons, and other application assets
|
+-- lib/
|   |
|   +-- main.dart                    # Application entry point
|   |
|   +-- models/                      # Application data models
|   |   +-- user_model.dart          # User data model
|   |
|   +-- providers/                   # Application state management
|   |   +-- auth_provider.dart       # Authentication state and logic
|   |   +-- theme_provider.dart      # Light/dark theme state
|   |
|   +-- services/                    # Backend and local service communication
|   |   +-- api_service.dart         # Central HTTP communication layer
|   |   +-- auth_service.dart        # Authentication and user API operations
|   |   +-- storage_service.dart     # Local token storage
|   |
|   +-- screens/                     # Application pages and screens
|   |   +-- auth_wrapper.dart        # Selects UI based on authentication state
|   |   +-- ...                      # Authentication and feature screens
|   |
|   +-- widgets/                     # Reusable Flutter UI widgets
|   |
|   +-- utils/                       # Shared helper functions
|
+-- .env                             # Environment configuration
+-- pubspec.yaml                     # Flutter dependencies and configuration
+-- pubspec.lock                     # Locked dependency versions
+-- README.md                        # General project documentation
```

The exact project may contain additional feature folders and files, but the core architecture is organized around models, providers, services, screens, and reusable widgets.

## Folder Responsibilities

### `lib/`

Contains the primary Dart source code for the Flutter application.

### `lib/models/`

Contains data models that represent application entities.

For example:

```text
UserModel
```

The `UserModel` converts backend JSON data into a structured Dart object that can be used throughout the application.

### `lib/providers/`

Contains `ChangeNotifier` classes responsible for managing application state.

The currently implemented providers include:

- `AuthProvider`
- `ThemeProvider`

### `lib/services/`

Contains classes responsible for communication and application services.

Examples include:

- `ApiService`
- `AuthService`
- `StorageService`

### `lib/screens/`

Contains the application's pages and feature screens.

The `AuthWrapper` is responsible for determining which screen should be displayed according to the user's authentication status.

### `lib/widgets/`

Contains reusable Flutter widgets that can be shared across multiple screens.

Typical examples may include:

- Custom buttons
- Form fields
- Loading indicators
- Error messages
- Cards
- Dialogs

### `.env`

Contains environment-specific configuration.

The frontend uses:

```text
API_BASE_URL
```

to configure the backend address.

### `pubspec.yaml`

Defines:

- Flutter dependencies
- Application assets
- Project metadata
- SDK requirements

---

# 9. Core Application Components

## Application Entry Point

The main application starts from `main.dart`.

The application performs the following initialization process:

1. Ensures Flutter bindings are initialized.
2. Loads environment variables from `.env`.
3. Creates the global `ThemeProvider`.
4. Starts the `MyApp` widget.
5. Loads the `AuthWrapper`.

The startup code conceptually follows:

```text
main()

  |

WidgetsFlutterBinding.ensureInitialized()

  |

dotenv.load()

  |

ChangeNotifierProvider

  |

MyApp

  |

AuthWrapper
```

The environment configuration is loaded before the application begins making API requests.

---

## Theme Management

The `ThemeProvider` manages whether the application is displayed in light mode or dark mode.

It contains:

```text
_isDarkMode
```

which stores the current theme state.

The provider exposes:

```text
isDarkMode
```

to allow the user interface to read the current theme.

The theme can be changed using:

```text
toggleTheme()
```

The flow is:

```text
User Changes Theme

        |

ThemeProvider.toggleTheme()

        |

_isDarkMode Updated

        |

notifyListeners()

        |

MyApp Rebuilds

        |

New Theme Applied
```

The application uses:

- `ThemeMode.light`
- `ThemeMode.dark`

depending on the current provider state.

---

## Authentication State Management

Authentication state is managed by `AuthProvider`.

The provider stores:

| State | Purpose |
| --- | --- |
| `_status` | Current authentication status |
| `_user` | Currently authenticated user |
| `_errorMessage` | Latest authentication error |
| `_hasRiskEvaluation` | Whether the user has completed a risk evaluation |

The provider exposes public getters so the user interface can access state without directly modifying private variables.

---

## Authentication Wrapper

The application starts with:

```text
AuthWrapper
```

The responsibility of the authentication wrapper is to display the appropriate part of the application according to the current authentication state.

The general flow is:

```text
Application Starts

        |

Authentication Status Checked

        |

+-------------------------------------+
|        Authentication Result        |
+-------------------------------------+

        |

Unauthenticated -------> Login / Authentication Screens

Email Verification -----> Verification Required Screen

Authenticated ---------> Main Application

Loading ---------------> Loading Screen
```

---

# 10. Navigation and Screen Flow

The application's screen flow is based primarily on authentication status.

The initial navigation flow is:

```text
Application Launch

        |

AuthWrapper

        |

Check Authentication

        |

+---------------+-------------------+-----------------+
|               |                   |                 |
|               |                   |                 |
Loading     Not Logged In     Email Verification   Authenticated

|               |                   |                 |
|               |                   |                 |
Loading      Login/Register      Verify Email      Main Application
Screen
```

After successful authentication, the user can access the main features implemented by the application.

The exact screen organization may contain multiple feature screens depending on the complete project implementation.

---

# 11. Authentication Flow

Authentication is one of the main application systems.

The frontend communicates with the backend through the `AuthService`, while `AuthProvider` manages the resulting application state.

## Authentication Status

The application defines the following authentication states:

| Status | Description |
| --- | --- |
| `uninitialized` | Authentication status has not yet been determined |
| `authenticated` | User is logged in and verified |
| `unauthenticated` | User is not logged in |
| `emailVerificationRequired` | User must verify their email |
| `loading` | An authentication operation is currently running |

These states allow the UI to respond correctly to the user's session.

---

## Application Startup Authentication Check

When `AuthProvider` is created, it automatically calls:

```text
checkAuthStatus()
```

The flow is:

```text
Application Starts

        |

AuthProvider Created

        |

checkAuthStatus()

        |

Read Token from StorageService

        |

Token Exists?

   +----+----+

   |         |

 No        Yes

   |         |

Unauthenticated    GET /auth/me

                    |

              User Retrieved?

                +---+---+ +

                |       |

               No      Yes

                |       |

        Unauthenticated  Check Verification

                            |

                    Check Risk Evaluation

                            |

                      Authenticated
```

If the token is missing, the application immediately sets the status to:

```text
unauthenticated
```

If a token exists, the application requests the current user from:

```text
/auth/me
```

---

## Login Flow

The login process uses:

```text
AuthProvider.login()
```

The user provides:

- Email
- Password

The complete flow is:

```text
User Enters Credentials

        |

AuthProvider.login()

        |

Status = Loading

        |

AuthService.login()

        |

POST /auth/login

        |

Backend Validates Credentials

        |

Access Token Returned

        |

StorageService.setToken()

        |

GET /auth/me

        |

Create UserModel

        |

Check Email Verification

        |

Check Risk Evaluation

        |

Status = Authenticated
```

If login fails, the provider stores the backend or network error message in:

```text
_errorMessage
```

and changes the state back to:

```text
unauthenticated
```

---

## Registration Flow

The registration process sends user information to:

```text
POST /auth/register
```

The registration request can include:

- First name
- Last name
- Email
- Password
- Confirm password
- Last menstrual period date
- Last appointment date

The flow is:

```text
User Completes Registration Form

        |

AuthProvider.register()

        |

AuthService.register()

        |

POST /auth/register

        |

Backend Creates User

        |

Registration Response

        |

User Continues Authentication Process
```

The provider does not automatically mark the user as authenticated after registration.

---

## Email Verification Flow

After login or session restoration, the application checks:

```text
_user.isVerified
```

If the user's email has not been verified, the authentication status becomes:

```text
emailVerificationRequired
```

The application can then direct the user to an appropriate email verification interface.

This ensures that the application can distinguish between:

```text
Authenticated Account
        and
Authenticated but Unverified Account
```

---

## Logout Flow

When the user logs out, `AuthProvider.logout()` performs the following actions:

```text
Logout Requested

        |

AuthService.logout()

        |

StorageService.clearToken()

        |

Clear Current User

        |

Clear Risk Evaluation State

        |

Clear Error Message

        |

Status = Unauthenticated

        |

notifyListeners()

        |

Return to Authentication UI
```

Clearing the locally stored token prevents future authenticated API requests from using the old session token.

---

# 12. API Integration and Service Layer

The frontend communicates with the backend through a centralized service architecture.

The service hierarchy is:

```text
Flutter Screen

      |

Provider

      |

Feature Service

      |

ApiService

      |

HTTP Request

      |

MyKira FastAPI Backend
```

## API Service

`ApiService` is the central HTTP communication utility.

Its responsibilities include:

- Reading the backend base URL
- Building request URLs
- Creating standard HTTP headers
- Adding authentication tokens when required
- Sending GET requests
- Sending POST requests
- Sending PATCH requests
- Sending DELETE requests
- Applying network request timeouts
- Safely decoding JSON responses

The configured base URL is read from:

```text
API_BASE_URL
```

If the environment value is unavailable, the application includes development fallback URLs.

### Development API Address Behavior

The service handles platform differences.

| Platform | Development Address |
| --- | --- |
| Flutter Web | `http://127.0.0.1:8000` |
| Android Emulator | `http://10.0.2.2:8000` |
| Other local environments | `http://127.0.0.1:8000` |

The Android emulator uses `10.0.2.2` because the emulator's `localhost` does not normally refer directly to the development computer.

---

## Standard Headers

The API service defines standard JSON headers:

```http
Accept: application/json
Content-Type: application/json
```

For authenticated requests, the application adds:

```http
Authorization: Bearer <token>
```

The token is retrieved through `StorageService`.

---

## Authentication Service

`AuthService` contains feature-specific communication with authentication and related backend endpoints.

Its responsibilities include:

- Registration
- Login
- Getting the current user
- Updating the user profile
- Checking risk evaluation history
- Password recovery
- Password reset
- Creating risk evaluations
- Getting appointment information
- Logout
- Google sign-in
- Microsoft sign-in

The service processes backend responses and returns structured results to the provider.

A typical result contains:

```text
success
message
data
statusCode
```

This makes it easier for the provider and UI to handle both successful and failed operations consistently.

---

## API Communication Flow

The general API request flow is:

```text
Provider Requests Operation

        |

Service Creates Request

        |

ApiService Builds URL

        |

Authorization Token Added if Required

        |

HTTP Request Sent

        |

15-Second Timeout Protection

        |

Backend Response Received

        |

Safe JSON Decoding

        |

Structured Result Returned

        |

Provider Updates State

        |

UI Updates
```

---

## Main API Endpoints Used

Based on the implemented `AuthService`, the frontend communicates with endpoints including:

| Endpoint | Method | Purpose |
| --- | --- | --- |
| `/auth/register` | POST | Creates a new user account |
| `/auth/login` | POST | Authenticates a user |
| `/auth/me` | GET | Retrieves the current authenticated user |
| `/auth/me` | PATCH | Updates profile information |
| `/auth/forgot-password` | POST | Starts password recovery |
| `/auth/reset-password` | POST | Resets the user's password |
| `/auth/google` | POST | Authenticates using Google |
| `/auth/microsoft` | POST | Authenticates using Microsoft |
| `/risk-evaluations/` | POST | Creates a pregnancy risk evaluation |
| `/risk-evaluations/user/{userId}` | GET | Checks user risk evaluations |
| `/users/{userId}/next-appointment` | GET | Retrieves the next appointment |

> **Note:** The exact backend behavior, request validation, and response schemas are controlled by the MyKira backend API.

---

# 13. State and Data Management

The application uses the **Provider** package together with Flutter's `ChangeNotifier`.

This provides a simple reactive state management architecture.

## Provider Architecture

The application state flow is:

```text
API Response

     |

Provider Updates Internal State

     |

notifyListeners()

     |

Listening Widgets Rebuild

     |

Updated User Interface
```

The main providers currently implemented include:

- `AuthProvider`
- `ThemeProvider`

---

## AuthProvider

`AuthProvider` manages authentication-related state.

Its major responsibilities include:

### Session Management

Checks whether a stored authentication token exists.

### User Management

Retrieves the authenticated user and stores the user as a `UserModel`.

### Authentication State

Controls whether the user is:

- Loading
- Authenticated
- Unauthenticated
- Required to verify email

### Error Management

Stores error messages returned by services.

### Risk Evaluation Status

Checks whether the current user already has a risk evaluation.

The main authentication state structure is:

```text
AuthProvider

+-- AuthStatus
+-- Current User
+-- Error Message
+-- Risk Evaluation Status
```

---

## ThemeProvider

`ThemeProvider` manages the application's theme mode.

It contains a boolean state:

```text
_isDarkMode
```

When the value changes, the provider calls:

```text
notifyListeners()
```

The `MyApp` widget listens to the provider and changes the `MaterialApp` theme mode.

---

# 14. Local Storage and Authentication Tokens

The application uses `StorageService` to manage the authentication token locally.

The service is used to:

- Save a token after successful login
- Retrieve a token during application startup
- Add the token to authenticated requests
- Clear the token during logout
- Clear expired tokens after unauthorized responses

The token lifecycle is:

```text
Successful Login

        |

Backend Returns Access Token

        |

StorageService.setToken()

        |

Future API Requests Read Token

        |

Authorization Header Added

        |

User Logs Out or Session Expires

        |

StorageService.clearToken()
```

The application does not send the authentication token manually from every screen. Instead, authenticated HTTP requests are centralized through `ApiService`.

---

# 15. Pregnancy Risk Evaluation

The frontend supports pregnancy risk evaluation through the authentication service and provider state.

A risk evaluation request can contain:

- User ID
- Last menstrual period date
- Symptoms
- Existing conditions
- Previous pregnancies
- Past complications

The request is sent to:

```text
POST /risk-evaluations/
```

The evaluation flow is:

```text
User Completes Risk Evaluation

        |

Frontend Collects Information

        |

AuthService.createRiskEvaluation()

        |

Authenticated POST Request

        |

MyKira Backend

        |

Risk Evaluation Processing

        |

Evaluation Stored

        |

Response Returned

        |

Frontend Updates Evaluation Status
```

The application can also check whether a user has completed a risk evaluation by requesting:

```text
GET /risk-evaluations/user/{userId}
```

The result is stored as:

```text
_hasRiskEvaluation
```

This allows the user interface to adapt based on whether the evaluation has already been completed.

---

# 16. User Profile and Appointment Features

## User Profile Updates

The application supports updating selected user information through:

```text
PATCH /auth/me
```

The currently implemented update request includes:

- Age
- Previous pregnancies

The flow is:

```text
User Updates Profile

        |

AuthService.updateProfile()

        |

Authenticated PATCH Request

        |

Backend Validates Data

        |

Profile Updated

        |

Response Returned to Application
```

## Next Appointment

The application can request the user's next appointment through:

```text
GET /users/{userId}/next-appointment
```

This enables the frontend to retrieve appointment-related information from the backend and display it to the user.

---

# 17. OAuth Authentication

The frontend contains support for external authentication through Google and Microsoft.

## Google Sign-In

The application sends a Google identity token to:

```text
POST /auth/google
```

The backend validates the identity token and can return an application access token.

The frontend stores the returned access token using `StorageService`.

## Microsoft Sign-In

The application sends a Microsoft identity token to:

```text
POST /auth/microsoft
```

The same general flow applies:

```text
External Provider Authentication

        |

Identity Token Obtained

        |

Token Sent to MyKira Backend

        |

Backend Validates Identity

        |

MyKira Access Token Returned

        |

Token Stored Locally

        |

Authenticated Session Established
```

The backend remains responsible for validating external authentication tokens.

---

# 18. Error Handling and User Feedback

The frontend service architecture includes error handling for common network and API failures.

## Network Errors

Each API request uses exception handling.

If the application cannot connect to the backend, the service returns a network error message indicating that the server may be unavailable.

The request also includes a timeout of:

```text
15 seconds
```

This prevents requests from waiting indefinitely.

---

## Safe JSON Decoding

The backend may occasionally return:

- JSON data
- An empty response
- Plain text

The `safeJsonDecode()` method handles these situations safely.

If the response is empty, the application creates a meaningful error object.

If JSON parsing fails, the raw response can be returned as a detail value instead of causing an uncontrolled application crash.

---

## HTTP Error Handling

The application processes important response statuses.

| Status Code | Example Handling |
| --- | --- |
| 200 | Request completed successfully |
| 201 | Resource created successfully |
| 401 | Authentication session may be expired |
| 403 | Access restricted or rate-limited depending on endpoint |
| Other errors | Return backend message or fallback message |

For example, when `/auth/me` returns `401`, the application clears the stored token and treats the session as unauthenticated.

---

## Error Message Extraction

`AuthService` includes a helper that attempts to extract useful messages from backend responses.

The service checks:

- `detail`
- `message`
- Validation error lists
- Plain text responses

This helps present backend errors in a more readable format.

---

# 19. UI and Theme Architecture

The application uses Flutter's Material Design system.

The application is configured with:

```text
Material 3
```

The visual design includes both light and dark themes.

## Light Theme

The light theme uses:

- A light background
- Pink primary color
- Roboto typography
- Material 3 components

## Dark Theme

The dark theme uses:

- Dark application backgrounds
- The same primary branding color
- White and light text for readability
- Material 3 dark components

The primary application color is:

```text
#F52DA0
```

The application uses Google Fonts to configure the text theme and includes an emoji font fallback.

The theme flow is:

```text
ThemeProvider

      |

isDarkMode

      |

MaterialApp.themeMode

      |

Light or Dark Theme

      |

Application UI
```

---

# 20. Security Considerations

The MyKira Mobile Frontend works together with the backend to protect user sessions and application data.

Important considerations include:

- Use HTTPS in production
- Never store user passwords after login
- Store authentication tokens carefully
- Clear tokens when logging out
- Clear invalid tokens after unauthorized responses
- Do not expose backend secrets in the mobile application
- Validate user input before submission
- Display safe error messages
- Always rely on backend authorization for protected resources

## Frontend and Backend Security Responsibilities

| Frontend Responsibility | Backend Responsibility |
| --- | --- |
| Collect user input | Validate and sanitize requests |
| Manage UI authentication state | Authenticate users |
| Attach bearer token | Verify tokens |
| Clear expired local sessions | Enforce authorization |
| Provide safe error feedback | Protect database access |
| Use production HTTPS URLs | Secure server communication |

> **Important:** The frontend can control what users see, but the backend must always enforce authentication, authorization, ownership, and security rules independently.

---

# 21. Build and Deployment

Before creating a production version of the application, ensure that the project has been fully tested.

The general deployment workflow is:

```text
Complete Development

        |

Test Features

        |

Configure Production API URL

        |

Test Authentication

        |

Create Production Build

        |

Test Release Build

        |

Distribute Application
```

For Android, a release build can be created using Flutter build commands configured for the project.

A typical Android build command is:

```bash
flutter build apk --release
```

For other targets, use the appropriate Flutter build command.

Before deployment, verify:

- Production API URL is configured correctly
- Debug configuration is disabled where appropriate
- Backend connectivity works
- Authentication works
- Logout clears sessions
- Expired sessions are handled
- Error states are tested
- The application is tested on target devices

---

# 22. Performance and Scaling

As MyKira grows, the mobile frontend should remain responsive and efficient.

Potential improvements include:

- Avoid unnecessary widget rebuilds
- Use efficient Provider listeners
- Paginate large backend lists
- Cache appropriate non-sensitive data
- Optimize images and assets
- Avoid repeated API requests
- Use loading states during network operations
- Dispose resources when screens are closed
- Separate large features into modular services and providers

The current service architecture already supports scaling because backend communication is centralized instead of being distributed throughout individual UI screens.

---

# 23. Troubleshooting

## Flutter Command Is Not Found

Verify that Flutter is installed and available in the system PATH.

Run:

```bash
flutter --version
```

If the command is not recognized, configure the Flutter SDK installation correctly.

Then run:

```bash
flutter doctor
```

---

## Cannot Connect to the Backend

Check:

- The MyKira backend server is running
- The `API_BASE_URL` is correct
- The device can reach the backend server
- Network connectivity is available
- The backend is listening on the expected port

For local development:

```text
Android Emulator -> http://10.0.2.2:8000
```

may be required instead of:

```text
http://127.0.0.1:8000
```

A physical device usually requires the development machine's reachable network address rather than `localhost`.

---

## Authentication Does Not Work

Check:

- The API base URL is correct
- The backend authentication service is running
- The login credentials are valid
- The backend is returning an access token
- The token is successfully saved by `StorageService`
- Authenticated requests contain the bearer token

Also test the backend authentication endpoint independently using the backend API documentation.

---

## Environment File Is Not Loaded

Ensure that the `.env` file exists in the project configuration and contains:

```text
API_BASE_URL=http://YOUR_SERVER_ADDRESS:8000
```

The application startup loads the environment using:

```dart
await dotenv.load(fileName: '.env');
```

Verify that the environment file is included and accessible according to the Flutter project configuration.

---

## API Request Fails

Check:

- Endpoint path
- HTTP method
- Request body
- Backend server availability
- Network connectivity
- Authentication token
- API response status code

The centralized `ApiService` is a useful place to inspect request configuration when debugging communication issues.