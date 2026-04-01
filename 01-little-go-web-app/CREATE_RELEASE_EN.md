# Guide: Creating a New Release

This guide explains how to create a new release of your Docker image and automatically trigger publishing and scanning with Docker Scout.

## Prerequisites

- Your GitHub repository is properly configured (see `GITHUB_SETUP_EN.md`)
- You have release creation rights on the repository
- Your code has been pushed to the main branch (`main` or `master`)

## Before Creating a Release

Before creating a release, make sure that:

1. **Your code is up to date** on the main branch
2. **Tests pass** (if you have any)
3. **The version is ready** for publishing

### Choose a Versioning Strategy

This project uses **Semantic Versioning** (SemVer): `MAJOR.MINOR.PATCH`

- `MAJOR`: Incompatible major change (ex: 1.0.0 → 2.0.0)
- `MINOR`: Backward-compatible new feature (ex: 1.0.0 → 1.1.0)
- `PATCH`: Bug fix (ex: 1.0.0 → 1.0.1)

Example versions:
- `0.0.1` (initial version)
- `0.1.0` (new features added)
- `1.0.0` (first stable version)
- `1.1.0` (new features)
- `1.1.1` (bug fix)

---

## Method 1: Create a Release with Git (Command Line) ⚡

This method creates a Git tag and pushes it to GitHub, which **automatically** triggers the workflow.

### Step 1: Create and Push the Tag

```bash
# 1. Make sure everything is committed and up to date
git status

# 2. Create an annotated tag for the version
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"

# 3. Push the tag to GitHub (THIS TRIGGERS THE WORKFLOW)
git push origin v0.0.1
```

**Replace `v0.0.1` with your version number.**

### Step 2: Complete Example with Changes

If you have changes to commit first:

```bash
# Add all changes
git add .

# Commit the changes
git commit -m "Prepare release v0.0.1"

# Create an annotated tag
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"

# Push the commit to the main branch
git push origin main  # or master, depending on your default branch

# Push the tag (THIS IMMEDIATELY TRIGGERS THE WORKFLOW)
git push origin v0.0.1
```

### Step 3: Verify the Tag is Pushed

```bash
# List local tags
git tag

# See remote tags
git ls-remote --tags origin
```

You should see your tag in the list.

### Step 4: Verify Workflow Trigger

After 5-10 seconds:

1. Go to your GitHub repository
2. Click on the **Actions** tab
3. You should see the workflow: `Build, Push and Scan with Docker Scout` running

---

## Method 2: Create a Release via GitHub Web Interface

You can also create a release with description and notes via the web interface.

### Step 1: Access the Releases Page

1. Go to `https://github.com/YOUR_USERNAME/your-repo/releases`
2. You'll see your existing tags (created via Git)

### Step 2: Create the GitHub Release

#### Option A: From an Existing Tag

1. Find your tag (ex: `v0.0.1`)
2. Click the three dots `...` next to the tag
3. Click **Create release**
4. Fill in the information below

#### Option B: Create a New Release Directly

1. Click **Draft a new release** or **Create a new release**
2. Click **Choose a tag**
3. Select the tag you just created (ex: `v0.0.1`)
4. Fill in the information below

### Step 3: Fill in Release Information

#### Release title

- **Value**: A descriptive title
- **Examples**:
  - `Release v0.0.1 - Initial Release`
  - `Release v1.0.0 - Stable Version`
  - `Release v1.1.0 - New Features and Fixes`

#### Description

- **Value**: A detailed description of changes
- **Recommended template**:

```markdown
## 🎉 Changes in this Release

### ✨ New Features
- Feature 1
- Feature 2

### 🐛 Bug Fixes
- Bug fix 1
- Bug fix 2

### 📝 Improvements
- Improvement 1
- Improvement 2

### 🔒 Security Updates
(If applicable)

## Docker Image

This release publishes the Docker image to Docker Hub:

**Organization**: bluewhale
**Image**: bluewhale/little-go-web-app
**Tags**: 
- `0.0.1` (example)
- `latest`
```

#### Set as a pre-release

- Uncheck this option for a stable release
- Check this option for a pre-release (ex: `1.0.0-beta`, `1.0.0-rc1`)

#### Set as the latest release

- Check this option if it's the current stable version
- GitHub will display it with a `Latest` badge

### Step 4: Publish the Release

Click **Publish release**

This will:
1. ✅ Create the GitHub release with description
2. ✅ **The workflow already triggered when you pushed the tag**

---

## Monitor the Workflow

The `Build, Push and Scan with Docker Scout` workflow automatically triggers after you push the tag.

### Follow the Progress

1. Go to your GitHub repository
2. Click the **Actions** tab
3. You'll see the running workflow: `Build, Push and Scan with Docker Scout`
4. Click on the workflow to see details

### Workflow Steps

The workflow automatically executes:

1. **Checkout code**: Fetches the release code
2. **Set up Docker Buildx**: Prepares the build tool
3. **Login to Docker Hub**: Authenticates with Docker Hub (uses configured secrets)
4. **Extract version from release tag**: Extracts the version from the tag
5. **Build and push Docker image**: 
   - Builds the Docker image
   - Publishes it to Docker Hub with the version tag
   - Publishes it with the `latest` tag
6. **Run Docker Scout vulnerability scan**: Scans the image for vulnerabilities

### Estimated Duration

The workflow typically takes **2 to 5 minutes** depending on image size and connection speed.

---

## Verify Publication

### On Docker Hub

1. Go to https://hub.docker.com/orgs/bluewhale
2. Click on the `little-go-web-app` repository
3. You should see the new tags:
   - The created version (ex: `0.0.1`)
   - The updated `latest` tag

### On GitHub

1. Go to your repository → **Security** → **Code scanning alerts**
2. You'll see the Docker Scout SARIF report with scan results

---

## Review Scan Results

Docker Scout scan results appear in:

### 1. GitHub Security

- Go to your repository → **Security** → **Code scanning alerts**
- You'll see vulnerabilities identified by Docker Scout
- Click on a vulnerability to see details

### 2. Workflow Console

- In the GitHub Actions workflow, the last step displays the report in text format

### 3. Docker Scout (optional)

- Visit https://scout.docker.com for detailed analysis

---

## Troubleshooting

### Workflow doesn't run

- Check that you configured `DOCKER_HUB_USERNAME` and `DOCKER_HUB_PASSWORD` secrets
- Make sure the tag was pushed: `git push origin vX.X.X`
- Wait a few seconds and refresh the **Actions** page
- Verify the tag is visible in **Releases** on GitHub

### Error: "denied: requested access to the resource is denied"

- Check Docker Hub secrets configured
- Confirm your Docker Hub user has access to the `bluewhale` organization

### Error: "manifest not found"

- Check that the `Dockerfile` exists at the path specified in the workflow
- Confirm all files required by the `Dockerfile` are present

### Image doesn't appear on Docker Hub

- Wait 1-2 minutes (push may be slow)
- Check the workflow log for errors
- Confirm the tag was created (see **Actions** → workflow → **Build and push Docker image**)

### Tag doesn't push to GitHub

```bash
# Check that the local tag exists
git tag

# Push all tags
git push origin --tags

# Or push a specific tag
git push origin v0.0.1
```

---

## Process Summary

| Step | Action | Automatic | Result |
|------|--------|-----------|--------|
| 1 | Create Git tag | ❌ Manual | Tag created locally |
| 2 | Push tag | ❌ Manual | Tag pushed to GitHub |
| 3 | Trigger workflow | ✅ Automatic | Workflow starts |
| 4 | Build Docker image | ✅ Automatic | Image built |
| 5 | Push to Docker Hub | ✅ Automatic | Image published |
| 6 | Scan with Docker Scout | ✅ Automatic | Report generated |
| 7 | Upload SARIF | ✅ Automatic | Results visible in GitHub |

---

## Quick Reference Commands

### Create and Push a Release (Complete)

```bash
# Commit changes
git add .
git commit -m "Release v0.0.1"

# Create the tag
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"

# Push everything
git push origin main
git push origin v0.0.1
```

### Create Multiple Tags Quickly

```bash
# v0.0.1
git tag -a v0.0.1 -m "Release v0.0.1"
git push origin v0.0.1

# v0.1.0
git tag -a v0.1.0 -m "Release v0.1.0"
git push origin v0.1.0

# v1.0.0
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

### List Versions

```bash
# Local tags
git tag

# Remote tags
git ls-remote --tags origin

# See tag details
git show v0.0.1
```

### Delete a Tag (If Error)

```bash
# Delete tag locally
git tag -d v0.0.1

# Delete tag on GitHub
git push origin --delete v0.0.1
```

---

## Next Steps

After creating a release:

1. Verify the workflow succeeded (**Actions** tab)
2. Review Docker Scout scan results
3. Fix any critical vulnerabilities identified
4. Create a new release for fixes

---

## Useful Shortcuts

- Releases: `https://github.com/YOUR_USERNAME/your-repo/releases`
- Actions: `https://github.com/YOUR_USERNAME/your-repo/actions`
- Security: `https://github.com/YOUR_USERNAME/your-repo/security`
- Docker Hub: `https://hub.docker.com/orgs/bluewhale`
- Docker Scout: `https://scout.docker.com`
