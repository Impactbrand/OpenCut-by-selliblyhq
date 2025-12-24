# Bun Installation Troubleshooting Guide

## 🔍 Issue Detected
Your system is experiencing network connectivity issues when trying to download Bun from GitHub. This is preventing automatic installation via PowerShell scripts.

---

## ✅ **Solution 1: Manual Download (Recommended)**

### Step 1: Download Bun Manually
1. **Open your web browser** (Chrome, Edge, Firefox)
2. **Navigate to**: https://github.com/oven-sh/bun/releases/tag/bun-v1.2.18
3. **Download**: `bun-windows-x64.zip` (click the link under "Assets")
4. **Save to**: `C:\Users\USER\Downloads\bun.zip`

### Step 2: Extract and Install
```powershell
# Run these commands in PowerShell:

# 1. Create Bun directory
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.bun\bin"

# 2. Extract the zip file
Expand-Archive -Path "$env:USERPROFILE\Downloads\bun.zip" -DestinationPath "$env:USERPROFILE\.bun" -Force

# 3. Move bun.exe to the bin folder
Move-Item -Path "$env:USERPROFILE\.bun\bun-windows-x64\bun.exe" -Destination "$env:USERPROFILE\.bun\bin\bun.exe" -Force

# 4. Add to PATH (User environment variable)
$currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
$bunPath = "$env:USERPROFILE\.bun\bin"
if ($currentPath -notlike "*$bunPath*") {
    [Environment]::SetEnvironmentVariable("Path", "$currentPath;$bunPath", "User")
    Write-Host "✅ Bun added to PATH. Please restart PowerShell."
}

# 5. Verify installation (after restarting PowerShell)
bun --version
```

### Step 3: Restart PowerShell
**Important**: Close and reopen PowerShell for PATH changes to take effect.

### Step 4: Verify Installation
```powershell
bun --version
# Should output: 1.2.18
```

---

## ✅ **Solution 2: Use Alternative Download Method**

If the GitHub download is slow, try using a mirror or CDN:

```powershell
# Download from alternative source
curl.exe -L -o "$env:USERPROFILE\Downloads\bun.zip" "https://github.com/oven-sh/bun/releases/download/bun-v1.2.18/bun-windows-x64.zip"

# Then follow Step 2 from Solution 1
```

---

## ✅ **Solution 3: Use Scoop Package Manager**

Scoop is a Windows package manager that might have better network handling:

```powershell
# Install Scoop (if not already installed)
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression

# Install Bun via Scoop
scoop install bun

# Verify
bun --version
```

---

## ✅ **Solution 4: Use WSL (Windows Subsystem for Linux)**

If you have WSL installed, you can use the Linux version:

```bash
# In WSL terminal:
curl -fsSL https://bun.sh/install | bash

# Verify
bun --version

# Navigate to project
cd /mnt/c/Users/USER/.gemini/antigravity/scratch/OpenCut-by-selliblyhq/apps/web

# Install and run
bun install
bun run dev
```

---

## ✅ **Solution 5: Use Docker (No Bun Installation Needed)**

If you have Docker Desktop installed, you can run the entire project in a container:

```powershell
# Start Docker Desktop first, then:
cd c:\Users\USER\.gemini\antigravity\scratch\OpenCut-by-selliblyhq

# Start all services (database, Redis, and web app)
docker-compose up

# Access the app at: http://localhost:3100
```

**Note**: The docker-compose.yaml is already configured in the project root.

---

## ✅ **Solution 6: Deploy to Vercel (No Local Setup)**

Skip local installation entirely and deploy to Vercel:

### Quick Deploy Steps:
1. **Go to**: https://vercel.com
2. **Sign in** with GitHub
3. **Import** your repository
4. **Configure**:
   - Root Directory: `apps/web`
   - Build Command: `bun run build`
   - Install Command: `bun install`
5. **Add Environment Variables**:
   ```
   DATABASE_URL=postgresql://... (from Neon.tech)
   BETTER_AUTH_SECRET=WhHM76GqvfHDA/JDA76tgtVBqqBcYYBwaCvwXhvMaYGE=
   NEXT_PUBLIC_BETTER_AUTH_URL=https://your-app.vercel.app
   UPSTASH_REDIS_REST_URL=... (from Upstash.com)
   UPSTASH_REDIS_REST_TOKEN=... (from Upstash.com)
   NODE_ENV=production
   ```
6. **Click Deploy**

You'll have a live URL in ~5 minutes!

---

## 🔧 **Troubleshooting Network Issues**

### Check Your Network Connection
```powershell
# Test GitHub connectivity
Test-NetConnection -ComputerName github.com -Port 443

# Test DNS resolution
Resolve-DnsName github.com

# Check if proxy is blocking
$env:HTTP_PROXY
$env:HTTPS_PROXY
```

### Bypass Proxy (if applicable)
```powershell
# Temporarily disable proxy
$env:HTTP_PROXY = ""
$env:HTTPS_PROXY = ""

# Try download again
```

### Check Firewall/Antivirus
- Temporarily disable Windows Firewall
- Disable antivirus (temporarily)
- Check corporate network restrictions

### Use VPN (if available)
- Connect to a VPN
- Try download again

---

## 📋 **After Bun is Installed**

Once Bun is successfully installed, run these commands:

```powershell
# Navigate to project
cd c:\Users\USER\.gemini\antigravity\scratch\OpenCut-by-selliblyhq\apps\web

# Install dependencies
bun install

# Start development server
bun run dev

# Open browser to: http://localhost:3000
```

---

## 🐛 **Common Errors & Fixes**

### Error: "bun: command not found"
**Fix**: Restart PowerShell or run:
```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### Error: "Cannot find module"
**Fix**: Delete node_modules and reinstall:
```powershell
Remove-Item -Recurse -Force node_modules
bun install
```

### Error: "Database connection failed"
**Fix**: Start Docker services:
```powershell
cd c:\Users\USER\.gemini\antigravity\scratch\OpenCut-by-selliblyhq
docker-compose up -d
```

### Error: "Port 3000 already in use"
**Fix**: Kill the process or use different port:
```powershell
# Find process using port 3000
netstat -ano | findstr :3000

# Kill process (replace PID with actual process ID)
taskkill /PID <PID> /F

# Or use different port
$env:PORT=3001
bun run dev
```

---

## 📞 **Need More Help?**

### Option 1: Check Bun Documentation
- https://bun.sh/docs/installation

### Option 2: Use GitHub Issues
- https://github.com/oven-sh/bun/issues

### Option 3: Try Alternative Package Managers
- **pnpm**: `npm install -g pnpm && pnpm install && pnpm dev`
- **yarn**: `npm install -g yarn && yarn install && yarn dev`

---

## 🎯 **Recommended Next Steps**

Based on your network issues, I recommend:

1. ✅ **Try Solution 1** (Manual Download) - Most reliable
2. ✅ **Try Solution 5** (Docker) - If you have Docker Desktop
3. ✅ **Try Solution 6** (Vercel) - Fastest way to see it running

All three options will get you up and running without fighting network issues!

---

## ✨ **Quick Status Check**

Run this script to check your environment:

```powershell
Write-Host "=== Environment Check ===" -ForegroundColor Cyan

# Check Bun
try {
    $bunVersion = bun --version 2>$null
    Write-Host "✅ Bun: $bunVersion" -ForegroundColor Green
} catch {
    Write-Host "❌ Bun: Not installed" -ForegroundColor Red
}

# Check Node.js
try {
    $nodeVersion = node --version 2>$null
    Write-Host "✅ Node.js: $nodeVersion" -ForegroundColor Green
} catch {
    Write-Host "❌ Node.js: Not installed" -ForegroundColor Red
}

# Check Docker
try {
    $dockerVersion = docker --version 2>$null
    Write-Host "✅ Docker: $dockerVersion" -ForegroundColor Green
} catch {
    Write-Host "❌ Docker: Not installed" -ForegroundColor Red
}

# Check Git
try {
    $gitVersion = git --version 2>$null
    Write-Host "✅ Git: $gitVersion" -ForegroundColor Green
} catch {
    Write-Host "❌ Git: Not installed" -ForegroundColor Red
}

Write-Host "`n=== Network Check ===" -ForegroundColor Cyan
Test-NetConnection -ComputerName github.com -Port 443 -InformationLevel Quiet
if ($?) {
    Write-Host "✅ GitHub: Accessible" -ForegroundColor Green
} else {
    Write-Host "❌ GitHub: Not accessible" -ForegroundColor Red
}
```

Save this as `check-env.ps1` and run it to see what's available on your system.
