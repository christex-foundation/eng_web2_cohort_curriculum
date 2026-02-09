# Week 1 Challenges: Understanding the Stack & Project Setup

## 🎯 Challenge Overview

These exercises help solidify your understanding of Express.js basics, project structure, and HTTP fundamentals. Complete them in order.

**Difficulty Levels:**
- 🟢 Beginner: Should be straightforward with the material
- 🟡 Intermediate: Requires some problem-solving
- 🔴 Advanced: Stretch challenge, may need research

---

## Challenge 1: Health Check Enhancement 🟢

### Objective
Enhance the basic health check endpoint with more useful information.

### Requirements
Modify `GET /api/health` to return:
- Current status
- Timestamp
- Server uptime (how long it's been running)
- Node.js version
- Environment (development/production)

### Expected Response
```json
{
  "status": "healthy",
  "timestamp": "2026-02-06T10:30:00.000Z",
  "uptime": "125 seconds",
  "nodeVersion": "v20.10.0",
  "environment": "development"
}
```

### Hints
- Store server start time when app launches
- Use `Date.now()` to calculate uptime
- `process.version` gives Node version
- `process.env.NODE_ENV` for environment

### Solution
<details>
<summary>Click to reveal solution</summary>

```javascript
// src/server.js
const express = require('express');
const config = require('./config/env');

const app = express();
const startTime = Date.now();

// Middleware
app.use(express.json());

// Health route
app.get('/api/health', (req, res) => {
  const uptime = Date.now() - startTime;

  res.json({
    status: 'healthy',
    timestamp: new Date().toISOString(),
    uptime: `${Math.floor(uptime / 1000)} seconds`,
    nodeVersion: process.version,
    environment: config.nodeEnv,
  });
});

app.listen(config.port, () => {
  console.log(`Server running on port ${config.port}`);
});
```
</details>

---

## Challenge 2: Add a Version Endpoint 🟢

### Objective
Create a new endpoint that returns API version information.

### Requirements
Create `GET /api/version` that returns:
- API version (from package.json)
- API name
- Description

### Expected Response
```json
{
  "name": "StartupHub API",
  "version": "1.0.0",
  "description": "Backend API for startup listings and investments"
}
```

### Steps
1. Create `src/routes/version.routes.js`
2. Read version from `package.json`
3. Register route in `server.js`

### Hints
- `const packageJson = require('../package.json')`
- Access with `packageJson.version`

### Solution
<details>
<summary>Click to reveal solution</summary>

```javascript
// src/routes/version.routes.js
const express = require('express');
const router = express.Router();
const packageJson = require('../../package.json');

router.get('/', (req, res) => {
  res.json({
    name: 'StartupHub API',
    version: packageJson.version,
    description: packageJson.description || 'Backend API for startup listings and investments',
  });
});

module.exports = router;

// src/server.js
const versionRoutes = require('./routes/version.routes');
app.use('/api/version', versionRoutes);
```
</details>

---

## Challenge 3: Request Logger Middleware 🟡

### Objective
Create custom middleware that logs every incoming request.

### Requirements
Log the following for each request:
- HTTP method
- Request path
- Timestamp
- IP address

### Expected Console Output
```
[2026-02-06 10:30:00] GET /api/health from 127.0.0.1
[2026-02-06 10:30:05] POST /api/auth/login from 192.168.1.5
```

### Steps
1. Create `src/middleware/logger.middleware.js`
2. Export a function that logs request info
3. Use `app.use()` to register it globally

### Hints
- Middleware signature: `(req, res, next) => {}`
- Call `next()` to pass to next middleware
- `req.method`, `req.path`, `req.ip`

### Solution
<details>
<summary>Click to reveal solution</summary>

```javascript
// src/middleware/logger.middleware.js
const logRequest = (req, res, next) => {
  const timestamp = new Date().toISOString().replace('T', ' ').substring(0, 19);
  console.log(`[${timestamp}] ${req.method} ${req.path} from ${req.ip}`);
  next();
};

module.exports = logRequest;

// src/server.js
const logRequest = require('./middleware/logger.middleware');

app.use(logRequest);
```
</details>

---

## Challenge 4: Environment-Based Configuration 🟡

### Objective
Load different port numbers based on environment.

### Requirements
- Development: Port 3000
- Testing: Port 4000
- Production: Port from env variable or 5000

### Steps
1. Update `.env` with `NODE_ENV`
2. Modify `src/config/env.js` to handle different environments
3. Test by changing `NODE_ENV`

### Expected Behavior
```bash
NODE_ENV=development npm run dev  # Port 3000
NODE_ENV=testing npm run dev      # Port 4000
NODE_ENV=production npm start     # Port 5000
```

### Solution
<details>
<summary>Click to reveal solution</summary>

```javascript
// src/config/env.js
require('dotenv').config();

const getPort = () => {
  if (process.env.PORT) {
    return parseInt(process.env.PORT, 10);
  }

  switch (process.env.NODE_ENV) {
    case 'development':
      return 3000;
    case 'testing':
      return 4000;
    case 'production':
      return 5000;
    default:
      return 3000;
  }
};

module.exports = {
  port: getPort(),
  nodeEnv: process.env.NODE_ENV || 'development',
};
```
</details>

---

## Challenge 5: 404 Not Found Handler 🟡

### Objective
Handle requests to non-existent routes with a proper 404 response.

### Requirements
When a user visits a route that doesn't exist:
- Return 404 status code
- Return JSON error message

### Expected Response
```
GET /api/nonexistent
→ 404 Not Found

{
  "error": "Route not found",
  "path": "/api/nonexistent",
  "method": "GET"
}
```

### Hints
- Add this middleware AFTER all routes
- `res.status(404)` to set status code
- It won't have `next()` - it's the final handler

### Solution
<details>
<summary>Click to reveal solution</summary>

```javascript
// src/middleware/not-found.middleware.js
const notFound = (req, res) => {
  res.status(404).json({
    error: 'Route not found',
    path: req.path,
    method: req.method,
  });
};

module.exports = notFound;

// src/server.js
const notFound = require('./middleware/not-found.middleware');

// All your routes here...
app.use('/api/health', healthRoutes);
app.use('/api/version', versionRoutes);

// 404 handler (MUST be last)
app.use(notFound);
```
</details>

---

## Challenge 6: Multiple Response Formats 🔴

### Objective
Allow health check to return data in different formats based on query parameter.

### Requirements
- `GET /api/health` → JSON (default)
- `GET /api/health?format=text` → Plain text
- `GET /api/health?format=html` → HTML

### Expected Responses

**JSON** (default):
```json
{"status": "healthy", "timestamp": "..."}
```

**Text**:
```
Status: healthy
Timestamp: 2026-02-06T10:30:00.000Z
```

**HTML**:
```html
<html>
<body>
  <h1>Health Check</h1>
  <p>Status: healthy</p>
  <p>Timestamp: 2026-02-06T10:30:00.000Z</p>
</body>
</html>
```

### Hints
- Access query params with `req.query.format`
- `res.json()` for JSON
- `res.send()` for text/HTML
- `res.set('Content-Type', 'text/html')` for HTML

### Solution
<details>
<summary>Click to reveal solution</summary>

```javascript
// src/routes/health.routes.js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  const data = {
    status: 'healthy',
    timestamp: new Date().toISOString(),
  };

  const format = req.query.format || 'json';

  switch (format) {
    case 'text':
      res.set('Content-Type', 'text/plain');
      res.send(`Status: ${data.status}\nTimestamp: ${data.timestamp}`);
      break;

    case 'html':
      res.set('Content-Type', 'text/html');
      res.send(`
        <html>
        <body>
          <h1>Health Check</h1>
          <p>Status: ${data.status}</p>
          <p>Timestamp: ${data.timestamp}</p>
        </body>
        </html>
      `);
      break;

    default:
      res.json(data);
  }
});

module.exports = router;
```
</details>

---

## Challenge 7: Request Counter Middleware 🔴

### Objective
Track how many requests have been made to the server since it started.

### Requirements
- Count total requests
- Add `/api/stats` endpoint that shows request count
- Reset counter with `POST /api/stats/reset`

### Expected Behavior
```
GET /api/stats
→ {"totalRequests": 42}

POST /api/stats/reset
→ {"message": "Counter reset", "previousCount": 42}

GET /api/stats
→ {"totalRequests": 1}
```

### Hints
- Use a variable outside the middleware to persist count
- Increment on every request
- Be careful with scope

### Solution
<details>
<summary>Click to reveal solution</summary>

```javascript
// src/middleware/counter.middleware.js
let requestCount = 0;

const countRequests = (req, res, next) => {
  requestCount++;
  next();
};

const getCount = () => requestCount;
const resetCount = () => {
  const previous = requestCount;
  requestCount = 0;
  return previous;
};

module.exports = { countRequests, getCount, resetCount };

// src/routes/stats.routes.js
const express = require('express');
const router = express.Router();
const { getCount, resetCount } = require('../middleware/counter.middleware');

router.get('/', (req, res) => {
  res.json({ totalRequests: getCount() });
});

router.post('/reset', (req, res) => {
  const previous = resetCount();
  res.json({
    message: 'Counter reset',
    previousCount: previous,
  });
});

module.exports = router;

// src/server.js
const { countRequests } = require('./middleware/counter.middleware');
const statsRoutes = require('./routes/stats.routes');

app.use(countRequests); // Must be before routes
app.use('/api/stats', statsRoutes);
```
</details>

---

## Challenge 8: Routing Organization 🔴

### Objective
Create a centralized route registration system.

### Requirements
- Create `src/routes/index.js` that imports all routes
- Register all routes from one place
- Clean up `server.js`

### Expected Structure
```javascript
// server.js is now cleaner
const routes = require('./routes');
routes(app);
```

### Solution
<details>
<summary>Click to reveal solution</summary>

```javascript
// src/routes/index.js
const healthRoutes = require('./health.routes');
const versionRoutes = require('./version.routes');
const statsRoutes = require('./stats.routes');

module.exports = (app) => {
  app.use('/api/health', healthRoutes);
  app.use('/api/version', versionRoutes);
  app.use('/api/stats', statsRoutes);

  // Add more routes here as the app grows
};

// src/server.js (simplified)
const express = require('express');
const config = require('./config/env');
const registerRoutes = require('./routes');
const notFound = require('./middleware/not-found.middleware');

const app = express();

// Middleware
app.use(express.json());

// Register all routes
registerRoutes(app);

// 404 handler
app.use(notFound);

// Start server
app.listen(config.port, () => {
  console.log(`Server running on port ${config.port}`);
});
```
</details>

---

## 🏆 Bonus Challenge: Complete API Documentation

### Objective
Document all your endpoints in a README.

### Requirements
Create `API_DOCS.md` with:
- List of all endpoints
- Request/response examples
- Query parameters
- Status codes

### Example Format
```markdown
# API Documentation

## Health Check
**GET** `/api/health`

Returns server health status.

**Query Parameters:**
- `format` (optional): Response format (`json`, `text`, `html`)

**Response (200 OK):**
```json
{
  "status": "healthy",
  "timestamp": "2026-02-06T10:30:00.000Z",
  "uptime": "125 seconds"
}
```
```

---

## 📊 Self-Assessment Checklist

After completing challenges, you should be able to:

- [ ] Create and organize Express routes
- [ ] Write custom middleware functions
- [ ] Handle different HTTP methods (GET, POST)
- [ ] Access request data (query params, body, headers)
- [ ] Send appropriate status codes
- [ ] Structure a project professionally
- [ ] Use environment variables
- [ ] Handle 404 errors
- [ ] Log and debug requests
- [ ] Work with different response formats

---

## 🎯 Next Steps

1. Commit your code to GitHub
2. Test all endpoints with Postman/Insomnia
3. Review the code with your pair partner
4. Read ahead: Next week covers authentication!

---

## 💡 Debugging Tips

**Server won't start:**
- Check if port is already in use
- Verify `.env` file exists
- Run `npm install` again

**Routes not found:**
- Check route registration order
- Verify path matches exactly
- Check for typos in file names

**Middleware not working:**
- Ensure it's registered before routes
- Check if you called `next()`
- Verify function signature is correct

**Need Help?**
- Review lesson plan
- Check solution code
- Ask in [community channel]
- Attend office hours

---

## 📚 Additional Resources

- [Express.js Official Docs](https://expressjs.com/)
- [MDN HTTP Guide](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [REST API Tutorial](https://restfulapi.net/)
