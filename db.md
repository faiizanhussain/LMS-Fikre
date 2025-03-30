# Database Schema

### **1. Users & Roles**

**Users Table**  
- **user_id** (PK)  
- email  
- password_hash  
- first_name  
- last_name  
- phone_number (optional)  
- is_active  
- created_at  
- updated_at  

**User_Roles Table**  
- **role_id** (PK)  
- role_name (e.g., Student, Instructor, Admin, Moderator, Content Reviewer, Finance Manager)  
- description  

**User_Role_Map Table** (Many-to-Many, if a user can have multiple roles)  
- **user_id** (FK → Users.user_id)  
- **role_id** (FK → User_Roles.role_id)

*Alternatively, if roles are mutually exclusive, you could include a role_id directly in the Users table.*

---

### **2. Courses & Content**

**Courses Table**  
- **course_id** (PK)  
- title  
- description  
- instructor_id (FK → Users.user_id)  
- category_id (FK → Course_Categories.category_id)  
- created_at  
- updated_at  

**Course_Categories Table**  
- **category_id** (PK)  
- name  
- description  

**Course_Tags Table**  
- **tag_id** (PK)  
- tag_name  

**Course_Tag_Map Table** (Mapping many-to-many between courses and tags)  
- **course_id** (FK → Courses.course_id)  
- **tag_id** (FK → Course_Tags.tag_id)

**Lectures (Video Content) Table**  
- **lecture_id** (PK)  
- course_id (FK → Courses.course_id)  
- title  
- video_url  
- drm_info (data or reference to DRM service)  
- allow_download (Boolean flag: true for one-time payment users)  
- created_at  
- updated_at

**Resources Table**  
- **resource_id** (PK)  
- course_id (FK → Courses.course_id)  
- title  
- resource_type (e.g., PDF, Note, Attachment)  
- file_path or URL  
- allow_download (Boolean flag similar to lectures)  
- created_at  
- updated_at

---

### **3. Interaction & Community**

**Discussions Table**  
- **discussion_id** (PK)  
- course_id (FK → Courses.course_id)  
- user_id (FK → Users.user_id)  
- title  
- content  
- parent_id (Self-reference for threaded replies; nullable)  
- created_at  
- updated_at

**Notifications Table**  
- **notification_id** (PK)  
- user_id (FK → Users.user_id)  
- type (e.g., course update, discussion reply)  
- message  
- is_read (Boolean)  
- created_at  

---

### **4. Assessments & Engagement**

**Quizzes Table**  
- **quiz_id** (PK)  
- course_id (FK → Courses.course_id)  
- title  
- description  
- created_at  
- updated_at

**Quiz_Questions Table**  
- **question_id** (PK)  
- quiz_id (FK → Quizzes.quiz_id)  
- question_text  
- question_type (e.g., MCQ, True/False)  
- options (Could be JSON if variable)  
- correct_answer (Depends on question_type)  
- created_at  
- updated_at

**Polls Table**  
- **poll_id** (PK)  
- course_id (FK → Courses.course_id)  
- question  
- options (JSON array)  
- created_at  
- expires_at (nullable)

**Assignments Table**  
- **assignment_id** (PK)  
- course_id (FK → Courses.course_id)  
- title  
- description  
- due_date  
- created_at  
- updated_at

**Assignment_Submissions Table**  
- **submission_id** (PK)  
- assignment_id (FK → Assignments.assignment_id)  
- user_id (FK → Users.user_id)  
- file_path or URL  
- version  
- submitted_at  
- grade (nullable)  
- feedback (nullable)

**Certificates Table**  
- **certificate_id** (PK)  
- course_id (FK → Courses.course_id)  
- user_id (FK → Users.user_id)  
- issue_date  
- certificate_url (or reference)

**User_Course_Progress Table**  
- **progress_id** (PK)  
- user_id (FK → Users.user_id)  
- course_id (FK → Courses.course_id)  
- lecture_id (FK → Lectures.lecture_id; optional if tracking per lecture)  
- progress_percent  
- last_viewed_at

---

### **5. Monetization & Payments**

**Subscriptions Table**  
- **subscription_id** (PK)  
- user_id (FK → Users.user_id)  
- plan (e.g., Monthly, Yearly)  
- start_date  
- end_date  
- status (active, expired)

**Payments Table**  
- **payment_id** (PK)  
- user_id (FK → Users.user_id)  
- payment_type (Subscription or One-Time)  
- amount  
- status (completed, pending, failed)  
- transaction_id  
- payment_date

---

### **6. Administration & Audit**

**Admin_Logs Table**  
- **log_id** (PK)  
- admin_id (FK → Users.user_id, restricted to admin roles)  
- action (e.g., user ban, content approval)  
- description  
- created_at

**Audit_Logs Table**  
- **audit_id** (PK)  
- user_id (FK → Users.user_id)  
- action (CRUD operations)  
- table_name  
- record_id  
- old_value (JSON or text snapshot)  
- new_value (JSON or text snapshot)  
- timestamp

**Moderation_Flags Table**  
- **flag_id** (PK)  
- flagged_by (FK → Users.user_id)  
- discussion_id (FK → Discussions.discussion_id; nullable)  
- resource_id (FK → Resources.resource_id; nullable)  
- reason  
- status (pending, reviewed, dismissed)  
- created_at
