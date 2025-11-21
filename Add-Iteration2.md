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
