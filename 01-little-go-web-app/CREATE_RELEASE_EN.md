# Guide: Creating a New Release

This guide explains how to create and publish a new release of your Docker image. The GitHub Action automatically handles building, publishing, and scanning the image.

## Prerequisites

- Your GitHub repository is properly configured (see `GITHUB_SETUP_EN.md`)
- You have pushed your code to the main branch (`main` or `master`)
- You have command-line access to the repository (Git configured)

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

## Complete Release Publishing Flow

The process is simple: create a Git tag with the version and push it. Everything else is automatic!

```
git tag → git push → GitHub Action → Build → Push Docker Hub → Scan → Create Release
```

---

## Step 1: Create a Local Git Tag

From your terminal, in the project directory, create an annotated tag:

```bash
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"
```

**Replace `v0.0.1` with your version number.**

### Verify the Tag is Created

```bash
git tag
```

You should see `v0.0.1` in the list of local tags.

---

## Step 2: Push the Tag to GitHub

This is the crucial moment! Pushing the tag triggers the GitHub Action.

```bash
git push origin v0.0.1
```

That's it! The GitHub Action starts automatically.

### Verify the Tag is Pushed

```bash
git ls-remote --tags origin
```

You should see your tag in the list of remote tags.

---

## Step 3: Verify GitHub Action Triggered

After 5-10 seconds:

1. Go to your GitHub repository: `https://github.com/YOUR_USERNAME/your-repo`
2. Click the **Actions** tab
3. You'll see the workflow: `Build, Push and Scan with Docker Scout` running

### Follow the Progress

Click on the workflow to see the steps:

1. **Checkout code**: Fetches code from the tag
2. **Set up Docker Buildx**: Prepares the build tool
3. **Login to Docker Hub**: Authenticates with Docker Hub
4. **Extract version from tag**: Extracts the version (ex: `0.0.1`)
5. **Build and push Docker image**: 
   - Builds the Docker image
   - Publishes it to Docker Hub with the version tag
   - Publishes it with the `latest` tag
6. **Run Docker Scout vulnerability scan**: Scans the image for vulnerabilities
7. **Upload Scout SARIF report**: Sends the report to GitHub
8. **Create GitHub Release**: **Automatically creates the GitHub release with description**

### Estimated Duration

The workflow typically takes **2 to 5 minutes** depending on image size and connection speed.

---

## Step 4: Verify Results

The GitHub Action automatically creates the release. You don't need to do anything!

### On GitHub

1. Go to your repository → **Releases**
2. You'll see the new release created automatically with:
   - Title: `Release v0.0.1`
   - An auto-generated description with the Docker Hub link
   - Published Docker tags

### On Docker Hub

1. Go to https://hub.docker.com/r/bluewhale/little-go-web-app/tags
2. You should see the new tags:
   - The created version (ex: `0.0.1`)
   - The updated `latest` tag

### On GitHub Security

1. Go to your repository → **Security** → **Code scanning alerts**
2. You'll see the Docker Scout SARIF report with scan results

---

## Review Scan Results

### 1. In GitHub Security

- Go to your repository → **Security** → **Code scanning alerts**
- You'll see vulnerabilities identified by Docker Scout
- Click on a vulnerability to see details

### 2. In the Workflow Console

- In the GitHub Actions workflow, the last step displays the report in text format

### 3. On Docker Scout (optional)

- Visit https://scout.docker.com for detailed analysis

---

## Complete Example: From Modification to Deployment

Here's a complete example if you have changes to make before the release:

```bash
# 1. Make your code modifications
# (edit files, test, etc.)

# 2. Commit the changes
git add .
git commit -m "Prepare release v0.0.1"

# 3. Push to the main branch
git push origin main  # or master

# 4. Create the tag
git tag -a v0.0.1 -m "Release v0.0.1 - Initial Release"

# 5. Push the tag (TRIGGERS THE GITHUB ACTION)
git push origin v0.0.1

# That's it! Wait for the workflow to complete (~2-5 minutes)
```

---

## Quick Reference Commands

### Create and Push a Release in One Line

```bash
git tag -a v0.0.1 -m "Release v0.0.1" && git push origin v0.0.1
```

### Create multiple versions quickly

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

# Delete tag on GitHub (and associated release)
git push origin --delete v0.0.1
```

---

## Troubleshooting

### Workflow doesn't start after pushing the tag

- Verify the tag starts with `v` (ex: `v0.0.1`, `v1.0.0`)
- Verify you pushed the tag: `git push origin vX.X.X`
- Wait 10-15 seconds and refresh the **Actions** page
- Verify the tag is visible on GitHub: `https://github.com/YOUR_USERNAME/your-repo/tags`

### Error: "denied: requested access to the resource is denied"

- Check Docker Hub secrets: `DOCKER_HUB_USERNAME` and `DOCKER_HUB_PASSWORD`
- Confirm your Docker Hub user has access to the `bluewhale` organization

### Error: "manifest not found"

- Verify the `Dockerfile` exists at path `./01-little-go-web-app/`
- Confirm all files required by the `Dockerfile` are present

### Image doesn't appear on Docker Hub

- Wait 1-2 minutes (push may be slow)
- Check the workflow log for errors
- Visit: `https://hub.docker.com/r/bluewhale/little-go-web-app`

### Release doesn't appear on GitHub

- Wait 1-2 minutes
- Go to: `https://github.com/YOUR_USERNAME/your-repo/releases`
- If it doesn't appear, verify the workflow completed without errors (**Actions** tab)

### Tag doesn't push to GitHub

```bash
# Check that local tag exists
git tag

# Push the specific tag
git push origin v0.0.1

# Or push all tags at once
git push origin --tags
```

---

## Automated Process Summary

| Step | Action | Type | Result |
|------|--------|------|--------|
| 1 | Create Git tag | ❌ Manual | Tag created locally |
| 2 | Push tag | ❌ Manual | Tag pushed, workflow triggered |
| 3 | Build Docker image | ✅ Automatic | Image built |
| 4 | Push Docker Hub | ✅ Automatic | Image published |
| 5 | Scan Docker Scout | ✅ Automatic | Report generated |
| 6 | Upload SARIF | ✅ Automatic | Results visible |
| 7 | Create GitHub Release | ✅ Automatic | Release created with description |

**You only need to do steps 1 and 2. Everything else is completely automatic!**

---

## Next Steps

After creating a release:

1. Verify the workflow succeeded (**Actions** tab)
2. Review Docker Scout scan results (**Security** → **Code scanning alerts**)
3. Verify the image is published on Docker Hub
4. Verify the GitHub release was created (**Releases**)
5. Fix any critical vulnerabilities identified
6. Create a new release for fixes

---

## Useful Shortcuts

- Tags: `https://github.com/YOUR_USERNAME/your-repo/tags`
- Releases: `https://github.com/YOUR_USERNAME/your-repo/releases`
- Actions: `https://github.com/YOUR_USERNAME/your-repo/actions`
- Security: `https://github.com/YOUR_USERNAME/your-repo/security`
- Docker Hub: `https://hub.docker.com/r/bluewhale/little-go-web-app`
- Docker Scout: `https://scout.docker.com`
