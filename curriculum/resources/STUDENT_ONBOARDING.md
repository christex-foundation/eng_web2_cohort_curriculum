# Student Onboarding Guide
## Welcome to Backend From First Principles!

---

## 🎉 Welcome!

Congratulations on joining this cohort! Over the next 12 weeks, you'll go from backend beginner to building production-ready APIs with confidence.

**What You'll Build:**
A complete startup platform API called **StartupHub** where founders can list their startups and investors can express interest.

By week 12, you'll have:
- ✅ A portfolio project to show employers
- ✅ Deep understanding of backend fundamentals
- ✅ Experience with modern tools and AI coding assistants
- ✅ Confidence to build any backend system

---

## 📋 Pre-Cohort Checklist

Complete these BEFORE the first session:

### 1. Software Installation ✅

#### Required:

**Node.js (v20 or higher)**
- Download: https://nodejs.org/
- Verify installation:
```bash
node --version   # Should show v20.x.x or higher
npm --version    # Should show 10.x.x or higher
```

**Git**
- Download: https://git-scm.com/
- Verify:
```bash
git --version   # Should show 2.x.x or higher
```

**Cursor or Windsurf IDE**
- Cursor: https://cursor.sh/
- Windsurf: https://codeium.com/windsurf
- Choose ONE (Cursor recommended for beginners)

**PostgreSQL (v15 or higher)**
- Mac: `brew install postgresql@15`
- Windows: https://www.postgresql.org/download/windows/
- Verify:
```bash
psql --version   # Should show 15.x or higher
```

**API Testing Tool (Choose One)**
- Postman: https://www.postman.com/downloads/
- Insomnia: https://insomnia.rest/download
- Bruno: https://www.usebruno.com/

#### Recommended:

**Redis (Optional - for Week 10)**
- Mac: `brew install redis`
- Windows: https://redis.io/download

**Docker Desktop (Optional - for Week 12)**
- Download: https://www.docker.com/products/docker-desktop

---

### 2. Account Setup ✅

**GitHub Account**
- Create at: https://github.com/
- Why: You'll push your code weekly

**Create Your Project Repository**
```bash
# On GitHub, create a new repository called "startup-hub-api"
# Make it public (good for portfolio!)
```

---

### 3. Environment Test ✅

Run this test to ensure everything works:

```bash
# Test Node.js
node -e "console.log('Node.js works!')"

# Test npm
npm -v

# Test PostgreSQL
psql --version

# Create test database
createdb test_db
dropdb test_db

# Test Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

If all commands work, you're ready! 🎉

---

### 4. Join Community ✅

**Slack/Discord**
- Join link: [PROVIDED BY INSTRUCTOR]
- Channels:
  - #general - General discussion
  - #help - Ask questions
  - #wins - Share your progress
  - #resources - Additional materials
  - #week-X - Week-specific discussion

**Introduce Yourself!**
Post in #general:
- Your name
- Your background
- Why you're learning backend
- What you hope to build

---

## 📚 What to Expect

### Weekly Structure

**Before Each Week:**
- 📖 Review lesson plan overview (15 min)
- 🎥 Watch any prep materials (optional)

**Session 1 (2 hours):**
- Concept teaching
- Live demos
- Q&A

**Session 2 (2 hours):**
- AI-assisted coding
- Build the feature together
- Start challenges

**Between Sessions:**
- Complete challenges (2-4 hours)
- Review and understand code
- Ask questions in community

**Total Time Commitment:** 8-10 hours/week

---

### Grading & Assessment

**Weekly (70%):**
- Challenge completion: 40%
- Code quality: 20%
- Participation: 10%

**Final Project (30%):**
- Capstone presentation: 15%
- Code review: 15%

**To Pass:** 70% overall

---

## 🎯 Learning Approach

### The "Concept → AI → Understanding" Method

**1. Concept (Session 1)**
- Instructor teaches the "why"
- Whiteboard diagrams
- Real-world examples

**2. AI-Assisted Build (Session 2)**
- Use provided prompts
- AI generates code
- Build feature quickly

**3. Understanding (Your Job!)**
- **DON'T** just copy-paste and move on
- **DO** read every line of generated code
- **DO** ask "why is this here?"
- **DO** modify and experiment

### The AI is a Tool, Not Magic

**Good Use:**
- "Generate a user schema with email and password fields"
- "Create a JWT authentication middleware"
- "Add rate limiting to login endpoint"

**Learning Anti-Pattern:**
- Generate code → It works → Move on ❌
- Copy error message to AI → Paste fix → Don't understand why ❌

**Better:**
- Generate code → Read it line by line → Understand it ✅
- Error occurs → Try to debug yourself first → Ask AI to explain ✅

---

## 💪 Success Tips

### 1. Stay Current
Don't fall behind! Each week builds on the previous.

**If You Miss a Session:**
- Watch recording (if available)
- Review lesson plan
- Complete AI prompt on your own
- Attend office hours

### 2. Ask Questions
**There are no stupid questions!**

Good questions:
- "I don't understand why we need middleware. Can you explain again?"
- "My code returns 404. I've checked X, Y, Z. What else should I look at?"
- "Can you explain the difference between authentication and authorization?"

Less helpful:
- "It doesn't work" (too vague)
- Send code without explaining what you tried

### 3. Debug Before Asking

**Before posting "help":**
1. Read the error message carefully
2. Google the error
3. Check your code against the example
4. Try changing one thing at a time
5. Then ask with context!

### 4. Use GitHub

**Commit often:**
```bash
git add .
git commit -m "Complete week 2 authentication"
git push
```

**Why:**
- Backup your work
- Track your progress
- Share code easily when asking for help
- Build your portfolio

### 5. Help Others

**Teaching solidifies learning!**

When you understand a concept:
- Answer questions in Slack
- Help debug peer's code
- Share resources you found helpful

### 6. Build Beyond the Curriculum

**Add Your Own Features:**
- Week 4: Add startup categories
- Week 5: Add investment history
- Week 7: Add video uploads
- Be creative!

---

## 🛠️ Workspace Setup

### Organize Your Files

```
~/coding/
  └── backend-cohort/
      ├── startup-hub-api/       # Your main project
      ├── notes/                 # Your notes
      ├── experiments/           # Try new things here
      └── resources/             # Downloaded materials
```

### VS Code / Cursor Setup

**Essential Extensions:**
- ESLint
- Prettier
- REST Client (for testing APIs)
- Database viewer (PostgreSQL)

**Settings:**
- Auto-save: ON
- Format on save: ON
- Tab size: 2 spaces

### Keep a Learning Journal

Create `notes/LEARNING_JOURNAL.md`:
```markdown
# Week 1
## What I Learned:
- HTTP request/response cycle
- Express.js basics
- Middleware concept

## Challenges:
- Port was already in use - learned to change ports
- Confused about middleware order

## Aha Moments:
- Middleware is like a security checkpoint!

## Questions:
- When should I use PUT vs PATCH?
```

---

## 📖 Recommended Resources

### Essential Reading
- [MDN HTTP Guide](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [Express.js Docs](https://expressjs.com/)
- [JavaScript.info](https://javascript.info/) - Refresh JS basics

### When You're Stuck
- Stack Overflow (search first!)
- Express.js Discord
- PostgreSQL Documentation
- Our Slack #help channel

### Going Deeper (Optional)
- "Node.js Design Patterns" book
- "Web Security Academy" (PortSwigger)
- API design blogs

---

## 🚨 Common Issues & Solutions

### "Port 3000 Already in Use"

**Solution:**
```bash
# Find what's using the port
# Mac/Linux:
lsof -ti:3000 | xargs kill -9

# Windows:
netstat -ano | findstr :3000
taskkill /PID [PID_NUMBER] /F

# Or just change the port in .env
PORT=3001
```

### "Cannot Connect to Database"

**Solutions:**
```bash
# Is PostgreSQL running?
# Mac:
brew services start postgresql@15

# Windows:
# Start PostgreSQL service from Services app

# Does database exist?
createdb startuphub

# Can you connect?
psql -d startuphub
```

### "Module Not Found"

**Solution:**
```bash
# Did you install dependencies?
npm install

# Still broken? Try:
rm -rf node_modules package-lock.json
npm install
```

### "AI Generated Wrong Code"

**Solution:**
1. Don't panic!
2. Read the error message
3. Check the AI prompt - was it clear?
4. Regenerate with more specific prompt
5. Or manually fix (learning opportunity!)

---

## 🎯 Week-by-Week Expectations

### Week 1: "I Built an API!"
You'll feel accomplished when your first endpoint works!

### Week 2: "Security is Hard"
Password hashing and JWT will feel complex - that's normal.

### Week 3: "I'm Getting the Hang of This"
Patterns start to click.

### Week 4-5: "This is Challenging"
Many-to-many relationships are tough - push through!

### Week 6-7: "I'm Building Real Features"
Search and file uploads feel very real-world.

### Week 8-9: "I'm Thinking Like a Developer"
You start caring about logging and security.

### Week 10-11: "This is Production-Grade"
Caching and webhooks make it feel professional.

### Week 12: "I Built This!"
Pride moment. Celebrate! 🎉

---

## 🏆 Final Project Expectations

By Week 12, your API should have:
- ✅ All CRUD endpoints working
- ✅ Authentication & authorization
- ✅ File uploads functional
- ✅ Proper error handling
- ✅ Logging implemented
- ✅ Security hardening
- ✅ Caching in place
- ✅ API documentation (Swagger)
- ✅ Deployed somewhere
- ✅ README with setup instructions

**Presentation (15 minutes):**
- Architecture overview (5 min)
- Live demo (5 min)
- Challenges & learnings (5 min)
- Q&A

---

## 🤝 Code of Conduct

### Be Respectful
- Everyone starts somewhere
- No "well actually" corrections
- Assume good intent

### Be Helpful
- Share resources
- Help debug
- Celebrate others' wins

### Be Honest
- Don't plagiarize
- Ask for help when stuck
- Admit when you don't understand

### Be Present
- Attend sessions (or watch recordings)
- Participate in discussions
- Do the work

---

## 📞 Getting Help

### Levels of Support

**Level 1: Self-Help (Try First)**
- Review lesson materials
- Google the error
- Check documentation
- Read code comments

**Level 2: Community**
- Post in Slack #help
- Ask in session
- Pair with classmate

**Level 3: Instructor**
- Office hours (scheduled)
- Direct message (for private issues)
- Code review sessions

**Level 4: Technical Issues**
- Email [technical-support@email.com]

---

## ✅ Final Pre-Cohort Checklist

Before first session:
- [ ] All software installed
- [ ] Environment tested
- [ ] GitHub account created
- [ ] Joined Slack/Discord
- [ ] Introduced yourself
- [ ] Reviewed Week 1 overview (optional)
- [ ] Set up workspace
- [ ] Ready to learn!

---

## 🎊 You're Ready!

**Remember:**
- This is challenging - that's the point
- Everyone struggles sometimes
- The community is here to help
- You'll be amazed at what you build
- Enjoy the journey!

**See you in Week 1! Let's build something awesome! 🚀**

---

## 📧 Contact

**Instructor:** [Email]
**Technical Support:** [Email]
**Community Manager:** [Slack @handle]

**Office Hours:** [Schedule Link]
**Session Times:** [Schedule]

---

**Pro Tip:** Print this guide or bookmark it. You'll reference it throughout the cohort!

---

*Last Updated: February 2026*
*Version: 1.0*
