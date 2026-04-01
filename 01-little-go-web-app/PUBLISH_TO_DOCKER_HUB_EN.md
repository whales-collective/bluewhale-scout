# Publishing Your Application to Docker Hub

This guide explains how to build and publish your Go web application image to the Docker Hub organization **bluewhale** with version tag **0.0.0**.

## Prerequisites

- Docker is installed and running
- You have a Docker Hub account with access to the `bluewhale` organization
- You are logged in to Docker Hub via the CLI

## Step 1: Login to Docker Hub

If you are not already logged in, authenticate with Docker Hub:

```bash
docker login
```

You will be prompted to enter your Docker Hub username and password (or personal access token).

## Step 2: Build the Docker Image

Navigate to the project directory and build the image with the appropriate tag:

```bash
docker build -t bluewhale/little-go-web-app:0.0.0 .
```

This command:
- Builds the image from the `Dockerfile`
- Tags it as `bluewhale/little-go-web-app:0.0.0` (replace `bluewhale` with your organization name if different)
- Uses the current directory (`.`) as the build context

## Step 3: Verify the Image

List your images to confirm it was built successfully:

```bash
docker images | grep little-go-web-app
```

You should see an entry with the tag `bluewhale/little-go-web-app:0.0.0` and the image size.

## Step 4: Push the Image to Docker Hub

Push the image to the Docker Hub organization:

```bash
docker push bluewhale/little-go-web-app:0.0.0
```

This uploads the image to the `bluewhale` organization on Docker Hub.

## Step 5: (Optional) Tag as Latest

If you want to also tag this version as `latest`:

```bash
docker tag bluewhale/little-go-web-app:0.0.0 bluewhale/little-go-web-app:latest
docker push bluewhale/little-go-web-app:latest
```

## Step 6: Verify on Docker Hub

Visit https://hub.docker.com/orgs/bluewhale and navigate to your repositories to confirm the image is published.

## Quick Reference: All Commands

```bash
# Login (if needed)
docker login

# Build the image
docker build -t bluewhale/little-go-web-app:0.0.0 .

# Verify the build
docker images | grep little-go-web-app

# Push to Docker Hub
docker push bluewhale/little-go-web-app:0.0.0

# (Optional) Also tag as latest
docker tag bluewhale/little-go-web-app:0.0.0 bluewhale/little-go-web-app:latest
docker push bluewhale/little-go-web-app:latest
```

## Troubleshooting

### Push fails with "denied: requested access to the resource is denied"
- Confirm you are logged in: `docker login`
- Verify you have permissions for the `bluewhale` organization

### Build fails
- Ensure Docker is running
- Check that all files referenced in the `Dockerfile` exist
- Review the build output for missing dependencies

### Image upload is slow
- Check your internet connection
- Consider building on a machine with better bandwidth if uploading from a slow connection
