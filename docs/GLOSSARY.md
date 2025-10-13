# Glossary of Key Terms

### **User (Account)**

An authenticated individual within the system.
Each User has a unique identity and owns their created content (Posts). The User is associated with authentication credentials and session tokens.

---

### **AuthToken**

A secure identifier representing an active authenticated session.
Each token belongs to one User and defines session validity (e.g., expiration time). Typically implemented as a unique, randomly generated string.

---

### **Authentication**

The process of verifying a User’s identity, usually through login credentials (e.g., username and password).
Successful authentication issues an `AuthToken`, enabling the User to perform authorized actions.

---

### **Authorization**

The process of determining what an authenticated User is permitted to do within the system — such as creating, editing, or publishing Posts.
Ensures that only resource owners (or privileged users) can modify or publish specific content.

---

### **Post**

A piece of content created and owned by a User.
Represents private or draft material, such as an article, blog post, or note.
Posts can be later transformed into **PublicPosts** if made publicly accessible.

---

### **Public**

A conceptual entity representing open, unauthenticated access.
Acts as a container or entry point for `PublicPost` objects, defining how general users (non-logged-in visitors) can access content.

---

### **Session**

An active, time-limited connection between a User and the system, represented by an `AuthToken`.
Sessions ensure that authenticated actions remain secure and traceable.

---

### **Visibility**

The state determining whether a Post is private (accessible only to its owner) or public (available via a PublicPost).
Controlled through the `is_public` flag in the Post entity.

---

### **Access Control**

A set of rules that restrict or permit operations based on the User’s authentication and authorization state.
Ensures private data and posts remain inaccessible to the public.

---

### **Publishing**

The process of converting a User’s Post into a PublicPost.
Involves validation, content filtering, and linking between the original Post and its public version.

---

### **Content Management**

The overall mechanism through which Users create, update, and control their Posts.
Forms the foundation of the User–Post–PublicPost relationship model.

---
