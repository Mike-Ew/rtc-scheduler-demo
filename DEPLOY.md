# Deployment Guide

## GitHub Repository
✅ **Created**: https://github.com/Mike-Ew/rtc-scheduler-demo (Private)

## Deploy to Render

### Option 1: Render Dashboard (Recommended)

1. Go to https://dashboard.render.com
2. Click **"New +"** → **"Static Site"**
3. Connect your GitHub account if not already connected
4. Select the repository: `Mike-Ew/rtc-scheduler-demo`
5. Configure:
   - **Name**: `rtc-scheduler-demo`
   - **Branch**: `main`
   - **Build Command**: Leave empty or `echo "No build needed"`
   - **Publish Directory**: `.` (root)
6. Click **"Create Static Site"**

Render will automatically detect the `render.yaml` configuration and deploy!

### Option 2: Render Blueprint (Automated)

Click this button to deploy automatically:

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/Mike-Ew/rtc-scheduler-demo)

### Option 3: Manual via render.yaml

The repository already includes `render.yaml`. Render will auto-detect and use it when you connect the repo.

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
