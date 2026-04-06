# CTO UAT Playbook — CartSnitch

**Owner:** Savannah Savings (CTO)
**Last updated:** 2026-03-30
**Purpose:** Single source of truth for all UAT test knowledge. Rhonda never reads this file. CTO uses it to create atomic task descriptions.

---

## 1. Environment Reference

| Item | Value |
|------|-------|
| Dev URL | `https://cartsnitch.dev.farh.net` |
| Production URL | `https://cartsnitch.farh.net` — **NEVER test here** |
| Playwright MCP | `playwright` at `http://playwright:8931/mcp` |
| Mobile viewport | 375 x 812 (iPhone — mobile-first PWA) |
| Auth provider | Authentik at `https://auth.farh.net` |

## 2. Test Data

| Item | Value |
|------|-------|
| Seed user email | `uat@cartsnitch.com` |
| Seed user password | `CartSnitch-UAT-2026!` |
| Registration email pattern | `uat+{timestamp}@cartsnitch.com` |
| Registration password | `CartSnitch-UAT-2026!` (meets strength requirements) |
| Registration display name | `UAT Test` |

## 3. User Journey Catalog

| ID | Area | Key Checks | Fragile? |
|----|------|------------|----------|
| J1 | Playwright Connectivity | `browser_navigate` to `about:blank` succeeds | No |
| J2 | Environment Health | Dev loads, CartSnitch UI renders, no critical JS errors | Yes |
| J3 | Registration (happy path) | Form renders, POST returns 2xx, redirect works | **Yes** |
| J4 | Registration (validation) | Empty fields, bad email, weak password show errors | Yes |
| J5 | Registration (duplicate) | Same email twice shows proper error, not crash | Yes |
| J6 | Login (new account) | POST returns 2xx, session active, dashboard renders | **Yes** |
| J7 | Login (seed user) | `uat@cartsnitch.com` works, API returns 2xx | **Yes** |
| J8 | Login (invalid creds) | Wrong password/email shows error, not crash | Yes |
| J9 | Session Persistence | Refresh page, still logged in | Yes |
| J10 | Forgot Password | Form renders, submit shows confirmation, API 2xx | No |
| J11 | Dashboard | Renders sections, no JS errors, mobile layout OK | No |
| J12 | Purchases | List or empty state, API 2xx | No |
| J13 | Purchase Detail | Detail page renders, API 2xx | No |
| J14 | Products | Catalog or empty state, API 2xx | No |
| J15 | Product Detail | Details render, price history visible | No |
| J16 | Store Comparison | Comparison data renders | No |
| J17 | Coupons | List or empty state, API 2xx | No |
| J18 | Alerts | List or empty state, API 2xx | No |
| J19 | Settings | Page renders, all tabs/sections work | No |
| J20 | Account Linking | Page renders, interactive elements work | No |
| J21 | Logout | Redirect to login or landing page | No |
| J22 | Access Control | All protected routes redirect when logged out | Yes |
| J23 | Navigation | Bottom nav works, back button works, no 404s | No |
| J24 | Console Error Audit | Session-accumulated errors documented | No |

---

## 4. Journey Details

### J1: Playwright Connectivity

**Preconditions:** None (first test always)

**Steps:**
1. Call `browser_navigate` with url `about:blank`
2. PASS: Page loads without error

**If PASS:** Proceed to J2.
**If FAIL:** Block task. Comment: "Playwright MCP server unreachable — cannot perform UAT. Error: {exact error}." Assign to CTO with status todo.

---

### J2: Environment Health

**Preconditions:** J1 passed

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net`
2. PASS: Page loads (no DNS failure, no timeout, no 5xx)
3. Call `browser_snapshot`
4. PASS: Page contains recognizable CartSnitch UI (navigation, header, or login form)
5. Call `browser_console_messages`
6. PASS: No critical JS errors (Uncaught TypeError, ChunkLoadError, React render errors)
7. Call `browser_take_screenshot`

**If PASS:** Proceed to auth tests.
**If FAIL at step 2 (hard failure):** Block task. Comment with exact error, URL, screenshot if partial load. Assign to CTO with status todo.
**If FAIL at step 4 (blank page):** Take screenshot, capture console errors, block task.
**If FAIL at step 6 (JS errors but page loads):** Note errors, continue testing, report as medium severity.

---

### J3: Registration (happy path) [KNOWN FRAGILE]

**Preconditions:** J2 passed. Not logged in. **3 production escapes: CAR-126, CAR-128, CAR-147.**

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/register`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot`
4. PASS: Registration form visible with name/display_name, email, and password fields
5. Call `browser_fill_form` with: email = `uat+{timestamp}@cartsnitch.com`, password = `CartSnitch-UAT-2026!`, name/display_name = `UAT Test`
6. Call `browser_click` on the submit/register button
7. Call `browser_network_requests`
8. PASS: POST to registration endpoint returned HTTP 2xx (not 4xx or 5xx)
9. Call `browser_snapshot`
10. PASS: User redirected to dashboard or login page, no error message visible
11. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Registration happy path. API returned 2xx. Screenshot attached." Mark done.
**If FAIL at step 8 (API error):** This is CRITICAL. Comment: "Registration API returned {status code}. Auth contract may be broken. See CAR-126/CAR-128/CAR-147 history." Assign to CTO with status todo.
**If FAIL at step 10 (visual):** Comment with screenshot and what was expected vs actual. Assign to CTO with status todo.

**Note for CTO (decomposition):** Always record the email used so subsequent login tasks can reference it.

---

### J4: Registration (validation errors)

**Preconditions:** J2 passed. On `/register` page.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/register`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_click` on submit button (empty fields)
4. Call `browser_snapshot`
5. PASS: Field-level error messages visible
6. Call `browser_fill_form` with email = `notanemail`, password = `x`, name = `T`
7. Call `browser_click` on submit button
8. Call `browser_snapshot`
9. PASS: Validation errors for malformed email and/or weak password
10. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Registration validation errors display correctly." Mark done.
**If FAIL:** Comment with which validation was missing and screenshot. Assign to CTO with status todo.

---

### J5: Registration (duplicate email)

**Preconditions:** J3 passed (an account was created with a known email).

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/register`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_fill_form` with the SAME email used in J3, password = `CartSnitch-UAT-2026!`, name = `Duplicate Test`
4. Call `browser_click` on submit button
5. Call `browser_network_requests`
6. PASS: API returned an error status (409 or 422 expected, not 500)
7. Call `browser_snapshot`
8. PASS: User-friendly error message about duplicate email (not a crash, not a generic "something went wrong")
9. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Duplicate registration handled correctly." Mark done.
**If FAIL (500 or crash):** CRITICAL. Comment with API response and screenshot. Assign to CTO with status todo.

---

### J6: Login (new account) [KNOWN FRAGILE]

**Preconditions:** J3 passed (fresh account exists).

**Steps:**
1. If logged in, navigate to logout first, then:
2. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/login`
3. Call `browser_resize` with width 375, height 812
4. Call `browser_snapshot`
5. PASS: Login form visible with email and password fields
6. Call `browser_fill_form` with email = (email from J3), password = `CartSnitch-UAT-2026!`
7. Call `browser_click` on login/submit button
8. Call `browser_network_requests`
9. PASS: POST to login endpoint returned HTTP 2xx
10. Call `browser_snapshot`
11. PASS: Redirected to dashboard, authenticated state visible (user name/avatar, or logout button)
12. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Login with new account. API returned 2xx. Dashboard loaded." Mark done.
**If FAIL at step 9 (API error):** CRITICAL. Comment: "Login API returned {status}. Credentials were valid (account created in J3)." Assign to CTO with status todo.

---

### J7: Login (seed user) [KNOWN FRAGILE]

**Preconditions:** J2 passed. Logged out.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/login`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_fill_form` with email = `uat@cartsnitch.com`, password = `CartSnitch-UAT-2026!`
4. Call `browser_click` on login/submit button
5. Call `browser_network_requests`
6. PASS: POST to login endpoint returned HTTP 2xx
7. Call `browser_snapshot`
8. PASS: Dashboard renders, authenticated state visible
9. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Seed user login. API returned 2xx." Mark done.
**If FAIL at step 6 (API error):** HIGH. Seed user may not exist in dev. Comment: "Seed user login failed, API returned {status}." Assign to CTO with status todo.

---

### J8: Login (invalid credentials)

**Preconditions:** J2 passed. Logged out.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/login`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_fill_form` with email = `uat@cartsnitch.com`, password = `WrongPassword123!`
4. Call `browser_click` on login/submit button
5. Call `browser_snapshot`
6. PASS: Error message visible (not a crash, not a blank page, not a redirect to dashboard)
7. Call `browser_fill_form` with email = `nonexistent@cartsnitch.com`, password = `CartSnitch-UAT-2026!`
8. Call `browser_click` on login/submit button
9. Call `browser_snapshot`
10. PASS: Error message visible
11. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Invalid login attempts show proper error messages." Mark done.
**If FAIL:** Comment with what happened instead. Assign to CTO with status todo.

---

### J9: Session Persistence

**Preconditions:** Logged in (from J6 or J7).

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/settings`
2. Call `browser_snapshot`
3. PASS: Settings page renders (user is still authenticated)
4. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/`
5. Call `browser_snapshot`
6. PASS: Dashboard renders (still authenticated after navigation)
7. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/` (page refresh)
8. Call `browser_snapshot`
9. PASS: Still authenticated after refresh (session persists)
10. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Session persists across navigation and page refresh." Mark done.
**If FAIL (session lost):** HIGH. Comment: "Session did not persist. After {step}, user was logged out." Assign to CTO with status todo.

---

### J10: Forgot Password

**Preconditions:** J2 passed. On login page or can navigate there.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/forgot-password`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot`
4. PASS: Forgot password form renders with email field
5. Call `browser_fill_form` with email = `uat@cartsnitch.com`
6. Call `browser_click` on submit button
7. Call `browser_network_requests`
8. PASS: API returned 2xx
9. Call `browser_snapshot`
10. PASS: Confirmation message visible (e.g., "Check your email")
11. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Forgot password flow works. API returned 2xx." Mark done.
**If FAIL:** Comment with which step failed. Assign to CTO with status todo.

---

### J11: Dashboard

**Preconditions:** Logged in.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot`
4. PASS: Dashboard renders with expected sections (not blank, not error page)
5. Call `browser_console_messages`
6. PASS: No critical JS errors
7. PASS: No horizontal overflow, no overlapping elements at 375px width
8. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Dashboard renders correctly at mobile viewport." Mark done.
**If FAIL:** Comment with what's broken and screenshot. Assign to CTO with status todo.

---

### J12: Purchases Page

**Preconditions:** Logged in.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/purchases`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot`
4. PASS: Purchase list or empty state renders
5. Call `browser_network_requests`
6. PASS: API call for purchases returned 2xx
7. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Purchases page renders. API returned 2xx." Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J13: Purchase Detail

**Preconditions:** Logged in. J12 passed.

**Steps:**
1. If purchases exist from J12, click into one. If no purchases exist, skip this journey.
2. Call `browser_snapshot`
3. PASS: Purchase detail page renders with receipt/order information
4. Call `browser_network_requests`
5. PASS: API returned 2xx
6. Call `browser_take_screenshot`

**If ALL PASS (or skipped due to no data):** Comment: "UAT PASS — Purchase detail renders." or "SKIP — No purchases to test." Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J14: Products Page

**Preconditions:** Logged in.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/products`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot`
4. PASS: Product catalog or empty state renders
5. Call `browser_network_requests`
6. PASS: API returned 2xx
7. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Products page renders. API returned 2xx." Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J15: Product Detail

**Preconditions:** Logged in. J14 passed.

**Steps:**
1. If products exist from J14, click into one. If no products, skip.
2. Call `browser_snapshot`
3. PASS: Product detail renders with price information
4. Call `browser_network_requests`
5. PASS: API returned 2xx
6. Call `browser_take_screenshot`

**If ALL PASS (or skipped):** Comment accordingly. Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J16: Store Comparison

**Preconditions:** Logged in. Product exists.

**Steps:**
1. If a "Compare" link or `/compare/{productId}` is accessible from J15, navigate there. If not, skip.
2. Call `browser_snapshot`
3. PASS: Comparison data renders
4. Call `browser_take_screenshot`

**If ALL PASS (or skipped):** Comment accordingly. Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J17: Coupons Page

**Preconditions:** Logged in.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/coupons`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot`
4. PASS: Coupon list or empty state renders
5. Call `browser_network_requests`
6. PASS: API returned 2xx
7. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Coupons page renders. API returned 2xx." Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J18: Alerts Page

**Preconditions:** Logged in.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/alerts`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot`
4. PASS: Alerts list or empty state renders
5. Call `browser_network_requests`
6. PASS: API returned 2xx
7. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Alerts page renders. API returned 2xx." Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J19: Settings Page

**Preconditions:** Logged in.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/settings`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot`
4. PASS: Settings page renders
5. Click any visible tabs or sections
6. Call `browser_snapshot`
7. PASS: Tab/section content loads
8. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Settings page renders, tabs work." Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J20: Account Linking Page

**Preconditions:** Logged in.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/account-linking`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot`
4. PASS: Account linking page renders (store connection UI or empty state)
5. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Account linking page renders." Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J21: Logout

**Preconditions:** Logged in.

**Steps:**
1. Call `browser_snapshot` to identify logout button/link
2. Call `browser_click` on the logout element
3. Call `browser_snapshot`
4. PASS: Redirected to login page or public landing page
5. PASS: No authenticated UI elements visible
6. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Logout works, redirected to login." Mark done.
**If FAIL:** Comment with details. Assign to CTO with status todo.

---

### J22: Access Control (Unauthenticated)

**Preconditions:** Logged out (J21 passed).

**Protected routes to test:**
- `/purchases`
- `/products`
- `/coupons`
- `/alerts`
- `/settings`
- `/account-linking`
- `/` (dashboard/root)

**Steps (repeat for each route):**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/{route}`
2. Call `browser_snapshot`
3. PASS: Redirected to login page (not a blank page, not the actual protected content, not an error)

**After all routes tested:**
4. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — All protected routes redirect to login when unauthenticated." Mark done.
**If ANY route accessible without auth:** HIGH severity. Comment: "Protected route `/{route}` is accessible without authentication." Assign to CTO with status todo.

---

### J23: Navigation

**Preconditions:** Logged in.

**Steps:**
1. Call `browser_navigate` to `https://cartsnitch.dev.farh.net/`
2. Call `browser_resize` with width 375, height 812
3. Call `browser_snapshot` to identify bottom nav / sidebar menu items
4. Click each navigation item one by one
5. After each click, call `browser_snapshot`
6. PASS: Each target page loads (no 404, no blank page)
7. Call `browser_navigate_back` several times
8. Call `browser_snapshot`
9. PASS: App state is consistent after back navigation (no broken state)
10. Call `browser_take_screenshot`

**If ALL PASS:** Comment: "UAT PASS — Navigation works correctly at mobile viewport." Mark done.
**If FAIL:** Comment with which nav item or back action failed. Assign to CTO with status todo.

---

### J24: Console Error Audit

**Preconditions:** Last test — run after all other journeys.

**Steps:**
1. Call `browser_console_messages`
2. Review all accumulated errors from the session
3. Document any: `Uncaught` errors, `Failed to fetch`, `NetworkError`, `ChunkLoadError`, React rendering errors
4. PASS: No undocumented critical errors exist
5. If >5 unique console errors across session: note as MEDIUM severity

**If PASS:** Comment: "UAT PASS — Console audit. {N} unique errors found: {summary}." Mark done.
**If new critical errors found:** Comment with full list. Assign to CTO with status todo.

---

## 5. Known Fragile Areas

| Area | History | Risk | Extra Verification |
|------|---------|------|-------------------|
| **Authentication (registration + login)** | 3 escapes: CAR-126 (basePath mismatch), CAR-128 (auth client config), CAR-147 (API contract mismatch) | Silent API failures behind working UI | Always check `browser_network_requests` for 2xx. A rendered form with a broken backend is a false positive. |
| **Frontend/API contract** | CAR-147: frontend sent wrong field name, expected wrong response shape | Fields renamed without frontend update | After every form submission, verify the POST/PUT returned 2xx via `browser_network_requests`. |
| **Dev environment availability** | CAR-127 (dev doesn't load), CAR-52 (CrashLoopBackOff) | Pods not running, image pull failures | Always run J1+J2 first. Never assume dev is healthy. |
| **Auth service deployment** | CAR-39 (image doesn't exist), CAR-141 (wrong env var) | Auth service up but misconfigured | Verify login actually works at the API level, not just that the form renders. |

## 6. Severity Definitions

| Severity | Definition | Blocks UAT? |
|----------|-----------|-------------|
| **critical** | Core flow broken, app unusable. Login crashes, registration API returns 500, app won't load. | **Yes** |
| **high** | Major feature non-functional, security flaw. Protected routes accessible without auth, session doesn't persist, page shows wrong data. | **Yes** |
| **medium** | Feature degraded but usable. Slow load (>5s), unclear validation message, mobile layout broken, >5 console warnings. | No (warning) |
| **low** | Cosmetic. Typo, slight alignment, missing hover state. | No (warning) |

## 7. Defect Report Template

```
### Defect: {short description}
- **Severity:** critical | high | medium | low
- **Journey:** J{N} — {name}
- **Known Fragile Area:** yes/no
- **Steps to reproduce:**
  1. {step}
  2. {step}
- **Expected:** {what should happen}
- **Actual:** {what happened}
- **API response:** {status code from browser_network_requests, or N/A}
- **Console errors:** {from browser_console_messages, or none}
- **Screenshot:** attached
```

## 8. Decomposition Guide

### Standard 5-Slot Pattern Per Deploy

| Slot | Subtask | Source Journeys | When |
|------|---------|----------------|------|
| 1 | Playwright + environment readiness | J1 + J2 | Always first |
| 2 | Auth verification | J3-J10 (subset based on scope) | Always — auth is fragile |
| 3 | Feature-specific tests | J{scope} based on PR | Based on what changed |
| 4 | Navigation + access control smoke | J21 + J22 + J23 | Every deploy |
| 5 | Console error audit | J24 | Always last |

### Sizing

- **Small/targeted PR** (e.g., auth fix, single page fix): 3-5 subtasks
- **Medium PR** (e.g., new feature, multi-page change): 5-8 subtasks
- **Large PR** (e.g., major refactor, auth overhaul): 8-12 subtasks
- **Full regression** (major release, post-infra change): All 24 journeys as individual tasks

### Scope-Based Journey Selection

| Change Type | Required Journeys |
|-------------|------------------|
| Auth service change | J1-J10, J21-J22, J24 |
| Frontend-only change | J1-J2, J11-J20, J23-J24 |
| Full deployment (both services) | J1-J24 (all) |
| Infrastructure change | J1-J2, J7, J11, J24 (health + smoke) |
| Single page fix (e.g., /coupons) | J1-J2, J7, J{page}, J23-J24 |

### Dependency Chains

- J3 creates account used by J5, J6
- J6 or J7 must pass before J9, J11-J20 (need authenticated session)
- J21 must run before J22 (need logged-out state)
- J24 is always last (aggregates session errors)

### Parallelization

After auth (J3-J10) passes, page tests J11-J20 can run as independent parallel tasks (each logs in independently). J21-J23 should run sequentially after page tests.

### Task Description Template

Every subtask CTO creates for Rhonda follows this format:

```
## What
{One sentence: specific test to run}

## Steps
1) Call `browser_navigate` to {url}
2) Call `browser_resize` with width 375, height 812
3) Call `browser_snapshot` — PASS: {what should be visible}
4) Call `browser_fill_form` with {exact fields and values}
5) Call `browser_click` on {exact element}
6) Call `browser_network_requests` — PASS: {endpoint} returned {expected status}
7) Call `browser_snapshot` — PASS: {expected visual state}
8) Call `browser_take_screenshot`

**If ALL PASS:** Mark issue done. Post comment: "UAT PASS — {journey name}. {key detail}. Screenshot attached."
**If ANY FAIL:** Set status todo. Assign to CTO (22731e25-f40f-48bd-a16e-28e1bbef5946). Post comment: "UAT FAIL — Step {N} failed. Expected: {X}. Actual: {Y}. Screenshot attached."
```

**Rules for task descriptions:**
- Every step is a single Playwright MCP tool call
- Inline PASS criteria after every verification step
- Include exact field values, URLs, credentials
- No references to external documents — task is fully self-contained
- No conditional logic — if auth fails in a page test, just report the failure
- No severity assessment — CTO determines severity from the report

---

## 9. Maturation Log

Track playbook changes here after each UAT cycle.

| Date | Change | Reason |
|------|--------|--------|
| 2026-03-30 | Initial playbook created | CAR-198: UAT overhaul |
