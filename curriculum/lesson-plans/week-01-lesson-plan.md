# Week 1 Lesson Plan: Understanding the Stack & Project Setup

## 📋 Overview

**Duration**: 4 hours (split into 2 sessions)
**Format**: 40% Concept / 60% Hands-on

## 🎯 Learning Objectives

By the end of this week, students will be able to:
- [ ] Explain the request lifecycle in a backend system
- [ ] Describe the purpose of each layer (Controller, Service, Repository)
- [ ] Set up a professional Express.js project structure
- [ ] Configure environment variables and tooling
- [ ] Create a basic health check endpoint

## 📚 Prerequisites

- Node.js v18+ installed
- Cursor or Windsurf IDE installed
- Basic JavaScript knowledge
- Git installed

---

## 📅 Session 1: Concepts & Architecture (2 hours)

### Part 1: Backend Architecture Deep Dive (45 min)

#### What to Cover:

**1. The Big Picture (15 min)**
- What is a backend? (The "invisible" part of web apps)
- Client-Server model
- Request → Server → Database → Response flow
- Real-world examples: "What happens when you log into Instagram?"

**Discussion Questions:**
- "Where does the backend live?"
- "What's the difference between frontend and backend code?"
- "Can you name 5 things a backend does?"

**2. HTTP Protocol Basics (15 min)**
- HTTP Request structure (Method, Headers, Body)
- HTTP Response structure (Status Code, Headers, Body)
- Common methods: GET, POST, PUT, DELETE, PATCH
- Status codes families (2xx, 4xx, 5xx)

**Activity**: Show raw HTTP request/response using browser DevTools

**3. Backend Layers Explained (15 min)**
- Why separate concerns?
- **Router**: "Which function handles this URL?"
- **Controller**: "What data do I need? Is input valid?"
- **Service**: "What's the business logic?"
- **Repository**: "How do I get/save data from DB?"
- **Model**: "What does this data look like?"

**Whiteboard Exercise**:
```
User Request: POST /api/auth/register
              ↓
Router: "This goes to authController.register"
              ↓
Controller: "Extract email, password from body"
            "Validate input"
            ↓
Service: "Check if email exists"
         "Hash password"
         "Create user"
         ↓
Repository: "INSERT INTO users..."
         ↓
Database: [stores user]
         ↓
Response: {success: true, user: {...}}
```

---

### Part 2: Project Structure & Tooling (30 min)

#### What to Cover:

**1. Why Structure Matters (10 min)**
- Messy vs clean codebases
- Scalability concerns
- Team collaboration
- Industry standards

**2. Folder Structure Walkthrough (15 min)**

Present this structure:
```
startup-hub-api/
├── src/
│   ├── config/          # Configuration (DB, env, logger)
│   ├── controllers/     # Handle requests, send responses
│   ├── services/        # Business logic
│   ├── repositories/    # Database queries
│   ├── models/          # Data schemas
│   ├── routes/          # URL → Controller mapping
│   ├── middleware/      # Request interceptors (auth, logging)
│   ├── validations/     # Zod schemas
│   ├── utils/           # Helper functions
│   └── server.js        # Entry point
├── drizzle/
│   ├── migrations/      # Database version history
│   └── schema/          # Table definitions
├── logs/                # Application logs
├── .env                 # Environment variables (SECRET!)
├── .env.example         # Template for .env
├── eslint.config.js     # Code linting rules
├── .prettierrc          # Code formatting rules
└── package.json         # Dependencies
```

**Key Points:**
- Never commit `.env` (add to `.gitignore`)
- Each folder has ONE responsibility
- Tests mirror source structure

**3. Essential Tools (5 min)**
- **ESLint**: Catches bugs, enforces code style
- **Prettier**: Automatic code formatting
- **Nodemon**: Auto-restart on file changes
- **Winston**: Logging
- **Dotenv**: Environment variables

---

### Part 3: Interactive Demo (30 min)

**Live Coding Session**: Create minimal Express server

```javascript
// server.js
const express = require('express');
const app = express();

app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
});

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

**Demonstrate:**
1. Start server: `node server.js`
2. Visit `http://localhost:3000/health` in browser
3. Show JSON response
4. Change response, restart server (manual pain point → introduce Nodemon later)

**Explain Each Line:**
- `const express = require('express')`: Import Express library
- `const app = express()`: Create app instance
- `app.get()`: "When someone visits this URL with GET method..."
- `res.json()`: "Send back JSON data"
- `app.listen()`: "Start listening for requests on port 3000"

---

### Part 4: Q&A + Setup Check (15 min)

**Questions to Address:**
- "What's the difference between `npm install` and `npm install --save-dev`?"
- "Why do we need so many folders?"
- "What if I want to use TypeScript instead?"

**Homework Preview:**
- Session 2 will use AI to build the complete project structure
- Students should have Node.js and Cursor/Windsurf ready

---

## 📅 Session 2: AI-Assisted Build (2 hours)

### Part 1: AI Prompt & Code Generation (45 min)

**Setup:**
1. Open Cursor/Windsurf
2. Create new folder: `startup-hub-api`
3. Navigate into it: `cd startup-hub-api`

**AI Prompt Usage:**
- Students use the provided AI prompt (`ai-prompts/week-01-prompt.md`)
- Let AI generate the project structure
- Generate one section at a time (don't overwhelm)

**Instructor's Role:**
- Walk around, help with AI tool usage
- Ensure students understand WHAT is being generated
- Pause and explain key files

---

### Part 2: Code Walkthrough (45 min)

**File-by-File Review:**

**1. `package.json` (10 min)**
```json
{
  "name": "startup-hub-api",
  "version": "1.0.0",
  "scripts": {
    "dev": "nodemon src/server.js",
    "start": "node src/server.js"
  },
  "dependencies": {
    "express": "^4.18.0",
    "dotenv": "^16.0.0",
    "cors": "^2.8.5",
    "helmet": "^7.0.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.0",
    "eslint": "^8.50.0",
    "prettier": "^3.0.0"
  }
}
```

**Explain:**
- `dependencies`: Needed to run the app
- `devDependencies`: Only for development
- `scripts`: Shortcuts (`npm run dev` → starts with Nodemon)

**2. `.env` and `.env.example` (5 min)**
```bash
# .env
PORT=3000
NODE_ENV=development

# .env.example (committed to Git)
PORT=
NODE_ENV=
```

**Explain:**
- Why `.env` is in `.gitignore`
- How to use `process.env.PORT`

**3. `src/config/env.js` (10 min)**
```javascript
require('dotenv').config();

module.exports = {
  port: process.env.PORT || 3000,
  nodeEnv: process.env.NODE_ENV || 'development',
};
```

**Explain:**
- Centralize env access
- Default values (`||`)
- Why not access `process.env` directly everywhere

**4. `src/server.js` (15 min)**
```javascript
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');
const config = require('./config/env');
const healthRoutes = require('./routes/health.routes');

const app = express();

// Middleware
app.use(helmet());
app.use(cors());
app.use(express.json());

// Routes
app.use('/api/health', healthRoutes);

// Start server
app.listen(config.port, () => {
  console.log(`Server running on port ${config.port}`);
});
```

**Explain:**
- **Middleware**: Functions that run BEFORE your route handlers
- **helmet()**: Sets security HTTP headers
- **cors()**: Allows frontend to call your API
- **express.json()**: Parses JSON request bodies

**5. `src/routes/health.routes.js` (5 min)**
```javascript
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.json({
    status: 'ok',
    timestamp: new Date().toISOString(),
    environment: process.env.NODE_ENV,
  });
});

module.exports = router;
```

**Explain:**
- Router groups related endpoints
- `/api/health` + `/` = `/api/health/`

---

### Part 3: Hands-On Testing (20 min)

**Activity: Run the Server**

1. Install dependencies:
```bash
npm install
```

2. Start server:
```bash
npm run dev
```

3. Test in browser: `http://localhost:3000/api/health`

4. Test with curl:
```bash
curl http://localhost:3000/api/health
```

**Expected Response:**
```json
{
  "status": "ok",
  "timestamp": "2026-02-06T10:30:00.000Z",
  "environment": "development"
}
```

**Troubleshooting Common Issues:**
- Port already in use → Change PORT in `.env`
- `Cannot find module` → Did you run `npm install`?
- Server not restarting → Check Nodemon is running

---

### Part 4: Challenge Time (10 min)

**Quick Challenge:**
1. Add a new route: `GET /api/health/uptime`
2. It should return how long the server has been running
3. Hint: Store `startTime` when server starts

**Solution:**
```javascript
const startTime = Date.now();

router.get('/uptime', (req, res) => {
  const uptime = Date.now() - startTime;
  res.json({
    uptime: `${Math.floor(uptime / 1000)} seconds`,
  });
});
```

---

## 📝 Homework & Next Week Preview

### Homework:
1. ✅ Ensure project runs successfully
2. ✅ Read through all generated files
3. ✅ Add one more health check route (be creative!)
4. ✅ Commit code to GitHub (create repo if needed)

### Next Week Preview:
- **Topic**: User Authentication System
- **Concepts**: Password hashing, JWT tokens, security
- **Build**: Registration & login endpoints
- **Prep**: Review basic cryptography concepts (optional)

---

## 🎓 Teaching Notes

### Common Student Questions:

**Q: "Why do we need so many files for a simple health check?"**
A: We're building for scale. Week 12 will have 50+ files. Structure now = easier later.

**Q: "Can I use TypeScript instead?"**
A: Yes! The patterns are the same. Focus on concepts this week, refactor to TS later.

**Q: "What if AI generates different code than yours?"**
A: Perfect! We'll learn by comparing approaches. There's no single "right" way.

### Common Pitfalls:

⚠️ Students skip reading generated code
- **Fix**: Mandatory code review session

⚠️ Port conflicts
- **Fix**: Show how to check/kill processes on that port

⚠️ Confusion between `require` and `import`
- **Fix**: Explain we're using CommonJS, not ES6 modules (for now)

### Time Management Tips:

- If running behind, skip detailed `.eslintrc` explanation
- If ahead, demonstrate API testing with Postman/Insomnia
- Save "why Express over others" debate for office hours

---

## 📊 Assessment

Students should be able to:
- ✅ Draw request flow diagram from memory
- ✅ Explain purpose of each folder
- ✅ Start server and test health endpoint
- ✅ Modify and add new routes
- ✅ Explain what middleware is

**Quick Quiz (5 min):**
1. What does `app.use(express.json())` do?
2. Where should database passwords go?
3. What's the difference between a controller and a service?
