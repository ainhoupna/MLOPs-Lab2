# Render Deployment Setup Guide

This guide walks you through deploying your FastAPI application to Render and automating deployments via GitHub Actions.

## Overview

Render will host your FastAPI backend using the Docker image from Docker Hub. Every time you push code to GitHub, the workflow will:
1. Build and push the Docker image to Docker Hub
2. Trigger Render to redeploy with the new image

## Step 1: Create Render Account

1. Go to [Render](https://render.com/)
2. Sign up for a free account (if you don't have one)
3. Verify your email

## Step 2: Create Web Service on Render

1. Log in to your Render dashboard
2. Click **New +** → **Web Service**
3. Select **Deploy an existing image from a registry**
4. In the **Image URL** field, enter:
   ```
   docker.io/ainhoupna/mlops-lab2:latest
   ```
5. Click **Next** or **Connect**

## Step 3: Configure Web Service

Fill in the following settings:

- **Name**: `mlops-lab2-api` (or any name you prefer)
- **Region**: Choose closest to you (e.g., Frankfurt for Europe)
- **Instance Type**: Select **Free** (under "Hobby Projects")
- **Environment Variables**: Leave empty (none needed for now)

### Important Settings:

- **Port**: Render should auto-detect port `8000` from your Dockerfile
  - If not, manually set: `8000`
- **Health Check Path**: `/` (optional but recommended)

Click **Deploy Web Service**

## Step 4: Wait for Initial Deployment

- Render will pull your Docker image from Docker Hub
- Initial deployment takes 2-5 minutes
- You'll see logs in real-time
- Once complete, you'll get a URL like: `https://mlops-lab2-api.onrender.com`

## Step 5: Test Your Deployed API

Once deployed, test your API:

```bash
# Test home page
curl https://mlops-lab2-api.onrender.com/

# Test prediction endpoint
curl -X POST -F "file=@image.jpg" https://mlops-lab2-api.onrender.com/predict
```

Or visit the URL in your browser to see the web interface.

## Step 6: Get Deploy Hook for Automation

1. In your Render web service dashboard, click **Settings**
2. Scroll down to **Deploy Hook** section
3. You'll see a URL like:
   ```
   https://api.render.com/deploy/srv-xxxxxxxxxxxxx?key=XXXXXXXXXXXXXXXX
   ```
4. **Copy only the key part** (everything after `key=`)
   - Example: If URL is `https://api.render.com/deploy/srv-abc123?key=xyz789`
   - Copy: `xyz789`

## Step 7: Add Render Secret to GitHub

1. Go to your GitHub repository settings
2. Navigate to **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add the secret:
   - **Name**: `RENDER_DEPLOY_HOOK_KEY`
   - **Secret**: Paste the key you copied in Step 6
5. Click **Add secret**

## Step 8: Update GitHub Actions Workflow

The workflow has been updated to include automatic Render deployment. After the Docker image is pushed to Docker Hub, it will trigger Render to redeploy.

## Step 9: Test End-to-End Pipeline

1. Make a small change to your code (e.g., update README)
2. Commit and push:
   ```bash
   git add .
   git commit -m "Test CI/CD pipeline"
   git push origin master
   ```
3. Watch the GitHub Actions workflow run
4. After completion, Render should automatically redeploy
5. Verify the changes are live on your Render URL

## Important Notes

### Free Tier Limitations

- **Spin down after inactivity**: Free services sleep after 15 minutes of inactivity
- **Cold starts**: First request after sleep takes 30-60 seconds
- **CPU only**: No GPU support (deep learning inference will be slower)
- **750 hours/month**: Shared across all free services

### Keeping Service Awake (Optional)

To prevent sleep, you can use a service like [UptimeRobot](https://uptimerobot.com/) to ping your API every 10 minutes.

## Troubleshooting

**Service won't start:**
- Check Render logs for errors
- Verify Docker image exists on Docker Hub
- Ensure port 8000 is exposed in Dockerfile

**Deploy hook not working:**
- Verify `RENDER_DEPLOY_HOOK_KEY` secret is correct
- Check GitHub Actions logs for deployment step errors

**API is slow:**
- This is expected on free tier (CPU only)
- Consider upgrading to paid tier for better performance

## Summary

After completing these steps:
- ✅ Your API is live on Render
- ✅ Every push to GitHub automatically deploys to Render
- ✅ Full CI/CD pipeline: Code → GitHub → Docker Hub → Render
