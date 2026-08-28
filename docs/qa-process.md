## QA and Testing  
Testing is used to verify that the different parts of MyKira work as expected.  
Testing covers the backend API, mobile PWA, administrative dashboard, and the integration between these components.  
**Backend Testing**  
Backend tests are organized separately from the application code.  
The main areas to test include:

* API endpoints  
* Authentication and authorization  
* User management  
* Risk scoring logic  
* AI chat routing and prompt handling  
* Community and peer engagement data  
* Challenge management  
* File reference handling (Gemini Files Service)

Backend tests should also verify that API requests return the expected responses, handle invalid input correctly, and securely manage AI service interactions (Gemini should never be called directly from the client).

**Informational Website Testing**  
[MyKira Informational Website](https://my-kira.vercel.app/)   
The informational website functionality is tested using test driven development (TDD) scripts that were made using Cypress. The main test areas are:

* Landing page  
* Features  
* How it works  
* Community  
* About   
* Contact

**Mobile Testing**  
The contains tests under unit, component and integration.

* Unit Tests: used to test individual pieces of application logic.  
* Component Tests: used to test individual UI components and user interface behaviour in the mobile application  
* Integration / E2E Tests: used to test functionality involving multiple parts of the mobile application, including API communication and module interactions.

**Dashboard Testing**  
[Dataverse Admin Dashboard](https://dataverse-dashboard.vercel.app/login)

Dashboard functionality should be tested across its main areas:

* Authentication  
* Registration and Login  
* Dashboard overview  
* Risk evaluation monitoring  
* Community monitoring  
* Challenge verification

Testing should cover administrator authentication, challenge review workflows, editing and publishing flows, and ensuring published challenges appear correctly in the Peer Engagement Module.

**API Integration Testing**  
Integration testing verifies communication between the client applications and the backend.  
Mobile PWA / Dashboard

* API request

MyKira Backend API

* Database operation

PostgreSQL

These tests help verify that requests, responses, authentication, and stored data work correctly across the connected components.

* Additionally, backend-to-AI integration should be tested:  
* AI generation request  
* Google Gemini Flash 3.5  
* File reference  
* Gemini Files Service

**QA Testing**  
The project also contains a dedicated QA area for organizing broader test cases and regression testing. The QA structure allows testing to be organized by feature and platform. This ensures the full functionality of the application end-to-end.

* API  
* Mobile  
* Dashboard  
* Integration  
* Regression


**Regression Testing**  
Regression testing is performed after changes using a regression checklist to make sure existing functionality continues to work.  