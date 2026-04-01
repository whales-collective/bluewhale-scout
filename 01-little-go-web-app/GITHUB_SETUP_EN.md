# GitHub Repository Configuration for Automatic Publishing

This guide explains how to configure your GitHub repository to enable the GitHub Action to automatically build, publish, and scan your Docker image when a release is created.

## Prerequisites

- A public or private GitHub repository
- A Docker Hub account with access to the `bluewhale` organization
- Administrator rights on the GitHub repository

## Step 1: Create a Docker Hub Personal Access Token

To allow the GitHub Action to publish the image to Docker Hub, you need to create a personal access token (PAT).

1. Sign in to your Docker Hub account: https://hub.docker.com
2. Click your avatar in the top right → **Account Settings**
3. In the left menu, click **Security** → **Personal Access Tokens**
4. Click **Generate new token** or **Create new token**
5. Give it a descriptive name: `github-action-publisher`
6. Select **Read & Write** permission (to publish images)
7. Click **Generate**
8. **Copy the token immediately** (you won't be able to see it again)

## Step 2: Add GitHub Secrets

GitHub secrets allow you to store your credentials securely.

1. Go to your GitHub repository
2. Click **Settings** (configuration tab)
3. In the left menu, click **Secrets and variables** → **Actions**
4. Click **New repository secret**

### Add the DOCKER_HUB_USERNAME Secret

- **Name**: `DOCKER_HUB_USERNAME`
- **Secret**: Your Docker Hub username
- Click **Add secret**

### Add the DOCKER_HUB_PASSWORD Secret

- **Name**: `DOCKER_HUB_PASSWORD`
- **Secret**: Paste the PAT (Personal Access Token) created in Step 1
- Click **Add secret**

## Step 3: Verify the GitHub Action File

Make sure the `.github/workflows/build-push-scan.yml` file is present in your repository at the root of the project. This file describes the automated workflow.

### Expected Repository Structure

```
.
├── .github/
│   └── workflows/
│       └── build-push-scan.yml     ← The workflow file
├── 01-little-go-web-app/
│   ├── Dockerfile
│   ├── main.go
│   └── ...
└── README.md
```

## Step 4: Verify Workflow Configuration

The workflow file uses the following variables that you can customize:

- `REGISTRY`: `docker.io` (Docker Hub)
- `IMAGE_NAME`: `bluewhale/little-go-web-app` — **Update if your organization or repo name is different**
- `WORKING_DIRECTORY`: `./01-little-go-web-app` — The path to your Dockerfile

To modify these parameters:
1. Open `.github/workflows/build-push-scan.yml`
2. Edit the values in the `env:` section
3. Commit and push the changes

## Step 5: Enable Workflow Permissions

By default, GitHub Actions has the correct permissions. Verify that:

1. Go to your repository → **Settings** → **Actions** → **General**
2. Under **Workflow permissions**, ensure **Read and write permissions** is selected
3. Click **Save**

## Configuration Summary

| Element | Configuration |
|---------|---------------|
| **GitHub Action** | `.github/workflows/build-push-scan.yml` |
| **Trigger** | Release creation |
| **Actions** | Build → Push → Scan (Docker Scout) |
| **Required Secrets** | `DOCKER_HUB_USERNAME`, `DOCKER_HUB_PASSWORD` |
| **Environment Variables** | `REGISTRY`, `IMAGE_NAME`, `WORKING_DIRECTORY` |

## Troubleshooting

### Workflow doesn't run after creating a release

- Check that the release is marked as `published` (not `draft`)
- Verify that the `.github/workflows/build-push-scan.yml` file is on the `main` branch (or your default branch)
- Go to your repository → **Actions** to see the workflow logs

### Error: "denied: requested access to the resource is denied"

- Verify that `DOCKER_HUB_USERNAME` and `DOCKER_HUB_PASSWORD` are correctly configured
- Confirm that the PAT token has **Read & Write** permission
- Ensure your Docker Hub user has access to the `bluewhale` organization

### Error: "Error response from daemon: manifest not found"

- Verify that the `IMAGE_NAME` variable in the workflow matches your Docker Hub organization and repository
- Confirm that the Dockerfile is at the path specified in `WORKING_DIRECTORY`

### Docker Scout scan is not running

- Docker Scout is free for public images
- If your image is private, check your Docker Scout permissions
- Scan results appear under **Security** → **Code scanning alerts** in GitHub

## Next Steps

Once configured, each new release created will automatically trigger:
1. **Build** of the Docker image
2. **Push** to Docker Hub with version tags and `latest`
3. **Scan** for vulnerabilities with Docker Scout
4. **Upload** of the SARIF report to GitHub (visible in the Security section)

See the **"Creating a Release"** guide for instructions on creating a new release.
