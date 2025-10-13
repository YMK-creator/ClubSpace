# Class Diagram
<img width="771" height="567" alt="uml_trpo" src="https://github.com/user-attachments/assets/e0546677-56b0-4b9f-9def-2879a0f266f2" />

---

# Class Diagram Description

This document describes the **class diagram** illustrating the core entities and their relationships within the application.
The diagram focuses on **user interactions** with posts and **public content**.

---

##  Main Entities

### **User**

Represents an authenticated individual within the system.
Users are central to content creation and management.

### **Post**

Represents content created by a user — such as a blog post, article, or other user-generated content that requires authentication to manage.

### **Public**

Represents general public access or content that is openly available without authentication.

### **AuthToken**

Represents an active, authenticated session for a user.

### **PublicPost**

Represents content that is publicly accessible, potentially derived from or related to a `Post`.

---

## Key Relationships

| Relationship            | Type                      | Description                                                                                                                                             |
| ----------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **User → AuthToken**    | One-to-Many               | A single User can have multiple active AuthToken sessions (e.g., logged in from different devices). Each AuthToken is associated with exactly one User. |
| **User → Post**         | One-to-Many               | A User can create and own many Post objects. Each Post is created and owned by a single User.                                                           |
| **User → Public**       | Implied                   | While not a direct data ownership relationship, a User (as an authenticated entity) can interact with Public content or make their content public.      |
| **Post → PublicPost**   | One-to-One or One-to-Many | A Post may have a corresponding PublicPost for public viewing. It could be a 1:1 transformation or 1:N (e.g., multiple versions).                       |
| **Public → PublicPost** | Grouping                  | Public acts as a conceptual container or access point for PublicPost entities, indicating they are visible to unauthenticated users.                    |

---

## Attributes (Proposed)

### **User**

| Field         | Type        | Description            |
| ------------- | ----------- | ---------------------- |
| id            | Primary Key | Unique user identifier |
| username      | String      | User’s name            |
| email         | String      | User’s email address   |
| password_hash | String      | Encrypted password     |
| created_at    | Timestamp   | Creation date          |

### **AuthToken**

| Field      | Type               | Description                       |
| ---------- | ------------------ | --------------------------------- |
| id         | Primary Key        | Unique token ID                   |
| token      | String (Unique)    | Authentication session identifier |
| user_id    | Foreign Key (User) | Linked user                       |
| expires_at | Timestamp          | Expiration date                   |

### **Post**

| Field      | Type               | Description                          |
| ---------- | ------------------ | ------------------------------------ |
| id         | Primary Key        | Unique post ID                       |
| title      | String             | Post title                           |
| content    | Text               | Main body of the post                |
| artist_id  | Foreign Key (User) | Post owner                           |
| likes      | Integer            | Show how much post have likes        |
| comment    | String             | Comments by other users              |

### **PublicPost**

| Field          | Type                 | Description                   |
| -------------- | ---------------------| ----------------------------- |
| id             | Primary Key          | Unique public post ID         |
| post_id        | Foreign Key (Post)   | Linked private/original post  |
| public_id      | Foreign Key (Public) | Sanitized or modified content |

---

## Model Ensures

* **User Ownership:** All post content is directly linked to a specific User.
* **Session Management:** `AuthToken` ensures authentication and session validity.
* **Public/Private Separation:** The distinction between `Post`, `PublicPost`, and `Public` provides granular control over visibility for authenticated and unauthenticated users.

---
