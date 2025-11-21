# ADD Iteration 2

## Step 1: Review Inputs

### Use Cases

| Use Case | Description |
|---------|-------------|
| **UC-1: Student queries chatbot for information** | The user inputs a request via text or voice input. Chatbot deciphers what language the user is using and what they are asking. The system then generates a response in the same language based on stored knowledge and live data. |
| **UC-2: Chatbot manages notifications and alerts** | Chatbot tracks upcoming events (i.e. assignment/project deadlines, tests, etc.) and alerts the user accordingly based on stored information and user preferences. |
| **UC-3: Upload, modify, or delete academic content** | The Lecturer through natural language is able to publish, update, and/or remove content (lecture slides, assignments, quizzes). The Chatbot processes the command and syncs with the database. |
| **UC-4: View personalized analytical statistics** | The student or lecturer requests dashboard statistics, which the system provides based on their role. Students get student-appropriate information such as performance indicators. Lecturers get lecturer-appropriate information such as class engagement. |
| **UC-5: Integrate and configure external systems with the chatbot** | System administrators can manage external system integration, as well as establish global policies related to data retention. |
| **UC-6: Monitor and maintain system health** | System maintainer updates the chatbot system using a pipeline system to ensure zero downtime within the system. The system maintainer can then view monitoring dashboards to ensure the system is healthy with the new software. |

---

### Quality Scenarios

| ID  | Scenario | Associated Use Case |
|-----|----------|---------------------|
| **QA-1: Performance** | At peak load, the Chatbot can handle 5,000 concurrent users submitting queries. The Chatbot provides a response to 95% of these users within 2 seconds. | UC-1 |
| **QA-2: Security** | A user authenticated as a Lecturer requests to access the attendance analytics. The system verifies the user’s permission level against the database; access is granted if authorized in under 50 ms. | UC-4 |
| **QA-3: Modifiability** | System administrator adopts a new LMS. Chatbot follows new policies and integrates new system changes while retaining core functionality. | UC-5 |
| **QA-4: Reliability** | The chatbot notifies the student of upcoming events/deadlines with an accuracy rate of at least 99%, with functionality to resend notifications in case of network errors. | UC-2 |
| **QA-5: Availability** | Should the system experience failure and the chatbot become unavailable, the system triggers the automatic fail-over and restores service to all users in under 60 seconds. | UC-6 |

---

### System Constraints

| ID     | Constraint |
|--------|-----------|
| **CON-1** | The system must scale to accommodate at least 5,000 concurrent users. |
| **CON-2** | The system must be interoperable with APIs (e.g. REST or GraphQL). |
| **CON-3** | The system must be accessible from mobile, web, and voice-assistant-based platforms. |
| **CON-4** | The system must be cloud-compatible and scalable (e.g. Kubernetes, AWS). |
| **CON-5** | The system shall comply with authentication and institutional privacy policies (SSO). |

---

### Architectural Concerns

| ID     | Concern |
|--------|---------|
| **CRN-1: Integration** | The system must integrate the Chatbot into various aspects of the existing systems (e.g. registration, calendar, email). This requires architecture that manages the various APIs of those services and their data synchronisation. |
| **CRN-2: Low-Level Access Control** | The system will handle sensitive data for the institution (e.g. Student, Lecturer). The authentication architecture must have access controls applied not just by user roles (student, lecturer) but also include the user’s direct relationship to the material. For example, a Lecturer can access attendance analytics for courses they teach but not for unaffiliated courses. |
| **CRN-3: Rate Limitations** | The Chatbot is expected to scale to upwards of 5000 concurrent users. A fair usage policy is tied to the user’s authentication to prevent slowdown on servers and maintain response times. |
| **CRN-4: Data Aggregation** | The Chatbot needs to ingest data from multiple sources. Cached vs non-cached data has different access times. The design must strike a balance between cached vs non-cached data to reach performance and latency targets. |

---

## Step 2: Establish Iteration Goal by Selecting Drivers

The goal of this iteration is to identify the support structures of the primary functionality for the system. This will be done by refining the internal structure of the microservices and API Gateway to support core functionality.

---

### Prioritized Use Cases

| Use Case | Description |
|---------|-------------|
| **UC-1: Student queries chatbot for information** | The user inputs a request via text or voice input. Chatbot deciphers what language the user is using and what they are asking. The system then generates a response in the same language based on stored knowledge and live data. |
| **UC-2: Chatbot manages notifications and alerts** | Chatbot tracks upcoming events (i.e. assignment/project deadlines, tests, etc.) and alerts the user accordingly based on stored information and user preferences. |
| **UC-3: Upload, modify, or delete academic content** | The Lecturer through natural language is able to publish, update, and/or remove content (lecture slides, assignments, quizzes). The Chatbot processes the command and syncs with the database. |

---

### Prioritized Quality Attributes

| ID  | Scenario | Associated Use Case |
|-----|----------|---------------------|
| **QA-2: Security** | A user authenticated as a Lecturer requests to access the attendance analytics. The system verifies the user’s permission level against the database; access is granted if authorized in under 50 ms. | UC-4 |
| **QA-4: Reliability** | The chatbot notifies the student of upcoming events/deadlines with an accuracy rate of at least 99%, with functionality to resend notifications in case of network errors. | UC-2 |

---

### Prioritized Constraints

| ID     | Constraint |
|--------|-----------|
| **CON-5** | The system shall comply with authentication and institutional privacy policies (SSO). |

---

### Prioritized Concerns

| ID      | Concern |
|---------|---------|
| **CRN-2: Low-Level Access Control** | The system will handle sensitive data for the institution (e.g. Student, Lecturer). The authentication architecture must have access controls applied not just by user roles (student, lecturer) but also include the user’s direct relationship to the material. For example, a Lecturer can access attendance analytics for courses they teach but not for unaffiliated courses. |

---

## Step 3: Selecting Element to Decompose

For this iteration, we will be focusing on the core function flows (UC-1 to UC-3) with a focus on security and reliability (QA-2, QA-4) under the security constraints and relevant architectural concerns (CON-5 and CRN-2).

The main goal for this iteration is to define and develop the primary components and structures that will form the system. In order to find the components that will make up these structures, we must **decompose the microservices logic** in order to properly plan the logic within it.

---

## Step 4: Choosing Design Concepts Satisfying the Inputs Considered in the Iteration

### Refine Microservice Architecture (UC-1, UC-2, UC-3, QA-4)

Extract components from the use cases to make the microservices module lower-level. Show how the components will interact with each other and the surrounding system.

- Conversation service  
- Notifications service  
- Content management service  

---

### Refine API Gateway Pattern (UC-1, UC-2, UC-3, QA-2, CON-5)

Extract components from the use cases to make the API gateway pattern module lower-level. Show how the components will interact with each other and the surrounding system.

- Routing and aggregation  
- Security and policy filters  
- Rate limiting hooks  

---

### Implement the Four-Tier Architecture (UC-1, UC-2, UC-3)

Split the extracted components into four tiers of the system. Show the connections between the system and the components within them.

- Client/channel tier  
- Edge/gateway tier  
- Microservices tier  
- Data tier  

---

### Use the Spring Framework to Build Modules (UC-1, UC-2, UC-3)

Use the Spring framework to build the modules so that they have an easier time connecting objects and methods within the different modules, allowing ease of information transfer.

- Spring Boot microservices  
- Spring Security  
- Spring Cloud / Gateway  
- Spring Data  

---

## Step 5: Instantiate Architectural Elements, Allocate Responsibilities and Define Interfaces

### Architectural Elements Table

| Element                                | Responsibility | Associated Driver |
|----------------------------------------|----------------|-------------------|
| **API Gateway – Routing & Segregation** | Single entry point for client requests. Routes use case requests to the correct backend service. Supports simple data aggregation when client views require calling many services. | UC-1, UC-2, UC-3 |
| **API Gateway – Security & Policy Filter** | Validate SSO tokens on incoming requests, extract user identity and role, perform basic authorization in line with institutional policies. | QA-2, CON-5, CRN-2 |
| **API Gateway – Rate Limiter**        | Enforce token rate limits on sensitive operations, prevent abuse of backend services, ensure stability during heavy loads. | CRN-3, QA-2 |
| **Conversation Service**              | Handle student/lecturer queries – receive natural language requests, coordinate with NLU components, cache, and data sources, then return a response. | UC-1, QA-2, CON-5, CRN-2 |
| **Notification Service**              | Implement notification-related logic, schedule notifications using user/course data, trigger sending using channel adapters. | UC-2, QA-4 |
| **Content Management Service**        | Implement upload/modify/delete of academic content, validate that users can modify content for a course, update Chatbot DB/LMS, record changes. | UC-3, QA-2, CON-5, CRN-2 |
| **Authorization/Policy Service**      | Holds centralized access control rules, evaluates if a user is authorized to perform an action on a resource; used by Conversation, Notification, and Content Management services. | QA-2, CON-5, CRN-2 |
| **University Integration Adapter (LMS services)** | Stable interface from internal services to university systems, translates internal requests to external API calls, enforces requesting/returning only authorized data following institutional rules. | UC-1, UC-3, QA-2, CON-5, CRN-2 |
| **Chatbot Data Access Layer**         | Encapsulates access to Chatbot DB, provides operations so notifications and content changes are stored reliably and are easily auditable. | UC-1, UC-2, UC-3, QA-4 |
| **Notification Queue/Scheduler**      | Sends messages asynchronously; messages are placed into a queue for delivery where messages are for course-related work (project deadlines, quizzes). | QA-4, UC-2 |
| **Chatbot Database**                  | Holds Chatbot-specific data used in use cases for conversation history, user preferences, notification status, etc.; facilitates reliable recovery and audit. | UC-1, UC-2, UC-3, QA-4 |
| **University Database**               | Responsible for hosting data required for university operations (grades, schedules); serves as the live data needed for the Chatbot, defines notifications to send and content operations allowed for a user. | CRN-2, UC-1, UC-2, UC-3 |

## Step 6: Sketch Views and Record Design Decisions

### Architectural Diagram

<img width="1118" height="735" alt="image" src="https://github.com/user-attachments/assets/54f7e4e2-e800-4e2b-9f4b-68c568344e1d" />

| Element                     | Description |
|----------------------------|-------------|
| **User**                   | Student/lecturer interacting with the system through the client app. |
| **Client Application**     | Front-end application capturing user input, sending chatbot requests to API Gateway, and rendering responses. |
| **API Gateway**            | Entry point to backend services, routes chat requests to backend services such as the Conversation Service. |
| **Service Registry**       | Tracks available instances of backend services, used for service discovery and registration. |
| **Conversation Service**   | Handles student/lecturer queries. |
| **Notification Service**   | Implements notification logic. |
| **Content Management Service** | Implements upload/modify/delete of academic content. |
| **Authorization/Policy Service** | Centralized service evaluating access control rules. |
| **University Integration (Adapter)** | Communicates with existing university systems, translating requests from backend services into API calls to university systems. |
| **Cached Storage**         | In-memory store used by the Chatbot to cache frequently accessed data. |
| **Chatbot Database**       | Chatbot-specific persistent data store. |
| **University Database**    | Institutional systems holding academic data accessible by the University Integration Adapter. |
| **Notification Queue/Scheduler** | Stores pending notifications with scheduled delivery times. |

---

### Logical View Diagram

<img width="491" height="844" alt="image" src="https://github.com/user-attachments/assets/b4b33a09-3669-4238-84e5-e04cbc6c28fe" />

| Element                     | Description |
|----------------------------|-------------|
| **Presentation Layer**     | Groups modules related to user interaction – talks to App/Integration layer using HTTP. |
| **App/Integration Layer**  | Groups modules implementing application logic and integration with other services. |
| **Data Layer**             | Groups modules related to data access – exposes simple operations to the App/Integration layer. |
| **Client Application**     | Front-end module rendering chatbot UI, capturing user input, sending HTTP requests to the API Gateway. |
| **API Gateway**            | Entry point to backend services, routes chat requests to backend services such as the Conversation Service. |
| **Service Registry**       | Tracks available instances of backend services, used for service discovery and registration. |
| **Conversation Service**   | Handles student/lecturer queries. |
| **Notification Service**   | Implements notification logic. |
| **Content Management Service** | Implements upload/modify/delete of academic content. |
| **Authorization/Policy Service** | Centralized service evaluating access control rules. |
| **University Integration (Adapter)** | Communicates with existing university systems, translating requests from backend services into API calls to university systems. |
| **Cached Storage**         | In-memory store used by the Chatbot to cache frequently accessed data. |
| **Chatbot Database**       | Chatbot-specific persistent data store. |
| **University Database**    | Institutional systems holding academic data accessible by the University Integration Adapter. |
| **Notification Queue/Scheduler** | Stores pending notifications with scheduled delivery times. |

## Step 7: Analyze Current Design and Review Iteration Goal and Design Objectives

The main focus for iteration two was building the support structures for the application, based on the prioritized drivers. This focus was accomplished by extracting components that shaped the modules from the use cases. We were also able to fully address all the prioritized drivers stated for iteration two.

### Driver Progress Tables

**Legend:**

- 🟥 – Not Addressed  
- 🟨 – Partially Addressed  
- 🟩 – Addressed  

---

### Use Cases

| ID   | Status | Decisions / Rationale | Planned For |
|------|--------|------------------------|-------------|
| **UC-1** | 🟩 | Implemented via a Conversation Service as well as a database and a data access layer. | IT 1 & IT 2 |
| **UC-2** | 🟩 | Notification system implemented via a Notification Service. | IT 2 |
| **UC-3** | 🟩 | File uploading has been implemented via a Content Management Service. | IT 2 |
| **UC-4** | 🟥 | Analytics service has not been implemented yet. Deferred to iteration 3. | IT 3 |
| **UC-5** | 🟩 | The integration service adapts external services such as the university LMS APIs. This is done via a University Integration Adapter. | IT 2 |
| **UC-6** | 🟨 | Service Registry does have health checks to manage load on the system but a dedicated dashboard is not yet implemented. Deferred to iteration 3. | IT 3 |

---

### Quality Scenarios

| ID   | Status | Rationale / Next Steps | Planned For |
|------|--------|------------------------|-------------|
| **QA-1: Performance** | 🟩 | We have horizontal scaling with microservices, load balancing (Service Registry), and caching (Cached Storage), as well as different storages (cache, Chatbot DB, University DB). | IT 1 |
| **QA-2: Security** | 🟩 | Gateway tier handles authentication and has role-based services and policies. | IT 2 |
| **QA-3: Modifiability** | 🟩 | The University Integration Service is an adapter isolating the system from LMS changes. | IT 1 |
| **QA-4: Reliability** | 🟩 | Notifications have been implemented via a Notification Service that allows for resending if necessary. | IT 2 |
| **QA-5: Availability** | 🟩 | The Service Registry and stateless property of microservices allow for the system to recover in case of a service or node crash. | IT 1 |

---

### System Constraints

| ID     | Status | Rationale / Next Steps | Planned For |
|--------|--------|------------------------|-------------|
| **CON-1** | 🟩 | Microservice architecture allows only the chatbot service to scale as needed under heavy load. | IT 1 |
| **CON-2** | 🟩 | API Gateway and the standard usage of REST and JSON in interfaces ensure interoperability. | IT 1 |
| **CON-3** | 🟩 | Both the Client Application and API Gateway are designed to handle mobile/web and/or voice inputs. | IT 1 |
| **CON-4** | 🟩 | The system is isolated into stateless units that can be added as needed on demand, and is built using web standards. | IT 1 |
| **CON-5** | 🟩 | Architecture has authentication and privacy policies implemented via an Authorization and Policy Service. | IT 2 |

---

### Architectural Concerns

| ID      | Concern | Status | Rationale / Next Steps | Planned For |
|---------|---------|--------|------------------------|-------------|
| **CRN-1: Integration** | Integration | 🟩 | Integration service to adapt the university’s existing systems is in place. | IT 1 |
| **CRN-2: Low-Level Access Control** | Low-level access control | 🟩 | We have authentication and user-specific data access implemented via a Chatbot Database. This allows the chatbot to access user preferences and data. | IT 2 |
| **CRN-3: Rate Limitations** | Rate limitations | 🟩 | The chatbot can scale to 5000 users and rate limits are tied to the user’s auth token. | IT 1 |
| **CRN-4: Data Aggregation** | Data aggregation | 🟨 | We have segregated databases but the balance of cache vs non-cached data isn’t implemented yet. Deferred to iteration 2 or 3. | IT 2 |
