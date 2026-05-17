# 🚀 Pre-Deployment Checklist

> **MANDATORY: Run this checklist BEFORE every `git push` to deploy.** Skipping this causes production bugs.

## 📋 Phase 1: Code Quality Checks

### TypeScript Compilation
```bash
npx tsc --noEmit
```
- [ ] Zero TypeScript errors
- [ ] No "any" types in critical paths
- [ ] All imports/exports resolve correctly
- [ ] No unused variables (except in dev)

### Duplicate Declaration Check
```bash
grep -n "function nowIso" server/index.ts
grep -n "function serializeJson" server/index.ts
grep -n "function parseStoredJson" server/index.ts
grep -n "function buildAssetPublicUrl" server/index.ts
```
- [ ] No duplicate function declarations
- [ ] No duplicate const/let declarations
- [ ] No conflicting variable names in same scope

### Code Pattern Checks
- [ ] No `console.log` in production code (use `console.error` for errors only)
- [ ] All async functions have try/catch blocks
- [ ] All database queries use parameterized inputs (no SQL injection)
- [ ] All user inputs are validated before use
- [ ] No hardcoded secrets in code

## 📦 Phase 2: Build & Bundle Checks

### Vite Build
```bash
npm run build
```
- [ ] Build succeeds without errors
- [ ] No chunk size warnings (>500KB)
- [ ] All assets load correctly
- [ ] `dist/` folder generated properly

### Worker Build
```bash
npx wrangler deploy --dry-run
```
- [ ] Dry run succeeds
- [ ] All bindings resolve correctly
- [ ] No missing environment variables
- [ ] Cron triggers configured properly

## 🗄️ Phase 3: Database Checks

### D1 Migrations
```bash
# Local
npx wrangler d1 migrations apply getrankbloom-db --local

# Remote (requires auth)
npx wrangler d1 migrations apply getrankbloom-db --remote
```
- [ ] Migrations apply without errors
- [ ] No duplicate column/table errors
- [ ] All `IF NOT EXISTS` clauses present
- [ ] Seed data uses `INSERT OR IGNORE`

### Schema Verification
```bash
npx wrangler d1 execute getrankbloom-db --local --command="SELECT name FROM sqlite_master WHERE type='table';"
```
- [ ] All expected tables exist
- [ ] No unexpected tables
- [ ] Column names match code expectations

## 🔌 Phase 4: Binding & Configuration Checks

### wrangler.jsonc Verification
- [ ] `ai` binding present and named `AI`
- [ ] `d1_databases` binding matches code (`DB`)
- [ ] `r2_buckets` binding matches code (`CONTENT_BUCKET`)
- [ ] `kv_namespaces` binding matches code (`SCHEDULER_KV`)
- [ ] `services` bindings match code (`AUTH_SERVICE`, `PAYMENT_SERVICE`)
- [ ] `vars` contains all required environment variables
- [ ] `triggers.crons` syntax is valid (UTC timezone)

### Environment Variables
- [ ] `ALIBABA_SECRET` is set
- [ ] `JWT_SECRET` is set (not placeholder)
- [ ] `APP_URL` matches production domain
- [ ] `AUTH_ORIGIN` is correct
- [ ] `CDN_URL` is correct

## 🧪 Phase 5: Testing Checks

### Route Testing (Local)
```bash
npm run dev
```
- [ ] All API routes respond correctly
- [ ] Auth flows work (login, session, logout)
- [ ] Site onboarding flow completes
- [ ] Post generation works end-to-end
- [ ] Webhook delivery succeeds
- [ ] Image generation returns valid image
- [ ] Billing endpoints work correctly

### Cron Testing
```bash
# Manual trigger test
curl -X POST https://app.getrankbloom.com/api/admin/trigger-scheduler
```
- [ ] Cron handler executes without errors
- [ ] Posts generated for active sites
- [ ] Webhooks delivered successfully
- [ ] Database records created correctly

## 🌐 Phase 6: Production Verification

### Post-Deployment Checks
```bash
# Monitor logs
npx wrangler tail getrankbloom

# Check Worker status
npx wrangler versions list
```
- [ ] Deployment succeeds without errors
- [ ] Worker logs show no critical errors
- [ ] All routes respond with correct status codes
- [ ] Cron trigger fires at expected time
- [ ] D1 migrations applied remotely
- [ ] R2 bucket accessible
- [ ] AI bindings working

### Monitoring Setup
- [ ] Cloudflare Workers analytics enabled
- [ ] Error rate monitoring configured
- [ ] Cron execution logging active
- [ ] Webhook failure alerting set up
- [ ] Credit usage tracking verified

## 🚨 Common Deployment Failures

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| `Duplicate function declarations` | Copied helper functions | Remove duplicates, keep originals |
| `AI is not defined` | Missing `ai` binding | Add to `wrangler.jsonc` |
| `D1_ERROR: no such table` | Migration didn't run | Run `wrangler d1 migrations apply` |
| `R2Error: NoSuchKey` | Wrong storage key | Verify key path in code |
| `404 Not Found` | Route not defined or after `notFound()` | Move route before `app.notFound()` |
| `Build failed with 4 errors` | TypeScript/ESM issues | Run `npx tsc --noEmit` |
| `Chunk size warning` | Large bundle | Add `manualChunks` to Vite config |
| `Cron not firing` | Wrong syntax or not deployed | Verify `triggers.crons` in `wrangler.jsonc` |

## ✅ Final Sign-Off

Before pushing:
- [ ] All Phase 1-6 checks passed
- [ ] No known critical bugs
- [ ] Commit message is descriptive
- [ ] Changes tested locally
- [ ] Ready for production

**If any check fails, DO NOT PUSH. Fix the issue first.**

---

*Last updated: 2026-05-17*
*This checklist is MANDATORY for all deployments to prevent production bugs.*
