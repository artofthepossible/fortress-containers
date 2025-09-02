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
docker pull demonstrationorg/dhi-demo-bcg:v2.0notomcat
docker pull demonstrationorg/dhi-demo-bcg:v2.0tomcat
docker pull demonstrationorg/dhi-demo-bcg:v1.0debian12
docker pull demonstrationorg/dhi-demo-bcg:v1.0debian13

# List available tags
docker images demonstrationorg/dhi-demo-bcg
```

## Step 2: Buildtime Security Assessment with Docker Scout

### Enable Docker Scout
```bash
# Login to Docker Scout (if not already authenticated)
docker scout auth login

# Quick vulnerability scan
docker scout cves demonstrationorg/dhi-demo-bcg:v2.0notomcat
docker scout cves demonstrationorg/dhi-demo-bcg:v2.0tomcat
docker scout cves demonstrationorg/dhi-demo-bcg:v1.0debian12
docker scout cves demonstrationorg/dhi-demo-bcg:v1.0debian13


```

### Advanced Scout Analysis
```bash
# Compare with base image vulnerabilities
docker scout compare --to demonstrationorg/dhi-demo-bcg:v2.0tomcat demonstrationorg/dhi-demo-bcg:v2.0notomcat

# Generate SBOM (Software Bill of Materials)
docker scout sbom demonstrationorg/dhi-demo-bcg:v1.0debian13


```

## Step 3: Runtime Security Scanning

### Option A: Twistlock/Prisma Cloud
```bash
# Install Twistlock Defender (if not already deployed)
# Scan running container
twistcli images scan demonstrationorg/dhi-demo-bcg:v1.0debian13

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
