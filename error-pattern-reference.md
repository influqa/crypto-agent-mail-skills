# 🐛 Error Pattern Reference - Cloudflare Workers Stack

> **Quick lookup for common errors and their fixes.** When you see an error, find it here first.

## 📚 Error Categories

### TypeScript/Build Errors

#### TS2300: Duplicate identifier 'X'
```
error TS2300: Duplicate identifier 'nowIso'.
```
**Fix:** Remove duplicate function declaration. ESM modules don't allow duplicates.

#### TS2345: Argument of type 'X' is not assignable to parameter of type 'Y'
```
error TS2345: Argument of type 'string | null' is not assignable to parameter of type 'string'.
```
**Fix:** Add null check or use type assertion: `value ?? 'default'` or `value as string`

#### TS7006: Parameter 'X' implicitly has an 'any' type
```
error TS7006: Parameter 'c' implicitly has an 'any' type.
```
**Fix:** Add explicit type: `(c: { env: RankBloomBindings }) =>`

#### ESBuild: Could not resolve "X"
```
✘ [ERROR] Could not resolve "marked"
```
**Fix:** Run `npm install marked` or check package.json dependencies

### Runtime Errors

#### ReferenceError: X is not defined
```
ReferenceError: nowIso is not defined
```
**Fix:** Function not in scope. Check imports/exports and function declarations.

#### TypeError: Cannot read properties of undefined
```
TypeError: Cannot read properties of undefined (reading 'prepare')
```
**Fix:** `env.DB` is undefined. Check D1 binding in `wrangler.jsonc`.

#### D1_ERROR: no such table: X
```
D1_ERROR: no such table: seo_posts
```
**Fix:** Run migration: `npx wrangler d1 migrations apply getrankbloom-db --remote`

#### D1_ERROR: duplicate column name: X
```
D1_ERROR: duplicate column name: webhook_url
```
**Fix:** Migration already ran. Use `ALTER TABLE ADD COLUMN IF NOT EXISTS` (not supported in SQLite) or skip migration.

#### R2Error: NoSuchKey
```
R2Error: NoSuchKey: The specified key does not exist.
```
**Fix:** Check storage key path. Verify file was uploaded to R2 bucket.

#### R2Error: AccessDenied
```
R2Error: AccessDenied: Access Denied.
```
**Fix:** Check R2 bucket permissions and binding in `wrangler.jsonc`.

### AI/Workers AI Errors

#### AI is not defined
```
ReferenceError: AI is not defined
```
**Fix:** Add to `wrangler.jsonc`:
```jsonc
"ai": { "binding": "AI" }
```

#### Workers AI did not return image bytes
```
Error: Workers AI did not return image bytes.
```
**Fix:** Check model name and response format. FLUX returns `{ image: "base64..." }` or `ReadableStream`.

#### Model not found
```
Error: Model '@cf/black-forest-labs/flux-1-schnell' not found
```
**Fix:** Verify model name is correct. Check [Workers AI docs](https://developers.cloudflare.com/workers-ai/models/) for valid models.

### Network/Webhook Errors

#### FetchError: request failed
```
FetchError: request to https://example.com/webhook failed
```
**Fix:** Check webhook URL is valid and destination server is running.

#### TypeError: fetch failed
```
TypeError: fetch failed: ECONNREFUSED
```
**Fix:** Destination server is down or URL is wrong.

#### Webhook delivery timed out
```
Error: Webhook delivery timed out (15s limit).
```
**Fix:** Destination server is slow. Increase timeout or optimize webhook handler.

### Cron/Scheduled Errors

#### Cron not firing
**Symptoms:** No posts generated at expected time
**Fix:**
1. Check `wrangler.jsonc` has `"triggers": { "crons": ["0 9 * * *"] }`
2. Verify cron syntax is valid (use [crontab.guru](https://crontab.guru))
3. Check Worker logs: `npx wrangler tail`

#### Scheduled handler not defined
```
Error: scheduled handler not found
```
**Fix:** Export `scheduled` function: `export async function scheduled(controller, env, ctx) { ... }`

### Hono Route Errors

#### 404 Not Found
**Symptoms:** API endpoint returns 404
**Fix:**
1. Check route path matches exactly
2. Check HTTP method (GET vs POST)
3. Verify route is defined BEFORE `app.notFound()`
4. Check middleware isn't blocking the route

#### 405 Method Not Allowed
**Symptoms:** GET request to POST endpoint returns 405
**Fix:** Use correct HTTP method: `curl -X POST ...`

#### 500 Internal Server Error
**Symptoms:** Route crashes with 500
**Fix:** Check Worker logs for exact error. Usually unhandled exception.

### Authentication Errors

#### 401 Unauthorized
**Symptoms:** API returns 401
**Fix:** Check session cookie `rb_session` is present and valid.

#### 403 Forbidden
**Symptoms:** API returns 403
**Fix:** Check user has required permissions/role.

#### Session expired
**Symptoms:** User logged out unexpectedly
**Fix:** Check `SESSION_MAX_AGE_SECONDS` value (default: 14 days).

### Billing/Credit Errors

#### Insufficient credits
```
Error: Insufficient credits for this action.
```
**Fix:** User needs to upgrade plan or purchase credits.

#### Credit balance not found
```
Error: Credit balance not found for workspace.
```
**Fix:** Run `ensureCreditBalanceRow()` to initialize balance.

## 🔍 Debugging Flowchart

```
Error Occurs
    ↓
Check Worker Logs (npx wrangler tail)
    ↓
Is it a Build Error? → Yes → Run npx tsc --noEmit
    ↓ No
Is it a Database Error? → Yes → Check migrations, run D1 query
    ↓ No
Is it an AI Error? → Yes → Check binding, model name, timeout
    ↓ No
Is it a Route Error? → Yes → Check path, method, middleware
    ↓ No
Is it a Webhook Error? → Yes → Check URL, timeout, payload
    ↓ No
Add Temporary Logging → Reproduce → Isolate → Fix
```

## 📝 Error Logging Best Practices

```typescript
// Good: Structured error logging
try {
  await generatePost()
} catch (error) {
  console.error('[Generate Post] Failed:', {
    siteId,
    error: error.message,
    stack: error.stack,
    timestamp: new Date().toISOString()
  })
  throw error
}

// Bad: Unhelpful logging
try {
  await generatePost()
} catch (error) {
  console.log('error')
  throw error
}
```

---

*Last updated: 2026-05-17*
*Stack: Hono + Cloudflare Workers + D1 + R2 + Workers AI + Alibaba API*
