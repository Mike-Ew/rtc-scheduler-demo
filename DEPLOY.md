# Deployment Guide

## GitHub Repository
✅ **Created**: https://github.com/Mike-Ew/rtc-scheduler-demo (Private)

## Deploy to Render

### Render CLI Note
The Render CLI (`render` command) is installed but only manages **existing** services. To create a new service, use the Dashboard below.

### Deploy via Render Dashboard (Required for New Services)

**Quick Steps:**

1. **Go to Render Dashboard**
   ```
   https://dashboard.render.com/select-repo?type=static
   ```

2. **Connect Repository**
   - Click **"Configure account"** if GitHub isn't connected yet
   - Grant Render access to `Mike-Ew/rtc-scheduler-demo` repository
   - Click **"Connect"** next to `rtc-scheduler-demo`

3. **Configure Service** (auto-filled from `render.yaml`):
   - **Name**: `rtc-scheduler-demo`
   - **Branch**: `main`
   - **Root Directory**: (leave empty)
   - **Build Command**: (leave empty)
   - **Publish Directory**: `.`

4. **Create Static Site**
   - Click **"Create Static Site"**
   - Deployment starts automatically!

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
- **Main Demo**: `https://rtc-scheduler-demo.onrender.com` (redirects to enhanced mockup)
- **Basic Version**: `https://rtc-scheduler-demo.onrender.com/mockup.html`
- **Enhanced Version**: `https://rtc-scheduler-demo.onrender.com/mockup-enhanced.html`

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
git commit -m "Update mockup"
git push
```

Render will automatically redeploy on push to `main` branch.
