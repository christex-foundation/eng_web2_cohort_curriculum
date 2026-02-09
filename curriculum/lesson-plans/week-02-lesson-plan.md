# Week 2 Lesson Plan: User Authentication System

## 📋 Overview
**Duration**: 4 hours (2 sessions)
**Format**: 30% Concept / 70% Hands-on

## 🎯 Learning Objectives
- [ ] Explain authentication vs authorization
- [ ] Understand password hashing and why it's critical
- [ ] Implement JWT-based authentication
- [ ] Create registration and login endpoints
- [ ] Build authentication middleware
- [ ] Use Zod for input validation

---

## 📅 Session 1: Security Concepts & Database Setup (2 hours)

### Part 1: Authentication Fundamentals (45 min)

**Topics to Cover:**

**1. Authentication vs Authorization (10 min)**
- **Authentication**: "Who are you?" (Login)
- **Authorization**: "What can you do?" (Permissions)
- Example: Airport security (ID check vs boarding pass)

**2. Password Security (15 min)**
- **NEVER store plain text passwords**
- Demonstration: Show haveibeenpwned.com breach
- **Hashing vs Encryption**:
  - Encryption: Two-way (can decrypt)
  - Hashing: One-way (cannot reverse)
- **Salt**: Random data added before hashing
- **bcrypt**: Industry standard, built-in salt, adaptive

**Visual**:
```
User Password: "mypassword123"
        ↓
Add Salt: "randomsalt" + "mypassword123"
        ↓
Bcrypt Hash: "$2b$10$N9qo8uLOickgx2ZMRZoMye..."
        ↓
Store in Database
```

**3. Token-Based Authentication (20 min)**
- **Session-based vs Token-based**
- **JWT (JSON Web Token) Structure**:
  ```
  header.payload.signature
  ```
- **How JWT Works**:
  1. User logs in with credentials
  2. Server validates and generates JWT
  3. Client stores JWT (localStorage/cookie)
  4. Client sends JWT with every request
  5. Server verifies JWT

**Whiteboard**: Draw authentication flow

**Discussion**: "Why not just send password with every request?"

---

### Part 2: Database Setup with Drizzle (45 min)

**Topics**:

**1. ORM Concept (10 min)**
- What is an ORM?
- Why use Drizzle over raw SQL?
- Type-safety benefits
- Migration management

**2. Schema Design (15 min)**
- Users table structure:
  - id (UUID)
  - email (unique)
  - password (hashed)
  - role (enum: founder, investor, admin)
  - createdAt, updatedAt
- Indexes on email for fast lookups

**3. Repository Pattern (20 min)**
- Separation of concerns
- Why not put SQL in controllers?
- Testability benefits

**File Structure**:
```
drizzle/
  schema/
    users.schema.js
  migrations/
    0001_create_users.sql

src/
  repositories/
    user.repository.js
```

---

### Part 3: Live Demo - Hashing & JWT (30 min)

**Demo 1: Password Hashing**
```javascript
const bcrypt = require('bcrypt');

// Hashing
const password = 'mypassword123';
const hash = await bcrypt.hash(password, 10); // 10 rounds
console.log(hash); // $2b$10$...

// Verification
const isMatch = await bcrypt.compare('mypassword123', hash);
console.log(isMatch); // true

const isWrong = await bcrypt.compare('wrongpassword', hash);
console.log(isWrong); // false
```

**Demo 2: JWT Creation & Verification**
```javascript
const jwt = require('jsonwebtoken');

// Create token
const payload = { userId: 1, email: 'user@example.com' };
const secret = 'your-secret-key';
const token = jwt.sign(payload, secret, { expiresIn: '24h' });
console.log(token);

// Verify token
const decoded = jwt.verify(token, secret);
console.log(decoded); // { userId: 1, email: '...', iat: ..., exp: ... }
```

**Key Points**:
- Secrets should be in .env
- Tokens expire (set appropriate duration)
- Never put sensitive data in JWT payload (it's encoded, not encrypted)

---

## 📅 Session 2: Building Auth System (2 hours)

### Part 1: AI-Assisted Build (60 min)

**Students use AI prompt to generate:**
1. User schema (Drizzle)
2. User repository
3. Auth service (registration, login, hashing)
4. Auth controller
5. Validation schemas (Zod)
6. JWT utilities
7. Auth middleware
8. Auth routes

**Instructor's Role:**
- Guide through each component
- Explain generated code
- Highlight security considerations

---

### Part 2: Code Walkthrough (40 min)

**Walk Through Key Files:**

**1. User Schema (drizzle/schema/users.schema.js)**
```javascript
import { pgTable, uuid, varchar, timestamp } from 'drizzle-orm/pg-core';

export const users = pgTable('users', {
  id: uuid('id').defaultRandom().primaryKey(),
  email: varchar('email', { length: 255 }).notNull().unique(),
  password: varchar('password', { length: 255 }).notNull(),
  role: varchar('role', { length: 50 }).notNull().default('founder'),
  createdAt: timestamp('created_at').defaultNow(),
  updatedAt: timestamp('updated_at').defaultNow(),
});
```

**Explain:**
- UUID for primary key (better than auto-increment)
- Unique constraint on email
- Varchar length for password hash
- Role with default value

**2. Auth Service (src/services/auth.service.js)**
```javascript
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');
const userRepository = require('../repositories/user.repository');
const config = require('../config/env');

class AuthService {
  async register(email, password, role = 'founder') {
    // Check if user exists
    const existing = await userRepository.findByEmail(email);
    if (existing) {
      throw new Error('Email already in use');
    }

    // Hash password
    const hashedPassword = await bcrypt.hash(password, 10);

    // Create user
    const user = await userRepository.create({
      email,
      password: hashedPassword,
      role,
    });

    // Generate token
    const token = this.generateToken(user);

    return { user: this.sanitizeUser(user), token };
  }

  async login(email, password) {
    // Find user
    const user = await userRepository.findByEmail(email);
    if (!user) {
      throw new Error('Invalid credentials');
    }

    // Verify password
    const isValid = await bcrypt.compare(password, user.password);
    if (!isValid) {
      throw new Error('Invalid credentials');
    }

    // Generate token
    const token = this.generateToken(user);

    return { user: this.sanitizeUser(user), token };
  }

  generateToken(user) {
    return jwt.sign(
      { userId: user.id, email: user.email, role: user.role },
      config.jwtSecret,
      { expiresIn: '24h' }
    );
  }

  sanitizeUser(user) {
    const { password, ...sanitized } = user;
    return sanitized;
  }
}

module.exports = new AuthService();
```

**Key Points:**
- Always sanitize user (remove password)
- Same error message for "user not found" and "wrong password" (prevent email enumeration)
- Token includes only necessary data

**3. Auth Middleware (src/middleware/auth.middleware.js)**
```javascript
const jwt = require('jsonwebtoken');
const config = require('../config/env');

const authenticate = (req, res, next) => {
  try {
    // Get token from header
    const authHeader = req.headers.authorization;
    if (!authHeader || !authHeader.startsWith('Bearer ')) {
      return res.status(401).json({ error: 'No token provided' });
    }

    const token = authHeader.split(' ')[1];

    // Verify token
    const decoded = jwt.verify(token, config.jwtSecret);

    // Attach user info to request
    req.user = decoded;

    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
};

const authorize = (...roles) => {
  return (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({ error: 'Not authenticated' });
    }

    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Insufficient permissions' });
    }

    next();
  };
};

module.exports = { authenticate, authorize };
```

**Explain:**
- `authenticate`: Verifies token, adds user to req
- `authorize`: Checks user role (authorization)
- 401 (Unauthorized) vs 403 (Forbidden)

---

### Part 3: Testing (20 min)

**Test Registration:**
```bash
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"founder@test.com","password":"test123","role":"founder"}'
```

**Expected Response:**
```json
{
  "user": {
    "id": "...",
    "email": "founder@test.com",
    "role": "founder"
  },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Test Login:**
```bash
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"founder@test.com","password":"test123"}'
```

**Test Protected Route:**
```bash
curl http://localhost:3000/api/auth/me \
  -H "Authorization: Bearer [YOUR_TOKEN]"
```

---

## 📝 Homework
1. Create admin and investor accounts
2. Test all auth endpoints with Postman
3. Try using expired/invalid tokens
4. Add password strength validation

## 🎓 Key Takeaways
- Never store plain passwords
- Use bcrypt for hashing
- JWT for stateless auth
- Middleware for protecting routes
- Repository pattern for clean code

## 📊 Assessment Quiz
1. What's the difference between authentication and authorization?
2. Why use bcrypt instead of just hashing?
3. What are the three parts of a JWT?
4. Where should JWT secret be stored?
5. What status code for unauthorized access?
