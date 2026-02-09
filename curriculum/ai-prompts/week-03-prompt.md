# Week 3 AI Prompt: User Management CRUD

## 📋 Instructions for Students

1. Open your StartupHub API project in Cursor/Windsurf
2. Ensure Week 2 authentication is working
3. Copy the entire prompt below
4. Paste into your AI coding assistant
5. Let it generate the code
6. Review what was created with your instructor

---

## 🤖 AI PROMPT (Copy Everything Below)

```
I'm continuing my StartupHub API. Now I need complete user management endpoints with proper authorization.

BUILD THESE ENDPOINTS:

1. GET /api/users (List all users - Admin only)
   - Pagination (page, limit)
   - Filtering (role, search by email/name)
   - Sorting (createdAt, email)
   - Return: users array, total count, page info

2. GET /api/users/:id (Get single user)
   - Anyone can view
   - Don't return password or sensitive fields
   - Return 404 if user not found

3. PATCH /api/users/:id (Update user)
   - Users can update own profile
   - Admins can update anyone
   - Cannot change: id, createdAt, role (only admin can change role)
   - Validate input with Zod
   - Return updated user

4. DELETE /api/users/:id (Soft delete)
   - Users can delete own account
   - Admins can delete anyone
   - Soft delete: Add deletedAt timestamp, don't actually remove
   - Exclude deleted users from listings

FILE STRUCTURE NEEDED:
- src/controllers/user.controller.js
- src/services/user.service.js (already have repository)
- src/validations/user.validation.js
- src/routes/user.routes.js

AUTHORIZATION RULES:
- List all users: Admin only
- View single user: Anyone authenticated
- Update user: Owner or Admin
- Delete user: Owner or Admin
- Change role: Admin only

FEATURES TO IMPLEMENT:
- Pagination utility
- Search functionality (by email)
- Soft delete pattern
- Authorization checks before operations
- Proper error messages

Use repository pattern, Zod validation, and follow existing code style.
```

---

## ✅ What You Should Have After

### File Structure:
- Complete user management endpoints
- Authorization middleware integrated
- Input validation with Zod
- Soft delete implementation

### Working Features:
- List users with pagination
- Get single user profile
- Update user information
- Soft delete users
- Search by email/name
- Role-based access control

---

## 🧪 Testing Your User Management

### Step 1: Create Test Users

Create users with different roles for testing:

**Admin User**:
```bash
POST http://localhost:3000/api/auth/register
Content-Type: application/json

{
  "email": "admin@startuphub.com",
  "password": "Admin123!",
  "role": "admin"
}
```

**Founder User**:
```bash
POST http://localhost:3000/api/auth/register
Content-Type: application/json

{
  "email": "founder@startuphub.com",
  "password": "Founder123!",
  "role": "founder"
}
```

**Investor User**:
```bash
POST http://localhost:3000/api/auth/register
Content-Type: application/json

{
  "email": "investor@startuphub.com",
  "password": "Investor123!",
  "role": "investor"
}
```

### Step 2: Test List Users (Admin Only)

**Should Fail (No Auth)**:
```bash
GET http://localhost:3000/api/users
```
Expected: 401 Unauthorized

**Should Fail (Non-Admin)**:
```bash
GET http://localhost:3000/api/users
Authorization: Bearer [founder_token]
```
Expected: 403 Forbidden

**Should Succeed (Admin)**:
```bash
GET http://localhost:3000/api/users
Authorization: Bearer [admin_token]
```
Expected: 200 OK with user list and pagination

**With Pagination**:
```bash
GET http://localhost:3000/api/users?page=1&limit=10
Authorization: Bearer [admin_token]
```

**With Filtering**:
```bash
GET http://localhost:3000/api/users?role=founder
Authorization: Bearer [admin_token]
```

**With Search**:
```bash
GET http://localhost:3000/api/users?search=john
Authorization: Bearer [admin_token]
```

### Step 3: Test Get Single User

```bash
GET http://localhost:3000/api/users/[user_id]
Authorization: Bearer [any_token]
```

**Expected Response**:
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "role": "founder",
    "createdAt": "2026-02-06T10:00:00Z",
    "updatedAt": "2026-02-06T10:00:00Z"
  }
}
```

Note: Should NOT include `password` field

### Step 4: Test Update User

**Update Own Profile**:
```bash
PATCH http://localhost:3000/api/users/[own_id]
Authorization: Bearer [user_token]
Content-Type: application/json

{
  "email": "newemail@example.com"
}
```

**Try to Update Someone Else (Should Fail)**:
```bash
PATCH http://localhost:3000/api/users/[other_user_id]
Authorization: Bearer [user_token]
Content-Type: application/json

{
  "email": "hacked@example.com"
}
```
Expected: 403 Forbidden

**Admin Updates Any User**:
```bash
PATCH http://localhost:3000/api/users/[any_user_id]
Authorization: Bearer [admin_token]
Content-Type: application/json

{
  "role": "admin"
}
```

### Step 5: Test Soft Delete

**Delete Own Account**:
```bash
DELETE http://localhost:3000/api/users/[own_id]
Authorization: Bearer [user_token]
```

**Verify User is Soft Deleted**:
Check database directly - user should have `deletedAt` timestamp

**Verify Excluded from Listings**:
```bash
GET http://localhost:3000/api/users
Authorization: Bearer [admin_token]
```
Deleted user should NOT appear in list

---

## 🔍 Code Review Checklist

Go through the generated code with your instructor:

- [ ] Authorization checks are in controllers
- [ ] Password field is never returned
- [ ] Pagination includes total count and page info
- [ ] Soft delete sets deletedAt timestamp
- [ ] Deleted users are excluded from queries
- [ ] Search works across multiple fields
- [ ] Zod validation schemas are strict
- [ ] Error messages are clear and helpful
- [ ] HTTP status codes are correct (200, 400, 401, 403, 404)

---

## 🐛 Common Issues & Fixes

### Issue: "403 Forbidden" when updating own profile
**Problem**: User ID comparison failing
**Solution**: 
```javascript
// Ensure both IDs are strings
if (id.toString() !== req.user.userId.toString()) {
  // ...
}
```

### Issue: Password showing in response
**Problem**: Not sanitizing user object
**Solution**:
```javascript
const { password, ...safeUser } = user;
return safeUser;
```

### Issue: Soft deleted users still appearing
**Problem**: Forgot to filter by deletedAt
**Solution**:
```javascript
.where(isNull(usersTable.deletedAt))
```

### Issue: Pagination not working
**Problem**: Query parameters not parsed as integers
**Solution**:
```javascript
const page = parseInt(req.query.page) || 1;
const limit = parseInt(req.query.limit) || 20;
```

### Issue: Search not finding users
**Problem**: Case-sensitive search
**Solution**:
```javascript
.where(ilike(usersTable.email, `%${search}%`))
// Use ilike for case-insensitive search
```

---

## 📝 Homework

### 1. Add User Profile Fields

Extend the user schema with:
```javascript
{
  firstName: string (optional),
  lastName: string (optional),
  bio: string (max 500 chars, optional),
  phone: string (regex validated, optional),
  profilePicture: string (URL, optional)
}
```

Update:
- Database schema
- Validation schemas
- Allow updates to these fields

### 2. Implement Advanced Search

Enhance search to work across multiple fields:
```
GET /api/users?search=john
→ Searches in email, firstName, lastName
```

### 3. Create Restore Endpoint

Add ability to restore soft-deleted users (Admin only):
```
POST /api/users/:id/restore
→ Sets deletedAt to null
```

### 4. Add Change Password Endpoint

```
PATCH /api/users/me/password
Body: {
  currentPassword: string,
  newPassword: string
}
```

Requirements:
- Verify current password
- Hash new password
- User can only change own password

### 5. Track Last Login

- Add `lastLoginAt` field to user schema
- Update on successful login
- Display in user profile

---

## 🎯 Learning Objectives Check

After completing this week, you should understand:

- ✅ CRUD operations (Create, Read, Update, Delete)
- ✅ REST conventions for HTTP methods
- ✅ Authorization vs Authentication
- ✅ Owner-based access control
- ✅ Soft delete vs hard delete
- ✅ Pagination implementation
- ✅ Database filtering and searching
- ✅ Input validation with Zod
- ✅ Sensitive data protection

---

## 📚 Additional Reading (Optional)

- [REST API Best Practices](https://restfulapi.net/)
- [Drizzle ORM Queries](https://orm.drizzle.team/docs/select)
- [Zod Validation](https://zod.dev/)
- [Soft Delete Pattern](https://en.wikipedia.org/wiki/Soft_deletion)

---

## 🚀 Next Week Preview

**Topic:** Startup Listings (CRUD Deep Dive)

**What We'll Build:**
- Complete startup CRUD system
- One-to-many relationships (User → Startups)
- Nested resource routes
- Advanced filtering by multiple criteria

**Prep Work:**
- Think about how one user can own multiple startups
- Review foreign key concepts
- Understand JOIN operations in SQL

---

## 💬 Questions for Discussion

1. When would you use hard delete vs soft delete?
2. Why check authorization in the controller layer?
3. How do you handle pagination for very large datasets?
4. What's the security risk of returning all user data?
5. How would you implement "restore deleted user" functionality?

**Be ready to discuss these in the next session!**
