# fortress-containers
a secure by design approach to containers

## Developer Walkthrough: Hardened Images with Security Scanning (ECR Edition)

### Overview
This guide walks you through using Docker hardened images from Amazon ECR and implementing comprehensive security scanning at both buildtime and runtime using Docker Scout and runtime application scanning.

### Prerequisites
- Docker Desktop with Scout enabled
- Access to your ECR repository (replace `123456789012` with your AWS account ID)
- Runtime security scanning tool of choice (Twistlock, CortexCloud etc.)

### Step 1: Pull and Inspect Hardened Images from ECR
```bash
# Authenticate Docker to ECR
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-west-2.amazonaws.com

# Pull the latest hardened images
docker pull 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v2.0notomcat:v2.0notomcat
docker pull 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v2.0tomcat:v2.0tomcat
docker pull 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v1.0debian12:v1.0debian12
docker pull 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v1.0debian13:v1.0debian13

# List available tags
docker images 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v*

Replace `123456789012` with your AWS account ID and `my-app:latest` with your image name and tag as needed.

```

### Step 2: Buildtime Security Assessment with Docker Scout
```bash
# Login to Docker Scout (if not already authenticated)
docker scout auth login

# Quick vulnerability scan
docker scout cves 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v2.0notomcat:v2.0notomcat
docker scout cves 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v2.0tomcat:v2.0tomcat
docker scout cves 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v1.0debian12:v1.0debian12
docker scout cves 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v1.0debian13:v1.0debian13

# Advanced Scout Analysis
docker scout compare --to 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v2.0tomcat:v2.0tomcat 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v2.0notomcat:v2.0notomcat
docker scout sbom 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v1.0debian13:v1.0debian13
```

### Step 3: Runtime Security Scanning
```bash
# Example: Twistlock/Prisma Cloud
# Scan running container
twistcli images scan 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v1.0debian13:v1.0debian13

# Runtime compliance check
twistcli compliance scan --container-id <container_id>
```

### Step 4: Run Container
```bash
docker run -d --name demo-app -p 8080:8080 123456789012.dkr.ecr.us-west-2.amazonaws.com/dhi-demo-v1.0debian13:v1.0debian13
```

### Troubleshooting
- **Scout not detecting image**: Ensure image is pulled locally first
- **Permission denied**: Verify user has Docker Scout access and runtime tool permissions

### Support Resources
- Docker Scout documentation: `docker scout --help`
- Runtime tool documentation (varies by chosen platform)
- Hardened image documentation from your ECR repository

