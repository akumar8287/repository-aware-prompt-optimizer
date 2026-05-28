# Task Classification Reference

## Task Types

### 1. Bug Fix

**Definition:** Something that worked before is broken, or something that should work does not.

**Detection signals:**
- Words: fix, broken, error, not working, failing, issue, crash, exception, 500, 404, undefined, null
- Pattern: describes unexpected behavior with implicit expectation of previous/correct behavior
- Hinglish: "kaam nahi kar raha", "error aa raha hai", "crash ho gaya", "nahi chal raha"

**Examples:**
- "login not working bro fix it"
- "payment button click karo toh kuch nahi hota"
- "API returns 500 on POST /users"
- "TypeError: cannot read property of undefined on dashboard load"
- "prod pe kuch toot gaya hai deploy ke baad"

---

### 2. Feature Request

**Definition:** Add new functionality that does not currently exist.

**Detection signals:**
- Words: add, create, implement, build, new, feature, allow, enable, support
- Pattern: describes behavior that is absent, not broken
- Hinglish: "add karo", "banana hai", "chahiye", "implement karo"

**Examples:**
- "add dark mode to settings page"
- "export button banana hai reports mein"
- "user ko email notification bhejo jab order place ho"
- "implement role-based access for admin panel"

---

### 3. UI / Frontend Change

**Definition:** Visual or interaction changes to the user interface. No backend logic change required.

**Detection signals:**
- Words: button, page, layout, style, color, component, CSS, responsive, modal, form, UI, UX, design, render
- File mentions: `.tsx`, `.vue`, `.svelte`, `.css`, `.scss`, component names
- Hinglish: "dikhna chahiye", "style change karo", "page theek karo", "button sahi karo"

**Examples:**
- "sidebar menu items ka font size bada karo"
- "login form center mein nahi aa raha mobile pe"
- "add loading spinner to submit button"
- "dashboard card hover effect lagao"

---

### 4. Backend / API Change

**Definition:** Server-side logic, API endpoint, business logic, or service layer change.

**Detection signals:**
- Words: API, endpoint, route, controller, service, handler, server, request, response, middleware, validation
- Hinglish: "backend pe karo", "API mein change karo", "route add karo"

**Examples:**
- "add pagination to GET /products endpoint"
- "validate email format in user registration API"
- "rate limiting lagao login route pe"
- "user profile update API mein phone number field add karo"

---

### 5. Database / Schema Change

**Definition:** Changes to database tables, models, columns, indexes, or relationships.

**Detection signals:**
- Words: table, column, field, schema, model, migration, index, foreign key, relation, database, DB
- Hinglish: "database mein column add karo", "schema change karo"

**Examples:**
- "add `last_login` column to users table"
- "orders aur products ka relation banana hai"
- "index lagao email field pe for faster lookup"
- "nullable karo `bio` field in profile model"

---

### 6. Migration

**Definition:** Move, upgrade, or transform code, data, infrastructure, or dependencies to a new version or system.

**Detection signals:**
- Words: migrate, upgrade, version, from X to Y, replace, convert, move, update package, switch
- Hinglish: "migrate karna hai", "upgrade karo", "purana wala hatao naya lagao"

**Examples:**
- "migrate from CommonJS to ESM"
- "React 17 to React 18 upgrade karna hai"
- "Mongoose 5 se 7 pe upgrade karo"
- "old REST API ko GraphQL mein convert karo"

---

### 7. Refactor

**Definition:** Restructure or clean up existing code without changing behavior.

**Detection signals:**
- Words: refactor, clean up, restructure, reorganize, rename, split, extract, simplify, DRY, remove duplication
- Hinglish: "code saaf karo", "restructure karo", "clean karo"
- Key signal: user explicitly says behavior must not change

**Examples:**
- "auth logic ek jagah se extract karo middleware mein"
- "duplicate validation code ko common function mein nikaalo"
- "long controller file ko service layer mein split karo"

---

### 8. Deployment / Config Issue

**Definition:** Build, CI/CD, environment, Docker, config file, or deployment-related problem.

**Detection signals:**
- Words: build, deploy, Docker, CI/CD, env, config, `.env`, pipeline, YAML, Dockerfile, crash on startup
- Hinglish: "deploy fail ho raha hai", "build nahi ho raha", "prod pe chal nahi raha"

**Examples:**
- "Docker build fail ho raha hai layer caching ke baad"
- "GitHub Actions mein test step fail kar raha hai"
- "prod env mein DB connection nahi ho raha, local pe chal raha hai"
- "app start nahi ho raha, .env missing variable hai shayad"

---

### 9. Unclear Request

**Definition:** Prompt is too vague, contradictory, or missing key information to classify confidently.

**Detection signals:**
- No error, no file, no specific feature, no context
- One-word or two-word prompts with no elaboration
- Multiple unrelated concerns in one message
- Hinglish: "kuch sahi nahi hai", "dekh lena", "pata nahi kya problem hai"

**Examples:**
- "fix everything"
- "make it better"
- "something wrong with app"
- "kuch toh karo yaar"
- "notifications nahi aa rahi" (no app context, no platform, no type)

**Action:** Generate a clarification prompt instead of an implementation prompt. Ask for: task type, affected feature, error message (if any), expected vs actual behavior.

---

## Classification Priority

When multiple task types seem to apply, use this priority:

1. If user mentions a specific error → **Bug Fix** (investigate root cause first)
2. If user mentions a specific new behavior → **Feature Request**
3. If user mentions database/schema → **Database Change** (even if it involves API changes)
4. If user mentions migration/upgrade → **Migration**
5. If user only mentions visual/UI → **UI/Frontend**
6. If nothing concrete → **Unclear Request**

---

## Compound Tasks

If a request spans two types (e.g., "add dark mode AND fix login bug"), split them into two separate optimized prompts. Label them clearly:

```
## Prompt 1 of 2 — Bug Fix: Login
...

## Prompt 2 of 2 — Feature: Dark Mode
...
```
