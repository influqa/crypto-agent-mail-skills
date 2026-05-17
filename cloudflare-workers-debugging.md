# 🏗️ Cloudflare Workers + Hono + D1 + R2 - Stack-Specific Debugging Guide

> **This is your go-to reference when things break.** Every common error, fix, and debugging pattern for our exact stack.

## 🚨 Critical Errors & Fixes

### 1. "Duplicate top-level function declarations" (ESM Module Error)
**Symptom:** Build fails with `The symbol "X" has already been declared`
**Root Cause:** ECMAScript modules don't allow duplicate top-level function declarations. When you add `export default app` at the bottom, the entire file becomes an ESM module, and all functions must be unique.
**Fix:**
```bash
# 1. Search for duplicate function names
grep -n "function nowIso" server/index.ts
grep -n "function serializeJson" server/index.ts
grep -n "function parseStoredJson" server/index.ts
grep -n "function buildAssetPublicUrl" server/index.ts

# 2. Remove duplicates, keep only the first occurrence
# 3. Verify no more duplicates before committing
```
**Prevention:** Never copy-paste helper functions. Check for existing implementations before adding new ones.

### 2. D1 Database Migration Failures
**Symptom:** `D1_ERROR: no such table: X` or `D1_ERROR: duplicate column name`
**Root Cause:** 
- Migration didn't run locally or remotely
- Schema drift between code and database
- Missing `IF NOT EXISTS` or `INSERT OR IGNORE` clauses

**Debug Flow:**
```bash
# 1. Check local DB state
npx wrangler d1 migrations apply getrankbloom-db --local

# 2. Check remote DB state
npx wrangler d1 migrations apply getrankbloom-db --remote

# 3. Query DB directly to verify schema
npx wrangler d1 execute getrankbloom-db --local --command="SELECT name FROM sqlite_master WHERE type='table';"

# 4. Check migration history
npx wrangler d1 migrations list getrankbloom-db --local
```

**Fix Patterns:**
```sql
-- Safe table creation
CREATE TABLE IF NOT EXISTS my_table (id TEXT PRIMARY KEY);

-- Safe column addition
ALTER TABLE my_table ADD COLUMN new_col TEXT;

-- Idempotent seed data
INSERT OR IGNORE INTO my_table (id, value) VALUES ('1', 'test');
```

### 3. Workers AI Binding Errors
**Symptom:** `AI is not defined` or `Workers AI binding is not configured`
**Root Cause:** 
- Missing `ai` binding in `wrangler.jsonc`
- Model name typo (e.g., `@cf/black-forest-labs/flux-1-schnell` vs `@cf/stabilityai/stable-diffusion-xl`)
- AI binding not available in local dev without `--remote`

**Fix:**
```jsonc
// wrangler.jsonc - MUST have this section
"ai": {
  "binding": "AI"
}
```

**Model Name Reference:**
```typescript
// Correct model names (2026-05):
'@cf/black-forest-labs/flux-1-schnell'  // Image generation
'@cf/meta/llama-3.3-70b-instruct'      // Text generation
'@cf/microsoft/phi-2'                  // Small text model
```

### 4. R2 Bucket Access Errors
**Symptom:** `R2Error: NoSuchKey` or `R2Error: AccessDenied`
**Root Cause:**
- Wrong bucket name in `wrangler.jsonc`
- Missing `r2_buckets` binding
- Storage key path doesn't exist

**Debug Flow:**
```bash
# List R2 buckets
npx wrangler r2 bucket list

# List objects in bucket
npx wrangler r2 object list getrankbloom-content --prefix "posts/"

# Get object metadata
npx wrangler r2 object get getrankbloom-content posts/uuid/featured.png --file=output.png
```

**Fix Pattern:**
```jsonc
// wrangler.jsonc
"r2_buckets": [
  {
    "binding": "CONTENT_BUCKET",
    "bucket_name": "getrankbloom-content",
    "preview_bucket_name": "getrankbloom-content-preview"
  }
]
```

### 5. Hono Route Not Found / 404
**Symptom:** API endpoint returns 404 or falls through to SPA fallback
**Root Cause:**
- Route path mismatch (e.g., `/api/posts/generate` vs `/api/post/generate`)
- HTTP method mismatch (GET vs POST)
- Middleware blocking the route
- Route defined after `app.notFound()` handler

**Debug Flow:**
```bash
# 1. Check route order in server/index.ts
# 2. Verify HTTP method matches
# 3. Check middleware chain
# 4. Test with curl
curl -X POST https://app.getrankbloom.com/api/posts/generate \
  -H "Content-Type: application/json" \
  -H "Cookie: rb_session=YOUR_TOKEN" \
  -d '{"siteId":"...","userQuestion":"..."}'
```

**Fix Pattern:**
```typescript
// Routes MUST be defined BEFORE app.notFound()
app.post('/api/posts/generate', async (c) => { ... })

// SPA fallback MUST be last
app.notFound(async (c) => { ... })
```

### 6. Timeout Errors (AI/Image Generation)
**Symptom:** Request hangs for >30s then fails with `timeout` or `ERR_REQUEST_TIMEOUT`
**Root Cause:**
- Cloudflare Workers have 10ms CPU time limit (free) or 30s (paid) for unbound workers
- AI model response is too slow
- Image generation takes too long

**Fix Pattern:**
```typescript
// ALWAYS wrap AI calls in timeout protection
const result = await Promise.race([
  c.env.AI.run('@cf/black-forest-labs/flux-1-schnell', { prompt, width: 1024, height: 1024 }),
  new Promise<never>((_, reject) => 
    setTimeout(() => reject(new Error('Image generation timed out (30s limit).')), 30000)
  ),
])
```

### 7. JSON Parsing Errors from AI
**Symptom:** `SyntaxError: Unexpected token` when parsing AI response
**Root Cause:**
- AI returns markdown code fences (```json ... ```)
- AI adds extra text before/after JSON
- AI response is truncated

**Fix Pattern:**
```typescript
function extractJsonObject(text: string): Record<string, unknown> | null {
  const trimmed = text.trim()
  const start = trimmed.indexOf('{')
  const end = trimmed.lastIndexOf('}')
  
  if (start === -1 || end === -1 || end <= start) return null
  
  try {
    return JSON.parse(trimmed.slice(start, end + 1)) as Record<string, unknown>
  } catch {
    return null
  }
}
```

### 8. Webhook Delivery Failures
**Symptom:** `Webhook delivery failed: 500` or `Webhook delivery timed out`
**Root Cause:**
- Destination server is down
- Webhook URL is invalid
- Payload is too large
- Network timeout

**Fix Pattern:**
```typescript
// Always timeout webhook calls
const webhookResponse = await Promise.race([
  fetch(webhookUrl, { method: 'POST', body: JSON.stringify(payload) }),
  new Promise<Response>((_, reject) => 
    setTimeout(() => reject(new Error('Webhook timed out')), 15000)
  ),
])

// Log failures for retry
await env.DB.prepare(
  `INSERT INTO publish_jobs (id, post_id, status, error_message) VALUES (?, ?, 'failed', ?)`
).bind(crypto.randomUUID(), postId, error.message).run()
```

### 9. Build Size Warnings (>500KB chunks)
**Symptom:** Vite warning: `Some chunks are larger than 500 kB after minification`
**Root Cause:**
- Bundling entire libraries (e.g., `marked`, `zod`) into single chunk
- No code splitting configured

**Fix:**
```typescript
// vite.config.ts - Add manual chunks
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom'],
          ui: ['framer-motion', '@radix-ui/react-dialog'],
          utils: ['zod', 'marked'],
        }
      }
    }
  }
})
```

### 10. Cron Trigger Not Firing
**Symptom:** Scheduled posts not generated at expected time
**Root Cause:**
- Cron syntax wrong in `wrangler.jsonc`
- Worker not deployed with cron triggers
- Timezone mismatch (cron runs in UTC)

**Debug Flow:**
```bash
# 1. Check cron configuration
cat wrangler.jsonc | grep -A 3 "triggers"

# 2. Verify deployment includes cron
npx wrangler deploy --dry-run

# 3. Check Worker logs for cron execution
npx wrangler tail getrankbloom
```

**Fix Pattern:**
```jsonc
// wrangler.jsonc - UTC timezone
"triggers": {
  "crons": ["0 9 * * *"]  // Daily at 9 AM UTC
}
```

## 🔍 Debugging Checklist

### Before Deployment:
- [ ] Run `npx tsc --noEmit` (no TypeScript errors)
- [ ] Check for duplicate function declarations
- [ ] Verify all bindings in `wrangler.jsonc` match code
- [ ] Test routes locally with `npm run dev`
- [ ] Verify cron syntax is valid
- [ ] Check Vite bundle size (warn if >500KB)
- [ ] Run D1 migrations locally
- [ ] Test webhook delivery with webhook.site

### After Deployment:
- [ ] Check Worker logs: `npx wrangler tail`
- [ ] Verify all routes respond correctly
- [ ] Test cron trigger manually
- [ ] Check D1 remote migrations applied
- [ ] Verify R2 bucket accessible
- [ ] Test webhook signature verification
- [ ] Monitor error rates in Cloudflare dashboard

### When Bug Occurs:
- [ ] Reproduce locally first
- [ ] Check Worker logs for exact error
- [ ] Verify environment variables are set
- [ ] Check D1/R2/AI bindings are configured
- [ ] Test with minimal payload
- [ ] Isolate the failing component
- [ ] Add temporary logging to trace flow

## 📚 Stack-Specific Patterns

### D1 Query Patterns:
```typescript
// Safe query with error handling
try {
  const result = await env.DB.prepare('SELECT * FROM sites WHERE id = ?')
    .bind(siteId)
    .first()
  if (!result) throw new Error('Site not found')
} catch (error) {
  console.error('[D1] Query failed:', error)
  throw new Error(`Database error: ${error.message}`)
}
```

### R2 Storage Patterns:
```typescript
// Safe file upload with validation
const MAX_FILE_SIZE = 10 * 1024 * 1024 // 10MB
if (imageBytes.length > MAX_FILE_SIZE) {
  throw new Error('Image too large')
}

await env.CONTENT_BUCKET.put(storageKey, imageBytes, {
  httpMetadata: { contentType: 'image/png' },
  customMetadata: { postId, uploadedAt: new Date().toISOString() }
})
```

### AI Generation Patterns:
```typescript
// Safe AI call with retry and timeout
async function safeAIRun(env: Env, model: string, input: any, maxRetries = 2) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      const result = await Promise.race([
        env.AI.run(model, input),
        new Promise<never>((_, reject) => 
          setTimeout(() => reject(new Error('AI timeout')), 30000)
        ),
      ])
      return result
    } catch (error) {
      if (attempt === maxRetries) throw error
      console.warn(`[AI] Attempt ${attempt} failed, retrying...`)
      await new Promise(resolve => setTimeout(resolve, 1000 * attempt))
    }
  }
}
```

---

*Last updated: 2026-05-17*
*Stack: Hono + Cloudflare Workers + D1 + R2 + Workers AI + Alibaba API*
