# 🌾 Anna-Data-Setu

> **Digital Crop Procurement Coordination & Queue Management Platform**\
> **SIH 2026 Project --- Team Techlume**

Anna-Data-Setu is a full-stack platform designed to make government
crop-procurement centre visits more predictable and transparent for
farmers.

Instead of requiring farmers to arrive and wait without knowing their
queue status, the platform provides a **rolling 7-day booking system,
daily capacity management, queue tokens, real-time queue updates,
procurement tracking, payment-status tracking, complaints, and
administrative monitoring**.

The platform is a **coordination and tracking layer**. It does not
directly execute government procurement payments or replace official
procurement systems.

------------------------------------------------------------------------

## 📌 Table of Contents

-   [Problem](#-problem)
-   [Solution](#-solution)
-   [Key Features](#-key-features)
-   [User Roles](#-user-roles)
-   [System Architecture](#-system-architecture)
-   [Technology Stack](#-technology-stack)
-   [Project Structure](#-project-structure)
-   [Core Workflows](#-core-workflows)
-   [Booking & Capacity Logic](#-booking--capacity-logic)
-   [Queue Management](#-queue-management)
-   [Procurement Workflow](#-procurement-workflow)
-   [Notifications & WebSockets](#-notifications--websockets)
-   [Authentication & Security](#-authentication--security)
-   [Database Architecture](#-database-architecture)
-   [API Overview](#-api-overview)
-   [Getting Started](#-getting-started)
-   [Configuration](#-configuration)
-   [Development Workflow](#-development-workflow)
-   [Known Limitations](#-known-limitations)
-   [Future Improvements](#-future-improvements)
-   [SIH Defense Points](#-sih-defense-points)
-   [Team](#-team)
-   [License](#-license)

------------------------------------------------------------------------

# 🎯 Problem

The traditional procurement-centre process can create several
operational problems:

-   Farmers may arrive without visibility into expected waiting time.
-   Large numbers of farmers can arrive at the same time.
-   Queue ordering may not be transparent.
-   Farmers may have to spend long periods physically waiting.
-   Procurement-centre staff need a better way to manage the daily
    queue.
-   Farmers may not know the status of procurement or payment records.
-   Complaints and operational issues can be difficult to track
    centrally.

Anna-Data-Setu addresses these problems by coordinating the **booking →
queue → procurement → payment-status → complaint** lifecycle digitally.

------------------------------------------------------------------------

# 💡 Solution

Anna-Data-Setu introduces:

1.  **Farmer registration and profile management**
2.  **Procurement-centre discovery and recommendation**
3.  **Rolling 7-day booking**
4.  **Daily centre capacity management**
5.  **Sequential queue tokens**
6.  **Live queue updates using WebSockets/STOMP**
7.  **Centre Officer queue operations**
8.  **Quality-check and weighment recording**
9.  **Internal payment-status tracking**
10. **Farmer complaints**
11. **Admin centre and operator management**
12. **Analytics and operational monitoring**
13. **Real-time in-app notifications**

Farmers book a **date**, not a rigid time slot. The queue token is
associated with the day of procurement.

------------------------------------------------------------------------

# ✨ Key Features

## 👨‍🌾 Farmer

-   Farmer registration
-   JWT-based authentication
-   Mandatory farmer profile completion
-   State → District → City selection
-   Bank-detail management
-   Procurement-centre recommendation
-   Centre selection
-   7-day date availability
-   Crop selection
-   Expected quantity entry
-   Booking confirmation
-   Booking history
-   Booking cancellation/rescheduling
-   Live queue position
-   Token status
-   Estimated waiting time
-   Procurement status
-   Payment status
-   Complaint submission
-   Notification centre
-   Real-time queue updates

## 🧑‍💼 Centre Officer

The technical role is `OPERATOR`. The UI refers to this user as **Centre
Officer**.

-   Centre-specific dashboard
-   View daily queue
-   Call farmer
-   Move farmer to processing
-   Put farmer on HOLD
-   Recall a held farmer
-   Complete queue session
-   Record procurement quality information
-   Record gross/tare weight
-   Track accepted quantity
-   Handle assigned complaints
-   Receive real-time queue updates

Each Operator is assigned to **one procurement centre at a time**.

## 🛠️ Admin

-   Admin authentication
-   Dashboard overview
-   Create procurement centres
-   Edit procurement centres
-   Create Centre Officers / Operators
-   Edit Operators
-   Assign Operators to centres
-   Manage Admin accounts
-   Monitor analytics
-   View complaints
-   Assign complaints
-   Update complaint status
-   Monitor procurement and payment information

------------------------------------------------------------------------

# 👥 User Roles

The backend strictly uses three technical roles:

  -----------------------------------------------------------------------
  Technical Role          UI Name                 Responsibility
  ----------------------- ----------------------- -----------------------
  `FARMER`                Farmer                  Booking, queue
                                                  tracking, complaints
                                                  and personal records

  `OPERATOR`              Centre Officer          Centre queue and
                                                  procurement operations

  `ADMIN`                 Admin                   Centre/operator
                                                  management, complaints
                                                  and analytics
  -----------------------------------------------------------------------

There is **no technical `OFFICER`, `DISTRICT_ADMIN`, `STATE_ADMIN`, or
`SUPER_ADMIN` role**.

> The backend contains an `officer/` package because of module
> naming/history. It does not represent a fourth technical role. The
> current technical role is `OPERATOR`.

------------------------------------------------------------------------

# 🏗️ System Architecture

``` mermaid
flowchart TD
    A[React Frontend] -->|REST API + JWT| B[Spring Boot Backend]
    A <-->|STOMP WebSocket| B

    B --> C[Controllers]
    C --> D[Services]
    D --> E[Spring Data JPA / Repositories]
    E --> F[(PostgreSQL)]

    B --> G[JWT Security]
    B --> H[SimpMessagingTemplate]
    H --> A
```

### Request lifecycle

``` text
React UI
   ↓
Axios REST Request
   ↓
JWT Authentication Filter
   ↓
SecurityContext
   ↓
Controller
   ↓
DTO Validation
   ↓
Service / Business Logic
   ↓
Repository
   ↓
Hibernate / JPA
   ↓
PostgreSQL
```

For real-time events:

``` text
Backend Event
    ↓
WebSocketPublisher
    ↓
SimpMessagingTemplate
    ↓
STOMP Topic
    ↓
React Subscriber
    ↓
UI Update
```

------------------------------------------------------------------------

# 🧰 Technology Stack

  Technology          Purpose
  ------------------- ------------------------------------------
  Java 21             Backend runtime
  Spring Boot         Backend framework
  Spring Security     Authentication and authorization
  JJWT                JWT generation and validation
  PostgreSQL          Relational database
  Hibernate / JPA     ORM and persistence
  WebSocket / STOMP   Real-time queue and notification updates
  React               Frontend
  Vite                Frontend build tooling
  Tailwind CSS        Frontend styling

The current handover document records the project versions as Java 21,
Spring Boot 4.1.1, JJWT 0.13.0, React 19.2.8, Vite 8.2.2 and Tailwind
CSS 4.3.3.

> PostgreSQL is listed as "Latest" in the handover documentation. Use
> the PostgreSQL version installed/configured for the development
> environment rather than assuming a specific version.

------------------------------------------------------------------------

# 📁 Project Structure

The repository is organized as a full-stack project with separate
backend and frontend applications.

``` text
Techlume/
│
├── backend-sih/
│   └── src/
│       └── main/
│           └── java/
│               └── backend_sih/
│                   └── project/
│                       │
│                       ├── admin/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   └── service/
│                       │
│                       ├── analytics/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   └── service/
│                       │
│                       ├── audit/
│                       │   ├── controller/
│                       │   └── service/
│                       │
│                       ├── auth/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   ├── entity/
│                       │   ├── exception/
│                       │   ├── repository/
│                       │   ├── security/
│                       │   └── service/
│                       │
│                       ├── booking/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   ├── entity/
│                       │   ├── repository/
│                       │   └── service/
│                       │
│                       ├── centre/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   ├── entity/
│                       │   ├── repository/
│                       │   └── service/
│                       │
│                       ├── common/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   │   ├── enums/
│                       │   │   └── util/
│                       │   ├── entity/
│                       │   └── repository/
│                       │
│                       ├── complaint/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   ├── entity/
│                       │   ├── repository/
│                       │   └── service/
│                       │
│                       ├── config/
│                       │
│                       ├── exception/
│                       │
│                       ├── farmer/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   ├── entity/
│                       │   ├── repository/
│                       │   └── service/
│                       │
│                       ├── location/
│                       │   └── controller/
│                       │
│                       ├── notification/
│                       │   └── controller/
│                       │
│                       ├── officer/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   ├── entity/
│                       │   ├── repository/
│                       │   └── service/
│                       │
│                       ├── payment/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   ├── entity/
│                       │   └── repository/
│                       │
│                       ├── procurement/
│                       │   └── controller/
│                       │
│                       ├── queue/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   ├── entity/
│                       │   ├── repository/
│                       │   └── service/
│                       │
│                       ├── recommendation/
│                       │   ├── controller/
│                       │   ├── dto/
│                       │   └── service/
│                       │
│                       └── simulation/
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── api/
│   │   │   ├── admin.js
│   │   │   ├── auth.js
│   │   │   ├── booking.js
│   │   │   ├── centre.js
│   │   │   ├── client.js
│   │   │   ├── complaint.js
│   │   │   ├── farmer.js
│   │   │   ├── location.js
│   │   │   ├── master.js
│   │   │   ├── procurement.js
│   │   │   └── queue.js
│   │   │
│   │   ├── assets/
│   │   ├── components/
│   │   │   ├── common/
│   │   │   ├── landing/
│   │   │   ├── layout/
│   │   │   └── notifications/
│   │   │
│   │   ├── context/
│   │   ├── pages/
│   │   │   ├── admin/
│   │   │   ├── auth/
│   │   │   ├── centre/
│   │   │   └── farmer/
│   │   │
│   │   ├── App.jsx
│   │   ├── index.css
│   │   └── main.jsx
│   │
│   └── vite.config.js
│
├── check_db.py
├── extract_endpoints.py
├── test_e2e.py
├── test_edit.py
├── test_ws.cjs
├── test.cjs
└── supporting development/patch scripts
```

------------------------------------------------------------------------

# 🗄️ Database Architecture

The application uses PostgreSQL with JPA/Hibernate.

### Main entities

``` text
User
 ├── FarmerProfile
 └── Centre assignment (Operator)

Farmer
 └── Booking
      └── QueueToken
           └── Procurement
                └── Payment

User
 └── Notification

Farmer
 └── Complaint
```

### Core entities

  Entity            Purpose
  ----------------- ----------------------------------------------
  `User`            Authentication and role information
  `FarmerProfile`   Farmer location and bank/profile information
  `Centre`          Procurement-centre master data
  `Booking`         Farmer's date-based procurement booking
  `QueueToken`      Daily queue position/token
  `Procurement`     Quality and weighment records
  `Payment`         Internal payment-status tracking
  `Notification`    Persistent in-app notifications
  `Complaint`       Farmer grievance tracking
  `Crop`            Crop master data

------------------------------------------------------------------------

# 📅 Booking & Capacity Logic

Anna-Data-Setu intentionally uses **dates instead of time slots**.

### Rolling 7-day window

A farmer can book within:

``` text
Today
+ Day 1
+ Day 2
+ Day 3
+ Day 4
+ Day 5
+ Day 6
```

This creates a rolling seven-calendar-day window.

### Daily capacity

Each centre has a `dailyCapacity`.

Availability is calculated using:

``` text
Available Capacity =
Centre Daily Capacity
-
Number of Non-Cancelled Bookings
```

A booking is rejected when the active booking count reaches the centre's
capacity.

### Important limitation

The current MVP uses:

``` text
count()
   ↓
compare with capacity
   ↓
insert booking
```

This leaves a theoretical race condition when multiple users attempt to
reserve the final available position simultaneously.

Production implementation should use a stronger concurrency-control
strategy such as optimistic/pessimistic locking or another atomic
capacity mechanism.

------------------------------------------------------------------------

# 🎟️ Queue Management

Queue tokens are associated with the centre and procurement date.

### Token concept

``` text
Centre + Date
      ↓
Sequential token number
      ↓
Queue token
```

Example:

``` text
C001-001
C001-002
C001-003
```

Token numbers reset for each centre/day.

### Queue state machine

``` text
WAITING
   ↓
CALLED
   ↓
PROCESSING
   ↓
COMPLETED
```

Temporary hold flow:

``` text
PROCESSING
   ↓
HOLD
   ↓
RECALL
   ↓
PROCESSING
```

### Single active farmer rule

The backend prevents another farmer from being called when the centre
already has a farmer in:

``` text
CALLED
or
PROCESSING
```

The current farmer must be completed or placed on HOLD before another
farmer can be called.

------------------------------------------------------------------------

# ⏸️ HOLD State

`HOLD` is temporary.

It is intended for situations where the farmer cannot immediately
continue the procurement process, for example:

-   Missing/incorrect document
-   Repacking or preparation required
-   Temporary issue requiring the farmer to step away

The Operator can recall the farmer.

The farmer does **not** independently recall their own ticket.

Unresolved HOLD tokens are automatically cancelled by the queue
scheduler near the end of the day according to the current scheduler
configuration.

------------------------------------------------------------------------

# 🌐 Real-Time Queue & WebSockets

The backend uses Spring WebSocket + STOMP.

### WebSocket endpoint

``` text
/ws
```

### Queue topic

``` text
/topic/centre/{centreId}/queue
```

### User notification topic

``` text
/topic/user/{userId}/notifications
```

### Event flow

``` text
Centre Officer
     ↓
REST API
     ↓
QueueService
     ↓
Database update
     ↓
WebSocketPublisher
     ↓
SimpMessagingTemplate
     ↓
STOMP topic
     ↓
Connected Farmer / Operator clients
```

This allows the UI to update queue information without repeatedly
requesting the server for the same state.

------------------------------------------------------------------------

# 🔔 Notification System

Notifications are persisted in PostgreSQL and also pushed through STOMP.

### Flow

``` text
Business Event
     ↓
NotificationService
     ↓
notifications table
     ↓
InAppNotificationProvider
     ↓
User STOMP topic
```

### Implemented notification events

-   `REGISTRATION`
-   `BOOKING_CONFIRMED`
-   `BOOKING_CANCELLED`
-   `QUEUE_CALLED`
-   `QUEUE_HOLD`
-   `QUEUE_RECALLED`
-   `PROCUREMENT_COMPLETED`
-   `PAYMENT_UPDATED`
-   `COMPLAINT_SUBMITTED`
-   `COMPLAINT_UPDATED`
-   `COMPLAINT_RESOLVED`
-   `COMPLAINT_ESCALATED`

------------------------------------------------------------------------

# 🌾 Procurement Workflow

Once a queue session is completed, the actual physical procurement
process can be recorded.

``` text
Queue COMPLETED
      ↓
QUALITY_CHECK
      ↓
WEIGHMENT
      ↓
Accepted Quantity
```

### Quality Check

The Operator records relevant quality information such as moisture
percentage.

### Weighment

The Operator records:

``` text
Gross Weight
Tare Weight
```

The backend derives:

``` text
Net Weight = Gross Weight - Tare Weight
```

and converts the accepted quantity into quintals as required by the
application's procurement model.

------------------------------------------------------------------------

# 💰 Payment Tracking

The current payment module is **not a government payment gateway**.

It is an internal tracking mechanism.

Example status progression:

``` text
NOT_STARTED
      ↓
PROCESSING
      ↓
COMPLETED
```

The platform records the status for coordination/visibility but does not
transfer money.

------------------------------------------------------------------------

# 📝 Complaint Management

Farmers can submit complaints from the application.

Admins can:

-   View complaints
-   Assign complaints
-   Monitor complaint status

Operators can handle complaints assigned to them.

### Current limitation

The planned **automatic 7-day complaint escalation scheduler is not
currently implemented**.

------------------------------------------------------------------------

# 🔐 Authentication & Security

Anna-Data-Setu uses stateless JWT authentication.

### Login flow

``` text
Credentials
    ↓
AuthController
    ↓
BCrypt password verification
    ↓
JWT generation
    ↓
Frontend stores authentication state
    ↓
Axios adds Bearer token
    ↓
JwtAuthenticationFilter
    ↓
SecurityContext
```

### JWT

The JWT contains:

-   Subject: user email
-   Role
-   Expiration

The current documented token lifetime is **24 hours**.

### Password security

Passwords are stored using BCrypt hashing rather than plain text.

### Role enforcement

Spring Security method-level authorization is used to enforce role
boundaries.

Example:

``` java
@PreAuthorize("hasRole('FARMER')")
```

### Farmer data isolation

Farmer-specific APIs derive the authenticated identity from the security
context instead of trusting a farmer ID supplied by the client.

### Operator centre ownership

The backend validates that the authenticated Operator is assigned to the
centre whose queue they are attempting to modify.

------------------------------------------------------------------------

# 🚪 Logout

Logout follows a confirmation-first UX:

``` text
Click Logout
     ↓
Confirmation Modal
     ↓
Cancel → Stay logged in
     ↓
Confirm
     ↓
Clear authentication state
     ↓
Navigate to /
     ↓
React cleanup / WebSocket teardown
```

The user is not logged out before confirmation.

------------------------------------------------------------------------

# 📍 Location System

The current application uses a static State → District → City mapping.

``` text
State
  ↓
District
  ↓
City
```

This is currently implemented without an external government
location/GIS API.

Centre recommendation uses centre coordinates and geographical distance
calculations.

------------------------------------------------------------------------

# 🏢 Centre Management

Admins create procurement centres through the Admin dashboard.

Centre codes are generated by the backend.

Example:

``` text
PRJ-01
PRJ-02
VNS-01
```

The centre code is separate from the internal database ID.

Each Operator is assigned to one centre at a time.

------------------------------------------------------------------------

# 📊 Analytics

The Admin dashboard provides aggregated operational information such as:

-   Capacity utilization
-   Average wait times
-   Procurement volume per centre
-   Centre-level operational data

Analytics are intended for administrative monitoring rather than direct
queue operation.

------------------------------------------------------------------------

# 🔌 API Overview

All backend APIs are prefixed with:

``` text
/api
```

## Authentication

  Method   Endpoint              Role
  -------- --------------------- ---------------
  POST     `/auth/register`      Public
  POST     `/auth/login`         Any role
  POST     `/auth/staff-login`   Staff roles
  POST     `/auth/logout`        Authenticated
  GET      `/auth/me`            Authenticated

## Farmer

  Method   Endpoint        Role
  -------- --------------- --------
  GET      `/farmers/me`   FARMER
  PUT      `/farmers/me`   FARMER

## Admin

  Method   Endpoint                  Role
  -------- ------------------------- -------
  GET      `/admin/overview`         ADMIN
  GET      `/admin/centres`          ADMIN
  POST     `/admin/centres`          ADMIN
  PUT      `/admin/centres/{id}`     ADMIN
  GET      `/admin/operators`        ADMIN
  POST     `/admin/operators`        ADMIN
  PUT      `/admin/operators/{id}`   ADMIN
  GET      `/admin/admins`           ADMIN
  POST     `/admin/admins`           ADMIN
  GET      `/admin/analytics`        ADMIN

## Booking

  Method   Endpoint                      Role
  -------- ----------------------------- -------------------
  POST     `/bookings`                   FARMER
  GET      `/bookings/{id}`              Authenticated
  PATCH    `/bookings/{id}/reschedule`   FARMER
  PATCH    `/bookings/{id}/cancel`       FARMER / OPERATOR
  GET      `/bookings/farmer/me`         FARMER

## Queue

  Method   Endpoint                            Role
  -------- ----------------------------------- ------------------
  GET      `/queue/{centreId}`                 OPERATOR / ADMIN
  GET      `/queue/token/{token}`              Authenticated
  POST     `/queue/{centreId}/token`           OPERATOR
  POST     `/queue/token/{token}/call`         OPERATOR
  POST     `/queue/token/{token}/processing`   OPERATOR
  POST     `/queue/token/{token}/hold`         OPERATOR
  POST     `/queue/token/{token}/recall`       OPERATOR
  POST     `/queue/token/{token}/complete`     OPERATOR

## Procurement

  Method   Endpoint                             Role
  -------- ------------------------------------ ---------------
  GET      `/procurement/{id}`                  Authenticated
  GET      `/procurement/booking/{bookingId}`   Authenticated
  PATCH    `/procurement/{id}/stage`            OPERATOR
  POST     `/procurement/{id}/quality-check`    OPERATOR
  POST     `/procurement/{id}/weighment`        OPERATOR

## Payment

  Method   Endpoint                  Role
  -------- ------------------------- ---------------
  GET      `/payments/{id}`          Authenticated
  PATCH    `/payments/{id}/status`   ADMIN

## Complaints

  Method   Endpoint                                Role
  -------- --------------------------------------- ------------------
  POST     `/complaints`                           FARMER
  GET      `/complaints/{id}`                      Authenticated
  GET      `/complaints/farmer/me`                 FARMER
  GET      `/complaints/officer/{officerId}`       OPERATOR
  GET      `/complaints/all`                       ADMIN
  PATCH    `/complaints/{id}/status`               OPERATOR / ADMIN
  PATCH    `/complaints/{id}/assign/{officerId}`   ADMIN

> Some complaint endpoint paths retain the historical `officer` naming.
> The technical role remains `OPERATOR`.

## Notifications

  Method   Endpoint                        Role
  -------- ------------------------------- ---------------
  GET      `/notifications`                Authenticated
  GET      `/notifications/unread-count`   Authenticated
  PUT      `/notifications/{id}/read`      Authenticated
  PUT      `/notifications/read-all`       Authenticated

## Location & Master Data

  Method   Endpoint                                    Role
  -------- ------------------------------------------- --------
  GET      `/locations/states`                         Public
  GET      `/locations/states/{state}/districts`       Public
  GET      `/locations/districts/{district}/cities`    Public
  GET      `/master/crops`                             Public
  GET      `/master/centres/{centreId}/availability`   Public

------------------------------------------------------------------------

# 🚀 Getting Started

## Prerequisites

Install:

-   Java 21
-   PostgreSQL
-   Node.js and npm
-   Git

The exact dependency versions should be taken from the project's current
backend build file and frontend package configuration.

------------------------------------------------------------------------

## 1. Clone the repository

``` bash
git clone <YOUR_REPOSITORY_URL>
cd Techlume
```

------------------------------------------------------------------------

## 2. Configure PostgreSQL

Create a PostgreSQL database for the application.

Example:

``` sql
CREATE DATABASE backendDb;
```

Configure the database connection in the backend's Spring configuration.

> Do not commit real database passwords, JWT secrets, API keys, or other
> credentials to Git.

------------------------------------------------------------------------

## 3. Start the Backend

Navigate to:

``` bash
cd backend-sih
```

Then run the project using the build tool configured in the repository.

For a Maven-based Spring Boot project, the typical commands are:

``` bash
./mvnw spring-boot:run
```

On Windows:

``` bash
mvnw.cmd spring-boot:run
```

The backend provides the REST API and WebSocket endpoint.

------------------------------------------------------------------------

## 4. Start the Frontend

Open another terminal:

``` bash
cd frontend
```

Install dependencies:

``` bash
npm install
```

Start the development server:

``` bash
npm run dev
```

Vite will display the local development URL in the terminal.

------------------------------------------------------------------------

# ⚙️ Configuration

Backend configuration should include the PostgreSQL connection and JWT
configuration required by the Spring Boot application.

Typical configuration concepts include:

``` text
Database URL
Database username
Database password
JWT secret
JWT expiration
CORS configuration
```

Frontend configuration should point the Axios client and WebSocket
client to the running backend.

> The exact environment-variable/property names should be taken from the
> current project configuration files. Do not copy production
> credentials into the repository.

------------------------------------------------------------------------

# 🧪 Testing & Development Utilities

The repository also contains development/testing utilities visible in
the project structure, including scripts for:

-   Database checks
-   Endpoint extraction
-   End-to-end testing
-   Edit-flow testing
-   WebSocket testing
-   Backend/frontend patching during development

Examples visible in the repository include:

``` text
check_db.py
extract_endpoints.py
test_e2e.py
test_edit.py
test_ws.cjs
test.cjs
```

These are development utilities and should not automatically be treated
as production application modules.

------------------------------------------------------------------------

# 🔄 End-to-End Booking Flow

The primary application workflow is:

``` text
Farmer Registration
        ↓
Login
        ↓
Complete Farmer Profile
        ↓
Select / Discover Centre
        ↓
Select Date
        ↓
Select Crop
        ↓
Enter Expected Quantity
        ↓
Confirm Booking
        ↓
Queue / Token on Procurement Day
        ↓
Centre Officer Calls Farmer
        ↓
PROCESSING
        ↓
Procurement Quality Check
        ↓
Weighment
        ↓
Accepted Quantity
        ↓
Procurement Completed
        ↓
Payment Status Tracking
```

------------------------------------------------------------------------

# 🔁 Queue Flow

``` text
WAITING
   │
   ▼
CALLED
   │
   ▼
PROCESSING
   │
   ├──────────────► HOLD
   │                  │
   │                  ▼
   │                RECALL
   │                  │
   │                  ▼
   │              PROCESSING
   │
   ▼
COMPLETED
```

------------------------------------------------------------------------

# 🛡️ Reliability & Transactions

Business operations that update multiple records use Spring's
`@Transactional` boundary where appropriate.

This helps ensure that related database operations succeed or roll back
together.

For example:

``` text
Database Operation A
        +
Database Operation B
        +
Database Operation C
        ↓
Transactional boundary
        ↓
Commit OR Rollback
```

The current implementation still has a known capacity-concurrency
limitation described above.

------------------------------------------------------------------------

# ⏰ Scheduled Queue Cleanup

`QueueScheduler` runs periodically.

Its purpose is to clean unresolved HOLD tokens at the end of the
operational day according to the application's configured scheduler
logic.

The current documented implementation checks India Standard Time and
cancels eligible HOLD tokens near the end of the day.

------------------------------------------------------------------------

# 🌱 Data Initialization

`DataInitializer` is responsible for application-startup initialization
and database constraint adjustments used by the current implementation.

It also seeds required master/demo administrative data.

The current system does **not** seed fake farmer or Operator accounts,
allowing Admin-created operational accounts and real registration flows
to be used.

------------------------------------------------------------------------

# ⚠️ Known Limitations

The current MVP intentionally has several limitations.

### 1. Booking concurrency

The capacity check currently uses a count-before-insert approach.

A production implementation should use stronger concurrency control.

### 2. Static location data

State, district and city data are currently maintained through static
backend mappings.

### 3. No real payment gateway

The payment module tracks status internally and does not transfer money.

### 4. Complaint escalation

Automatic seven-day complaint escalation is planned but is not currently
implemented.

### 5. JWT refresh mechanism

The current authentication system uses a 24-hour JWT. Production
deployment could introduce short-lived access tokens with secure
refresh-token rotation.

### 6. WebSocket broker scalability

The current architecture uses Spring's messaging infrastructure. A
larger deployment could introduce a dedicated broker.

------------------------------------------------------------------------

# 🚀 Future Improvements

Potential production-scale improvements include:

-   Government location/GIS integration
-   Real payment/Treasury integration
-   Optimistic or pessimistic booking locking
-   Redis caching
-   Distributed rate limiting
-   Refresh-token rotation
-   Dedicated message broker
-   Horizontal WebSocket scaling
-   Advanced audit logging
-   Automated complaint escalation
-   Monitoring and observability
-   Production-grade centralized logging
-   Automated database migrations

------------------------------------------------------------------------

# 🧠 SIH Defense Points

Some important technical points to understand before presenting the
project:

### Why PostgreSQL?

Because the application contains strongly related entities such as:

``` text
Booking
QueueToken
Procurement
Payment
```

and requires relational integrity and transactional consistency.

### Why JWT?

It provides stateless authentication where the server does not maintain
traditional session state.

### Why WebSockets?

Queue state can change while a farmer is viewing the dashboard.
WebSockets allow the backend to push changes instead of requiring
continuous client polling.

### Why STOMP?

STOMP provides routing and messaging semantics over WebSockets, allowing
the application to separate queue broadcasts from user-specific
notifications.

### Why no fixed time slots?

The product is designed around a flexible daily queue model rather than
rigid time appointments.

### How is Operator access protected?

The backend verifies that the authenticated Operator owns the target
centre before allowing queue operations.

### What is the biggest current technical debt?

The main documented technical debt is the lack of robust concurrency
control around the daily booking-capacity check, along with the absence
of refresh-token authentication.

------------------------------------------------------------------------

# 🏆 SIH Backend Questions

The project contains a dedicated defense document with 30 backend
questions covering:

### Architecture

-   Spring Boot vs Node.js
-   Codebase scalability
-   Request lifecycle
-   Configuration management
-   PostgreSQL vs MongoDB

### Security

-   JWT
-   Farmer data isolation
-   Operator ownership
-   Token interception
-   Password hashing

### Booking

-   Daily capacity
-   Seven-day window
-   Concurrency
-   Cancellation
-   No time slots

### Queue / WebSocket

-   WebSockets vs polling
-   STOMP
-   Single active farmer
-   HOLD state
-   EOD cleanup

### Database / Reliability

-   Transaction rollback
-   Migration strategy
-   `@Transactional`
-   Queue indexing
-   Idempotent initialization

### SIH System Defense

-   MVP mocks
-   Government payment integration
-   Notification failures
-   Scaling to 10,000 centres
-   Technical debt

The detailed answers should be reviewed from the project's backend
handover/defense document before the SIH presentation.

------------------------------------------------------------------------

# 📂 Frontend Page Structure

The React application contains role-specific pages.

## Admin

``` text
pages/admin/
├── AdminManagementPage.jsx
└── AdminOperatorsPage.jsx
```

Additional Admin pages/components are part of the current frontend
implementation.

## Authentication

``` text
pages/auth/
├── LoginPage.jsx
├── RegisterPage.jsx
└── StaffLoginPage.jsx
```

## Centre Officer / Operator

``` text
pages/centre/
├── CentreDashboard.jsx
├── ProcurementPage.jsx
└── QueuePage.jsx
```

## Farmer

``` text
pages/farmer/
├── BookSlot.jsx
├── FarmerDashboard.jsx
├── FarmerProfile.jsx
├── FindCentres.jsx
├── MyBookings.jsx
└── MyComplaints.jsx
```

------------------------------------------------------------------------

# 🔗 Frontend API Modules

The frontend API layer is organized by domain:

``` text
src/api/
├── admin.js
├── auth.js
├── booking.js
├── centre.js
├── client.js
├── complaint.js
├── farmer.js
├── location.js
├── master.js
├── procurement.js
└── queue.js
```

This keeps API communication separated from UI components.

------------------------------------------------------------------------

# 🎨 Frontend Components

Shared UI components are organized into:

``` text
src/components/
├── common/
├── landing/
├── layout/
└── notifications/
```

This allows reusable UI patterns to be shared across role-specific
pages.

------------------------------------------------------------------------

# 📡 WebSocket Topics

  Topic                                  Purpose
  -------------------------------------- -----------------------------
  `/topic/centre/{centreId}/queue`       Centre queue updates
  `/topic/user/{userId}/notifications`   User-specific notifications

------------------------------------------------------------------------

# 🔑 Important Backend Modules

  Module             Responsibility
  ------------------ --------------------------------------------
  `auth`             Registration, login, JWT and security
  `admin`            Centre/operator/admin management
  `analytics`        Admin analytics
  `booking`          Booking and capacity
  `centre`           Procurement-centre master data
  `complaint`        Farmer complaints
  `farmer`           Farmer profiles
  `location`         Static location data
  `notification`     Persistent + real-time notifications
  `payment`          Internal payment tracking
  `procurement`      Quality and weighment
  `queue`            Queue state machine and scheduler
  `recommendation`   Centre recommendations
  `common`           Shared DTOs/utilities/WebSocket publishing

------------------------------------------------------------------------

# 🧾 Project Scope

Anna-Data-Setu should be presented as:

> **A digital coordination layer for crop procurement centres that helps
> farmers schedule visits, receive queue tokens, monitor live queue
> status, and track procurement-related progress while giving Centre
> Officers and Administrators the tools to manage operations.**

It should **not** be presented as:

-   A replacement for government procurement authorities
-   A government payment gateway
-   A banking platform
-   A guaranteed payment-processing system
-   A complete government GIS/location platform

------------------------------------------------------------------------

# 👨‍💻 Team Techlume

**Project:** Anna-Data-Setu\
**Team:** Techlume\
**Hackathon:** Smart India Hackathon 2026

The project combines:

-   Frontend engineering
-   Backend engineering
-   Database design
-   Authentication and authorization
-   Real-time communication
-   Queue management
-   Procurement workflow modelling
-   Administrative analytics
-   UI/UX design

------------------------------------------------------------------------

# ⭐ Final Note

Anna-Data-Setu is designed around a simple principle:

> **Farmers should have visibility into when they are expected, where
> they are in the queue, and what is happening with their procurement
> --- while procurement-centre staff should have a structured way to
> manage the daily flow.**

The current MVP focuses on making that coordination workflow functional,
demonstrable, and extensible while clearly separating implemented
features from future production integrations.
