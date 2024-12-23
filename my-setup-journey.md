# My Jenkins Docker Pipeline Setup Journey

## Day 1: Initial Setup

### Installing Prerequisites
1. First installed Docker Desktop on my Mac
2. Verified Docker installation:
```bash
docker --version
```

### Setting up Jenkins
1. Pulled Jenkins Docker image:
```bash
docker pull jenkins/jenkins:lts
```

2. Started Jenkins container:
```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

3. Retrieved initial admin password:
```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

4. Accessed Jenkins UI at http://localhost:8080 and:
   - Entered the initial admin password
   - Chose "Install suggested plugins"
   - Created admin user account
   - Set Jenkins URL

## Day 2: Docker Integration

### Setting up Docker in Jenkins
1. Installed Docker-related plugins in Jenkins:
   - Docker Pipeline
   - Docker plugin
   - DockerHub plugin

2. Added Docker Hub credentials:
   - Navigated to Manage Jenkins → Credentials
   - Added new credentials
   - Selected "Username with password"
   - Used my Docker Hub username/password
   - Set ID as "dockerhub-credentials"

3. Granted Docker permissions to Jenkins:
```bash
docker exec -u root jenkins apt-get update
docker exec -u root jenkins apt-get install -y docker.io
docker exec -u root jenkins usermod -aG docker jenkins
docker restart jenkins
```

## Day 3: NgRok and GitHub Integration

### Setting up NgRok
1. Downloaded NgRok Using brew
```
brew install ngrok
```

```

1. Started NgRok tunnel:
```bash
ngrok http 8080
```
1. Got my public URL: https://[my-ngrok-subdomain].ngrok.io

### GitHub Webhook Configuration
1. Went to my GitHub repository settings
2. Added webhook:
   - URL: https://[my-ngrok-subdomain].ngrok.io/github-webhook/
   - Content type: application/json
   - Enabled SSL verification
   - Selected "Just the push event"

## Day 4: Pipeline Creation

### Creating Pipeline Files
1. Created basic Node.js application files:
   - app.js with Express server
   - package.json with dependencies
   - Dockerfile for containerization
   - Jenkinsfile for pipeline definition

### Setting up Jenkins Pipeline
1. Created new Pipeline job:
   - Clicked "New Item"
   - Named it "assingment"
   - Selected "Pipeline" option

2. Configured pipeline settings:
   - Enabled GitHub project
   - Added repository URL
   - Selected "GitHub hook trigger for GITScm polling"
   - Added my Jenkinsfile path

## Day 5: Testing and Troubleshooting

### Initial Tests
1. Made test commit to repository
2. Observed webhook delivery in GitHub
3. Watched Jenkins pipeline execution
4. Verified Docker image creation

### Issues and Solutions
1. Fixed Docker permission issue:
```bash
docker exec -u root jenkins chmod 666 /var/run/docker.sock
```

2. Resolved NgRok connection drops by using:
```bash
ngrok http 8080 --request-header-add "X-Forwarded-Proto: https"
```

3. Fixed Docker Hub authentication by updating credentials

## Final Setup Verification

1. Tested complete workflow:
   - Pushed code changes
   - Confirmed webhook trigger
   - Verified Jenkins build
   - Checked Docker Hub for new image
   - Pulled and ran container locally

2. Everything working as expected:
```bash
docker pull adhamafis/test-app
docker run -p 3000:3000 adhamafis/test-app
```
