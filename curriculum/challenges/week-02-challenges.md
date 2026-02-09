# Week 2 Challenges: User Authentication System

## Challenge 1: Password Strength Validator 🟢

### Objective
Add password strength validation to registration.

### Requirements
Password must:
- Be at least 8 characters
- Contain at least 1 uppercase letter
- Contain at least 1 lowercase letter
- Contain at least 1 number
- Contain at least 1 special character (@$!%*?&)

### Where to Implement
Update `src/validations/auth.validation.js`

### Hints
Use Zod's `.regex()` method or custom `.refine()`

<details>
<summary>Solution</summary>

```javascript
// src/validations/auth.validation.js
import { z } from 'zod';

const passwordSchema = z
  .string()
  .min(8, 'Password must be at least 8 characters')
  .regex(/[A-Z]/, 'Password must contain at least one uppercase letter')
  .regex(/[a-z]/, 'Password must contain at least one lowercase letter')
  .regex(/[0-9]/, 'Password must contain at least one number')
  .regex(/[@$!%*?&]/, 'Password must contain at least one special character');

export const registerSchema = z.object({
  email: z.string().email('Invalid email format'),
  password: passwordSchema,
  role: z.enum(['founder', 'investor', 'admin']).optional(),
});
```
</details>

---

## Challenge 2: Logout Endpoint 🟢

### Objective
Add logout functionality (client-side token clearing).

### Requirements
- Create `POST /api/auth/logout` endpoint
- Return success message
- Document that client must delete token

### Note
With JWT, logout is client-side (delete token from storage). But we return a success response for consistency.

<details>
<summary>Solution</summary>

```javascript
// src/controllers/auth.controller.js
async logout(req, res) {
  // JWT is stateless, so logout is client-side
  // Client should remove token from localStorage/cookies
  res.json({
    message: 'Logged out successfully',
    note: 'Please remove token from client storage'
  });
}

// src/routes/auth.routes.js
router.post('/logout', authenticate, authController.logout);
```
</details>

---

## Challenge 3: Refresh Token System 🟡

### Objective
Implement refresh tokens for better security.

### Requirements
- Access token expires in 15 minutes
- Refresh token expires in 7 days
- Create `POST /api/auth/refresh` endpoint
- Store refresh tokens in database
- Return new access token when refresh token is valid

### Database Changes Needed
Add `refreshTokens` table:
- id, userId, token, expiresAt, createdAt

<details>
<summary>Solution</summary>

```javascript
// drizzle/schema/refreshTokens.schema.js
export const refreshTokens = pgTable('refresh_tokens', {
  id: uuid('id').defaultRandom().primaryKey(),
  userId: uuid('user_id').references(() => users.id).notNull(),
  token: varchar('token', { length: 500 }).notNull().unique(),
  expiresAt: timestamp('expires_at').notNull(),
  createdAt: timestamp('created_at').defaultNow(),
});

// src/services/auth.service.js
async login(email, password) {
  // ... existing login logic ...

  // Generate both tokens
  const accessToken = this.generateAccessToken(user);
  const refreshToken = this.generateRefreshToken();

  // Store refresh token in DB
  await refreshTokenRepository.create({
    userId: user.id,
    token: refreshToken,
    expiresAt: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000), // 7 days
  });

  return {
    user: this.sanitizeUser(user),
    accessToken,
    refreshToken,
  };
}

async refreshAccessToken(refreshToken) {
  // Verify refresh token exists and not expired
  const storedToken = await refreshTokenRepository.findByToken(refreshToken);
  if (!storedToken || storedToken.expiresAt < new Date()) {
    throw new Error('Invalid or expired refresh token');
  }

  // Get user
  const user = await userRepository.findById(storedToken.userId);

  // Generate new access token
  const accessToken = this.generateAccessToken(user);

  return { accessToken };
}

generateAccessToken(user) {
  return jwt.sign(
    { userId: user.id, email: user.email, role: user.role },
    config.jwtSecret,
    { expiresIn: '15m' }
  );
}

generateRefreshToken() {
  return jwt.sign(
    { type: 'refresh' },
    config.jwtRefreshSecret,
    { expiresIn: '7d' }
  );
}

// src/routes/auth.routes.js
router.post('/refresh', authController.refresh);
```
</details>

---

## Challenge 4: Email Verification 🟡

### Objective
Require email verification before account is active.

### Requirements
- Add `emailVerified` boolean to users table
- Add `verificationToken` to users table
- Send verification email on registration (simulate with console.log)
- Create `POST /api/auth/verify-email/:token` endpoint
- Prevent unverified users from logging in

<details>
<summary>Solution</summary>

```javascript
// Update drizzle/schema/users.schema.js
export const users = pgTable('users', {
  // ... existing fields ...
  emailVerified: boolean('email_verified').default(false),
  verificationToken: varchar('verification_token', { length: 100 }),
});

// src/services/auth.service.js
async register(email, password, role) {
  const verificationToken = crypto.randomBytes(32).toString('hex');

  const hashedPassword = await bcrypt.hash(password, 10);

  const user = await userRepository.create({
    email,
    password: hashedPassword,
    role,
    emailVerified: false,
    verificationToken,
  });

  // Simulate sending email
  console.log(`Verification link: http://localhost:3000/api/auth/verify-email/${verificationToken}`);

  const token = this.generateToken(user);
  return { user: this.sanitizeUser(user), token };
}

async verifyEmail(token) {
  const user = await userRepository.findByVerificationToken(token);
  if (!user) {
    throw new Error('Invalid verification token');
  }

  await userRepository.update(user.id, {
    emailVerified: true,
    verificationToken: null,
  });

  return { message: 'Email verified successfully' };
}

async login(email, password) {
  const user = await userRepository.findByEmail(email);
  if (!user) {
    throw new Error('Invalid credentials');
  }

  if (!user.emailVerified) {
    throw new Error('Please verify your email before logging in');
  }

  // ... rest of login logic ...
}
```
</details>

---

## Challenge 5: Password Reset Flow 🔴

### Objective
Implement forgot password and reset password functionality.

### Requirements
1. `POST /api/auth/forgot-password`
   - Takes email
   - Generates reset token
   - "Sends" reset email (console.log)
   - Token expires in 1 hour

2. `POST /api/auth/reset-password`
   - Takes token and new password
   - Validates token
   - Updates password
   - Invalidates token

### Database Changes
Add to users table:
- resetPasswordToken
- resetPasswordExpires

<details>
<summary>Solution</summary>

```javascript
// Update users schema
export const users = pgTable('users', {
  // ... existing fields ...
  resetPasswordToken: varchar('reset_password_token', { length: 100 }),
  resetPasswordExpires: timestamp('reset_password_expires'),
});

// src/services/auth.service.js
async forgotPassword(email) {
  const user = await userRepository.findByEmail(email);
  if (!user) {
    // Don't reveal if email exists
    return { message: 'If email exists, reset link has been sent' };
  }

  const resetToken = crypto.randomBytes(32).toString('hex');
  const resetExpires = new Date(Date.now() + 60 * 60 * 1000); // 1 hour

  await userRepository.update(user.id, {
    resetPasswordToken: resetToken,
    resetPasswordExpires: resetExpires,
  });

  // Simulate sending email
  console.log(`Password reset link: http://localhost:3000/reset-password?token=${resetToken}`);

  return { message: 'If email exists, reset link has been sent' };
}

async resetPassword(token, newPassword) {
  const user = await userRepository.findByResetToken(token);
  if (!user || user.resetPasswordExpires < new Date()) {
    throw new Error('Invalid or expired reset token');
  }

  const hashedPassword = await bcrypt.hash(newPassword, 10);

  await userRepository.update(user.id, {
    password: hashedPassword,
    resetPasswordToken: null,
    resetPasswordExpires: null,
  });

  return { message: 'Password reset successfully' };
}

// src/controllers/auth.controller.js
async forgotPassword(req, res, next) {
  try {
    const { email } = req.body;
    const result = await authService.forgotPassword(email);
    res.json(result);
  } catch (error) {
    next(error);
  }
}

async resetPassword(req, res, next) {
  try {
    const { token, password } = req.body;
    const result = await authService.resetPassword(token, password);
    res.json(result);
  } catch (error) {
    next(error);
  }
}

// src/routes/auth.routes.js
router.post('/forgot-password', authController.forgotPassword);
router.post('/reset-password', authController.resetPassword);
```
</details>

---

## Challenge 6: Rate Limiting on Auth Endpoints 🔴

### Objective
Prevent brute force attacks by rate limiting login attempts.

### Requirements
- Limit login attempts to 5 per 15 minutes per IP
- Return 429 status when limit exceeded
- Use express-rate-limit package

<details>
<summary>Solution</summary>

```bash
npm install express-rate-limit
```

```javascript
// src/middleware/rate-limit.middleware.js
const rateLimit = require('express-rate-limit');

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // 5 requests per window
  message: 'Too many login attempts, please try again later',
  standardHeaders: true,
  legacyHeaders: false,
});

const registerLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 3, // 3 registrations per hour
  message: 'Too many accounts created, please try again later',
});

module.exports = { loginLimiter, registerLimiter };

// src/routes/auth.routes.js
const { loginLimiter, registerLimiter } = require('../middleware/rate-limit.middleware');

router.post('/login', loginLimiter, authController.login);
router.post('/register', registerLimiter, authController.register);
```
</details>

---

## Challenge 7: Role-Based Authorization Middleware 🟡

### Objective
Test the authorize middleware with different roles.

### Requirements
1. Create a test route `/api/admin/users` that only admins can access
2. Create a test route `/api/founder/startups` that only founders can access
3. Test with different user roles

<details>
<summary>Solution</summary>

```javascript
// src/controllers/test.controller.js
class TestController {
  async adminOnly(req, res) {
    res.json({
      message: 'Welcome admin!',
      user: req.user,
    });
  }

  async founderOnly(req, res) {
    res.json({
      message: 'Welcome founder!',
      user: req.user,
    });
  }
}

module.exports = new TestController();

// src/routes/test.routes.js
const express = require('express');
const router = express.Router();
const { authenticate, authorize } = require('../middleware/auth.middleware');
const testController = require('../controllers/test.controller');

router.get('/admin-only', authenticate, authorize('admin'), testController.adminOnly);
router.get('/founder-only', authenticate, authorize('founder'), testController.founderOnly);

module.exports = router;

// Register in src/routes/index.js
app.use('/api/test', testRoutes);
```

**Test:**
```bash
# As founder (should fail with 403)
curl http://localhost:3000/api/test/admin-only \
  -H "Authorization: Bearer [FOUNDER_TOKEN]"

# As admin (should succeed)
curl http://localhost:3000/api/test/admin-only \
  -H "Authorization: Bearer [ADMIN_TOKEN]"
```
</details>

---

## 📊 Self-Assessment

- [ ] Understand bcrypt hashing and salt
- [ ] Can create and verify JWT tokens
- [ ] Know the difference between 401 and 403
- [ ] Can implement authentication middleware
- [ ] Can implement authorization middleware
- [ ] Understand token expiration
- [ ] Can validate inputs with Zod
- [ ] Know how to handle authentication errors

---

## 🎯 Next Week Preview

We'll build user profile management:
- GET /api/users/:id
- PATCH /api/users/:id
- DELETE /api/users/:id
- List users with pagination
- Role-based access (users can only edit themselves, admins can edit anyone)

**Prepare:** Review HTTP PATCH vs PUT, soft delete pattern
