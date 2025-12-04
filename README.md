# Final Project - Phase III

## Students

-   **Nabhil Irtisham** -- 100914422\
-   **Colton Bernas** -- 100918756\
-   **Ammeer Jamal Talwani** -- 100791669

------------------------------------------------------------------------

# ADD Iteration 3

## Step 1: Review Inputs

**Purpose:**\
The purpose of this iteration is to build on the structures described in
iterations 1 and 2 in order to fulfill targeted quality attribute
scenarios.

------------------------------------------------------------------------

## Primary Functional Requirements

  -----------------------------------------------------------------------
  Use Case                       Description
  ------------------------------ ----------------------------------------
  **UC‑1: Student queries        The user inputs a request via text or
  chatbot for information**      voice input. Chatbot determines language
                                 and intent, then responds in the same
                                 language using stored knowledge and live
                                 data.

  **UC‑2: Chatbot manages        Chatbot tracks upcoming events
  notifications and alerts**     (deadlines, tests, etc.) and alerts the
                                 user based on stored information and
                                 preferences.

  **UC‑3: Upload, modify, or     Lecturer uses natural language to
  delete academic content**      publish/update/remove content. Chatbot
                                 processes the command and syncs with the
                                 database.

  **UC‑4: View personalized      Users request dashboard analytics.
  analytical statistics**        Students see their performance;
                                 lecturers see class engagement.

  **UC‑5: Integrate and          Administrators manage external system
  configure external systems**   integrations and data-retention
                                 policies.

  **UC‑6: Monitor and maintain   Maintainers update the system with zero
  system health**                downtime and view monitoring dashboards.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Prioritized Quality Attributes

  ---------------------------------------------------------------------------------
  ID                Scenario             Associated Use Case
  ----------------- -------------------- ------------------------------------------
  **QA‑1:           At peak load,        UC‑1
  Performance**     chatbot handles      
                    5,000 concurrent     
                    queries, with 95%    
                    responding in \<2s.  

  **QA‑2:           Lecturer requests    UC‑4
  Security**        analytics; system    
                    verifies permission  
                    in \<50ms.           

  **QA‑3:           New LMS adoption;    UC‑5
  Modifiability**   chatbot integrates   
                    smoothly while       
                    retaining core       
                    functionality.       

  **QA‑4:           Chatbot sends        UC‑2
  Reliability**     notifications with   
                    99% accuracy and     
                    retries on failures. 

  **QA‑5:           Automatic failover   UC‑6
  Availability**    restores service     
                    \<60s after failure. 

  **QA‑6:           Auditor retrieves    UC‑3, UC‑4
  Auditability**    detailed             
                    chronological        
                    change/access logs   
                    in \<5s with 99%     
                    completeness.        

  **QA‑7:           Student resumes      UC‑1, UC‑2
  Usability**       chatbot conversation 
                    across devices with  
                    full context \<2s.   

  **QA‑8:           New Conversation     UC‑6
  Operability**     Service deployed     
                    with zero downtime;  
                    automatic rollback   
                    \<5 minutes on       
                    errors.              
  ---------------------------------------------------------------------------------

------------------------------------------------------------------------

## Constraints

  -----------------------------------------------------------------------
  ID                Constraint
  ----------------- -----------------------------------------------------
  **CON‑1**         System must support 5,000 concurrent users.

  **CON‑2**         Must interoperate with APIs (REST, GraphQL).

  **CON‑3**         Must work on mobile, web, and voice platforms.

  **CON‑4**         Must be cloud‑compatible and scalable (Kubernetes,
                    AWS).

  **CON‑5**         Must comply with SSO authentication and institutional
                    privacy.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Concerns

  -----------------------------------------------------------------------
  ID                    Concern
  --------------------- -------------------------------------------------
  **CRN‑1:              System must integrate with existing institutional
  Integration**         systems (calendar, registration, email).

  **CRN‑2: Low‑Level    Must enforce role‑based AND relationship‑based
  Access Control**      access (lecturers only access their own courses).

  **CRN‑3: Rate         System must prevent overload and maintain latency
  Limitations**         requirements.

  **CRN‑4: Data         Need balance between cached vs. non‑cached data
  Aggregation**         to meet performance.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# Step 2: Iteration Goal (Drivers Selected)

**Iteration Goal:**\
Build upon previous structures to fulfill targeted quality attributes
QA‑6, QA‑7, and QA‑8.

### Targeted Quality Attribute Scenarios

-   **QA‑6: Auditability** -- Provide audit logs in \<5 seconds, 99%
    complete.\
-   **QA‑7: Usability** -- Restore cross‑device conversation context in
    \<2 seconds.\
-   **QA‑8: Operability** -- Deploy updates with zero downtime; rollback
    \<5 minutes.

------------------------------------------------------------------------

# Step 3: Selecting Elements to Decompose

This iteration decomposes:

-   **Microservices Tier**
-   **Data Tier**

New elements introduced:

-   Analytics Service & Analytics Store\
-   Audit Logging Service & Audit Log Store\
-   Session/Context Management\
-   Monitoring & Alerting Service\
-   Deployment Pipeline tools

------------------------------------------------------------------------

# Step 4: Choosing Design Concepts Satisfying Inputs

## **Analytics & Reporting (QA‑6)**

### Components

-   **Dedicated Analytics Service**
-   **Analytics Data Store**
-   **ETL Jobs / Data Aggregation**

### Purpose

-   Offload aggregation from operational services\
-   Provide near‑real‑time analytics dashboards\
-   Support role‑based analytical access

------------------------------------------------------------------------

## **Audit Logging (QA‑6)**

### Components

-   **Audit Logging Service**\
-   **Audit Log Store** (append‑only, tamper‑proof)\
-   **Audit Query Interface**

### Purpose

-   Track all content changes + analytics access events\
-   Support compliance audits\
-   Guarantee integrity + immutability

------------------------------------------------------------------------

## **Context Maintenance (QA‑7)**

### Components

-   **Session Manager / Context Service**
-   **User Profile Service**
-   **Conversation History Service**

### Purpose

-   Restore conversation across devices\
-   Maintain user preferences\
-   Keep system responses personalized

------------------------------------------------------------------------

## **Deployability & Monitoring (QA‑8)**

### Components

-   **CI/CD Pipeline**
-   **Deployment Manager**
-   **Canary / Blue‑Green Deployment**
-   **Monitoring & Alerting Service**
-   **Configuration Service**

------------------------------------------------------------------------

# Step 5: Architectural Elements, Responsibilities, and Interfaces

  ------------------------------------------------------------------------
  Element             Responsibility                     Driver
  ------------------- ---------------------------------- -----------------
  **Analytics         Generate dashboards; fetch &       QA‑6
  Service**           aggregate data                     

  **Analytics Store** Optimized data store for analytics QA‑6
                      queries                            

  **Audit Logging     Receives audit events, stores      QA‑6
  Service**           logs, exposes audit queries        

  **Audit Log Store** Append‑only log storage            QA‑6

  **Session Manager** Maintain sessions + conversation   QA‑7
                      context                            

  **User Profile      Store/retrieve user preferences    QA‑7
  Service**                                              

  **Monitoring &      Collect metrics; trigger alerts    QA‑8
  Alerting Service**                                     

  **CI/CD &           Ensure zero‑downtime deployment    QA‑8
  Deployment                                             
  Manager**                                              

  **Traffic           Route traffic between versions     QA‑8
  Controller**                                           

  **ETL Pipeline**    Move data from operational DBs to  QA‑6
                      analytics store                    
  ------------------------------------------------------------------------

------------------------------------------------------------------------

# Step 6: Sketch Views and Record Design Decisions

(Diagram placeholders preserved from your text.)

------------------------------------------------------------------------

# Key Design Decisions

  -------------------------------------------------------------------------
  ID        Decision              Rationale               Driver
  --------- --------------------- ----------------------- -----------------
  **D8**    Dedicated Analytics   Boosts performance &    QA‑6
            Service + Store       reduces load on         
                                  operational DB          

  **D9**    Audit Logging         Compliance + integrity  QA‑6
            Service + Log Store                           

  **D10**   Session Manager +     Enable cross‑device     QA‑7
            User Profile Service  continuity              

  **D11**   Monitoring & Alerting Required for safe       QA‑8
            Service               rollouts                

  **D12**   CI/CD +               Enables zero downtime   QA‑8
            Canary/Blue‑Green                             
            Deployment                                    

  **D13**   ETL Pipeline          Keeps analytics         QA‑6
                                  consistent              
  -------------------------------------------------------------------------

------------------------------------------------------------------------

# Step 7: Analyze Design and Confirm Iteration Objectives

All targeted quality attributes (QA‑6, QA‑7, QA‑8) are satisfied.

------------------------------------------------------------------------

# ATAM Assessment

## **Auditability (QA‑6)**

### Utility Tree

-   **AU1:** Retrieve history \<5s (High importance, High risk)\
-   **AU2:** Retrieve analytics access logs \<5s (High importance,
    Medium risk)

### Risks

  Risk     Description
  -------- -----------------------------------------------
  **R5**   Audit system failure impacts compliance
  **R6**   Large audit volume may hinder \<5s query time

------------------------------------------------------------------------

## **Usability (QA‑7)**

### Utility Tree

-   **U1:** Resume session in \<2s\
-   **U2:** Update preferences immediately

### Risks

  Risk     Description
  -------- ---------------------------------------------
  **R7**   Session Manager may fail to restore context
  **R8**   Query latency may exceed 2s

------------------------------------------------------------------------

## **Operability (QA‑8)**

### Utility Tree

-   **O1:** Zero‑downtime deployments\
-   **O2:** Detect spikes in \<1 minute

### Risks

  Risk     Description
  -------- ----------------------------------------
  **R1**   Routing misconfiguration affects users
  **R2**   Incorrect rollback thresholds
  **R3**   Incompatible service deployments
  **R4**   Monitoring Service becomes bottleneck

------------------------------------------------------------------------

End of full document.
