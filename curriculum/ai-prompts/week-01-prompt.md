# Week 1 AI Prompt: Project Setup & Basic Server

## 📋 Instructions for Students

1. Create a new folder called `startup-hub-api`
2. Open it in Cursor/Windsurf
3. Copy the prompt below
4. Paste into your AI coding assistant
5. Let it generate the code
6. Review what was created with your instructor

---

## 🤖 AI PROMPT (Copy Everything Below)

```
I'm building a professional Express.js REST API called StartupHub where users can list startups and investors can express interest in them.

PROJECT SETUP:

1. Initialize a Node.js project with the following structure:

```
startup-hub-api/
├── src/
│   ├── config/
│   │   └── env.js
│   ├── routes/
│   │   └── health.routes.js
│   └── server.js
├── logs/
├── .env
├── .env.example
├── .gitignore
├── package.json
├── eslint.config.js
└── .prettierrc
```

2. DEPENDENCIES to install:
- express
- dotenv
- cors
- helmet
- morgan
- winston
- bcrypt
- jsonwebtoken
- zod
- drizzle-orm
- @neondatabase/serverless (or pg for PostgreSQL)

3. DEV DEPENDENCIES:
- nodemon
- eslint
- prettier

4. CREATE THE FOLLOWING FILES:

**package.json:**
- Include scripts for "dev" (using nodemon), "start", "lint", "format"
- Include all dependencies listed above

**.env:**
```
PORT=3000
NODE_ENV=development
```

**.env.example:**
```
PORT=
NODE_ENV=
```

**.gitignore:**
```
node_modules/
.env
logs/
*.log
.DS_Store
```

**src/config/env.js:**
- Load environment variables using dotenv
- Export configuration object with: port, nodeEnv
- Provide sensible defaults

**src/server.js:**
- Import Express
- Import and configure helmet for security
- Import and configure cors
- Use express.json() middleware
- Register health routes at /api/health
- Add global error handler middleware
- Start server on configured port
- Log when server starts successfully

**src/routes/health.routes.js:**
- Create Express router
- Define GET / route that returns:
  - status: "healthy"
  - timestamp: current ISO timestamp
  - environment: current NODE_ENV
  - message: "StartupHub API is running"
- Export router

**eslint.config.js:**
- Set up ESLint for Node.js environment
- Use recommended rules
- Allow console.log (we'll use proper logging later)

**.prettierrc:**
```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```

5. REQUIREMENTS:
- Use CommonJS (require/module.exports), not ES6 imports
- All route handlers should be in separate files, not in server.js
- Configuration should be centralized in config folder
- Follow the repository pattern (we'll add more layers next week)
- Include helpful comments explaining key parts
- Use arrow functions consistently
- Handle errors gracefully

6. TESTING:
After generation, I should be able to:
- Run `npm install`
- Run `npm run dev`
- Visit http://localhost:3000/api/health
- See JSON response with health status

Please generate all these files with production-ready code, following Express.js best practices.
```

---

## ✅ What You Should Have After Running This Prompt

### File Structure:
- Complete project structure with all folders
- 10+ files created
- All dependencies in package.json
- Configuration files (ESLint, Prettier)

### Working Features:
- Server starts on port 3000
- Health check endpoint responds
- Environment variables loaded
- Security headers set (Helmet)
- CORS enabled

---

## 🧪 Testing Your Setup

### Step 1: Install Dependencies
```bash
npm install
```

**Expected**: All packages install without errors

### Step 2: Start Server
```bash
npm run dev
```

**Expected Output:**
```
Server running on port 3000
```

### Step 3: Test Health Endpoint

**In Browser:**
Visit: `http://localhost:3000/api/health`

**Using curl:**
```bash
curl http://localhost:3000/api/health
```

**Expected Response:**
```json
{
  "status": "healthy",
  "timestamp": "2026-02-06T10:30:00.000Z",
  "environment": "development",
  "message": "StartupHub API is running"
}
```

### Step 4: Verify Auto-Restart (Nodemon)

1. Open `src/routes/health.routes.js`
2. Change the message
3. Save file
4. Check terminal - server should restart automatically
5. Refresh browser - new message should appear

---

## 🔍 Code Review Checklist

Go through the generated code with your instructor:

- [ ] Can you find where Express is imported?
- [ ] Where is the port number defined?
- [ ] What does `helmet()` do?
- [ ] What does `cors()` do?
- [ ] What does `express.json()` do?
- [ ] How is the health route registered?
- [ ] Where are environment variables loaded?
- [ ] What's in `.gitignore` and why?

---

## 🐛 Common Issues & Fixes

### Issue: "Cannot find module 'express'"
**Solution:**
```bash
npm install
```

### Issue: "Port 3000 is already in use"
**Solution:**
Option 1 - Change port in `.env`:
```
PORT=3001
```

Option 2 - Kill process using port 3000:
```bash
# Mac/Linux
lsof -ti:3000 | xargs kill -9

# Windows
netstat -ano | findstr :3000
taskkill /PID [PID_NUMBER] /F
```

### Issue: "Module not found: 'dotenv'"
**Solution:** Verify dependencies in package.json and run:
```bash
npm install dotenv
```

### Issue: Server starts but health endpoint returns 404
**Solution:**
- Check route path in `health.routes.js`
- Verify route is registered in `server.js`
- Ensure URL includes `/api/health` not just `/health`

---

## 📝 Homework

1. **Understand Every File**
   - Read through each generated file
   - Understand what each line does
   - Ask questions in next session

2. **Add Uptime to Health Check**
   - Store server start time
   - Calculate uptime in seconds
   - Add to health response

3. **Create GitHub Repository**
   - Initialize git: `git init`
   - Create repo on GitHub
   - Push your code:
   ```bash
   git add .
   git commit -m "Initial project setup"
   git remote add origin [your-repo-url]
   git push -u origin main
   ```

4. **Explore Middleware**
   - Add a `console.log()` before `app.use('/api/health', healthRoutes)`
   - Make a request
   - Observe the order of execution

---

## 🎯 Learning Objectives Check

After completing this setup, you should understand:

- ✅ How to structure an Express.js project
- ✅ Purpose of middleware (helmet, cors, express.json)
- ✅ Environment variable management
- ✅ Basic routing in Express
- ✅ How to test API endpoints
- ✅ Importance of .gitignore
- ✅ Package.json scripts usage

---

## 📚 Additional Reading (Optional)

- [Express.js Guide](https://expressjs.com/en/guide/routing.html)
- [Helmet.js Documentation](https://helmetjs.github.io/)
- [CORS Explained](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
- [Semantic Versioning](https://semver.org/) (for package.json)

---

## 🚀 Next Week Preview

**Topic:** User Authentication System
**What We'll Build:**
- User registration endpoint
- Login with JWT tokens
- Password hashing with bcrypt
- Auth middleware

**Prep Work:**
- Understand what JWT is (brief reading)
- Know why we never store plain passwords
- Review Promises/async-await in JavaScript

---

## 💬 Questions for Discussion

1. Why do we separate routes into different files?
2. What happens if we don't use `express.json()` middleware?
3. Why is helmet important for security?
4. What's the difference between dependencies and devDependencies?
5. Why do we need both `.env` and `.env.example`?

**Be ready to discuss these in the next session!**
