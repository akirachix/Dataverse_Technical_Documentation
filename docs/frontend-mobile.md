# Frontend Mobile (Flutter)

## 1. Overview

The MyKira mobile application is a standalone **Flutter** application designed specifically for maternal health tracking, pregnancy journey guidance, and real-time risk evaluation. Built with Flutter, it provides cross-platform support for both Android and iOS devices with consistent performance, secure local persistence, and real-time community interaction.

**Target Users:** Expectant Mothers & Healthcare Community  
**Platforms:** Android and iOS (via Flutter cross-platform compilation)  
**Primary Use Case:** Pregnancy tracking, maternal risk assessment, community engagement, and AI-driven support  
**Distribution:** Google Play Store and Apple App Store  

The core functionalities of the app include:

* **Evaluate maternal risk factors** using structured health data inputs and receive instant classifications (Low, Medium, High).
* **Engage with peer groups and community feeds** for real-time discussions via WebSockets.
* **Track weekly trimester habit challenges** to build healthy routines throughout pregnancy.
* **Consult Kirabot**, an integrated AI assistant powered by Gemini for personalized pregnancy recommendations.
* **Persist session state and security tokens** locally for seamless re-authentication.

---

## 2. Core Architecture & Feature Modules

### 2.1 Splash & Onboarding Flow
The entry point for all users. It displays a branded splash screen for two seconds, verifies onboarding completion via local storage, and routes the user to the login screen or onboarding flow.

* **Page Logic:** `lib/screens/splash_screen.dart`, `lib/screens/teaser_screen.dart`
* **Storage Check:** `lib/services/storage_service.dart`

### 2.2 Authentication Flow
Manages user identity from initial launch through session recovery. Includes email/password login, registration, password reset, email verification, and OAuth social login.

* **Page Logic:** `lib/screens/login_screen.dart`, `lib/screens/signup_screen.dart`, `lib/screens/forgot_password.dart`, `lib/screens/reset_password_screen.dart`, `lib/screens/email_verification.dart`, `lib/screens/oauth_web_screen.dart`, `lib/screens/oauth_web_helper.dart`
* **Route Guard:** `lib/screens/auth_wrapper.dart`
* **Auth Service:** `lib/services/auth_service.dart`
* **Auth State:** `lib/providers/auth_provider.dart`

### 2.3 Home Dashboard
The primary landing screen after authentication, providing a summary of pregnancy progress, health risk status, active challenges, and quick navigation.

* **Page Logic:** `lib/screens/home_screen.dart`
* **Main Shell:** `lib/screens/main_screen.dart`
* **Data Model:** `lib/models/home_data.dart`

### 2.4 Risk Assessment Module
Allows users to submit symptoms and health metrics to receive a calculated maternal risk evaluation (Low, Medium, or High).

* **Page Logic:** `lib/screens/risk_assessment_screen.dart`, `lib/screens/risk_result_screen.dart`
* **Data Model:** `lib/models/risk_evaluation_model.dart`
* **Service Logic:** `lib/services/risk_service.dart`
* **Base Network Layer:** `lib/services/service.dart`

### 2.5 Community & Challenges Modules
Enables real-time peer interactions, discussion groups, and trimester-specific habit challenges.

* **Community Screen:** `lib/screens/community_screen.dart`, `lib/screens/groups_tab.dart`
* **Challenges Screen:** `lib/screens/challenges_tab.dart`, `lib/screens/progress_tab.dart`
* **Data Models:** `lib/models/community_post_model.dart`
* **Real-time Layer:** WebSocket integration inside `lib/services/`

### 2.6 Kirabot AI Assistant
Provides conversational guidance on nutrition, self-care, and stage-specific care options.

* **Page Logic:** `lib/screens/kirabot_screen.dart`
* **Data Model:** `lib/models/chat_message_model.dart`
* **Backend Integration:** Fast-API / Gemini backend via `lib/services/service.dart`

---

## 3. Technical Stack & Dependencies

* **Framework:** Flutter (stable channel)
* **Language:** Dart
* **State Management:** Provider (`lib/providers/`)
* **Environment Configuration:** `flutter_dotenv`
* **Local Persistence:** `SharedPreferences` / `flutter_secure_storage`

```
lib/
├── constants/
│   └── app_colors.dart         # Global brand colors & gradients
├── models/
│   ├── home_data.dart          # Dashboard state data models
│   ├── risk_evaluation.dart    # AI risk output models
│   └── user_model.dart         # Authentication & profile models
├── providers/
│   ├── auth_provider.dart      # Authentication state management
│   └── theme_provider.dart     # Dynamic light/dark theme state
├── screens/
│   ├── auth_wrapper.dart       # Dynamic auth state route handler
│   ├── challenges_tab.dart     # Trimester community tasks
│   ├── community_screen.dart   # Peer support forums
│   ├── edit_profile_screen.dart# User profile management
│   ├── email_verification_screen.dart
│   ├── forgot_password_screen.dart
│   ├── groups_tab.dart         # Micro-communities by trimester
│   ├── home_screen.dart        # Main maternal dashboard
│   ├── kirabot_screen.dart     # Real-time AI chat interface
│   ├── login_screen.dart       # User authentication
│   ├── main_screen.dart        # Bottom navigation container
│   ├── progress_tab.dart       # Pregnancy milestone tracker
│   ├── reset_password_screen.dart
│   ├── risk_assessment_screen.dart # WHO-aligned health intake
│   ├── risk_result_screen.dart # Clinical triage output display
│   ├── signup_screen.dart      # User registration
│   ├── splash_screen.dart      # App initialization & onboarding flow
│   └── teaser_screen.md
└── services/
    └── storage_service.dart    # SharedPreferences driver for persistent local state
```

