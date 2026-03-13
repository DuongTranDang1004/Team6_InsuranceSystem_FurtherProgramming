# 🛡️ Insurance Management System

> **RMIT University — COSC2440 Further Programming | Assignment 2**  
> A robust desktop application for managing insurance company operations, replacing traditional paperwork with real-time CRUD functionality and role-based access control.

[![Java](https://img.shields.io/badge/Java-17%2B-orange?style=flat-square&logo=java)](https://www.java.com/)
[![JavaFX](https://img.shields.io/badge/JavaFX-GUI-blue?style=flat-square)](https://openjfx.io/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-336791?style=flat-square&logo=postgresql)](https://www.postgresql.org/)
[![Hibernate](https://img.shields.io/badge/Hibernate-ORM-59666C?style=flat-square&logo=hibernate)](https://hibernate.org/)
[![Supabase](https://img.shields.io/badge/Supabase-Server-3ECF8E?style=flat-square&logo=supabase)](https://supabase.com/)
[![License](https://img.shields.io/badge/License-Academic-lightgrey?style=flat-square)]()

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Team](#-team)
- [Features](#-features)
- [Architecture & Design](#-architecture--design)
- [Design Patterns](#-design-patterns)
- [Tech Stack](#-tech-stack)
- [Getting Started](#-getting-started)
- [Demo Credentials](#-demo-credentials)
- [Project Structure](#-project-structure)
- [Individual Contributions](#-individual-contributions)
- [Reflections](#-reflections)
- [Links](#-links)

---

## 🔍 Overview

The **Insurance Management System** is a full-featured JavaFX desktop application built for insurance company operations. It supports real-time CRUD operations that execute immediately upon user interaction, with data persisted to an online PostgreSQL server hosted on Supabase.

The system enforces **role-based access control**, meaning each user type sees a personalised dashboard with tables and actions appropriate to their role — ranging from Policy Holder dashboards to System Administrator control panels.

---

## 👥 Team

| # | Name | Student ID | Role |
|---|------|------------|------|
| 1 | **Tran Dang Duong** | S3979381 | Project Manager |
| 2 | Luong Thanh Trung | S3967981 | Technical Leader |
| 3 | Cao Huy Tri | S3751366 | Frontend Developer |

**Practical Class:** Minh Vu Thanh — Thursday 12:30, SGS

---

## ✨ Features

### Role-Based Dashboards

Each user role gets a personalised dashboard with a profile information panel and role-specific data tables:

| User Role | Dashboard Capabilities |
|-----------|----------------------|
| **System Administrator** | Full access to all tables: claims, policy owners, policy holders, dependants, insurance surveyors, and managers. Can add, update, remove any record. |
| **Insurance Manager** | Assign claims to surveyors, approve/reject claims, set claim amounts, view assigned surveyors. |
| **Insurance Surveyor** | Update claim statuses to "Need More Info" or "Processing". |
| **Policy Owner** | Manage dependants and policy holders, submit claims, view insurance history. |
| **Policy Holder** | View and update own claims, manage dependants, submit claims. |
| **Dependant** | Read-only access to the claims table. |

### Claims Management
- Full CRUD support with role-gated permissions
- Status lifecycle: `New → Processing → Need More Info → Approved / Rejected`
- Document download functionality
- Insurance Manager approval workflow with claim amount setting

### Data & Security
- Automatic user session timeout after **15 minutes** of inactivity
- Forced dashboard refresh every **2 minutes** to maintain data consistency
- Input validation on all creation and update forms with meaningful error messages
- Role-based data isolation: users can only read/write data relevant to their scope

---

## 🏗️ Architecture & Design

### Module Overview

The project is organised into five well-separated modules following **high cohesion / low coupling** principles:

```
src/
├── entity/          # Domain models — User hierarchy, Claims, Policies
├── interfaces/      # DAO layer — CRUD operation contracts per role
├── controller/
│   ├── tablefilling/     # Data population for dashboard tables
│   ├── dashboard/        # Event handling and lifecycle for each role's dashboard
│   └── creationupdate/   # Shared controller for Create and Update FXML pages
├── threads/
│   ├── TableFillingThread        # Concurrent table population
│   └── UserActivityHandler      # Inactivity timer and session enforcement
└── utility/         # StageBuilder (page transitions), IDGenerator (auto IDs)
```

### OOP Design Principles

#### High Cohesion, Low Coupling
- Each module handles one specific concern (entity logic, CRUD contracts, view control)
- `protected` access modifiers localise method and attribute accessibility
- No "God class" anti-pattern — responsibilities are strictly separated

#### SOLID Principles Applied
- **Single Responsibility**: Every class has one well-defined purpose, simplifying unit testing and debugging
- **Interface Segregation**: Fine-grained interfaces (e.g., `CustomerRead`, `CustomerCreateAndDelete`) rather than broad monolithic ones
- **Dependency Inversion**: Collections use abstract types (`Collection<Entity>`, `List<Entity>`) rather than concrete implementations, enabling flexible optimisation

### UML Class Diagram
📐 [View Full UML Class Diagram](https://drive.google.com/file/d/1mQWwTVWmHsXzSd6kA0Rh6CAYXQ-phDOO/view?usp=sharing)

---

## 🎨 Design Patterns

### 1. MVC (Model-View-Controller)
| Layer | Implementation |
|-------|---------------|
| **Model** | Entity classes — define domain data and user privilege logic |
| **View** | JavaFX FXML controllers — render and bind UI components |
| **Controller** | Interface layer — encapsulates all database interaction logic |

- `DashboardController` extends a chain of `TableFilling` classes, using hierarchical inheritance to isolate table-specific responsibilities
- `CreationAndUpdatePageController` uses a single FXML template for both create and update operations
- ORM via **JPA over Hibernate** to avoid future implementation conflicts

### 2. DAO (Data Access Object)
- Entity classes never interact directly with the database
- Interfaces act as intermediaries, enforcing separation between domain models and persistence logic
- Prevents accidental corruption of server-mapped tables

### 3. Observable Pattern
For each table-filling class, data flows through three stages:
```
Raw DB Query → ObservableList → FilteredList (text field triggers) → SortedList (custom comparators)
```
This enables **simultaneous multi-condition filtering and sorting** — e.g., filter by date range AND sort by claim amount descending.

### 4. Comparator Pattern
Custom `Comparator` classes implement specific sorting logic for claims and users, allowing the `SortedList` to handle complex, domain-specific ordering criteria.

### 5. Singleton Pattern
Applied to two globally shared objects:
- **`EntityManager`** — manages JPA sessions for database access
- **`UserActivityHandler`** — manages session timer threads

Both use `static` instance attributes with `public` access points and lazy initialisation to minimise resource overhead and enforce thread safety.

---

## 🛠️ Tech Stack

| Technology | Purpose |
|-----------|---------|
| **Java 17+** | Core application language |
| **JavaFX** | Desktop GUI framework |
| **PostgreSQL** | Relational database |
| **Hibernate / JPA** | ORM for database interaction |
| **Supabase** | Cloud database hosting |
| **Maven** | Build and dependency management |
| **Java Multithreading** | Concurrent table population, session management |
| **Java Serialisation** | Local user action history persistence |

---

## 🚀 Getting Started

### Prerequisites
- Java 17 or above installed
- No additional setup required for the pre-built JAR

### Run the Application
```bash
# Simply double-click or run via terminal:
java -jar InsuranceManagementApp.jar
```

### Build from Source
```bash
git clone https://github.com/DuongTranDang1004/Team6_InsuranceSystem_FurtherProgramming.git
cd Team6_InsuranceSystem_FurtherProgramming
mvn clean install
java -jar target/InsuranceManagementApp.jar
```

---

## 🔐 Demo Credentials

Use these accounts to explore each user role's dashboard:

| Role | ID | Email | Password |
|------|----|-------|----------|
| System Admin | `SA90987611` | TrungLuong1999@gmail.com | `1234567890` |
| Insurance Surveyor | `IS206473646` | tlaundon13@cloudflare.com | `915913g/qyC` |
| Insurance Manager | `IM17150987` | thanhTrung@gmail.com | `1234567890` |
| Policy Owner | `PO6173753721` | shirlene1004@gmail.com | `88760260` |
| Policy Holder | `PH0000000001` | Tri1998@gmail.com | `1234567890` |
| Dependant | `DE0000000001` | caohuytri2010@gmail.com | `6543218899` |

---

## 📁 Project Structure

```
Team6_InsuranceSystem_FurtherProgramming/
│
├── src/main/java/
│   ├── entity/
│   │   ├── User.java                    # Base user class
│   │   ├── SystemAdmin.java
│   │   ├── InsuranceManager.java
│   │   ├── InsuranceSurveyor.java
│   │   ├── PolicyOwner.java
│   │   ├── PolicyHolder.java
│   │   ├── Dependant.java
│   │   └── Claim.java
│   │
│   ├── interfaces/
│   │   ├── ReadClaimDatabase.java
│   │   ├── CustomerRead.java
│   │   ├── CustomerCreateAndDelete.java
│   │   └── ...
│   │
│   ├── controller/
│   │   ├── tablefilling/
│   │   │   ├── HistoryActionTableFillingClass.java
│   │   │   ├── ClaimTableFillingClass.java
│   │   │   └── InsuranceManagerTableFillingClass.java
│   │   ├── dashboard/
│   │   │   └── [Role]DashboardController.java
│   │   └── creationupdate/
│   │       └── CreationAndUpdatePageController.java
│   │
│   ├── threads/
│   │   ├── TableFillingThread.java
│   │   └── UserActivityHandlerClass.java
│   │
│   └── utility/
│       ├── StageBuilder.java
│       └── IDGenerator.java
│
├── src/main/resources/fxml/            # JavaFX UI layouts
├── InsuranceManagementApp.jar           # Executable JAR
└── pom.xml                              # Maven config
```

---

## 👤 Individual Contributions — Tran Dang Duong (S3979381)

**Role:** Project Manager  
**GitHub:** [github.com/DuongTranDang1004/Team6_InsuranceSystem_FurtherProgramming](https://github.com/DuongTranDang1004/Team6_InsuranceSystem_FurtherProgramming)

### Technical Contributions

**OOP & Software Design**
- Designed the full UML Class Diagram, establishing the module structure and class hierarchy for the entire team
- Applied **SOLID principles** and **high cohesion / low coupling** throughout OOP class design
- Implemented edge-case handling — e.g., preventing unauthorised cross-role read/write on insurance claims data
- Enforced role-based data scoping so users can only access records relevant to their privilege level

**Design Patterns**
- Implemented **MVC** architecture separating view, controller, and model concerns cleanly
- Applied **DAO pattern** via interface intermediaries to decouple Entity classes from direct DB access
- Used **Observable pattern** to build reactive table filtering and sorting pipelines (ObservableList → FilteredList → SortedList)
- Integrated **Comparator pattern** for multi-criteria, configurable data ordering
- Implemented **Singleton pattern** on `EntityManager` and `UserActivityHandler` for thread-safe global state

**Database & Persistence**
- Used **PostgreSQL** with Java ORM (JPA/Hibernate), writing SQL queries for targeted data retrieval
- Ensured **ACID-compliant transaction management** for reliable, consistent data operations
- Designed local history storage via Java Serialisation — persists user action history as files keyed by user ID

**Concurrency**
- Applied **Java multithreading** to populate multiple dashboard tables concurrently, reducing initialisation time
- Ensured thread safety by assigning each thread to independent resources (no shared mutable state)
- Implemented `UserActivityHandlerClass` timer threads using boolean flags for safe concurrency control
- Auto-logout after 15 minutes of inactivity; button lock after 2 minutes without refresh

**Feature Development**
- Developed the `TableFilling` module and all table-filling logic for the dashboard
- Built input validators for login page and create/update forms, with descriptive error messaging
- Implemented `UserActivityHandlerClass` — security and consistency enforcement via timer threads
- Developed event handlers for page transitioning buttons using `StageBuilder`
- Implemented calculations for total successful claim amounts and total yearly insurance rates

---

## 📖 Reflections

### Successes
- Effective application of design patterns and SOLID principles significantly improved code maintainability and scalability
- Concurrent threading reduced dashboard load times noticeably for data-heavy admin views
- Local file storage for user history reduced database costs by keeping non-critical data off the server

### Challenges
- Initial project setup (JavaFX + Maven + Supabase) caused a ~1 week delay due to miscommunication on environment configuration — reinforcing the value of thorough onboarding

### Future Improvements
- Centralised history storage so users can access their action log from any device
- Real-time dashboard updates without manual refresh
- Responsive UI for smaller screen sizes
- Document merging / appending to existing files
- Encrypted password storage (currently stored in plaintext — a known security gap)

---

## 🔗 Links

| Resource | Link |
|----------|------|
| 📁 GitHub Repository | [Team6_InsuranceSystem_FurtherProgramming](https://github.com/DuongTranDang1004/Team6_InsuranceSystem_FurtherProgramming) |
| 📐 UML Class Diagram | [Google Drive](https://drive.google.com/file/d/1mQWwTVWmHsXzSd6kA0Rh6CAYXQ-phDOO/view?usp=sharing) |
| 🎥 Video Demo | [YouTube](https://www.youtube.com/watch?v=ceWJRz4J9Dk) |
| 🗄️ Database Server | [Supabase Dashboard](https://supabase.com/dashboard/project/lfmakwarfqeintgjcseo) |

---

<div align="center">

**RMIT University · COSC2440 Further Programming · Team 6**

*Built with Java, JavaFX, PostgreSQL, and a healthy respect for SOLID principles.*

</div>
