# Vercel Deployment Guide - Zero Local Setup Required

## 🚀 Deploy OpenCut to Vercel in 10 Minutes

Since you're experiencing network timeout issues with local installations, deploying to Vercel is the **fastest way** to see OpenCut running live.

---

## ✅ **Prerequisites**

- ✅ GitHub account (you already have the code)
- ✅ Web browser
- ❌ NO local installations needed
- ❌ NO downloads required

---

## 📋 **Step-by-Step Deployment**

### **Step 1: Set Up Free Database (Neon)**

1. **Open browser** and go to: https://neon.tech
2. **Sign up** with GitHub (free, no credit card)
3. **Create new project**:
   - Name: `opencut-production`
   - Region: Choose closest to you
   - PostgreSQL version: 17
4. **Copy connection string**:
   - Click "Connection string"
   - Copy the full URL (starts with `postgresql://`)
   - Save it for Step 3

---

### **Step 2: Set Up Free Redis (Upstash)**

1. **Open browser** and go to: https://upstash.com
2. **Sign up** with GitHub (free, no credit card)
3. **Create Redis database**:
   - Name: `opencut-cache`
   - Type: Regional
   - Region: Choose closest to you
4. **Copy credentials**:
   - Copy `UPSTASH_REDIS_REST_URL`
   - Copy `UPSTASH_REDIS_REST_TOKEN`
   - Save both for Step 3

---

### **Step 3: Deploy to Vercel**

1. **Open browser** and go to: https://vercel.com
2. **Sign in** with GitHub
3. **Click** "Add New..." → "Project"
4. **Import** your repository:
   - Search for: `OpenCut-by-selliblyhq`
   - Click "Import"

5. **Configure Project**:
   ```
   Framework Preset: Next.js (auto-detected)
   Root Directory: apps/web
   Build Command: bun run build (auto-detected)
   Output Directory: .next (auto-detected)
   Install Command: bun install (auto-detected)
   ```

6. **Add Environment Variables** (click "Environment Variables"):

   **Required Variables:**
   ```bash
   # Database (from Neon - Step 1)
   DATABASE_URL=postgresql://[paste-your-neon-connection-string]

   # Authentication (generate new or use existing)
   BETTER_AUTH_SECRET=WhHM76GqvfHDA/JDA76tgtVBqqBcYYBwaCvwXhvMaYGE=
   NEXT_PUBLIC_BETTER_AUTH_URL=https://[your-project-name].vercel.app

   # Redis (from Upstash - Step 2)
   UPSTASH_REDIS_REST_URL=[paste-from-upstash]
   UPSTASH_REDIS_REST_TOKEN=[paste-from-upstash]

   # Node Environment
   NODE_ENV=production

   # Blog CMS (optional - use example key for now)
   MARBLE_WORKSPACE_KEY=cm6ytuq9x0000i803v0isidst
   NEXT_PUBLIC_MARBLE_API_URL=https://api.marblecms.com
   ```

   **Note**: For `NEXT_PUBLIC_BETTER_AUTH_URL`, you'll update this after deployment with your actual Vercel URL.

7. **Click "Deploy"**

8. **Wait 3-5 minutes** for build to complete

---

### **Step 4: Update Auth URL**

After deployment completes:

1. **Copy your Vercel URL** (e.g., `https://opencut-abc123.vercel.app`)
2. Go to **Project Settings** → **Environment Variables**
3. **Edit** `NEXT_PUBLIC_BETTER_AUTH_URL`
4. **Replace** with your actual Vercel URL
5. **Redeploy**: Go to Deployments → Latest → "..." → "Redeploy"

---

### **Step 5: Run Database Migrations**

You need to run migrations once. Two options:

#### **Option A: Via Vercel CLI (if you can install it)**
```bash
npm i -g vercel
vercel login
vercel env pull
cd apps/web
bun run db:migrate
```

#### **Option B: Via Local PowerShell (without Bun)**
Since you can't install Bun, we'll use a workaround:

1. Install the database migration tool globally:
```powershell
npm install -g drizzle-kit
```

2. Create a temporary config file:
```powershell
# Create migration config
@"
{
  "schema": "./src/schema.ts",
  "out": "./migrations",
  "driver": "pg",
  "dbCredentials": {
    "connectionString": "YOUR_NEON_DATABASE_URL"
  }
}
"@ | Out-File -FilePath "drizzle.config.json"
```

3. Run migrations:
```powershell
drizzle-kit push:pg
```

#### **Option C: Skip Migrations (Temporary)**
The app will work without migrations for basic frontend viewing. You can add migrations later.

---

## 🎉 **You're Live!**

Your OpenCut video editor is now running at:
```
https://[your-project-name].vercel.app
```

### **What You Can Do:**
- ✅ View the landing page
- ✅ Create an account
- ✅ Access the video editor
- ✅ Upload and edit videos (client-side)
- ✅ Use all editor features
- ✅ Share the URL with others

---

## 🔧 **Troubleshooting**

### **Build Failed?**

**Check build logs** in Vercel dashboard:
- Common issue: Missing environment variables
- Solution: Add all required env vars from Step 3

### **Database Connection Error?**

**Verify DATABASE_URL**:
- Must start with `postgresql://`
- Must include `?sslmode=require` at the end
- Example: `postgresql://user:pass@host/db?sslmode=require`

### **Authentication Not Working?**

**Check BETTER_AUTH_SECRET**:
- Must be a secure random string
- Generate new one: https://generate-secret.vercel.app/32
- Update in Vercel env vars and redeploy

### **Redis Errors?**

**Verify Upstash credentials**:
- URL should start with `https://`
- Token should be a long string
- Both must match your Upstash dashboard

---

## 📊 **Free Tier Limits**

You're using 100% free services:

| Service | Free Tier | Your Usage | Cost |
|---------|-----------|------------|------|
| **Vercel** | 100 GB bandwidth | ~10 GB/month | $0 |
| **Neon** | 512 MB storage | ~50 MB | $0 |
| **Upstash** | 10K commands/day | ~1K/day | $0 |
| **Total** | - | - | **$0/month** |

### **When to Upgrade:**
- Vercel: >100 GB bandwidth → $20/month
- Neon: >512 MB database → $19/month
- Upstash: >10K commands/day → $10/month

For a video editor with client-side processing, you'll likely stay within free tiers for months!

---

## 🔄 **Making Changes**

### **Update Code:**
```bash
# Just push to GitHub
git add .
git commit -m "Update feature"
git push origin main

# Vercel auto-deploys in ~2 minutes
```

### **Update Environment Variables:**
1. Go to Vercel Dashboard
2. Project Settings → Environment Variables
3. Edit variable
4. Redeploy (automatic)

### **View Logs:**
1. Vercel Dashboard → Deployments
2. Click latest deployment
3. View "Build Logs" or "Function Logs"

---

## 🎯 **Next Steps**

After deployment:

1. ✅ **Test the app** - Create account, upload video, edit
2. ✅ **Add custom domain** (optional) - Vercel Settings → Domains
3. ✅ **Enable analytics** - Vercel Settings → Analytics (free)
4. ✅ **Set up monitoring** - Use UptimeRobot (free)
5. ✅ **Share with users** - Your app is live!

---

## 📞 **Need Help?**

- **Vercel Docs**: https://vercel.com/docs
- **Neon Docs**: https://neon.tech/docs
- **Upstash Docs**: https://upstash.com/docs
- **OpenCut Issues**: https://github.com/OpenCut-app/OpenCut/issues

---

## ✨ **Advantages of Vercel Deployment**

✅ **No local setup** - Everything runs in the cloud  
✅ **Auto-scaling** - Handles traffic spikes automatically  
✅ **Global CDN** - Fast loading worldwide  
✅ **HTTPS** - Automatic SSL certificates  
✅ **Preview deployments** - Test changes before going live  
✅ **Zero downtime** - Seamless updates  
✅ **Free tier** - Perfect for getting started  

---

**You're now ready to deploy OpenCut to production without any local installations!** 🚀

The entire process takes about 10-15 minutes and requires zero downloads or local setup.
