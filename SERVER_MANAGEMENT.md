# Server Management & Restart Guide

## 🔄 Vercel Serverless Architecture

OpenCut uses **Vercel's serverless platform**, which means:
- ✅ **No traditional servers** to manage or restart
- ✅ **Auto-scaling** based on traffic
- ✅ **Automatic restarts** on every request
- ✅ **Zero downtime** deployments

---

## 🚀 How to "Restart" the Server

### Method 1: Redeploy (Recommended)

**Via Git Push:**
```bash
# Make any change or use empty commit
git commit --allow-empty -m "Redeploy application"
git push origin main

# Vercel will automatically:
# 1. Build the application
# 2. Run tests
# 3. Deploy to production
# 4. Switch traffic to new deployment (zero downtime)
```

**Via Vercel Dashboard:**
```
1. Go to https://vercel.com/dashboard
2. Select your project
3. Click "Deployments" tab
4. Click "..." on latest deployment
5. Click "Redeploy"
6. Confirm
```

**Via Vercel CLI:**
```bash
# Install Vercel CLI
npm i -g vercel

# Login
vercel login

# Deploy to production
vercel --prod

# Force rebuild
vercel --prod --force
```

### Method 2: Environment Variable Update

**Triggers automatic redeployment:**
```
1. Go to Vercel Dashboard > Settings > Environment Variables
2. Edit any variable (or add a dummy one)
3. Click "Save"
4. Vercel will automatically redeploy
```

### Method 3: GitHub Actions (Automated)

**Already configured in `.github/workflows/bun-ci.yml`:**
- Runs on every push to `main`
- Builds and tests the application
- Vercel auto-deploys on successful build

---

## 🔍 Monitoring Server Health

### Health Check Endpoint

**Already implemented in docker-compose:**
```bash
# Check if server is running
curl https://your-domain.vercel.app/api/health

# Expected response:
# {"status": "ok"}
```

### Vercel Analytics

**Enable in dashboard:**
```
1. Project Settings > Analytics
2. Enable Web Analytics (free)
3. View real-time traffic, errors, performance
```

### Check Deployment Status

**Via Vercel Dashboard:**
```
Deployments tab shows:
- Build status (Building/Ready/Error)
- Build logs
- Runtime logs
- Performance metrics
```

**Via CLI:**
```bash
# List deployments
vercel ls

# View logs
vercel logs [deployment-url]

# Inspect deployment
vercel inspect [deployment-url]
```

---

## 🐛 Troubleshooting

### Issue: Application Not Responding

**Diagnosis:**
```bash
# 1. Check Vercel status
curl https://www.vercel-status.com/api/v2/status.json

# 2. Check your deployment
vercel ls

# 3. View recent logs
vercel logs --follow
```

**Solution:**
```bash
# Redeploy
vercel --prod --force
```

### Issue: Database Connection Errors

**Diagnosis:**
```bash
# Check Neon database status
# Visit: https://console.neon.tech

# Test connection locally
psql $DATABASE_URL -c "SELECT 1"
```

**Solution:**
```bash
# 1. Verify DATABASE_URL in Vercel env vars
# 2. Check Neon database is not suspended (free tier auto-suspends)
# 3. Redeploy to refresh connections
vercel --prod
```

### Issue: Redis Connection Errors

**Diagnosis:**
```bash
# Check Upstash status
# Visit: https://console.upstash.com

# Test connection
curl -X POST $UPSTASH_REDIS_REST_URL/ping \
  -H "Authorization: Bearer $UPSTASH_REDIS_REST_TOKEN"
```

**Solution:**
```bash
# 1. Verify Upstash credentials in Vercel
# 2. Check free tier limits (10,000 commands/day)
# 3. Redeploy
vercel --prod
```

### Issue: Build Failures

**Diagnosis:**
```bash
# View build logs in Vercel dashboard
# Or via CLI:
vercel logs [deployment-url] --build
```

**Common causes:**
- Missing environment variables
- TypeScript errors
- Dependency conflicts
- Out of memory (increase in vercel.json)

**Solution:**
```bash
# 1. Fix errors locally first
cd apps/web
bun install
bun run build

# 2. Commit and push
git add .
git commit -m "Fix build errors"
git push origin main
```

---

## ⚡ Performance Optimization

### Reduce Cold Starts

**Option 1: Keep Functions Warm (Free)**
```typescript
// Create apps/web/src/app/api/cron/keep-warm/route.ts
export async function GET() {
  return Response.json({ status: 'warm' });
}
```

**Option 2: Vercel Cron Jobs (Free)**
```json
// Add to vercel.json
{
  "crons": [{
    "path": "/api/cron/keep-warm",
    "schedule": "*/5 * * * *"
  }]
}
```

### Database Connection Pooling

**Already optimized:**
- Neon has built-in connection pooling
- Drizzle ORM handles connections efficiently
- Vercel serverless functions reuse connections

### Redis Optimization

**Current setup:**
- Upstash Redis is serverless (no connection pooling needed)
- HTTP REST API (no persistent connections)
- Auto-scales with traffic

---

## 📊 Monitoring & Alerts

### Set Up Uptime Monitoring (Free)

**UptimeRobot (Free Tier):**
```
1. Visit: https://uptimerobot.com
2. Add monitor:
   - Type: HTTP(s)
   - URL: https://your-domain.vercel.app/api/health
   - Interval: 5 minutes
3. Set up email alerts
```

**Vercel Monitoring (Built-in):**
```
Dashboard > Analytics
- Real-time traffic
- Error rates
- Response times
- Geographic distribution
```

### Error Tracking (Optional)

**Sentry (Free Tier):**
```bash
# Install
bun add @sentry/nextjs

# Initialize
npx @sentry/wizard@latest -i nextjs

# Configure in Vercel env vars
SENTRY_DSN=[your-dsn]
```

---

## 🔐 Security Best Practices

### Rotate Secrets Regularly

**Every 90 days:**
```bash
# 1. Generate new BETTER_AUTH_SECRET
openssl rand -base64 32

# 2. Update in Vercel env vars
# 3. Redeploy (automatic)

# 4. Old sessions will be invalidated
# 5. Users will need to re-login
```

### Monitor Failed Login Attempts

**Check logs:**
```bash
vercel logs --follow | grep "auth"
```

### Rate Limiting

**Already configured:**
- Uses Upstash Redis
- Configured in middleware
- Prevents DDoS attacks

---

## 📅 Maintenance Schedule

### Daily (Automated)
- ✅ Vercel auto-deploys on git push
- ✅ Neon auto-backups database
- ✅ Upstash auto-scales Redis

### Weekly (Manual)
- [ ] Check Vercel analytics for errors
- [ ] Review deployment logs
- [ ] Monitor free tier usage

### Monthly (Manual)
- [ ] Update dependencies (`bun update`)
- [ ] Review and clean old database sessions
- [ ] Check security advisories
- [ ] Review performance metrics

### Quarterly (Manual)
- [ ] Rotate authentication secrets
- [ ] Audit database size
- [ ] Review and optimize costs
- [ ] Update documentation

---

## 🆘 Emergency Procedures

### Critical Bug in Production

```bash
# 1. Rollback to previous deployment
# Via Vercel Dashboard:
# Deployments > Previous deployment > Promote to Production

# Via CLI:
vercel rollback [previous-deployment-url]

# 2. Fix bug locally
# 3. Test thoroughly
# 4. Deploy fix
git commit -m "Fix critical bug"
git push origin main
```

### Database Corruption

```bash
# 1. Neon has automatic backups
# Visit: https://console.neon.tech
# Select project > Restore > Choose point in time

# 2. Or restore from migration
cd apps/web
bun run db:push:prod
```

### Complete Service Outage

```bash
# 1. Check Vercel status
curl https://www.vercel-status.com/api/v2/status.json

# 2. Check Neon status
# Visit: https://neon.tech/status

# 3. Check Upstash status
# Visit: https://status.upstash.com

# 4. If all services are up, redeploy
vercel --prod --force

# 5. If issue persists, contact support
# Vercel: https://vercel.com/support
```

---

## 📞 Support Contacts

| Service | Support | Response Time |
|---------|---------|---------------|
| **Vercel** | https://vercel.com/support | 24-48 hours (free tier) |
| **Neon** | https://neon.tech/docs/introduction/support | Community forum |
| **Upstash** | https://upstash.com/docs | Email support |
| **Modal** | https://modal.com/docs | Community Discord |

---

## 🎯 Quick Commands Reference

```bash
# Deploy to production
vercel --prod

# Force rebuild
vercel --prod --force

# Rollback deployment
vercel rollback [deployment-url]

# View logs
vercel logs --follow

# List deployments
vercel ls

# Check environment variables
vercel env ls

# Add environment variable
vercel env add [KEY]

# Remove environment variable
vercel env rm [KEY]

# Run database migrations
cd apps/web && bun run db:migrate

# Check build locally
cd apps/web && bun run build

# Start local server
cd apps/web && bun run dev
```

---

## ✅ Server Management Checklist

### Before Deployment
- [ ] All environment variables set in Vercel
- [ ] Database migrations run
- [ ] Build succeeds locally
- [ ] Tests pass (when implemented)
- [ ] Security secrets rotated

### After Deployment
- [ ] Health check endpoint responds
- [ ] Authentication works
- [ ] Database connections successful
- [ ] Redis cache operational
- [ ] No errors in logs
- [ ] Performance metrics acceptable

### Regular Maintenance
- [ ] Monitor free tier limits
- [ ] Review error logs weekly
- [ ] Update dependencies monthly
- [ ] Rotate secrets quarterly
- [ ] Backup verification quarterly

---

**Remember**: With Vercel's serverless architecture, you don't need traditional server restarts. Every request is handled by a fresh function instance, ensuring your application is always running the latest code after deployment.
