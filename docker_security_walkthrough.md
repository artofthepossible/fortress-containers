# Developer Walkthrough: Hardened Images with Security Scanning

## Overview
This guide walks you through using Docker hardened images and implementing comprehensive security scanning at both buildtime and runtime using Docker Scout and runtime application scanning.

## Prerequisites
- Docker Desktop with Scout enabled
- Access to `demonstrationorg/dhi-demo-bcg` hardened images
- Runtime security scanning tool of choice (Twistlock, Aqua, Sysdig, etc.)

## Step 1: Pull and Inspect Hardened Images

```bash
# Pull the latest hardened image
docker pull demonstrationorg/dhi-demo-bcg:latest

# List available tags
docker images demonstrationorg/dhi-demo-bcg
```

## Step 2: Buildtime Security Assessment with Docker Scout

### Enable Docker Scout
```bash
# Login to Docker Scout (if not already authenticated)
docker scout auth login

# Quick vulnerability scan
docker scout cves demonstrationorg/dhi-demo-bcg:latest

# Detailed security report with recommendations
docker scout recommendations demonstrationorg/dhi-demo-bcg:latest
```

### Advanced Scout Analysis
```bash
# Compare with base image vulnerabilities
docker scout compare --to debian:12 demonstrationorg/dhi-demo-bcg:latest

# Generate SBOM (Software Bill of Materials)
docker scout sbom demonstrationorg/dhi-demo-bcg:latest

# Policy evaluation (if policies are configured)
docker scout policy demonstrationorg/dhi-demo-bcg:latest
```

## Step 3: Container Runtime Deployment

### Basic Deployment
```bash
# Run with security best practices
docker run -d \
  --name secure-app \
  --read-only \
  --tmpfs /tmp \
  --tmpfs /var/run \
  --no-new-privileges \
  --cap-drop ALL \
  --cap-add NET_BIND_SERVICE \
  -p 8080:8080 \
  demonstrationorg/dhi-demo-bcg:latest
```

### Production-Ready Deployment
```bash
# Run with comprehensive security controls
docker run -d \
  --name production-app \
  --read-only \
  --tmpfs /tmp:noexec,nosuid,size=100m \
  --tmpfs /var/run:noexec,nosuid,size=50m \
  --no-new-privileges \
  --cap-drop ALL \
  --cap-add NET_BIND_SERVICE \
  --security-opt no-new-privileges \
  --security-opt seccomp=default \
  --user 1001:1001 \
  -p 8080:8080 \
  demonstrationorg/dhi-demo-bcg:latest
```

## Step 4: Runtime Security Scanning

### Option A: Twistlock/Prisma Cloud
```bash
# Install Twistlock Defender (if not already deployed)
# Scan running container
twistcli images scan demonstrationorg/dhi-demo-bcg:latest

# Runtime compliance check
twistcli compliance scan --container-id <container_id>
```

## Troubleshooting

### Common Issues
- **Scout not detecting image**: Ensure image is pulled locally first
- **Permission denied**: Verify user has Docker Scout access and runtime tool permissions

### Support Resources
- Docker Scout documentation: `docker scout --help`
- Runtime tool documentation (varies by chosen platform)
- Hardened image documentation from `demonstrationorg`

---
