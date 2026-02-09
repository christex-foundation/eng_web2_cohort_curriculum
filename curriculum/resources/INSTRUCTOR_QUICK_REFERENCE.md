# Instructor Quick Reference Guide

## 🎯 Teaching Philosophy

**Core Principle:** Teach concepts first, then use AI to implement faster

Students should understand:
- **WHY** something works (concepts)
- **WHAT** it does (functionality)
- **HOW** to modify it (practical skills)

Don't let AI become a "magic box" - always explain generated code!

---

## ⏱️ Time Management

### Typical Session Structure (2 hours)

```
0:00 - 0:10  Welcome, recap previous week
0:10 - 0:40  Concept teaching (slides + whiteboard)
0:40 - 0:50  Break
0:50 - 1:20  Live demo + AI code generation
1:20 - 1:50  Hands-on practice
1:50 - 2:00  Wrap-up, Q&A, homework
```

### If Running Behind:
- ⏩ Skip "bonus slides"
- ⏩ Assign some challenges as homework
- ⏩ Provide recorded demo instead of live

### If Running Ahead:
- ✨ Deeper dive into questions
- ✨ Live debugging session
- ✨ Advanced challenge preview
- ✨ Industry stories/examples

---

## 💡 Teaching Tips by Week

### Week 1: Project Setup
**Watch Out For:**
- Port conflicts (common!)
- Path issues (Windows vs Mac)
- Git not configured
- Node version mismatches

**Pro Tips:**
- Have backup ports ready (3001, 3002)
- Show how to check Node version
- Do Git config check first
- Have them test health endpoint immediately

**Common Questions:**
- "Why so many files for hello world?" → Building for scale
- "Can I use TypeScript?" → Yes, but focus on concepts first
- "What's middleware?" → Checkpoint/security guard metaphor works well

---

### Week 2: Authentication
**Watch Out For:**
- JWT_SECRET not set in .env
- Database not created
- Confusion between hashing and encryption
- Password validation frustration

**Pro Tips:**
- Show password breach example (makes it real)
- Demonstrate JWT at jwt.io (visual!)
- Have preset database ready for demos
- Emphasize "same error message" security pattern

**Common Questions:**
- "Why not just send password each time?" → Great teaching moment!
- "What if token is stolen?" → Discuss token storage, HTTPS
- "How long should tokens last?" → Depends on use case discussion

**Live Demo Must-Do:**
- Actually hash a password and show bcrypt output
- Create a JWT and decode it
- Show what happens with wrong password

---

### Weeks 3-4: CRUD & Relationships
**Watch Out For:**
- Foreign key confusion
- Authorization vs authentication mixing
- Soft delete concept unclear
- Pagination off-by-one errors

**Pro Tips:**
- Draw database relationships on whiteboard
- Use real-world analogies (library card = foreign key)
- Test authorization by trying to edit someone else's data
- Show pagination with actual large dataset

---

### Weeks 5-6: Complex Features
**Watch Out For:**
- Junction table confusion
- Search performance issues
- Over-complicated queries
- Index misunderstanding

**Pro Tips:**
- Use Venn diagrams for many-to-many
- Show slow query first, then add index and show improvement
- Pagination: actually show 100+ results to make it real

---

### Weeks 7-8: Files & Logging
**Watch Out For:**
- File path confusion
- Upload size limits hit unexpectedly
- Log rotation not understood
- Error handling missing

**Pro Tips:**
- Upload a real file during demo
- Show what happens when file is too large
- Tail logs in terminal during requests
- Intentionally cause errors to show logging

---

### Weeks 9-10: Security & Performance
**Watch Out For:**
- Rate limiting too strict during testing
- Cache invalidation confusion
- Security concepts overwhelming
- Redis not running

**Pro Tips:**
- Demonstrate an actual XSS attack (safe example)
- Show rate limit triggering
- Cache hit vs miss with performance timing
- Use memory cache if Redis setup is problematic

---

### Weeks 11-12: Events & Deployment
**Watch Out For:**
- Event emitter pattern unclear
- Webhook signature confusion
- Deployment anxiety
- Docker issues on Windows

**Pro Tips:**
- Use real-life event examples (doorbell = event)
- Actually send a webhook to webhook.site
- Do a live deployment if possible
- Have VM ready if Docker fails

---

## 🎤 Presentation Skills

### Whiteboard Techniques

**Request Flow Diagrams:**
```
Client → Server → [Router] → [Controller] → [Service] → [Repository] → Database
                                                                    ↓
Client ← Server ← [Controller] ← [Service] ← [Repository] ← ← ← ← ←
```

Draw with colors:
- Blue: Client/external
- Green: Your code
- Orange: Database
- Red: Errors

### Live Coding Tips

**Do:**
- ✅ Explain what you're about to do first
- ✅ Type at learnable speed (not blazing fast)
- ✅ Make intentional mistakes and fix them
- ✅ Think out loud ("Now I need to...")
- ✅ Ask students to predict what happens next

**Don't:**
- ❌ Copy-paste large blocks without explaining
- ❌ Skip errors ("I'll fix this later")
- ❌ Use shortcuts without explaining them
- ❌ Assume everyone's following

### Using AI in Teaching

**Best Practices:**
1. Read the prompt out loud before submitting
2. Explain why the prompt is structured that way
3. Wait for code generation to complete
4. Review generated code FILE BY FILE with class
5. Ask students: "What does this line do?"

**Don't Just:**
- Run AI prompt → "It works!" → Move on
- Students need to understand WHAT was generated

---

## 🐛 Debugging in Class

### When Student Has Error

**Step 1:** Stay calm, it's a teaching moment!

**Step 2:** Ask diagnostic questions:
- "What were you trying to do?"
- "What did you expect to happen?"
- "What actually happened?"
- "Can you show me the error message?"

**Step 3:** Model debugging process:
- "Let's read the error together..."
- "The error says line 42, let's look there"
- "What does this error mean in plain English?"

**Step 4:** Guide them to solution:
- Don't just fix it - guide them
- "What do you think might fix this?"
- "Let's try X and see what happens"

### Common Errors & Quick Fixes

| Error | Likely Cause | Quick Fix |
|-------|--------------|-----------|
| "Cannot find module" | Didn't run npm install | `npm install` |
| "Port already in use" | Server already running | Kill process or change port |
| "Cannot connect to database" | DB not running or wrong URL | Check PostgreSQL, verify DATABASE_URL |
| "Invalid token" | Token expired or wrong secret | Generate new token or check JWT_SECRET |
| "404 Not Found" | Route not registered | Check routes/index.js |
| "Undefined is not a function" | Missing import or typo | Check imports at top of file |

---

## 📊 Student Progress Monitoring

### Red Flags (Student Struggling)

- 🚩 Not asking questions
- 🚩 Falling behind on challenges
- 🚩 Code doesn't run
- 🚩 Can't explain their code
- 🚩 Missing sessions

**Action:**
1. Private check-in
2. Offer extra office hours
3. Pair with stronger student
4. Provide additional resources
5. Consider slower pace

### Green Flags (Student Excelling)

- ✅ Completing advanced challenges
- ✅ Helping other students
- ✅ Asking "what if" questions
- ✅ Adding extra features
- ✅ Exploring beyond curriculum

**Action:**
1. Provide stretch goals
2. Invite to help teach
3. Suggest advanced resources
4. Encourage open source contributions

---

## 💬 Handling Questions

### The "Why?" Questions

**Student:** "Why do we need repositories? Can't we just query in controllers?"

**Good Answer:**
"Great question! Let's say you need to switch from PostgreSQL to MongoDB. With repositories, you only change one file. Without them, you'd change every controller. Also, repositories make testing way easier. Want me to show you?"

**Bad Answer:**
"Because that's the pattern we're using."

### The "Can I Use X Instead?" Questions

**Student:** "Can I use TypeScript instead of JavaScript?"

**Good Answer:**
"Absolutely! TypeScript is great. The concepts we're learning apply to both. For this cohort, we're using JavaScript so everyone's on the same page, but I encourage you to try converting your project to TypeScript as a challenge."

**Bad Answer:**
"No, stick with the curriculum."

### The "This Is Too Hard" Questions

**Student:** "I don't understand JWT at all."

**Good Answer:**
"That's totally normal - JWT is confusing at first! Let's break it down. What specifically is confusing? The structure? How it's verified? Let's tackle one piece at a time."

**Bad Answer:**
"Just use the code that was generated."

---

## 🎯 Week-Specific Gotchas

### Week 1
- ⚠️ Students might not have Node installed correctly
- ⚠️ .env file confusion (why is it hidden?)
- ⚠️ "It works on my machine" syndrome begins

### Week 2
- ⚠️ Database setup can take 30+ minutes if issues arise
- ⚠️ Password hashing is SLOW (10 rounds) - this is intentional!
- ⚠️ JWT payload is NOT encrypted (security misconception)

### Week 3
- ⚠️ Soft delete concept needs clear explanation
- ⚠️ Authorization rules get complex - draw tables

### Week 4-5
- ⚠️ Foreign keys can be mind-bending for beginners
- ⚠️ CASCADE delete conversations get philosophical

### Week 6
- ⚠️ Performance optimization is abstract without seeing real data
- ⚠️ Pagination math is error-prone

### Week 7
- ⚠️ File uploads fail silently if directory doesn't exist
- ⚠️ Path issues on Windows vs Mac

### Week 8
- ⚠️ Log files can fill up disk if not careful
- ⚠️ Error handling middleware ORDER matters

### Week 9
- ⚠️ Rate limiting will frustrate during testing
- ⚠️ CORS errors are confusing

### Week 10
- ⚠️ Redis adds deployment complexity
- ⚠️ Cache invalidation is "the hardest problem in CS"

### Week 11
- ⚠️ Events feel "magical" - show the internals
- ⚠️ Webhooks need external receiver for testing

### Week 12
- ⚠️ Deployment stress is real
- ⚠️ Swagger docs can be tedious to write

---

## 🎭 Teaching Personas

### The Explainer (Lecture Mode)
- Use during concept teaching
- Slower pace, more detail
- Check for understanding frequently

### The Guide (Live Coding)
- "Let's try this together"
- Think out loud
- Embrace mistakes

### The Cheerleader (Practice Time)
- Walk around, encourage
- "Great question!"
- "You're on the right track"

### The Debugger (Problem Solving)
- Patient, methodical
- Ask guiding questions
- Celebrate when they find the bug

---

## 📝 After-Class Checklist

- [ ] Answer questions in forum/Slack
- [ ] Review any code submitted early
- [ ] Prepare next week's materials
- [ ] Update curriculum based on what worked/didn't
- [ ] Schedule office hours for struggling students
- [ ] Test next week's AI prompts
- [ ] Backup your demo code

---

## 🆘 Emergency Backup Plans

### "My Demo Isn't Working"
- Have pre-built version ready
- Show from GitHub instead
- "Let's debug this together" (teaching moment!)

### "Server Is Down" (Hosting Issues)
- Have local demo ready
- Use localhost for all demos
- Screen record demos as backup

### "Student's Environment Is Broken"
- Have Docker container ready
- Pair them with someone whose works
- Use GitHub Codespaces as backup

### "I'm Sick/Emergency"
- Pre-record key concepts
- Have substitute instructor guide
- Move to async for that week

---

## 🌟 Making It Memorable

### Week 1: "The Light Bulb Moment"
When health endpoint returns JSON - they built an API!

### Week 2: "The Security Revelation"
Show them haveibeenpwned - password security becomes REAL

### Week 6: "The Performance Win"
Query goes from 2s to 200ms with index - they feel like hackers

### Week 10: "The Cache Magic"
Response time drops dramatically - optimization is tangible

### Week 12: "The Pride Moment"
They present their working API - celebrate this!

---

## 📚 Recommended Reading for You

- "The Pragmatic Programmer" - Teaching edition
- "Teaching Tech Together" by Greg Wilson
- "Make It Stick" - Learning science
- Express.js docs (stay current!)
- Security blogs (OWASP)

---

## 🤝 Building Community

- Create Slack/Discord with channels per week
- Encourage students to help each other
- Share wins publicly
- Do code reviews as group activity
- Alumni network for after cohort

---

## ✨ Final Tips

1. **Energy Management**: Teaching is exhausting - take breaks
2. **Adaptation**: No plan survives contact with students - be flexible
3. **Patience**: Some concepts take multiple explanations
4. **Celebration**: Celebrate small wins and progress
5. **Feedback**: Ask for feedback regularly and adapt
6. **Authenticity**: Share your own learning struggles
7. **Passion**: Your excitement is contagious!

---

**Remember:** You're not just teaching code - you're empowering careers. Every student who "gets it" is a win. Some will struggle, some will soar, but all will learn.

**You've got this! 🚀**
