# Investigation Patterns

Per-task-type patterns for the "Inspect First" and "Investigation Order" sections.

---

## Auth / Login Bugs

**Likely areas to inspect first:**
- Auth route handler (`routes/auth.js`, `auth.routes.ts`, `api/auth/route.ts`)
- Auth controller (`auth.controller.ts`, `AuthController.java`)
- Auth service (`auth.service.ts`, `AuthService.js`)
- Passport strategy or JWT strategy (`jwt.strategy.ts`, `local.strategy.ts`, `passport.js`)
- Auth middleware (`middleware/auth.ts`, `middleware/authenticate.js`)
- Session config (`session.config.ts`, `express-session` setup in `app.ts`)
- Environment variables for `JWT_SECRET`, `SESSION_SECRET`, `OAUTH_CLIENT_ID`

**Investigation order:**
1. Reproduce the error and capture the exact error message from browser console or server logs.
2. Locate the login route handler — trace from route definition to handler function.
3. Follow the execution path through middleware, service, and strategy layers.
4. Check token signing/verification or session creation logic.
5. Verify environment variables required for auth are present.
6. Check for recent commits to auth files that may have introduced the regression.

---

## Dashboard / Route Bugs

**Likely areas to inspect first:**
- Dashboard page component (`pages/Dashboard.tsx`, `views/dashboard.vue`, `dashboard.component.ts`)
- Dashboard route definition (`routes/dashboard.js`, `app-routing.module.ts`)
- Data-fetching hooks or service calls inside the dashboard (`useDashboard.ts`, `dashboardService.ts`)
- Auth guard protecting the route (if redirect or 403 is the symptom)
- API endpoint that the dashboard calls for its data

**Investigation order:**
1. Identify exact symptom: blank page, redirect, data not loading, or component error.
2. Check browser console for JS errors or failed network requests.
3. If blank/redirect: inspect route guard, auth state, and route definition.
4. If data not loading: inspect the API call, response, and component state.
5. If component error: inspect the specific component and its props/data shape.

---

## Payment Bugs

**Likely areas to inspect first:**
- Payment route handler (`routes/payment.js`, `api/checkout/route.ts`)
- Payment service (`payment.service.ts`, `PaymentService.java`, `stripe.service.ts`)
- Webhook handler (`webhooks/stripe.ts`, `payment/webhook.js`)
- Order model/service (payment completion often updates order state)
- Environment variables for `STRIPE_SECRET_KEY`, `RAZORPAY_KEY_ID`, `PAYMENT_GATEWAY_URL`

**Investigation order:**
1. Identify the exact failure point: checkout initiation, payment confirmation, or webhook processing.
2. Check payment provider dashboard logs (Stripe, Razorpay, PayPal) for failed events.
3. Inspect the payment service for the failing operation.
4. If webhook-related: inspect the webhook handler and verify the signing secret.
5. Check environment variables for payment credentials.
6. Verify idempotency keys or duplicate-order guards if double-charge is the issue.

---

## API Bugs

**Likely areas to inspect first:**
- Route definition file for the affected endpoint
- Controller/handler for that route
- Request validation middleware (Joi, Zod, class-validator, express-validator)
- Service layer called by the controller
- Database query within the service (if data-related)

**Investigation order:**
1. Identify the HTTP method, path, and status code of the failing request.
2. Locate the route definition using grep for the path string.
3. Read the controller/handler function.
4. Check input validation — is the request body/params being validated?
5. Follow into the service layer if the controller delegates there.
6. Inspect the database query if the error is data-related.

---

## Database Bugs

**Likely areas to inspect first:**
- Model definition for the affected entity (`User.model.ts`, `Order.js`, `models/user.py`)
- Repository or query layer (`user.repository.ts`, `UserRepository.java`)
- Migration file that introduced the schema change
- Seed files if the issue is with test/dev data
- Database connection config (`database.config.ts`, `db.js`, `settings.py`)

**Investigation order:**
1. Identify whether the error is at query time, model definition time, or migration time.
2. For query errors: inspect the query in the repository/service and compare against the actual schema.
3. For migration errors: read the failing migration file and the current schema state.
4. For connection errors: inspect the DB connection config and verify environment variables.
5. Check for recent schema changes (migrations) that may have broken existing queries.

---

## Frontend UI Issues

**Likely areas to inspect first:**
- The specific component mentioned or implied (`Button.tsx`, `Modal.vue`, `Sidebar.component.ts`)
- The CSS/SCSS file scoped to that component or the global stylesheet if cascade is suspected
- The page/layout file that renders the component (to check props passed)
- Responsive breakpoints in the stylesheet or Tailwind config

**Investigation order:**
1. Identify the exact visual symptom: wrong style, broken layout, not rendering, wrong data displayed.
2. Locate the component file using glob for its name.
3. Inspect the component's render logic and conditional rendering.
4. Check the CSS scoped to the component.
5. If layout issue: inspect the parent layout component for container constraints.
6. If data display issue: trace the prop chain from the page to the component.

---

## Migration Tasks

**Likely areas to inspect first:**
- `package.json` (for dependency version changes)
- Entry files (`index.ts`, `app.ts`, `main.py`) for API surface changes
- Config files affected by the new version (`tsconfig.json`, `babel.config.js`, `vite.config.ts`)
- Breaking change documentation for the target version (user should provide or link it)
- The specific files that use the deprecated API

**Investigation order:**
1. Identify the exact "from" and "to" versions or systems.
2. Grep for all usages of the deprecated API, syntax, or pattern.
3. Inspect the config file changes required by the new version.
4. Update files in order from lowest-level (utilities, helpers) to highest-level (pages, routes).
5. Run type-checking or linting after each file to catch cascade errors early.

---

## Deployment / Build Issues

**Likely areas to inspect first:**
- `.env` and `.env.example` (compare keys present vs expected)
- `package.json` and `package-lock.json` / `yarn.lock` (for dependency issues)
- `Dockerfile` and `docker-compose.yml`
- CI/CD config: `.github/workflows/*.yml`, `Jenkinsfile`, `bitbucket-pipelines.yml`
- Build config: `vite.config.ts`, `webpack.config.js`, `tsconfig.json`, `next.config.js`
- Startup file (`server.js`, `index.ts`, `main.py`) for runtime startup crashes

**Investigation order:**
1. Read the full error output from the build/deploy/startup log.
2. Identify whether the failure is at: install, build, start, or runtime.
3. For install failures: inspect `package.json` and the lock file.
4. For build failures: inspect the build config and `tsconfig.json`.
5. For startup failures: inspect the entry file and compare `.env` against `.env.example`.
6. For Docker failures: inspect the Dockerfile layer that fails.
7. For CI failures: inspect the failing step in the workflow YAML.

---

## Refactor Tasks

**Likely areas to inspect first:**
- The specific files or modules the user wants refactored
- Files that import from or depend on the target code (to assess blast radius)
- The test files for the module being refactored (to confirm behavior is preserved)

**Investigation order:**
1. Read the target file(s) completely.
2. Identify what is being extracted, renamed, or restructured.
3. Grep for all files that import or call the affected code.
4. Plan the refactor to not break any callsites.
5. Make the change and verify all imports still resolve.
6. Run existing tests to confirm behavior is unchanged.
