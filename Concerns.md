| ID | Description |
| :--- | :--- |
| **CRN-1: Current System Integration** | The LMS system must integrate the Chatbot into various aspects of its existing systems (e.g registration, calendar, email). This requires architecture that manages the various APIs of those services and their data synchronisation. |
| **CRN-2: Low-Level Access Control** | The system will handle sensitive data for the institution (e.g Student, Lecturer). The authentication architecture must have access controls applied not just by user roles (student, lecturer) but also include the user’s direct relationship to the material. For example, a Lecturer can access attendance analytics for courses they teach but not for unaffiliated courses. |
| **CRN-3: Rate Limitations** | The Chatbot is expected to scale to upwards of 5,000 concurrent users. A fair usage policy tied to the user’s authentication to prevent slowdown on servers and maintain response times. |
| **CRN-4: Data Aggregation** | The Chatbot needs to ingest data from multiple sources. Cached vs non-cached data has different access times. The design must strike a balance between cached vs non-cached data to reach performance and latency targets. |
