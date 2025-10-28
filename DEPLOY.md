# Deployment Guide

## GitHub Repository
✅ **Created**: https://github.com/Mike-Ew/rtc-scheduler-demo (Private)

## Deploy to Render

### Render CLI Note
The Render CLI (`render` command) is installed but only manages **existing** services. To create a new service, use the Dashboard below.

### Deploy via Render Dashboard (Step-by-Step)

**Step 1: Go to Render Dashboard**
```
https://dashboard.render.com
```

**Step 2: Create New Static Site**
- Click the **"New +"** button (top right)
- Select **"Static Site"**

**Step 3: Connect GitHub Repository**

If this is your first time:
- Click **"Configure account"** under GitHub
- Authorize Render to access your GitHub
- Select either:
  - **"All repositories"** (easier), or
  - **"Only select repositories"** → choose `rtc-scheduler-demo`
- Click **"Install & Authorize"**

If GitHub is already connected:
- Look for `Mike-Ew/rtc-scheduler-demo` in the repository list
- If you don't see it:
  - Click **"Configure account"**
  - Add `rtc-scheduler-demo` to the allowed repositories
  - Go back to Render dashboard

**Step 4: Connect the Repository**
- Find `Mike-Ew/rtc-scheduler-demo` in the list
- Click **"Connect"** button next to it

**Step 5: Configure Service**
Fill in these settings:
- **Name**: `rtc-scheduler-demo`
- **Branch**: `main`
- **Root Directory**: (leave blank)
- **Build Command**: (leave blank)
- **Publish Directory**: `.` (just a period)

**Step 6: Create Static Site**
- Click **"Create Static Site"** at the bottom
- Wait 30-60 seconds for deployment
- Your site will be live!

**Troubleshooting "Not Found":**
- ✅ Repo exists at: https://github.com/Mike-Ew/rtc-scheduler-demo
- ⚠️ Repo is **private** - Render needs GitHub permission to access it
- 💡 Solution: Complete Step 3 above to grant Render access

### After Deployment - Using Render CLI

Once your service exists, you can manage it with CLI:

```bash
# View all services
render services list -o json

# View recent deploys
render deploys list --service rtc-scheduler-demo -o json

# View logs
render logs --service rtc-scheduler-demo

# Trigger manual deploy
render restart --service rtc-scheduler-demo
```

## Access Your Demo

After deployment (takes 1-2 minutes):
- **Main Demo**: `https://rtc-scheduler-demo.onrender.com`

## Features Available

- ✅ Daily Coverage Board
- ✅ Staff Portal View
- ✅ Shift Management
- ✅ People Directory
- ✅ Conflict Detection
- ✅ Smart Staff Suggestions
- ✅ Availability Tracking
- ✅ Mobile Responsive

## Share with Client

Once deployed, share the Render URL directly with your client. The demo is:
- **Fully interactive** - no backend needed
- **Mobile friendly** - works on phones/tablets
- **Fast** - static HTML, instant loading
- **Secure** - HTTPS by default on Render

## Update the Demo

To update after making changes:
```bash
git add .
git commit -m "Update demo"
git push
```

Render will automatically redeploy on push to `main` branch.
