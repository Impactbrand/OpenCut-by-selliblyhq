# Fix Environment Variables - Final Step!

## 🎉 **Great Progress!**

✅ All dependencies installed successfully  
✅ Code compiled successfully  
✅ Type checking passed  
❌ **Environment variables need to be configured**

---

## 🔍 **The Problem**

Your Vercel deployment is using **local development values** for:
- `DATABASE_URL` → Points to `localhost:5432` (won't work on Vercel)
- `UPSTASH_REDIS_REST_URL` → Points to `localhost:8079` (won't work on Vercel)

You need **production database and Redis URLs**.

---

## ✅ **Solution: Set Up Free Services**

### **Step 1: Create Neon Database (Free PostgreSQL)**

1. **Go to**: https://neon.tech
2. **Sign up** with GitHub (instant, free)
3. **Create a new project**:
   - Name: `opencut-db`
   - Region: Choose closest to you
   - PostgreSQL version: 17 (latest)
4. **Copy the connection string**:
   - Click "Connection Details"
   - Copy the **"Connection string"** (starts with `postgresql://`)
   - It looks like: `postgresql://username:password@ep-xxx.us-east-2.aws.neon.tech/neondb?sslmode=require`

---

### **Step 2: Create Upstash Redis (Free Redis Cache)**

1. **Go to**: https://upstash.com
2. **Sign up** with GitHub (instant, free)
3. **Create a new database**:
   - Name: `opencut-redis`
   - Type: Regional
   - Region: Choose closest to you
   - Enable TLS: Yes
4. **Copy the credentials**:
   - Go to "REST API" tab
   - Copy `UPSTASH_REDIS_REST_URL` (starts with `https://`)
   - Copy `UPSTASH_REDIS_REST_TOKEN` (long string)

---

### **Step 3: Update Vercel Environment Variables**

1. **Go to Vercel Dashboard**: https://vercel.com/dashboard
2. **Click your project** (OpenCut)
3. **Go to Settings** → **Environment Variables**
4. **Update these variables**:

#### **Required Variables:**

```bash
# Database (from Neon)
DATABASE_URL=postgresql://username:password@ep-xxx.us-east-2.aws.neon.tech/neondb?sslmode=require

# Redis Cache (from Upstash)
UPSTASH_REDIS_REST_URL=https://xxx-xxx-xxx.upstash.io
UPSTASH_REDIS_REST_TOKEN=AxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxA==

# Authentication
BETTER_AUTH_SECRET=WhHM76GqvfHDA/JDA76tgtVBqqBcYYBwaCvwXhvMaYGE=
NEXT_PUBLIC_BETTER_AUTH_URL=https://your-project.vercel.app

# Environment
NODE_ENV=production

# Blog (safe to use example)
MARBLE_WORKSPACE_KEY=cm6ytuq9x0000i803v0isidst
NEXT_PUBLIC_MARBLE_API_URL=https://api.marblecms.com
```

#### **How to Update:**

For each variable:
1. Find it in the list
2. Click "Edit"
3. Paste the new value
4. Click "Save"

**OR** delete the old ones and add new ones:
1. Click "Add New"
2. Key: `DATABASE_URL`
3. Value: Your Neon connection string
4. Environment: Production, Preview, Development (check all)
5. Click "Save"

---

### **Step 4: Update NEXT_PUBLIC_BETTER_AUTH_URL**

After you get your Vercel deployment URL:

1. Copy your Vercel URL (e.g., `https://opencut-xyz.vercel.app`)
2. Update `NEXT_PUBLIC_BETTER_AUTH_URL` to that URL
3. Save

---

### **Step 5: Redeploy**

After updating environment variables:

1. **Go to Deployments** tab
2. **Click the latest deployment**
3. **Click the ⋯ menu** (three dots)
4. **Click "Redeploy"**
5. **Check "Use existing Build Cache"**
6. **Click "Redeploy"**

---

## 📋 **Environment Variables Checklist**

### **✅ Required (Must Set):**
- [ ] `DATABASE_URL` - Neon PostgreSQL URL
- [ ] `UPSTASH_REDIS_REST_URL` - Upstash Redis URL
- [ ] `UPSTASH_REDIS_REST_TOKEN` - Upstash Redis token
- [ ] `BETTER_AUTH_SECRET` - Already set ✅
- [ ] `NEXT_PUBLIC_BETTER_AUTH_URL` - Your Vercel URL
- [ ] `NODE_ENV` - Set to `production`

### **✅ Optional (Can Use Examples):**
- [ ] `MARBLE_WORKSPACE_KEY` - Use example ✅
- [ ] `MARBLE_API_URL` - Use example ✅

### **⚠️ Skip for Now:**
- `FREESOUND_CLIENT_ID` - Not needed
- `FREESOUND_API_KEY` - Not needed
- `CLOUDFLARE_ACCOUNT_ID` - Not needed
- `R2_ACCESS_KEY_ID` - Not needed
- `R2_SECRET_ACCESS_KEY` - Not needed
- `R2_BUCKET_NAME` - Not needed
- `MODAL_TRANSCRIPTION_URL` - Not needed

---

## 🎯 **Quick Setup (5 Minutes)**

### **Neon Setup (2 minutes):**
1. Visit https://neon.tech
2. Sign up with GitHub
3. Create project
4. Copy connection string

### **Upstash Setup (2 minutes):**
1. Visit https://upstash.com
2. Sign up with GitHub
3. Create database
4. Copy URL and token

### **Vercel Update (1 minute):**
1. Go to Settings → Environment Variables
2. Update `DATABASE_URL`
3. Update `UPSTASH_REDIS_REST_URL`
4. Update `UPSTASH_REDIS_REST_TOKEN`
5. Update `NEXT_PUBLIC_BETTER_AUTH_URL`
6. Save and redeploy

---

## ✅ **After Setup**

Once you update the environment variables and redeploy:

1. ✅ Build will succeed
2. ✅ Database will connect
3. ✅ Redis will work
4. ✅ App will be live
5. ✅ All features functional

**Total time**: ~10 minutes (including signups)

---

## 🐛 **Common Issues**

### **Issue**: "Invalid DATABASE_URL"
**Fix**: Make sure you copied the full connection string from Neon, including `?sslmode=require`

### **Issue**: "Redis connection failed"
**Fix**: Make sure you copied both URL and TOKEN from Upstash REST API tab

### **Issue**: "Auth not working"
**Fix**: Make sure `NEXT_PUBLIC_BETTER_AUTH_URL` matches your actual Vercel URL

---

## 💡 **Pro Tips**

1. **Copy-paste carefully** - Don't add extra spaces or quotes
2. **Use the REST API tab** in Upstash (not the native tab)
3. **Include `?sslmode=require`** at the end of DATABASE_URL
4. **Set all 3 environments** (Production, Preview, Development) for each variable
5. **Redeploy after changes** - Environment variables don't auto-apply

---

## 📊 **Cost Breakdown**

| Service | Free Tier | What You Get |
|---------|-----------|--------------|
| **Neon** | Free forever | 0.5 GB storage, 1 project |
| **Upstash** | Free forever | 10,000 commands/day |
| **Vercel** | Free forever | 100 GB bandwidth/month |
| **Total** | **$0/month** | Fully functional app |

---

## 🚀 **Next Steps**

1. ✅ Create Neon database (2 min)
2. ✅ Create Upstash Redis (2 min)
3. ✅ Update Vercel env vars (1 min)
4. ✅ Redeploy (3 min)
5. ✅ **Your app is live!** 🎉

---

**Let me know when you've set up Neon and Upstash, and I'll help you configure the environment variables!** 🚀
