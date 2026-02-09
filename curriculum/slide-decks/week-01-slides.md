# Week 1 Slide Deck: Understanding the Stack & Project Setup

**Total Slides**: ~35-40
**Duration**: 45 minutes of concept teaching

---

## SLIDE 1: Title Slide
**Backend From First Principles**
Week 1: Understanding the Stack & Project Setup

Instructor: [Your Name]

---

## SLIDE 2: Today's Agenda
- What is a Backend?
- The Request Lifecycle
- HTTP Protocol Basics
- Backend Architecture Layers
- Project Structure & Tooling
- Hands-on: Build Your First Server

---

## SECTION 1: What is a Backend?

---

## SLIDE 3: The Invisible Half of the Web
**Visual**: Iceberg diagram
- **Above water (Frontend)**: What users see
- **Below water (Backend)**: Where the magic happens

**Text**:
"Frontend: The Face
Backend: The Brain"

---

## SLIDE 4: Real-World Examples

**What Happens When You...**

1. **Log into Instagram**
   - Frontend: You enter email/password
   - Backend: Checks credentials, creates session

2. **Post a Tweet**
   - Frontend: You type text, click "Tweet"
   - Backend: Stores tweet, notifies followers

3. **Order on Amazon**
   - Frontend: Add to cart, checkout
   - Backend: Process payment, update inventory, schedule delivery

**Key Point**: Backend is the "business logic"

---

## SLIDE 5: Frontend vs Backend

| Frontend | Backend |
|----------|---------|
| Runs in browser | Runs on server |
| HTML, CSS, JavaScript | Any language (JS, Python, Go) |
| User sees it | User never sees it |
| Presentation | Logic & Data |
| Examples: React, Vue | Examples: Express, Django |

---

## SECTION 2: The Request Lifecycle

---

## SLIDE 6: Client-Server Model

**Visual**: Simple diagram
```
[Client/Browser] ←→ [Internet] ←→ [Server]
                                      ↕
                                 [Database]
```

**Key Terms**:
- **Client**: Requests data (your browser, mobile app)
- **Server**: Provides data (backend application)
- **Database**: Stores data permanently

---

## SLIDE 7: The Request Journey

**Visual**: Step-by-step flow diagram

```
1. User clicks "Login" button
   ↓
2. Frontend sends HTTP request
   POST /api/auth/login
   Body: {email, password}
   ↓
3. Request travels over internet
   ↓
4. Server receives request
   ↓
5. Server processes (check credentials)
   ↓
6. Server queries database
   ↓
7. Server sends HTTP response
   Status: 200 OK
   Body: {token, user}
   ↓
8. Frontend receives response
   ↓
9. User sees "Welcome back!"
```

---

## SLIDE 8: Synchronous vs Asynchronous

**Synchronous (Blocking)**:
- Wait for response before doing anything else
- Like waiting in line at a bank

**Asynchronous (Non-blocking)**:
- Send request, continue working, handle response later
- Like ordering food delivery - you don't wait by the door

**JavaScript/Node.js**: Asynchronous by default!

---

## SECTION 3: HTTP Protocol

---

## SLIDE 9: What is HTTP?

**HTTP**: HyperText Transfer Protocol
- The "language" browsers and servers speak
- Request-Response cycle
- Stateless (each request is independent)

**Visual**: Two people talking with speech bubbles
- Client: "Can I have the login page?"
- Server: "Here's the HTML!"

---

## SLIDE 10: HTTP Request Structure

```
POST /api/auth/login HTTP/1.1
Host: api.startuphub.com
Content-Type: application/json
Authorization: Bearer abc123

{
  "email": "user@example.com",
  "password": "secret123"
}
```

**Parts**:
1. **Method**: POST (what action?)
2. **Path**: /api/auth/login (where?)
3. **Headers**: Metadata (Content-Type, Auth)
4. **Body**: The actual data

---

## SLIDE 11: HTTP Methods (Verbs)

| Method | Purpose | Example |
|--------|---------|---------|
| GET | Read data | Get list of startups |
| POST | Create new | Create new startup |
| PUT | Replace existing | Update entire profile |
| PATCH | Partial update | Change just email |
| DELETE | Remove | Delete startup |

**RESTful Convention**: Use the right verb for the right action

---

## SLIDE 12: HTTP Response Structure

```
HTTP/1.1 200 OK
Content-Type: application/json
Set-Cookie: session=xyz789

{
  "success": true,
  "user": {
    "id": 1,
    "email": "user@example.com"
  }
}
```

**Parts**:
1. **Status Code**: 200 (how did it go?)
2. **Headers**: Metadata
3. **Body**: The actual data returned

---

## SLIDE 13: HTTP Status Codes

**2xx - Success** ✅
- 200 OK
- 201 Created

**4xx - Client Error** ⚠️
- 400 Bad Request
- 401 Unauthorized
- 404 Not Found

**5xx - Server Error** 🔥
- 500 Internal Server Error
- 503 Service Unavailable

**Mnemonic**:
- 2xx = "Yay!"
- 4xx = "You messed up"
- 5xx = "We messed up"

---

## SECTION 4: Backend Architecture

---

## SLIDE 14: Why Layer Your Code?

**Bad Approach** (Everything in one file):
```javascript
app.post('/login', (req, res) => {
  // Extract data
  // Validate input
  // Check database
  // Hash password
  // Compare hashes
  // Generate token
  // Log the event
  // Send response
  // ALL IN ONE FUNCTION! 😱
});
```

**Problems**:
- Hard to read
- Hard to test
- Hard to reuse
- Hard to maintain

---

## SLIDE 15: Separation of Concerns

**Principle**: Each part does ONE thing well

**Visual**: Assembly line diagram
```
Request → Router → Controller → Service → Repository → Database
                                                    ↓
Response ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ← ←
```

Each station has a specific job

---

## SLIDE 16: The Layers

**1. Router**
- "Which function handles this URL?"
- Maps URL paths to controllers

**2. Controller**
- "What data do I need?"
- Handles HTTP request/response
- Validates input

**3. Service**
- "What's the business logic?"
- Core application rules
- Framework-agnostic

**4. Repository**
- "How do I access the database?"
- All database queries
- Data layer abstraction

**5. Model**
- "What does this data look like?"
- Data structure definitions

---

## SLIDE 17: Example Flow (User Registration)

```
POST /api/auth/register
        ↓
[Router]
"This goes to authController.register"
        ↓
[Controller]
- Extract email, password from req.body
- Validate with Zod schema
- If invalid → 400 error
        ↓
[Service]
- Check if email already exists
- Hash password with bcrypt
- Call repository to save
        ↓
[Repository]
- Execute: INSERT INTO users...
- Return created user
        ↓
[Database]
- Stores user record
        ↓
Response: 201 Created
{success: true, user: {...}}
```

---

## SLIDE 18: Benefits of Layered Architecture

✅ **Testability**: Test each layer independently
✅ **Reusability**: Services can be used by multiple controllers
✅ **Maintainability**: Bug? Know exactly where to look
✅ **Flexibility**: Swap database? Only change repository
✅ **Collaboration**: Teams can work on different layers

---

## SECTION 5: Project Structure

---

## SLIDE 19: Professional Folder Structure

```
startup-hub-api/
├── src/
│   ├── config/          # 🔧 Configuration
│   ├── controllers/     # 🎮 Request handlers
│   ├── services/        # 🧠 Business logic
│   ├── repositories/    # 💾 Database access
│   ├── models/          # 📋 Data schemas
│   ├── routes/          # 🗺️ URL mapping
│   ├── middleware/      # 🚧 Request interceptors
│   ├── validations/     # ✅ Input validation
│   ├── utils/           # 🛠️ Helper functions
│   └── server.js        # 🚀 Entry point
├── drizzle/             # 🗄️ Database
├── logs/                # 📝 Application logs
├── .env                 # 🔐 Secrets (DON'T COMMIT!)
└── package.json         # 📦 Dependencies
```

---

## SLIDE 20: Config Folder

**Purpose**: Centralize configuration

```
src/config/
├── env.js          # Environment variables
├── db.js           # Database connection
├── logger.js       # Logging setup
└── security.js     # Security settings
```

**Why?**
- One place to change settings
- Easy to switch environments (dev/prod)
- No hardcoded values scattered around

---

## SLIDE 21: The MVC Pattern (Adapted)

**MVC**: Model-View-Controller

**Our Adaptation**:
- **Model**: Data schemas
- **View**: (Frontend handles this)
- **Controller**: Request handlers

**We Add**:
- **Service**: Business logic
- **Repository**: Data access

**Result**: More separation, better code organization

---

## SLIDE 22: Environment Variables

**Problem**:
- Different settings for dev/production
- Secrets shouldn't be in code

**Solution**: `.env` file

```bash
# .env (NEVER COMMIT THIS!)
DATABASE_URL=postgres://localhost/mydb
JWT_SECRET=super-secret-key-change-this
PORT=3000
NODE_ENV=development
```

**Load with**: `require('dotenv').config()`
**Access with**: `process.env.DATABASE_URL`

---

## SLIDE 23: .gitignore is Critical!

**Must Ignore**:
```
node_modules/    # Dependencies (huge!)
.env             # Secrets!
logs/            # Log files
*.log
.DS_Store        # Mac junk
```

**Why?**
- node_modules: Others can run `npm install`
- .env: Committing secrets = security breach
- logs: Can be regenerated

**Provide**: `.env.example` (template without secrets)

---

## SECTION 6: Essential Tools

---

## SLIDE 24: Middleware Explained

**What is Middleware?**
Functions that run BEFORE your route handler

**Visual**: Checkpoint diagram
```
Request
   ↓
[Middleware 1: Logging]
   ↓
[Middleware 2: Auth Check]
   ↓
[Middleware 3: Parse JSON]
   ↓
[Your Route Handler]
   ↓
Response
```

**Example Uses**:
- Logging every request
- Checking authentication
- Parsing request body
- Error handling

---

## SLIDE 25: Common Middleware

**1. express.json()**
- Parses JSON request bodies
- Without this: `req.body` is undefined

**2. helmet()**
- Sets security HTTP headers
- Protects against common attacks

**3. cors()**
- Allows cross-origin requests
- Needed for frontend to call your API

**4. morgan()**
- Logs HTTP requests
- "GET /api/users 200 5ms"

---

## SLIDE 26: Code Quality Tools

**ESLint**:
- Finds bugs before runtime
- Enforces code style
- "You forgot to return a value"

**Prettier**:
- Auto-formats code
- No more arguments about tabs vs spaces
- Consistent style across team

**Nodemon**:
- Auto-restarts server on file changes
- No manual stop/start cycle

---

## SLIDE 27: Package.json Scripts

```json
{
  "scripts": {
    "dev": "nodemon src/server.js",
    "start": "node src/server.js",
    "lint": "eslint src/",
    "format": "prettier --write src/"
  }
}
```

**Usage**:
- `npm run dev` → Development mode (auto-restart)
- `npm start` → Production mode
- `npm run lint` → Check code quality
- `npm run format` → Auto-format code

---

## SECTION 7: Our Tech Stack

---

## SLIDE 28: Technologies We're Using

| Category | Tool | Purpose |
|----------|------|---------|
| Runtime | Node.js | Execute JavaScript on server |
| Framework | Express.js | Web application framework |
| Database | PostgreSQL | Relational database |
| ORM | Drizzle | Database toolkit |
| Validation | Zod | Schema validation |
| Auth | JWT + bcrypt | Tokens & password hashing |
| Logging | Winston | Structured logging |
| Security | Helmet | Security headers |

---

## SLIDE 29: Why Express.js?

**Pros**:
✅ Minimal and flexible
✅ Huge ecosystem
✅ Great for learning fundamentals
✅ Used by: Netflix, PayPal, Uber

**Alternatives**:
- **NestJS**: More structured, TypeScript-first
- **Fastify**: Faster, but newer
- **Koa**: By Express creators, modern

**Note**: Concepts you learn apply to ALL frameworks

---

## SLIDE 30: Why PostgreSQL?

**Relational Database**:
- Tables with rows and columns
- SQL query language
- ACID compliant (reliable)

**Alternatives**:
- **MongoDB**: NoSQL, document-based
- **MySQL**: Similar to PostgreSQL
- **SQLite**: File-based, great for small apps

**We Choose PostgreSQL**:
- Industry standard
- Great for learning SQL
- Powerful features (JSON columns, full-text search)

---

## SECTION 8: Let's Build!

---

## SLIDE 31: Today's Goal

**Build**: Minimal Express server with health check

```javascript
GET /api/health
→ {status: "ok", timestamp: "..."}
```

**Steps**:
1. Initialize project
2. Install dependencies
3. Create server.js
4. Add health route
5. Test it!

---

## SLIDE 32: The Simplest Server

```javascript
const express = require('express');
const app = express();

app.get('/health', (req, res) => {
  res.json({ status: 'ok' });
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

**That's it!**
- Import Express
- Create app instance
- Define route
- Start listening

---

## SLIDE 33: Breaking It Down

```javascript
app.get('/health', (req, res) => {
  res.json({ status: 'ok' });
});
```

**Parts**:
- `app.get()`: Handle GET requests
- `'/health'`: The URL path
- `(req, res) => {}`: Route handler function
- `req`: Request object (data from client)
- `res`: Response object (send data back)
- `res.json()`: Send JSON response

---

## SLIDE 34: Next Steps

**Today (Session 2)**:
- Use AI to generate full project structure
- Walk through generated code
- Run and test the server

**This Week**:
- Explore the codebase
- Modify health check endpoint
- Commit to GitHub

**Next Week**:
- Authentication system
- Password hashing
- JWT tokens

---

## SLIDE 35: Key Takeaways

✅ Backend handles business logic and data
✅ HTTP is request-response protocol
✅ Layer your code (Router → Controller → Service → Repository)
✅ Middleware processes requests before route handlers
✅ Environment variables for configuration
✅ Project structure matters from day one

**Remember**: Good structure now = easier coding later

---

## SLIDE 36: Questions?

**Common Questions**:
- "Can I use TypeScript?"
- "Why not use serverless?"
- "Is Express still relevant in 2026?"
- "How does this compare to Django/Rails?"

**Office Hours**: [Time/Link]

---

## SLIDE 37: Resources

**Documentation**:
- Express.js: https://expressjs.com
- Node.js: https://nodejs.org/docs
- MDN HTTP: https://developer.mozilla.org/en-US/docs/Web/HTTP

**This Week's Materials**:
- Lesson plan
- AI prompt for project setup
- Challenge exercises

**Community**:
- [Slack/Discord channel]
- [Office hours schedule]

---

## BONUS SLIDES (if time permits)

---

## BONUS SLIDE 1: REST API Design Principles

**REST**: Representational State Transfer

**Principles**:
1. **Stateless**: Each request is independent
2. **Client-Server**: Separation of concerns
3. **Uniform Interface**: Consistent URL patterns
4. **Resource-Based**: URLs represent resources

**Example**:
```
GET    /api/startups      # List all
POST   /api/startups      # Create new
GET    /api/startups/:id  # Get one
PUT    /api/startups/:id  # Update
DELETE /api/startups/:id  # Delete
```

---

## BONUS SLIDE 2: JSON Format

**JavaScript Object Notation**

**Why JSON?**
- Human-readable
- Easy to parse
- Language-agnostic
- Lightweight

**Example**:
```json
{
  "user": {
    "id": 1,
    "email": "founder@startup.com",
    "role": "founder"
  },
  "startup": {
    "name": "TechCorp",
    "industry": "AI"
  }
}
```

---

## BONUS SLIDE 3: Debugging Tips

**Tools**:
1. **console.log()**: Old reliable
2. **Chrome DevTools**: Inspect requests
3. **Postman/Insomnia**: Test APIs
4. **VS Code Debugger**: Set breakpoints

**Common Issues**:
- "Cannot find module" → `npm install`
- "Port in use" → Change port or kill process
- "CORS error" → Enable CORS middleware
- "undefined is not a function" → Check imports

---

## END SLIDE

**See you in Session 2!**

Get your laptops ready
Install Node.js if you haven't
Open Cursor/Windsurf

Let's build something awesome 🚀
