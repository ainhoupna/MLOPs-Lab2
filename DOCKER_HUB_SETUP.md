# GitHub Actions Docker Hub Setup Guide

Follow these steps to automate Docker image building and pushing to Docker Hub via GitHub Actions.

## Step 1: Create Docker Hub Personal Access Token

1. Go to [Docker Hub](https://hub.docker.com/)
2. Log in with your account (`ainhoupna`)
3. Click on your username (top right) → **Account Settings**
4. Go to **Security** → **Personal Access Tokens** (or **Access Tokens**)
5. Click **Generate New Token** (or **New Access Token**)
6. Configure the token:
   - **Token Description**: `GitHub Actions MLOps Lab2` (or any descriptive name)
   - **Access Permissions**: Select **Read, Write, Delete**
   - **Expiration**: Choose your preferred expiration date (e.g., 90 days, 1 year, or never)
7. Click **Generate**
8. **IMPORTANT**: Copy the generated token immediately (you won't be able to see it again!)

## Step 2: Add Secrets to GitHub Repository

1. Go to your GitHub repository: `https://github.com/ainhoupna/MLOPs-Lab2`
2. Click on **Settings** (top menu)
3. In the left sidebar, go to **Secrets and variables** → **Actions**
4. Click **New repository secret**
5. Add the first secret:
   - **Name**: `DOCKERHUB_USERNAME`
   - **Secret**: `ainhoupna`
   - Click **Add secret**
6. Click **New repository secret** again
7. Add the second secret:
   - **Name**: `DOCKERHUB_TOKEN`
   - **Secret**: Paste the token you copied from Docker Hub
   - Click **Add secret**

## Step 3: Verify Secrets

After adding both secrets, you should see:
- ✅ `DOCKERHUB_USERNAME`
- ✅ `DOCKERHUB_TOKEN`

## Step 4: GitHub Actions Workflow

The workflow file has been created at `.github/workflows/docker.yml`. This workflow will:
- Trigger on every push to the `master` branch
- Build the Docker image
- Push it to Docker Hub as `ainhoupna/mlops-lab2:latest`

## Step 5: Test the Automation

1. Commit and push the workflow file:
   ```bash
   git add .github/workflows/docker.yml
   git commit -m "Add Docker Hub automation to GitHub Actions"
   git push origin master
   ```

2. Go to your GitHub repository → **Actions** tab
3. You should see the workflow running
4. Once complete, check Docker Hub to verify the image was pushed

## Troubleshooting

**Workflow fails with authentication error:**
- Verify the secrets are correctly set in GitHub
- Make sure the token has "Read, Write, Delete" permissions
- Check that the token hasn't expired

**Image not appearing on Docker Hub:**
- Check the Actions logs for errors
- Verify your Docker Hub username is correct in the secrets
