# ADD Iteration 1 – Establishing Overall System Structure

## Step 1: Review Inputs

### Purpose

The purpose of this design is to design a starter architecture that shows the system structure, core functionality, and prioritized quality attributes.

---

### Primary Functional Requirements

#### Use Cases

| Use Case | Description |
|---------|-------------|
| **UC-1: Student queries chatbot for information** | The user inputs a request via text or voice input. The chatbot deciphers what language the user is using and what they are asking. The system then generates a response in the same language based on stored knowledge and live data. |
| **UC-2: Chatbot manages notifications and alerts** | Chatbot tracks upcoming events (i.e. assignment/project deadlines, tests, etc.) and alerts the user accordingly based on stored information and user preferences. |
| **UC-3: Upload, modify, or delete academic content** | The Lecturer, through natural language, is able to publish, update, or remove content (lecture slides, assignments, quizzes). The Chatbot processes the command and syncs with the database. |
| **UC-4: View personalized analytical statistics** | The student or lecturer requests dashboard statistics, which the system provides based on their role. Students get student-based information such as performance indicators. Lecturers get lecturer-appropriate information such as class engagement. |
| **UC-5: Integrate and configure external systems with the chatbot** | System administrators can manage external system integration, as well as establish global policies related to data retention. |
| **UC-6: Monitor and maintain system health** | System maintainer updates the chatbot system using a pipeline system to ensure zero downtime within the system. The system maintainer can then view monitoring dashboards to ensure the system is healthy with the new software. |

---

### Prioritized Quality Attributes

| ID   | Scenario | Associated Use Case |
|------|----------|---------------------|
| **QA-1: Performance** | At peak load, the Chatbot can handle 5,000 concurrent users submitting queries. The Chatbot provides a response to 95% of these users within 2 seconds. | UC-1 |
| **QA-2: Security** | A user authenticated as a Lecturer requests to access the attendance analytics. The system verifies the user’s permission level against the database; access is granted if authorized in under 50 ms. | UC-4 |
| **QA-3: Modifiability** | System administrator adopts a new LMS. Chatbot follows new policies and integrates new system changes while retaining core functionality. | UC-5 |
| **QA-4: Reliability** | The chatbot notifies the student of upcoming events/deadlines with an accuracy rate of at least 99%, with functionality to resend notifications in case of network errors. | UC-2 |
| **QA-5: Availability** | Should the system experience failure and the chatbot become unavailable, the system triggers the automatic fail-over and restores service to all users in under 60 seconds. | UC-6 |

---

### Constraints

| ID     | Constraint |
|--------|-----------|
| **CON-1** | The system must scale to accommodate at least 5,000 concurrent users. |
| **CON-2** | The system must be interoperable with APIs (e.g. REST or GraphQL). |
| **CON-3** | The system must be accessible from mobile, web, and voice-assistant-based platforms. |
| **CON-4** | The system must be cloud-compatible and scalable (e.g. Kubernetes, AWS). |
| **CON-5** | The system shall comply with authentication and institutional privacy policies (SSO). |

---

### Concerns

| ID     | Concern |
|--------|---------|
| **CRN-1: Integration** | The system must integrate the Chatbot into various aspects of the existing systems (e.g. registration, calendar, email). This requires architecture that manages the various APIs of those services and their data synchronisation. |
| **CRN-2: Low-Level Access Control** | The system will handle sensitive data for the institution (e.g. Student, Lecturer). The authentication architecture must have access controls applied not just by user roles (student, lecturer) but also include the user’s direct relationship to the material. For example, a Lecturer can access attendance analytics for courses they teach but not for unaffiliated courses. |
| **CRN-3: Rate Limitations** | The Chatbot is expected to scale to upwards of 5000 concurrent users. A fair usage policy is tied to the user’s authentication to prevent slowdown on servers and maintain response times. |
| **CRN-4: Data Aggregation** | The Chatbot needs to ingest data from multiple sources. Cached vs non-cached data has different access times. The design must strike a balance between cached vs non-cached data to reach performance and latency targets. |

---

## Step 2: Establish Iteration Goal by Selecting Drivers

### Iteration Goal

The goal for this iteration is to design the initial system architecture at a high level, which future iterations build from.

---

### Primary Use Cases

| Use Case | Description |
|---------|-------------|
| **UC-1: Student queries chatbot for information** | The user inputs a request via text or voice input. Chatbot deciphers what language the user is using and what they are asking. The system then generates a response in the same language based on stored knowledge and live data. |

---

### Prioritized Quality Attributes (Iteration 1)

| ID   | Scenario | Associated Use Case |
|------|----------|---------------------|
| **QA-1: Performance** | At peak load, the Chatbot can handle 5,000 concurrent users submitting queries. The Chatbot provides a response to 95% of these users within 2 seconds. | UC-1 |

---

### Prioritized Constraints (Iteration 1)

| ID     | Constraint |
|--------|-----------|
| **CON-1** | The system must scale to accommodate at least 5,000 concurrent users. |
| **CON-2** | The system must be interoperable with APIs (e.g. REST or GraphQL). |
| **CON-4** | The system must be cloud-compatible and scalable (e.g. Kubernetes, AWS). |

---

### Prioritized Concerns (Iteration 1)

| ID       | Concern |
|----------|---------|
| **CRN-1** | Integration: The system must integrate the Chatbot into various aspects of the existing systems (e.g. registration, calendar, email). This requires architecture that manages the various APIs of those services and their data synchronisation. |

---

## Step 3: Selecting Element to Decompose

The element we are decomposing in this iteration is the **whole system**.

We are decomposing the whole system because of the iteration’s goal. The drivers chosen for this iteration are very conceptual and high-level drivers that affect the design of the entire system. These drivers are scalability, cloud-compatibility, and API integration. Therefore, decomposing the whole system is essential in order to define key components of the architecture that will dictate how components interact with each other.

---

## Step 4: Choosing Design Concepts Satisfying the Inputs Considered in the Iteration

### Reference Architecture: Service-Oriented / Microservices Architecture

For our reference architecture, we identified a few suitable reference architectures from the reference architecture catalog document. Ultimately, we chose the **microservices architecture** as it suits our needs the best.

From our architectural drivers, we recognized that we need:

- A scalable application capable of handling upwards of **five thousand users** with a **sub-two-second response time** (QA-1).
- A system that is **cloud-compatible** and can **integrate with various API endpoints** exposed by other parts of the university system (e.g. registration, calendar, email).

A service-oriented design simplifies integration with external systems and facilitates this desired functionality (CON-4, CRN-1). Microservices allow system functionalities to be implemented and scaled independently as needed. This means that during peak times, **only the services actually under pressure need to be scaled out** (CON-1, QA-1).

---

### Deployment Pattern: Distributed Four-Tier, Load-Balanced Cluster

**Client/channel tier (interact with backend using HTTPS)**

- Web + mobile clients  
- Voice-assistant connectors  

**Edge/gateway tier**

- Terminate TLS connections  
- Handle SSO authentication tokens  
- Route requests to backend  

**Microservices tier**

- Content/knowledge integration  
- LMS integration  
- Authentication with institutional SSO  
- Conversation orchestration and NLU integration  
- Analytics and reporting  

**Data tier**

- Operational DBs for core data  
- Caching layer  
- Analytics data stores (needed for metrics)  

Load balancers distribute requests across several stateless service instances, and the microservices tier scales horizontally based on incoming traffic (QA-1, CON-1). The tiered separation structure lends itself to a cloud system design consisting of load balancers, container clusters, caching services, and databases (CON-4). The calendar and email systems as well as the LMS are external nodes connected to integration services in the microservices layer, controlling external traffic (CRN-1).

---

### Architectural / Design Patterns

- **API Gateway pattern – simplified client integration**
  - Single entry point for external clients (web, mobile, voice)
  - Routes to backend services

- **Domain-oriented service partitioning – independent domain evolution**
  - Conversation service  
  - Content/knowledge service  
  - Integration service  

---

### Performance & Scalability Tactics (QA-1, CON-1)

- **Caching frequently accessed data**
  - Course schedules, assignment deadlines, etc. cached in an in-memory store close to microservices tier  
  - Chatbot responds to queries from cache, reducing latency and load

- **Asynchronous task processing**
  - Noncritical operations are offloaded to background workers using message queues  
  - Ensures request and response processing is lightweight and fast (for UC-1)

- **Connection pooling and resource management**
  - Microservices use pooled connections to DBs and external systems  
  - Ensures predictable response times and prevents excess resource usage at high loads/concurrencies

- **Load-balancing and auto-scaling**
  - Traffic distributed across multiple API Gateway and backend microservices  
  - Metrics determine scaling – add instances during peak load times, scale down at idle times

- **Stateless service instances**
  - Main request-handling services are stateless with respect to user session data  
  - Allows horizontal scaling without session affinity problems

---

### Interoperability & Integration Design Concepts (CON-2, CRN-1)

- **Standardized internal APIs**
  - Microservices communicate with RESTful HTTP APIs and JSON payloads  
  - Internal APIs are developed and documented to handle differences in external LMS APIs during integration

- **Adapter/facade for external LMS and institutional systems**
  - Integration service(s) function as adapters/facades to external systems  
  - Handle external system discrepancies and provide the AIDAP with a stable, unified interface

---

### Framework

- **Spring framework**  
  Helps connect microservices components and unify the application.

---

## Step 5: Instantiate Architectural Elements, Allocate Responsibilities and Define Interfaces

### Architectural Elements Table

| Element                  | Responsibility | Associated Driver |
|--------------------------|----------------|-------------------|
| **Client Application**   | Responsible for handling user interaction on web and mobile as well as capturing voice data from the user. | UC-1, CON-2 |
| **API Gateway**          | Responsible for being the entry to the system; it routes requests to the appropriate service and acts as an alert to the Service Registry to manage load. | QA-1, CON-1, CON-2 |
| **Chatbot Service**      | Responsible for processing a user’s language (intent) and generating messages after retrieving necessary data from sources (cache, database). | UC-1 |
| **Chatbot Database**     | Responsible for storing user conversation history and the chatbot-specific database (separate from the university). | UC-1 |
| **Cached Storage**       | Responsible for reducing response latency. A temporary storage for frequently asked questions and data. | QA-1 |
| **University Integration (Adapter)** | Responsible for interacting with the University’s existing systems (email, registration, calendar) and providing a translation layer our APIs can rely on. | CRN-1, CON-4 |
| **University Database**  | Responsible for hosting data required for university operations (grades, schedules). It serves as the live data needed for the chatbot. | UC-1, CRN-1 |
| **Service Registry**     | Maintains a list of all active services to manage load balance; adds/removes instances depending on usage. | CON-1, CON-4 |

---

## Step 6: Sketch Views and Record Design Decisions

### Deployment View

| Element                                  | Description |
|------------------------------------------|-------------|
| **pc Student/Lecturer Workstation**      | Student/lecturer end-user machine hosting client-side application and connecting to AIDAP server. |
| **Client Application (Web/Mobile Client)** | Front-end web/mobile app running in browser/mobile OS capturing and sending queries and displaying chatbot responses. |
| **AIDAP Application Server (replicated)** | Handles chatbot requests coming from client devices, coordinates access to data stores and university systems. |
| **Chatbot Application (Server-Side)**    | Backend logic running on AIDAP app server, processing requests, NLU handling, querying chatbot DB and university systems, returning responses. |
| **Chatbot DB Server (replicated)**       | DB server for chatbot platform (chatbot-specific data), replicable for scalability. |
| **Chatbot Database**                     | Logical DB on Chatbot DB server, holds conversation history, user preferences, accessed by chatbot app. |
| **University Systems Server**            | University IT environment, exposes institutional data and services the chatbot needs. |
| **University Database and Services**     | University academic data sources and related services, accessed by Chatbot Application using external API. |

---

### Architectural Diagram

| Element                   | Description |
|---------------------------|-------------|
| **User**                  | Student/lecturer interacting with the system through the client application. |
| **Client Application**    | Front-end application capturing user input, sending chatbot requests to API gateway, rendering responses. |
| **API Gateway**           | Entry point to backend services, routes chat requests to Chatbot Service instances. |
| **Service Registry**      | Tracks available instances of backend services, used for service discovery and registration. |
| **Chatbot Service**       | Processes chat requests – interprets user queries, performs cache lookups and DB access, generates responses returned to client. |
| **University Integration (Adapter)** | Communicates with existing university systems, translating requests from Chatbot Service into API calls to university systems. |
| **Cached Storage**        | In-memory store used by Chatbot to cache frequently accessed data. |
| **Chatbot Database**      | Chatbot-specific persistent data store. |
| **University Database**   | Institutional systems holding academic data accessible by University Integration Adapter. |

---

### Logical View Diagram

| Element               | Description |
|-----------------------|-------------|
| **Presentation Layer** | Groups modules related to user interaction – talks to App/Integration layer using HTTP. |
| **App/Integration Layer** | Groups modules implementing application logic and integration with other services. |
| **Data Layer**        | Groups modules related to data access – exposes simple operations to App/Integration layer. |
| **Client Application** | Front-end module rendering chatbot UI, capturing user input, sending HTTP requests to API Gateway. |
| **API Gateway**       | Entry-point module for client HTTP traffic. |
| **Service Registry**  | Backend service instances registry. |
| **Chatbot Service**   | Core application logic. |
| **University Integration Adapter** | Abstracts access to external university systems. |
| **Cached Storage**    | Cache used by Chatbot Service for cache lookup operations (frequently requested data). |
| **Chatbot DB**        | Data-access module for chatbot’s DB. |
| **University DB**     | Data-access module for university DBs. |

---

### Key Design Decisions

| Design ID | Design Decision                          | Rationale | Impacted Drivers |
|-----------|-------------------------------------------|-----------|------------------|
| **D1**    | 4-Tier Microservices-Style Architecture   | Allows scaling only layers under load and independent deployment. | QA-1, CON-1, CON-4 |
| **D2**    | API Gateway                               | Provides a stable external API and centralized cross-cutting concerns, simplifying client integration. | QA-1, CON-2 |
| **D3**    | Service Registry                          | Makes it easier to add/remove Chatbot Service instances. | QA-1, CON-1, CON-4 |
| **D4**    | Separate Chatbot Database                 | Reduces coupling to university systems and allows independent evolution of the chatbot. | CRN-1 |
| **D5**    | Cached Storage Layer                      | Reduces average response time and reduces load. | QA-1, CON-1 |
| **D6**    | University Integration (Adapter)          | Encapsulates integration logic and protocol differences. | CRN-1, CON-2 |
| **D7**    | Stateless Services                        | Allows horizontal scaling. | QA-1, CON-1 |

---

## Step 7: Analyze Current Design and Review Iteration Goal and Design Objectives

The main focus for iteration one was building a high-level architecture for the system based on the prioritized drivers. This focus was accomplished by choosing a microservices architecture with a four-tier deployment package that includes an API gateway pattern. We were also able to fully address all prioritized drivers while partially addressing some other less important drivers.

---

### Driver Progress Tables

**Legend:**

- 🟥 – Not Addressed  
- 🟨 – Partially Addressed  
- 🟩 – Addressed  

---

#### Use Cases

| ID   | Status | Decisions / Rationale | Planned For |
|------|--------|-----------------------|-------------|
| **UC-1** | 🟩 | Defined in architecture: Client Tier → Gateway Tier → Chatbot Service → then back to user. | – |
| **UC-2** | 🟥 | Notification system not implemented yet. | IT 2 |
| **UC-3** | 🟥 | File uploading has not been implemented yet. | IT 2 |
| **UC-4** | 🟥 | Analytics service has not been implemented yet. | IT 3 |
| **UC-5** | 🟨 | The integration service adapts external services such as the university LMS APIs; data retention not yet implemented. | IT 3 |
| **UC-6** | 🟨 | Service registry does have health checks to manage load on the system but a dedicated dashboard is not yet implemented. | IT 3 |

---

#### Quality Scenarios

| ID   | Status | Rationale / Next Steps | Planned For |
|------|--------|------------------------|-------------|
| **QA-1: Performance** | 🟩 | We have horizontal scaling with microservices, load balancing (service registry), and caching (cached storage), as well as separate storages (cache, chatbot DB, university DB). | – |
| **QA-2: Security** | 🟨 | Gateway tier does handle authentication but does not have distinct role-level permissions. | IT 2 |
| **QA-3: Modifiability** | 🟩 | The university integration service is an adapter isolating the system from LMS changes. | – |
| **QA-4: Reliability** | 🟥 | Tied to UC-2; notifications have not yet been implemented. | IT 2 |
| **QA-5: Availability** | 🟩 | The service registry and stateless property of microservices allow for the system to recover in case of a service or node crash. | – |

---

#### System Constraints

| ID     | Status | Rationale / Next Steps | Planned For |
|--------|--------|------------------------|-------------|
| **CON-1** | 🟩 | Microservice architecture allows only the chatbot service to scale as needed under heavy load. | – |
| **CON-2** | 🟩 | API Gateway and the standard usage of REST and JSON in interfaces ensure interoperability. | – |
| **CON-3** | 🟩 | Both the Client Application and API Gateway are designed to handle mobile/web and/or voice inputs. | – |
| **CON-4** | 🟩 | The system is isolated into stateless units that can be added as needed on demand, and is built using web standards. | – |
| **CON-5** | 🟨 | Architecture has authentication but privacy policies are not implemented yet. | IT 3 |

---

#### Architectural Concerns

| ID        | Status | Rationale / Next Steps | Planned For |
|-----------|--------|------------------------|-------------|
| **CRN-1: Integration** | 🟩 | Integration service to adapt the University’s existing systems is in place. | – |
| **CRN-2: Low-Level Access Control** | 🟥 | We have authentication but user-specific data access is not implemented yet. | IT 2 |
| **CRN-3: Rate Limitations** | 🟩 | The chatbot can scale to 5000 users and rate limits are tied to the user’s auth token. | – |
| **CRN-4: Data Aggregation** | 🟨 | We have segregated databases, but the balance of cache vs non-cached data is not implemented yet. | IT 3 |
