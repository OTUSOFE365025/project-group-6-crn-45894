# Final Project - Phase III

## Students

| Student           | Student Number |
| ----------------- | -------------- |
| Nabhil Irtisham   | 100914422      |
| Colton Bernas     | 100918756      |
| Ammeer Jamal Talwani | 100791669  |

---

## ADD Iteration 3

### Step 1: Review Inputs

**Purpose:** The purpose of this iteration is to build on the structures described in iterations 1 and 2 in order to fulfill targeted quality attributes scenarios.

#### Primary Functional Requirements

| Use Case | Description |
| --- | --- |
| **UC-1: Student queries chatbot for information** | The user inputs a request via text or voice input. Chatbot deciphers what language the user is using and what they are asking. The system then generates a response in the same language based on stored knowledge and live data. |
| **UC-2: Chatbot manages notifications and alerts** | Chatbot tracks upcoming events (i.e. assignment/project deadlines, tests, etc.) and alerts the user accordingly based on stored information and user preferences. |
| **UC-3: Upload, modify, or delete academic content** | The Lecturer through natural language is able to publish, update, and or remove content (lecture slides, assignments, quizzes). The Chatbot processes the command and syncs with the database. |
| **UC-4: View personalized analytical statistics** | The student or lecturer requests dashboard statistics, which the system provides based on their role:<br>- Students get student-based information such as performance indicators.<br>- Lecturers get lecturer-appropriate information such as class engagement. |
| **UC-5: Integrate and configure external systems with the chatbot** | System administrators can manage external system integration, as well as establish global policies related to data retention. |
| **UC-6: Monitor and maintain system health** | System maintainer updates the chatbot system using a pipeline system to ensure zero downtime within the system. The system maintainer can then view monitoring dashboards to ensure the system is healthy with the new software. |

#### Prioritized Quality Attributes

| ID   | Scenario | Associated Use Case |
| --- | --- | --- |
| **QA-1: Performance** | At peak load, the Chatbot can handle 5,000 concurrent users submitting queries. The Chatbot provides a response to 95% of these users within 2 seconds. | UC-1 |
| **QA-2: Security** | A user authenticated as a Lecturer requests to access the attendance analytics. The system verifies the user’s permission level against the database, access is granted if authorized in under 50 ms. | UC-4 |
| **QA-3: Modifiability** | System administrator adopts a new LMS. Chatbot follows new policies and integrates new system changes while retaining core functionality. | UC-5 |
| **QA-4: Reliability** | The chatbot notifies the student of upcoming events/deadlines with an accuracy rate of at least 99%, with functionality to resend notifications in case of network errors. | UC-2 |
| **QA-5: Availability** | Should the system experience failure and the chatbot become unavailable, the system triggers the automatic fail-over and restores service to all users in under 60 seconds. | UC-6 |
| **QA-6: Auditability** | An auditor requests a history of content changes and analytics accesses for a course over a time period. The system returns a chronological list (with who/what/when information about changes) in less than 5 seconds with 99% completeness. | UC-3, UC-4 |
| **QA-7: Usability** | A student starts a conversation with the chatbot on mobile and continues on a laptop later. After opening the chatbot, the previous conversation context and preferences are restored and ready to use in less than two seconds. | UC-1, UC-2 |
| **QA-8: Operability** | A system maintainer deploys a new version of the Conversation Service with zero downtime for end-users. The system must be able to rollback automatically to a previous version within 5 minutes if error rates exceed a set limit. | UC-6 |

As seen above, three new Quality Attributes have been added to the system to facilitate the completion of this iteration, as its focus is on the realization of Quality Attributes through system development.

#### Constraints

| ID    | Constraint |
| ----- | ---------- |
| **CON-1** | The system must scale to accommodate at least 5,000 concurrent users. |
| **CON-2** | The system must be interoperable with APIs (e.g. REST or GraphQL). |
| **CON-3** | The system must be accessible from mobile, web, and voice-assistant-based platforms. |
| **CON-4** | The system must be cloud-compatible and scalable (e.g. Kubernetes, AWS). |
| **CON-5** | The system shall comply with authentication and institutional privacy policies (SSO). |

#### Concerns

| ID | Concern |
| --- | --- |
| **CRN-1: Integration** | The system must integrate the Chatbot into various aspects of the existing systems (e.g registration, calendar, email). This requires architecture that manages the various APIs of those services and their data synchronisation. |
| **CRN-2: Low-Level Access Control** | The system will handle sensitive data for the institution (e.g Student, Lecturer). The authentication architecture must have access controls applied not just by user roles (student, lecturer) but also include the user’s direct relationship to the material. For example a Lecturer can access attendance analytics for courses they teach but not for unaffiliated courses. |
| **CRN-3: Rate Limitations** | The Chatbot is expected to scale to upwards of 5000 concurrent users. A fair usage policy tied to the user’s authentication to prevent slowdown on servers and maintain response times. |
| **CRN-4: Data Aggregation** | The Chatbot needs to ingest data from multiple sources. Cached vs non-cached data has different access times. The design must strike a balance between cached vs non-cached data to reach performance and latency targets. |

---

### Step 2: Establish Iteration Goal by Selecting Drivers

**Iteration goal:** The goal for this iteration is to build upon previous structures within the system and fulfill the selected quality attribute scenarios.

| ID   | Scenario | Associated Use Case |
| --- | --- | --- |
| **QA-6: Auditability** | An auditor requests a history of content changes and analytics accesses for a course over a time period. The system returns a chronological list (with who/what/when information about changes) in less than 5 seconds with 99% completeness. | UC-3, UC-4 |
| **QA-7: Usability** | A student starts a conversation with the chatbot on mobile and continues on a laptop later. After opening the chatbot, the previous conversation context and preferences are restored and ready to use in less than two seconds. | UC-1, UC-2 |
| **QA-8: Operability** | A system maintainer deploys a new version of the Conversation Service with zero downtime for end-users. The system must be able to rollback automatically to a previous version within 5 minutes if error rates exceed a set limit. | UC-6 |

---

### Step 3: Selecting Element to Decompose

For this iteration, we focus on decomposing the microservices and data tiers to introduce:

- Analytics and reporting  
- Audit logging  
- Session/context management  
- Deployment and monitoring  

This will enable the development of components that will manifest the desired quality attributes (QA-6–8) in the overall system. Thus, the elements to decompose will be the existing **Microservices Tier** and the **Data Tier**. The following elements will be introduced to complete this goal:

- Analytics Service, associated Analytics Store  
- Audit Logging Service, Audit Log Store  
- Session/Profile Management components  
- Monitoring & Alerting Service, Deployment Pipeline/Manager  

---

### Step 4: Choosing Design Concepts Satisfying the Inputs

#### Analytics & Reporting (QA-6)

- **Dedicated Analytics Service**
  - Aggregates Chatbot DB, University DB, and Notification data.
  - Provides dashboards for different roles (student vs. lecturer).
  - Takes over heavy aggregation logic from Conversation/Notification services.
- **Analytics Data Store**
  - Specialized for analytics queries.
  - ETL from Chatbot/University DBs and Notification Queue.
- **Data Aggregation Methods**
  - ETL jobs to regularly update analytics store.
  - Pre-calculated aggregates for metrics that are requested often.

#### Audit Logging (QA-6)

- **Audit Logging Service**
  - Conversation, Notification, Content, Analytics services emit audit events for sensitive operations (e.g. content upload, config. changes, etc.).
  - Audit events written to log (append-only, not allowing changes).
- **Audit Log Store**
  - Separate from Chatbot DB to prevent tampering/modification and support long-term retention.
  - Design optimized for writing.
- **Audit Query Interface**
  - Admin API to query and filter records (e.g. by user, course, time range, action type, etc.).

#### Context Maintenance (QA-7)

- **Session Manager/Context Service**
  - Manage conversation sessions between communication channels and devices.
  - Store session identifiers associated with devices/channels and user identity.
- **User Profile Service**
  - Manage user preferences that are persistent over time and devices/channels (conversation language, notification settings, etc.).
  - Expose API (`getProfile(userId)`, `updatePreferences(...)`) to allow for retrieval and modification of user information.
- **Conversation History Maintenance**
  - Conversation Service uses Session Manager and Chatbot DB to load previous interactions when conversation is resumed on another device.

#### Deployability & Monitorability (QA-8)

- **CI/CD Pipeline, Deployment Manager**
  - Integrate with container platform to support deployments rolled out to small user subsets (canary deployments) or new versions deployed and run with the current version (blue/green).
- **Traffic Control**
  - API Gateway sends small portion of traffic to new version before full rollout (canary deployment).
- **Monitoring & Alerting Service**
  - Central monitoring system collects logs/metrics from all services.
  - Provides dashboards and alerts to system maintainers.
- **Configuration Service**
  - Allows feature rollout and configuration without requiring system redeployment.

#### Mapping to Spring Framework

- **Spring Boot** – Analytics, Audit Logging, Session Manager, User Profile, Monitoring  
- **Spring Cloud** – Service discovery, routing  
- **Spring Security** – Consistent user identity between sessions and audit events  
- **Spring Data** – Access to Analytics Store and Audit Log Store  

---

### Step 5: Instantiate Architectural Elements, Allocate Responsibilities, Define Interfaces

| Element | Responsibility | Associated Driver |
| --- | --- | --- |
| **Analytics Service** | Aggregate and provide analytical stats for students and lecturers. Fetch Chatbot DB, University DB, and Analytics Store data. Return dashboards based on role. | QA-6 |
| **Analytics Store** | Data store optimized for read-heavy queries of analytical stats. Holds pre-aggregated metrics and engagement data. Populated using ETL. | QA-6 |
| **Audit Logging Service** | Receive audit events from Conversation, Notification, Content, Analytics, and Integration services. Write them into Audit Log Store. Expose API for audit queries used by admins. | QA-6 |
| **Audit Log Store** | Append-only tamper-proof audit record storage component. Supports filtering based on criteria (user, course, time, etc.). | QA-6 |
| **Session Manager/Context Service** | Maintain conversation sessions and link to user identities across channels and devices. Provide APIs to create, retrieve, and update session states for quick context restoration. | QA-7 |
| **User Profile Service** | Hold and return user preferences, exposing APIs to let other services personalize responses and notification behavior. | QA-7 |
| **Monitoring & Alerting Service** | Collect logs and metrics info from all services. Provide data to dashboards for maintainers and trigger alerts when error threshold is surpassed. | QA-8 |
| **CI/CD Pipeline & Deployment Manager** | Build, test, and deploy new versions of system services. Coordinate deployments and trigger rollback when error threshold is surpassed. | QA-8 |
| **Traffic Controller** | Configure API Gateway, controlling which version of the system receives traffic. Supports gradual deployment/rollback. | QA-8 |
| **ETL/Streaming Pipeline** | Transfer data from databases and notification queue into Analytics Store. Ensure analytics stay consistent and updated using data aggregation and transformation. | QA-6 |
| **Existing Services Reused in Iteration 3** | Keep Iteration 2 responsibilities. Extended to also emit audit events to Audit Logging Service, consult User Profile Service and Session Manager, and expose metrics for Monitoring & Alerting. | QA-7 |

---

### Step 6: Sketch Views and Record Design Decisions

#### Deployment Diagram

<img width="1146" height="782" alt="image" src="https://github.com/user-attachments/assets/3886185c-44d9-4658-aaef-f1853fe5895f" />

| Element | Description |
| --- | --- |
| **Client App** | Web/mobile UI running on user device. Sends HTTPS requests to backend, displays chatbot responses, notifications, analytics. |
| **API Gateway** | External entry point for system. Routes requests to microservices, handles cross-cutting issues. |
| **Traffic Controller** | Configures API Gateway routing to control which versions get traffic. |
| **Service Registry** | Tracks available microservices instances so other services and gateway can do service discovery. |
| **Conversation Service** | Uses Session Manager, User Profile, Cache, and Chatbot DB to interpret intent and return response. |
| **Notification Service** | Reads/writes notification metadata in Chatbot DB and Notification Queue. Enables notification functionality. |
| **Content Management Service** | Coordinates with University Integration Adapter and Chatbot DB to allow upload/modifying/deleting of content. |
| **Analytics Service** | Collects data from different DBs to make dashboards for different roles, enabling analytics functionality. |
| **Authorization/Policy Service** | Evaluates access control policies for protected operations. |
| **University Integration (Adapter)** | Abstracts external APIs and accesses university DB. |
| **Session Manager/Context Service** | Maintains conversation sessions over devices and channels, stores session IDs/context to allow resuming of conversations. |
| **User Profile Service** | Stores and retrieves persistent user preferences for personalization. |
| **Audit Logging Service** | Receives audit events from services and writes them to Log Store. Exposes admin APIs for querying of audits. |
| **Cached Storage** | Cache in-memory for frequently-accessed data to speed up response time. |
| **Chatbot DB** | Database for chatbot-specific data. |
| **University DB** | Database for university data, accessed by University Integration Adapter. |
| **Notification Queue** | Queue storing pending notifications and schedule. Allows asynchronous sending and retries. |
| **Analytics Store** | Holds aggregated analytical metrics and engagement data. Populated with ETL. |
| **Audit Log Store** | Stores audit records, supports queries, append-only. |
| **CI/CD Pipeline & Deployment Manager** | Builds, tests, and deploys new service versions. Triggers rollback and coordinates deployments. |
| **Monitoring & Alerting Service** | Collects metrics from microservices, provides dashboards, raises alerts. |
| **ETL/Streaming Pipeline** | Collects data from stores to update the Analytics Store. |

---

#### UC-4: View Personalized Analytical Statistics

<img width="1139" height="574" alt="image" src="https://github.com/user-attachments/assets/f7ec6a86-4e07-484f-a32c-60490f0e7133" />

| Element | Description |
| --- | --- |
| **Student** | Requests personalized analytics dashboard for a course. |
| **Client App** | Front-end sends analytics request and displays resulting dashboard. |
| **API Gateway** | Receives analytics request, validates it, forwards call to Analytics Service. |
| **Analytics Service** | Carries out analytics request. |
| **Authorization/Policy Service** | Confirms if user can view analytics for requested course. |
| **User Profile Service** | Provides user preferences to let dashboard be personalized. |
| **University Integration (Adapter)** | Retrieves contextual info from Chatbot DB and University DB. |
| **Analytics Store** | Pre-aggregated metrics information for course and time range. |
| **Chatbot DB** | Extra context used by integration adapter. |
| **Audit Logging Service** | Records that user accessed analytics for given course and time range. |
| **Audit Log Store** | Persists audit record in log for later review. |

---

#### UC-6: Monitor and Maintain System Health (Zero-downtime Deployment)

<img width="1147" height="823" alt="image" src="https://github.com/user-attachments/assets/ff18c69d-284b-462b-85fa-be6b320b864e" />

| Element | Description |
| --- | --- |
| **System Maintainer** | Deploys new versions of services, monitors system health. |
| **CI/CD Pipeline & Deployment Manager** | Automates building/testing/packaging/deployment of new versions of services. |
| **Service Registry** | Registers/deregisters service instances so components know currently-available versions. |
| **API Gateway** | Routes user traffic to appropriate Conversation Service. |
| **Traffic Controller** | Manages policies for gateway, controls what amount of traffic goes to each version. |
| **Monitoring & Alerting Service** | Collects metrics from Conversation Service instance and API Gateway, exposes dashboards used by System Maintainer. |
| **Conversation Service v1** | Current version of Conversation Service receiving most traffic initially. |
| **Conversation Service v2** | New version of Conversation Service receiving only a small part of the traffic at first, either promoted or rolled back. |

---

#### Key Design Decision Table

| Design ID | Design Decision | Rationale | Impacted Drivers |
| --- | --- | --- | --- |
| **D8** | Introduced a dedicated Analytics service and store | Separates analytic work from operational services and optimizes read performance for dashboards. | QA-6 |
| **D9** | Added Audit Logging service and Audit Log Store | Provides audit logs that cannot be changed, allowing for data to be used in investigations. | QA-6 |
| **D10** | Added a session manager and user profile service | Enables multi-device conversation continuation and personalized behaviour based on stored preferences. | QA-7 |
| **D11** | Added Monitoring & Alerting Service | Provides real-time observability and dashboards for maintainers, which is required for safe rollouts. | QA-8 |
| **D12** | Adopted a CI/CD Pipeline with blue/green or canary deployments and Version Router in front of services. | Enables zero-downtime deployments and fast rollback in case of errors. | QA-8 |
| **D13** | Introduced ETL/Streaming pipeline between operational data stores and Analytics Store. | Ensures analytics reflect current data while isolating operational workloads from analytical workloads. | QA-6 |

---

### Step 7: Analyze Current Design and Review Iteration 3 Objectives

The iteration was designed to fulfill the newly-added quality attributes of Auditability (QA-6), Usability (QA-7), and Operability (QA-8). These were accomplished by adding Analytics, Audit Logging, and Operations components and integrating them with the currently existing services, as well as establishing session/profile management support.

#### Quality Scenarios

| ID | Status | Rationale / Next Steps | Planned For |
| --- | --- | --- | --- |
| **QA-1: Performance** | 🟩 | Microservices, caching, and horizontal scaling still in place; analytics load moved to separate Analytics Store. | IT1 |
| **QA-2: Security** | 🟩 | Role-based policies via Authorization/Policy Service; all sensitive operations audited and tied to authenticated identities. | IT2 & IT3 |
| **QA-3: Modifiability** | 🟩 | Clear service boundaries and adapter pattern for LMS; feature flags and independent deployments enhance modifiability. | IT1 & IT3 |
| **QA-4: Reliability** | 🟩 | Notification Service with queue and retry logic; monitoring for failures; analytics separated from operational workloads. | IT2 & IT3 |
| **QA-5: Availability** | 🟩 | Service registry, stateless services, and zero-downtime deployment support high availability. | IT1 & IT3 |
| **QA-6: Auditability** | 🟩 | New Audit Logging Service and Audit Log Store, analytics and content services emitting events; admin audit queries supported. | IT3 |
| **QA-7: Usability** | 🟩 | Session Manager and User Profile Service restore conversation context and preferences across channels in less than 2 seconds. | IT3 |
| **QA-8: Operability** | 🟩 | CI/CD pipeline, Version Router, and Monitoring & Alerting Service support safe canary/blue-green deployments and quick rollback. | IT3 |

---

## ATAM Assessment (Iteration 3)

An ATAM assessment was performed individually for each Quality Attribute addressed in this iteration.

### ATAM Assessment: Auditability (QA-6)

#### Utility Tree: Auditability

**Top-level Goal:** Utility

- **Auditability (QA-6)**
  - **History of Content Changes**
    - **AU1:** Retrieve full history of content changes for a course in \<5 seconds, over last term (High importance, High risk).
  - **Access Compliance**
    - **AU2:** List all analytics views for a course (who/when) in \<5 seconds (High importance, Medium risk).

#### Risk Assessment Table (QA-6)

| Risk ID | Scenario / QA | Architectural Decision | Risk | Sensitivity Points | TradeOffs / Non-Risk Issues |
| --- | --- | --- | --- | --- | --- |
| **R5** | QA-6: Auditability | Introduce an Audit Logging service and an append-only Audit Log Store. | Without an Audit Logging service available we risk compromising compliance and losing audit trails. | Availability and performance of the Audit Logging Service, as well as the durability of the append-only Audit Log Store. | A dedicated audit service adds complexity but is worthwhile for a proper audit trail. |
| **R6** | QA-6 | Auditor requests the history of content changes and access to analytics. High amounts of audit data will lead to slow queries and place us outside of the 5 second threshold. | High amounts of audit data will lead to slow queries and place us outside of the 5 second threshold. | Indexing technique for the Audit Log Store and efficiency of our Audit Query Interface. | Strike a balance between optimizing for fast writes and efficient reads for queries. |

#### Analysis of Auditability Risks

We face a tradeoff and must compromise between **Data Integrity** and **Performance**:

- **Performance:** Need to ensure a balance between the large amount of audit data (Risk R6) and our 5 second latency requirement.  
- **Dependency:** By creating a centralized audit system (Risk R5) we meet the required standards for compliance but create a critical dependency on the audit system. To address this problem we have replication and failover protection to ensure the component’s uptime.

---

### ATAM Assessment: Usability (QA-7)

#### Utility Tree: Usability

- **Usability – Context Continuity (QA-7)**
  - **Persistent Context**
    - **U1:** Resume conversation on new device with previous 20 turns and preferences in \<2 seconds (High importance, Medium risk).
  - **User Configuration**
    - **U2:** Users can adjust notification preferences; the changes take effect on future notifications without logging out/in (Medium importance, Low risk).

#### Risk Assessment Table (QA-7)

| Risk ID | Scenario / QA | Architectural Decision | Risk | Sensitivity Points | TradeOffs / Non-Risk Issues |
| --- | --- | --- | --- | --- | --- |
| **R7** | QA-7: Persistent Context | Introduce Session Manager/Context Service and the User Profile Service. | The session manager fails to restore conversation context, resulting in a bad user experience overall. | Reliability and scalability of the Session Manager service, maintaining consistent session data across devices. | Maintaining session state, although complex, is vital for a cross-device user experience. |
| **R8** | QA-7: Cross-Device Context | Student starts chat on their mobile but continues on laptop. | Latency in retrieving the users’ information could take more than 2 seconds as required. | Performance of the User Profile Service, conversation history service, and network latency. | Balance between the speed of retrieval and the quality of context restored. |

#### Analysis of Usability Risks

The primary challenge is the **performance cap for restoring context**:

- To achieve a smooth cross-device experience, the system needs to retrieve the user data in under 2 seconds. The Session Manager makes this possible but has a latency risk (R8). To address this problem we have to achieve a balance between data persistence and latency.

---

### ATAM Assessment: Operability (QA-8)

#### Utility Tree: Operability

- **Operability – Deployability (QA-8)**
  - **O1:** Deploy new Conversation Service version with zero downtime and rollback in \<5 minutes (High importance, High risk).
- **Monitorability**
  - **O2:** Detect and alert on error-rate spikes in \<1 minute (High importance, Medium risk).

#### Risk Assessment Table (QA-8)

| Risk ID | Scenario / QA | Architectural Decision | Risk | Sensitivity Points | TradeOffs / Non-Risk Issues |
| --- | --- | --- | --- | --- | --- |
| **R1** | QA-8: Zero-downtime deployment of Conversation Service | Use CI/CD pipeline, canary/blue-green deployments, Version Router + API Gateway. | Misconfiguration of routing rules could send too much traffic to faulty versions, impacting users before rollback triggers. | Thresholds for error rates, percentage of canary traffic, health-check definitions. | Higher operational complexity vs. simpler “stop-and-restart” deployment; but significantly improves availability and reduces downtime. |
| **R2** | QA-8: Automatic Rollback | Introduce Monitoring & Alerting Service for automated rollback decisions. | Incorrect alert thresholds or missing metrics may delay rollback or cause false positives. | What metrics are monitored (latency, error rate), and the threshold configuration. | Tradeoff between sensitivity (fast rollback) and stability (avoiding unnecessary rollbacks). |
| **R3** | QA-8: Independent service deployment | Independent deployment of microservices (Conversation, Notification, Content, Analytics). | Incompatible changes between services can surface only at runtime despite canary testing. | API contracts between services, backward compatibility of message formats. | Encourages discipline in API versioning; requires additional effort in contract testing and version management. |
| **R4** | QA-8: Centralized Monitoring Service | Logging and metrics are centralized via Monitoring Service. | Central monitoring stack becomes a critical dependency: if it fails, operators have poor visibility. | Availability and scaling of Monitoring Service and its storage backends. | Can be mitigated with replication and failover for monitoring platforms; adds some infrastructure cost but is acceptable. |

#### Analysis of Operability Risks

To meet our goal of zero-downtime deployment we have accepted the required architectural complexity.

- **Operational complexity vs Availability:**  
  The architecture now has canary releases, version routers, automated rollbacks (R1, R2, R3). This does carry additional burden upon the infrastructure and the health checks in place.

- **Centralised Visibility:**  
  For a unified view of the system health we implemented centralized monitoring. However, this creates a dependency on the monitoring service. To address this we have redundancy in place.

  <img width="1145" height="549" alt="image" src="https://github.com/user-attachments/assets/32c3cf54-f4c5-456e-a807-d56d79e7a57a" />
