# Week 2 AI Prompt: User Authentication System

## 🤖 AI PROMPT (Copy Everything Below)

```
I'm building on my StartupHub API from last week. Now I need to implement a complete user authentication system with registration, login, and JWT-based authentication.

CONTEXT:
- Using Express.js
- Database: PostgreSQL with Drizzle ORM
- Password hashing: bcrypt
- Tokens: JWT (jsonwebtoken)
- Validation: Zod
- Following repository pattern

ADD THE FOLLOWING TO MY PROJECT:

1. DATABASE SCHEMA (drizzle/schema/users.schema.js):
- Create users table with:
  - id: UUID primary key (auto-generated)
  - email: varchar(255), unique, not null
  - password: varchar(255), not null (will store hash)
  - role: enum('founder', 'investor', 'admin'), default 'founder'
  - createdAt: timestamp, default now
  - updatedAt: timestamp, default now
- Export the schema

2. DATABASE CONFIG (src/config/db.js):
- Configure Drizzle ORM connection
- Use connection string from environment variable DATABASE_URL
- Handle connection errors gracefully
- Export db instance

3. UPDATE .env and .env.example:
Add these variables:
```
DATABASE_URL=postgresql://localhost:5432/startuphub
JWT_SECRET=your-super-secret-jwt-key-change-this-in-production
JWT_EXPIRES_IN=24h
```

4. USER REPOSITORY (src/repositories/user.repository.js):
Create a UserRepository class with methods:
- findByEmail(email): Find user by email
- findById(id): Find user by ID
- create(userData): Create new user
- update(id, userData): Update user
- All methods should use Drizzle ORM
- Export a singleton instance

5. AUTH SERVICE (src/services/auth.service.js):
Create AuthService class with methods:

- register(email, password, role):
  * Validate email format
  * Check if email already exists (throw error if exists)
  * Hash password with bcrypt (10 rounds)
  * Create user via repository
  * Generate JWT token
  * Return { user: sanitizedUser, token }

- login(email, password):
  * Find user by email
  * If not found, throw "Invalid credentials" error
  * Compare password with bcrypt
  * If wrong, throw "Invalid credentials" error
  * Generate JWT token
  * Return { user: sanitizedUser, token }

- verifyToken(token):
  * Verify JWT token
  * Return decoded payload

- sanitizeUser(user):
  * Remove password from user object
  * Return safe user data

Helper: generateToken(user) - creates JWT with userId, email, role
Export singleton instance

6. AUTH CONTROLLER (src/controllers/auth.controller.js):
Create AuthController class with methods:

- register(req, res, next):
  * Extract email, password, role from req.body
  * Validate with Zod schema
  * Call authService.register()
  * Return 201 with user and token
  * Handle errors (send appropriate status codes)

- login(req, res, next):
  * Extract email, password from req.body
  * Validate with Zod schema
  * Call authService.login()
  * Return 200 with user and token
  * Handle errors

- getMe(req, res, next):
  * Get userId from req.user (set by auth middleware)
  * Fetch user from repository
  * Return user data
  * This is a protected route

Export controller instance

7. VALIDATION SCHEMAS (src/validations/auth.validation.js):
Using Zod, create schemas for:

- registerSchema:
  * email: valid email format, required
  * password: min 8 characters, required
  * role: enum ['founder', 'investor', 'admin'], optional

- loginSchema:
  * email: valid email, required
  * password: required

Export both schemas

8. JWT UTILITY (src/utils/jwt.util.js):
Helper functions:
- generateToken(payload, expiresIn): Creates JWT
- verifyToken(token): Verifies and decodes JWT
- extractTokenFromHeader(authHeader): Extracts token from "Bearer token"
Export all functions

9. AUTH MIDDLEWARE (src/middleware/auth.middleware.js):
Create two middleware functions:

- authenticate(req, res, next):
  * Extract token from Authorization header
  * Verify token with JWT
  * If invalid, return 401 error
  * If valid, attach decoded user to req.user
  * Call next()

- authorize(...roles):
  * Returns a middleware function
  * Checks if req.user.role is in allowed roles
  * If not, return 403 Forbidden
  * If yes, call next()

Export both functions

10. AUTH ROUTES (src/routes/auth.routes.js):
Define routes:
- POST /register - authController.register
- POST /login - authController.login
- GET /me - authController.getMe (protected with authenticate middleware)

Export router

11. UPDATE src/routes/index.js:
Import and register auth routes at /api/auth

12. ERROR HANDLING (src/middleware/error.middleware.js):
Create global error handler:
- Handle Zod validation errors (400)
- Handle JWT errors (401)
- Handle duplicate email errors (409)
- Handle generic errors (500)
- Return JSON with error message

13. UPDATE src/server.js:
- Import error handling middleware
- Register it AFTER all routes
- Add it as: app.use(errorHandler)

REQUIREMENTS:
- Use async/await consistently
- All passwords must be hashed with bcrypt (10 rounds)
- JWT secret MUST be from environment variable
- Never return password in any response
- Use proper HTTP status codes (201 for created, 401 for unauthorized, 403 for forbidden, 409 for conflict)
- Validate all inputs with Zod
- Follow repository pattern strictly
- Add helpful comments
- Handle all errors gracefully
- Use try-catch in async functions

SECURITY CONSIDERATIONS:
- Never log passwords
- Use same error message for "user not found" and "wrong password"
- Token should expire (24h default)
- Validate email format strictly
- Password minimum 8 characters

Please generate all files with production-ready code following best practices for authentication and security.
```

---

## ✅ What You Should Have After Running This Prompt

### New Files Created:
```
drizzle/
  schema/
    users.schema.js
src/
  config/
    db.js
  controllers/
    auth.controller.js
  services/
    auth.service.js
  repositories/
    user.repository.js
  middleware/
    auth.middleware.js
    error.middleware.js
  validations/
    auth.validation.js
  utils/
    jwt.util.js
  routes/
    auth.routes.js
```

### Updated Files:
- `.env` (with new variables)
- `.env.example`
- `src/routes/index.js`
- `src/server.js`

---

## 🧪 Testing Your Authentication

### Step 1: Set Up Database
```bash
# Install Drizzle Kit
npm install -D drizzle-kit

# Generate migration
npx drizzle-kit generate:pg

# Run migration
npx drizzle-kit push:pg
```

### Step 2: Update .env
```env
DATABASE_URL=postgresql://username:password@localhost:5432/startuphub
JWT_SECRET=change-this-to-random-string-in-production
JWT_EXPIRES_IN=24h
```

### Step 3: Start Server
```bash
npm run dev
```

---

## 🧪 API Testing

### Test 1: Register a User
```bash
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "founder@test.com",
    "password": "password123",
    "role": "founder"
  }'
```

**Expected Response (201):**
```json
{
  "user": {
    "id": "uuid-here",
    "email": "founder@test.com",
    "role": "founder",
    "createdAt": "2026-02-06T10:30:00.000Z"
  },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### Test 2: Login
```bash
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "founder@test.com",
    "password": "password123"
  }'
```

**Expected Response (200):**
```json
{
  "user": { ... },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### Test 3: Get Current User (Protected Route)
```bash
# Replace [TOKEN] with your actual token
curl http://localhost:3000/api/auth/me \
  -H "Authorization: Bearer [TOKEN]"
```

**Expected Response (200):**
```json
{
  "id": "uuid-here",
  "email": "founder@test.com",
  "role": "founder",
  "createdAt": "2026-02-06T10:30:00.000Z"
}
```

### Test 4: Invalid Token
```bash
curl http://localhost:3000/api/auth/me \
  -H "Authorization: Bearer invalid-token"
```

**Expected Response (401):**
```json
{
  "error": "Invalid token"
}
```

### Test 5: Duplicate Email
```bash
# Try registering with same email again
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "founder@test.com",
    "password": "password123"
  }'
```

**Expected Response (409):**
```json
{
  "error": "Email already in use"
}
```

---

## 🔍 Code Review Checklist

- [ ] Database schema has all required fields
- [ ] Password is hashed before storing
- [ ] JWT secret comes from environment variable
- [ ] Tokens have expiration time
- [ ] Password never appears in responses
- [ ] Auth middleware extracts and verifies token
- [ ] Proper HTTP status codes used
- [ ] Zod validates all inputs
- [ ] Errors are handled gracefully
- [ ] Repository pattern followed

---

## 🐛 Common Issues & Fixes

### Issue: "Cannot connect to database"
**Solution:**
1. Ensure PostgreSQL is running
2. Verify DATABASE_URL in .env is correct
3. Check database exists: `createdb startuphub`

### Issue: "JWT_SECRET is not defined"
**Solution:**
Add to .env:
```
JWT_SECRET=your-secret-key-here
```

### Issue: "Duplicate key error"
**Solution:**
Email already exists. Either:
- Use different email
- Delete existing user from database
- Handle this error properly (return 409 Conflict)

### Issue: "Token expired"
**Solution:**
- Login again to get new token
- Increase JWT_EXPIRES_IN in .env

---

## 📝 Homework

1. **Test All Scenarios:**
   - Register users with different roles
   - Login with correct/incorrect credentials
   - Access protected routes with/without token
   - Try accessing with expired token

2. **Security Audit:**
   - Verify passwords are hashed in database
   - Confirm password never appears in logs
   - Check JWT payload (decode at jwt.io)

3. **Add Features:**
   - Password strength validation (min 1 uppercase, 1 number)
   - Email format validation (no disposable emails)
   - Rate limiting on login endpoint

4. **Testing:**
   - Create 3 users: founder, investor, admin
   - Save their tokens for next week
   - Document all API endpoints in Postman

---

## 🎯 Learning Objectives Check

- ✅ Understand bcrypt hashing
- ✅ Generate and verify JWT tokens
- ✅ Protect routes with middleware
- ✅ Implement repository pattern
- ✅ Validate inputs with Zod
- ✅ Handle authentication errors
- ✅ Use proper HTTP status codes

---

## 🚀 Next Week Preview

**Topic:** Database Layer & User Management
**What We'll Build:**
- User profile endpoints (GET, UPDATE, DELETE)
- Role-based access control
- User search and filtering
- Soft delete pattern

**Prepare:**
- Understand REST conventions
- Review SQL joins
- Know CRUD operations
