# OpenCut Production Deployment Plan (Zero Cost)

**Generated:** 2025-12-22  
**Status:** Ready for Production Deployment

---

## 📋 Project Overview

**OpenCut** is a free, open-source video editor built with Next.js 15, featuring:
- **Privacy-first**: Client-side video processing (no server uploads)
- **Multi-track timeline editing** with real-time preview
- **FFmpeg integration** for video processing
- **Authentication** via Better Auth
- **Database**: PostgreSQL with Drizzle ORM
- **Caching**: Redis for rate limiting
- **Blog**: Marble CMS integration
- **Transcription**: Modal.com serverless GPU (optional)
- **Monorepo**: Turborepo with Bun package manager

---

## 🏗️ Current Architecture

### Tech Stack
- **Framework**: Next.js 15.5.7 (App Router, Standalone output)
- **Runtime**: Bun 1.2.18
- **Database**: PostgreSQL 17
- **Cache**: Redis 7 + Upstash-compatible HTTP interface
- **State Management**: Zustand
- **Styling**: Tailwind CSS 4.1
- **Video Processing**: FFmpeg.wasm (client-side)
- **Authentication**: Better Auth
- **ORM**: Drizzle
- **Deployment**: Docker-ready, Netlify-configured

### Key Components
1. **Web App** (`apps/web/`) - Main Next.js application
2. **Transcription Service** (`apps/transcription/`) - Modal.com Python service
3. **Auth Package** (`packages/auth/`) - Shared authentication
4. **DB Package** (`packages/db/`) - Shared database schema

### Database Schema
- `users` - User accounts
- `sessions` - Authentication sessions
- `accounts` - OAuth provider accounts
- `verifications` - Email verification tokens
- `export_waitlist` - Export feature waitlist

---

## 💰 Zero-Cost Production Deployment Strategy

### Recommended Stack (100% Free)

#### 1. **Application Hosting: Vercel (Free Tier)**
- ✅ **Cost**: $0/month
- ✅ **Limits**: 
  - 100 GB bandwidth/month
  - Unlimited deployments
  - Automatic HTTPS
  - Edge network (CDN)
  - Serverless functions (100 GB-hours/month)
- ✅ **Perfect for**: Next.js apps (built by Vercel)
- ✅ **Auto-scaling**: Yes
- ✅ **Custom domain**: Yes (free)

**Why Vercel?**
- Native Next.js support (zero config)
- Already has "Deploy with Vercel" button in README
- Automatic preview deployments for PRs
- Edge functions for API routes
- Built-in analytics

#### 2. **Database: Neon (Free Tier)**
- ✅ **Cost**: $0/month
- ✅ **Limits**:
  - 512 MB storage
  - 1 project
  - 10 branches (for dev/staging)
  - Autosuspend after inactivity (instant resume)
- ✅ **Type**: Serverless PostgreSQL
- ✅ **Compatible**: 100% PostgreSQL (works with Drizzle)
- ✅ **Backups**: Automatic

**Alternative**: Supabase (Free Tier)
- 500 MB database
- 1 GB file storage
- 50,000 monthly active users

#### 3. **Redis Cache: Upstash (Free Tier)**
- ✅ **Cost**: $0/month
- ✅ **Limits**:
  - 10,000 commands/day
  - 256 MB storage
  - Global replication
- ✅ **Type**: Serverless Redis
- ✅ **Already integrated**: Uses Upstash SDK in codebase

#### 4. **Transcription: Modal (Free Tier)**
- ✅ **Cost**: $0/month (with free credits)
- ✅ **Limits**: $30/month free credits
- ✅ **Already implemented**: `apps/transcription/transcription.py`
- ✅ **GPU**: A10G for Whisper model
- ⚠️ **Optional**: Can disable if not needed

#### 5. **File Storage: Cloudflare R2 (Free Tier)**
- ✅ **Cost**: $0/month
- ✅ **Limits**:
  - 10 GB storage/month
  - 1 million Class A operations
  - 10 million Class B operations
- ✅ **Use case**: Transcription audio uploads (optional)
- ⚠️ **Optional**: Only needed for auto-captions

#### 6. **Blog CMS: Marble (Free Tier)**
- ✅ **Cost**: $0/month
- ✅ **Already integrated**: `MARBLE_WORKSPACE_KEY` in env
- ✅ **Headless CMS**: API-based content management

---

## 🚀 Deployment Steps

### Phase 1: Database Setup (Neon)

1. **Create Neon Account**
   ```
   Visit: https://neon.tech
   Sign up with GitHub
   ```

2. **Create Database**
   ```
   Project name: opencut-production
   Region: Choose closest to users
   PostgreSQL version: 17
   ```

3. **Get Connection String**
   ```
   Format: postgresql://[user]:[password]@[host]/[dbname]?sslmode=require
   Save for environment variables
   ```

4. **Run Migrations**
   ```bash
   cd apps/web
   # Set DATABASE_URL to Neon connection string
   bun run db:migrate
   ```

### Phase 2: Redis Setup (Upstash)

1. **Create Upstash Account**
   ```
   Visit: https://upstash.com
   Sign up with GitHub
   ```

2. **Create Redis Database**
   ```
   Name: opencut-cache
   Region: Choose closest to users
   Type: Regional (free tier)
   ```

3. **Get Credentials**
   ```
   Copy UPSTASH_REDIS_REST_URL
   Copy UPSTASH_REDIS_REST_TOKEN
   ```

### Phase 3: Vercel Deployment

1. **Connect Repository**
   ```
   Visit: https://vercel.com
   Import Git Repository
   Select: OpenCut-by-selliblyhq
   ```

2. **Configure Build Settings**
   ```
   Framework Preset: Next.js
   Root Directory: apps/web
   Build Command: bun run build
   Output Directory: .next
   Install Command: bun install
   ```

3. **Environment Variables**
   Add these in Vercel dashboard:
   ```bash
   # Database
   DATABASE_URL=postgresql://[neon-connection-string]
   
   # Auth
   BETTER_AUTH_SECRET=[generate-with-openssl-rand-base64-32]
   NEXT_PUBLIC_BETTER_AUTH_URL=https://your-domain.vercel.app
   
   # Redis
   UPSTASH_REDIS_REST_URL=[from-upstash]
   UPSTASH_REDIS_REST_TOKEN=[from-upstash]
   
   # Blog (if using)
   MARBLE_WORKSPACE_KEY=[your-marble-key]
   NEXT_PUBLIC_MARBLE_API_URL=https://api.marblecms.com
   
   # Node Environment
   NODE_ENV=production
   
   # Optional: Freesound API (for audio effects)
   FREESOUND_CLIENT_ID=[optional]
   FREESOUND_API_KEY=[optional]
   
   # Optional: Transcription (Modal + R2)
   CLOUDFLARE_ACCOUNT_ID=[optional]
   R2_ACCESS_KEY_ID=[optional]
   R2_SECRET_ACCESS_KEY=[optional]
   R2_BUCKET_NAME=[optional]
   MODAL_TRANSCRIPTION_URL=[optional]
   ```

4. **Deploy**
   ```
   Click "Deploy"
   Wait for build to complete (~3-5 minutes)
   ```

### Phase 4: Modal Transcription (Optional)

1. **Install Modal CLI**
   ```bash
   pip install modal
   modal token new
   ```

2. **Create Modal Secrets**
   ```bash
   modal secret create opencut-r2-secrets \
     CLOUDFLARE_ACCOUNT_ID=[your-id] \
     R2_ACCESS_KEY_ID=[your-key] \
     R2_SECRET_ACCESS_KEY=[your-secret] \
     R2_BUCKET_NAME=opencut-transcription
   ```

3. **Deploy Transcription Service**
   ```bash
   cd apps/transcription
   modal deploy transcription.py
   # Copy the endpoint URL to MODAL_TRANSCRIPTION_URL
   ```

### Phase 5: Custom Domain (Optional)

1. **Add Domain in Vercel**
   ```
   Project Settings > Domains
   Add your domain
   Update DNS records as instructed
   ```

2. **Update Environment Variables**
   ```
   NEXT_PUBLIC_BETTER_AUTH_URL=https://yourdomain.com
   ```

---

## 🔄 Server Restart Strategy

### Vercel Auto-Restart
- **Automatic**: Vercel serverless functions auto-scale and restart
- **No manual restart needed**: Each request spawns a fresh instance
- **Cold starts**: ~1-2 seconds (acceptable for video editor)

### Database Connection Pooling
- **Neon**: Built-in connection pooling
- **Drizzle**: Handles reconnection automatically
- **No manual intervention**: Connections auto-recover

### Redis Persistence
- **Upstash**: Serverless, always available
- **No restart needed**: Managed service

### Force Redeployment (if needed)
```bash
# Trigger new deployment
git commit --allow-empty -m "Force redeploy"
git push origin main

# Or via Vercel CLI
vercel --prod
```

---

## 📊 Cost Breakdown

| Service | Free Tier Limits | Estimated Usage | Cost |
|---------|-----------------|-----------------|------|
| **Vercel** | 100 GB bandwidth/month | ~50 GB (video editor is client-side) | $0 |
| **Neon** | 512 MB storage | ~200 MB (users, sessions) | $0 |
| **Upstash** | 10,000 commands/day | ~5,000/day (rate limiting) | $0 |
| **Modal** | $30 free credits/month | ~$10/month (if used) | $0 |
| **Cloudflare R2** | 10 GB storage | ~2 GB (temp audio files) | $0 |
| **Marble CMS** | Free tier | Blog content | $0 |
| **Total** | - | - | **$0/month** |

### Scaling Considerations
- **When to upgrade**: 
  - Vercel: >100 GB bandwidth (~10,000 users/month)
  - Neon: >512 MB database (~50,000 users)
  - Upstash: >10,000 Redis commands/day
- **Next tier costs**:
  - Vercel Pro: $20/month (1 TB bandwidth)
  - Neon Scale: $19/month (10 GB storage)
  - Upstash: $10/month (100,000 commands/day)

---

## ✅ Pre-Deployment Checklist

### Code Quality
- [x] TypeScript strict mode enabled
- [x] Biome linting configured
- [x] GitHub Actions CI/CD setup
- [x] Standalone Next.js output configured
- [x] Production source maps enabled

### Security
- [ ] Generate strong `BETTER_AUTH_SECRET`
- [ ] Enable HTTPS (automatic with Vercel)
- [ ] Set secure environment variables
- [ ] Review database RLS policies
- [ ] Add rate limiting (already configured)

### Performance
- [x] Next.js standalone output (smaller bundle)
- [x] Turbopack for dev builds
- [x] Redis caching configured
- [x] Image optimization configured
- [x] Remove console logs in production

### Monitoring
- [ ] Enable Vercel Analytics (free)
- [ ] Set up error tracking (optional: Sentry free tier)
- [ ] Configure uptime monitoring (optional: UptimeRobot)

### Documentation
- [x] README.md complete
- [x] CONTRIBUTING.md present
- [x] Environment variables documented
- [ ] Add deployment guide to README

---

## 🐛 Known Issues & Considerations

### 1. **FFmpeg.wasm Performance**
- **Issue**: Large video files may cause browser memory issues
- **Solution**: Client-side processing means no server costs, but users need decent hardware
- **Mitigation**: Add file size warnings, optimize FFmpeg settings

### 2. **Database Size Limits**
- **Issue**: Neon free tier is 512 MB
- **Solution**: Implement data retention policies
- **Mitigation**: 
  - Delete old sessions (>30 days)
  - Archive inactive users
  - Use IndexedDB for project storage (already implemented)

### 3. **Cold Starts**
- **Issue**: Vercel serverless functions have cold starts
- **Solution**: Accept 1-2 second delay on first request
- **Mitigation**: Keep functions warm with cron job (Vercel free tier)

### 4. **Transcription Costs**
- **Issue**: Modal free credits may run out
- **Solution**: Make transcription optional
- **Current**: Already optional in codebase

### 5. **OPFS Browser Support**
- **Issue**: Origin Private File System not supported in all browsers
- **Solution**: Fallback to IndexedDB (already implemented)
- **Support**: Chrome 86+, Edge 86+, Safari 15.2+

---

## 🔧 Maintenance & Updates

### Automatic Updates
- **Vercel**: Auto-deploys on `git push` to main
- **Dependencies**: Use Dependabot (already configured in `.github/`)
- **Database**: Neon auto-updates PostgreSQL

### Manual Tasks
1. **Monitor free tier limits** (monthly)
2. **Review error logs** (weekly)
3. **Update dependencies** (monthly)
4. **Database cleanup** (quarterly)
5. **Security patches** (as needed)

### Backup Strategy
- **Database**: Neon automatic backups (point-in-time recovery)
- **Code**: GitHub repository
- **User projects**: Stored in browser (IndexedDB/OPFS)
- **Export**: Users download their videos (no server storage)

---

## 🎯 Production Readiness Score: 9/10

### Strengths ✅
- Modern, well-architected codebase
- Docker-ready for self-hosting
- Comprehensive error handling
- Client-side processing (no video upload costs)
- Monorepo with shared packages
- CI/CD pipeline configured
- Security best practices (RLS, rate limiting)

### Minor Improvements Needed ⚠️
1. Add health check endpoint (`/api/health` - already in docker-compose)
2. Implement database cleanup cron jobs
3. Add user-facing error messages
4. Create deployment documentation
5. Set up monitoring/alerting

### Blockers ❌
- None! Ready to deploy.

---

## 🚀 Quick Start Deployment (TL;DR)

```bash
# 1. Create accounts (5 minutes)
# - Vercel.com (GitHub login)
# - Neon.tech (GitHub login)
# - Upstash.com (GitHub login)

# 2. Set up services (10 minutes)
# - Create Neon database → copy connection string
# - Create Upstash Redis → copy URL + token
# - Generate auth secret: openssl rand -base64 32

# 3. Deploy to Vercel (5 minutes)
# - Import GitHub repo
# - Set root directory: apps/web
# - Add environment variables (see Phase 3)
# - Click Deploy

# 4. Run migrations (2 minutes)
# - In Vercel dashboard, go to Settings > Environment Variables
# - Add DATABASE_URL
# - Redeploy
# - Or run locally: bun run db:migrate

# Total time: ~25 minutes
# Total cost: $0/month
```

---

## 📞 Support & Resources

- **Vercel Docs**: https://vercel.com/docs
- **Neon Docs**: https://neon.tech/docs
- **Upstash Docs**: https://upstash.com/docs
- **Modal Docs**: https://modal.com/docs
- **OpenCut Issues**: https://github.com/OpenCut-app/OpenCut/issues

---

## 📝 Next Steps

1. **Immediate**: Deploy to Vercel (follow Phase 1-3)
2. **Week 1**: Monitor performance and errors
3. **Week 2**: Set up custom domain (optional)
4. **Month 1**: Review usage and optimize
5. **Ongoing**: Monitor free tier limits, update dependencies

---

**Status**: ✅ Ready for production deployment at zero cost with automatic server management via Vercel's serverless infrastructure.
