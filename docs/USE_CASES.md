
#  Club Space — Use Case Analysis

## Actors

| Actor                            | Description                                                                                                            |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Unauthenticated User (Guest)** | A user who has not logged in. Can only access the login and registration pages, as well as public content.             |
| **Authenticated User**           | A registered user who has successfully logged into the system. This is the primary actor for all core functionalities. |

---

## Use Case Scenarios

---

### **UC1: Register New Account**

**Actor:** Unauthenticated User
**Precondition:** The user is not logged in and is on the application's landing or login page.

**Flow of Events:**

1. The user clicks on **"Register"**.
2. The frontend displays a registration form with fields for **Username**, **Email**, **Password**, and **Confirm Password**.
3. The user fills the form and submits it.
4. Client-side validation checks data format and password confirmation.
5. The frontend sends a `POST /api/v1/users/register` request to the server.
6. The **User Service**:

   * Validates uniqueness of username and email.
   * Hashes the password (e.g., bcrypt).
   * Creates a new `User` record in the database.
   * Optionally issues an initial `AuthToken` for auto-login.
7. The server returns **201 Created** with user data or `AuthToken`.
8. The frontend shows a success message and redirects accordingly.

**Alternative Flow (A): Validation fails**

* The server returns a **4xx error** (e.g., `409 Conflict`, `400 Bad Request`).
* The frontend displays an inline error message.
* The user corrects the form and retries.

**Postcondition:** A new `User` account is created in the system.

---

### **UC2: Login**

**Actor:** Unauthenticated User
**Precondition:** The user is on the login page.

**Flow of Events:**

1. The user enters **Username** and **Password**.
2. The frontend sends a `POST /api/v1/auth/login` request to the **Auth Service**.
3. The Auth Service validates credentials and hashes.
4. If valid:

   * Creates an `AuthToken` (JWT).
   * Stores it (cookie or response body).
5. The frontend redirects to the Dashboard and sets session data.

**Alternative Flow (A): Invalid credentials**

* Server returns `401 Unauthorized`.
* Frontend displays "Invalid credentials" message.

**Postcondition:** A valid session is established (`AuthToken` active).

---

### **UC3: Upload Post in Public**

**Actor:** Authenticated User
**Precondition:** The user is logged in and on the post creation page.

**Flow of Events:**

1. User clicks **"Create Post"**.
2. Enters **Title** and **Content**.
3. Clicks **"Publish"**.
4. Frontend validates input.
5. Sends `POST /api/v1/posts` to the **Post Service** with JWT.
6. The service:

   * Validates JWT and user ownership.
   * Creates a `Post` (private) record.
   * Generates a linked `PublicPost` (derived public content).
7. Returns **201 Created** with post data.
8. Frontend displays “Post is Public!” and redirects.

**Alternative Flow (A): Invalid data**

* Server returns `400 Bad Request`, frontend shows errors.

**Alternative Flow (B): Unauthorized**

* Server returns `401/403`, user redirected to login.

**Postcondition:** `Post` and `PublicPost` are created and linked.

---

### **UC4: Add Post to Profile**

**Actor:** Authenticated User
**Precondition:** User is logged in and has private posts.

**Flow of Events:**

1. User opens **Profile Page**.
2. Clicks **"Add Existing Post"**.
3. System shows a list of private posts.
4. User selects one and confirms.
5. Frontend sends `PUT /api/v1/profile/posts/{post_id}` with JWT.
6. Server verifies ownership and updates post visibility.
7. Returns `200 OK`; UI updates.

**Alternative Flows:**

* (A) User cancels → No changes.
* (B) User doesn’t own post → `403 Forbidden`.
* (C) Post already visible → `409 Conflict` or `200 OK`.

**Postcondition:** Post is now visible in the user’s profile feed.

---

### **UC5: Publish Post (as PublicPost)**

**Actor:** Authenticated User
**Precondition:** User is logged in and viewing a private post.

**Flow of Events:**

1. User clicks **"Publish Publicly"**.
2. Confirms or adjusts public display options.
3. Frontend sends `POST /api/v1/posts/{post_id}/publish` with JWT.
4. The **Post Service**:

   * Validates ownership.
   * Creates or updates a `PublicPost`.
5. Returns `200 OK` or `201 Created`.
6. Frontend shows “Post successfully published!” and updates UI.

**Alternative Flow (A): Unauthorized publishing**

* Server returns `403 Forbidden`.

**Postcondition:** `PublicPost` is created/updated and available to guests.

---

### **UC6: View Public Posts**

**Actor:** Unauthenticated User, Authenticated User
**Precondition:** User is on public feed page.

**Flow of Events:**

1. Frontend sends `GET /api/v1/public-posts` (no JWT required).
2. Server retrieves all `PublicPost` records.
3. Returns `200 OK` with JSON list.
4. Frontend displays posts as cards.
5. User can click a card to view details (`GET /api/v1/public-posts/{id}`).

**Alternative Flows:**

* (A) No public posts → empty list, “No public posts yet.”
* (B) Post not found → `404 Not Found`.

**Postcondition:** Public posts are retrieved and displayed.

---

### **UC7: Like a Post**

**Actor:** Authenticated User
**Precondition:** User is logged in and viewing a post.

**Flow of Events:**

1. User clicks **"Like"**.
2. Frontend sends `POST /api/v1/posts/{id}/like` with JWT.
3. Server validates JWT, checks ownership, and updates `likes`.
4. Returns `200 OK` with new count.
5. Frontend updates like icon and count.

**Alternative Flows:**

* (A) Already liked → `409 Conflict`.
* (B) Post not found → `404 Not Found`.

**Postcondition:** Like count increases, user-like recorded.

---

### **UC8: Add Comment to Post**

**Actor:** Authenticated User
**Precondition:** User is logged in and viewing a post.

**Flow of Events:**

1. User enters text in **comment field**.
2. Clicks **"Submit Comment"**.
3. Frontend sends `POST /api/v1/posts/{id}/comments` with JWT.
4. Server validates and stores comment.
5. Returns `201 Created` with comment data.
6. Frontend updates comments dynamically.

**Alternative Flows:**

* (A) Invalid comment → `400 Bad Request`.
* (B) Unauthorized → `401/403`.

**Postcondition:** Comment added and linked to the user and post.

---

### **UC9: Logout**

**Actor:** Authenticated User
**Precondition:** User is logged in.

**Flow of Events:**

1. User clicks **"Logout"**.
2. Frontend sends `POST /api/v1/auth/logout` with JWT.
3. Server invalidates `AuthToken` and clears cookies.
4. Returns `200 OK` or `204 No Content`.
5. Frontend clears session data and redirects to login page.

**Alternative Flow (A): Already logged out**

* Server returns `401 Unauthorized`.
* Frontend still clears session data.

**Postcondition:** Session terminated; user becomes Unauthenticated.
