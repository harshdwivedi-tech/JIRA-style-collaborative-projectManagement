# Jira-Style Collaborative Project Management System

A **Jira-inspired collaborative project management system** built using **Java, Spring Boot, Hibernate/JPA, and MySQL**.

The application provides a structured backend for managing **users, projects, boards, tickets, comments, assignments, priorities, ticket types, and ticket statuses**. It also demonstrates important Hibernate features such as **entity relationships, optimistic locking, auditing, and entity lifecycle management**.

The project is designed to model a real-world collaborative development environment where multiple users can work on projects and manage tasks/tickets while maintaining data consistency during concurrent operations.

---

##  Project Overview

In a collaborative software development environment, multiple team members may work on the same project and modify the same ticket simultaneously.

A project management system needs to handle:

* Project organization
* Board management
* Ticket/task management
* User assignment
* Comments and discussions
* Ticket priorities and statuses
* Different ticket types
* Concurrent updates
* Data auditing and history
* Consistent database relationships

This project provides a backend architecture inspired by **Jira-style project management workflows** to address these requirements.

---

##  Key Features

### 1. Project Management

* Create and manage projects.
* Maintain project-related boards.
* Organize project work into different boards.
* Maintain relationships between projects and boards.

### 2. Board Management

* Create boards under projects.
* Associate tickets with boards.
* Organize project tasks in a structured workflow.

### 3. Ticket Management

Tickets represent individual units of work within the system.

The system supports different ticket types such as:

* Epic
* Story
* Task

Tickets can contain information such as:

* Title/summary
* Description
* Priority
* Status
* Type
* Assignee
* Project/board association
* Version information

### 4. User Management

* Manage users participating in projects.
* Assign users to tickets.
* Maintain ownership/assignment relationships.

### 5. Comments

Users can add comments to tickets to support team communication and collaboration.

The ticket-comment relationship is designed using a parent-child relationship with cascading operations.

### 6. Ticket Status Management

The system supports different ticket states through the `TicketStatus` enum.

This allows a ticket to move through a defined workflow.

### 7. Priority Management

Tickets can have different priority levels using the `Priority` enum.

This helps teams identify which tasks require greater attention.

### 8. Ticket Type Management

The `TicketType` enum provides different categories of tickets, such as:

* Epic
* Story
* Task

---

#  Technology Stack

| Technology           | Purpose                         |
| -------------------- | ------------------------------- |
| **Java 21**          | Programming language            |
| **Spring Boot**      | Backend application framework   |
| **Spring Data JPA**  | Data access layer               |
| **Hibernate**        | ORM and persistence             |
| **MySQL**            | Relational database             |
| **Maven**            | Dependency management and build |
| **JUnit 5**          | Testing                         |
| **Hibernate Envers** | Entity auditing/history         |

---

#  Architecture

The application follows a layered Spring Boot architecture.

```text
                    Client
                      │
                      ▼
              ┌───────────────┐
              │   Controller  │
              └───────┬───────┘
                      │
                      ▼
              ┌───────────────┐
              │    Service    │
              └───────┬───────┘
                      │
                      ▼
              ┌───────────────┐
              │  Repository   │
              └───────┬───────┘
                      │
                      ▼
              ┌───────────────┐
              │ Hibernate/JPA │
              └───────┬───────┘
                      │
                      ▼
              ┌───────────────┐
              │     MySQL     │
              └───────────────┘
```

### Application Layers

**Controller Layer**

Handles HTTP requests and exposes REST APIs.

Examples:

* `ProjectController`
* `BoardController`
* `TicketController`
* `CommentController`
* `UserController`

**Service Layer**

Contains the business logic.

Examples:

* `ProjectService`
* `BoardService`
* `TicketService`
* `CommentService`
* `UserService`

**Repository Layer**

Provides database access using Spring Data JPA.

Examples:

* `ProjectRepository`
* `BoardRepository`
* `TicketRepository`
* `CommentRepository`
* `UserRepository`

**Entity Layer**

Represents database entities and their relationships.

Examples:

* `Project`
* `Board`
* `Ticket`
* `Comment`
* `User`

**DTO Layer**

DTOs are used to transfer data between the API and application layers.

Examples:

* `ProjectDto`
* `BoardDto`
* `TicketDto`
* `CommentDto`
* `UserDto`

---

#  Project Structure

```text
src/main/java/com/example/Jira
│
├── JiraApplication.java
│
├── controller
│   ├── BoardController.java
│   ├── CommentController.java
│   ├── ProjectController.java
│   ├── TicketController.java
│   └── UserController.java
│
├── DTO
│   ├── BoardDto.java
│   ├── CommentDto.java
│   ├── ProjectDto.java
│   ├── TicketDto.java
│   └── UserDto.java
│
├── Entity
│   ├── Board.java
│   ├── Comment.java
│   ├── Project.java
│   ├── Ticket.java
│   └── User.java
│
├── Enum
│   ├── Priority.java
│   ├── TicketStatus.java
│   └── TicketType.java
│
├── Repository
│   ├── BoardRepository.java
│   ├── CommentRepository.java
│   ├── ProjectRepository.java
│   ├── TicketRepository.java
│   └── UserRepository.java
│
└── Service
    ├── BoardService.java
    ├── CommentService.java
    ├── ProjectService.java
    ├── TicketService.java
    └── UserService.java
```

---

#  Entity Relationships

The application uses Hibernate/JPA relationships to model the project management domain.

```text
             ┌──────────────┐
             │   Project    │
             └──────┬───────┘
                    │
                 1 : N
                    │
                    ▼
             ┌──────────────┐
             │    Board     │
             └──────┬───────┘
                    │
                 1 : N
                    │
                    ▼
             ┌──────────────┐
             │    Ticket    │
             └───┬──────┬───┘
                 │      │
              N : 1    1 : N
                 │      │
                 ▼      ▼
            ┌────────┐ ┌──────────┐
            │  User  │ │ Comment │
            └────────┘ └──────────┘
```

### Main Relationships

* **Project → Board:** `@OneToMany`
* **Board → Ticket:** `@OneToMany`
* **Ticket → User:** `@ManyToOne`
* **Ticket → Comment:** `@OneToMany`
* **Comment → Ticket:** Parent-child relationship

These relationships allow the system to represent a realistic project management hierarchy.

---

#  Hibernate Features

One of the major objectives of this project is to demonstrate practical Hibernate features used in enterprise applications.

## 1. Optimistic Locking

The application uses optimistic locking to protect ticket data from **lost update problems** when multiple users modify the same ticket.

### Problem

Consider two users:

```text
              Ticket #101
                  │
          ┌───────┴────────┐
          ▼                ▼
       User A            User B
          │                │
     Edit Summary      Edit Priority
          │                │
          └───────┬────────┘
                  ▼
          Concurrent Update
                  │
                  ▼
          Lost Update Risk
```

Without concurrency control, one user's changes may overwrite another user's changes.

### Solution

Hibernate's `@Version` mechanism can be used to maintain a version number for a ticket.

```java
@Version
private Long version;
```

The version is checked whenever the entity is updated.

If a user attempts to update an outdated version of the ticket, Hibernate detects the conflict and can throw an `OptimisticLockException`.

This prevents silent overwriting of another user's changes.

---

## 2. Hibernate Envers Auditing

Hibernate Envers can be used to maintain a historical record of entity changes.

Entities marked with:

```java
@Audited
```

can have their changes automatically tracked by Hibernate Envers.

This is useful for maintaining a history of:

* Ticket modifications
* Assignee changes
* Status changes
* Priority changes
* Other important entity updates

The audit information can be stored in corresponding audit tables, allowing previous versions of an entity to be examined.

### Simplified Flow

```text
User modifies Ticket
        │
        ▼
   Hibernate/JPA
        │
        ├──────────────► Main Ticket Table
        │
        └──────────────► Audit Table
                              │
                              ▼
                       Historical Record
```

---

## 3. Entity Lifecycle Management

JPA entity lifecycle callbacks can be used to automatically maintain audit-related information.

For example:

```text
Entity Created
      │
      ▼
 @PrePersist
      │
      ▼
createdAt / createdBy
```

And during updates:

```text
Entity Updated
      │
      ▼
 @PreUpdate
      │
      ▼
updatedAt / updatedBy
```

This reduces the need to manually update these fields throughout the application.

---

# ⚡ Concurrency Handling

A key challenge addressed by this project is **concurrent ticket editing**.

### Example

Suppose User A and User B open the same ticket.

```text
                    Ticket Version = 5
                           │
                 ┌─────────┴─────────┐
                 ▼                   ▼
              User A              User B
              Version 5            Version 5
                 │                   │
          Change Summary       Change Priority
                 │                   │
                 ▼                   ▼
              Update              Update
                 │                   │
                 ▼                   ▼
          Version becomes 6    Version mismatch
                                     │
                                     ▼
                          OptimisticLockException
```

The version field allows Hibernate to detect that User B is working with an outdated version.

This helps maintain **data consistency** in multi-user environments.

---

#  DTO-Based Data Transfer

The project separates entities from API data transfer objects using DTO classes.

Examples:

```text
Entity                         DTO
────────────────────────────────────────
Project.java          →        ProjectDto.java
Board.java            →        BoardDto.java
Ticket.java           →        TicketDto.java
Comment.java          →        CommentDto.java
User.java             →        UserDto.java
```

Using DTOs provides better separation between the persistence model and API layer and helps control which data is exposed through the API.

---

#  Getting Started

## Prerequisites

Make sure the following software is installed:

* Java 17 or later
* Maven
* MySQL
* Git

---

## 1. Clone the Repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
```

Navigate to the project:

```bash
cd JIRA-Style-Collaborative-Project-Management-System
```

---

## 2. Create the MySQL Database

Create a database in MySQL:

```sql
CREATE DATABASE jira_db;
```

---

## 3. Configure Database Connection

Update your Spring Boot database configuration with your MySQL credentials.

Example:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/jira_db
spring.datasource.username=YOUR_USERNAME
spring.datasource.password=YOUR_PASSWORD

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

> Never commit your actual database password, API keys, or other secrets to GitHub.

---

## 4. Build the Project

Using Maven:

```bash
./mvnw clean install
```

Or, if Maven is installed globally:

```bash
mvn clean install
```

---

## 5. Run the Application

```bash
./mvnw spring-boot:run
```

The application will start on the configured Spring Boot port.

By default:

```text
http://localhost:8080
```

---

#  Testing

The project uses **JUnit 5** for testing.

Tests can be executed using:

```bash
./mvnw test
```

or:

```bash
mvn test
```

---

#  Application Workflow

A typical workflow can be represented as:

```text
User
 │
 ▼
Create Project
 │
 ▼
Create Board
 │
 ▼
Create Ticket
 │
 ├── Select Ticket Type
 ├── Set Priority
 ├── Set Status
 └── Assign User
 │
 ▼
Add Comments
 │
 ▼
Update Ticket
 │
 ├── Version Check
 └── Audit Change
 │
 ▼
Updated Project Data
```

---

#  Learning Objectives

This project demonstrates practical implementation of:

* Spring Boot application development
* REST API architecture
* Layered application design
* Spring Data JPA
* Hibernate ORM
* Entity relationships
* DTO pattern
* Repository pattern
* Service layer architecture
* MySQL database integration
* Hibernate Envers auditing
* Optimistic locking
* Entity lifecycle callbacks
* Concurrent data modification handling
* JUnit 5 testing

---

#  Real-World Use Case

The architecture can be applied to applications such as:

* Software project management systems
* Issue tracking systems
* Team collaboration platforms
* Task management applications
* Internal enterprise workflow systems
* Agile/Scrum management tools

The Jira-style workflow makes the project particularly relevant to **software development teams**, where multiple users frequently work on the same projects and tickets.

---

#  Future Enhancements

Possible future improvements include:

* JWT-based authentication and authorization
* Role-based access control
* Project member management
* Advanced ticket filtering and searching
* Ticket activity timeline
* WebSocket-based real-time updates
* Email notifications
* File/attachment support
* Pagination and sorting
* Docker containerization
* API documentation using Swagger/OpenAPI
* Frontend dashboard using React
* CI/CD integration

---

#  Key Concepts Demonstrated

```text
Spring Boot
    │
    ├── REST APIs
    ├── Dependency Injection
    └── Layered Architecture
             │
             ▼
       Spring Data JPA
             │
             ▼
          Hibernate
             │
      ┌──────┼────────┐
      ▼      ▼        ▼
 Relationships  Auditing  Locking
      │          │         │
      ▼          ▼         ▼
   MySQL       Envers    @Version
```

---

#  Author

**Harsh Dwivedi**

GitHub

https://github.com/harshdwivedi-tech

---

#  If You Find This Project Useful

If this project helped you understand Spring Boot, Hibernate, JPA, database relationships, auditing, or concurrency handling, consider giving the repository a ⭐ on GitHub.
